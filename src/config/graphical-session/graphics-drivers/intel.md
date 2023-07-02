# Intel

英特尔 GPU 支持需要 `linux-firmware-intel` 软件包。如果你已经安装了 `linux` 或 `linux-lts` 软件包，它将作为一个依赖项被安装。如果你安装了一个特定版本的内核包（例如，linux5.4），可能需要手动安装`linux-firmware-intel`。

## OpenGL

OpenGL 需要 `mesa-dri`包。这是由 `xorg` 元包提供的，但在使用 `xorg-minimal` 包或运行 Wayland 合成器时，需要手动安装。

## Vulkan

安装 Khronos Vulkan Loader 和 Mesa Intel Vulkan 驱动包，分别是 `vulkan-loader` 和 `mesa-vulkan-intel`。

## 视频加速

安装 `intel-video-accel` 元包:

这将安装所有英特尔 VA-API 驱动，将默认使用 `intel-media-driver`，以下选择可以在运行时通过环境变量 `LIBVA_DRIVER_NAME` 覆盖。

| 驱动包      | 支持的 GPU Gen |   Explicit selection    |
|----------------------|-------------------|--------------------------|
| `libva-intel-driver` | up to Coffee Lake | `LIBVA_DRIVER_NAME=i965` |
| `intel-media-driver` | from Broadwell    | `LIBVA_DRIVER_NAME=iHD`  |

## 故障排除

Void 打包的内核被配置为 `CONFIG_INTEL_IOMMU_DEFAULT_ON=y`，这可能导致其图形驱动的问题，正如[内核文档](https://www.kernel.org/doc/html/latest/x86/iommu.html#graphics-problems)所报告的。为了解决这个问题，有必要禁用集成 GPU 的 IOMMU。这可以通过在你的[内核 cmdline](../../kernel.md#cmdline) 中添加 `intel_iommu=igfx_off`来完成。这个问题预计会发生在 Broadwell 一代的内部 GPU 上。如果你有另一个内部 GPU，而你的问题被这个内核选项修复了，你应该提交一个 bug，向内核开发者报告这个问题。

对于较新的英特尔芯片组，[DDX](../xorg.md#ddx) 驱动程序可能会干扰正确的操作。表现为图形加速不工作和一般图形不稳定。如果是这种情况，请尝试删除所有 `xf86-video-*` 软件包。

