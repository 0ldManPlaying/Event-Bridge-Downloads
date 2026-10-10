# IDIS Event Bridge downloads

Public firmware and Windows discovery downloads for IDIS Event Bridge. The
product source repository remains private. No GitHub account is needed to
download the published files.

## Downloads

Latest stable firmware: **v1.5.1**, released on 10 October 2026.
See the [v1.5.1 release notes and downloads](https://github.com/0ldManPlaying/Event-Bridge-Downloads/releases/tag/v1.5.1).

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
preserving configuration, accounts, history, TLS files and the installed NVR
relay. It is not an operating-system image or a fresh-device installer.

Version management requires v1.3.1 or newer and an installed firmware helper.
Older devices need an initial operator-assisted upgrade. The device checks
architecture, database compatibility and package contents before accepting an
update. Failed startup automatically restores the previous software. Manual
rollback is available when the previous build is compatible.

Proprietary IDIS SDK headers, libraries and relay executables are not included.
The release's `SHA256SUMS` asset lists the download checksums.

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
