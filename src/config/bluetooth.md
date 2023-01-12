# 蓝牙

确保蓝牙控制器没有被封锁。使用 `rfkill` 检查是否有任何 blocks，并删除soft blocks。如果有 hard block，很可能有一个物理硬件开关或 BIOS 中的一个选项来启用蓝牙控制器。

```
$ rfkill
ID TYPE     DEVICE      SOFT      HARD
0 wlan      phy0   unblocked unblocked
1 bluetooth hci0     blocked unblocked

# rfkill unblock bluetooth
```

## 安装

安装 `bluez` 软件包并启用 `bluetoothd` 和 `dbus` 服务。然后，将你的用户加入 `bluetooth`用户组，重启 `dbus` 服务，或者直接重启系统。注意，重启 `dbus` 服务可能会杀死使用该服务的进程。

为了使用音频设备，如无线扬声器或耳机，ALSA 用户需要安装 `bluez-alsa` 软件包。[PulseAudio](./media/pulseaudio.md) 用户不需要任何额外的软件。[PipeWire](./media/pipewire.md) 用户需要 `libspa-bluetooth`。

## 用法

使用 `bluetoothctl` 管理蓝牙连接和控制器，它提供一个命令行界面，也接受标准输入的命令。

查阅 [Arch Wiki](https://wiki.archlinux.org/index.php/Bluetooth#Pairing) 有关如何配对设备的示例。 

## 配置

主要配置文件是 `/etc/bluetooth/main.conf`.
