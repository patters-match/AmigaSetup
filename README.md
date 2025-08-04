# AmigaOS 3.2.3 Install from CF card on hardware

This guide assumes you have purchased a CF/SD card PCMCIA kit which includes a bootable Workbench floppy with PCMCIA support.

#### With your system’s original Kickstart ROM
- Put [ADFBlitzer](https://aminet.net/package/disk/misc/adfblitzer) onto your PCMCIA CF card along with Install3.2.adf from the Amiga OS 3.2 CDROM.
- Boot the PCMCIA floppy
- Open the CF card and right-click -> Window -> Show -> All Files
- Use ADFBlitzer to write Install3.2.adf to a floppy. Make two copies in case you get something wrong during this process, which will avoid you having to swap Kickstart ROMs once again.

#### Power off and physically install the new Kickstart 3.2.3 ROM
- Boot the Install3.2 floppy
- Open this disk and right-click -> Window -> Show -> All Files
- Now delete ActivateCDROM, L/CDFileSystem, and Storage/DOSDrivers/CD0
- Insert and open the working PCMCIA floppy and right-click -> Window -> Show -> All Files. Use the RAM disk as a intermediary to:
    - Copy Devs/DOSDrivers/CF0 to Storage/DOSDrivers on the Install3.2 floppy
    - Copy Devs/compactflash.device to Devs on the Install3.2 floppy
    - Copy L/fat95 to L on the Install3.2 floppy
- Reboot from the Install3.2 floppy
- Double-click on Storage/DOSDrivers/CF0 to mount the CF card (it will not auto mount because S:Startup-sequence does not instruct this).
- Now you have a bridge to get files to your Amiga using KickStart 3.2.3.
- Extract the entire AmigaOS3.2 ISO to your CF card.
- Register an account at [Hyperion Entertainment](https://www.hyperion-entertainment.com/) using your CD key, and download the 3.2.3 update. The prior updates are not needed.

#### Hard disk (internal CF card) setup:
- N.B. I tested using PFS3 for both boot and data partitions and found PFS3 to be slower at opening drawers full of icons (Prefs) than FFS with directory cache. On a 68000 we need all the marginal gains.
- Open HDTools/HDToolBox. The internal CF card will be detected, but will present as ‘Unknown’
- Change drive type -> Define new -> Read configuration -> Continue
- Save changes to the drive and reboot.
- Use HDToolBox to create a 512MB bootable Standard File System partition with device name DH0 with Fast File System, Directory Cache, MaxTransfer of 0x1FE00 and Mask 0xFFFFFFFC, Blocksize of 512. Be very sure to always hit Enter after each change to ensure it is recorded by the UI.
- Quick format the new volume in Workbench
- Open Shell
- The installer expects a specific volume name which is not a valid FAT volume name, so we must make an assign (replace the second argument with the path to your installation files): `assign AmigaOS3.2CD: CF_CARD:AmigaOS3.2CD`
- Open ‘Install/Start Here’
- Open your choice of installer from the Install drawer on the Install3.2 floppy. Select both English British and English languages, so there is a fallback if the British localisation is incomplete. Decline GlowIcons as they are far too slow on 68000.
- Once AmigaOS 3.2 is installed, boot from DH0:
- Add PCMCIA CompactFlash support to the installed Workbench, using the files from the install3.2 floppy:
    - Copy DF0:Storage/DOSDrivers/CF0 to DHO:Devs/DOSDrivers/
    - Copy DF0:Devs/compactflash.device to DHO:Devs/
    - Copy DF0:L/fat95 to DH0:L/
- If necessary, run a608mcfg to switch from 8MB Fast RAM to 4MB, to enable PCMCIA
- Reboot
- From the CF card, run the AmigaOS 3.2.3 Update by double-clicking to mount the file ADFs/Update3.2.3.adf
- Once that floppy image is mounted, launch the installer from the Install folder, ensuring to select the Expert User option. It seems to fail during a scripted file copy when Novice User is selected. Once again select both English British, and English as installed languages.
- Use HDToolbox to add a new partition type. Browse to the unpacked pfs3aio filesystem driver file, which will copy it to the drive RDB. Set the DOSType to 0x50465303. Version is 19.2 which is derived from the driver file.
- Create a new partition of type ‘PFS\03’ to span the rest of the drive with device name DH1 with MaxTransfer of 0x1FE00, and Mask 0xFFFFFFFC, Blocksize of 512.
- Quick format this volume
- Copy [NoClick](https://aminet.net/package/util/cdity/noclick20_usr) to WBStartup
- Install WHDLoad to C:
- Copy [a608mcfg](http://wiki.archi-tech.com.pl/pl/A608mini) to C:
- Copy Kickstarts to DH0:Devs/
- QuitKey=$59 in S:WHDLoad.prefs (F10 to exit all games)
- In Prefs, set Locale, set Overscan, in Input set keyboard layout, in IControl set windows ‘can move off-screen’
