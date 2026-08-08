# Future Bluetooth and Continuity work

Status: future goal, researched 8 August 2026. No fix described here has been applied to the tested machine or added to the published EFI.

Everything else currently used on this Hackintosh is reported working. The practical workaround remains a wired Magic Trackpad. Because the machine is stable, Bluetooth and Apple Continuity work should be resumed only as a controlled, reversible project.

## Current failure

The Intel AX201 Bluetooth USB device (VID `8087`, PID `0026`) enumerates on HS10 and reports that firmware loaded, but Tahoe produces:

- a zero or missing Bluetooth address;
- no usable `controllerInfo`;
- `BT_ERROR_STACK_NOT_READY`; and
- a controller that remains off after restart and a full electrical power drain.

Restarting Bluetooth services did not help. Changing HS10 from connector type 0 to 255 coincided with a failed black-screen boot and was rolled back. Do not repeat that USB-map experiment as part of the first Bluetooth test.

## What the research found

The public EFI contains IntelBluetoothFirmware and IntelBTPatcher binaries labelled 2.5.0. Binary inspection established that its IntelBTPatcher is a pre-Tahoe upstream development build:

- executable SHA-256: `f1863bea810478e027f7207112685f8ac1edc7733d3705f6d908064ab3fb704c`;
- Mach-O UUID: `6E452EF4-4BD3-36F2-8A67-37D25247A73F`;
- maximum encoded kernel: 24 (Sequoia), not 25 (Tahoe); and
- no Tahoe 26.5+ BLE-hook symbols found in the later fork.

This explains the public package's failure signature: IntelBTPatcher can load yet skip Tahoe initialization. The remaining uncertainty is whether the live EFI binaries are identical to the sanitized public package; they have not yet been compared read-only.

The leading candidate for a future diagnostic test is [lshbluesky/IntelBluetoothFirmware v2.5.1](https://github.com/lshbluesky/IntelBluetoothFirmware/releases/tag/v2.5.1), tag commit `9e7bff5e959a32b47399d40cd3cc6af43b41b401`. It extends the supported kernel range to Tahoe and adds later Tahoe BLE handling. Reports on other AX201/Tahoe systems make controller recovery plausible, but no report matches this exact board, AX201 mapping and Tahoe build.

Verified candidate hashes from the research:

| Artifact | SHA-256 |
|---|---|
| `IntelBluetooth-2.5.1-RELEASE.zip` | `0e58a31993657ee2227fd548dff6d0cdf38bc9e4cb1495816e148c8d6ab43e5c` |
| IntelBluetoothFirmware 2.5.1 executable | `2da7a9d1213fbb0ba1a6b11396ea416fd06ba4597dfca569aa1c6f53ada25617` |
| IntelBTPatcher 2.5.1 executable | `f3e2547873c7505d3a83e8d60452f9f09dc0858495934201ff5ade52aee174b3` |

Do not use the release's `IntelBluetoothInjector.kext` on Monterey or newer.

## Important safety qualification

The fork is not being recommended for unattended or proven daily use. Independent source review identified stability and memory-safety concerns in its asynchronous USB/BLE kernel hook, including allocation-length handling, short-transfer validation, completion ownership and unsynchronised shared state. These are stability concerns, not evidence of malware.

The relevant commits and lightweight tag are not fully signed, the release lacks a reproducible-build attestation, and the BLE changes do not have a formal upstream review trail. A separate test EFI makes configuration rollback easier but cannot prevent a kernel panic or filesystem damage caused by a crash.

## Safe resumption point

Before changing anything:

1. Hash the live IntelBTPatcher and IntelBluetoothFirmware executables and record their Mach-O UUIDs.
2. Compare them with the public package. Stop and inspect independently if they differ.
3. Reconfirm the cold-boot failure signature: firmware loaded, zero/missing address and controller not ready.
4. Verify the candidate archive and executable hashes listed above.
5. Confirm two recovery paths: the untouched emergency USB and a dated copy of the current working internal EFI.
6. Save current work and ensure user data is backed up.

Only after those read-only gates pass, and only with explicit approval, construct a separate one-variable test EFI:

- replace IntelBluetoothFirmware and IntelBTPatcher together with the v2.5.1 release pair;
- keep BlueToolFixup 2.7.2;
- keep the existing `-ibtcompatbeta` argument for the controlled comparison, without claiming current upstream documentation requires it;
- keep HS10 at connector type 0;
- do not change the USB map, NVRAM values, kext order, Wi-Fi kext, SIP, SecureBootModel, ACPI, BIOS, display or Ethernet settings; and
- do not add IntelBluetoothInjector.

Objective pass/fail checks should include a nonzero Bluetooth address, controller information, discovery and pairing, restart, cold boot and sleep/wake. Stop and roll back after any panic, freeze, new USB failure or loss of an already-working function.

## Bluetooth is not the same as Continuity

Even if AX201 Bluetooth is restored, the current `itlwm.kext` plus HeliPort Wi-Fi stack is exposed to macOS as Ethernet rather than native AirPort and does not provide the required Apple Wireless Direct Link path.

| Feature with current itlwm/HeliPort | Expected status |
|---|---|
| Ordinary Bluetooth input/audio/pairing | Plausible only if a future Bluetooth test succeeds |
| AirDrop | Unavailable |
| Handoff | Unavailable |
| Universal Clipboard | Unavailable |
| Continuity Camera | Unavailable |
| Instant Hotspot | Unavailable as a native workflow |
| Apple Watch unlock | No reliable support |

Switching to AirportItlwm would be a separate configuration project. It may provide limited Handoff and Universal Clipboard support, but it still does not supply reliable AirDrop or Continuity Camera support. Do not combine that experiment with the first Bluetooth diagnosis.

## Possible compatible Wi-Fi-card project

If a compatible card is found, record its exact label, chipset, Apple part number, connector keying, adapter and antenna connections before installing it.

The AX201 uses the board's CNVio2 socket. Do not assume that a standard Broadcom A/E-key PCIe/USB module will operate in that socket. The motherboard's full-size PCIe slot is occupied by the RX 6600 XT.

Two older, exact-board examples document a different route:

- [sbfkcel/Gigabyte-Z490i-Intel-10700k-5700XT](https://github.com/sbfkcel/Gigabyte-Z490i-Intel-10700k-5700XT) used an Apple/Broadcom card arrangement on this board.
- [bluedrank/Z490i-Aorus-ultra-hackintosh](https://github.com/bluedrank/Z490i-Aorus-ultra-hackintosh) documents a Broadcom Apple card through an M.2 storage/PCIe adapter plus a separate internal USB Bluetooth lead.

That path may consume a storage M.2 slot and requires checks for physical clearance, antenna connectors, the internal USB route and USB mapping. These examples concern older macOS versions and do not prove Tahoe 26.6 compatibility.

On Tahoe, legacy Broadcom Wi-Fi may require OpenCore Legacy Patcher-style root patches, reduced SIP protection and re-patching after macOS updates. Treat it as a distinct higher-risk project with its own backup and rollback plan. A USB Bluetooth dongle may be a reversible ordinary-Bluetooth alternative if its exact chipset is verified on Tahoe, but it cannot provide the missing native Wi-Fi/AWDL path by itself.

## Do not try casually

- Do not change HS10 to connector type 255 again.
- Do not reset NVRAM without a new, specific reason.
- Do not add IntelBluetoothInjector on Tahoe.
- Do not change kext order or a second variable during the first diagnostic test.
- Do not place an unidentified Broadcom card into the CNVio2 socket.
- Do not begin OCLP/root patching merely to test ordinary Bluetooth.
- Do not assume a separate EFI eliminates kernel-panic or filesystem risk.

The best future starting point is the read-only live-binary comparison, followed by identifying any spare Wi-Fi card before deciding between ordinary AX201 Bluetooth and a separate full-Continuity hardware project.
