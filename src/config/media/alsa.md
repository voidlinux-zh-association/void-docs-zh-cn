# ALSA

要使用 ALSA，请安装 `alsa-utils` 软件包，并确保你的用户是 `audio` 用户组的成员。

`alsa-utils` 软件包提供了 `alsa` 服务。启用后，该服务在关机和启动时分别保存和恢复ALSA的状态。

为了允许使用需要 PulseAudio 的软件，安装 `apulse` 包。`apulse` 提供了应用程序所期望的 PulseAudio 接口的一部分，将对该接口的调用转化为对 ALSA 的调用。关于使用 `apulse` 的细节，请查阅[项目的README](https://github.com/i-rinat/apulse/blob/master/README.md)。


## 配置

默认声卡可以通过 ALSA 配置文件或内核模块选项来指定。

要获得关于加载声卡模块的顺序的信息：

```
$ cat /proc/asound/modules
 0 snd_hda_intel
 1 snd_hda_intel
 2 snd_usb_audio
```

要将不同的卡设置为默认，请编辑 `/etc/asound.conf` 或每个用户的配置文件 `~/.asoundrc`:


```
defaults.ctl.card 2;
defaults.pcm.card 2;
```

或在 `/etc/modprobe.d/alsa.conf` 中指定声卡模块顺序：

```
options snd_usb_audio index=0
```

## Dmix

`dmix` ALSA 插件允许从多个来源播放声音。对于不支持硬件混音的声卡，默认情况下启用 `dmix`。要为数字输出启用它，请编辑 `/etc/asound.conf`:

```
pcm.dsp {
    type plug
    slave.pcm "dmix"
}
```
