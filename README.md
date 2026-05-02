# qwserver-build

Automated GitHub Actions build that creates a LinuxGSM-ready QuakeWorld server
bundle and publishes it as a GitHub Release artifact.

## Why this exists

LinuxGSM `qwserver` currently relies on a pinned static archive. This repository
provides a repeatable build-and-release pipeline so refreshed bundles can be
published without manual packaging.

## What the workflow does

1. Reads upstream component versions for:
   - MVDSV (`QW-Group/mvdsv` latest release)
   - KTX (`QW-Group/ktx` latest release)
   - QWFWD (`QW-Group/qwfwd` latest release)
2. Downloads QuakeWorld server distribution files from the nQuake distfiles
   snapshot release.
3. Extracts and assembles a LinuxGSM-friendly archive layout.
4. Produces `nquake.server.linux.<date>.full.tar.xz` and `SHA256SUMS`.
5. Publishes a GitHub Release with notes and artifacts.

## Schedule

- Weekly: Monday at 03:00 UTC
- Manual: `workflow_dispatch`

## Output artifact

- `nquake.server.linux.<YYYYMMDD>.full.tar.xz`

## Notes

- This workflow currently packages the latest `snapshot` distfiles set from
  `nQuake/distfiles`.
- Release notes include upstream component versions for traceability.
