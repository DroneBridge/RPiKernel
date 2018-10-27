# RPiKernel
Sources of the current kernel used for the DroneBridge Raspberry Pi images &amp; its patch files.
Kernel source files contain the https://github.com/aircrack-ng/rtl8812au driver. It is experimental and not patched to output max power. Current branch is 5.20.x.
You might want to update the files in the `/drivers/net/wireless/rtl8812au` folder

## Building (cross compile)
Compile for Armv6 (Pi0) & Armv7 (Pi2+) using the instructions given here:
* https://www.raspberrypi.org/documentation/linux/kernel/building.md
* https://www.raspberrypi.org/documentation/linux/kernel/configuring.md

Copy the `db.txt` to `drivers/net/wireless` and enable `CONFIG_CFG80211_INTERNAL_REGDB=y` in the `.conf` files, if you want to built a new kernel without using the provided source code under "releases" in this repository.

1. Rename the provided config files for both architectures to `.conf` depending what kernel you are building and copy them to the root folder of the sources (`/linux/.conf`).
2. `KERNEL=kernel` or `KERNEL=kernel7`
3. `make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig` to configure the kernel for your needs
4. Compile the kernel using `make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs -j 8` where 8 is your CPU core count
5. Use the correct location of your mounted SD card/DroneBridge patitions (see raspberrypi.org docs). We use `/dev/sddx` here:

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

# How to apply patches

To apply the patches copy the .patch file to the kernel root src directory run `patch -p0 < db_kernel_xxx.patch`


# Credits

* Initial patches provided by rodizio1 in the EZ-Wifibroadcast project
* Updated patches, move to Kernel 4.14.66 and integration of aircrack-ng/rtl8812au driver for DroneBridge by seeul8er
