
```
更新软件包索引，并且安装必要的依赖软件，来添加一个新的 HTTPS 软件源

sudo apt update
sudo apt install apt-transport-https ca-certificates curl gnupg-agent software-properties-common

使用下面的 `curl` 导入源仓库的 GPG key

curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -

将 Docker APT 软件源添加到你的系统

sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"

现在，Docker 软件源被启用了，你可以安装软件源中任何可用的 Docker 版本


sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io

一旦安装完成，Docker 服务将会自动启动。你可以输入下面的命令，验证它：


sudo systemctl status docker


```