# 软件源

软件源是 XBPS 软件包系统的核心。软件源可以是本地或远程的。一个存储库包含二进制包文件，可能有签名，还有一个名为 `$ARCH-repodata` 的数据文件（例如 `x86_64-repodata`），也可能有签名。

注意，虽然本地软件源不需要签名，但远程软件源 **必须** 签名。

## 主软件源

主软件源与基本[镜像 URL](./mirrors/index.md) 相关的位置是：

- glibc: `/current`
- musl: `/current/musl`
- aarch64 和 aarch64-musl: `/current/aarch64`

## 子软件源

除了安装时启用的主软件源外，Void 还提供了其他由 Void 项目维护的官方软件源库，但在默认情况下没有启用：

- nonfree: 包含具有非自由许可证的软件包
- multilib: 包含用于64位系统的32位库（glibc 独有）。
- multilib/nonfree: 包含非自由的 multilib 软件包
- debug: 包含软件包调试的 symbols

这些软件源可以通过安装相关软件包来启用。这些软件包只在 `/usr/share/xbps.d` 中安装一个资源库配置文件。

### nonfree

Void 有一个 `nonfree` 仓库，用于存放非自由许可证的软件包。它可以通过安装 `void-repo-nonfree` 包来启用。

软件包会因为一些原因而出现在 `nonfree` 软件源中：

- 非自由许可的软件，有发布的源代码。
- 只作为可重新分发的二进制软件包发布的软件。
- 专利技术，可能有也可能没有（其他方面）开放的实施。

### multilib

`multilib` 源提供32位软件包作为64位系统的兼容层。它可以通过安装 `void-repo-multilib` 包来启用。

这些存储库只适用于运行 `glibc` C库的 `x86_64` 系统。

### multilib/nonfree

`multilib/nonfree` 软件源提供了额外的 32 位软件包，这些软件包具有非自由许可证。它可以通过安装 `void-repo-multilib-nonfree` 包来启用。

### debug

Void Linux 软件包没有调试 symbols。如果你想对软件进行调试或查看核心转储，你将需要调试 symbols。这些软件包包含在调试软件源中。它可以通过安装 `void-repo-debug` 包来启用。

Once enabled, symbols may be obtained for `<package>` by installing
`<package>-dbg`.

一旦启用，可以通过安装 `<package>-dbg` 来获得 `<package>` 的 symbols.

#### 查看调试的依赖关系

`xtools` 软件包包含 [xdbg(1)](https://man.voidlinux.org/xtools.1) 工具，用来检索一个软件包的调试包列表，包括依赖关系：

```
$ xdbg bash
bash-dbg
glibc-dbg
# xbps-install -S $(xdbg bash)
```
