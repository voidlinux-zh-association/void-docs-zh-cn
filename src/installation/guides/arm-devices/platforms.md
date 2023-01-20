# 所支持的平台

## 树莓派

所有 Raspberry Pi 变种的 `rpi-kernel` 包都是由 Raspberry Pi 基金会的内核树构建的，它应该可以实现所有主线内核所没有的特殊功能。RPi 内核包也有自己的头文件包，`rpi-kernel-headers` 。如果你想使用任何 DKMS 包，就应该安装这些包。Void 提供的 `rpi-base` 元包可以安装相关的 `rpi-kernel` 和 `rpi-firmware` 包。这些包一起实现了 Wi-Fi 和蓝牙功能。

传递给内核的 [command line](../../../config/kernel.md#cmdline) 参数在 `rootfs/boot/cmdline.txt` 文件中。一些相关的参数在[官方文档](https://www.raspberrypi.org/documentation/configuration/cmdline-txt.md)中有所记载。


### 支持的机型

| 机型                                         |  架构         |
|---------------------------------------------|--------------|
| 1 A, 1 B, 1 A+, 1 B+, Zero, Zero W, Zero WH | armv6l       |
| 2 B                                         | armv7l       |
| 3 B, 3 A+, 3 B+, Zero 2W, 4 B, 400          | aarch64      |

> 可以在 RPi 3 上运行 armv7l 图像，因为 RPi 3 的 CPU 同时支持 Armv8 和 Armv7 指令集。这些图像的区别在于，armv7l 图像提供了一个32位系统，而 arch64 图像提供了一个 64 位系统。

### 启用硬件 RNG 设备

默认情况下，[HWRNG](https://en.wikipedia.org/wiki/Hardware_random_number_generator) 设备不被系统使用，which may result in the random devices taking long to seed on boot。如果你想启动 sshd 并期望能够立即连接，这可能会很烦人。

为了解决这个问题，请安装 `rng-tools` 软件包，并[启用](../../../config/services/index.md#enabling-services) `rngd` 服务，它使用 `/dev/hwrng` 设备作为 `/dev/random` 的 seed。

### 图形 session

`mesa-dri` 软件包包含了所有 Raspberry Pi 变种的驱动，可以与 [modesetting Xorg
driver](../../../config/graphical-session/xorg.md#modesetting) 驱动或 [Wayland](../../../config/graphical-session/wayland.md) 一起使用。

### 硬件

More configuration information can be found in the Raspberry Pi Foundation's
[official
documentation](https://www.raspberrypi.org/documentation/configuration/). The
`raspi-config` utility isn't available for Void Linux, so editing the
`/boot/config.txt` file is usually required.

#### 声音

To enable the soundchip, add `dtparam=audio=on` to `/boot/config.txt`.

#### Serial

To enable serial console logins,
[enable](../../../config/services/index.md#enabling-services) the
`agetty-ttyAMA0` service. See
[securetty(5)](https://man.voidlinux.org/securetty.5) for interfaces that allow
root login. For configuration of the serial port at startup, refer to the kernel
command line in `/boot/cmdline.txt` - in particular, the
`console=ttyAMA0,115200` parameter.

### I2C

To enable [I2C](https://en.wikipedia.org/wiki/I%C2%B2C), add
`device_tree_param=i2c_arm=on` to `/boot/config.txt`, and
`bcm2708.vc_i2c_override=1` to `/boot/cmdline.txt`. Then create a
[modules-load(8)](https://man.voidlinux.org/modules-load.8) `.conf` file with
the following content:

```
i2c-dev
```

Finally, install the `i2c-tools` package and use
[i2cdetect(8)](https://man.voidlinux.org/i2cdetect.8) to verify your
configuration. It should show:

```
$ i2cdetect -l
i2c-1i2c          bcm2835 I2C adapter                 I2C adapter
```

### Memory cgroup

The kernel from the `rpi-kernel` package [disables the memory cgroup by
default](https://github.com/raspberrypi/linux/commit/9b0efcc1ec497b2985c6aaa60cd97f0d2d96d203#diff-f1d702fa7c504a2b38b30ce6bb098744).

This breaks workloads which use containers. Therefore, if you want to use
containers on your Raspberry Pi, you need to enable memory cgroups by adding
`cgroup_enable=memory` to `/boot/cmdline.txt`.
