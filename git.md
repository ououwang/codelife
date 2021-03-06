



# git

![Saving changes](https://wac-cdn.atlassian.com/dam/jcr:75f75cb6-a6ab-4f0b-ab29-e366914f513c/hero.svg?cdnVersion=kg)

***[git add](2.3.1-git_add.md) / [git commit](2.3.2-git_commit.md) / [git diff](2.3.3-git_diff.md) / [git stash](2.3.4-git_stash.md) / [.gitignore](2.3.5-git_ignore.md)***


>
> ©️ 本文演绎自 Atlassian 编写的 [_Saving Changes_](https://www.atlassian.com/git/tutorials/saving-changes)。页面上所有内容采用知识共享-署名（[CC BY 2.5 AU](http://creativecommons.org/licenses/by/2.5/au/deed.zh)）许可协议。

## git标签操作

命令git push origin <tagname>可以推送一个本地标签；

命令git push origin --tags可以推送全部未推送过的本地标签；

命令git tag -d <tagname>可以删除一个本地标签；

命令git push origin :refs/tags/<tagname>可以删除一个远程标签。


## git add

`git add` 命令将工作目录中的变化添加到缓存区。它告诉 Git 你希望下一个提交中包含这个文件的更新。不过，`git add` 不会实际上并不不会改变你的仓库——直到你运行 `git commit` 前更改都还不会真正被记录。

使用这些命令的同时，你还需要 `git status` 来查看工作目录和缓存区的状态。

## 工作原理

`git add` 和 `git commit` 这两个命令组成了最基本的 Git 工作流。每个 Git 用户都必须理解这两个命令，不管团队使用的是哪种协作模型。它们的作用是将项目的诸多版本记录到仓库历史中。

一个项目的编写离不开这个基本模式：编辑、缓存和提交。首先，你在工作目录中编辑你的文件。当你想要备份当前的项目状态时，你通过 `git add` 来缓存更改。当你觉得这个缓存的快照没问题了，你通过 `git commit` 将它提交到项目历史。`git reset` 命令用于撤销提交或缓存的快照。

除了 `git add` 和 `git commit` 之外，`git push` 也是完整的 Git 协作流程中的重要一环。`git push` 用于将提交的更改发送到远端仓库。操作完成后，其他团队成员也可以看到这些更改。

![Git Tutorial: git add Snapshot](https://wac-cdn.atlassian.com/dam/jcr:0f27e004-f2f5-4890-921d-65fa77ba2774/01.svg)

`git add` 命令和 `svn add` 不同。后者直接将**文件**添加到仓库中，而 `git add` 作用于更抽象的**更改**（更低一层）。也就是说，哪怕是同一个文件时，每次修改时你都需要使用 `git add` ，而 `svn add` 只需要使用一次。这听上去多此一举，但实际上使得项目更容易管理。

## 缓存区

`git add` 命令最主要的作用是将工作目录中的更改添加到 Git 的缓存区。缓存区是 Git 特有的功能之一，如果你之前使用的是 SVN（甚至是更古老的 Mercurial），那你可得花点时间理解了。你可以把它理解成工作目录和项目历史中间的缓冲区。缓存区是 Git 三个树状文件结构之一，另外两个是工作目录和提交历史。

缓存允许你把密切相关的一些更改合并成一份快照，而不是直接提交所有新的更改。也就是说你可以同时进行多个无关的更改，最后将相关的更改添加到缓存区，分成几次分别提交。对于任何版本控制系统来说，保持提交的原子性非常重要，以便于追踪 bug 以及用最小的代价撤销更改。

## 用法

```
git add <文件>
```

缓存 `<文件>` 中的更改，准备下次提交。

```
git add <目录>
```

缓存 `<目录>` 下的所有更改，准备下次提交。

```
git add -p
```

开始交互式的缓存，你可以将某个文件的其中一处更改加入到下次提交缓存。Git 会显示一堆更改，并等待用户命令。使用 `y` 缓存某一处更改，使用 `n` 忽略某一处更改，使用 `s` 将某一处分割成更小的几份，使用 `e` 手动编辑某一处更改，使用  `q` 退出编辑。

## 栗子

对于新项目来说，`git add` 和 `svn import` 的作用类似。使用下面两个命令为当前目录创建首个提交：

```
git add .
git commit
```

项目设置好之后，新的文件可以通过路径传递给 `git add` 来添加到缓存区：

```
git add hello.py
git commit
```

