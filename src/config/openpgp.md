# GnuPG

Void 同时提供了GnuPG legacy 版（作为 `gnupg1` ）和 GnuPG 稳定版（作为 `gnupg`）。

## 智能卡

对于用 GnuPG 使用 Yubikeys 等智能卡，有两个后端可以通过 GnuPG 与它们进行通信：GnuPG 的 scdaemon 的内部 CCID 驱动，或 PC/SC 驱动。

## 带有内部 CCID 驱动的 scdaemon

默认情况下，scdaemon（在 GnuPG 中使用智能卡时需要）使用其内部的 CCID 驱动。为了让它工作，你的智能卡需要是[这里](https://github.com/void-linux/void-packages/blob/master/srcpkgs/gnupg/files/60-scdaemon.rules)的 udev 规则中的智能卡之一，并且你需要使用 elogind 或成为 plugdev 组的成员。如果这两个条件都满足，而且你没有运行 pcscd，`gpg --card-status` 应该能成功地打印你当前的卡的状态。


##  带有 pcscd 后端的 scdaemon

如果你因为其他原因需要使用 pcscd，运行 `echo disable-ccid >> ~/.gnupg/scdaemon.conf`。现在，假设你的 pcscd 设置工作正常，`gpg --card-status` 应该会打印你的卡片状态。

# OpenPGP card tools

作为 GnuPG 和智能卡的替代品，Void 还提供了 `openpgp-card-tools`，这是一个基于Rust的工具，不依赖 GnuPG。它需要使用 `pcscd` 来与智能卡交互，所以如果你想与 GnuPG 并行使用它，你需要配置 `scdaemon` 来使用 pcscd 后台，如上文 "带有 pcscd 后端的 scdaemon" 所述。
