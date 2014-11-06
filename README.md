utilite
=======

Patches and instructions for booting Fedora 21 on the Utilite

*Not yet finished!*
*Not yet Tested!*

This is currently not for the faint of heart. I believe that a serial connection is required for the initial setup. I am hopeful that the Fedora 21 stable Arm image will be much easier to work with. There is currently a bit of a convaluted bootstrapping process to get to a working system. These instructions are tested for the pro version only. I do have the other two Utilite models, but so far have only worked with the Pro.

First step is to grab the Fedora image and copy to your SD card. Instructions here: 
http://fedoraproject.org/wiki/Architectures/ARM/F21_Alpha/Installation


Next we need to build our new u-boot image. Grab the 2014.10 u-boot sources and apply the utilite-uboot.patch. If you are not building this on an arm machine, you will need a cross compiling toolchain. I used gcc-arm-linux-gnu available in Fedora. You can basically follow the instructions here: http://compulab.co.il/utilite-computer/wiki/index.php/Utilite_U-Boot_firmware.
Once built, dd the firmware to the Fedora sd card. Command will need to look like this: 
dd if=cm-fx6-firmware of=/dev/sdX bs=1K skip=1 seek=1 oflag=dsync
Replace /dev/sdX with the path to the sd card. 

At this point, pop the sdcard into the utilite and power it up. You should see the boot attempt and then have a prompt. On this first boot, These commands should get you started.

env default -a
boot
setenv catcat setenv catout\;'setenv catX setenv catout '\\\\\\\"''\$\$catin'\\\\\\\"''' \; run catX
run k1
run c9

setenv u_dtb imx6q-cm-fx6.dtb
setenv u_root /dev/mmcblk0p3
setenv u_devpart 2:1
boot

This should boot into Fedora running on the SD card.


From here, copy image to sata, change to smaller sd card or wipe boot part. Boot manually into sata Change a-b-c, build kernel, install kernel. Possibly done.
