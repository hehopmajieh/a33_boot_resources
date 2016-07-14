# Build Kernel,U-Boot  and RootFS

    1. Get Kernel and setup toolchain
```sh
hehopmajieh@bundie:~$ cd ~
hehopmajieh@bundie:~$ apt-get install build-essential git qemu-user-static debootstrap binfmt-support
hehopmajieh@bundie:~$ wget https://launchpad.net/linaro-toolchain-binaries/trunk/2012.02/+download/gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux.tar.bz2
hehopmajieh@bundie:~$ sudo tar jxfv gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux.tar.bz2 -C /opt
hehopmajieh@bundie:~$ sudo export PATH=$PATH:/opt/gcc-linaro-arm-linux-gnueabi-2012.02-20120222_linux/bin/

###Clone Linux kernel tree
git clone https://github.com/hehopmajieh/a33_linux
cd a33_linux
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- a33Olinuxino_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- uImage -j4
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- modules -j4
 make ARCH=arm CROSS_COMPILE=arm-linux-gnueabi- -j4 INSTALL_MOD_PATH=<path_to_install_modules> modules_install 
#Note: 	You must specify path, where modules should be installed
```
    2.Build RootFS
```sh
hehopmajieh@bundie:~$ targetdir=rootfs
hehopmajieh@bundie:~$ distro=wheezy
hehopmajieh@bundie:~$ sudo debootstrap --arch=armhf --foreign $distro $targetdir

#Cofee/Beer Time

hehopmajieh@bundie:~$ sudo cp /usr/bin/qemu-arm-static $targetdir/usr/bin/
hehopmajieh@bundie:~$ sudo cp /etc/resolv.conf $targetdir/etc
hehopmajieh@bundie:~$ sudo chroot $targetdir

#We need to set some ENV before second stage

I have no name!@bundie:/# distro=jessie
I have no name!@bundie:/# export LANG=C
I have no name!@bundie:/# /debootstrap/debootstrap --second-stage

#Replace mirror address with local one

I have no name!@bundie:/# cat <<EOT > /etc/apt/sources.list
deb http://ftp.bg.debian.org/debian $distro main contrib non-free
deb-src http://ftp.bg.debian.org/debian $distro main contrib non-free
deb http://ftp.bg.debian.org/debian $distro-updates main contrib non-free
deb-src http://ftp.bg.debian.org/debian $distro-updates main contrib non-free
deb http://security.debian.org/debian-security $distro/updates main contrib non-free
deb-src http://security.debian.org/debian-security $distro/updates main contrib non-free
EOT

I have no name!@bundie:/# apt-get update
I have no name!@bundie:/# apt-get install locales dialog
I have no name!@bundie:/# dpkg-reconfigure locales

#Set Hostname and root password
I have no name!@bundie:/# passwd
I have no name!@bundie:/# echo "A33-OLinuXino" > /etc/hostname
 
# If you want to install additional packages

I have no name!@bundie:/# apt-get install wireless-tools alsa-tools

#Exit from chrooted ENV

I have no name!@bundie:/# exit
```
    3.Setup SD Card
        - Install Boot Loader
        - Partition SD
        -Copy files to SD
```sh
hehopmajieh@bundie:~$ cd ~

#mount partioned SD card 

hehopmajieh@bundie:~$ mount /dev/sdX /mount_point/

#Copy kernel and modules to SD card

hehopmajieh@bundie:~$ cp a33_linux/arch/arm/boot/uImage rootfs/boot/.
hehopmajieh@bundie:~$ cp -aRv a33_linux/out/lib/* rootfs/lib/.
hehopmajieh@bundie:~$ cp a33_linux/arch/arm/boot/uImage rootfs/boot/.

#Get https://github.com/hehopmajieh/a33_boot_resources

hehopmajieh@bundie:~$ git clone https://github.com/hehopmajieh/a33_boot_resources
hehopmajieh@bundie:~$ fex2bin a33_boot_resources/script.bin > rootfs/boot/script.bin
hehopmajieh@bundie:~$ cp a33_boot_resources/boot.scr /rootfs/boot/.
hehopmajieh@bundie:~$ cp -aRv rootfs/* /mount_point/.
hehopmajieh@bundie:~$ sync; umount /mount_point
```



