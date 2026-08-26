# Third-party components

The EFI bundles third-party bootloader, driver and kernel-extension binaries. Each project retains its own copyright and licence. Refer to the upstream repositories before redistributing or modifying them.

| Component | Version in EFI | Upstream |
|---|---:|---|
| OpenCorePkg | 1.0.7 | [Acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| Lilu | 1.7.2 | [Acidanthera/Lilu](https://github.com/acidanthera/Lilu) |
| AppleALC | 1.9.7 | [Acidanthera/AppleALC](https://github.com/acidanthera/AppleALC) |
| AppleIGC | 1.7 | [SongXiaoXi/AppleIGC](https://github.com/SongXiaoXi/AppleIGC) |
| AppleIntelI210Ethernet | 2.3.1 (Apple binary preserved by Dortania; enabled for the 2026-08-26 I225 test) | [Dortania/OpenCore-Install-Guide](https://github.com/dortania/OpenCore-Install-Guide/blob/master/extra-files/AppleIntelI210Ethernet.kext.zip) |
| WhateverGreen | 1.7.2 custom PR build | [Acidanthera/WhateverGreen PR #127](https://github.com/acidanthera/WhateverGreen/pull/127) |
| VirtualSMC / SMCProcessor / SMCSuperIO | 1.3.7 | [Acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) |
| RestrictEvents | 1.1.6 | [Acidanthera/RestrictEvents](https://github.com/acidanthera/RestrictEvents) |
| BlueToolFixup | 2.7.2 | [Acidanthera/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM) |
| IntelBluetoothFirmware / IntelBTPatcher | 2.5.0 development binaries; IntelBTPatcher is pre-Tahoe | [OpenIntelWireless/IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) |
| itlwm | 2.3.0 | [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm) |
| USBToolBox | 1.2.0 | [USBToolBox/kext](https://github.com/USBToolBox/kext) |
| UTBMap | 1.1 custom map | Generated for this machine with USBToolBox |
| XHCI-unsupported | 0.9.2 | Common Hackintosh USB support kext; verify provenance before updating |
| HfsPlus driver data | bundled | [Acidanthera/OcBinaryData](https://github.com/acidanthera/OcBinaryData) |

The `EFI/oc/Drivers` directory also contains OpenRuntime, OpenCanopy and ResetNvramEntry from OpenCorePkg.

AppleIGC 1.7 remains bundled as a rollback aid but is disabled in `config.plist` while the AppleIntelI210Ethernet route is tested. The AppleIntelI210Ethernet executable SHA-256 is `5cb9a99cdf5126ab2a7a28bdbe9bbdd75e072ddb31a45ff1a09843914b231027`.

No Wi-Fi credentials are stored in the EFI. HeliPort is installed separately and is not bundled here.

The displayed 2.5.0 version does not mean that the bundled IntelBTPatcher contains Tahoe support. Binary inspection found a maximum kernel value of 24 (Sequoia). No replacement Bluetooth binaries are included in this repository update; see [Future Bluetooth and Continuity work](docs/FUTURE-BLUETOOTH-AND-CONTINUITY.md).
