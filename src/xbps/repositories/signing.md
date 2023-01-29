# 把软件源签名

对远程软件源 **必须** 进行签名。 本地存储库不需要签名。

[xbps-rindex(1)](https://man.voidlinux.org/xbps-rindex.1) 工具被用来签名软件源。

用于签名软件包的私钥需要是一个 PEM 编码的 RSA 密钥。该密钥可以用 [ssh-keygen(1)](https://man.voidlinux.org/ssh-keygen.1) 或 [openssl(1)](https://man.voidlinux.org/openssl.1) 来生成：

```
$ ssh-keygen -t rsa -m PEM -f private.pem
```

```
$ openssl genrsa -out private.pem
```

一旦密钥生成，私钥的公开部分就必须被添加到软件源元数据中。这个步骤只需要一次。

```
$ xbps-rindex --privkey private.pem --sign --signedby "I'm Groot" /path/to/repository/dir
```

然后用以下命令签名一个或多个软件包:

```
$ xbps-rindex --privkey private.pem --sign-pkg /path/to/repository/dir/*.xbps
```

请注意，未来的软件包将不会被自动签名。
