> 我又来玩了，这次准备给我的 Mi 11 Pro 编译一个 AOSP。

## 安装基础软件

主要软件包有
1. python3

```sh
sudo apt install python-is-python3
```

## 安装 repo

> 使用清华源的 git-repo 镜像

```sh
mkdir ~/bin         # 新建目录
PATH=~/bin:$PATH    # 添加到 PATH
curl https://mirrors.tuna.tsinghua.edu.cn/git/git-repo -o repo  # 下载 repo
```

编辑 `~/.profile` 文件，并写入：

```sh
REPO_URL='https://mirrors.tuna.tsinghua.edu.cn/git/git-repo'
```

设置更新 repo 所用的源

## 初始化 repo 客户端

创建目录并进入

```sh
mkdir ~/android
cd ~/android
```

设置 git
```sh
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

### 使用清华源的初始化包

```sh
wget -c https://mirrors.tuna.tsinghua.edu.cn/aosp-monthly/aosp-latest.tar   #下载
tar xf aosp-latest.tar          # 解压缩
mv AOSP/* ~/android/            # 移动文件到工作目录
```

### 使用传统初始化方式

```sh
repo init -u https://mirrors.tuna.tsinghua.edu.cn/git/AOSP/platform/manifest    # 初始化仓库
repo sync       # 同步仓库
```

> 可以使用 `-j` 来指定并发数量。**由于 AOSP 镜像造成CPU/内存负载过重，TUNA方面限制了并发数量，建议在流量小时同步并使用并发4**