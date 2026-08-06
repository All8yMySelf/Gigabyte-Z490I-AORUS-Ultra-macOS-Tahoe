# Troubleshooting and recovery notes

## Black screen after verbose text

### Original symptom

OpenCore selected the right Tahoe volume, verbose boot text appeared, then every connected display went black and slept. A later hard power cycle could boot without any EFI change.

### Isolation results

- AOC DisplayPort alone booted.
- LG HDMI alone booted.
- One display at boot followed by hot-plugging the other worked.
- The problem therefore followed the multi-monitor graphics handoff, not a dead display or the USB map.
- No reliable persisted panic or WindowServer report was produced by the failed boots.

### Rejected experiment

WhateverGreen was disabled and its AGDP behaviour was replaced with a manual `board-id` to `board-ix` patch. HDMI booted, but DisplayPort disappeared completely. The experiment was rolled back.

### Working change

Use the included WhateverGreen 1.7.2 custom build from PR #127. Keep `agdpmod=pikera`; do not add `-radnoprop` merely because the new build contains support for it.

If a black screen returns:

1. Wait long enough to exclude a slow login/indexing event.
2. Photograph the final visible verbose line.
3. Power off only if the system is genuinely unresponsive.
4. Boot the separate recovery USB.
5. Restore the previously backed-up EFI rather than making several speculative changes at once.
6. Retest with the AOC DisplayPort monitor only to separate graphics handoff from other changes.

## Ethernet freeze or panic

### Do not use the failed combinations

- AppleIGC 1.5 development build: produced a panic in `AppleIGC::outputStart`.
- Native Tahoe `AppleEthernetE1000` DriverKit attachment on this machine: cable insertion reproducibly froze the system.

### Working combination

- AppleIGC 1.7 enabled.
- `DisableIoMapper = true`.
- `DisableIoMapperMapping = false`.

Do not transplant another board's DMAR table. If changing the network driver, disconnect Ethernet until the loaded driver is positively identified.

## Bluetooth controller unavailable

The Intel Bluetooth USB device is visible and its firmware kexts load, but Tahoe reports no usable controller address. Restarting Bluetooth services, changing HS10 from connector type 0 to 255 and performing a full electrical power drain did not solve it. The HS10 connector experiment coincided with a black-screen boot and was fully rolled back.

Current workaround: connect the Magic Trackpad by USB. Do not assume that changing the USB map will repair the Tahoe Bluetooth software/firmware compatibility problem.

## Slow or apparently frozen desktop after login

OneDrive, Photos and Spotlight performed heavy initial scanning during early Tahoe boots. The menu bar and applications could take several minutes to settle even though memory was healthy. Pause large sync/indexing jobs while diagnosing boot stability.

## Recovery rules

- Keep a separate, known-working OpenCore USB that is not modified during experiments.
- Back up the EFI before every OpenCore, kext, USB-map or connector change.
- Change one variable per test.
- Do not repair or erase an EFI filesystem merely to update a GitHub copy.
- Never publish or reuse somebody else's SMBIOS identity.
