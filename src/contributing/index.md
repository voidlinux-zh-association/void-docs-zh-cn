# 贡献

运行一个发行版，不仅仅是写代码。

要为Void软件包库做贡献，首先要阅读 void-packages GitHub 仓库中的 [CONTRIBUTING](https://github.com/void-linux/void-packages/blob/master/CONTRIBUTING.md) 文档。

要为本手册做出贡献，请阅读 void-docs 仓库中的 [CONTRIBUTING](https://github.com/void-linux/void-docs/blob/master/CONTRIBUTING.md)。

如果你有任何问题，欢迎通过 IRC 在 irc.libera.chat 的 #voidlinux 中提问，或者在 [the voidlinux subreddit](https://reddit.com/r/voidlinux) 中提问。

## 使用情况统计

如果你想提供使用报告，[PopCorn](https://github.com/the-maldridge/popcorn)  程序会向 Void 项目报告安装统计数据。这些统计数据是 opt-in --PopCorn 在任何 Void 系统上都*没有*安装或默认启用。此外， PopCorn 要求在您的系统上不封锁 8001 端口。

*PopCorn* 只报告哪些软件包被安装，它们的版本，以及主机 CPU 架构（`xuname` 的输出）。这并不报告哪些服务被启用，或任何其他个人信息。单个系统通过一个随机的（客户生成的）UUID 被持续跟踪，以确保每个系统在每个 24 小时的采样期只被计算一次。

收集的数据 *PopCorn* 可在以下位置查看
<http://popcorn.voidlinux.org>

### 设置 PopCorn

首先，安装 `PopCorn` 软件包。然后，启用 `popcorn` 服务，它将尝试每天报告一次统计数据。
