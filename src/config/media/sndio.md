# sndio

安装 `sndio` 软件包并启用 [sndiod(8)](https://man.voidlinux.org/sndiod.8) 服务。

## 配置

服务可以通过向配置文件 (`/etc/sv/sndiod/conf`) 的变量 `OPTS` 中添加 [sndiod(8)](https://man.voidlinux.org/sndiod.8) 标志来配置。 

### 默认设备

[sndiod(8)](https://man.voidlinux.org/sndiod.8) 默认使用第一个alsa设备。欲将另外的alsa设备设为默认的 `snd/0` ，向配置文件中添加标志：

```
# echo 'OPTS="-f rsnd/Speaker"' >/etc/sv/sndiod/conf
```

使用 `-f` Use the `-f` flag to chooses a device by its ALSA device index or its ALSA
device name.

## Volume control

The master and per application volume controls are controlled with MIDI messages
by hardware or software.

[aucatctl(1)](https://man.voidlinux.org/aucatctl.1) is a tool specific to sndio
to send MIDI control messages to the
[sndiod(8)](https://man.voidlinux.org/sndiod.8) daemon. It can be found in the
`aucatctl` package.

## Application specific configurations

### Firefox

Firefox is built with sndio support and should work out of the box since version
71 if libsndio is installed and the `snd/0` device is available.

The following `about:config` changes are required for versions prior to 71 and
should be removed when using version 71 or later:

```
media.cubeb.backend;sndio
media.cubeb.sandbox;false
security.sandbox.content.read_path_whitelist;/home/<username>/.sndio/cookie
security.sandbox.content.write_path_whitelist;/home/<username>/.sndio/cookie
```

### OpenAL

libopenal comes with sndio support, but prioritizes ALSA over sndio by default.
You can configure this behavior per user in `~/.alsoftrc` or system wide in
`/etc/openal/alsoft.conf` by adding the following lines:

```
[general]
drivers = sndio
```
