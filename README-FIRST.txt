READ THIS BEFORE BOOTING
========================

This EFI is for the tested Gigabyte Z490I AORUS Ultra / Intel Core i7-10700K /
AMD Radeon RX 6600 XT configuration described in README.md.

The SMBIOS identity has been sanitized for public release:

- SystemSerialNumber is blank
- MLB is blank
- SystemUUID is all zeroes
- ROM is six zero bytes

Generate your own valid, unique iMac20,1 identity and insert all four values into
EFI/oc/config.plist before booting. Never reuse somebody else's published SMBIOS.

Also review the custom SIP configuration and disabled OpenCore SecureBootModel.
Keep a separate known-working recovery USB and back up your existing EFI first.

Bluetooth is not currently working. Wi-Fi requires HeliPort. WhateverGreen 1.7.2
is a custom build from open PR #127, not an official Acidanthera release.
