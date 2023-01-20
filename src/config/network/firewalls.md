# 防火墙

## iptables

默认情况下，`iptables` 软件包被安装在基本系统上。它提供了 [iptables(8)/ip6tables(8)](https://man.voidlinux.org/iptables.8)。相关服务使用 `/etc/iptables/iptables.rules` 和 `/etc/iptables/ip6tables.ruleset` 文件，这些文件必须由系统管理员创建。

在 `/etc/iptables` 目录下提供了两个规则集的例子：`empty.rules` 和 `simple_firewall.rules` 。

### 在启动时应用规则

要在 runit stage 1应用 iptables 规则，请安装 `runit-iptables` 软件包。这增加了一个核心服务，恢复了 `iptables.rules` 和 `ip6tables.rulesets`。

或者，要在 stage 2 应用这些规则，请在 `/etc/rc.local` 中添加以下内容：

```
if [ -e /etc/iptables/iptables.rules ]; then
  iptables-restore /etc/iptables/iptables.rules
fi

if [ -e /etc/iptables/ip6tables.rules ]; then
  ip6tables-restore /etc/iptables/ip6tables.rules
fi
```

重新启动后，检查活动的防火墙规则：

```
# iptables -L
# ip6tables -L
```

### 在运行时应用规则

`iptables` 带有两个 runit 服务，`iptables` 和 `ip6tables`，用于快速 falsh 或 restore `iptables.rules` 和 `ip6tables.rulesets`。一旦这些服务被[启用](../services/index.md)，你可以通过关闭相关服务来刷新规则集，例如:

```
# sv down iptables
```

并通过提高相关服务来恢复它们，例如:

```
# sv up ip6tables
```

## nftables

`nftables` 取代了 `iptables`、`ip6tables`、`arptables` 和 `ebtables`（统称为 `xtables`）。[nftables wiki](https://wiki.nftables.org/wiki-nftables/index.php/Main_Page) 描述了与 `iptables` 工具集的[主要区别](https://wiki.nftables.org/wiki-nftables/index.php/Main_differences_with_iptables)。

要使用 `nftables`，请安装 `nftables` 包，它提供了 [nft(8)](https://man.voidlinux.org/nft.8)。它还提供了 [iptables-translate(8)/ip6tables-translate(8)](https://man.voidlinux.org/iptables-translate.8) 和 [iptables-restore-translate(8)/ip6tables-restore-translate(8)](https://man.voidlinux.org/iptables-restore-translate.8)，它们将 `iptables` 规则转换成 `nftables` 规则。

### 在启动时应用规则

要在 runit stage 1应用 nftables 规则，请安装 `runit-nftables` 软件包。这增加了一个核心服务，它恢复了`/etc/nftables.conf` 中的规则集。

### 在运行时应用规则

`nftables` 软件包提供了 `nftables` 服务，它使用来自 `/etc/nftables.conf` 的规则。一旦你启用了 `nftables` 服务，要加载规则，请运行:


```
# sv up nftables
```

要刷新规则，请运行：

```
# sv down nftables
```
