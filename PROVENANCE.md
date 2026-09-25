# android_device_qcom_sepolicy-legacy @ lineage-19.0

A mirror. Nothing here is ours, and nothing here should be edited.

## Where it came from

    upstream   gitlab.com/TipzTeam/android_device_qcom_sepolicy-legacy
    branch     lineage-19.0
    source     Software Heritage snapshot 19ca6f9498d896be9c80d258e8013b04efef9b0f
    captured   2022-02-19

## Why it is mirrored

**The upstream is gone.** The TipzTeam GitLab group was deleted, and Software Heritage is the only
remaining copy. This branch exists so that a port depending on this tree does not depend on a single
archive continuing to serve it.

## Why it cannot be replaced by LineageOS's own tree

This is the pre-rename Qualcomm legacy policy: lowercase `sepolicy.mk`, `common/`, `legacy-common/`,
and per-SoC directories **including `msm8992` and `msm8994`**.

LineageOS's `android_device_qcom_sepolicy` dropped that layout. Every branch now ships `SEPolicy.mk`
with `generic/`, `legacy/` and `qva/` only, and none of them carry an `msm8992` directory. There is
no branch of the upstream repo that can stand in for this one.

Pinning the wrong repo here is not a small mistake: it costs roughly thirty SELinux types, six
domains, four `te_macros` and all their labels, which then have to be reconstructed by hand.

## Using it

    <project name="TheDBP/ether-trees"
             path="device/qcom/sepolicy-legacy"
             remote="github"
             revision="mirror/android_device_qcom_sepolicy-legacy/lineage-19.0" />

Consumed by `TheDBP/ether-lineage` (Nextbit Robin, msm8992), which vendors it at
`vendored/sepolicy-legacy` rather than syncing it, for the same reason this mirror exists.
