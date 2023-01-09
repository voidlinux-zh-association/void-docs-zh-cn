# rc.conf, rc.local 还有 rc.shutdown

文件 `/etc/rc.conf` 、`/etc/rc.local` 和 `/etc/rc.shutdown` 可以用来配置 Void 系统的某些部分。`rc.conf` 通常由 `void-installer` 配置。
## rc.conf

来源于 runit stage 1 和 3。这个文件可以用来设置变量，包括以下内容：

### 键盘

指定在Linux控制台使用哪一个键盘类型。可用的键盘类型中列于 `/usr/share/kbd/keymaps` 如说：

```
KEYMAP=fr
```

有关详细信息，请参阅 [loadkeys(1)](https://man.voidlinux.org/loadkeys.1).

### 硬件时间

指定硬件时钟是设置为 UTC 还是本地时间。

By default this is set to `utc`. However, Windows sets the hardware clock to
local time, so if you are dual-booting with Windows, you need to either
configure Windows to use UTC, or set this variable to `localtime`.

默认情况下，它被设置为 `utc`。然而，Windows 将硬件时钟设置为本地时间，所以如果你用 Windows 进行双系统，你需要将Windows配置为使用UTC，或者将这个变量设置为 `localtime`。

有关详细信息，请参阅 [hwclock(8)](https://man.voidlinux.org/hwclock.8).

### 字体

指定用于 Linux 控制台的字体。 可用字体列在 `/usr/share/kbd/consolefonts`。 例如：

```
FONT=eurlatgr
```

有关详细信息，请参阅  [setfont(8)](https://man.voidlinux.org/setfont.8).

## rc.local

源于runit stage 2 。一个shell脚本，可以用来指定在登录前要做的配置。

## rc.shutdown

源于 runit stage 3。一个 shell 脚本，可以用来指定在关机时要完成的任务。
