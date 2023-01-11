# wpa_supplicant

`wpa_supplicant` 软件包默认安装在基本系统上。它包括配置无线接口和处理无线安全协议的实用程序。要使用 `wpa_supplicant`，你需要启用 wpa_supplicant 服务。

[wpa_supplicant(8)](https://man.voidlinux.org/wpa_supplicant.8) 是一个基于 [wpa_supplicant.conf(5)](https://man.voidlinux.org/wpa_supplicant.conf.5) 配置文件来管理无线接口的守护程序。在 `/usr/share/examples/wpa_supplicant/wpa_supplicant.conf` 中可以找到关于配置选项的广泛概述，包括例子。

[wpa_passphrase(8)](https://man.voidlinux.org/wpa_passphrase.8) 帮助创建用于配置文件的预共享密钥。 [wpa_cli(8)](https://man.voidlinux.org/wpa_cli.8) 提供了一个管理 `wpa_supplicant` 守护进程的 CLI。

## WPA-PSK

To use WPA-PSK, generate a pre-shared key with
[wpa_passphrase(8)](https://man.voidlinux.org/wpa_passphrase.8) and append the
output to the relevant `wpa_supplicant.conf` file:

要使用 WPA-PSK，用以下方法生成一个预共享密钥 [wpa_passphrase(8)](https://man.voidlinux.org/wpa_passphrase.8)，并将输出结果附加到相关的 `wpa_supplicant.conf` 文件中。


```
# wpa_passphrase <MYSSID> <passphrase> >> /etc/wpa_supplicant/wpa_supplicant-<device_name>.conf
```

## WPA-EAP

WPA-EAP 经常被用于机构 (教育机构) 登录，特别是 eduroam。这不使用 PSK，但可以像这样生成一个密码哈希:

```
$ echo -n <passphrase> | iconv -t utf16le | openssl md4
```

## WEP

对于 WEP 配置，将以下行添加到您的设备的 `wpa-supplicant.conf`: 

```
network={
    ssid="MYSSID"
    key_mgmt=NONE
    wep_key0="YOUR AP WEP KEY"
    wep_tx_keyidx=0
    auth_alg=SHARED
}
```

### wpa_supplicant 服务

`wpa_supplicant` 服务检查 `/etc/sv/wpa_supplicant/conf` 中的下列选项：

- `OPTS`: 要传递给服务的选项。覆盖任何其他选项。
- `CONF_FILE`: 用于配置的文件路径。 默认为 
   `/etc/wpa_supplicant/wpa_supplicant.conf`。
- `WPA_INTERFACE`: 要匹配的接口。 可能包含通配符； 默认为所有接口。
- `DRIVER`: 要使用的驱动程序。请参阅 `wpa_supplicant -h` 了解可用的驱动程序。

如果没有找到 `conf` 文件，服务会在 `/etc/wpa_supplicant` 中搜索以下文件：

- `wpa_supplicant-<interface>.conf`: 如果找到，这些文件就会被绑定到命名的接口上。
- `wpa_supplicant.conf`: 如果找到了，这个文件就会被加载，并与所有发现的其他接口绑定。

一旦你对你的配置感到满意，启用 `wpa_supplicant` 服务。

### 使用 wpa_cli

使用 `wpa_cli` 从命令行管理 `wpa_supplicant` 时，请务必通过 `-i` 选项指定使用哪个网络接口，例如。

```
# wpa_cli -i wlp2s0
```

不这样做会导致各种 `wpa_cli` 命令（例如， `scan` 和 `scan_results`) 没有产生预期的输出。 
