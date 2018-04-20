# 环境准备

我使用的系统的是 macOS，所以基本也是已我的系统环境为基础。

需要准备：

* 命令行工具
* Node
* Npm
* Webpack

## 安装Node.js环境

这里我采用的是`nvm`的 `node`版本管理工具，他是通过简单的bash脚本来管理多个node.js版本。

1、安装 `nvm` 
```
# curl方式
$ curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.33.9/install.sh | bash

# wget方式
$ wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.9/install.sh | bash
```

2、需要把启动 `nvm` 的脚本添加下,让它随着终端的打开就会自行启动（不然关闭终端后你就会发现 nvm 不能用了）

将下面的源代码行添加到您的配置文件(~/.bash_profile, ~/.zshrc, ~/.profile, or ~/.bashrc). 

我这里是 `.zshrc`

```
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh" # This loads nvm
```

添加完后，执行下面的命令，让其配置生效

```
$ source ~/.zshrc
```

验证是否安装成功

```
$ nvm --version
```

**nvm 使用**

```
# 查看当前的 node 列表, 箭头指向的就是当前使用的版本
$ nvm list

# 安装 node 8代表的版本号
$ nvm install 8

# 查看 node 是否安装成功
$ node -v

# 选择版本
$ nvm use v8.11.1

# 查看 node 的目录
$ nvm which v8.11.1
```

[官方文档](https://github.com/creationix/nvm)
## nrm -- NPM registry manager

`nrm` 是一个让你可以随意切换 `npm` 源的工具，也叫`npm`注册管理器。

https://registry.npmjs.com 是node官方的源（registry），服务器在国外，下载速度较慢，推荐安装nrm来切换源，国内的cnpm和taobao的源都非常快（但使用`cnpm`安装依赖跟 `npm` 下载有的依赖的时候目录结构还是有点不一样，毕竟命令更换了，然后遇到各种问题，所以还是使用 `nrm`来切换源）。

```
# 安装很简单,使用 npm 命令全局安装
$ npm install -g nrm

# 检查是否安装成功
$ nrm --version

# 查看 npm 的源列表，前面有 * 号的表示当前使用的源
$ nrm ls

# 切换 到 taobao 源
$ nrm use taobao

```

切换源后， 你会发现 使用 `npm install` 安装各种依赖的速度杠杠的～～

> Note: 如果还想直到其他的命令， 都是命令后跟上 `-h` 就OK，eg: `node -h`、 `nrm -h`、`npm -h`... 屡试不爽 

