# Hackintosh, Gigabyte Z790 Aorus Master X, Intel Core i9-14900K, AMD Radeon RX 6800 XT and PCI RTL8111e Ethernet

<img width="2349" height="1395" alt="Geekvench - V6   Ai - Results" src="https://github.com/dpps5387690/EFI-GIGABYTE-Z790-AORUS-MASTER-X-INTEL-i9-14900KF-RX-6800-XT/blob/main/Geekbench6.png" />

# Basic Information

**Latest working macOS**: macOS Tahoe (26.2)
<br>
**Current OpenCore**: 1.0.7
<br>
**Release date**: 17/06/2026

# Hackintosh Specifications
|Item|Description|
|-|:-------:|
|Motherboard|Gigabyte Z790 Aorus Master X|
|BIOS|F12a|
|Processor|Intel Core i9-14900K|
|Memory|2x 32Gb DDR5|
|dGPU|AMD Radeon RX 6800 XT|
|Audio Codec|Realtek ALC1220|
|Ethernet|Marvell AQtion AQC113C 10GbE|
|Ethernet|PCI RTL8111e Ethernet|
|Storage|GIGABYTE AG450E1TB|

# BIOS Setting
|Section|Choose|
|-|:-------:|
|Secure Boot|Disabled|
|Internal Graphics|Disabled|
|Re-Size BAR Support|Disabled|
|Intel Platform Trust Technology(PTT)|Disabled|
|CFG Lock|Disabled|


# Tool
|Name|Description|
|-|:-------:|
|SSDTTime|Get Patch ACPI|
|USBToolBox|Get USB Mapping|
|Mist|Download macOS to Applications File|
|GenSMBIOS|Generate SMBIOS Information|
|OpenCore|BootInstall_X64.tool|

## Step 1 - Download macOS Installer

Use **Mist** to download the macOS installer.

Download:

```text
https://github.com/ninxsoft/Mist
```

1. Open **Mist**
2. Select the macOS version you want
3. Choose **Application**
4. Click **Download**

After downloading, verify that the installer exists:

```bash
/Applications/Install macOS Tahoe.app 
```

---

## Step 2 - Create a Bootable USB Installer

### Format the USB Drive

Open **Disk Utility** and erase the USB drive with:

| Setting | Value |
|----------|----------|
| Format | Mac OS Extended (Journaled) |
| Scheme | GUID Partition Map |
| Name | InstallUSB |

### Create Installer

Open Terminal:

```bash
sudo /Applications/Install\ macOS\ Tahoe.app/Contents/Resources/createinstallmedia --volume /Volumes/InstallUSB
```

Replace:

```text
InstallUSB
```

with your USB volume name.

Wait until:

```text
Install media now available at "/Volumes/Install macOS Tahoe"
```

appears.

---

## Step 3 - Install OpenCore to USB

Open the OpenCore package and run:

```bash
Utilities/BootInstall/BootInstall_X64.tool
```

Select your USB drive when prompted.

This installs OpenCore onto the USB EFI partition.

---

## Step 4 - Copy EFI Folder

Mount the USB EFI partition.

Copy your prepared EFI folder:

```text
EFI
├── BOOT
└── OC
```

to:

```text
USB EFI Partition
```

and replace any existing files.

---

## Step 5 - Configure OpenCore

Open:

```text
EFI/OC/config.plist
```

using:

- OpenCore Auxiliary Tools (OCAT)
- OpenCore Configurator

Configure the following sections according to your motherboard and CPU:

### ACPI
- SSDT files
- ACPI patches

### Kernel
- Lilu.kext
- VirtualSMC.kext
- WhateverGreen.kext
- AppleALC.kext
- USB Mapping

### SMBIOS
Choose the correct SMBIOS for your platform.

Examples:

| CPU Generation | SMBIOS |
|---------------|---------|
| Coffee Lake | iMac19,1 |
| Comet Lake | iMac20,2 |
| Alder Lake | iMacPro1,1 |
| Raptor Lake | iMacPro1,1 |

### Generate SMBIOS Information

Download:

```text
https://github.com/corpnewt/GenSMBIOS
```

Run:

```bash
GenSMBIOS.command
```

Select:

```text
3. Generate SMBIOS
```

Choose the SMBIOS model you configured in OpenCore.

Example:

```text
iMac20,2
```

GenSMBIOS will generate:

```text
SystemSerialNumber
MLB (Board Serial Number)
SystemUUID
```

Example Output:

```text
Type:         iMac20,2

Serial:       C02XXXXXXX
Board Serial: C027XXXXXXXYYYYA
SmUUID:       XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
```

Copy the values into:

```text
PlatformInfo
 └─ Generic
     ├─ SystemSerialNumber
     ├─ MLB
     └─ SystemUUID
```

in your `config.plist`.

---

## Step 6 - Configure BIOS

### Disable

```text
CSM Support
Secure Boot
Fast Boot
Intel Platform Trust Technology (PTT)
```

### Enable

```text
XHCI Hand-off
Above 4G Decoding
Hyper-Threading
VT-d
```

### Optional

```text
Resize BAR
CFG Lock
```

Configure according to your EFI requirements.

---

## Step 7 - Install macOS

Boot from the USB drive.

Select:

```text
Install macOS Tahoe
```

from OpenCore Picker.

---

## Step 8 - Prepare Target SSD

Open:

```text
Disk Utility
```

Click:

```text
View → Show All Devices
```

Erase the target SSD:

| Setting | Value |
|----------|----------|
| Format | APFS |
| Scheme | GUID Partition Map |
| Name | Macintosh HD |

Example:

```text
Container
└── Macintosh HD
```

---

## Step 9 - Install macOS

Select:

```text
Macintosh HD
```

as the destination drive.

The installer will reboot several times.

Always select:

```text
macOS Installer
```

in OpenCore Picker until installation completes.

---

## Step 10 - First Boot

After installation completes:

Select:

```text
macOS
```

from OpenCore Picker.

Complete the initial setup wizard.

---

## Step 11 - Install OpenCore to SSD

Mount the EFI partition of the installed SSD.

Copy:

```text
EFI
├── BOOT
└── OC
```

from the USB drive to the SSD EFI partition.

---

## Step 12 - Verify Boot

Remove the USB drive.

Reboot the system.

Verify that macOS boots directly from the SSD.

---

# Troubleshooting

## Installer Stops During Installation

Check:

- SMBIOS selection
- USB Mapping
- ACPI configuration
- SATA mode = AHCI
- Secure Boot disabled

---

## Missing Ethernet

Check:

```bash
ifconfig
```

and:

```bash
system_profiler SPEthernetDataType
```

Install the correct network driver if the NIC is unsupported.

Example:

```text
Marvell AQC113C
```

requires additional Aquantia patches.

---

## Missing Preboot / Recovery

Run:

```bash
diskutil apfs list
```

If Preboot and Recovery are missing:

```bash
diskutil apfs updatePreboot /
```

or reinstall macOS.

---

## USB Issues

Use:

```text
USBToolBox
```

to generate:

```text
UTBMap.kext
```

Enable:

```text
USBToolBox.kext
UTBMap.kext
```

Disable:

```text
USBPorts.kext
```

if using USBToolBox mapping.

---

## Useful Tools

- Mist
- OpenCore
- OpenCore Auxiliary Tools (OCAT)
- ProperTree
- GenSMBIOS
- USBToolBox
- Hackintool

---
