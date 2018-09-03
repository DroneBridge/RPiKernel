# RPiKernel
Sources of the current kernel used for the DroneBridge Raspberry Pi images &amp; its patch files.
Since the current version of the https://github.com/aircrack-ng/rtl8812au driver is not compiling successfuly it is deactivated.
You might want to update the files in the `/drivers/net/wireless/rtl8812au` folder

## Building (cross compile)
Compile for Armv6 (Pi0) & Armv7 (Pi2+) using the instructions given here:
* https://www.raspberrypi.org/documentation/linux/kernel/building.md
* https://www.raspberrypi.org/documentation/linux/kernel/configuring.md

1. Rename the provided config files for both architectures to `.conf` depending what kernel you are building.
2. `KERNEL=kernel` or `KERNEL=kernel7`
3. `make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig` to configure the kernel for your needs
4. Use the correct location of your mounted SD card/DroneBridge patitions (see raspberrypi.org docs). We use `/dev/sddx` here:

```bash
mkdir mnt
mkdir mnt/fat32
mkdir mnt/ext4
sudo mount /dev/sdd1 mnt/fat32
sudo mount /dev/sdd2 mnt/ext4
sudo make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=mnt/ext4 modules_install
sudo cp arch/arm/boot/zImage mnt/fat32/$KERNEL.img
sudo cp arch/arm/boot/dts/*.dtb mnt/fat32/
sudo cp arch/arm/boot/dts/overlays/*.dtb* mnt/fat32/overlays/
sudo cp arch/arm/boot/dts/overlays/README mnt/fat32/overlays/
sudo umount mnt/fat32
sudo umount mnt/ext4
```
