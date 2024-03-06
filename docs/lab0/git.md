# Git 版本维护

Git 是一个免费、开源的分布式版本控制系统，非常推荐大家掌握必要的 Git 操作，这对大家协作进行大作业的开发非常有效。下面的教学是一个较为精炼的版本，且仅给出 Linux 上的执行命令，更多细节可点击文章末尾的详细教程链接查询。

???question "Git 有哪些好处？"

    - **版本控制**：Git 可以跟踪代码的变化，允许开发人员随时访问以前的版本。这意味着您可以轻松地撤消错误更改，恢复文件状态，比较代码差异等等。在团队协作中，Git 可以允许团队成员协同工作，对同一份代码进行修改和版本管控，保证了代码的稳定性与质量。
    - **分支管理**：Git 的分支管理可以使多个团队成员同时在代码库上开发不同的功能和新特性，简化了并行开发的过程。开发人员可以使用 Git 分支来隔离他们的工作，不影响其他开发人员的工作，同时可以方便地合并多个分支，使开发环节更清晰明确。
    - **回溯和还原**：Git 允许开发人员回到任何一个历史版本，并进行修改和还原操作，这是非常简单、快速和可靠的。这意味着开发人员可以找到系统中的问题，快速定位问题源头，并进行解决。
    - **远程协作**：Git 可以让团队成员在不同的地点协作开发，而不会受到距离和时间限制。团队成员可以在本地上建立与远程代码库的连接，并轻松推送和拉取变更。这使得团队内成员的协作更加自然、高效。

## 创建 Git 可管理的仓库

从已有文件夹创建：

```shell
# 假设打开一个新的 shell
$ pwd             # 如果你不知道这个指令，请尝试使用 man 等方式查找含义
/home/osh         # pwd 的输出。"osh" 字符串对应的是用户名。整个目录是你的家目录
$ mkdir learn_git # 创建一个新的名为 learn_git 的文件夹
$ cd learn_git    # 进入该文件夹。你可以再次使用 pwd 检查路径
$ git init
Initialized empty Git repository in /home/osh/learn_git/.git/
```

版本库由 `.git` 文件夹来管理，请不要对此文件夹进行手动操作！

## 进行 Git 版本库的提交和修改

在刚刚创建好的版本库里，假设有一个 `readme.txt` 文件，内容如下：

```
Git is a version control system.
Git is free software.
```

用命令 `git add` 把文件添加到仓库：

```shell
$ git add readme.txt
```

用命令 `git commit` 把文件提交到仓库：

```shell
$ git commit -m "wrote a readme file"
[master (root-commit) eaadf4e] wrote a readme file
 1 file changed, 2 insertions(+)
 create mode 100644 readme.txt
```

这个命令的完整含义是：在本地 Git 代码库中提交了一次更新，这次更新的说明信息是「wrote a readme file」。它会将本次的修改实际保存在本地 Git 代码库中，但不会影响到远程分支。

`commit` 可以一次提交很多文件，所以可以多次 `add` 不同的文件，一般会用到下面这条命令来添加当前目录的所有文件。不过，你可以在仓库目录下编写一个名为 `.gitignore` 的文本文件，并将不需要维护版本的文件/文件夹的相对路径逐行写入，这可以避免维护对项目版本无影响的文件，如人工智能项目中的模型文件就可以避免维护。

```shell
$ git add .
```

## Git 版本库的回溯和撤销回溯

每次 `commit` 之后，新状态会以一个哈希值的形式标识。使用 `git log` 命令可以查看所有的提交记录。

```shell
$ git log
commit 1094adb7b9b3807259d8cb349e7df1d4d6477073 (HEAD -> master)
Author: OSH-TA <fake-email@qq.com>
Date:   Fri May 18 21:06:15 2022 +0800

    append message

commit e475afc93c209a690c39c13a46716e8fa000c366
Author: OSH-TA <fake-email@qq.com>
Date:   Fri May 18 21:03:36 2022 +0800

    add docs
```

如上面的 `1094adb7b9b3807259d8cb349e7df1d4d6477073` 就代表最新的状态，以 `HEAD` 标识。实际上这一长串用前面几位就可以标识，在使用时不需要复制整个串。

注：`HEAD` 的上一个版本是 `HEAD~`，上上一个版本就是 `HEAD~~` 或 `HEAD~2`，如果往上 100 个版本可以写成 `HEAD~100`，也可以用哈希值来标识。

如果现在要把当前版本 `append message` 回退到上一个版本 `add docs`，可以使用`git reset`命令：

```shell
$ git reset --hard HEAD^
HEAD is now at e475afc add docs
```

如果现在又想撤销回退，并且上面的命令行窗口还没有被关掉，就可以顺着往上找到目标版本的哈希值，指定回到未来的某个版本。

```shell
$ git reset --hard 1094a
HEAD is now at 83b0afe append message
```

但如果回退到了某个版本，关掉了电脑，之后想恢复到新版本而找不到新版本的 `commit id` 怎么办？Git 提供了命令 `git reflog` 用来记录你的每一次命令：

```shell
$ git reflog
e475afc HEAD@{1}: reset: moving to HEAD^
1094adb (HEAD -> master) HEAD@{2}: commit: append message
e475afc HEAD@{3}: commit: add docs
eaadf4e HEAD@{4}: commit (initial): wrote a readme file
```

从输出可知，`append message` 的 commit id 是 `1094adb`。

## Git 版本库的分支的创建与切换

一般来说，`main` / `master` 分支中的内容是程序员认为稳定、可靠的版本。如果想要在此基础上进行新特性的开发，又担心产生 bug，则可以创建一个新的分支，在新的分支上改动，等测试稳定后再与主分支合并，或者因 bug 而直接丢弃新分支。这比起回退版本来说更加可靠和容易。

首先，我们创建 `dev` 分支，然后切换到 `dev` 分支：

```shell
$ git checkout -b dev
Switched to a new branch 'dev'
```

`git checkout` 命令加上 `-b` 参数表示创建并切换。然后，用`git branch`命令查看当前分支：

```shell
$ git branch
* dev
  master
```

`git branch` 命令会列出所有分支，当前分支前面会标一个 `*` 号。对新分支的改动不会影响原来的分支，当新分支的工作完成后，就可以切换回原来的分支：

```shell
$ git checkout master
Switched to branch 'master'
```

然后，把 `dev` 分支的工作成果合并到 `master` 分支上：

```shell
$ git merge dev
Updating d46f35e..b17d20e
Fast-forward
 readme.txt | 1 +
 1 file changed, 1 insertion(+)
```

合并后，`readme.txt` 的内容就和 `dev` 分支的最新提交是完全一样的。合并完成后，就可以放心地删除 `dev` 分支了：

```shell
$ git branch -d dev
Deleted branch dev (was b17d20e).
```

## Git 版本库的远程仓库

这一小节的远程仓库的托管方是指 GitHub。本地 Git 仓库和 GitHub 仓库之间的传输是通过 SSH 加密的，请按照如下步骤进行设置：

- 创建 SSH Key。在用户主目录下，查找有没有 `.ssh` 目录，如果有，再查找这个目录下有没有 `id_rsa` 和 `id_rsa.pub `这两个文件。如果已经有了，可直接跳到下一步。如果没有，打开 Shell 创建 SSH Key：

  ```shell
  $ ssh-keygen -t rsa -C "youremail@example.com"
  ```

  `id_rsa` 是私钥，不能泄露出去，`id_rsa.pub` 是公钥，可以放心地告诉任何人。

- 点击 [Account Settings - SSH keys](https://github.com/settings/keys) - Add SSH key，填上任意 Title，在 Key 文本框里粘贴 `id_rsa.pub` 文件的内容，然后 Add Key 即可；

- 假定你有若干电脑，只要把每台电脑的 Key 都添加到 GitHub，就可以在每台电脑上往 GitHub 推送了。

### 创建一个新的 GitHub 远程仓库

尝试在你的 GitHub 账号下创建一个名为 learn_git 的 **空白** 远程仓库。

具体步骤请大家自行探索完成。

### 将一个空的 GitHub 远程仓库与本地仓库关联

???+ warning "注意"

    learn_git 仓库不是实验需要提交的 osh-2024-labs 仓库，仅供自行练习。

在本地的 `learn_git` 仓库下运行命令：

```shell
$ git remote add origin git@github.com:OSH-TA/learn_git.git
```

请注意把上面的 `OSH-TA` 替换成你自己的 GitHub 账户名，添加后，远程库的名字为 `origin`，这是 Git 默认的叫法。下一步，就可以把本地库的所有内容推送到远程库上：

```shell
$ git push -u origin master
Counting objects: 20, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (15/15), done.
Writing objects: 100% (20/20), 1.64 KiB | 560.00 KiB/s, done.
Total 20 (delta 5), reused 0 (delta 0)
remote: Resolving deltas: 100% (5/5), done.
To github.com:michaelliao/learn_git.git
 * [new branch]      master -> master
Branch 'master' set up to track remote branch 'master' from 'origin'.
```

把本地库的内容推送到远程，用 `git push` 命令，实际上是把当前分支 `master` 推送到远程。

由于远程库是空的，我们第一次推送 `master` 分支时，加上了 `-u` 参数，Git 不但会把本地的 `master` 分支内容推送的远程新的 `master` 分支，还会把本地的 `master` 分支和远程的 `master` 分支关联起来，在以后的推送或者拉取时就可以简化命令。推送成功后，可以立刻在 GitHub 页面中看到远程库的内容已经和本地一模一样。从现在起，只要本地作了提交，就可以通过命令：

```shell
$ git push origin master
```

把本地 `master` 分支的最新修改推送至 GitHub。

### 从已有的 GitHub 仓库克隆到本地

用命令 `git clone` 克隆一个本地库：

```shell
$ git clone git@github.com:OSH-TA/gitskills.git
Cloning into 'gitskills'...
remote: Counting objects: 3, done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 3
Receiving objects: 100% (3/3), done.
```

如果有多个人协作开发，那么每个人各自从远程克隆一份就可以了。

### 协作时，将新的远程更改合并到本地

多人协作时，大家都会往分支上推送各自的修改。现在，模拟一个你的小伙伴 A 也参与你们的项目。如果 A 已经向远程分支推送了他的提交，而碰巧你也对同样的文件作了修改，并试图推送：

```shell
$ cat env.txt
env
$ git add env.txt
$ git commit -m "add new env"
[dev 7bd91f1] add new env
 1 file changed, 1 insertion(+)
 create mode 100644 env.txt
$ git push origin dev
To github.com:OSH-TA/learn_git.git
 ! [rejected]        dev -> dev (non-fast-forward)
error: failed to push some refs to 'git@github.com:OSH-TA/learn_git.git'
hint: Updates were rejected because the tip of your current branch is behind
hint: its remote counterpart. Integrate the remote changes (e.g.
hint: 'git pull ...') before pushing again.
hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

推送失败，因为你的小伙伴的最新提交和你试图推送的提交**有冲突**，Git 提示我们先用 `git pull` 把最新的提交抓下来，然后，在本地合并，解决冲突，再推送。

多人协作的工作模式通常是这样：

1. 首先，可以试图用 `git push origin <branch-name>` 推送自己的修改；
2. 如果推送失败，则因为远程分支比你的本地更新，需要先用 `git pull` 试图合并；
3. 如果合并有冲突，则解决冲突，并在本地提交；
4. 没有冲突或者解决掉冲突后，再用 `git push origin <branch-name>` 推送就能成功！

## 练习

- Git 如何做到在不同提交状态快速切换的？可参考 [Git 教程](https://www.liaoxuefeng.com/wiki/896043488029600)。

- 学习如何用精简的语言描述一次 commit。可以从分类的角度考虑，如：

  ```
  docs: add finial report
  feat: add fork
  fix: typo
  ...
  ```

- **「必做」**在你自己的账号下（而不是 OSH-2024 组织）创建一个新的、**私有的** GitHub 远程仓库，将其命名为 osh-2024-labs，然后邀请三位助教（id 分别为：Odeinjul，SproutNan，IrisesD）成为这个仓库的共同贡献者（collaborators），以便后续实验的批改。

???+ question "我现在需要提交什么？"

    初始建立空仓库即可。具体提交内容请见下一小节。
