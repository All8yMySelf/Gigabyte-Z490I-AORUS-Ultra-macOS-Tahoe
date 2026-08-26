# Intel I225-V long-sleep panic and I210 test — 26 August 2026

## Why this change was made

The machine was running macOS Tahoe 26.6 with AppleIGC 1.7 and remained generally stable during normal use and short sleeps. After a long sleep, it failed to recover and the subsequent panic report identified the networking receive/input path, including the assertion:

```text
m->m_flags & M_PKTHDR
```

The associated path included `dlil_create_input_thread`. The objective is to preserve wired Ethernet while avoiding the AppleIGC path implicated by the report. Wi-Fi remains available as a fallback but is not the desired permanent connection.

## Safety and backups

The active system was confirmed as the sealed `MacOS26c` installation. Its EFI partition was resolved by partition UUID, not by a potentially changing `diskXsY` identifier.

Before any active config change:

- The full active `EFI` folder was copied alongside it as `Copy Working 2026-08-26`.
- Source and backup each contained 289 files; a recursive checksum comparison reported no differences.
- A Desktop archive named `Copy Working 2026-08-26.zip` was created from that backup and passed `unzip -t` with no errors.
- Pre-change `config.plist` SHA-256: `347b38165b14da042b7629788760a0e566f480ddd33b681bc821060158330cba`.

The separate recovery USB was not modified.

## Pre-change Ethernet state

- AppleIGC 1.7: present and enabled.
- AppleIntelI210Ethernet: absent.
- `e1000=0`: absent.
- `SSDT-I225V`: absent.
- Ethernet device-property spoof: absent.
- Ethernet kernel patches: absent.
- `DisableIoMapper = true`.
- `DisableIoMapperMapping = false`.

## Chosen test configuration

The AppleIntelI210Ethernet 2.3.1 archive came from Dortania's OpenCore Install Guide. Its IOKit personality directly lists `pci8086,15f3`, matching this machine's Intel I225-V, so adding a fake `device-id` or `SSDT-I225V` would be redundant. The archive SHA-256 is `0bece103ca39c08239de090e0f969602ff260dc586661389be8aab70a1b0bee8`; the installed universal executable SHA-256 is `5cb9a99cdf5126ab2a7a28bdbe9bbdd75e072ddb31a45ff1a09843914b231027`.

Only these active EFI changes were made:

1. Added `EFI/oc/Kexts/AppleIntelI210Ethernet.kext` 2.3.1.
2. Disabled the AppleIGC 1.7 `Kernel -> Add` entry. Its files remain present for rollback.
3. Added and enabled AppleIntelI210Ethernet with `Arch = x86_64`, `MinKernel = 22.0.0`, and the standard executable/plist paths.
4. Added `e1000=0` to `boot-args`.
5. Changed `DisableIoMapperMapping` from `false` to `true`; `DisableIoMapper` remains `true`.

No ACPI table, device property, kernel patch, SMBIOS field, USB map, graphics setting, Wi-Fi setting or Bluetooth setting was changed.

## Validation before reboot

- `plutil -lint`: passed.
- OpenCore 1.0.7 `ocvalidate`: passed with no issues.
- Live config matched the validated staging copy byte-for-byte.
- Installed I210 executable matched the inspected Dortania source byte-for-byte.
- Post-change `config.plist` SHA-256: `1eced707e7e832611edc41bfc15f9168dc1e692904647860c1ef75e562440b5c`.
- EFI structure was inspected and both driver folders were present; only the I210 entry is enabled.

No reboot was performed during this workflow.

## First-boot and long-sleep test plan

1. Keep the Ethernet cable disconnected for the first startup.
2. Confirm AppleIntelI210Ethernet is the loaded service and AppleIGC is not loaded.
3. Connect Ethernet, confirm DHCP and link negotiation, then run controlled traffic.
4. Test a short sleep/wake cycle.
5. Only after those checks pass, perform a controlled long-sleep test.
6. Record the result here before describing the I210 route as proven.

If the system does not boot or Ethernet fails, boot the untouched recovery USB, mount the `MacOS26c` EFI by UUID, and restore the `Copy Working 2026-08-26` folder as `EFI`. Do not mix the AppleIGC and AppleIntelI210Ethernet methods.
