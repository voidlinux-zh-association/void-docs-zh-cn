# 日期和时间

查看系统的当前日期和时间信息，以及对其进行更改，请使用 [date(1)](https://man.voidlinux.org/date.1).

## 时区

系统默认的时区可以通过将时区文件链接到 `/etc/localtime` :

```
# ln -sf /usr/share/zoneinfo/<timezone> /etc/localtime
```


> 注意: 如果在 `/etc/rc.conf`　中设置了　`TIMEZONE` 变量, 应该将其删除或注释掉。因为这将覆盖在 `ln` 中设置的内容。
> 然后重启.

要在每个用户的基础上改变时区，可以从你的 shell 的配置文件中导出 `TZ` 变量。

```
export TZ=<timezone>
```

注意，设置*时区*并不设置*时间*（或日期）；相反，它只是指定了与 [UTC](https://en.wikipedia.org/wiki/Coordinated_Universal_Time)  的偏移量，如 [timezone(3)](https://man.voidlinux.org/timezone.3) 中所述。

## 硬件时钟

默认情况下，Void 的硬件时钟被存储为 UTC。Windows 默认不使用 UTC，如果你是双系统启动，这将与 Void 冲突。你可以通过在 `/etc/rc.conf` 中设置 `HARDWARECLOCK` 变量，将 Windows 改为使用 UTC，或者将 Void Linux 改为使用 `localtime`:

```
export HARDWARECLOCK=localtime
```

有关详细信息，请参阅 [hwclock(8)](https://man.voidlinux.org/hwclock.8).

## NTP

为了保持系统时钟的准确性，您可以使用 [网络时间协议](https://en.wikipedia.org/wiki/Network_Time_Protocol) (NTP).

Void 为三个 NTP 守护进程提供了包：NTP、OpenNTPD 和 Chrony。 

一旦你安装了一个 NTP 守护进程，你就可以[启用服务](../config/services/index.md#managing-services)或 [xbps-alternatives(1)](https://man.voidlinux.org/xbps-alternatives.1) 管理的 `ntpd` 服务来为它启用服务。


### NTP

NTP 是网络时间协议的官方参考实现。

`ntp` 包提供 NTP 和 `isc-ntpd` 服务。 

有关详细信息，请访问 [the NTP site](https://www.ntp.org/).

### OpenNTPD

OpenNTPD 专注于提供一个安全、精简的NTP实现，对于大多数的使用情况来说，它 "只是工作"，具有合理的准确性。

`openntpd` 软件包提供 OpenNTPD 和 `openntpd` 服务。

有关详细信息，请访问 https://www.openntpd.org/.

### Chrony

Chrony 的设计是为了在各种条件下都能很好地工作；它可以比NTP更快地同步，而且精度更高。

这 `chrony` 包提供了 Chrony 和 `chronyd` 服务。 

Chrony网站简要介绍了其[相对于NTP的优势](https://chrony.tuxfamily.org/faq.html#_how_does_chrony_compare_to_ntpd)，[以及Chrony 、 NTP 和 OpenNTPD 的详细功能比较](https://chrony.tuxfamily.org/comparison.html)。
