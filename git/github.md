# github

## 一、基本概念

1. repository

仓库：存放代码，一个仓库对应一个项目

2. star

收藏：方便查看

3. fork

复制克隆项目：点击fork，克隆一个仓库到你的账号

fork项目是独立存在的

4. pull request

发起请求：修改后请求更改原项目

原项目人查看请求，决定是否合并到原仓库

5. watch

关注：关注项目后，如果改动会通知你

6. issue

事务卡片：发现BUG，讨论

7. GIthub主页

左侧：动态

右侧：显示所有git库

8. 仓库主页

显示项目信息：代码、版本、等等

9. 个人主页

略

### 1、创建仓库

项目名称

项目描述

公开/私有

[创建readme]

点击创建

### 2、仓库主页

修改描述

创建文件

上传文件

搜索仓库文件

克隆/下载

新建issue

关注、收藏、复制项目

### 3、仓库管理

1. 新建文件

create new file

名字、内容

添加描述、详细描述

提交文件

2. 编辑文件

点击文件名，进入详情页，可以编辑、删除文件

没提交一次都会记录

删除的文件可以点击 commits查看

3. 上传文件

点击上传文件

添加描述

4. 搜索仓库文件

快捷键T

5. 下载/检出文件

### 4、issues

发现bug，但是目前没有成型代码

1. 创建新issue

点击issue，创建issue

标题、内容

提交

修复bug后可以关闭issue

## 二、Git

### 1、git基础设置

1. 设置用户名

```shell
git config --global user.name 'itcast'
```

2. 设置用户邮箱

```shell
git config --global user.email 'itcast@itcast.com'
```

3. 查看设置

```shell
git config --list
```

### 2、git克隆操作

将远程仓库下载到本地

```shell
git clone 地址
# 地址从github复制
```

若无法同步，或没有权限

```
#打开 .git 文件加下的 config 文件，将
[remote "origin"]
	url = http://github.com/用户名/仓库名.git
修改为
[remote "origin"]
	url = http://用户名:密码@github.com/用户名/仓库名.git
```

## 三、github 搭建网站

### 1、个人网站

#### 1.1、访问

https://用户名.github.io

#### 1.2、搭建步骤：

1. 创建个人站点 -> 新建仓库（名字必须是[用户名.github.io]）

2. 在仓库下新建 index.html的文件即可

### 2、项目站点

#### 2.1、访问

https://用户名.github.io/仓库名

#### 2.2、搭建步骤

1. 进入项目主页，点击 settings
2. 在settings页面点击[launch automatic page generator] 自动生成主题页面
3. 新建站点基础设置。名称、描述、等，提交
4. 选择主题、生成

已改版

> Source的NONE那里改成master branch









