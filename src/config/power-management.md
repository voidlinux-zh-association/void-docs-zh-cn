# 电源管理

## acpid

[acpid(8)](https://man.voidlinux.org/acpid.8) 的 `acpid` 服务已经安装，如果Void是从使用 本地源 的 live 镜像中安装的，那么默认情况下会被启用。ACPI 事件由 `/etc/acpi/handler.sh` 处理，它使用 [zzz(8)](https://man.voidlinux.org/zzz.8) 来处理 suspend-to-RAM 事件.

## elogind

`elogind` 服务是由 `elogind` 软件包提供的。默认情况下，[elogind(8)](https://man.voidlinux.org/elogind.8) 监听并处理与 lid-switch 激活 以及*电源*、*暂停*和*休眠键* 有关的 ACPI 事件。如果 `acpid` 服务被安装并启用，这将与它发生冲突。要么在启用 elogind 时禁用 acpid ，要么在 [logind.conf(5)](https://man.voidlinux.org/logind.conf.5) 中把elogind 配置为忽略 ACPI 事件。有几个配置选项，都以关键字 `Handle` 开头，应该设置为忽略，以避免与acpid发生干扰。


## 省电 - tlp

笔记本电脑的电池寿命可以通过使用 [tlp(8)](https://man.voidlinux.org/tlp.8) 来延长。要使用它，请安装 `tlp` 软件包，并[启用](./services/index.md#enabling-services) `tlp` 服务。详情请参考[TLP文档](https://linrunner.de/tlp/)。
