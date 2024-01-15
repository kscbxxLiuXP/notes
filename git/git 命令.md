推代码之前：

查看代码差异：

```
git diff
git diff --cached #会显示已经暂存的更改的详细内容。
```

选择同一个文件中的部分代码提交：

```bash
git add -p 
# 会逐个显示出你的修改，然后询问你是否要将其暂存。你可以选择 "y" 暂存，"n" 不暂存，"s" 拆分当前的修改等。
```



```
git diff

git pull --rebase
```

```
gitdir=$(git rev-parse --git-dir); scp -p -P 29418 liuxinpeng@rd.loongson.cn:hooks/commit-msg ${gitdir}/hooks/

git commit --amend --no-edit
```



推送代码

```
git push origin HEAD:refs/for/master
```

