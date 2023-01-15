# 更多程序

## 编程语言

Void 仓库有许多 Python 和 Lua 软件包。如果可能的话，从 Void 仓库安装软件包，或者考虑打包你需要的库或应用程序。将你的应用程序打包可以使系统维护更容易，并能使其他 Void Linux 用户受益，所以考虑为它提出一个拉动请求。贡献说明可以在[这里](https://github.com/void-linux/void-packages/blob/master/CONTRIBUTING.md))找到。

为了使软件包更小，Void 为头文件和开发工具提供了单独的 `devel` 包。如果你通过一种语言的包管理器（如 `pip`，`gem`）安装一个库或应用程序，或从源代码编译一个库或应用程序，你可能需要安装编程语言的  `-devel` 包。这对 `musl libc` 用户来说特别重要，因为预建的二进制文件通常以  `glibc` 为目标。


| 编程语言 | 软件包管理器                | Void 软件包    |
|----------|--------------------------------|-----------------|
| Python3  | pip, anaconda, virtualenv, etc | `python3-devel` |
| Python2  | pip, anaconda, virtualenv, etc | `python2-devel` |
| Ruby     | gem                            | `ruby-devel`    |
| lua      | luarocks                       | `lua-devel`     |

## 受限制的软件

有些软件包在分发上有法律限制（例如 Discord），或者可能太大，或者有其他条件使 Void 难以分发。这些软件包有构建模板，但软件包本身没有被构建或分发。因此，它们必须在本地构建。更多信息请参见[受限制的软件包](../xbps/repositories/restricted.md)的页面。


## 非 x86_64 架构

Void 构建系统在 x86_64 服务器上运行，既用于编译也用于交叉编译软件包。然而，有些软件包（如 `libreoffice`）不支持交叉编译。这些软件包必须在运行与使用该软件包的系统相同架构和 libc 的计算机上进行本地构建。要了解如何构建软件包，请参考 [void-packages 仓库的 README 文件](https://github.com/void-linux/void-packages/blob/master/README.md)。

## Flatpak

Flatpak 是另一种在 Linux 上安装外部专有应用程序的方法。有关在 Void Linux 上使用 Flatpak 的信息，请参见[官方Flatpak 文档](https://flatpak.org/setup/Void%20Linux/)。

如果使用 Flatpak 安装的程序没有声音，[PulseAudio](./media/pulseaudio.md) 的自动激活功能可能没有正常工作。在启动程序之前，请确保 PulseAudio 正在运行。

请注意，Flatpak 的沙箱不一定能保护你免受专利软件的任何安全和/或侵犯隐私的功能的影响。

### 故障排除

一些应用程序可能无法正常运行（例如，无法访问主机系统的文件）。其中一些问题可以通过安装一个或多个 `xdg-desktop-portal`、`xdg-desktop-portal-gtk`、`xdg-user-dirs`、`xdg-user-dirs-gtk` 或 `xdg-utils` 软件包来解决。

一些 Flatpaks 需要 [D-Bus](./session-management.md#d-bus) 和/或者 [Pulseaudio](./media/pulseaudio.md)。


## AppImages

[AppImage](https://appimage.org/) 是一个文件，它将一个应用程序与运行它所需的一切捆绑在一起。一个 AppImage 可以通过使其可执行并运行来使用，不需要安装。AppImage 可以在沙盒中运行，如 [firejail](https://firejail.wordpress.com/)

在 [AppImageHub](https://appimage.github.io/) 上可以找到一些可以使用 AppImage 的应用程序。

AppImages 尚不适用于 musl 安装。 

## Octave 软件包

一些 Octave 软件包需要外部依赖才能编译和运行。例如，为了构建控制包，你必须安装 `openblas-devel`、`libgomp-devel`、`libgfortran-devel`、`gcc-fortran` 和 `gcc` 包。

## MATLAB

要使用 MATLAB 的 help browser、live 脚本、附加安装程序和 simulink，请安装 `libselinux` 包。

## Steam

Steam 可以通过本机包安装[需要启用 "非自由 "仓库](../xbps/repositories/index.md#nonfree)或 Flatpak 来安装。不同平台的依赖性列表和本地软件包的故障排除信息可以在其 [Void-specific文档](./package-documentation/index.html) 中找到，而这部分则是关于 Flatpak 用户面临的潜在问题。

如果你使用不同的驱动器来存储你的游戏库，[flatpak-override(1)](https://man.voidlinux.org/flatpak-override.1) 的 `--filesystem`` 选项可以证明是有用的。
