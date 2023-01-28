# NVIDIA Optimus

NVIDIA Optimus 指的是笔记本电脑上的双图形配置，包括一个英特尔集成显卡和一个 NVIDIA 独立显卡。

有不同的方法来利用 NVIDIA GPU，这取决于你的硬件所支持的驱动版本。

为了确定要安装的正确驱动程序，只看 NVIDIA 网站上的 "支持的产品" 列表是不够的，因为它们不能保证能在 Optimus 配置中工作。因此，唯一的办法是尝试安装最新的 `nvidia`，重新启动，并查看内核日志。如果你的设备不被支持，你会看到这样的信息:

```
NVRM: The NVIDIA GPU xxxx:xx:xx.x (PCI ID: xxxx:xxxx)
NVRM: installed in this system is not supported by the xxx.xx
NVRM: NVIDIA Linux driver release.  Please see 'Appendix
NVRM: A - Supported NVIDIA GPU Products' in this release's
NVRM: README, available on the Linux driver download page
NVRM: at www.nvidia.com.
```

这意味着你必须卸载 `nvidia` 并安装传统的 `nvidia390`。

Void 支持的方法的摘要，这些方法是相互排斥的。

[PRIME Render Offload](#prime-render-offload)

- 只适用于 `NVIDIA`
- 允许在每个应用的基础上切换到英伟达 GPU。
- 更加灵活，但省电能力取决于硬件（pre-Turing 之前的设备不会完全关闭）。
- 
Offloading Graphics Display with RandR 1.4

- 可在 `nvidia` 和 `nvidia390` 上使用
- 允许选择在 X session 开始时使用哪个 GPU
- 灵活性较差，但允许用户在不使用 NVIDIA GPU 时完全关闭，从而节省电力

[Bumblebee](#bumblebee)

- 可在 `nvidia` 和 `nvidia390` 上使用
- 允许在每个应用的基础上切换到英伟达 GPU
- 非官方的方法，性能不佳

[Nouveau PRIME](#nouveau-prime)

- 使用开源的驱动程序 `nouveau`
- 允许在每个应用的基础上切换到英伟达GPU
- `nouveau` 是一个反向工程的驱动程序，性能很差。

你可以通过在 `glxinfo` 命令的输出中搜索渲染器字符串来检查当前使用的 GPU。为此，有必要安装 `glxinfo` 软件包。对于下面的前两个方案，也可以通过检查 `nvidia-smi` 的输出来验证一个进程是否在使用 NVIDIA GPU。

## PRIME Render Offload

在这种方法中，在执行要在 NVIDIA GPU 上渲染的应用程序时，通过设置环境变量来完成 GPU 切换。封装脚本 `prime-run` 可从 `nvidia` 软件包中获得，可以如下所示使用。

```
$ prime-run <application>
```

了解更多信息，请参见 NVIDIA 的
[README](https://download.nvidia.com/XFree86/Linux-x86_64/440.44/README/primerenderoffload.html)

## Bumblebee

启用 `bumblebeed` 服务并将用户添加到 `bumblebee` 组。这需要重新登录才能生效。

用 `optirun` 在 NVIDIA GPU 上运行要渲染的应用程序。

```
$ optirun <application>
```

## Nouveau PRIME


这种方法使用开源的 `nouveau` 驱动程序。如果安装了NVIDIA驱动程序，就有必要对[系统进行配置以使用`nouveau`](./nvidia.md#nvidia-切换到-nouveau)。

设置 `DRI_PRIME=1` 以在 NVIDIA GPU 上运行一个应用程序。

```
$ DRI_PRIME=1 <application>
```
