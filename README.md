# AmigaOS 3.2.3 bare install from PCMCIA SD/CF card
This guide sets up an Amiga A600 from scratch with a bootable internal CF card attached to its internal IDE interface. The steps are broadly identical for an A1200, but these have a PCMCIA hardware bug that can prevent cards being reinitialised on reboot without [this fix](https://aminet.net/package/util/boot/CardReset).

The described method only requires a working floppy drive, and assumes you have purchased a CF/SD card PCMCIA kit which includes a bootable Workbench floppy with PCMCIA support. Various eBay sellers offer these kits. No WinUAE emulator, and no CD-ROM drive required.

## Prepare install media
With your system's original Kickstart ROM that can boot the PCMCIA floppy...
- Put [ADFBlitzer](https://aminet.net/package/disk/misc/adfblitzer) onto your PCMCIA transfer SD/CF card, along with Install3.2.adf from the Amiga OS 3.2 CDROM.
- Boot the PCMCIA floppy
- Open the transfer SD/CF card and right-click -> Window -> Show -> All Files
- Use ADFBlitzer to write Install3.2.adf to a floppy. Make two copies in case you get something wrong during this process, which will avoid you having to swap Kickstart ROMs once again.
- On your modern computer, extract the entire AmigaOS3.2 ISO to your transfer SD/CF card
- Register an account at [Hyperion Entertainment](https://www.hyperion-entertainment.com/) using your CD key, and download the 3.2.3 update. The prior updates are not needed. Extract and copy to the transfer SD/CF card.

## Add PCMCIA storage support to Install3.2 boot floppy
- Power off and physically install the new Kickstart 3.2.3 ROM
- Boot the Install3.2 floppy
- Open this disk and right-click -> Window -> Show -> All Files
- Now delete `ActivateCDROM`, `L/CDFileSystem`, and `Storage/DOSDrivers/CD0`
- Insert and open the working PCMCIA floppy and right-click -> Window -> Show -> All Files. Use the RAM disk as a intermediary to:
    - Copy `Devs/DOSDrivers/CF0` to `Storage/DOSDrivers` on the Install3.2 floppy
    - Copy `Devs/compactflash.device` to `Devs` on the Install3.2 floppy
    - Copy `L/fat95` to `L` on the Install3.2 floppy  
- Reboot from the Install3.2 floppy, with the PCMCIA transfer SD/CF card inserted. If you have an A1200, do a cold boot here to guarantee that your PCMCIA card initialises.
- Double-click on `Storage/DOSDrivers/CF0` to mount the transfer PCMCIA SD/CF card (it will not auto mount because `S:Startup-sequence` does not instruct this)
- Now you have a bridge to get files to your Amiga using KickStart 3.2.3

## Hard disk (internal CF card) boot partition setup
N.B. I tested using [PFS3 filesystem](https://en.wikipedia.org/wiki/Professional_File_System) for both boot and data partitions and found PFS3 to be slower at opening drawers full of icons (Prefs) than FFS with directory cache. On a 68000 we need all the marginal gains.
- From the Install3.2 floppy, open `HDTools/HDToolBox`. The internal CF card will be detected, but will present as 'Unknown'
- Change drive type -> Define new -> Read configuration -> Continue
- Save changes to the drive and reboot
- Use HDToolBox to create a 512MB bootable Standard File System partition with device name DH0 with Fast File System, Directory Cache, MaxTransfer of `0x1FE00` and Mask `0xFFFFFFFC`, Blocksize of 512. Be very sure to always hit Enter after each change to ensure it is recorded by the UI.
- Quick format the new volume in Workbench

## Install AmigaOS 3.2
- Open Shell
- The installer expects a specific volume name which is not a valid FAT volume name, so we must make an assign (replace the second argument with the path to your installation files):   
  `assign AmigaOS3.2CD: CF_CARD:AmigaOS3.2CD`
- Open `AmigaOS3.2CD:Install/Start Here`
- Open your choice of installer from the Install drawer on the Install3.2 floppy. Select both English British and English languages, so there is a fallback if the British localisation is incomplete. Decline GlowIcons as they are far too slow on 68000.
- Once AmigaOS 3.2 is installed, boot from DH0:

## Add PCMCIA storage support
Although the OS native CrossDOSFileSystem supports FAT32 and LFNs it seems very RAM hungry, so we'll stick with fat95.
- From the Install3.2 floppy:
    - Copy `DF0:Storage/DOSDrivers/CF0` to `DHO:Devs/DOSDrivers/`
    - Copy `DF0:Devs/compactflash.device` to `DHO:Devs/`
    - Copy `DF0:L/fat95` to `DH0:L/`
- If necessary, run [a608mcfg](http://wiki.archi-tech.com.pl/pl/A608mini) to switch from 8MB Fast RAM to 4MB, to enable PCMCIA
- Reboot (cold boot if using an A1200)

## Update to AmigaOS 3.2.3
- From the PCMCIA transfer SD/CF card, run the AmigaOS 3.2.3 Update by double-clicking to mount the file `ADFs/Update3.2.3.adf`
- Once that floppy image is mounted, launch the installer from the Install folder, ensuring to select the Expert User option. It seems to fail during a scripted file copy when Novice User is selected. Once again select both English British, and English as installed languages.

## Hard disk (internal CF card) data partition setup
- Use `DH0:Tools/HDToolbox` to add a new partition type. Browse to the unpacked [pfs3aio filesystem driver](https://aminet.net/package/disk/misc/pfs3aio) file, which will copy it to the drive [RDB](https://en.wikipedia.org/wiki/Amiga_rigid_disk_block). Set the DOSType to `0x50465303`. Version is 19.2 which is derived from the driver file.
- Create a new partition of type `PFS\03` to span the rest of the drive with device name DH1 with MaxTransfer of `0x1FE00`, and Mask `0xFFFFFFFC`, Blocksize of 512. This partition can be larger than 4GB.
- Save the changes to the drive and exit to Workbench
- Reboot
- Quick format this volume from the Icon menu

## Additional customisation
- Copy [NoClick](https://aminet.net/package/util/cdity/noclick20_usr) to `DH0:WBStartup`
- Install [WHDLoad_usr_small](https://whdload.de) to `C:`
- Copy [a608mcfg](http://wiki.archi-tech.com.pl/pl/A608mini) to `C:`
- Copy Kickstarts to `DH0:Devs/`
- QuitKey=$59 in `S:WHDLoad.prefs` (F10 to exit all games)
- In Prefs, set Locale, set Overscan, in Input set keyboard layout, in IControl set windows 'can move off-screen'
