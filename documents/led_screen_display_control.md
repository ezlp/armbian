# LED Screen Display Control Instructions

- The configuration files are located in the [/usr/share/openvfd](../build-armbian/armbian-files/platform-files/amlogic/rootfs/usr/share/openvfd) directory of the `Armbian/OpenWrt` system. The `Armbian` command file is at [/usr/sbin/armbian-openvfd](../build-armbian/armbian-files/platform-files/amlogic/rootfs/usr/sbin/armbian-openvfd), and the `OpenWrt` command file is at [/usr/sbin/openwrt-openvfd](https://github.com/ophub/amlogic-s9xxx-openwrt/blob/main/make-openwrt/openwrt-files/common-files/usr/sbin/openwrt-openvfd). If these files are not present in your system, upload them manually and assign execution permissions: `chmod +x /usr/share/openvfd/vfdservice /usr/sbin/*-openvfd`.

- Update the system kernel to the latest version. For the `Armbian` system, use the `armbian-sync` command.

- Rename the configuration file to `diy.conf` and upload it to the `/usr/share/openvfd/conf` directory, then enter the command `armbian-openvfd 99` for testing.

- The command `armbian-openvfd 0` disables the LED display and clears associated system processes. Before testing each new configuration, always run this disable command first, then execute `armbian-openvfd 99` to test the modified configuration.

- Some devices may display a boot message before Linux starts (e.g., showing `BOOT`). To clear this message, first run `armbian-openvfd 0` to stop existing services, then run `armbian-openvfd <boxid>` to take control of the LED display. To disable the display entirely, run `armbian-openvfd 0` again.

- Once the display is working correctly, add it to the boot startup tasks. Replace `15` in the following commands with your device's `BoxID`:

```yaml
# Execute the following command in the terminal to enable the openvfd service
sed -i 's|^#*openvfd_enable=.*|openvfd_enable="yes"|g' /etc/custom_service/start_service.sh
sed -i 's|^#*openvfd_boxid=.*|openvfd_boxid="15"|g' /etc/custom_service/start_service.sh
# Some devices require restarting the OpenVFD service to clear 'BOOT' and related messages
sed -i 's|^#*openvfd_restart=.*|openvfd_restart="yes"|g' /etc/custom_service/start_service.sh
```

|  BoxName   | `BoxID` |  Armbian Command      |   OpenWrt Command       |  Function   |
| ---------- | ------- | --------------------- | ----------------------- | ----------- |
| diy        |  99     |  armbian-openvfd 99   |   openwrt-openvfd 99    | Enable LED  |
| -          |  0      |  armbian-openvfd 0    |   openwrt-openvfd 0     | Disable LED |
| -          |  -u     |  armbian-openvfd -u   |   openwrt-openvfd -u    | Update Conf |
