---
title: Hexo+GitHub搭建个人博客
date: 2023-05-30 18:41:44
tags: [-Hexo, -Github -Blog]
categories: -教程
---

---

### 环境准备

#### 安装 node.js

去[node.js 官网](http://nodejs.cn/download/)下载 64 位安装包，版本要求 16 以上，注意要下载长期支持版，安装时的配置默认就行。  
如果下载很慢就[去这](https://mirrors.tuna.tsinghua.edu.cn/)，进入网站直接搜索 node 就能看到，
每个大版本最后一个小版本即为长期支持版，比如 16 版的长期支持版为 v16.20.0。

#### 安装 Git

去[Git 官网](https://git-scm.com/download/win)下载 64 位安装包，选择 64-bit Git for Windows Setup，安装时的配置默认就行。

#### 安装 npm

打开 Window 自带的 powershell，输入以下指令进行安装

```text
npm install -g npm --registry=https://registry.npm.taobao.org
```

#### 验证是否安装成功

在 powershell 里面输入下面这些指令，如果显示各自的版本号则成功安装。

```text
node --version
git -version
npm -version
```

#### 注册 Github 账号，并建立一个仓库

去[Github 官网](https://github.com/)注册一个 GitHub 账号。  
创建仓库界面如下图所示，由于我已建立该仓库，所以显示红色提示，
正常情况下是绿色提示，注意选择公开仓库，加不加说明文件都可以，然后点击创建按钮。  
![image_signupgithubuser](https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202305301943094.png)

---

### 搭建

#### 创建本地仓库

在你的磁盘中选一个合适的位置创建一个文件夹（建议创建在在用户文件夹下如 C:\Users\你的用户名\blog），默认为 blog，可以自定义，但最好不以数字开头，不要有空格，不要有中文。

#### 与 github 建立连接

打开 powershell，输入指令使目录定位到新建的 blog 文件夹；或者在 blog 文件夹下右键使用 vscode 打开，vscode 的终端会自动定位到该目录，vscode 的终端可以替代 powershell 的功能，也方便后期的博客内容编写及发布操作。

```text
cd ./文件夹名/   //定位到当前目录的该文件夹
cd ..           //返回上一目录
```

在 powershell 或者 vscode 中输入以下指令

```text
ssh-keygen -t rsa -C "建立github所用的邮箱"
```

1.打开 blog 同级目录下的.ssh 文件夹（如果找不到请检查是否以上指令没有执行成功），右键选择打开方式，以记事本形式打开"id_rsa.pub"文件，Ctrl+A 全选，复制里面的所有内容。

2.打开你的 GitHub 主页，打开设置，并选择左侧的"SSH and GPG keys"，点击"New SSH key"，为你的 key 命名，不以数字开头，不要有空格，不要有中文。

3.在 key 中粘贴上一步复制的内容，并选择下面的"Add SSH key"

4.然后在 powershell 或者 vscode 中输入以下指令，并输入 yes 进行验证，与 GitHub 建立连接。成功后你的邮箱会收到 GitHub 的邮件。

```text
ssh -T git@github.com
```

#### 绑定 GitHub 账户

在 powershell 或者 vscode 中输入以下指令进行绑定账户

```text
git config --global user.name "github用户名"
git config --global user.email "建立github所用的邮箱"
```

#### 安装 Hexo

在 powershell 或者 vscode 中输入以下指令安装 Hexo 框架

```text
npm install -g hexo-cli
```

#### 初始化 hexo

在 powershell 或者 vscode 中输入以下指令进行初始化,这时 blog 文件夹下多了 Hexo 的默认网站文件，可以使用"hexo s"指令在浏览器输入 localhost:4000 就可以看到。

```text
hexo init
```

用记事本打开本地仓库中的\_config.yml 文件，在以下的地方进行修改。

```text
# Deployment
## Docs: https://hexo.io/docs/one-command-deployment
deploy:
  type: git
  repository: git@github.com:GitHub用户名/仓库名.git
  branch: master //你的仓库分支，可以去你的仓库的设置进行查看，一般是main
```

#### 安装上传工具

在 powershell 或者 vscode 中输入以下指令

```text
npm install hexo-deployer-git
```

#### 将本地文件部署到 GitHub 仓库

在 powershell 或者 vscode 中输入以下指令

```text
hexo g -d
```

至此已基本安装完成，可以在浏览器输入你的博客地址查看效果。

#### Hexo 的常用指令

```text
hexo clean    or  hexo c   //清除生成的静态网页缓存
hexo deploy   or  hexo d   //部署到你的网站
hexo generate or  hexo g   //更新静态网页
hexo help     or  hexo h   //查看帮助
hexo new "name"   or  hexo n "name" //创建一篇新博客
hexo server   or  hexo s   //创建本地服务器以查看网页情况，并没有部署
hexo g -d                  //更新静态网页并部署到你的网站
```
