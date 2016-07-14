# a33_boot_resources

# script.fex

    1. Compile script.fex
```sh
apt-get install build-essential git libusb-1.0-0-dev
git clone https://github.com/linux-sunxi/sunxi-tools
cd sunxi-tools
make
make install
fex2bin script.fex > script.bin
```
    2.Copy compiled fex to SD card
```sh
cp script.bin > /path_to_mounted_mmc/boot/.
umount /path_to_mounted_mmc/; sync
```
