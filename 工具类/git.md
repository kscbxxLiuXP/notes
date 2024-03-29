# git

## 补丁(diff和patch)

## 一、 patch 和diff 的区别

Git 提供了两种补丁方案，一是用git diff生成的UNIX标准补丁.diff文件，二是git format-patch生成的Git专用.patch 文件。\
.diff文件只是记录文件改变的内容，不带有commit记录信息,多个commit可以合并成一个diff文件。\
.patch文件带有记录文件改变的内容，也带有commit记录信息,每个commit对应一个patch文件。

##### 在Git下，我们可以使用.diff文件也可以使用.patch 文件来打补丁，主要应用场景有：CodeReview、代码迁移等。

## 二、创建patch和diff

#### 1、创建patch 文件的常用命令行

##### \*某次提交（含）之前的几次提交：

```
git format-patch 【commit sha1 id】-n

```

n指从sha1 id对应的commit开始算起n个提交。\
eg：

```
git format-patch  2a2fb4539925bfa4a141fe492d9828d030f7c8a8 -2

```

##### \*某个提交的patch：

```
git format-patch 【commit sha1 id】 -1

```

eg：

```
git format-patch  2a2fb4539925bfa4a141fe492d9828d030f7c8a8 -1

```

##### \*某两次提交之间的所有patch:

```
git format-patch 【commit sha1 id】..【commit sha1 id】 

```

eg：

```
git format-patch  2a2fb4539925bfa4a141fe492d9828d030f7c8a8..89aebfcc73bdac8054be1a242598610d8ed5f3c8

```

##### 2、创建diff文件的常用方法

###### 使用命令行

```
git diff  【commit sha1 id】 【commit sha1 id】 >  【diff文件名】

```

eg：

```
git diff  2a2fb4539925bfa4a141fe492d9828d030f7c8a8  89aebfcc73bdac8054be1a242598610d8ed5f3c8 > patch.diff

```

###### 使用SourceTree

选中要目标commit ,右击，选择create patch

##### 3、如何获取commit sha1 id

git 中的每个commit都有对应的一个sha1 id，我们可以通过在终端输入`git log`，然后找到对应的commit sha1 id:

![](git.assets/webp-20231009151932990)

\
如图中`2a2fb4539925bfa4a141fe492d9828d030f7c8a8`便是sha1 id

如果用Sourcetree的话也很方便，右击对应的commit，选择copy SHA-1 toclipboard便复制sha1 id到剪切板中：

![](git.assets/webp-20231009151932996)

## 三、应用patch 和 diff

#### 相关命令行

###### 检查patch/diff是否能正常打入:

```
git apply --check 【path/to/xxx.patch】
git apply --check 【path/to/xxx.diff】

```

###### 打入patch/diff:

```
git apply 【path/to/xxx.patch】
git apply 【path/to/xxx.diff】

```

或者

```
git  am 【path/to/xxx.patch】

```

#### 使用SourceTree

选择SourceTree，在屏幕顶部选择Aciotn-Apply patch

\
&#x20;![](git.assets/webp-20231009151932995)&#x20;

选择patch或者diff的路径，然后点OK

\
&#x20;![](git.assets/webp-20231009151932989)

## 四、冲突解决

在打补丁过程中有时候会出现冲突的情况，有冲突时会打入失败，如图：

\
&#x20;![](git.assets/webp)

此时需要解决冲突：\
1、首先使用 以下命令行，自动合入 patch 中不冲突的代码改动，同时保留冲突的部分：

```
git  apply --reject  xxxx.patch

```

可以在终端中显示出冲突的大致代码：

![](git.assets/webp-20231009151933001)

\
同时会生成后缀为 .rej 的文件，保存没有合并进去的部分的内容，可以参考这个进行冲突解决。\
2、解决完冲突后删除后缀为 .rej 的文件，并执行`git add.`添加改动到暂存区.\
3、接着执行`git am --resolved`或者`git am --continue`

#### 说明：在打入patch冲突时，可以执行`git am --skip`跳过此次冲突，也可以执行`git am --abort`回退打入patch的动作，还原到操作前的状态。

关于冲突解决详情可以参考[git am冲突解决](https://blog.csdn.net/Qidi_Huang/article/details/61920472)

\
\
作者：alanwangmodify\
链接：<https://www.jianshu.com/p/ec04de3f95cc>\
来源：简书\
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

补丁生效

移除补丁