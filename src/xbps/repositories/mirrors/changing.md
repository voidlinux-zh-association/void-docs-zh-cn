# 更换镜像

每个软件库都有一个文件定义了所使用的镜像的 URL 。对于官方软件库，这些文件由软件包管理器安装在 `/usr/share/xbps.d` 中，但如果在 `/etc/xbps.d` 中发现重复的文件，就会使用这些值来代替。

为了干净利落地修改镜像URL，把所有版本库的配置文件复制到 `/etc/xbps.d`，并修改每个复制的版本库文件中的 URL。

```
# mkdir -p /etc/xbps.d
# cp /usr/share/xbps.d/*-repository-*.conf /etc/xbps.d/
# sed -i 's|https://repo-default.voidlinux.org|<repository>|g' /etc/xbps.d/*-repository-*.conf
```

After changing the URLs, you must synchronize xbps with the new mirrors:

```
# xbps-install -S
```

You should see the new repository URLs while synchronizing. You can also use
`xbps-query` to verify the repository URLs, but only after they have been
synchronized:

```
$ xbps-query -L
 9970 https://repo-default.voidlinux.org/current (RSA signed)
   27 https://repo-default.voidlinux.org/current/multilib/nonfree (RSA signed)
 4230 https://repo-default.voidlinux.org/current/multilib (RSA signed)
   47 https://repo-default.voidlinux.org/current/nonfree (RSA signed)
 5368 https://repo-default.voidlinux.org/current/debug (RSA signed)
```

Remember that repositories added afterwards will also need to be changed, or
they will use the default mirror.
