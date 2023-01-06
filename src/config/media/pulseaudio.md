# PulseAudio

根据您使用的应用程序,您可能需要为 PulseAudio 提供 D-BUS session bus（例如通过 dbus-run-session）或 D-BUS system bus（通过 dbus 服务）。

对于直接使用 ALSA 而不支持 PulseAudio 的应用程序， `alsa-plugins-pulseaudio` 包可以使它们通过 ALSA 使用 PulseAudio。

PulseAudio 软件包带有服务文件，这在大多数设置中是不必要的,PulseAudio维护者[不鼓励](https://www.freedesktop.org/wiki/Software/PulseAudio/Documentation/User/SystemWide/)使用全系统的设置。相反，PulseAudio 会在需要时自动启动。如果它没有自动启动，可以通过在终端调用 [pulseaudio(1)](https://man.voidlinux.org/pulseaudio.1) 来手动启动它，方法如下。

```
$ pulseaudio --daemonize=no --exit-idle-time=-1
```

另一方面，PulseAudio 也可能在不需要的时候被自动激活。为了进展这种行为，可以将 [pulse-client.conf(5)](https://man.voidlinux.org/pulse-client.conf.5) 中的 `autospawn` 指令设置为 `no`。

有几种方法可以让 PulseAudio 访问音频设备。最简单的方法是把你的用户加入到 "audio" 组。另外，你可以使用一个 session 管理器，如 `elogind`。
