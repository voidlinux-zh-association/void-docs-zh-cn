# GnuPG

Void 同时提供了GnuPG legacy 版（作为 `gnupg1` ）和 GnuPG 稳定版（作为 `gnupg`）。

## 智能卡

对于用 GnuPG 使用 Yubikeys 等智能卡，有两个后端可以通过 GnuPG 与它们进行通信：GnuPG 的 scdaemon 的内部 CCID 驱动，或 PC/SC 驱动。

## 带有内部 CCID 驱动的 scdaemon

By default, scdaemon, which is required for using smartcards with GnuPG, uses
its internal CCID driver. For this to work, your smartcard needs to be one of
the smartcards in the udev rules
[here](https://github.com/void-linux/void-packages/blob/master/srcpkgs/gnupg/files/60-scdaemon.rules)
and you need to either be using elogind or be a member of the plugdev group. If
these two condition are fulfilled and you don't have pcscd running, `gpg
--card-status` should successfully print your current card status.

## scdaemon with pcscd backend

If you need to use pcscd for other reasons, run `echo disable-ccid >>
~/.gnupg/scdaemon.conf`. Now, assuming your pcscd setup works correctly, `gpg
--card-status` should print your card status.

# OpenPGP Card Tools

As an alternative to GnuPG with smartcards, Void also ships
`openpgp-card-tools`, a Rust based utility not reliant on GnuPG. It requires
using `pcscd` for interacting with smart cards, so if you want to use it in
parallel with GnuPG, ou need to configure `scdaemon` to use the pcscd backend,
as described above in "[scdaemon with pcscd
backend](#scdaemon-with-pcscd-backend)".
