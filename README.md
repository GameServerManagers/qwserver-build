# qwserver-build

Automated GitHub Actions build that assembles a LinuxGSM-ready QuakeWorld
server bundle from the current nQuake distfiles snapshot and publishes both a
dated release and a moving `latest` release.

## Upstream components in this bundle

- [MVDSV](https://github.com/QW-Group/mvdsv)
  - QuakeWorld dedicated server binary used to run the game server process.
- [KTX](https://github.com/QW-Group/ktx)
  - Server-side game logic (`qwprogs.so`), configs, and gameplay extensions
    used by modern QuakeWorld servers.
- [QWFWD](https://github.com/QW-Group/qwfwd)
  - QuakeWorld network forwarder/proxy binary commonly used alongside servers
    to improve routing and connectivity.
- [QTV](https://github.com/QW-Group/qtv)
  - QuakeTV spectator relay binary (`qtv/qtv.bin`) used for broadcasting
    matches; useful for leagues, streams, and public spectators.
- [nQuake distfiles snapshot](https://github.com/nQuake/distfiles/releases/tag/snapshot)
  - Bundled maps, configs, assets, and runtime files that are packaged together
    into the final LinuxGSM-ready archive.

These are packaged together to create one installable server
bundle that contains the core daemon plus the typical competitive QW runtime
files and tooling.

## Glossary (quick)

- nQuake
  - A QuakeWorld distribution bundle used here as the source of server
    distfiles (binaries, configs, maps, and assets).
- ezQuake
  - A QuakeWorld client used by players to connect and play.
- MVDSV
  - The dedicated QuakeWorld server process.
- KTX
  - Server-side game logic and competitive config/mode ecosystem.
- QWFWD
  - A forwarding/proxy component to help network paths and connectivity.
- QTV
  - A spectator relay service for broadcasts and public viewing.

## What you actually need

For a working modern QuakeWorld server, the practical minimum is:

- MVDSV: required (server daemon)
- KTX: required in this build model (server game logic and configs)
- nQuake distfiles content: required (maps/assets/runtime files)

Usually optional but strongly recommended:

- QWFWD: optional (network forwarding/proxy)
- QTV: optional (spectator relay)

In short: your current component set is already enough for a modern deploy.
Additional components are mostly operational add-ons (spectating, networking,
monitoring), not hard requirements for basic server functionality.

## Upstream version status

This section is automatically updated by the build workflow on each run.

<!-- BEGIN AUTO-UPDATED UPSTREAM STATUS -->
| Component | Version | Release date (UTC) | Upstream release |
| --- | --- | --- | --- |
| MVDSV | 1.11 | 2025-02-27 | [release](https://github.com/QW-Group/mvdsv/releases/tag/1.11) |
| KTX | 1.46 | 2025-09-14 | [release](https://github.com/QW-Group/ktx/releases/tag/1.46) |
| QWFWD | 1.30 | 2025-02-25 | [release](https://github.com/QW-Group/qwfwd/releases/tag/1.30) |
| QTV | 152a43dadb95 | 2026-03-02 | [commit](https://github.com/QW-Group/qtv/commit/152a43dadb959f8c36de55c5b3b23d0431c9c344) |

Last refreshed (UTC): 2026-05-02T21:38:16Z
<!-- END AUTO-UPDATED UPSTREAM STATUS -->

## Why this exists

LinuxGSM `qwserver` is still pinned to the legacy archive:

- `http://linuxgsm.download/QuakeWorld/nquake.server.linux.190506.full.tar.xz`

That means QuakeWorld server installs are not automatically refreshed by
upstream component updates. This repository gives you a repeatable way to build
and publish refreshed bundles, but LinuxGSM will only consume them
automatically if you also update the installer side to point at your new URL
and checksum.

## What the workflow does

- Reads upstream references:
  - MVDSV (`QW-Group/mvdsv` latest release)
  - KTX (`QW-Group/ktx` latest release)
  - QWFWD (`QW-Group/qwfwd` latest release)
  - QTV (`QW-Group/qtv` latest commit, because no latest release endpoint is available)
- Downloads the current QuakeWorld server distfiles from
  `nQuake/distfiles` `snapshot`.
- Extracts and re-packs them into the flat archive layout LinuxGSM expects.
- Produces two bundle names:
  - `nquake.server.linux.<YYYYMMDD>.full.tar.xz`
  - `nquake.server.linux.latest.full.tar.xz`
- Produces both `MD5SUMS` and `SHA256SUMS`.
- Generates a build comparison table in the GitHub Actions job summary,
  comparing distfiles timestamps versus upstream release/commit timestamps.
- Publishes:
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
- Release notes record the latest MVDSV, KTX, and QWFWD release tags plus the
  latest QTV commit reference for
  traceability

## Practical use

If your goal is "auto builds" rather than "build from source", the moving
`latest` release is the useful output of this repository.

If your goal is "LinuxGSM installs update themselves", this repo is only half
the solution. The other half is changing the download URL and MD5 that
`qwserver` uses when installing.
