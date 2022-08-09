# buildBBBWithYocto

Setup --
Host OS : Windows 10
Guest OS: NAME="Ubuntu"
          VERSION="22.04.1 LTS (Jammy Jellyfish)"
          The guest OS has 50GB disk space.
Target  : BeagleBone Black, Rev C
SD card : 32GB
TeraTerm: Running on Windows 10 (Using a Fidi cable to connect to one of PC usb ports)


Steps:

1. Install packages recommandes from Yocto Ref manual 
sudo apt install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm python-crypto cpio python python3 python3-pip python3-pexpect xz-utils debianutils iputils-ping libssl-dev

2. make a directory for Yocto:
mkdir yocto
cd yocto

3. download yocto:
git clone git://git.yoctoproject.org/poky.git -b dunfell
cd poky

4. verifty dunfell is the branch:
git branch

5. download more compoents:
git clone git://git.yoctoproject.org/meta-security.git -b dunfell
git clone git://git.openembedded.org/meta-openembedded -b dunfell
git clone git://git.yoctoproject.org/meta-ti -b dunfell
git clone git://git.yoctoproject.org/meta-arm -b dunfell 

6. Create a local.conf:
source oe-init-build-env

This will make directory change to poky/build

7.Modify local.conf to enable beaglebone-yocto
vi conf/local.conf
Change line 37 into # MACHINE ??= "qemux86-64"
Change line 31 into MACHINE ?= "beaglebone-yocto"

add openssh:
# ssh server for remote login
EXTRA_IMAGE_FEATURES += "ssh-server-openssh"

Without this feature, you can not access BBB with ssh.

Save the file

8. Modify bblayers.conf
vi conf/bblayers.conf
BBLAYERS ?= " \
    /home/user/project/yocto/poky/meta \
    /home/user/project/yocto/poky/meta-poky \
    /home/user/project/yocto/poky/meta-yocto-bsp \
    /home/user/project/yocto/poky/meta-openembedded/meta-oe \
    /home/user/project/yocto/poky/meta-openembedded/meta-python \
    /home/user/project/yocto/poky/meta-openembedded/meta-networking \
    /home/user/project/yocto/poky/meta-ti \
    /home/user/project/yocto/poky/meta-arm/meta-arm \
    /home/user/project/yocto/poky/meta-arm/meta-arm-toolchain \
    "

9. Build:
bitbake core-image-minimal

10. After several hours, check the build images:
ls build/tmp/deploy/images/beaglebone-yocto/*.*

11. Precare SD card
I use application called: Gparted.
BOOT: FAT16 1GB, boot flag set
ROOT: ext4 31GB

12. Imported!!!
Make sure the SD card is correct one, or you could release your main disk of your host OS.
ls /dev/sd*
Mine is sdb

13. Copy image file (.wic) to SD card:
cd tmp/deploy/images/beaglebone-yocto
sudo dd if=core-image-minimal-beaglebone-yocto.wic of=/dev/sdb

Here if- input file
     of - outpup file
     outdisk is /dev/sdb (!!!)

14. It took a long time before the SD is ready

15. Unplug SD and plug it back in, you will see the boot files and rootfs are in the places.

16. Insterd the SD card into the BBB and reset set it while holding reset button to let the BBB
    boot from the SD card.




