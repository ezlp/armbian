# Armbian Build and Usage Guide

GitHub Actions is a CI/CD service from Microsoft that provides high-performance virtual server environments for building, testing, packaging, and deploying projects. Public repositories can use it free of charge with no time limit, and each build can run for up to 6 hours — more than sufficient for compiling Armbian (typically completed within about 3 hours). This project is shared for educational and experience-exchange purposes. Please do not initiate any harmful network attacks or misuse GitHub Actions.

# Table of Contents

- [Armbian Build and Usage Guide](#armbian-build-and-usage-guide)
- [Table of Contents](#table-of-contents)
  - [1. Register your own Github account](#1-register-your-own-github-account)
  - [2. Set up private variable GITHUB\_TOKEN etc](#2-set-up-private-variable-github_token-etc)
  - [3. Fork the repository and set Workflow permissions](#3-fork-the-repository-and-set-workflow-permissions)
  - [4. Customization instructions for personalized Armbian system files](#4-customization-instructions-for-personalized-armbian-system-files)
  - [5. Compile the system](#5-compile-the-system)
    - [5.1 Manual Compilation](#51-manual-compilation)
    - [5.2 Scheduled Compilation](#52-scheduled-compilation)
    - [5.3 Customizing Default System Configuration](#53-customizing-default-system-configuration)
    - [5.4 Expanding Github Actions Compilation Space Using Logical Volumes](#54-expanding-github-actions-compilation-space-using-logical-volumes)
    - [5.5 Build Armbian Docker image](#55-build-armbian-docker-image)
  - [6. Saving the System](#6-saving-the-system)
  - [7. Downloading the System](#7-downloading-the-system)
  - [8. Installing Armbian to EMMC](#8-installing-armbian-to-emmc)
    - [8.1 Installation Method for Amlogic s905x HG680P](#81-installation-method-for-amlogic-s905x-hg680p)
    - [8.2 Installation Method for the Docker Version of Armbian](#82-installation-method-for-the-docker-version-of-armbian)
      - [8.2.1 Install Docker Runtime Environment](#821-install-docker-runtime-environment)
      - [8.2.2 Configure macvlan Network](#822-configure-macvlan-network)
      - [8.2.3 Run Armbian Docker Container](#823-run-armbian-docker-container)
  - [9. Compiling Armbian Kernel](#9-compiling-armbian-kernel)
    - [9.1 How to Add Custom Kernel Patches](#91-how-to-add-custom-kernel-patches)
    - [9.2 How to Make Kernel Patches](#92-how-to-make-kernel-patches)
    - [9.3 How to Customize Compilation of Driver Modules](#93-how-to-customize-compilation-of-driver-modules)
  - [10. Updating Armbian Kernel](#10-updating-armbian-kernel)
  - [11. Installing Common Software](#11-installing-common-software)
  - [12. Frequently Asked Questions](#12-frequently-asked-questions)
    - [12.1 dtb and u-boot Correspondence Table](#121-dtb-and-u-boot-correspondence-table)
    - [12.2 Instructions for LED Screen Display Control](#122-instructions-for-led-screen-display-control)
    - [12.3 How to Restore the Original Android TV System](#123-how-to-restore-the-original-android-tv-system)
      - [12.3.1 Backup and Restore Using Armbian-ddbr](#1231-backup-and-restore-using-armbian-ddbr)
      - [12.3.2 Recovering using Amlogic Flashing Tool](#1232-recovering-using-amlogic-flashing-tool)
    - [12.4 Setting the box to boot from USB/TF/SD](#124-setting-the-box-to-boot-from-usbtfsd)
      - [12.4.1 Initial Installation of Armbian System](#1241-initial-installation-of-armbian-system)
      - [12.4.2 Reinstallation of Armbian System](#1242-reinstallation-of-armbian-system)
    - [12.5 Disable Infrared Receiver](#125-disable-infrared-receiver)
    - [12.6 Boot file selection](#126-boot-file-selection)
    - [12.7 Network Configuration](#127-network-configuration)
    - [12.8 How to Add Startup Tasks](#128-how-to-add-startup-tasks)
    - [12.9 How to Update Service Scripts in the System](#129-how-to-update-service-scripts-in-the-system)
    - [12.10 How to Get Android System Partition Information on eMMC](#1210-how-to-get-android-system-partition-information-on-emmc)
    - [12.11 How to build the u-boot file for Amlogic devices](#1211-how-to-build-the-u-boot-file-for-amlogic-devices)
    - [12.12 Error in Memory Size Recognition](#1212-error-in-memory-size-recognition)
    - [12.13 How to Decompile dtb Files](#1213-how-to-decompile-dtb-files)
    - [12.14 How to Modify cmdline Settings](#1214-how-to-modify-cmdline-settings)
    - [12.15 How to Add New Supported Devices](#1215-how-to-add-new-supported-devices)
    - [12.16 How to Resolve I/O Errors Writing to eMMC](#1216-how-to-resolve-io-errors-writing-to-emmc)
    - [12.17 How to build the boot.scr file](#1217-how-to-build-the-bootscr-file)
    - [12.18 How to Enable Remote Desktop and Modify the Default Port](#1218-how-to-enable-remote-desktop-and-modify-the-default-port)
    - [12.19 TCP Congestion Control Optimization Guide](#1219-tcp-congestion-control-optimization-guide)

---

## 8. Installing Armbian to EMMC

First, download the Armbian system image for your device from [Releases](https://github.com/ophub/amlogic-s9xxx-armbian/releases) and decompress it to .img format.

After installation, connect the device to your `router` and allow `2 minutes` for it to boot. Then find the device's `IP` address (listed as Armbian) in your router's admin panel and connect via `SSH`. The default username is `root`, the default password is `1234`, and the default port is `22`.

### 8.1 Installation Method for Amlogic s905x HG680P

Log in to the Armbian system (default user: root, default password: 1234) → Enter the command:

```shell
armbian-install
```

| Optional Parameter | Default Value | Options | Description                          |
| ------------------ | ------------- | ------- | ------------------------------------ |
| -m                 | no            | yes/no  | Use Mainline u-boot                  |
| -a                 | yes           | yes/no  | Use [ampart](https://github.com/7Ji/ampart) partition table adjustment tool |
| -l                 | no            | yes/no  | List. Display the entire device list |

Example: `armbian-install -m yes -a no`

### 8.2 Installation Method for the Docker Version of Armbian

You can use Docker versions of Armbian images on Ubuntu/Debian/Armbian systems. These images are hosted on [Docker Hub](https://hub.docker.com/r/ophub) and can be downloaded directly for use.

Four Armbian Docker images with different base versions are provided: `armbian-trixie`, `armbian-bookworm`, `armbian-noble`, and `armbian-resolute`.

#### 8.2.1 Install Docker Runtime Environment

```shell
curl -fsSL https://get.docker.com | sh
sudo usermod -aG docker $USER
```

#### 8.2.2 Configure macvlan Network

```shell
docker network create -d macvlan --subnet=192.168.1.0/24 --gateway=192.168.1.1 -o parent=eth0 macvlan
```

#### 8.2.3 Run Armbian Docker Container

```shell
docker run -d --name armbian --net macvlan --ip 192.168.1.200 --privileged ophub/armbian:trixie
```

---

## 9. Compiling Armbian Kernel

### 9.1 How to Add Custom Kernel Patches

Put custom `.patch` files in `compile-kernel/tools/patch`. When `armbian-kernel` or `./recompile -p true` is executed, patches in this directory will be automatically applied during build.

### 9.2 How to Make Kernel Patches

```shell
diff -u original_file modified_file > my_patch.patch
```

### 9.3 How to Customize Compilation of Driver Modules

Custom drivers or module choices can be configured during kernel recompile options (`make menuconfig`).

---

## 10. Updating Armbian Kernel

Log in to the Armbian system as root and run:

```shell
armbian-update
```

---

## 11. Installing Common Software

Run `armbian-software` to install or manage software applications.

---

## 12. Frequently Asked Questions

### 12.1 dtb and u-boot Correspondence Table

For `Amlogic s905x HG680P`, the device tree and boot configuration details:

| SoC | Model | DTB File | U-Boot Overload |
| --- | ----- | -------- | --------------- |
| s905x | HG680P | meson-gxl-s905x-p212.dtb | u-boot-p212.bin |

> [!NOTE]
> - **Local U-Boot File**: Pre-loaded in `build-armbian/u-boot/amlogic/overload/u-boot-p212.bin` for standalone building.
> - **Kernel Repository**: Configured by default to use [ezlp/kernel](https://github.com/ezlp/kernel).

### 12.2 Instructions for LED Screen Display Control

Refer to [led_screen_display_control.md](led_screen_display_control.md).

### 12.3 How to Restore the Original Android TV System

#### 12.3.1 Backup and Restore Using Armbian-ddbr

Boot Armbian from USB/SD card and execute:

```shell
armbian-ddbr
```

Enter `b` to back up or `r` to restore eMMC partitions.

#### 12.3.2 Recovering using Amlogic Flashing Tool

Flash original Android ROM using Amlogic USB Burning Tool over USB male-to-male cable.

### 12.4 Setting the box to boot from USB/TF/SD

#### 12.4.1 Initial Installation of Armbian System

Use Android terminal or reboot app with `reboot update` command while USB drive is inserted.

#### 12.4.2 Reinstallation of Armbian System

Insert USB drive and power on device.

### 12.5 Disable Infrared Receiver

Modify `/etc/modprobe.d/` blacklists or disable infrared service if needed.

### 12.6 Boot file selection

For `s905x HG680P`, use `meson-gxl-s905x-p212.dtb`.

### 12.7 Network Configuration

Configured via standard `/etc/network/interfaces` or `NetworkManager` (`nmtui` / `nmcli`).

### 12.8 How to Add Startup Tasks

Place custom scripts in `/etc/custom_service/start_service.sh` or create systemd services.

### 12.9 How to Update Service Scripts in the System

Run `armbian-sync`.

### 12.10 How to Get Android System Partition Information on eMMC

Run `ampart /dev/mmcblk2` or view `/proc/partitions`.

### 12.11 How to build the u-boot file for Amlogic devices

Extract bootloader and dtb using `dd` and build using `u-boot` source code.

### 12.12 Error in Memory Size Recognition

If memory is improperly detected, verify `/boot/u-boot.ext` or `/boot/u-boot.emmc`.

### 12.13 How to Decompile dtb Files

```shell
dtc -I dtb -O dts -o meson-gxl-s905x-p212.dts meson-gxl-s905x-p212.dtb
```

### 12.14 How to Modify cmdline Settings

In Amlogic devices, add/modify/delete settings in `/boot/uEnv.txt`.

### 12.15 How to Add New Supported Devices

Add device entry to `/etc/model_database.conf` and place device files in `build-armbian/armbian-files/`.

### 12.16 How to Resolve I/O Errors Writing to eMMC

Add `mmc_core.max_freq=50000000` to cmdline or adjust `max-frequency` in DTB.

### 12.17 How to build the boot.scr file

```shell
mkimage -C none -A arm -T script -d boot.cmd boot.scr
```

### 12.18 How to Enable Remote Desktop and Modify the Default Port

Edit `/etc/xrdp/xrdp.ini` to adjust default port.

### 12.19 TCP Congestion Control Optimization Guide

Configure TCP congestion control algorithm in `/etc/sysctl.conf`.
