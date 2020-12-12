# Asus-G771JM-Hackintosh
**Hackintosh Installation Guide  for Asus ROG G771JM**
<p align="center" style="margin:0 auto !important;text-align:center !important;"><img src="https://github.com/ouija/Asus-S510UA-DS71-Hackintosh/raw/master/Images/Asus-S510UA-DS71-Hackintosh.png"></p>

## Preface
It's been a very long time since I've done a *fresh install* on this unit, as I hackintoshed it when first getting this unit 6 or 7 years ago and have only done upgrades ever since.   This is more for tracking my own notes than a guide, so let me know if the installation instructions should be updated in any way.

## Hardware Specs
- Intel Core i7-4710HQ [Haswell] Processor 2.5 GHz (Turbo up to 3.5 GHz)
- NVIDIA GeForce GTX 860M (4GB GDDR5, GM107) Optimus w/Intel HD5600
- Shitty OEM Mediatek MT7630E (Ralink) Wireless adapter, replaced with [BCM94352HMB](https://www.newegg.ca/broadcom-bcm94352hmb-m-2-mini-pci-e/p/0XM-00BD-00005)
- Realktek HD Audio ALC668
- Realtek RTS5208 PCI Card Reader
- Asus SmartTouch (Synaptics) PS2 Touchpad

## Quick Installation Notes

1. Create macOS 10.15.5 Catalina USB Installer via [Vanilla Method](https://hackintosher.com/guides/how-to-make-a-macos-10-15-catalina-flash-drive-installer/) or [UniBeast](https://www.tonymacx86.com/resources/unibeast-10-1-0-catalina.469/) (EFI Method)
 2. Mount EFI partition of USB Installer *(using [Hackintool](https://github.com/headkaze/Hackintool/releases) or [Clover Configurator](https://mackie100projects.altervista.org/download-clover-configurator/) if necessary)*
 3. Replace the `EFI/CLOVER/config.plist` file with [the version from this repo](Installation/EFI/CLOVER/config.plist) *(see detailed installation notes  below regarding necessary modifications to this file to enable installation)*
 4. Copy/replace [these kexts](Installation/EFI/CLOVER/kexts/Other) in the `/EFI/CLOVER/kexts/Other` folder
 5. Ensure BIOS has Display Memory set to 64MB and that both Secure Boot and CSM mode is disabled.
 6. Reboot and boot from USB and complete macOS installation! 
