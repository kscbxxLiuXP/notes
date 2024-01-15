# vim-plug

## 介绍

Vim-plug 是一个Vim插件管理器，利用异步并行可以快速地安装、更新和卸载插件。它的安装和配置都非常简单，而且在操作过程中会给出很多易读的反馈信息，是一个自由、开源、速度非常快的、并行地安装或更新插件，极简的 vim 插件管理器。

Vim-plug的获取链接：https://github.com/junegunn/vim-plug

## 二、目录准备

### 2.1 检查用户下是否有~/.vim文件夹

```
mkdir ~/.vim
cd ~/.vim
mkdir plugged plugin syntax colors doc autoload （autoload文件夹也可在安装Vim-plug插件时创建）
```

### 2.2 ~/.vim文件夹下目录介绍

```
~/.vim/autoload/它是一个非常重要的目录，尽管听起来比实际复杂。简而言之，它里面放置的是当你真正需要的时候才被自动加载运行的文件，而不是在vim启动时就加载。
~/.vim/colors/是用来存放vim配色方案的。
~/.vim/plugin/存放的是每次启动vim都会被运行一次的插件，也就是说只要你想在vim启动时就运行的插件就放在这个目录下。我们可以放从vim-plug官方下载下来的插件.vim
~/.vim/syntax/语法描述脚本。我们放有关文本（比如c语言）语法相关的插件
~/.vim/doc/为插件放置文档的地方。例如:help的时候可以用到。
~/.vim/ftdetect/中的文件同样也会在vim启动时就运行。有些时候可能没有这个目录。ftdetect代表的是“filetype detection（文件类型检测）”。此目录中的文件应该用自动命令（autocommands）来检测和设置文件的类型，除此之外并无其他。也就是说，它们只该有一两行而已。
~/.vim/ftplugin/此目录中的文件有些不同。当vim给缓冲区的filetype设置一个值时，vim将会在~/.vim/ftplugin/ 目录下来查找和filetype相同名字的文件。例如你运行set filetype=derp这条命令后，vim将查找~/.vim/ftplugin/derp.vim此文件，如果存在就运行它。不仅如此，它还会运行ftplugin下相同名字的子目录中的所有文件，如~/.vim/ftplugin/derp/这个文件夹下的文件都会被运行。每次启用时，应该为不同的文件类型设置局部缓冲选项，如果设置为全局缓冲选项的话，将会覆盖所有打开的缓冲区。
~/.vim/indent/这里面的文件和ftplugin中的很像，它们也是根据它们的名字来加载的。它放置了相关文件类型的缩进。例如python应该怎么缩进，java应该怎么缩进等等。其实放在ftplugin中也可以，但单独列出来只是为了方便文件管理和理解。
~/.vim/compiler/和indent很像，它放的是相应文件类型应该如何编译的选项。
~/.vim/after/这里面的文件也会在vim每次启动的时候加载，不过是等待~/.vim/plugin/加载完成之后才加载after里的内容，所以叫做after。
~/.vim/spell/拼写检查脚本。
```

## 三、Vim-plug的安装

### 3.1离线安装

下载 plug.vim 文件，根据操作系统不同，放置在以下~/.vim/autoload目录中：

```
mkdir -p  ~/.vim/autoload/
cp plug.vim  ~/.vim/autoload/plug.vim
```

### 3.2在线安装

```
curl -fLo ~/.vim/autoload/plug.vim --create-dirs https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

## 四、插件的添加和删除

`vim-plug`支持源码托管在`GitHub`的插件，你可以在"https://github.com/vim-scripts/" 上找到(`vim官网`)[(https://www.vim.org)]里所有插件的镜像。也可以安装Windows版本的Git查看vim插件。

要安装插件，你必须如下所示首先在 Vim 配置文件中声明它们。

一般`Vim`的配置文件是`~/.vimrc`, `Neovim` 的配置文件是 `~/.config/nvim/init.vim`。

请记住，当你在配置文件中声明插件时，列表应该以 `call plug#begin(PLUGIN_DIRECTORY)` 开始，并以 `plug#end()` 结束。

编辑 `~/.vimrc` 文件中的内容，比如安装 `lightline.vim` 插件

```
call plug#begin('~/.vim/plugged')
Plug 'itchyny/lightline.vim'
call plug#end()
```

运行命令重新加载：

```
:source ~/.vimrc
```

## 五、vim中vim-plug指令用法

### 5.1安装插件

如果希望安装新插件，首先找到其在[http://github.com的网址](http://github.xn--com-fs6e924libl/)，然后将其追加至vimrc配置文件中的call plug#begin()和call plug#end()之间，最后执行以下命令安装所有引用的插件：

```
:PlugInstall
```

你也可以使用以下命令，指定安装特定的插件：

```
:PlugInstall gist-vim
```

### 5.2卸载插件

如果希望卸载插件，请先在vimrc配置文件中注释或者删除对应插件的配置信息，然后再执行以下命令：

```
:PlugClean
```

### 5.3更新插件

使用以下命令，可以更新vim-plug插件自身：

```
:PlugUpgrade
```

使用以下命令，可以批量更新所有已安装的插件：

```
:PlugUpdate
```

### 5.4插件状态

使用以下命令，可以查看当前已安装插件的状态信息：

```
:PlugStatus
```

因为每个插件配置均有不同，插件作者会在Git中添加插件配置方法，所以各个插件的配置方法请参考插件作者的使用说明！