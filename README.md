# ether-trees

Every upstream source tree a **Nextbit Robin** (codename `ether`, Snapdragon 808 / msm8992) build
depends on, kept in one place so the phone stays buildable after its upstreams are gone. Two kinds:

- **Recovered** — origins already deleted; rebuilt from [Software Heritage](https://archive.softwareheritage.org/)
  and checked in flat on `main`.
- **Mirrored** — origins still up as of 2026-09-14, but for a device LineageOS dropped after 18.1;
  kept as `mirror/<repo>/<branch>` branches with their full upstream history, unmodified.

Apache-2.0 / GPL-2.0 source only. **No proprietary blobs**: `TheMuppets/proprietary_vendor_nextbit`
is deliberately not here. Take it from TheMuppets (`lineage-18.1`), or run the device tree's
`extract-files.sh` against the stock `Robin_Nougat_108` firmware.

Consumer: [ether-lineage](https://github.com/TheDBP/ether-lineage) (LineageOS 20.0 for the Robin, released; 21 in progress).

## Branches

`main` holds this README and the two recovered trees below. Everything else is a mirror branch named
`mirror/<repo>/<branch>`, an unmodified copy of `LineageOS/<repo>` at that branch:

| repo | branch | why |
|---|---|---|
| `android_kernel_nextbit_msm8992` | `lineage-18.1` | the only kernel for this SoC that boots a modern userspace (3.10, last touched 2024-02). GPL-2.0: this branch is also the corresponding-source offer for every `ether-lineage` release |
| `android_device_nextbit_ether` | `lineage-18.1` | LineageOS's last official device tree (2021-08); the 19.1 tree on `main` derives from it |
| `android_hardware_qcom_audio` | `lineage-18.1-caf-msm8994` | msm8994-family CAF HALs, frozen at 18.1; newer branches dropped msm8992 |
| `android_hardware_qcom_display` | `lineage-18.1-caf-msm8994` | same |
| `android_hardware_qcom_media` | `lineage-18.1-caf-msm8994` | same |

To build from a mirror branch instead of the origin, point the manifest project at this repo:

```xml
<project name="TheDBP/ether-trees" path="kernel/nextbit/msm8992" remote="github"
         revision="mirror/android_kernel_nextbit_msm8992/lineage-18.1" />
```

## Recovered trees (on `main`)

### `device_nextbit_ether/` — Nextbit Robin device tree, lineage-19.1

| | |
|---|---|
| origin | `gitlab.com/TipzTeam/android-trees/android_device_nextbit_ether` |
| branch | `lineage-19.1` |
| SWH snapshot | `7c668c96` |
| captured | 2024-07-08 |
| status | **origin deleted** — the GitLab group is gone |

The only real lineage-19.1 device tree for the Robin. LineageOS never had a 19.1 branch for this
device, so ports otherwise run the 18.1 tree with 19.1 fixes bolted on. This tree already solves
several of them properly:

- `TARGET_SUPPORT_HAL1 := false`, so the legacy camera HAL is never built
- audio HAL `@6.0` instead of `@2.0`, with a matching VINTF manifest
- the ELF-prebuilt flag set correctly

### `sepolicy-legacy/` — pre-rename qcom legacy SELinux policy (apq8084, msm8916, msm8974, msm8992, msm8994 …)

| | |
|---|---|
| origin | `gitlab.com/TipzTeam/android_device_qcom_sepolicy-legacy` |
| branch | `lineage-19.0` |
| SWH snapshot | `19ca6f9498d896be9c80d258e8013b04efef9b0f` |
| captured | 2022-02-19 |
| status | **origin deleted** |

The old layout: lowercase `sepolicy.mk`, `common/`, `legacy-common/`, and per-SoC directories
**including `msm8992` and `msm8994`**. LineageOS's own `android_device_qcom_sepolicy` dropped this
shape — every current branch ships `SEPolicy.mk` with `generic/legacy/qva`, and none carry msm8992.

Pinning the wrong repo at `device/qcom/sepolicy-legacy` is what makes a msm8992 port spend days
restoring ~30 SELinux types, six domains, four `te_macros` and their labels by hand. It is all here,
correct and complete. Not Robin-specific: any msm8974/msm8916/msm8994-era port that needs
`device/qcom/sepolicy-legacy` can vendor this directory.

## Verifying provenance

Nothing here has been modified from what Software Heritage holds. To check for yourself:

```sh
# the origin record still exists even though the repo does not
curl -s "https://archive.softwareheritage.org/api/1/origin/\
https://gitlab.com/TipzTeam/android-trees/android_device_nextbit_ether.git/get/"

# list its archived visits and snapshots
curl -s "https://archive.softwareheritage.org/api/1/origin/\
https://gitlab.com/TipzTeam/android-trees/android_device_nextbit_ether.git/visits/"
```

SWH's vault can cook a snapshot into a downloadable tarball. Two things that cost time when doing
that: the `raw/` endpoint 302-redirects to Azure, so use `curl -L` or you get an empty file; and
cooking is asynchronous — poll the vault URL until status is `done`.

## Using them

Point a local manifest at this repo, or vendor the directories in. If you use
[rom-forge](https://github.com/TheDBP/rom-forge), declare them so they survive `repo sync`, which
prunes any worktree that is not a manifest project:

```sh
VENDORED_PROJECTS="vendored/device_nextbit_ether:device/nextbit/ether \
                   vendored/sepolicy-legacy:device/qcom/sepolicy-legacy"
```

## Credit

Original authorship is TipzTeam's, LineageOS's and the AOSP/CAF contributors these trees derive from. This
repository adds nothing but continued availability. If a maintainer wants it taken down or moved,
open an issue.
