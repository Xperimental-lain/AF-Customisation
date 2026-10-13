---

# Flashing OpenWrt onto JAF IDUs

*Disclaimer: This is ONLY for educational purposes. No one is responsible for any type of damage. Proceed at your own risk.*

This page is a generic guide for flashing OpenWrt onto the JAF IDU family, including both MediaTek (MTK7986A / Filogic) and Qualcomm (IPQ9554 / IPQ95xx) units.

## 1. Stock OpenWrt resources

Use the official OpenWrt resources before flashing:

- OpenWrt Firmware Selector: https://firmware-selector.openwrt.org/
- MediaTek / Filogic target info: https://openwrt.org/toh/mediatek/filogic
- Qualcomm IPQ95xx target info: https://openwrt.org/toh/qualcomm/ipq95xx
- Jio IDU community thread: https://forum.openwrt.org/t/install-openwrt-in-jio-6101-6201-6401-6601-6701-6111-6411-6611-6811-6911/253608
- MaxNet Qualcomm Jio OpenWrt build: https://github.com/mahendraplus/maxidu

If you are flashing a MediaTek IDU, select the `mediatek/filogic` images. If you are flashing a Qualcomm unit, select the `qualcomm/ipq95xx` images or use the Jio-specific community build referenced above.

## 2. Requirements

- USB-to-TTL serial adapter (3.3V logic)
- Ethernet cable and a TFTP server
- A working PC with `tftp`, `ping`, and a serial terminal (`screen`, `minicom`, or PuTTY)
- The correct OpenWrt initramfs image and, if available, the sysupgrade image for your model
- A backup of the stock firmware if possible

## 3. Prepare the host

1. Connect your serial adapter to the IDU UART header.
2. Open a serial terminal at 115200 baud (or the port's default rate, depending on model).
3. Power on the device and interrupt U-Boot by pressing the correct key combination as prompted.
4. Configure the PC with a static IP on the same subnet as the IDU bootloader network.
5. Put the initramfs image in the TFTP directory and ensure it is reachable.

Example network values:

```bash
PC: 192.168.1.2
IDU bootloader: 192.168.1.1
```

## 4. Boot into the OpenWrt initramfs image

### MediaTek / MTK7986A models

In U-Boot, use a flow similar to the following:

```bash
setenv ipaddr 192.168.1.1
setenv serverip 192.168.1.2

# example image name; adjust to your board and image file name
 tftpboot 0x46000000 openwrt-mediatek-filogic-initramfs-kernel.bin
bootm 0x46000000
```

If the exact commands vary by board revision, use the serial output to find the correct addresses and boot method.

### Qualcomm / IPQ9554 models

For Qualcomm-based Jio IDUs, the usual flow is:

```bash
setenv ipaddr 192.168.1.1
setenv serverip 192.168.1.2

# example image name; adjust for your image
 tftpboot 0x44000000 openwrt-qualcommax-ipq95xx-initramfs-fit-uImage.itb
bootm 0x44000000
```

Some boards may need a `booti`, `bootm`, or a different image format, so always validate the exact boot instructions from your build documentation or the community reference links above.

## 5. Verify the boot and install OpenWrt permanently

Once the initramfs image boots:

1. Confirm you have a working OpenWrt shell and network.
2. Check the storage layout, system partitions, and current rootfs.
3. Use `sysupgrade` from the OpenWrt CLI or the LuCI web interface once the image is confirmed functional.

Example:

```bash
sysupgrade -v /tmp/openwrt-*.bin
```

If the device was previously running the vendor firmware, make sure you back up the current configuration before replacing it.

## 6. After flashing

- Reboot and verify that OpenWrt boots normally.
- Configure the network, Wi-Fi, and any needed package feeds.
- If you need LuCI, follow the guide in `instructions/JAF-IDU-LuCI-Install-Guide.md`.
- If you need root access, use the steps in `instructions/JAF-IDU-Root-Access.md`.

## 7. Important notes

- Always confirm the exact board variant before flashing.
- Do not flash a mis-matched image; the wrong kernel or partition layout can brick the device.
- Keep the stock firmware available in case you need to recover the device.
- If the bootloader or UART access is locked or partially hidden, check the board-specific community references and serial console output before proceeding.

---

This guide is intentionally generic because different firmware revisions and board variants have slightly different bootloader instructions.
