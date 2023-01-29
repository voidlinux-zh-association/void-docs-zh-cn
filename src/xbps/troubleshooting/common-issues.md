# 常见问题

## 验证 RSA 密钥

如果 Void RSA 密钥已经改变，[xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 将报告新的密钥指纹，并要求你确认：

```
<repository> repository has been RSA signed by "Void Linux"
Fingerprint: <rsa_fingerprint>
Do you want to import this public key? [Y/n]
```

为了验证密钥，确保 `<rsa_fingerprint>` 与 [void-packages](https://github.com/void-linux/void-packages/tree/master/common/repo-keys) 和  [void-mklive](https://github.com/void-linux/void-mklive/tree/master/keys) 中的一个指纹相匹配。

## 更新或安装软件包时出现错误

如果在更新或安装新软件包时有任何错误，确保你使用的是最新版本的软件源索引。使用 `-S` 选项运行 [xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 可以保证这一点。

### "Operation not permitted"

"Operation not permitted" 的错误，例如：

```
ERROR: [reposync] failed to fetch file https://repo-default.voidlinux.org/current/nonfree/x86_64-repodata': Operation not permitted
```

可能是由于你的系统的日期和/或时间不正确造成的。确保你的 [日期和时间](../../config/date-time.md)  是正确的。


### "Not Found"

"Not Found" 的错误，例如：

```
ERROR: [reposync] failed to fetch file `https://repo-default.voidlinux.org/current/musl/x86_64-repodata': Not Found
```

通常意味着你的 XBPS 配置对你的系统来说指向了错误的存储库。确认你的 [xbps.d(5)](https://man.voidlinux.org/xbps.d.5) 文件指向了[正确的软件源](../repositories/index.md)。


### shlib 错误

"unresolvable shlib" 的错误, 例如:

```
libllvm8-8.0.1_2: broken, unresolvable shlib `libffi.so.6'
```

可能是由于过期或无主的软件包造成的。要检查过时的软件包，只需尝试[更新你的系统](../index.md#updating)。另一方面，"孤立" 包已经从 Void repos 中删除，但仍然安装在你的系统上；它们可以通过运行带有 `-o` 选项的 [xbps-remove(1)](https://man.voidlinux.org/xbps-remove.1) 来删除。

如果你得到一个错误信息说：

```
Transaction aborted due to unresolved shlibs
```

软件源处于暂存状态，这可能是由于大型构建而发生的。解决的办法是等待构建完成。你可以在 [Buildbot's Waterfall
Display](https://build.voidlinux.org/waterfall) 中查看构建的进度。

### repodata 错误

在 2020 年 3 月，用于软件源数据（ repodata ）的压缩格式从 gzip 改为 zstd。 如果 XBPS 没有更新到 `0.54` 版本（2019 年 6 月发布）或更新版本，就不可能用它来更新系统。不幸的是，这种情况没有错误信息，但可以通过运行带有 `-Sd` 标志的 `xbps-install` 来检测。这个错误的调试信息显示如下。

```
[DEBUG] [repo] `//var/db/xbps/https___repo-default_voidlinux_org_current/x86_64-repodata' failed to open repodata archive Invalid or incomplete multibyte or wide character
```

在这种情况下，有必要遵循 [xbps-static](./static.md) 中的步骤。

## 残疾系统

如果你的系统由于某种原因出现故障，无法进行更新或安装软件包，使用[静态链接版本的 xbps](./static.md)来更新和安装软件包可以帮助你避免重新安装整个系统。

