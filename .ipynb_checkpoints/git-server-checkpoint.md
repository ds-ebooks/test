# 测试Git服务器

## 1、Git服务器
```bash
ssh-keygen -t rsa -C "your_email@example.com"
```
1. 在用户目录创建文件`~/.ssh/authorized_keys`，把本地`id_rsa.pub`公匙添加到这个文件中就可以了。
2. 在本地用户目录创建文件`~/.ssh/config`，添加如下信息：
```bash
host ecs
   user root
   hostname pulic-ip
   port 22
   identityfile ~/.ssh/id_rsa
```
其中hostname是公网ip，`host`与之后的`git`服务器名对应。
3. 服务器端初始化一个项目
比喻在`/root/JupyterLab/REPO`下：
```bash
git init --bare test.git
```
4. 本地`clone`和`push`
```bash
git clone git-ecs:/root/JupyterLab/REPO/test.gitit
# 一系列的git add commit...
git push origin master
```
不过此时服务器端还不能直接看到文件，需要`git pull`一下，但是可以利用`Git`钩子，相当于每次`push`之后，自动执行`pull`,并将文件同步到设置的文件夹.

## 2、学会使用`Git`钩子

1. 服务器端在`/root/JupyterLab/REPO`初始化一个空仓
```
git init --bare test.git
```
2. 本地新建文件`post-receive` 并编辑：
这个路径必须事先存在
```bash
#!/bin/bash
git --work-tree=/root/JupyterLab/REPO/test checkout -f
```

添加可执行权限`chmod +x post-receive`.
3. 通过`ssh`将文件上传到服务器
```bash
scp post-receive ecs:/root/JupyterLab/REPO/test.git/hooks
```

一个简易的`Git`服务器就搭建完成了。

