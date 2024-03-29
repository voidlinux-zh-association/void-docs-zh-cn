# 打印

CUPS（Common Unix Printing System）是 Void Linux 上支持的连接到打印机的机制。

作为先决条件，安装 `cups` 软件包并启用 `cupsd` 服务。等到该服务被标记为可用。

## 安装打印机驱动程序

如果打印机是通过网络访问的，并且支持 PostScript 或 PCL，仅 CUPS 就足够了。然而，额外的驱动包对于本地打印机的支持是必要的。`cups-filters` 软件包为CUPS提供驱动支持。

根据有关硬件的情况，可能需要额外的驱动程序。

一些 CUPS 驱动包含专有的或仅有的二进制扩展。这些只在非免费软件库中提供，有时只针对特定的架构。

### 无驱动打印

大多数现代网络打印机都支持使用 IPP Everywhere 标准进行无驱动打印。请参阅 https://www.pwg.org/printers/ ，了解支持该标准的自我认证的打印机列表。即使某台打印机不在这个名单上，它仍有很大可能被支持。

请注意，无驱动打印仍然需要 `cups-filters`。

### Gutenprint 驱动

Gutenprint 为许多打印机提供了支持。 这些驱动程序包含在 `gutenprint` 软件包。 

### HP 驱动

惠普的打印机需要 `hplip` 软件包。

运行以下命令将指导你完成驱动安装过程。它所建议的默认配置选择通常是足够的。

```
# hp-setup -i
```

### Brother 驱动

对于 Brother 打印机的支持，请安装 foomatic 驱动程序，它包含在 `foomatic-db` 和 `foomatic-db-nonfree` 软件包中。对各种激光打印机型号的支持是由 `brother-brlaser` 软件包提供的。

###  Epson 喷墨打印机的驱动

请安装 `epson-inkjet-printer-escpr`。

### Canon PIXMA/MAXIFY 驱动

`cnijfilter2` 软件包包含了用于佳能 PIXMA 和 MAXIFY 各种型号的驱动程序。请注意，安装该驱动包[需要启用 "非自由" 软件源](../../xbps/repositories/index.md#nonfree)。

## 配置新打印机

CUPS 提供了一个 Web 界面和命令行工具，可用于配置打印机。 此外，还提供各种本机 GUI 选项，并且 可能更适合，具体取决于用例。 


### 自动地

可以发现和配置支持 IPP Everywhere 的打印机 自动使用 [ZeroConf](http://www.zeroconf.org/)。 要启用此功能， 安装 `avahi` 和 `nss-mdns` 打包并启用 `avahi-daemo`n 服务。 

### Web 界面

要使用 CUPS 网络界面配置打印机，请在浏览器中导航到 http://localhost:63 1。在 "Administration" 标签下，选择 "Printers > Add Printer"。当被要求登录时，使用 `lpadmin` 用户组中的账户。

### Command line

[lpadmin(8)](https://man.voidlinux.org/lpadmin.8)  工具可用于使用命令行配置打印机.

### 图形化界面

`system-config-printer` 软件包提供了简单而强大的新打印机配置。安装并调用它：

```
# system-config-printer
```

通常情况下，这个工具需要 root 权限。然而，如果你使用 PolicyKit，你可以安装 `cups-pk-helper` 包，以允许无特权用户使用 `system-config-printer`。

虽然这里显示的是 `system-config-printer`，但你的桌面环境可能有一个本地的打印机对话框，可以通过查阅你的桌面环境的文档找到。


## 故障排除

### 未显示 USB 打印机

可以通过运行以下命令手动找到设备 URI： 

```
# /usr/lib/cups/backend/usb
```

