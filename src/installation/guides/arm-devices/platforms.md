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

更多配置信息可以在 Raspberry Pi 基金会的[官方文档](https://www.raspberrypi.org/documentation/configuration/)中找到。`raspi-config` 工具不适用于 Void Linux，所以通常需要编辑 `/boot/config.txt` 文件。


#### 声音

要启用声音，请添加 `dtparam=audio=on` 到 `/boot/config.txt`. 

#### 串口

要启用串口控制台登录，请[启用](../../../config/services/index.md) `agetty-ttyAMA0` 服务。关于允许 root 登录的接口，见 [securetty(5)](https://man.voidlinux.org/securetty.5)。关于启动时串口的配置，参考 `/boot/cmdline.txt` 中的内核命令行 - 特别是 `console=ttyAMA0,115200` 参数。

### I2C

为了启用 [I2C](https://en.wikipedia.org/wiki/I%C2%B2C) ，在 `/boot/config.txt` 中加入`device_tree_param=i2c_arm=on`，在 `/boot/cmdline.txt` 中加入 `bcm2708.vc_i2c_override=1` 。然后创建一个 [modules-load(8)](https://man.voidlinux.org/modules-load.8) `.conf` 文件，内容如下:


```
i2c-dev
```

最后，安装 `i2c-tools` 包并使用 [i2cdetect(8)](https://man.voidlinux.org/i2cdetect.8) 来验证你的配置。它应该显示:

```
$ i2cdetect -l
i2c-1i2c          bcm2835 I2C adapter                 I2C adapter
```

### 内存 cgroup

`rpi-kernel` 软件包的内核[默认禁用了内存 cgroup](https://github.com/raspberrypi/linux/commit/9b0efcc1ec497b2985c6aaa60cd97f0d2d96d203#diff-f1d702fa7c504a2b38b30ce6bb098744)。

这破坏了使用容器的工作负载。因此，如果你想在 Raspberry Pi 上使用容器，你需要在 `/boot/cmdline.txt` 中加入`cgroup_enable=memory` 来启用内存 cgroups 。

