# qwserver-build

Automated GitHub Actions build that assembles a LinuxGSM-ready QuakeWorld
server bundle from the current nQuake distfiles snapshot and publishes both a
dated release and a moving `latest` release.

## Why this exists

LinuxGSM `qwserver` is still pinned to the legacy archive:

- `http://linuxgsm.download/QuakeWorld/nquake.server.linux.190506.full.tar.xz`

That means QuakeWorld server installs are not automatically refreshed by
upstream component updates. This repository gives you a repeatable way to build
and publish refreshed bundles, but LinuxGSM will only consume them
automatically if you also update the installer side to point at your new URL
and checksum.

## What the workflow does

1. Reads upstream reference versions for:
   - MVDSV (`QW-Group/mvdsv` latest release)
   - KTX (`QW-Group/ktx` latest release)
   - QWFWD (`QW-Group/qwfwd` latest release)
2. Downloads the current QuakeWorld server distfiles from the
   `nQuake/distfiles` `snapshot` release.
3. Extracts and re-packs them into the flat archive layout LinuxGSM expects.
4. Produces two bundle names:
   - `nquake.server.linux.<YYYYMMDD>.full.tar.xz`
   - `nquake.server.linux.latest.full.tar.xz`
5. Produces both `MD5SUMS` and `SHA256SUMS`.
6. Publishes:
   - a dated GitHub release tagged `build-<YYYYMMDD>`
   - a moving GitHub release tagged `latest`

## Schedule

- Weekly: Monday at 03:00 UTC
- Manual: `workflow_dispatch`

## Release outputs

- Dated artifact for traceable builds:
  - `nquake.server.linux.<YYYYMMDD>.full.tar.xz`
- Stable artifact for automation targets that need a fixed filename:
  - `nquake.server.linux.latest.full.tar.xz`
- Checksums:
  - `MD5SUMS`
  - `SHA256SUMS`

## Important limitation

This repository does not change LinuxGSM by itself.

If you want fully automatic consumption from LinuxGSM, you still need to patch
the QuakeWorld install source in LinuxGSM or override it locally so that it
uses:

- your stable download URL
- the matching filename
- the matching MD5 checksum

At the time of writing, LinuxGSM hardcodes the old `190506` archive and MD5 in
its `install_server_files.sh` module.

## Current source of truth

- Bundle contents come from `https://github.com/nQuake/distfiles/releases/tag/snapshot`
- Release notes record the latest MVDSV, KTX, and QWFWD release tags for
  traceability

## Practical use

If your goal is "auto builds" rather than "build from source", the moving
`latest` release is the useful output of this repository.

If your goal is "LinuxGSM installs update themselves", this repo is only half
the solution. The other half is changing the download URL and MD5 that
`qwserver` uses when installing.
