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

## Upstream version status

This section is automatically updated by the build workflow on each run.

<!-- BEGIN AUTO-UPDATED UPSTREAM STATUS -->
| Component | Version | Release date (UTC) | Upstream release |
| --- | --- | --- | --- |
| MVDSV | 1.11 | 2025-02-27 | [release](https://github.com/QW-Group/mvdsv/releases/tag/1.11) |
| KTX | 1.46 | 2025-09-14 | [release](https://github.com/QW-Group/ktx/releases/tag/1.46) |
| QWFWD | 1.30 | 2025-02-25 | [release](https://github.com/QW-Group/qwfwd/releases/tag/1.30) |
| QTV | 152a43dadb95 | 2026-03-02 | [commit](https://github.com/QW-Group/qtv/commit/152a43dadb959f8c36de55c5b3b23d0431c9c344) |
| nQuake distfiles | snapshot | 2026-02-28 | [release](https://github.com/nQuake/distfiles/releases/tag/snapshot) |

Last refreshed (UTC): 2026-05-02T22:18:20Z
<!-- END AUTO-UPDATED UPSTREAM STATUS -->

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

## QuakeWorld clients

This repository builds the **server** side only. Players need a separate
QuakeWorld client to connect and play. The most common options in 2026 are:

- [ezQuake](https://github.com/QW-Group/ezquake-source)
  - The dominant modern QuakeWorld client. Actively maintained, feature-rich
    HUD, built-in browser, demo playback, cross-platform. Most competitive
    players and pick-up game (PUG) communities use this.
- [FTE QuakeWorld (FTEQW)](https://fte.triptohell.info/)
  - A highly capable alternative client with extended rendering and modding
    features. Compatible with QuakeWorld servers and supports a wider range of
    Quake engine extensions.
- [nQuake](https://nquake.com/)
  - A ready-to-play installer for Windows and Linux that bundles ezQuake with
    configs, maps, and sensible defaults. The easiest starting point for new
    players. Uses the same nQuake distfiles content that this repository
    packages for the server side.
