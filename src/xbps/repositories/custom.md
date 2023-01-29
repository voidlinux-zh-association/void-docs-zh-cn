# 自定义软件源

Void 支持用户创建的本地和远程软件源。 这只是推荐用于提供个人创建的自定义包或来自另一个值得信赖的来源。 Void 项目不支持 **任何** 第三方软件源 - 第三方软件包的使用构成非常严重的安全问题，并有可能严重损坏您的系统。 

## 添加自定义软件源

要添加自定义软件源，请在中 `/etc/xbps.d` 创建一个文件 ，内容为：

```
repository=<URL>
```

其中 `<URL>` 是一个本地目录或一个指向远程软件源的 URL。

例如，要定义一个远程软件源：

```
# echo 'repository=http://my.domain.com/repo' > /etc/xbps.d/my-remote-repo.conf
```

[xbps-install(1)](https://man.voidlinux.org/xbps-install.1) 拒绝安装来自远程软件源的软件包，如果它们没有被[签名](./signing.md)。

要定义本地存储库： 

```
# echo 'repository=/path/to/repo' > /etc/xbps.d/my-local-repo.conf
```
