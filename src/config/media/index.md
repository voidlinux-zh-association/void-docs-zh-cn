# 多媒体

## 音频设置

要在您的 Void Linux 系统上设置音频，您必须决定是否要使用 [PulseAudio](./pulseaudio.md) 、[PipeWire](./pipewire.md) 或者是 [ALSA](./alsa.md)。 

有些应用程序需要 PulseAudio ，特别是闭源程序，但 [PipeWire](./pipewire.md) 提供了 PulseAudio 的直接替代。

如果没有启用 [elogind](../session-management.md)，就必须在 `audio` 用户组中，以便能够访问音频设备。
