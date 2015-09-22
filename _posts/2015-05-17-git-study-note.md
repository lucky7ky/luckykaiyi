---
published: true
layout: post
title: "Git 常用命令手册"
date: 2015-05-17
---

<center>
 <img src="http://7xloce.com1.z0.glb.clouddn.com/515b86be7dbb921080c193fa50c4065f.jpg" style="width:200px"/>
</center>
### 🌀 克隆仓库  
比如  
`git clone git@github.com:lucky7ky/practice.git`  
### 🌀【添加、提交、推送】三步曲  
🌺 首先，了解**工作流**    
本地仓库由 git 维护的三棵“树”组成。第一个是工作目录，它持有实际文件；第二个是暂存区（Index），它像个缓存区域，临时保存改动；最后是 HEAD，它指向最后一次提交的结果。    

🌲 **git add**     
提出更改（把它们添加到暂存区），使用如下命令：

`git add <file>`  
`git add *`  

🌲 **git commit**  
使用如下命令以实际提交改动： 
      
`git commit -m "代码提交信息"`   
 
这样改动就提交到了本地仓库的 HEAD  

将 **git add** 和 **git commit** 两步结合起来，可以使用如下命令  

`git commit -a -m "代码提交信息"`

🌲 **git push**   
将改动推送同步到远端仓库，执行如下命令：    
  `git push` 等价于

+ `git push origin master`
+ 或者 `git push origin master:master`  

### 🌀 分支 
在创建仓库的时候，master 是默认的分支。在其他分支上进行开发，完成后再将它们合并到主分支上。
     
+ 查看所有分支 `git branch -a`
+ 新建分支 `git branch <file>`
+ 切换分支 `git checkout <file>`  
（切换回主分支 `git checkout master`)
+ 新建并切换分支 `git checkout -b <file>`
+ 删除分支 `git branch -d <file>`

### 🌀 更新与合并
+ 更新本地仓库至最新改动，执行`git pull`
+ 合并其他分支到当前分支（例如 master），执行：  

`git merge <branch>`

在这两种情况下，git 都会尝试去自动合并改动。遗憾的是，可能出现冲突（conflicts）。 这时候就需要修改这些文件来手动合并这些冲突（conflicts）。改完之后，需要执行如下命令以将它们标记为合并成功：  `git add <filename>`  

🌲在合并改动之前，可以使用如下命令预览差异：  

`git diff <source_branch> <target_branch>`
### 🌀 discard changes
`git checkout -- <file>`
### 🌀 回滚
`git reset <commit_id>`
回滚到某个commit状态，但是要慎用。

### 🌀 查看远程仓库  
方法一: `git remote -v`   
方法二：打开 `.git/config`文件查看

### 🌀 SSH keys
在**home**目录下，`ls -al`显示所有文件，打开 `.ssh`文件夹：  
其中 【私钥】：`xx_rsa`;【公钥】：`xx_rsa.pub`。



📒参考资料
[git--简明指南] 、[Git Tutorial]

  


[git--简明指南]:http://rogerdudler.github.io/git-guide/index.zh.html
[Git Tutorial]:https://blog.udemy.com/git-tutorial-a-comprehensive-guide
