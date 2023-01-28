# sndio

安装 `sndio` 软件包并启用 [sndiod(8)](https://man.voidlinux.org/sndiod.8) 服务。

## 配置

服务可以通过向配置文件 (`/etc/sv/sndiod/conf`) 的变量 `OPTS` 中添加 [sndiod(8)](https://man.voidlinux.org/sndiod.8) 标志来配置。 

### 默认设备

[sndiod(8)](https://man.voidlinux.org/sndiod.8) 默认使用第一个 alsa 设备。欲将另外的 alsa 设备设为默认的 `snd/0` ，向配置文件中添加标志：

```
# echo 'OPTS="-f rsnd/Speaker"' >/etc/sv/sndiod/conf
```

使用 `-f` 标志，通过 ALSA 设备索引或 ALSA 设备名称选择一个设备。

## 音量控制

主控和每个应用程序的音量控制是通过硬件或软件的 MIDI 信息来控制的。

[aucatctl(1)](https://man.voidlinux.org/aucatctl.1) 是一个专门针对 sndio 的工具，用来向 [sndiod(8)](https://man.voidlinux.org/sndiod.8) 守护程序发送 MID I控制信息。它可以在 `aucatctl` 软件包中找到。

## 特定的应用配置

### Firefox

火狐浏览器在构建时支持 sndio，如果安装了 libsndio 并且有 `snd/0` 设备，那么从 71 版开始就应该可以使用。

以下 `about:config` 的修改对于 71 之前的版本是必须的，当使用 71 或更高版本时，应该被删除:

```
media.cubeb.backend;sndio
media.cubeb.sandbox;false
security.sandbox.content.read_path_whitelist;/home/<username>/.sndio/cookie
security.sandbox.content.write_path_whitelist;/home/<username>/.sndio/cookie
```

### OpenAL

libopenal 支持 sndio，但默认情况下 ALSA 优先于 sndio。你可以在 `~/.alsoftrc` 中为每个用户配置这一行为，也可以在 `/etc/openal/alsoft.conf` 中通过添加以下几行来为整个系统配置：

```
[general]
drivers = sndio
```
