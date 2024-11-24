[go.dev](https://link.zhihu.com/?target=https%3A//go.dev/dl/) 在官网下载 windows 版的安装包，直接安装，新版本不需要再设置环境变量
## 添加镜像
打开 PowerShell 输入命令
```text
go env -w GO111MODULE=on
go env -w GOPROXY=https://goproxy.cn,direct 
```

## VScode 的插件安装
1.安装 Go 和 Go Nightly 两个官方扩展
2.安装 Go tools
`Ctrl+Shift+P` 调出来 Vscode 的命令面板，输入 `go:install`
选择 Go:Install/Update Tools 这个选项，勾选所有安装


## 其他
以前写 go 要设置 `gopath` 、 `goroot` 路径，还要在 `gopath` 路径下创建三个文件夹 `src` `pkg` `bin`
现在用 [go mod](https://www.zhihu.com/search?q=go%20mod&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22article%22%2C%22sourceId%22%3A%22626354347%22%7D) 就不必再搞这些东西