# WhateverGreen PR #127 build provenance

The bundled `WhateverGreen.kext` is a custom Release build intended to test macOS Tahoe AMD changes. It is not the official WhateverGreen 1.7.0 release.

## Source

- Upstream project: [Acidanthera/WhateverGreen](https://github.com/acidanthera/WhateverGreen)
- Pull request: [#127](https://github.com/acidanthera/WhateverGreen/pull/127)
- PR branch author: `olwimo/tahoe-boot-fix`
- Exact source commit: `6f7be38f5f82501cf713b05a82997ded1e4304d3`
- Reported bundle version: 1.7.2
- Architecture used here: x86_64 Release kext

## Build process

The exact PR source was built with GitHub Actions. The inherited upstream workflow initially selected GitHub's newer ARM macOS runner and failed before compilation because its Xcode path was unavailable. In the temporary build fork, only the workflow runner was pinned from `macos-latest` to `macos-15`. The WhateverGreen source files remained identical to the PR commit.

The subsequent workflow passed:

- Debug build
- Release build
- Debug Clang analysis
- Release Clang analysis
- Artifact upload

The Release kext in this repository is the artifact installed for the successful dual- and triple-monitor tests.

## What PR #127 changes

The patch addresses Tahoe-specific kernel-property routing and AMD connector-autofix safety, including safe handling when Tahoe no longer provides a connector-table symbol and bounds checks around framebuffer writes. It is a plausible and successful fix for this machine's failure class, but it is not a universal RX 6600 XT multi-monitor guarantee.

## Risk statement

At the time of this test the PR was open and unmerged. Re-check upstream before updating. Do not silently replace this kext with official 1.7.0 and assume equivalent Tahoe multi-monitor behaviour. Conversely, move back to an official release when the relevant fixes are merged and verified on this hardware.
