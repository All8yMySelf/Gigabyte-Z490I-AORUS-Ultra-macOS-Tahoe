# Test record

Test date: 6 August 2026, Australia/Adelaide.

Tested on macOS Tahoe 26.6 build 25G72 with OpenCore 1.0.7, Lilu 1.7.2, AppleIGC 1.7 and the custom WhateverGreen 1.7.2 build described in [BUILD-PROVENANCE.md](BUILD-PROVENANCE.md).

## Boot and displays

| Test | Result | Notes |
|---|---|---|
| AOC DisplayPort only | Pass | Known-reliable diagnostic boot path. |
| LG HDMI only | Pass | Included a short black graphics-handoff interval. |
| AOC boot, then LG HDMI hot-plug | Pass | Both displays and LG audio became available. |
| AOC + LG connected before startup, test 1 | Pass | First startup with WEG PR #127 build. |
| AOC + LG connected before startup, test 2 | Pass | Full shutdown, wait, then cold boot. |
| Third display hot-plugged into running two-display desktop | Pass | Three independent display objects and audio paths. |
| All three displays operating after startup | Pass | One startup confirmation; longer observation recommended. |

The three displays were visible on separate Navi23 framebuffer paths. RX 6600 XT remained detected with 8 GB VRAM, PCIe x16 and Metal acceleration.

## Ethernet

The Intel I225-V was positively matched to AppleIGC at boot. Tahoe's native `AppleEthernetE1000` DriverKit driver was not attached.

| Test | Result |
|---|---|
| Cable inserted after login | Pass |
| DHCP and IPv4/IPv6 addressing | Pass |
| Sustained download/upload stress | Pass |
| Cold boot with Ethernet already connected | Pass |
| Ethernet retained while adding second and third displays | Pass |
| Sequential Apple network quality test | Pass |

The physical link negotiated at 1000baseT, full duplex with flow control. The controller supports 2.5 Gb/s, but the tested network path negotiated 1 Gb/s.

Latest speed comparison:

| Interface | Download | Upload | Base RTT |
|---|---:|---:|---:|
| AppleIGC Ethernet `en0` | 376.689 Mb/s | 97.932 Mb/s | 25.060 ms |
| itlwm/HeliPort Wi-Fi `en2` | 268.321 Mb/s | 106.213 Mb/s | 25.371 ms |

Both tests completed without a freeze, display drop or input failure.

## USB and input

- NuPhy Air96 V2 keyboard: normal letters, Backspace, Return, Command-A and navigation tests passed in Mac/wired mode.
- Razer mouse: working.
- USB microphone: enumerated and working during system tests.
- Magic Trackpad by USB: working and can coexist with the mouse.
- USB hub and mapped ports used by the current peripherals: working.

An early login problem where characters appeared to delete themselves was traced to the Delete/Backspace key having been repeatedly pressed during boot selection, not a continuing Tahoe keyboard fault.

## Audio

- LG HDMI audio: working.
- Display-audio paths for all three monitors: enumerated.
- Volume and mute keys changed audio, although Tahoe's visual feedback did not always match older macOS behaviour.
- Onboard analogue audio has not been fully validated.

## Sleep/wake

- Sleep duration: more than three minutes.
- Wake: pass; the system resumed and remained usable.
- Minor observation: audio from the video that had been playing resumed before the login password was entered.
- Long-duration sleep, repeated sleep cycles and Bluetooth recovery remain untested.

## Still not working or not fully tested

- Intel AX201 Bluetooth.
- Native Apple Wi-Fi features such as AirDrop/Handoff/Continuity.
- Long-duration/repeated sleep.
- DRM edge cases.
- More than one cold/startup test with all three monitors attached.

## Confidence level

Ethernet and two-monitor startup have repeat testing. Triple-monitor startup and short sleep/wake each have one successful controlled confirmation. Keep the recovery EFI until the system has accumulated normal daily use and additional cold boots.
