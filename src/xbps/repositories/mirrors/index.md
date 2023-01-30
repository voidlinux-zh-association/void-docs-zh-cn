# 镜像源

Void Linux 在几个地理区域都有镜像供你使用。一个新的安装将默认使用欧洲的主镜像，但你也可以 [选择一个不同的镜像](./changing.md)


## 一级镜像

第 1 级镜像由 Void Linux 基础设施团队维护。这些镜像直接从构建主站同步，并且总是有最新的软件包。

默认情况下，XBPS 将联系 <https://repo-default.voidlinux.org> ，它可以映射到任何一级镜像。

<!-- Order alphabetically by: (1) Region, (2) Country, (3) City/State/Province/etc, (4) URL -->

| Repository                                     | Region        | Location           |
|------------------------------------------------|---------------|--------------------|
| <https://repo-fi.voidlinux.org/>               | Europe        | Helsinki, Finland  |
| <https://repo-de.voidlinux.org/>               | Europe        | Frankfurt, Germany |
| <https://mirrors.servercentral.com/voidlinux/> | North America | Chicago, USA       |
| <https://repo-us.voidlinux.org/>               | North America | Kansas City, USA   |


## 二级镜像

在可能的情况下，第2级镜像从附近的第1级镜像同步。这些镜像不是由 Void 管理的，也不保证软件包的新度和完整性,也不要求它们同步每个可用的架构或子库。


### 全球镜像

<!-- Order alphabetically by: (1) Region, (2) Country, (3) City/State/Province/etc, (4) URL -->

| Repository                                         | Region        | Location               |
|----------------------------------------------------|---------------|------------------------|
| <https://mirror.ps.kz/voidlinux/>                  | Asia          | Almaty, Kazakhstan     |
| <https://mirror.nju.edu.cn/voidlinux/>             | Asia          | China                  |
| <https://mirrors.bfsu.edu.cn/voidlinux/>           | Asia          | Beijing, China         |
| <https://mirrors.cnnic.cn/voidlinux/>              | Asia          | Beijing, China         |
| <https://mirrors.tuna.tsinghua.edu.cn/voidlinux/>  | Asia          | Beijing, China         |
| <https://mirror.sjtu.edu.cn/voidlinux/>            | Asia          | Shanghai, China        |
| <https://void.webconverger.org/>                   | Asia          | Singapore              |
| <http://ftp.dk.xemacs.org/voidlinux/>              | Europe        | Denmark                |
| <https://mirrors.dotsrc.org/voidlinux/>            | Europe        | Denmark                |
| <https://quantum-mirror.hu/mirrors/pub/voidlinux/> | Europe        | Hungary                |
| <https://voidlinux.mirror.garr.it/>                | Europe        | Italy                  |
| <https://void.cijber.net/>                         | Europe        | Amsterdam, Netherlands |
| <https://void.sakamoto.pl/>                        | Europe        | Warsaw, Poland         |
| <http://ftp.debian.ru/mirrors/voidlinux/>          | Europe        | Russia                 |
| <https://mirror.yandex.ru/mirrors/voidlinux/>      | Europe        | Russia                 |
| <https://ftp.lysator.liu.se/pub/voidlinux/>        | Europe        | Sweden                 |
| <https://mirror.accum.se/mirror/voidlinux/>        | Europe        | Sweden                 |
| <https://mirror.puzzle.ch/voidlinux/>              | Europe        | Bern, Switzerland      |
| <https://mirror.vofr.net/voidlinux/>               | North America | California, USA        |
| <https://mirror2.sandyriver.net/pub/voidlinux/>    | North America | Kentucky, USA          |
| <https://mirror.clarkson.edu/voidlinux/>           | North America | New York, USA          |
| <https://mirror.aarnet.edu.au/pub/voidlinux/>      | Oceania       | Canberra, Australia    |
| <https://ftp.swin.edu.au/voidlinux/>               | Oceania       | Melbourne, Australia   |
| <https://voidlinux.com.br/repo/>                   | South America | Ouro Preto, Brazil     |
| <http://void.chililinux.com/voidlinux/>            | South America | Pimenta Bueno, Brazil  |

## Tor 镜像

Void Linux 也在 Tor 网络上做了镜像。更多信息请参见使用 [Tor 镜像](./tor.md)。

## 创建镜像

如果你想建立一个镜像，并且确信你能保持它的合理更新，请遵循许多关于用 [rsync(1)](https://man.voidlinux.org/rsync.1),做镜像的指南之一，然后向 [void-docs 仓库](https://github.com/void-linux/void-docs) 提交一个请求，将你的镜像添加到本页的相应镜像表中。

一个完整的镜像需要大约 1TB 的存储空间。也可以只镜像部分软件库。不包括调试包是减少一级镜像负载的一种方法，对用户的影响很小。

请记住，我们为所有从一级镜像发出的数据支付带宽。您可以尊重这一点，只有当您的镜像的使用情况能够抵消镜像同步所消耗的网络吞吐量时，才会进行镜像。
