# Git 基本操作命令汇总

## 克隆仓库

执行 `git clone` 命令实现克隆 github 仓库

```shell
git clone https://github.com/wannain/test.git
```

执行 `git remote -v` 命令可以查看本地仓库所关联的远程仓库信息

```shell
git remote -v
```

执行 `git status` 查看整个仓库的状态

```shell
git status
```

## 添加修改到暂存区

对多个文件或目录进行了增删改，可以使用 `git add .` 命令全部添加到暂存区

```shell
git add .
```

要撤销暂存区的修改怎么办？根据上图的提示，执行 `git reset -- [文件名]` 或者 `git rm --cached [文件名]` 命令即可

```shell
git reset -- a.txt
```

执行 `git commit -m` 命令生成一个新的提交

```shell
git commit -m "how do you do "
```


命令 `git diff`，它可以用来查看工作区被跟踪的文件的修改详情。

```shell
git diff
```

这里有一个比较：
git diff #是工作区(work dict)和暂存区(stage)的比较
git diff –cached #是暂存区(stage)和版本库的比较

git add 命令只是把工作区当前的修改提交到暂存区中，git commit 一次将暂存区中的内容提交到版本库中。为了防止工作区的修改没有被提交到暂存区中，或则查看工作区和暂存区之间的差异，可以使用 git diff 指令。

命令 `git log`，它用来查看版本区的提交历史记录

- `git log [分支名]` 查看某分支的提交历史，不写分支名查看当前所在分支
- `git log --oneline` 一行显示提交历史
- `git log -n` 其中 n 是数字，查看最近 n 个提交
- `git log --author [贡献者名字]` 查看指定贡献者的提交记录
- `git log --graph` 图示法显示提交历史

## git 的本地设置

命令是 `git config --global alias.[别名] [原命令]`，如果原命令中有选项，需要加引号

```shell
git config --global user.email "your_email@qq.com"
git config __global user.name "your_github_name"
```

`git config -l` 可以查看配置信息

```shell
git config -l
```

## 版本回退

对于版本回退，可以采取两种办法

+ 撤销最近一次提交，修改文件后重新提交推送
+ 在工作区修改，然后再提交

执行 `git reset --soft HEAD^` 撤销最近的一次提交，将修改还原到暂存区。`--soft` 表示软退回，对应的还有 `--hard` 硬退回，`HEAD^` 表示撤销一次提交，`HEAD^^` 表示撤销两次提交，撤销 n 次可以简写为 `HEAD~n`。

```shell
git reset --soft HEAD^`
```

## git 分支操作

命令 `git branch -avv`，它用来查看全部分支信息

```shell
git branch -avv
```

`git reflog` 命令，它会记录本地仓库所有分支的每一次版本变化

执行 `git branch [分支名]` 可以创建新的分支

```shell
git branch luozijian
```

执行 `git checkout [分支名]` 切换分支

```shell
git checkout luozijian
```

## git 推送到版本库

执行 `git push [主机名] [本地分支名]:[远程分支名]` 即可将本地分支推送到远程仓库的分支中，通常冒号前后的分支名是相同的，如果是相同的，可以省略 `:[远程分支名]`，如果远程分支不存在，会自动创建。

```shell
git push origin luozijian:luozijian
```

执行这个命令 `git branch -u [主机名/远程分支名] [本地分支名]` 将本地分支与远程分支关联

```shell
git branch -u origin/luozijian luozijian
```

执行 `git branch --unset-upstream [分支名]` 即可撤销该分支对远程分支的跟踪，同样地，如果撤销当前所在的分支的跟踪，分支名可以省略不写

## 删除分支

使用 `git push [主机名] :[远程分支名]` 删除远程分支

另一个删除远程分支的命令：`git push [主机名] --delete [远程分支名]`

使用 `git branch -D [分支名]` 删除本地分支

使用 `git branch -m [原分支名] [新分支名]` 本地分支改名

## git 同步主仓库

执行 `git remote add [主机名] [主仓库的地址]`来增加一个关联主机，
注意，主仓库的地址使用 https 开头的

使用 `git remote -v` 命令查看关联的主机情况

执行 `git pull --rebase origin master` 拉取

## git 修改仓库远程地址

修改命令

```shell
git remote set-url origin [url]
```

或者先删后加

```shell
git remote rm origin

git remote add origin [url]
```

