# 更换镜像

每个软件库都有一个文件定义了所使用的镜像的 URL 。对于官方软件库，这些文件由软件包管理器安装在 `/usr/share/xbps.d` 中，但如果在 `/etc/xbps.d` 中发现重复的文件，就会使用这些值来代替。

## xmirror

为了方便修改当前选择的镜像，可以使用 [xmirror(1)](https://man.voidlinux.org/xmirror.1)（来自 `xmirror` 软件包）。这个工具会处理所有更新所选镜像的步骤。

## 手动方式

另外，这也可以手动完成：

为了干净利落地修改镜像URL，把所有版本库的配置文件复制到 `/etc/xbps.d`，并修改每个复制的版本库文件中的 URL。

```
# mkdir -p /etc/xbps.d
# cp /usr/share/xbps.d/*-repository-*.conf /etc/xbps.d/
# sed -i 's|https://repo-default.voidlinux.org|<repository>|g' /etc/xbps.d/*-repository-*.conf
```

在更改 URL 后，你必须将 xbps 与新的镜像同步：

```
# xbps-install -S
```

您应该在同步时看到新的存储库 URL。 您也可以使用 `xbps-query` 验证存储库 URL，但只有在它们被验证之后同步： 

```
$ xbps-query -L
 9970 https://repo-default.voidlinux.org/current (RSA signed)
   27 https://repo-default.voidlinux.org/current/multilib/nonfree (RSA signed)
 4230 https://repo-default.voidlinux.org/current/multilib (RSA signed)
   47 https://repo-default.voidlinux.org/current/nonfree (RSA signed)
 5368 https://repo-default.voidlinux.org/current/debug (RSA signed)
```

请记住，之后添加的存储库也需要改变，否则它们将使用默认的镜像。
