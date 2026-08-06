# Third-party components

The EFI bundles third-party bootloader, driver and kernel-extension binaries. Each project retains its own copyright and licence. Refer to the upstream repositories before redistributing or modifying them.

| Component | Version in EFI | Upstream |
|---|---:|---|
| OpenCorePkg | 1.0.7 | [Acidanthera/OpenCorePkg](https://github.com/acidanthera/OpenCorePkg) |
| Lilu | 1.7.2 | [Acidanthera/Lilu](https://github.com/acidanthera/Lilu) |
| AppleALC | 1.9.7 | [Acidanthera/AppleALC](https://github.com/acidanthera/AppleALC) |
| AppleIGC | 1.7 | [SongXiaoXi/AppleIGC](https://github.com/SongXiaoXi/AppleIGC) |
| WhateverGreen | 1.7.2 custom PR build | [Acidanthera/WhateverGreen PR #127](https://github.com/acidanthera/WhateverGreen/pull/127) |
| VirtualSMC / SMCProcessor / SMCSuperIO | 1.3.7 | [Acidanthera/VirtualSMC](https://github.com/acidanthera/VirtualSMC) |
| RestrictEvents | 1.1.6 | [Acidanthera/RestrictEvents](https://github.com/acidanthera/RestrictEvents) |
| BlueToolFixup | 2.7.2 | [Acidanthera/BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM) |
| IntelBluetoothFirmware / IntelBTPatcher | 2.5.0 | [OpenIntelWireless/IntelBluetoothFirmware](https://github.com/OpenIntelWireless/IntelBluetoothFirmware) |
| itlwm | 2.3.0 | [OpenIntelWireless/itlwm](https://github.com/OpenIntelWireless/itlwm) |
| USBToolBox | 1.2.0 | [USBToolBox/kext](https://github.com/USBToolBox/kext) |
| UTBMap | 1.1 custom map | Generated for this machine with USBToolBox |
| XHCI-unsupported | 0.9.2 | Common Hackintosh USB support kext; verify provenance before updating |
| HfsPlus driver data | bundled | [Acidanthera/OcBinaryData](https://github.com/acidanthera/OcBinaryData) |

The `EFI/oc/Drivers` directory also contains OpenRuntime, OpenCanopy and ResetNvramEntry from OpenCorePkg.

No Wi-Fi credentials are stored in the EFI. HeliPort is installed separately and is not bundled here.
