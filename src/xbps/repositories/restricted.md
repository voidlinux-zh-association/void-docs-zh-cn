# 受限制的软件包

Void 提供了一些官方维护的软件包，但没有分发。这些包被标记为有限制性的，必须从它们的 [void-packages](https://github.com/void-linux/void-packages) 模板本地构建。

软件包可以被上游作者或 Void 限制发布。Void 保留限制任何软件包发布的权利，原因不限，最常见的是体积庞大。另一个常见的原因是限制性许可，不允许第三方重新分发源代码或二进制包。

## 手动构建

你可以使用 [void-packages](https://github.com/void-linux/void-packages) 仓库中的 `xbps-src` 来从模板构建受限制的包。关于从模板构建软件包的说明，请参考 [void-packages](https://github.com/void-linux/void-packages) 文档，特别是 ["Quick start"
section](https://github.com/void-linux/void-packages#quick-start) 部分。

记住，必须通过在 `xbps-src` 配置中设置 `XBPS_ALLOW_RESTRICTED=yes` 来明确启用受限软件包的构建（在版本库的`etc/conf` 文件中）。

## 自动构建

还有一个工具，[xbps-mini-builder](https://github.com/the-maldridge/xbps-mini-builder)，它可以自动建立一个软件包列表的过程。这个脚本可以被定期调用，并且只在软件包的模板发生变化时才重新构建。
