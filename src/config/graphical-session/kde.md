# KDE

## 安装

安装 `kde5` 包，而且可以选择， `kde5-baseapps`软件包来安装附加软件。 

要使用 “Networks” 小部件，请启用 `dbus` 和 `NetworkManager` 服务。 

安装 `kde5` 软件包的同时也安装了 `sddm` 软件包，它为简单桌面显示管理器提供了 `sddm` 服务。这个服务依赖于 `dbus` 服务的[启用](../services/index.md#testing-services)；在启用之前要测试该服务。你将需要安装 `xorg-minimal` 包或 `xorg` 包。默认情况下，SDDM 会启动一个基于 X 的 Plasma 会话，但你可以请求一个基于 Wayland 的 Plasma 会话。

If you wish to start an X-based session from the console, use
[startx](./xorg.md#startx) to run `startplasma-x11`. For a Wayland-based
session, run `startplasma-wayland` directly.

如果你想从 控制台 启动一个基于 X 的会话，使用 [startx](./xorg.md#startx) 来运行 `startplasma-x11`。如果是基于 Wayland 的会话，直接运行`startplasma-wayland`。

## Dolphin

Dolphin 是 KDE 桌面环境的默认文件管理器。 自行安装 `dolphin` 包，或者可以安装作为的一部分 `kde5-baseapps` 元包。

### 缩略图预览

要启用缩略图文件预览，请安装 `kdegraphics-thumbnailers` 软件包。如果你想要视频缩略图，`ffmpegthumbs` 包也是必要的。在 "Control" -> "Configure Dolphin" -> "General" ->
"Previews" 中勾选相应的方框来启用预览。点击 Dolphin 工具栏上的 " Previews "后，所选文件类型的文件预览将被显示。
