# TeX Live

在 Void 中，`texlive-bin` 包提供了一个基本的 TeX 安装，包括 `tlmgr` 程序。使用 `tlmgr` 来安装 TeX 软件包和来自 CTAN 镜像的软件包集。安装 `gnupg` 包以允许 `tlmgr` 验证TeX包。

`texlive-bin` 软件包包含最新的 TeX Live 版本；然而，早期版本，如 `texlive2018-bin`，也是可用的。

`texlive` 包和 `texlive-*` 包也是可用的，它们通过 xbps 直接提供 TeX 包。通过这些包安装的 TeX 包不能与直接从 CTAN（通过 `tlmgr`）安装的 TeX 包交互。例如：来自 `texlive-pdflatex` 的 `pdflatex` 不能用来编译一个使用通过 `tlmgr` 安装的包的 TeX 文档；为此需要  `tlmgr install pdflatex`

## 配置 TeX Live

安装完 TeX Live 后，更新 `PATH` 值:

```
$ source /etc/profile
```

检查 `/opt/texlive/<year>/bin/x86_64-linux` (或
`/opt/texlive/<year>/bin/i386-linux`) 是否在你的 `PATH` 中:

```
$ echo $PATH
```

如果需要，改变全局下默认的纸张尺寸:

```
# tlmgr paper <letter|a4>
```

## 安装/更新TeX软件包

为了安装所有可用的软件包，运行:

```
# tlmgr install scheme-full
```

要安装特定的软件包，你可以安装包括这些软件包的集合。
列出可用的集合，请运行:

```
$ tlmgr info collections
```

查看一个集合所拥有的文件的列表，请运行:

```
$ tlmgr info --list collection-<name>
```

安装这个集合，请运行:

```
# tlmgr install collection-<name>
```

要安装一个独立的软件包，首先检查该软件包是否存在:

```
$ tlmgr search --global <package>
```

然后安装:

```
# tlmgr install <package>
```

要找到提供某个特定文件的软件包（例如某个字体），请运行:

```
$ tlmgr search --file <filename> --global
```

删除一个包或一个集合，请运行:

```
# tlmgr remove <package>
```

更新已安装的软件包，请运行:

```
# tlmgr update --all
```

完整描述可参阅:

<https://www.tug.org/texlive/doc/tlmgr.html>
