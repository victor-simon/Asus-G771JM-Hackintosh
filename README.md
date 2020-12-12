# Asus-G771JM-Hackintosh
**Hackintosh Installation Guide  for Asus ROG G771JM**
<p align="center" style="margin:0 auto !important;text-align:center !important;"><img src="https://github.com/ouija/Asus-G771JM-Hackintosh/raw/master/Images/Asus-G771JM-Hackintosh.png"></p>

## Preface
It's been a very long time since I've done a *fresh install* on this unit, as I hackintoshed it with when first getting this unit 6 or 7 years ago with **Mavericks**, and have only done upgrades since then, so this is more for my own notes than an actual installation guide.

## Hardware Specs
- Intel Core i7-4710HQ [Haswell] Processor 2.5 GHz (Turbo up to 3.5 GHz)
- NVIDIA GeForce GTX 860M (4GB GDDR5, GM107) Optimus w/Intel HD4600
- Mediatek MT7630E (Ralink) WiFi -> Replaced with [BCM94352HMB](https://www.newegg.ca/broadcom-bcm94352hmb-m-2-mini-pci-e/p/0XM-00BD-00005)
- Realktek HD Audio ALC668
- Realtek RTS5208 PCI Card Reader
- Asus SmartTouch (Synaptics) PS2 Touchpad

## Quick Installation Notes

 - Use one of the [guides](https://www.tonymacx86.com/threads/how-to-create-a-macos-big-sur-public-beta-installation-usb.300100/) available on the internet to create USB installer for Big Sur and complete installation. 
 - Note that I preferred to use **Clover** *(v5123 or greater)*, because I am using the internal NVMe drive/controller for my OSX installation with the latest OEM BIOS which does NOT support booting from the NVMe controller and requires use of the **NvmExpressDxe.efi** driver with Clover installed to an EFI on one of the internal SATA drives.
 - OpenCore wasn't detecting the macOS system partition for whatever reason using this method but Clover was so I just chose to stick with it.
 - You can [build a custom BIOS](https://rog.asus.com/forum/showthread.php?110850-G771JM-Custom-BIOS-with-NVMe-Support) to enable support for booting directly from the NVMe controller/drive instead, but I was getting random BSOD in Windows and reverted back to this more stable method.
 

