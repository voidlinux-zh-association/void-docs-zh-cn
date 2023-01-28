# NVIDIA

## nouveau (开源驱动)

这是一个主要由社区开发的逆向工程驱动，由 Nvidia 提供一些文档。它往往在旧硬件上表现良好，并且大部分 Wayland 合成器可以用。

在写这篇文章的时候，从第二代 Maxwell（GTX 9xx）开始的显卡无法用 `nouveau` 发挥其全部潜力。这是因为 `linux-firmware` 集合中缺少重新锁定这些显卡其启动频率所需的签名固件块。

要在 Wayland 上使用 `nouveau`，你只需要 `mesa-dri` 包，它提供了 OpenGL 驱动。在 X11 上，你还需要一个合适的 Xorg 驱动。你可以安装 `xf86-video-nouveau` 或者使用与 Xorg 捆绑的通用模式设置驱动程序（这是基于 Tegra 的 ARM 板的唯一选择）。前者可以利用 GPU 特定的 2D 加速，这在具有专门的固定功能硬件的旧卡上主要是有用的（模式化驱动程序将通过 GLAMOR 使用 OpenGL 加速 2D）。当有疑问时，首先尝试 `xf86-video-nouveau` 是个好主意。

注意: 如果你使用 `xorg` 元包，`xf86-video-nouveau` 通常会默认安装。如果你使用 `xorg-minimal`，你将需要直接或通过 `xorg-video-drivers` 手动安装它。


## nvidia (专有驱动程序)

专有的驱动程序可以在[非自由软件库](../../../xbps/repositories/index.md#nonfree)中找到。

检查你的显卡是否属于 [legacy 分支](https://www.nvidia.com/en-us/drivers/unix/legacy-gpu/)。如果不是，请安装 `nvidia` 软件包。否则，你应该安装 legacy 驱动程序，即 `nvidia470` 或 `nvidia390` 。较早的传统驱动程序 `nvidia340` 已不再可用，我们鼓励用户切换到 `nouveau`。


| Brand  | Type        | Model          | Driver Package |
|--------|-------------|----------------|----------------|
| NVIDIA | Proprietary | 800+           | `nvidia`       |
| NVIDIA | Proprietary | 600/700        | `nvidia470`    |
| NVIDIA | Proprietary | 400/500 Series | `nvidia390`    |

专有驱动程序通过 [DKMS](../../kernel.md#dynamic-kernel-module-support-dkms) 整合到内核中。

该驱动提供更好的性能和功率处理，建议在需要性能的地方使用

## 支持 32 位的驱动程序 (glibc 独有)

为了运行支持驱动的32位程序，你需要安装额外的软件包。

如果使用 `nouveau` 驱动，请安装 `mesa-dri-32bit` 软件包。

如果使用 `nvidia` 驱动，请安装 `nvidia<x>-libs-32bit` 软件包。`<x>` 代表传统驱动程序的版本（`470` 或 `390`），或者可以留空，代表主要驱动程序。
    

## 从 nvidia 切换到 nouveau

### 卸载 nvidia

为了切换到 `nouveau` 驱动程序，请安装 `nouveau` 驱动程序（如果尚未安装），然后根据情况删除 `nvidia`、`nvidia470` 或 `nvidia390` 包。

如果你使用的是过时的 `nvidia340` 驱动，你可能需要在删除 `nvidia340` 包之后再安装 `libglvnd` 包。


### 保留两个驱动程序

It is possible to use the `nouveau` driver while still having the `nvidia`
driver installed. To do so, remove the blacklisting of `nouveau` in
`/etc/modprobe.d/nouveau_blacklist.conf`, `/usr/lib/modprobe.d/nvidia.conf`, or
`/usr/lib/modprobe.d/nvidia-dkms.conf` by commenting it out:

可以使用 `nouveau` 驱动同时还有 `nvidia` 驱动程序。 为此，请删除 `nouveau` 在 `/etc/modprobe.d/nouveau_blacklist.conf` , `/usr/lib/modprobe.d/nvidia.conf`， 或者 `/usr/lib/modprobe.d/nvidia-dkms.conf` 的黑名单注释掉： 

```
#blacklist nouveau
```

对于 Xorg，通过创建包含以下内容的文件 `/etc/X11/xorg.conf.d/20-nouveau.conf`，指定它应该加载 `nouveau`驱动而不是 nvidia 驱动：

```
Section "Device"
    Identifier "Nvidia card"
    Driver "nouveau"
EndSection
```

您可能需要重新启动系统才能使这些更改生效。 
