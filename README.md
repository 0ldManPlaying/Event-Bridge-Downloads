# IDIS Event Bridge downloads

Public firmware and Windows discovery downloads for IDIS Event Bridge. The
product source repository remains private. No GitHub account is needed to
download the published files.

## Downloads

Latest stable firmware: **v1.5.4**, released on 20 October 2026.
See the [v1.5.4 release notes and downloads](https://github.com/0ldManPlaying/Event-Bridge-Downloads/releases/tag/v1.5.4).

- [Latest release and release information](https://github.com/0ldManPlaying/Event-Bridge-Downloads/releases/latest)
- [Windows x64 discovery tool ZIP](https://github.com/0ldManPlaying/Event-Bridge-Downloads/releases/latest/download/IDIS-Discover-Windows-x64.zip)
- [Windows x64 discovery EXE](https://github.com/0ldManPlaying/Event-Bridge-Downloads/releases/latest/download/IDIS-Discover.exe)

The discovery tool is portable: extract the ZIP and start `IDIS-Discover.exe`.
It finds local bridges, shows their installed version and checks the latest stable
firmware automatically. No installation, Go runtime or GitHub login is required.

The Windows executable does not have an Authenticode publisher signature. Firmware
downloads made by the tool are checked against an Ed25519-signed catalog and a
SHA256 checksum. Windows code signing and the download catalog signature are
different mechanisms.

## Use the discovery tool

- Enter `1` to open the first device's web interface.
- Enter `U 1` to download newer firmware when available and open that device's
  Settings > Version management page. Sign in, select the downloaded ZIP, review
  its version and confirm the update on the device.
- Enter `D` to download the latest verified firmware without selecting a device.
- Enter `T` to download the verified discovery tool ZIP.
- Enter `R` to open the release information. Press Enter to exit.

Downloaded files are saved in `Downloads/IDIS-Event-Bridge` under the version
directory; the tool prints the full path. The tool does not store device passwords
or install firmware automatically. Update and rollback remain available in the
device's Version management page.

Offline discovery is available with `IDIS-Discover.exe --offline`. Use
`--check-updates` to print the verified update catalog, `--download-latest` to
download firmware non-interactively, and `--json` for a local-only device inventory.

## Firmware updates

The `event-bridge-firmware-vX.Y.Z.zip` asset is software-only firmware for the
Luckfox ARMv7 device. It updates the bridge application and web interface while
preserving configuration, accounts, TLS files and the installed NVR relay.
Event history follows the configured age policy and the 50,000-event limit. It is not an operating-system image or a fresh-device installer.

Version management requires v1.3.1 or newer and an installed firmware helper.
Older devices need an initial operator-assisted upgrade. The device checks
architecture, database compatibility and package contents before accepting an
update. Failed startup automatically restores the previous software. Manual
rollback is available when the previous build is compatible.

Proprietary IDIS SDK headers, libraries and relay executables are not included.
The release's `SHA256SUMS` asset lists the download checksums.

## v1.5.4: verified NVR alarm output and clearer diagnostics

- NVR event input, read-only HTTP reachability and network-alarm output have
  separate status information. A recorder rejecting the read-only check with
  HTTP 400 no longer causes DEGRADED when actual alarm commands are accepted.
  Output errors remain visible even when incoming NVR events are connected.
- Settings and NVR destinations provide an explicit ON/OFF network-alarm test.
  Choose a configured test slot, disarm the bridge, review its linked actions
  and acknowledge the test. The bridge attempts OFF even if ON fails or the
  browser disconnects. Normal health checks never send alarms.
- Valid API acceptance and operator-confirmed receipt in the NVR are distinct.
  Only confirm receipt after checking the NVR event search or alert window.
- New NVR configurations use the alarm number displayed on the recorder:
  alarm 17 maps to API channel 16. Existing configurations and older backups
  retain compatibility numbering, preserving their physical alarm targets.
  Review associated rules and flows before changing the numbering mode.
- Alarm delivery validates the JSON acknowledgement, retains Digest
  authentication and rejects redirects, login HTML and incomplete responses.
  Authentication refusal suspends automatic requests through that client until
  its connection settings are saved again.
- Firmware downgrade and rollback checks block versions that cannot preserve
  the selected NVR numbering. Existing configuration, accounts, event history,
  TLS files, the installed relay and database schema 13 are preserved.
- New controls, diagnostics and confirmation messages support Dutch, English,
  German, French and Spanish.

Status observations are held in memory and reset after a bridge restart.
An accepted API command is not automatic proof that an NVR action occurred.
Use the HTTP or HTTPS protocol actually enabled on the recorder's web service.
No SDK migration or change to existing non-NVR output types is required.

## v1.5.3: bounded event history and manual clearing

- Event history is capped at 50,000 records. New events replace the oldest;
  configured age retention still applies, with a default of 30 days.
- Event log > Clear event log provides an explicit confirmation in all five
  languages. Clearing preserves settings, active alarms and events received
  after the confirmation was opened. The action is irreversible.
- The dashboard and Event log show the real storage usage and limit.
- Application text and JSON logs rotate at 5 MiB, with two archives per log.
  Oversized older files are trimmed, and individual stored records are bounded.

On first startup, history above 50,000 and oversized application logs are trimmed.
Export required history before upgrading. CSV export includes only currently
loaded, filtered events. A software rollback does not restore removed history
or log lines, so keep a separate recovery copy when required. SQLite reuses freed
pages; clearing does not immediately compact the database file.

Configuration, accounts, TLS files and the installed NVR relay are preserved.
There is no database schema or SDK migration. External relay/service logs follow
their host's logging policy. Existing alarm processing and outputs are unchanged.

## v1.5.2: NVR diagnostics, WebRelay-Quad and Home Assistant

- The NVR connection test uses read-only requests rather than an artificial alarm
  on channel 0. Recorders that do not support this HTTP check show a reachability
  warning and, when available, confirmation of the matching NVR event connection.
  This does not verify network-alarm delivery. Authentication and server failures
  remain errors.
- Manual tests and health checks honor the configured timeout, from 1 to 300
  seconds, with a default of 15 seconds. Real timeouts have a separate message.
- A dedicated WebRelay-Quad preset supports the original ControlByWeb
  X-WR-4R3-I/E, including X-WR-4R3-E. Select relay 1 to 4 and a hardware-timed
  pulse of 0.1 to 3,600 seconds in Devices, then use it in Rules or Flows.
  Connection tests only read status; pulse commands are not automatically retried.
- Optional read-only Home Assistant MQTT discovery automatically adds selected
  or all cameras from one NVR, including camera names, motion sensors, events
  and shared bridge diagnostics. The integration is disabled by default.
- New controls and diagnostics are available in all five interface languages.

Existing configuration, users, history, TLS files and the installed NVR relay are
preserved. There is no database schema or SDK migration. Rollback retains the new
settings, but older builds cannot operate the new integration or flow node types.

Home Assistant requires a separate host and MQTT broker. Real 32-camera discovery,
motion events and reconnect recovery were tested; permanent broker setup and
real motion-stop commissioning remain site-specific. Snapshots, video, voice
control and direct Apple Home, Google Home or Alexa integration are not included.
WebRelay-Quad is simulation-tested and requires commissioning on the actual
module, including authentication, pulse timing and electrical operation.

## v1.5.1: NVR camera discovery and manual reconnect

Settings > NVR settings > NVR events now provides Reconnect in all five
languages. It refreshes the managed NVR session and camera list, retaining
saved configuration, unsaved form entries, recent events and known camera names.
Retries require login and are limited to one per ten seconds. A refused NVR
login remains blocked until its address or credentials change.

Empty camera lists explain the connection requirement. An incompatible legacy
relay reports a specific diagnostic. Discovery and reconnection were verified
with a real NVR and 32 cameras. The firmware preserves the installed relay;
devices with an original pre-managed relay need a one-time operator-assisted
relay upgrade. There is no database schema or SDK migration.

## v1.5.0: speaker announcements and MOXA digital I/O

- IDIS network speaker destination for prerecorded messages, with file selection,
  volume, authentication, bounded playback and an audible test button.
- Staged loitering announcements with three configurable dwell thresholds,
  initially 60, 120 and 300 seconds. Optional scheduling and a stage-3 HTTP
  notification are available. Departure, disarm and source loss cancel pending
  announcements; repeated events do not restart the sequence.
- MOXA ioLogik E1200 digital inputs as event sources and digital outputs/relays
  as action destinations. Inputs work in Rules and Flows. A timed output action
  automatically attempts OFF after its configured duration or cancellation.
- MOXA output mode validation, targeted writes and readback confirmation.
  Connection tests read the output without switching it.
- Editing and deleting older saved flows, including empty test flows, with
  recovery of missing editor metadata while preserving parameters and connections.
- New controls and input labels in Dutch, English, German, French and Spanish.

Configuration, accounts, history and the installed NVR relay are preserved.
There is no database schema or SDK migration. Older firmware cannot operate the
new plugin/node types after rollback, although their settings remain stored.

Basic IDIS speaker bell playback has been confirmed audible. The full recorded
message sequence requires commissioning with the actual camera/NVR and speaker;
it monitors camera/zone alarm state rather than tracking an individual person.
MOXA support is simulation-tested; a real module has not yet been tested. Only
digital DI and DO/relay modes are supported, excluding analog, counter and pulse
generator modes. Physical output OFF cannot be guaranteed during a network or
module failure.

## v1.4.1: consistent logos on every PC

Login and sidebar logos use vector outlines, preserving their appearance on PCs
without the original font. No logo font installation or download is required.

## Changes included in v1.4.0, from v1.3.0

- Consistent alarm processing across event sources, correct destination routing,
  ordered ON/OFF delivery and cleanup when disarming or changing configuration.
- Complete encrypted application backups and transactional restore, including
  sources, destinations, accounts and credentials.
- Visible firmware version, software update and rollback in Settings.
- Authentication, recovery-code, UI and network-helper security improvements.
- Named NVR cameras in the live feed and event log. NVR status reflects an active
  event connection when the read-only HTTP probe is unsupported.
- Complete Dutch, English, German, French and Spanish interface coverage, with
  remembered browser language preference and preservation of edits when switching.

## Verified update catalog

The stable catalog is at
[`channels/stable.json`](https://raw.githubusercontent.com/0ldManPlaying/Event-Bridge-Downloads/main/channels/stable.json).
Its payload is signed with Ed25519. The discovery tool pins this public key:

```text
a0a1d9ce60398452da9e9bacb8883166d4e4028e4e17bbbc5cbdb25cf5bdc7ec
```

The catalog points to versioned release assets. A newly published release becomes
the stable download only after all its assets have been uploaded and verified.
