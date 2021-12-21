# GIT

## 一、git基本概念

### 1、blob and tree

git 将文件目录用两种数据类型组成，分别是tree(folder)和blob(file)，tree包含tree或者blob，也就是说，文件目录是tree递归形成的。git项目的根目录用 ‘/’ 表示，也是一个tree。

用伪代码形式表示

```tex
type blob = array<byte>
type tree = map<string, [blob | tree]>
```

```tex
/(root)
|
|--foo(tree)
|    |
|    |--bar.txt(blob)
|
|--baz.txt(blob)
```

以上表示了一个简单项目的git结构，root tree包含一个tree和一个blob，而tree又包含一个blob。

### 2、snapshot

git可以很好地进行版本控制，因为它可以对每个修改拍摄快照，通过快照，你可以回到任何一个之前的节点，每个快照都包含一个完整的git项目。通过快照你可以找到它的父节点。

```tex
      o <---------- o <----------- o <------------- o
                       ^               /
                        \             /
                          o <------/
```

快照也可以很方便地进行协作开发，例如，你要开发一个新功能，但又要解决已存在的bug，这时候就可以fork一下，分别做两件事，最后通过git合并。git合并是自动完成的，如果它不确定，它会提交给开发者看来决定。例如，有两个人分别对一个文件的同一行进行了修改，但不相同，他会让你决定保留哪一项修改，再例如，在相同的下面添加了不同的内容，git不知道如何调整顺序，谁的放在前面，它会让你决定。

### 3、commit

除此之外，还有commit数据类型，它是git提交时产生的对文件的描述文件，它是一个struct结构，包含作者、时间戳、描述等信息

```tex
# 用伪代码表示
type commit = struct {
	parents: array<commit>
	author: string
	message: string
	snapshot: tree
	...
}
```

```tex
references = map<string, string>

def update_reference(name, id):
    references[name] = id

def read_reference(name):
    return references[name]

def load_reference(name_or_id):
    if name_or_id in references:
        return load(references[name_or_id])
    else:
        return load(name_or_id)
```

### 4、object and sha-1

git在磁盘中存储这些数据，必须有一个对象存储它。所以git定义了一个object类，用map(字典)存储一系列的object对象。通过这些对象就可以回溯之前每一个提交。

```tex
type object = [blob | tree | commit]
objects = map<string, object>
```

那么如何存储和找到这些object呢，git通过object的key来获取object，那么如何决定key的值呢？

通过哈希函数，git定义了两个函数，分别用来生成哈希并存储object和通过哈希值获取object。

```tex
def store(o) {
	id = sha1(o);
	objects[id] = o;
}

def load(id) {
	return objects[id];
}
```

> 这个哈希值是通过一定的算法，将object转化为一个160位的二进制数，如果用十六进制表示就是40位，这也是在实际使用中看到的。

### 5、references

但是，这么长的一串字符对于我们理解和记忆具体提交了什么内容毫无意义，所以git定义了一个references，以人能理解的字符代替这么长的哈希值。

```tex
type references = map<string, string>
```

## 二、命令演示

### 1、initialize

首先创建一个目录作为项目的根目录，然后进入

```bash
mkdir demo
cd demo
```

然后使用命令将其初始化为git目录

```bash
git init
```

这个命令会创建 .git 目录，有以下内容

```tex
drwxrwxr-x  8 zhu521 zhu521 4.0K May 26 18:58 ./
drwxrwxr-x  3 zhu521 zhu521 4.0K May 26 18:26 ../
drwxrwxr-x  2 zhu521 zhu521 4.0K May 26 17:16 branches/
-rw-rw-r--  1 zhu521 zhu521  269 May 26 18:26 COMMIT_EDITMSG
-rw-rw-r--  1 zhu521 zhu521   92 May 26 17:16 config
-rw-rw-r--  1 zhu521 zhu521   73 May 26 17:16 description
-rw-rw-r--  1 zhu521 zhu521   23 May 26 17:16 HEAD
drwxrwxr-x  2 zhu521 zhu521 4.0K May 26 17:16 hooks/
-rw-rw-r--  1 zhu521 zhu521  176 May 26 18:26 index
drwxrwxr-x  2 zhu521 zhu521 4.0K May 26 17:16 info/
drwxrwxr-x  3 zhu521 zhu521 4.0K May 26 17:21 logs/
drwxrwxr-x 13 zhu521 zhu521 4.0K May 26 18:26 objects/
drwxrwxr-x  4 zhu521 zhu521 4.0K May 26 17:16 refs/
```

objects 和 refs 目录里存放了所有存储库数据

### 2、help and status

#### 2.1、help

```bash
git help [command]
```

这个命令可以查看所有命令的帮助

#### 2.2、status

```bash
git status
```

告诉你项目处于什么状态

### 3、提交

#### 3.1、git 工作区域

从上到下依次有三个层次

1. Repositories
2. Staging area
3. work area

### 4、command-line

#### 4.1、add and commit

1. add

```bash
git add <file_name>
```

将文件提交到暂存区，这样你就可以添加多个文件到暂存区，然后一次性提交到仓库。

2. commit

```bash
git commit
```

将暂存区的文件提交到仓库，然后你需要编写commit内容。

> 好的commit内容可以帮助你更好地回顾版本。可以参考commit标准。
>
> https://tbaggery.com/2008/04/19/a-note-about-git-commit-messages.html

提交完成后，会给出哈希值的一部分，可以根据这个哈希值找到所有存储数据

```tex
➜  demo git:(master) git cat-file -p 1df5c162
tree 68aba62e560c0ebc3396e8ae9335232cd93a3f60
author zhuaowei <z2845191711@163.com> 1622020851 +0800
committer zhuaowei <z2845191711@163.com> 1622020851 +0800

Add hello.txt
➜  demo git:(master) git cat-file -p 68aba62e560c0ebc3396e8ae9335232cd93a3f60
100644 blob 3b18e512dba79e4c8300dd08aeb37f8e728b8dad    hello.txt
➜  demo git:(master) git cat-file -p 3b18e512dba79e4c8300dd08aeb37f8e728b8dad
hello world
```

3. log cat-file

```bash
# 查看所有提交记录，最新的显示在上面
git log

# 查看提交的commit和blob内容
git cat-file -p <sha-1>
```

> 使用 git add 和 git commit 的好处是我可以只提交我修改的那部分，而不是将整个项目打包然后拍摄快照。

```bash
# 以图形化的形式展示历史记录
git log --all --graph --decorate
```

#### 4.2、checkout

git checkout 可以让你回到任一历史记录中

```bash
# 这个命令可以让你回到哈希值指向的快照
git checkout <sha-1>

# 如果你对文件进行修改，但是并没有提交，使用checkout会报错
# 并提示已修改的文件
# 使用 -f 强制执行，慎用，因为这回导致你放弃所有当前修改的内容，再也回不到这里
git checkout -f <sha-1>
```

在使用这个命令后查看log，你会发现一些不同。

HEAD 和 master的位置会发生改变，HEAD会指向你当前的快照，而master是一个分支，是创建项目创建的主分支，事实上，在你工作的大部分时候都是只有这一个分支。master始终指向该分支的最新提交记录。

master其实是一个 reference，就像之前提到的，它是哈希值的别名，可以使用它转到最新的主分支。

```bash
git checkout master
```

#### 4.3、diff

这个命令可以比较任何两个快照和当前工作区内容的变化

```bash
git diff [option[ other]] <filename>
```

> 这个命令默认是比较当前工作区和HEAD所指向的快照的区别
>
> 当然你也可以添加参数比较其他的快照之间的
>
> git diff HEAD master <filename>
>
> git diff 1df5c162b hello.txt

#### 4.4、branch

分支可以让你并行完成两件事，当你分别完成这两件事情时，可以将其合并到master分支。

1. branch

```bash
git branch
```

这个命令会展示所有分支，并且标注你所在的分支

```bash
git branch -vv
```

这个命令除有 <code>git branch</code> 的功能外，还会展示额外信息，例如分支的哈希值和提交信息。

```bash
git branch <name>
```

在当前位置建立一个分支。

2. checkout

```bash
git checkout <name>
```

切换到目的分支。

```bash
git checkout -b <name>
```

创建分支并切换到该分支。

```bash
git log --all --graph --decorate --oneline
```

与 <code>git log --all --graph --decorate</code> 不同的是，这个命令会显示更少信息，每个快照只显示一行，看起开更精简。

#### 4.5、merge

当你全部完成并行的两个分支后，可以使用merge合并到一起。我们并不能直接使用 git merge cat dog直接将两个分支合并到master，需要先合并其中一个分支，然后再合并另一个。

```bash
# 将cat分支合并到当前分支
git merge cat
```

> 因为merge是将分支合并到当前分支，所以合并之前要看清自己所在的分支和快照。

合并时可能会产生冲突，git会在冲突的地方对文档进行标注。

```bash
# 这两个不是成对使用的
git merge --abort
git merge --continue
```

使用 <code>git merge --abort</code> 中断合并，然后项目会回到合并前的状态。

一般情况下，使用 解决冲突的工具 (e.g. mergetool vimdiff)解决冲突（不会）。

特殊情况下，我们可以手动将冲突解决，删除冲突标记，然后使用 git merge --continue 告诉git我们已经解决了冲突，再将修改的文件添加到暂存区，再次使用 git merge --continue 会进入边 commit 界面，填写commit信息后就会提交，这次是真正的解决冲突，这个时候 git 的分支就会合并到一起，并创建一个新的快照。

#### 4.6、remote

1. init

在拷贝远程库之前需要做些准备。

```bash
git init # 创建普通库
git init --bare #创建裸库
```

与 git init 的区别是，它创建的是裸库，虽然它会创建 .git 文件夹，但是你没有权限进行修改和删除，但是你仍然可以进行读取操作。这是在多人共享时用到的，通常这个文件夹用 .git 作为后缀，例如：example.git。

实际上，它不会创建 .git 文件夹，而是将所有的 .git 下的文件直接放到项目的根目录。

2. remote

```bash
git remote
git remote add <remote> <url>
```

git remote 会列出所有你配置过的远程仓库

git remote add <remote> <url> 添加一个远程仓库的配置，这个url可以是一个git仓库连接，也可以是一个本地文件夹的路径。之后可以将本地仓库 push 到远程仓库

3. push clone

```bash
git push <remote> <local branch>:<remote branch>
git clone <url> <folder name>
git clone --shallow <url> <folder name>
```

第一个命令是将本地仓库 push 到远程仓库，第二个命令是将远程仓库克隆到本地。

使用push命令前后可以使用 <code>git log --all --graph --decorate --oneline</code> 查看分支如何变化。

在push之后，会在push的节点多出一些信息，这些信息显示了远程仓库指向的节点。

3. upstream

```bash
git branch --set-upstream-to=origin/master
```

本地库的所有修改不会影响远程，只有再次push才可以。不过有一种简单的方式不用每次都push，就是将远程设置为上游.

这时候用 <code>git branch -vv</code> 可以看到origin已经作为本地的一个分支。之后再push的时候只需要使用 git push即可自动push到origin/master.

4. fetch pull

```bash
git fetch
git pull
```

<code>git fetch</code> 会下载远程库到本地，并不会覆盖本地，而是作为一个分支，会检索本地与远程库是否相同。然后可以使用 <code>git merge</code>将其与本地合并。<code>git fetch</code> 默认会同步所有远程仓库的 master 分支，可以使用 <code>git fetch <remote> <branch></code> 指定远程库及其分支。

git pull == git fetch; git merge

### 5、config

```bash
git config
vim ~/.gitconfig
```

git config 会显示当前的git配置。

git的默认配置在 ~/.gitconfig 中。

