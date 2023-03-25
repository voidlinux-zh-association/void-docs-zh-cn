# Live 安装程序

Void 提供的 live 安装器镜像包括一组基础工具、安装器程序和安装全新 Void 系统需要的软件包。Live 镜像可以用来修复无法引导或正常运行的系统。

`glibc` 和 `musl` 都有 `x86_64` 镜像，但只有 `glibc` 支持 `i686` 架构。Live 安装程序不支持其他架构，其他架构的用户需要用 rootfs 压缩包安装，或者手动安装。

## 安装程序镜像

Void 发布两种镜像：base 镜像和 xfce 桌面镜像，推荐 Linux 新手尝试功能更完善的 xfce 桌面镜像。高手们往往会倾向于用 base 镜像安装系统，只安装他们需要的软件包。

### base 镜像

base 镜像提供了能用来安装 Void 系统的，最小化的软件包集合。这些基础软件包仅够用来配置新机器、升级系统和从仓库里安装其他软件包。

### Xfce 镜像

xfce 桌面镜像包括完整的桌面环境、Web 浏览器和自带的基本工具。 与 base 镜像的唯一区别是它增加了软件包和服务。 

包括一下软件:

- **窗口管理器:** xfwm4
- **文件管理器:** Thunar
- **Web 浏览器:** Firefox ESR
- **终端:** xfce4-terminal
- **文本编辑器:** Mousepad
- **图片查看器:** Ristretto
- **其他:** Bulk rename, Orage Globaltime, Orage Calendar, Task Manager, Parole
   Media Player, Audio Mixer, MIME type editor, Application finder
 
xfce 映像的安装过程与 base 映像基本相同，除了在安装时你必须选择 `Local` 作为软件源。 若你选择了 `Network` 源，安装程序将下载并安装最新版本的 base 系统，live 镜像中将不包含任何其他软件包。
