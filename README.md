# Gigabyte Z490I AORUS Ultra — macOS Tahoe Hackintosh

A tested OpenCore EFI and troubleshooting record for macOS Tahoe on the Gigabyte Z490I AORUS Ultra, Intel Core i7-10700K and AMD Radeon RX 6600 XT.

This configuration boots three monitors and provides accelerated AMD graphics, Intel Wi-Fi through HeliPort, HDMI/DisplayPort audio, USB peripherals and short sleep/wake. AppleIGC 1.7 was the tested Intel I225-V Ethernet solution, but a long-sleep Tahoe panic reported on 26 August 2026 prompted a controlled switch to Apple's AppleIntelI210Ethernet 2.3.1 route. The new Ethernet route is configured but has not yet been reboot-tested.

As of 26 August 2026, Bluetooth and native Apple Continuity features remain future goals; no experimental Bluetooth fix is included in this repository. See the [I225 long-sleep Ethernet test record](docs/ETHERNET-I225-LONG-SLEEP-TEST-2026-08-26.md) before using or evaluating the current network configuration.

> [!CAUTION]
> This is a hardware-specific reference, not a universal EFI. The included `config.plist` has deliberately blank/zeroed SMBIOS identity fields. Generate your own values before booting it. Keep a known-working recovery USB and a backup of your existing EFI.

## Tested hardware and software

| Component | Tested configuration |
|---|---|
| Motherboard | Gigabyte Z490I AORUS Ultra (mini-ITX) |
| CPU | Intel Core i7-10700K, 8 cores / 16 threads, 3.8 GHz |
| Memory | 32 GB DDR4-3200 |
| GPU | AMD Radeon RX 6600 XT (Navi23), 8 GB, PCIe x16, Metal acceleration |
| Ethernet | Intel I225-V 2.5 Gb onboard NIC, revision 2 |
| Wi-Fi / Bluetooth | Intel AX201 |
| macOS | macOS Tahoe 26.6, build 25G72 |
| SMBIOS | iMac20,1 |
| Bootloader | OpenCore 1.0.7 |
| Main display test | AOC Q3279WG5B over DisplayPort |
| Second display | LG IPS QHD over HDMI, including HDMI audio |
| Third display | KAMN32QF7TB; three-display operation verified |

## Current status

### Working

- Internal OpenCore boot into macOS Tahoe 26.6.
- RX 6600 XT acceleration, 8 GB VRAM, PCIe x16 and Metal.
- Two monitors attached before startup: two consecutive successful tests.
- Three simultaneous monitors: hot-plug and one all-connected startup test passed.
- LG HDMI audio and the other display-audio paths.
- Historical baseline: Intel I225-V Ethernet through AppleIGC 1.7 at a negotiated 1 Gb/s, full duplex with flow control.
- Historical baseline: Ethernet hot-plug, DHCP, IPv4/IPv6, sustained traffic and cable-connected startup.
- Intel Wi-Fi through `itlwm.kext` 2.3.0 and HeliPort.
- USB keyboard, mouse, microphone, hub and wired Magic Trackpad.
- Short sleep/wake test of more than three minutes.

### Known limitations

- Intel Bluetooth is not working on this Tahoe build. The USB device enumerates and firmware loads, but macOS does not obtain a usable controller address. Use the Magic Trackpad by USB for now.
- HeliPort Wi-Fi is not native Apple Wi-Fi. macOS System Settings may show its normal Wi-Fi switch as off even while HeliPort is connected.
- With the current `itlwm.kext`/HeliPort Wi-Fi stack, AirDrop, Handoff, Universal Clipboard, Continuity Camera, Instant Hotspot and Apple Watch unlock should not be expected. Restoring ordinary Bluetooth would not add the missing native Wi-Fi/AWDL path.
- Only one all-three-monitors-connected startup has been recorded so far. Longer observation is sensible because the original display failure was intermittent.
- After the successful sleep/wake test, previously playing media audio resumed before the account password was entered. Pause private media before sleep if that matters to you.
- Onboard analogue audio, DRM playback and long-duration sleep have not been fully validated.
- The current AppleIntelI210Ethernet 2.3.1 / `e1000=0` Ethernet configuration is pending its first boot and long-sleep validation. AppleIGC 1.7 remains bundled but disabled for rollback.
- The WhateverGreen build is from an open, unmerged pull request. It is not an official Acidanthera release.
- The tested config uses a custom SIP configuration and has OpenCore `SecureBootModel` disabled. Review those security choices for your own needs.

See [Future Bluetooth and Continuity work](docs/FUTURE-BLUETOOTH-AND-CONTINUITY.md) for the completed research, candidate diagnostic test, safety warnings and possible Broadcom hardware route. Treat that document as a future-work record, not an instruction to modify a stable installation.

## The problems this solved

### 1. Ethernet froze or panicked Tahoe

An old AppleIGC 1.5 development build produced a kernel panic in `AppleIGC::outputStart`. Disabling AppleIGC allowed Tahoe's native `AppleEthernetE1000` DriverKit driver to attach, but inserting the cable then reproducibly froze the computer. A hard power-off could also leave an unrelated APFS panic report on the next boot, which initially made the failure look like storage corruption.

**Previously tested change:** install and enable official AppleIGC 1.7. The successful short-sleep baseline used these kernel quirks:

- `DisableIoMapper = true`
- `DisableIoMapperMapping = false`

Do not copy a DMAR table from another motherboard. AppleIGC 1.8 was deliberately not used during this work because a Tahoe I225-V panic report was open at the time of testing.

On 26 August 2026, the machine reported a Tahoe networking panic after a long sleep, with the `m->m_flags & M_PKTHDR` assertion in the receive/input path. The active test configuration now disables AppleIGC, enables AppleIntelI210Ethernet 2.3.1, adds `e1000=0`, and sets `DisableIoMapperMapping = true`. The real I225-V identity `pci8086,15f3` is already matched by the Apple kext, so no fake device property or `SSDT-I225V` was added. This is a controlled test, not yet a proven replacement; see the dated Ethernet record.

### 2. Two displays went black after verbose boot

OpenCore selected the correct Tahoe volume and verbose text appeared, but both displays could then sleep indefinitely. Either monitor worked alone, and booting one display followed by hot-plugging the other worked, which isolated the problem to Tahoe's AMD connector/graphics handoff rather than USB or Ethernet.

Disabling WhateverGreen and applying only a manual AppleGraphicsDevicePolicy patch was tested and rejected: HDMI booted, but DisplayPort stopped enumerating.

**Working change:** replace official WhateverGreen 1.7.0 with the 1.7.2 build from [Acidanthera WhateverGreen PR #127](https://github.com/acidanthera/WhateverGreen/pull/127), exact source commit `6f7be38f5f82501cf713b05a82997ded1e4304d3`.

The PR addresses Tahoe-specific AMD routing and connector-autofix safety problems, including missing-symbol handling and framebuffer write bounds. No extra graphics boot argument was added:

- `agdpmod=pikera` remains enabled.
- `-radnoprop` was not added.

This produced two successful two-monitor startups, three-monitor hot-plug and an all-three-connected startup.

## Network results

Before the long-sleep panic, Apple's `networkQuality` was run sequentially against the AppleIGC baseline and forced to each interface using HTTP/1.1 while all three displays remained active:

| Interface | Download | Upload | Base latency |
|---|---:|---:|---:|
| Ethernet (`en0`) | 376.7 Mb/s | 97.9 Mb/s | 25.1 ms |
| HeliPort Wi-Fi (`en2`) | 268.3 Mb/s | 106.2 Mb/s | 25.4 ms |

Ethernet was approximately 40% faster for downloads. Upload was near the service's roughly 100 Mb/s ceiling on both interfaces.

## Before you use this EFI

1. Read [README-FIRST.txt](README-FIRST.txt).
2. Generate your own valid iMac20,1 SMBIOS identity using a trusted tool such as GenSMBIOS and the [Dortania PlatformInfo guide](https://dortania.github.io/OpenCore-Install-Guide/config.plist/comet-lake.html#platforminfo).
3. Set all four values in `EFI/oc/config.plist` under `PlatformInfo -> Generic`:
   - `SystemSerialNumber`
   - `MLB`
   - `SystemUUID`
   - `ROM`
4. Review the custom SIP and disabled Secure Boot settings. They reproduce the tested machine, but may not be suitable for yours.
5. Back up your present EFI and prepare a separate known-working OpenCore USB.
6. Copy the `EFI` directory to the target EFI System Partition.
7. Install HeliPort separately if you intend to use the bundled `itlwm.kext`; Wi-Fi credentials are not stored in this repository.
8. Reset NVRAM from OpenCore after installing or substantially changing the configuration.

Do not transplant this folder if your board, CPU generation, GPU, network controller or USB port layout differs. In particular, `UTBMap.kext` is specific to the tested system's USB layout.

## Repository contents

- `EFI/` — sanitized copy of the tested OpenCore configuration.
- `README-FIRST.txt` — the required identity and recovery warning.
- `docs/TESTING.md` — test history and observed results.
- `docs/TROUBLESHOOTING.md` — symptoms, rejected experiments and recovery advice.
- `docs/FUTURE-BLUETOOTH-AND-CONTINUITY.md` — evidence, limitations and a gated plan for possible future work.
- `docs/BUILD-PROVENANCE.md` — custom WhateverGreen build provenance.
- `docs/ETHERNET-I225-LONG-SLEEP-TEST-2026-08-26.md` — panic, backup, exact config change, validation and rollback record.
- `THIRD_PARTY.md` — bundled component versions and upstream projects.

## Important security note

No real SMBIOS serial, MLB, UUID, ROM value, MAC address, LAN address, disk identifier, Wi-Fi password or local user path is intentionally included. The published EFI uses zero/blank identity placeholders and must be personalized before use.

## Disclaimer

Hackintosh configurations can stop booting after firmware, macOS, OpenCore or kext updates. This repository records one successful hardware combination and does not promise support for other machines. Use it at your own risk, keep recoverable backups, and comply with the licences and terms that apply to macOS and every bundled third-party component.
