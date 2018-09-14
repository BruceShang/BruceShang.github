---
title: iTem2 + oh my zsh + agnoster 打造自己的终端工具
date: 2018-07-06 10:39:26
tags: [iTem2, zsh]
categories: iTem2
---

### 1. 安装iTem2

[iTerm2传送门，选择要下载的安装包](https://www.iterm2.com/downloads.html)

### 2. oh-my-zsh介绍
> oh-my-zsh是基于zsh的功能做了一个扩展，方便的插件管理、主题自定义，以及漂亮的自动完成效果

#### 安装zsh
```js
brew install zsh
// 出现这个画面后就代码安装成功
```
![zsh](/images/omz.png)

> 没有安装homebrew的先安装一下
[homebrew官网](http://brew.sh/index_zh-cn.html)

复制下面命令，在终端中输入：
<!-- more -->
```js
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```
> brew 安装插件的时候默认都会进行更新，这个是特别慢的毕竟有道墙，手动设置关闭自动更新，在终端中执行下面代码。

```js
export HOMEBREW_NO_AUTO_UPDATE=true
```
#### 查看当前默认bash，修改默认shell
```js
chsh -s /bin/zsh
```
修改完之后验证是否修改成功
```js
echo $SHELL
// 此时终端打印出 /bin/zsh 恭喜你设置成功
```

### 下载安装PowerFonts字体
```js
# git clone
git clone https://github.com/powerline/fonts.git --depth=1
# cd to folder
cd fonts
# run install shell
./install.sh
```
安装好字体库之后，我们来设置iTerm2的字体，具体的操作是iTerm2 -> Preferences -> Profiles -> Text，在Font区域选中Change Font，然后找到Meslo LG字体。有L、M、S可选，看个人喜好：
![zsh](/images/font1.png)

### 配置agnoster主题
安装成功之后，我们可以通过vi ~/.zshrc，设置ZSH_THEME="agnoster"对主题进行修改。
> 最终效果图

![zsh](/images/success.png)

### 命令补全

```js
cd ~/.oh-my-zsh/custom/plugins/
git clone https://github.com/zsh-users/zsh-autosuggestions
vi ~/.zshrc
// 找到plugins，修改内容
plugins=(
    git
    zsh-autosuggestions
)
// 默认打开里面已经有一个git
```
> 效果展示
![auto](/images/auto.png)

### 安装高亮插件
```js
cd ~/.oh-my-zsh/custom/plugins/
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git
vi ~/.zshrc
// 找到plugins，修改内容
plugins=(
    git
    zsh-autosuggestions
    zsh-syntax-highlighting
)
```
zsh-syntax-highlighting必须在最后一个。

然后在文件的最后一行添加：
```js
source ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh
```
按一下esc调出vi命令，输入:wq保存并退出vi模式。

执行命令使刚才的修改生效：
```js
source ~/.zshrc
```
### 配置完成
![zsh-syntax-highlighting.zsh](/images/gaoliang.png)