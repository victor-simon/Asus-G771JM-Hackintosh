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
 - You need to enable some DSDT patches in Clover to support Catalina or greater, specifically the `EC0 to EC` patch *(see more detailed installation notes below)* but essentially can use the same `config.plist` for Clover on your installation USB if installed for the same `Asus G771JM` model.
 - **Improtant:** Ensure you select the `Preboot` partition after the second reboot during installation to avoid issues with AppStore downloads failing post-upgrade. (read more [here](https://github.com/CloverHackyColor/CloverBootloader/issues/300#issuecomment-731096921) and [here](https://www.reddit.com/r/hackintosh/comments/jtv7cn/clover_big_sur_app_store_download_stuck_on/))

## Detailed Installation and Configuration Notes
**BIOS RELATED:**

Ensure BIOS has Display Memory set to 64MB and that both Secure Boot and CSM mode is disabled.   If VT-d left enabled, ensure to enable `DsiableIoMapper` quirk in Clover.

**CLOVER RELATED:**

 - **ACPI/DSDT/Patches**  include: `change _OSI to XOSI`, `change _DSM to XDSM`, `change EC0 to EC`, `change GFX0 to IGPU`, `change SAT0 to SATA`, `change EHC1 to EH01`, `change EHC2 to EH02`, and `change B0D3 to HDAU`
 
  - **ACPI/SSDT/Generate/PluginType** is enabled (true) to take advantage of CPU scaling SSDT generated by **[ssdtPRGen](https://github.com/Piker-Alpha/ssdtPRGen.sh)**.

 - SMBIOS Definition set for **MacBookPro11,2** which closely matches the hardware of the Asus G771JM

 - Enable the following Quicks in Clover v5123 or greater to enable proper OpenRuntime.efi support:
	 - AvoidRuntimeDefrag
	 - EnableSafeModeSlide
	 - EnableWriteUnprotector
	 - ProvideCustomSlide
	 - SetupVitrualMap
	 - DsiableIoMapper *(if VT-d enabled in BIOS)* 
	 - XhciPortLimit

- `Devices` has  `IntelGFX` set to `0x04128086`, `Audio` -> `Inject`set to 3, `SetIntelBacklight` and `SetIntelMaxBacklight` enabled.
	- `PciRoot(0x0)/Pci(0x1B,0x0)` is defined with the following properties: 
		- key: `No-hda-gfx` value: `00000000 00000000 00` type: `DATA`
		-  key: `alc-layout-id` value: `3` type: `NUMBER`
	- `PciRoot(0x0)/Pci(0x2,0x0)` is defined with the following properties:
		- key: `AAPL,ig-platform-id` value: `0600260A` type: `DATA`
		- key: `device-id` value: `12040000` type: `DATA`
		- key: `framebuffer-con1-enable` value: `01000000` type: `DATA`
		- key: `framebuffer-con1-pipe` valuue: `12000000` type: `DATA`
		- key: `framebuffer-con2-enable` value: `01000000` type: `DATA`
		- key: `framebuffer-con2-pipe` value: `12000000` type: `DATA`
		- key: `framebuffer-cursormem` value: `00009000` type: `DATA`
		- key: `framebuffer-patch-enable` value: `01000000` type: `DATA`

- Note that it's better NOT to use the `CsmVideoDxe.efi` to enable higher resolutions if/when using external monitors and tp  keep Clover boot resolution at 1024x768.

**KEXT RELATED:**
- Using **[Lilu.kext](https://github.com/acidanthera/lilu/releases)** and **[WhateverGreen.kext](https://github.com/acidanthera/whatevergreen/releases)** to enable Intel HD4600 _(with FakeID injection and device properties defined in Clover as outlined above)_
- Using  **[VirtualSMC](https://github.com/acidanthera/virtualsmc/releases)**  _(instead of  [FakeSMC](https://bitbucket.org/RehabMan/os-x-fakesmc-kozlek/downloads/)  and  [ACPIBatteryManager.kext](https://bitbucket.org/RehabMan/os-x-acpi-battery-driver/downloads/))_ to enable battery status
	- Include all VirtualSMC kexts except for `SMCDellSensors.kext`
	- Patch DSDT with Rehabaman's `Asus G75VW`  battery patch
- Using **[AsusSMC](https://github.com/hieplpvip/AsusSMC)** with `[als] Fake ALS` and `[kbd] Haswell` and `F3 to F6` patches without AsusSMCDaemon to enable Asus Function Keys and Keyboard Backlight _(instead of  [AsusNBFnKeys.kext](https://osxlatitude.com/forums/topic/1968-fn-hotkey-and-als-sensor-driver-for-asus-notebooks/))_
- Using acidanthera's [VoodooPS2.kext](https://github.com/acidanthera/VoodooPS2) instead of emlydinesh's [ApplePS2SmartTouchPad.kext](https://osxlatitude.com/forums/topic/1948-elan-focaltech-and-synaptics-smart-touchpad-driver-mac-os-x) which supports AsusSMC and F9 key to disable trackpad
- Using acidanthera's [AirportBrcmFixup.kext](https://github.com/acidanthera/AirportBrcmFixup) to enable wireless but need to set `brcmfx-driver=2` boot argument to enable, as well as removing `AirPortBrcm4360_Injector.kext` from plugins folder within kext to support Big Sur.
- Using acidanthera's [BrcmPatchRAM](https://github.com/acidanthera/BrcmPatchRAM) to enable bluetooth
	-  `BrcmPatchRAM3.kext`, `BrcmFirmwareData.kext`, and `BrcmBluetoothInjector.kext` 
- Using custom **[USBMap.kext](https://github.com/corpnewt/USBMap)** to properly enable USB ports/hubs,  instead of FakePCIID.kexts *(which will cause slow startup and wifi issues in Big Sur)*
	- Note this enables camera, and is also needed to enable wireless and sdcard support
- Using cholonam's [Sinetek-rtsx.kext](https://github.com/cholonam/Sinetek-rtsx/releases) to enable SD card reader *([original version](https://github.com/sinetek/Sinetek-rtsx) is causing kernel panic in Big Sur when mounting SD card)*
- Using Mieze's  [RealtekRTL8111.kext](https://github.com/Mieze/RTL8111_driver_for_OS_X/releases) to enable LAN
- Using modified version of Rehabman's [CodecCommander.kext](https://bitbucket.org/RehabMan/os-x-eapd-codec-commander/downloads/) for `ALC668` to resolve [audio issue](https://www.tonymacx86.com/threads/alc1150-dual-boot-with-windows-and-10-10-3-no-sound-solved.162380/) when dual-booting with Windows.
- Using acidanthera's [NVMeFix](https://github.com/acidanthera/NVMeFix) to improve support for NVMe drive and enable TRIM *(optional)*

**DSDT RELATED:**

 - [This guide](https://www.tonymacx86.com/threads/guide-patching-laptop-dsdt-ssdts.152573/)  by RehabMan is still *one of the best* by far when it comes to disassembling and modifying the DSDT.

 - All `Common Patches` from the RehabMan guide have been applied to the DSDT, including the `Fix PNOT/PPNT` patch, as I am <ins>not</ins> including OEM SSDT *(only ssdtPRGen SSDT)* and also apply  `Add IMEI` patch as DSDT does **not** contain HECI/IMEI device.
 - Applied `USB3_PRW 0x0D (instant wake)` patch to fix USB causing wake after sleep.
 - Applied `[bat] Asus G75VW` patch for battery status support.
 - Applied [patches to disable Nvidia Optimus](https://www.insanelymac.com/forum/topic/295584-disabling-nvidia-optimus-card-on-all-laptops/) and improve battery life.
