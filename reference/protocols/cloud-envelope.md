# iSolarCloud message envelope

Every payload the dongle publishes to iSolarCloud uses the same JSON envelope. Every command it
receives uses the same envelope too. This page describes the envelope, how long messages are split
into chunks, how chunks are compressed, and the rules the dongle applies to inbound commands.

For the session and topics see [cloud-mqtt.md](cloud-mqtt.md). For the contents of `businessData`
see [cloud-messages.md](cloud-messages.md) (uploads) and [cloud-commands.md](cloud-commands.md)
(commands and replies).

The WiNet-S and WiNet-S2 build the envelope the same way. Only the two product constants differ.

## Outbound envelope

### Fields

The keys always appear in this order:

| # | Field | Type | Content |
|---|---|---|---|
| 1 | `messageId` | string | `<serial>_<sendTime>_<counter>`, with the counter as five decimal digits |
| 2 | `packgeCount` | string | Total number of chunks, decimal (`"1"` for a single-chunk message) |
| 3 | `packgeId` | string | Index of this chunk, 1-based, decimal |
| 4 | `publicData` | object | Sender header; see the next table |
| 5 | `businessData` | array or string | The payload: a JSON array when there is one chunk, or a string holding one slice when there are several |

The spellings `packgeCount` and `packgeId` are Sungrow's and must be matched exactly.

`publicData` holds four fields:

| # | Field | Type | WiNet-S2 | WiNet-S |
|---|---|---|---|---|
| 1 | `productType` | string | `"1824"` | `"1813"` |
| 2 | `productName` | string | `"WiNet-S2"` | `"WiNet-S"` |
| 3 | `productSN` | string | Dongle serial | Dongle serial |
| 4 | `sendTime` | string | Local time `YYYYMMDDhhmmss` | Local time `YYYYMMDDhhmmss` |

- **`productType` and `productName`** are fixed in each firmware build. They are not read from the
  dongle's stored identity, and every dongle of a generation sends the same values.
- **`sendTime` and every `dataTime`** in the payload are in the dongle's **local time**, with no
  separators and no zone suffix. The zone comes from the cloud's set-time-zone command, which uses
  the form `UTC+10:00`.
- **The `messageId` counter** is a 16-bit value shared by all messages. It is incremented before use,
  skips 0, and wraps after 65535. Some publishes also use up a counter value for internal
  bookkeeping, so consecutive `messageId`s often go up by 2.

### Missing header

While the clock is invalid (year outside 1971 to 2069), the dongle builds an envelope that holds only
`businessData`. Scheduled messages are not sent at all in that state. The one envelope that still
goes out without a header is a will built before the clock was set.

### Fault replay messages

Offline-fault replay messages (`pub/faultdata/history`) build their own header:

- It is always the WiNet-S2 constants on the WiNet-S2.
- It is always a single chunk.
- It is sent even while the clock is invalid.

## Chunking

The dongle prints the `businessData` array as compact JSON and splits it into chunks if it is too
long.

| Rule | Value |
|---|---|
| Slice size | 2560 bytes of the printed array. The last slice holds the remainder. |
| Chunk count | `ceil(length / 2560)` |
| One chunk | `businessData` is the JSON array itself |
| Several chunks | `businessData` is a JSON **string** holding the next 2560 bytes of the printed array. The slice is escaped as an ordinary JSON string, so quotes become `\"`. |
| `messageId` | Generated for chunk 1 and reused for every later chunk of the same message |
| `sendTime` | Read again for every chunk, so it can change between chunks |
| Reassembly | The receiver unescapes each slice, joins the slices in `packgeId` order and parses the result as one JSON array |

### Sending order

- Chunk 1 is published at once.
- Each continuation chunk is published when the PUBACK for the chunk before it arrives.
- A message whose tail has not gone out within 120 s is abandoned.
- A publish whose PUBACK does not arrive within 20 to 25 s is given up. Nothing is ever resent, by the
  MQTT layer or by the application.

Rundata adds one more level of pacing: each device gets its own message, and the next device's
message goes out only when the previous one is acknowledged, or when its wait times out.

Some families never chunk:

| Family | Rule |
|---|---|
| `faultmergedata`, `faultdata/history` | Single chunk only. A payload over 2560 bytes is dropped. |
| `faultdata` | All chunks are published at once, with no PUBACK pacing. |

## Compression

When the topic's compress flag is set (the `c` in `enum_e_k_c`; on by default), the dongle compresses
**each chunk envelope separately**, after the chunk has been printed as JSON.

| Rule | Detail |
|---|---|
| Format | zlib stream (RFC 1950), default level. The payload begins with a two-byte zlib header, normally `0x78 0x9C`. |
| Input | The printed envelope **plus its terminating NUL byte** |
| Uncompressed payload | When the flag is 0, the payload is the JSON text without a NUL |
| Unit | One chunk. The `messageId`, `packgeCount` and `packgeId` of each chunk are inside the compressed data. |
| Will | Compressed in the same way when the flag is set |

A receiver inflates the payload, strips one trailing `0x00` and parses the JSON.

### No application-layer encryption

No outbound path applies application-layer encryption. The encrypt flag (`e`) is carried in the
topic and never applied. TLS is the only protection.

## Inbound command rules

The cloud sends commands in the same envelope. The dongle handles each incoming PUBLISH as follows:

1. **Topic prefix.** The topic must contain `isolarcloud/devicedata/cmd/`. The dongle also has a
   second framing for other topics: a binary frame encrypted with AES-128 and checked with a CRC-16.
   No topic the dongle subscribes to ever reaches it.
2. **Compress flag.** The `enum_<e>_<k>_<c>` suffix of the **incoming** topic decides whether the
   payload is compressed. A topic with no suffix is treated as uncompressed.
3. **Reply topic.** The reply topic is `isolarcloud/devicedata/rsp/<family>/<own serial>/enum_<own e>_<own k>_<own c>`,
   with `<family>` taken from the incoming topic. The time-zone broadcast topic gets no reply topic.
4. **Size.** A payload over 4096 bytes is dropped.
5. **Decompression.** If the compress flag is set, the payload is inflated into a 4096-byte buffer. A
   failed inflate is only logged, and parsing is attempted anyway.
6. **Parsing.** The payload is parsed as JSON:
   - `businessData` must be an array, and only element 0 is used.
   - `messageId`, `packgeCount`, `packgeId` and `publicData` are ignored.
   - Commands are never reassembled from chunks. Each PUBLISH must be a complete command.
7. **De-duplication.** This applies only when element 0 has a `batchId`. The dongle compares the
   printed `businessData` with the previous command it accepted. If they are identical, the command is
   ignored completely: no handler runs, and no reply or retained clear is sent. Only the last command
   is remembered.
8. **`cmdType`.** It must be a JSON string. A number is rejected.
9. **Remote-control gate.** The command's group must be allowed by the remote-control mask
   ([cloud-mqtt.md](cloud-mqtt.md#remote-control-mask)). A refused command gets no handler.
10. **Dispatch** is by **substring match**. The dongle walks its command table in order and takes the
    first entry whose number appears anywhere in `cmdType`. Neither the topic family nor the serial
    in the topic is checked. Any subscribed `cmd` topic can therefore carry any allowed command.
11. **Retained clear.** After dispatch, the dongle publishes a 0-byte, QoS 1, retained message to the
    incoming topic. This removes the command from the broker so it is not delivered again after a
    reconnect.

A command that fails to parse, or whose number is not in the table, gets no reply.

### Reply shape

Replies go on the reply topic in an ordinary outbound envelope. `businessData` is an array of one
element. That element starts with the command's `batchId` and `cmdType`, then `dataTime` and a
numeric `result`, followed by the command-specific fields described in
[cloud-commands.md](cloud-commands.md).

## Worked examples

These examples use a placeholder serial `A2xxxxxxxxx` and illustrative values. On the wire, every
payload below is zlib-compressed, because the compress flag is on by default.

### A single-chunk upload

This is one `rundata/5min` message for a hybrid inverter. The topic is
`isolarcloud/devicedata/pub/rundata/5min/A2xxxxxxxxx/enum_0_0_1`.

```json
{"messageId":"A2xxxxxxxxx_20261024143212_00007",
 "packgeCount":"1",
 "packgeId":"1",
 "publicData":{"productType":"1824","productName":"WiNet-S2",
               "productSN":"A2xxxxxxxxx","sendTime":"20261024143212"},
 "businessData":[{"devCode":1,"devType":14,"dataTime":"20261024143000",
                  "data":{"13011":"2410","13003":"2650","13141":"0.655","13150":"240"}}]}
```

Line breaks are added here for reading. The dongle prints compact JSON with no whitespace.

### A multi-chunk upload

Suppose a `propertydata` element prints to 3000 bytes. It goes out as two envelopes with the same
`messageId`:

```json
{"messageId":"A2xxxxxxxxx_20261024143215_00009","packgeCount":"2","packgeId":"1",
 "publicData":{"productType":"1824","productName":"WiNet-S2","productSN":"A2xxxxxxxxx","sendTime":"20261024143215"},
 "businessData":"[{\"batchId\":\"999999999999999\",\"devCode\":1,\"devType\":14, … first 2560 bytes …"}
```

```json
{"messageId":"A2xxxxxxxxx_20261024143215_00009","packgeCount":"2","packgeId":"2",
 "publicData":{"productType":"1824","productName":"WiNet-S2","productSN":"A2xxxxxxxxx","sendTime":"20261024143216"},
 "businessData":"… remaining 440 bytes …,\"result\":1}]"}
```

The second envelope is sent only after the broker has acknowledged the first. Its `sendTime` is one
second later than the first's.

### The will

The topic is `isolarcloud/devicedata/pub/willdata/A2xxxxxxxxx/enum_0_0_1`, published with QoS 1 and
retained.

```json
{"messageId":"A2xxxxxxxxx_20261024142950_00001","packgeCount":"1","packgeId":"1",
 "publicData":{"productType":"1824","productName":"WiNet-S2","productSN":"A2xxxxxxxxx","sendTime":"20261024142950"},
 "businessData":[{"devCode":247,"devType":22,"beat":0}]}
```

### An inbound command and its reply

The cloud publishes a read request on `isolarcloud/devicedata/cmd/A2xxxxxxxxx/getparam/enum_0_0_1`.
The envelope header is ignored, so only `businessData` matters:

```json
{"businessData":[{"cmdType":"10325","batchId":"123456789012345","devCode":1,
                  "readOnly":1,"data":{"p1":{"modbusAddress":"5017","dataType":"6"}}}]}
```

The dongle then sends two messages:

1. The reply, in an outbound envelope on
   `isolarcloud/devicedata/rsp/getparam/A2xxxxxxxxx/enum_0_0_1`:

   ```json
   {
     "businessData": [
       {
         "batchId": "123456789012345",
         "cmdType": "10325",
         "dataTime": "20261024143301",
         "mode": 0,
         "readOnly": 1,
         "result": 1,
         "data": {
           "p1": {
             "modbusAddress": "5017",
             "readValue": "…",
             "readResult": "1"
           }
         }
       }
     ]
   }
   ```

2. A 0-byte retained publish on `isolarcloud/devicedata/cmd/A2xxxxxxxxx/getparam/enum_0_0_1`.

The field-level meaning of the request and the reply is in [cloud-commands.md](cloud-commands.md).

## Not yet known

- The MQTT receive path drops a PUBLISH whose remaining length is over 2048 bytes, which would be a
  tighter limit than the 4096-byte rule in step 4. Whether every command goes through that check, and how
  the limit relates to the compressed size on the wire, is not confirmed.
- It is not known whether the cloud ever publishes commands with the retain flag set, which is the
  case the retained clear guards against.
