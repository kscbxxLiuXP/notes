# vim

## 安装插件

`vim`的插件需要用插件管理工具[`vim-plug`](https://github.com/junegunn/vim-plug)来管理

将`plug.vim`放到`.vim/autoload/`目录下，然后在`.vimrc`中写：

```
call plug#begin()
Plug 'junegunn/fzf.vim'
Plug 'junegunn/fzf', { 'do': { -> fzf#install() } }
call plug#end()
```

打开`vim`，输入`:PlugInstall`就可以自动安装插件

## 打开多窗口

`vim -o2`打开两个水平窗口

`vim -O2`打开两个垂直窗口

打开两个垂直窗口，并填充两个文件

```
vim -O5 hello1.txt hello2.txt
```

## 挂起vim

在vim中 `Ctrl-z`， 或者 `:stop`和 `:suspend`，可以将vim挂在后台

唤醒直接在命令行输入`fg`

## vim启动时附加命令

`vim +{cmd}`可以在`vim`启动时立即执行一个命令

打开`hello.txt`时将`pancake`全部替换成`bagel`

```
vim +%s/pancake/bagel/g hello.txt
```



除此之外，还可以将命令行执行的结果直接重定向到vim

```
ls -l | vim -
```

## buffers, windows, tabs

### buffers

vim可以一次打开多个文件，在vim界面一次只能显示一个文件，但其实每打开一个文件就是一个对应的buffer，通过在buffer之间切换可以显示不同的文件

一次打开两个文件 ：

```vim
vim file1.c file2.c
```

此时展示的是`file1.c`文件buffer，但是实际上创建了`file1.js`和`file2.js`两个`buffer`

查看所有的buffer： `:buffers`，`:ls`，`:files`

在buffer之间切换：

- `:bnext`，`:bprevious`，切换到前一个或后一个
- `:buffer filename`切换到`filename`
- `:buffer n `  切换到第`n`
- `Ctrl-O`和`Ctrl-I` 快捷键可以左右切换

删除buffer与上面类似 `:bdelete`,`:bdelete n`,`:bdelete filename` 

简写：`:%bd`

退出buffer与普通退出类似 `:qall` ，`:qall!`， `:wqall`

### windows

> a window is a viewport on a buffer

可以将视窗进行分割成多个窗口

`vsplit`是左右垂直分割，`split`是上下水平分割

```
:split <filename>
```

`filename`是可选的，如果没有就是将当前打开的文件分割，否则将新的`filename`展示在顶部，原文件展示在底部

在窗口之间切换：

```
Ctrl-W H    Moves the cursor to the left window
Ctrl-W J    Moves the cursor to the window below
Ctrl-W K    Moves the cursor to the window upper
Ctrl-W L    Moves the cursor to the right window
```

所有窗口统一显示一个`buffer`:

```
:buffer file2.js
```

关闭窗口

`Ctrl-W C`或者`:quit`，窗口关闭了，但是buffer还在

```
:vsplit filename    Split window vertically
:split filename     Split window horizontally
:new filename       Create new window
```

### tabs

```
:tabnew file.txt    Open file.txt in a new tab
:tabclose           Close the current tab
:tabnext            Go to next tab
:tabprevious        Go to previous tab
:tablast            Go to last tab
:tabfirst           Go to first tab
```

标签跳转

- `gt` next tab

- `gT` previous tab

- `3gt` third tab

以标签形式打开多个文件

```
vim -p file1.js file2.js file3.js
```

## Opening and searching files

### edit & find

本节介绍如何快速搜索或打开工作目录下的文件

`:edit`和`:find`语法类似，在使用这两个命令时可以用`<tab>`键来快速填充

同时可以使用`*`通配符来搜索

```
:edit **/*.md<Tab>
```

当`edit`后面接的是一个目录的时候，将会启用`netrw`，文件浏览器

`find`和`edit`的区别就是，`find`可以配置搜索时的`path`,默认`path`：

```
path=.,/usr/include,,
```

文件在path下的话，直接输入文件名就可以打开，而不需要输入完整的路径

配置path：

```
:set path+=app/controllers/
```

### grep

如果要在文件里面搜索关键字，vim提供了两种搜索模式

- 内部grep `:vim`

  /pattern/ 是一个正则表达式

  ```
  :vim /pattern/ file
  ```

  比如：

  ```
  :vim /breakfast/ app/controllers/**/*.rb
  ```

- 外部grep `:grep`

  实际上执行是外部终端中的`grep`命令，可以通过`grepprg`变量来定义具体执行的是哪个终端指令

  比如：

  ```
  :grep -R "lunch" app/controllers/
  ```

搜索结果通过`quickfix`窗口来呈现，相关的指令如下：

```
:copen        Open the quickfix window
:cclose       Close the quickfix window
:cnext        Go to the next error
:cprevious    Go to the previous error
:colder       Go to the older error list
:cnewer       Go to the newer error list
```

### vim的文件浏览器Netrw

配置文件：

```
set nocp
filetype plugin on
```

在终端中可以使用`vim .`来打开目录

在`vim`内部时，可以使用`:edit .`来启用

### fzf

`fzf`是一个功能很强大的工具，可以通过配置将它集成到`vim`中





## vim语法

