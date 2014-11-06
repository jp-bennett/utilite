utilite
=======

Patches and instructions for booting Fedora 21 on the Utilite

*Not yet finished!*

*Not yet Tested!*

This is currently not for the faint of heart. I believe that a serial connection is required for the initial setup. I am hopeful that the Fedora 21 stable Arm image will be much easier to work with. There is currently a bit of a convaluted bootstrapping process to get to a working system. These instructions are tested for the pro version only. I do have the other two Utilite models, but so far have only worked with the Pro.

First step is to grab the Fedora image and copy to your SD card. Instructions here: 
http://fedoraproject.org/wiki/Architectures/ARM/F21_Alpha/Installation

I used this image:

http://download.fedoraproject.org/pub/fedora/linux/releases/test/21-Beta/Images/armhfp/Fedora-KDE-armhfp-21_Beta-4-sda.raw.xz

My dd command looks like this, for reference: xzcat Fedora-KDE-armhfp-21_Beta-4-sda.raw.xz | sudo dd of=/dev/sdg

After DD'ing the image, Remove and re-insert the sdcard, and use something like gparted to grow the root partition to fill the rest of the sd card. If you intend to install Fedora to the sata drive, then mount the enlarged root partion and copy the Fedora image file to it for later use.


Next we need to build our new u-boot image. Grab the 2014.10 u-boot sources (ftp://ftp.denx.de/pub/u-boot/u-boot-2014.10.tar.bz2) and apply the utilite-uboot.patch. If you are not building this on an arm machine, you will need a cross compiling toolchain. I used gcc-arm-linux-gnu available in Fedora. We basically follow the instructions here: http://compulab.co.il/utilite-computer/wiki/index.php/Utilite_U-Boot_firmware.

export ARCH=arm

export CROSS_COMPILE=arm-linux-gnu-

make mrproper

make cm_fx6_config && make

dd if=/dev/zero count=500 bs=1K | tr '\000' '\377' > cm-fx6-firmware

dd if=SPL of=cm-fx6-firmware bs=1K seek=1 conv=notrunc && dd if=u-boot.img of=cm-fx6-firmware bs=1K seek=64 conv=notrunc

Once built, dd the firmware to the Fedora sd card. Command will need to look like this: 

sudo dd if=cm-fx6-firmware of=/dev/sdg bs=1K skip=1 seek=1 oflag=dsync

Replace /dev/sdg with the path to the sd card. 

At this point, pop the sdcard into the utilite and power it up. You should see the boot attempt and then have a prompt. On this first boot, These commands should get you started.

env default -a

boot

setenv catcat setenv catout\;'setenv catX setenv catout '\\\\\\\"''\$\$catin'\\\\\\\"''' \; run catX

run k1

run c9

setenv u_dtb imx6q-cm-fx6.dtb

setenv u_devpart 2:1

boot

This should boot into Fedora running on the SD card. 

At this point we branch

If you are installing to an internal sata drive, go ahead and dd the image to the sata drive, just like above.

Also, if installing to the sata drive, u-boot currently dies without an sd-card in the drive. I will eventually fix this, but for now, I have a blank 256 meg sd card with the u-boot firmware written to it. If you are installing to the sata card, you'll want to do the same. You need boot onto the sata card to continue. Power down and swap to the u-boot only sd card. The boot commands for sata are: 

setenv catcat setenv catout\;'setenv catX setenv catout '\\\\\\\"''\$\$catin'\\\\\\\"''' \; run catX

run k1

run c9

setenv u_dtb imx6q-cm-fx6.dtb

setenv u_root /dev/sda3

setenv u_iodevs sata

boot


Whether booting from the sata or sd card, copy a-b-c patch to /etc/ and run "patch -p1 < a-b-c.patch". Run a-b-c. Now your utilite should autoboot without having to muck around in u-boot.


build kernel, install kernel. Possibly done.
