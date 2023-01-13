# Void 文档

欢迎来到 Void Linux文档。本仓库是 <https://docs.voidlinux.org/> 文档的中文汉化版本。对这个仓库的贡献遵循与软件包相同的协议。详情请参阅[CONTRIBUTING](./CONTRIBUTING.md).

## 构建

[res/build.sh](./res/build.sh) 构建了HTML、roff和PDF版本的 Void 文档和 `void-docs.1` 主界面. 想要构建它需要如下依赖:

- `mdBook`
- `findutils`
- `lowdown` (0.8.1版本或更新的)
- `texlive`
- `perl`
- `perl-File-Which`
- `perl-JSON`
- `librsvg-utils`
- `python3-md2gemini`

为构建和安装这些文档, 请将 `PREFIX` 和 `DESTDIR` 设置为适当的环境变量，并运行 `res/build.sh`再运行`res/install.sh`
