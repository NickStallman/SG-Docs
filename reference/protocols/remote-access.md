# Remote access and debug surfaces

This page describes the dongle's remote-maintenance switch, the reverse-tunnel
feature, the plaintext debug-log stream, and the cloud-triggered log export. Security
implications are summarised in [the security chapter](../../guide/09-security-and-privacy.md).

"The dongle" means either WiNet-S or WiNet-S2; differences are called out.

## Remote-maintenance switch (`rdswitch`, "O&M mode")

A 24-hour switch that, while open, lets the dongle accept the vendor `develop`
(level 4) login. It can be opened from iSolarCloud (a logger parameter-set command)
or from the local web API (`POST /user/rdSwitch`, shown in the web UI as an "O&M"
Enable/Disable control, and in the app as "Developer management"). The local route
requires an admin (level 3) session.

- State lives in two **volatile** logger settings: an "open" timestamp and a window
  state flag. Opening stores the current epoch time; closing clears it. Because
  both are volatile, **a reboot always closes the window.**
- The window is open while `now − open_time ≤ 86399 s` (one second short of 24 h).
  It is tested at login, and a 10-second timer independently closes it (and turns
  logging off) within 10 s of the 24-hour mark.
- What it unlocks:
  1. **The `develop` login.** A `develop` login needs both the rotating `develop`
     password and an open window; with the window closed a correct password returns
     error 116 ("R&D permission not enabled"). The login check happens only at
     login — an already-established `develop` session is **not** revoked when the
     window later expires.
  2. **The ESP-IDF log level.** While the window is open the console log level is
     raised to INFO; closed, it is set to NONE. This is why a production dongle's
     console is normally silent.
- It does **not** open any outbound stream on either model and does not touch the
  cloud link; the window itself is independent of the remote-control mask, although
  the cloud command that opens it (10384 point 17075) needs mask bit 2 (Settings).

Security-relevant points:

- The window can be opened by any admin session or by the cloud. Anyone who can
  send the cloud command can unlock the vendor login for 24 h without the site's
  involvement. The local "O&M" control is the owner-side opt-in.
- The login checks the password before the window, so a holder of a valid rotating
  password can tell from the error code (116 versus the lockout codes) whether the
  window is open, and only the wrong-password path increments the lockout counter.

## Reverse tunnel (`vpn_enable`)

Both dongles carry the configuration, status fields and a launcher for a vendor
reverse tunnel — an ngrok-style client that would dial out to a tunnel
server and expose the dongle's local port 80 through it, giving the vendor a path
back into the web UI from outside the site.

**Nothing ever starts it.** The launcher function only formats the command line
into a local buffer and returns; there is no call to any process-start API in
either image, and the helper binary is not in the shipped filesystem. The command
line is Linux shell syntax (a trailing `&`, a `killall` on the disable path), which
the dongle's RTOS has no way to run — it is carried over from an earlier
Linux-based logger product.

**Nothing reachable even sets the enable flag.** No web route, WebSocket service,
Modbus-TCP register or cloud command can set the tunnel's enable flag or host, so
the launcher never runs. A
read-only WebSocket service (`vpn`) exposes the fields but cannot change them, and
the shipped web UI never calls it. The tunnel is therefore inert: the server it
*would* use defaults to `connect.isolarcloud.com` port 8443, but the dongle never
connects.

## Plaintext debug-log stream (WiNet-S only)

The WiNet-S can be told to stream its console log to any IPv4 address and port with
a single Modbus write; the WiNet-S2 does not have this feature.

- **Trigger:** Modbus function 16 to unit 247 (the logger), starting at register
  39303 (wire 39302), 3 registers — 4 IPv4 octets followed by a big-endian port.
  Nothing is configured at boot; the feature is dormant until this write arrives.
- The write reaches the handler from **any** source that feeds the Modbus slave
  engine: the Modbus-TCP listeners, the reverse-connected Modbus-TCP client links
  (which have no source-IP filter), and the RS-485 RTU-slave forwarding slots.
  There is no authorization, write-enable flag or range check in front of it.
- The dongle then connects to the given host as a **TCP client** and replaces its
  log output function with a hook that copies every log line into a ring buffer;
  a task drains the buffer to the socket. There is no TLS, no authentication, no
  handshake and no framing — each log line is one `send()`. The dongle never reads
  from the socket, and the destination is not restricted to the LAN.
- Once connected, log lines no longer go to the local console; they come back only
  if a send fails. Nothing stops the stream except a reboot or a connection error,
  and recovery is weak (after a refused connection the task retries on a dead
  socket and never reconnects). A second write starts a second task.

What leaks: everything the firmware logs at INFO or above — IP addresses, device
and internal identifiers, and cloud and firmware-update progress messages. Anyone who can
send a Modbus write to the logger unit (over a listener, a client link or the
RS-485 RTU-slave path) can redirect a live copy of the log to a host of their
choice, with no confirmation and no way to stop it short of a reboot.

Sibling debug registers in the same table share the same lack of authentication: a
nearby register toggles a task monitor, another sets per-module log levels, and
another performs a data-model read whose result is printed to the log (and so into
this stream on the WiNet-S).

## Cloud-triggered log export

A cloud command (10329, `logexport`) makes the dongle collect log files and upload
them to an SFTP server whose address, port, user name, password and directory all
come from the command. The upload runs as an SFTP client (over libcurl/libssh2) on
the dongle. Only the upload leg is noted here; the command fields, the on-RS-485
file fetch and the result codes are covered by
[diagnostics-and-log-export.md](diagnostics-and-log-export.md).

The security-relevant point for this page: **the upload has no SSH host-key
policy.** The dongle sets no known-hosts file, no fingerprint and no key callback,
so the client accepts whatever key the server presents, and it then sends the
command-supplied password to a server the cloud chose over a channel with no server
authentication. Only a 60 s connect timeout is set (no transfer timeout, no retry).
