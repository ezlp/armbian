<div align="center">
    <img alt="Armbian" src="https://github.com/user-attachments/assets/74e55052-031b-48f8-9aca-e5f1dd9e256a" />
</div>

# Armbian

Armbian is a lightweight Linux distribution built specifically for ARM chips, based on Debian/Ubuntu. The Armbian system is lean, clean, and 100% compatible with Debian/Ubuntu, inheriting its functionality and rich software ecosystem. It runs securely and stably on TF/SD/USB storage and the device's eMMC. This project preserves the integrity of the official Armbian system while providing support for the S905X HG680P TV box, transforming it into a powerful server.

This project relies on many [contributors](CONTRIBUTORS.md) to build the Armbian system for the `Amlogic s905x HG680P` device. It supports writing to eMMC, kernel updates, and other features. For detailed usage, see the [Armbian User Documentation](./documents). The latest Armbian system can be downloaded from [Releases](https://github.com/ophub/amlogic-s9xxx-armbian/releases). Welcome to `Fork` and customize. If this project is helpful, please click the `Star` button in the upper right corner to show your support.

## Default Information for Armbian System

| System Name    | Default Username | Default Password  | SSH Port  | IP Address  |
| -------------- | ---------------- | ----------------- | --------- | ----------- |
| 🐧 [Armbian.OS](https://github.com/ophub/amlogic-s9xxx-armbian/releases) | root | 1234 | 22 | Obtain from router |
| 🐋 [Armbian.Docker](https://hub.docker.com/u/ophub) | root | 1234 | 22 | Static MacVLAN IP |

## Supported Device

| SoC | [Device](https://github.com/ophub/amlogic-s9xxx-armbian/releases) | [Kernel](https://github.com/ezlp/kernel) |
| ---- | ---- | ---- |
| s905x | [HG680P](https://github.com/ophub/amlogic-s9xxx-armbian/issues/262) | [stable](https://github.com/ezlp/kernel/releases/tag/kernel_stable) |


> [!NOTE]
> **Repository Scope & Configuration**:
> - **Target Device**: Exclusively supports **Amlogic S905X HG680P** (`meson-gxl-s905x-p212.dtb`).
> - **Kernel Repository**: Defaults to [ezlp/kernel](https://github.com/ezlp/kernel).
> - **Local U-Boot**: Pre-bundled with `u-boot-p212.bin` under `build-armbian/u-boot/amlogic/overload/` for self-contained builds without external downloads.
> - **GitHub Workflows**: Streamlined workflows pre-configured for S905X HG680P with `ezlp` signature.

> [!TIP]
> For more information, refer to the [✅ Model Database](build-armbian/armbian-files/common-files/etc/model_database.conf). Please read the [Armbian User Documentation](./documents) before use.

## Installation and Upgrade Instructions for Armbian

- ### Install Armbian to EMMC

For the `Amlogic s905x HG680P` device, use tools such as [Rufus](https://rufus.ie/) or [balenaEtcher](https://www.balena.io/etcher/) to write the system to a USB stick, then insert the USB stick into the device. Log in to the Armbian system (default user: root, default password: 1234) and enter the command:

```shell
armbian-install
```

| Optional | Default | Options | Description       |
| -------- | ------- | ------- | ----------------- |
| -m       | no      | yes/no  | Use mainline u-boot |
| -a       | yes     | yes/no  | Use [ampart](https://github.com/7Ji/ampart) partition adjustment tool |
| -l       | no      | yes/no  | Show full device list |

Example: `armbian-install -m yes -a no`

- ### Update Armbian Kernel

Log in to the Armbian system and enter the command:

```shell
# Run as root user (sudo -i)
# If no parameter is specified, it will be updated to the latest version.
armbian-update
```

| Optional | Default      | Options       | Description                      |
| -------- | ------------ | ------------- | -------------------------------- |
| -r       | ezlp/kernel | `<owner>/<repo>` | Set the repository for downloading kernels from github.com |
| -u       | Automation   | stable/flippy/beta | Set the kernel [tags suffix](https://github.com/ezlp/kernel/releases) |
| -k       | Latest version | Kernel version | Set the [kernel version](https://github.com/ezlp/kernel/releases/tag/kernel_stable) |
| -b       | yes          | yes/no        | Automatically back up the currently used kernel when updating |
| -d       | deb          | tar/deb       | Set the preferred kernel package format. If unavailable, the script will automatically try the alternative format. The `deb` format is recommended for compiling custom drivers. |
| -m       | no           | yes/no        | Use mainline u-boot |
| -s       | None         | None/DiskName | [SOS] Restore the system kernel on eMMC/NVMe/sdX or other disks |
| -h       | None         | None          | View help information |

Example: `armbian-update -k 5.15 -u stable -d deb`

When specifying the kernel version via the `-k` parameter, you can provide an exact version number (e.g., `armbian-update -k 5.15.50`) or specify only the kernel series (e.g., `armbian-update -k 5.15`). When a series is specified, the latest version within that series will be used automatically.

During kernel updates, the currently running kernel is automatically backed up to the `/ddbr/backup` directory, retaining the 3 most recent versions. If the newly installed kernel proves unstable, you can restore a backup kernel at any time. If a kernel update renders the system unbootable, use `armbian-update -s` to restore the system kernel. For more details, see the [Help Document](documents#10-updating-armbian-kernel).

- ### Replace Armbian Sources

Log in to the Armbian system and enter the command:

```shell
armbian-apt
```

Choosing the appropriate software source for your country or region can significantly improve download speeds. For more details, see the [Help Document](documents#11-installing-common-software).

- ### Install Common Software

Log in to the Armbian system and enter the command:

```shell
armbian-software
```

The command `armbian-software -u` updates the local software center list. Based on user feedback in [Issues](https://github.com/ophub/amlogic-s9xxx-armbian/issues), commonly used [software](build-armbian/armbian-files/common-files/usr/share/ophub/armbian-software/software-list.conf) has been gradually integrated with one-click install/update/uninstall support. This includes `Docker images`, `desktop software`, `application services`, and more. See the [detailed instructions](documents/armbian_software.md).

- ### Modify Armbian Configuration

Log in to the Armbian system and enter the command:

```shell
armbian-config
```

- ### Create Swap for Armbian

If you find the device's memory insufficient when running memory-intensive applications such as `Docker`, you can create a `swap` virtual memory partition to use a portion of disk space as additional memory. The parameter unit is `GB`, with a default value of `1`.

Log in to the Armbian system and enter the command:

```shell
armbian-swap 1
```

- ### Control LED Display

Log in to the Armbian system and enter the command:

```shell
armbian-openvfd
```

Debug according to the [LED Screen Display Control Instructions](documents/led_screen_display_control.md).

- ### Backup/Restore EMMC Original System

Supports backing up and restoring the device's `EMMC` partition via `TF/SD/USB`. Before installing Armbian on a new device, it is recommended to back up the original Android TV system for future restoration if needed.

Boot the Armbian system from `TF/SD/USB` and enter the command:

```shell
armbian-ddbr
```

Enter `b` at the prompt to back up the system, or `r` to restore.

> [!IMPORTANT]
> Alternatively, the Android system can be flashed directly into eMMC via USB cable. Android system images are available in [Tools](https://github.com/ezlp/kernel/releases/tag/tools).

- ### Compile the Kernel in Armbian

For kernel compilation instructions, see the [Compile Kernel](compile-kernel) documentation. Log in to the Armbian system and enter the command:

```shell
armbian-kernel -u
armbian-kernel -k 6.6.12
```

- ### More Usage Instructions

To update all service scripts in the system to the latest version, log in to the Armbian system and enter the command:

```shell
armbian-sync
```

For common issues and their solutions when using Armbian, see [documents](documents).

## Local Packaging

1. Clone the repository to local `git clone --depth 1 https://github.com/ophub/amlogic-s9xxx-armbian.git`

2. Install the necessary software packages (using Ubuntu 24.04 as an example)

Enter the `~/amlogic-s9xxx-armbian` root directory, then run the installation command:

```shell
sudo apt-get update -y
sudo apt-get full-upgrade -y
# For Ubuntu-24.04
sudo apt-get install -y $(cat compile-kernel/tools/script/ubuntu2404-build-armbian-depends)
```

3. Enter the `~/amlogic-s9xxx-armbian` root directory, create the `build/output/images` folder, and upload the Armbian image file (e.g., `Armbian_21.11.0-trunk_S905x_current_5.15.50.img`) to the `~/amlogic-s9xxx-armbian/build/output/images` directory. Retain the release version number (e.g., `21.11.0`) and kernel version number (e.g., `5.15.50`) in the original filename, as they will be used for naming the rebuilt Armbian system.

4. Enter the `~/amlogic-s9xxx-armbian` root directory and run `sudo ./rebuild -b s905x -k 6.6.12` to generate the Armbian image for the HG680P s905x board. Output files are saved in the `build/output/images` directory.

- ### Local Packaging Parameter Description

| Parameter | Meaning     | Description |
| ----      | ----------  | ----------  |
| -b        | Board      | Specifies the target device codename (`s905x` for HG680P). For supported board configuration, refer to [model_database.conf](build-armbian/armbian-files/common-files/etc/model_database.conf). Default: `s905x` |
| -r        | KernelRepo | Specify the `<owner>/<repo>` of the github.com kernel repository. Default value: `ezlp/kernel` |
| -u        | kernelUsage | Set the `tags suffix` of the kernel used, such as [stable](https://github.com/ezlp/kernel/releases/tag/kernel_stable), [flippy](https://github.com/ezlp/kernel/releases/tag/kernel_flippy), [beta](https://github.com/ezlp/kernel/releases/tag/kernel_beta). Default value: `stable` |
| -k        | Kernel     | Specify [kernel](https://github.com/ezlp/kernel/releases/tag/kernel_stable) name, such as `-k 6.6.12`. Connect multiple kernels with `_`, such as `-k 6.6.12_5.15.50`. |
| -a        | AutoKernel | Set whether to automatically use the latest kernel within the same series. When `true`, the kernel repository is checked for newer versions within the series specified by `-k` (e.g., 6.6.12), and if found, the latest version is used automatically. When `false`, the exact specified version is used. Default: `true` |
| -t        | RootfsType | Set the file system type of the ROOTFS partition. Options: `ext4` or `btrfs`. Example: `-t btrfs`. Default: `ext4` |
| -s        | Size       | Set the image partition sizes. To set only the ROOTFS partition, specify a single value (e.g., `-s 2560`). To set both BOOTFS and ROOTFS, join them with `/` (e.g., `-s 512/2560`). Default: `512/2560` |
| -n        | BuilderName | Set the Armbian system builder signature. Do not include spaces. Default: None |

- `sudo ./rebuild`: Use default configuration to package for s905x HG680P.
- `sudo ./rebuild -b s905x -k 6.6.12`: Build for s905x HG680P with specified kernel.
- `sudo ./rebuild -b s905x -k 6.1.y`: Build for s905x HG680P using the latest kernel in the 6.1.y series.

## Use GitHub Actions for Compilation

1. Workflow configuration files are located in the [.github/workflows/](.github/workflows/) directory.

2. Fresh Build: On the [Actions](https://github.com/ophub/amlogic-s9xxx-armbian/actions) page, select ***`Build Armbian server image`*** to use the [build-armbian-arm64-server-image.yml](.github/workflows/build-armbian-arm64-server-image.yml) workflow. You can choose from Ubuntu series (e.g., `resolute`) or Debian series (e.g., `trixie`). Click ***`Run workflow`*** to start the build.

3. Rebuild: If [Releases](https://github.com/ophub/amlogic-s9xxx-armbian/releases) already contains compiled `Armbian_.*-trunk_.*.img.gz` files and you only need to repackage for the s905x HG680P board, skip the source compilation step and use [build-armbian-using-releases-files.yml](.github/workflows/build-armbian-using-releases-files.yml) for secondary builds.

4. To use other Armbian systems, reference this repository's script in the workflow file [build-armbian-using-official-image.yml](.github/workflows/build-armbian-using-official-image.yml):

```yaml
- name: Build Armbian
  uses: ophub/amlogic-s9xxx-armbian@main
  with:
    build_target: armbian
    armbian_path: build/output/images/*.img
    armbian_board: s905x
    armbian_kernel: 6.12.y_6.18.y
```

- ### GitHub Actions Input Parameter Description

| Parameter       | Default       | Description                                             |
|-----------------|---------------|---------------------------------------------------------|
| armbian_path    | None          | Set the path of the original Armbian file. Supports workflow file paths (e.g., `build/output/images/*.img`) and network download URLs. |
| armbian_board   | s905x         | Set the `board` of the package box (`s905x`), refer to `-b` |
| kernel_repo     | ezlp/kernel  | Specify `<owner>/<repo>` of the github.com kernel repository, refer to `-r` |
| kernel_usage    | stable        | Set the `tags suffix` of the used kernel. Refer to `-u` |
| armbian_kernel  | 6.12.y_6.18.y | Set the [version](https://github.com/ezlp/kernel/releases/tag/kernel_stable) of the kernel, refer to `-k` |
| auto_kernel     | true          | Set whether to automatically adopt the latest version of the same series kernel, refer to `-a`       |
| armbian_fstype  | ext4          | Set the file system type of the system's ROOTFS partition, refer to `-t`  |
| armbian_size    | 512/2560      | Set the size of the system BOOTFS and ROOTFS partitions, function reference `-s`  |
| armbian_files   | false         | Add custom Armbian files. When set, all files in this directory will be copied to [common-files](build-armbian/armbian-files/common-files). |
| builder_name    | None          | Set the Armbian system builder signature, refer to `-n` |

- ### GitHub Actions Output Variable Description

Uploading to `Releases` requires `Workflow read and write permissions` for the repository. See the [usage instructions](documents/README.md#2-set-up-private-variable-github_token) for details.

| Parameter                        | Default       | Description                           |
|----------------------------------|---------------|---------------------------------------|
| ${{ env.PACKAGED_OUTPUTPATH }}   | out           | Armbian system files output path      |
| ${{ env.PACKAGED_OUTPUTDATE }}   | 04.13.1058    | Packaging date (month.day.hourminute) |
| ${{ env.PACKAGED_STATUS }}       | success       | Packaging status: success / failure   |

## Build Armbian Docker Image

For creating [Docker](https://hub.docker.com/u/ophub) images of the Armbian system, refer to the [armbian_docker](./compile-kernel/tools/script/docker) build script.

## Compiling Kernel Using GitHub Actions

For kernel compilation instructions, see [compile-kernel](compile-kernel).

```yaml
- name: Compile the kernel
  uses: ophub/amlogic-s9xxx-armbian@main
  with:
    build_target: kernel
    kernel_version: 6.12.y_6.18.y
    kernel_auto: true
    kernel_sign: -yourname
```

## Armbian Contributors

First and foremost, thanks to [150balbes](https://github.com/150balbes) for the outstanding contributions and the solid foundation laid for running Armbian on Amlogic TV boxes. The [Armbian](https://github.com/armbian/build) system compiled here uses the latest official source code for real-time builds. Thanks to everyone's dedication and sharing.

The [u-boot](https://github.com/ophub/u-boot), [kernel](https://github.com/ezlp/kernel), and other resources used in this system are primarily sourced from the [unifreq/openwrt_packit](https://github.com/unifreq/openwrt_packit) project. Some files have been contributed by users through [Pull Requests](https://github.com/ophub/amlogic-s9xxx-armbian/pulls) and [Issues](https://github.com/ophub/amlogic-s9xxx-armbian/issues). All contributions since the repository's creation (`2021-09-19`) are recorded in [CONTRIBUTORS.md](https://github.com/ophub/amlogic-s9xxx-armbian/blob/main/CONTRIBUTORS.md). Thanks again to everyone for breathing new life and purpose into these devices.

## Other Distributions

- The [amlogic-s9xxx-openwrt](https://github.com/ophub/amlogic-s9xxx-openwrt) project provides the `OpenWrt` system for TV boxes.
- The [fnnas](https://github.com/ophub/fnnas) project provides the `FnNAS` system for TV boxes.
- [7Ji](https://7ji.github.io/) has published articles on reverse engineering and development for the Amlogic platform. His [ampart](https://github.com/7Ji/ampart) project provides a partition tool for reading and editing Amlogic eMMC partition tables and DTB partitions.

## Links

- [armbian](https://github.com/armbian/build)
- [unifreq](https://github.com/unifreq)
- [kernel.org](https://kernel.org)

## License

The amlogic-s9xxx-armbian © OPHUB is licensed under [GPL-2.0](LICENSE)
