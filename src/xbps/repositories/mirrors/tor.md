# 使用 Tor 镜像

Tor 是一个匿名软件，它通过世界各地的计算机弹出流量。它可以提供对互联网上常规网站的访问，或访问只有在网络上才有的隐藏网站。

下列 Void Linux 镜像在 Tor 网络上可用：

| Repository                                                                             | Location   |
|----------------------------------------------------------------------------------------|------------|
| <http://lysator7eknrfl47rlyxvgeamrv7ucefgrrlhk7rouv3sna25asetwid.onion/pub/voidlinux/> | EU: Sweden |

## 使用 Tor 的 XBPS

XBPS 可以使用 Tor 连接到镜像。这些镜像可以是正常的镜像，通过出口中继，或者，为了潜在的更大的匿名性，可以是网络上隐藏的服务器镜像。

XBPS 遵循 `SOCKS_PROXY` 环境变量，这使得它很容易通过 Tor 使用。

### 安装 Tor

Tor包含在 `tor` 软件包中。

在安装了Tor之后，你可以以你自己的用户身份启动它：

```
$ tor
```

或启用其系统服务。

默认情况下，Tor 将作为一个客户端，在本地主机的 TCP 端口 9050 上打开一个 SOCKS5 代理。

### 使 XBPS 通过 SOCKS 代理连接

XBPS 读取 `SOCKS_PROXY` 环境变量并将使用其中指定的任何代理。通过简单地将该变量设置为由 Tor 客户端打开的代理的地址和端口，所有 XBPS 的连接将通过Tor网络进行。

一个通过 Tor 升级系统的例子：

```
# export SOCKS_PROXY="socks5://127.0.0.1:9050"
# xbps-install -Su
```

### 使用一个隐藏的服务器镜像

为了使用一个隐藏的服务器镜像，默认的镜像需要用指向 `.onion`-addresses 的配置文件来覆盖，这些地址在 Tor 网络内部使用。XBPS 允许在 `/etc/xbps.d` 下重写存储库地址。

将你的软件源文件从 `/usr/share/xbps.d` 复制到 `/etc/xbps.d`，并用洋葱服务器的地址替换（以 Lysator 的洋葱为例）：

```
# mkdir -p /etc/xbps.d
# cp /usr/share/xbps.d/*-repository-*.conf /etc/xbps.d/
# sed -i 's|https://repo-default.voidlinux.org|http://lysator7eknrfl47rlyxvgeamrv7ucefgrrlhk7rouv3sna25asetwid.onion/pub/voidlinux|g' /etc/xbps.d/*-repository-*.conf
```

Tor 提供分层的端对端加密，因此不需要 HTTPS。

当安装软件包时，如果像前面的例子那样设置了 `SOCKS_PROXY`，XBPS 应该表明它正在从覆盖中指定的洋葱地址同步存储库：

```
# xbps-install -S
[*] Updating `http://lysator7eknrfl47rlyxvgeamrv7ucefgrrlhk7rouv3sna25asetwid.onion/pub/voidlinux/current/aarch64/nonfree/aarch64-repodata' ...
aarch64-repodata: 4030B [avg rate: 54KB/s]
[*] Updating `http://lysator7eknrfl47rlyxvgeamrv7ucefgrrlhk7rouv3sna25asetwid.onion/pub/voidlinux/current/aarch64/aarch64-repodata' ...
aarch64-repodata: 1441KB [avg rate: 773KB/s]
```

### 安全方面的的考虑

如果你使用 onion，建议在你的环境中自动设置 `SOCKS_PROXY`。如果缺少该设置，对隐藏服务器名称的 DNS 查询将泄露给配置的 DNS 服务器。

要自动设置环境变量，请将其添加到文件
`/etc/profile.d`:

```
# cat - <<EOF > /etc/profile.d/socksproxy.sh
#!/bin/sh
export SOCKS_PROXY="socks5://127.0.0.1:9050"
EOF
```
