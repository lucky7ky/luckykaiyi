---
published: true
layout: post
title: "如何在Mac上搭建 PHP 环境？"
date: 2016-04-16
---

看了一点点 PHP 入门教程，觉得光看语法有点走马观花，还是得搭建一下环境起码在本地 `hello world` 一下。

看了网上的介绍，有推荐用 homestead、MAMP 等等的，有吐槽在本机上自己配环境会留下一些坑的，不过仅仅就现在自己的需求而言，最简单直接的方式还是使用 Mac 上自带的 Apache 和 PHP。

那么，快速搭建一下本地 PHP 开发环境吧 →

### 记录一下搭建的主要步骤 

**版本 ：Mac OS X 10.10.5**

### 1.启动 Apache  

由于 Mac 上自带 Apache，所以只要启动对应的服务就可以。
在 Mac 终端输入命令：`sudo apachectl start` 启动 Apache 服务。

```
🌺  Apache 常用命令
//启动 Apache 服务
sudo apachectl start
// 重新启动 Apache 服务
sudo apachectl restart
// 关闭 Apache 服务
sudo apachectl stop
// 查看 Apache 版本
httpd -v
```
启动后，在浏览器中输入 `localhost`，可以看到页面上显示 It works。  
它对应的是 `/Library/WebServer/Documents/` 下的 `index.html.en` 文件。

✨ `/Library/WebServer/Documents/` 是 Apache 的默认根目录。


### 2.启动 PHP

在 Mac OS 中内置了 PHP，只需在 Apache 的配置文件中添加 Apache 对 PHP 的支持就可以。  

- 输入 `sudo vim  /etc/apache2/httpd.conf` 命令打开 `http.conf` 配置文件。

- 找到以下代码并去掉注释（去掉前面的‘#’号）, `wq` 保存退出。
`LoadModule php5_module libexec/apache2/
libphp5.so`

### 3.重启 Apache

运行 `sudo apachectl restart` ，这样就可以在 Apache 的根目录
`/Library/WebServer/Documents/`下新建 `.php` 文件进行调试。

### 其他
 
#### 权限问题 → chown 一下 

正常情况到上面步骤为止就基本OK啦。但是由于我的 Mac 上 `/Library/WebServer/Documents/` 目录的owner是root，所以不能直接在根目录下新建 `.php` 文件。  
所以，还需要改一下权限问题。具体操作如下：


- 在这个根目录下 `mkdir project` , 新建名为 `project` 的文件夹专门存放 `.php` 文件。  
这里新建文件夹，只change这个文件夹的owner，而不改动根目录下其他文件的owner。

- 修改 `project` 文件夹的owner。    
`sudo chown zhangkaiyi:staff project`    
这样 `project` 文件夹的owner就是我自己的账户了。  
就可以在里面新建 `.php` 文件。

- 测试一下，在 `project` 里新建 `test.php`。

```
<!DOCTYPE html>
<html>
    <body>
    <?php
        echo "Hello World!";
    ?>
    </body>
</html>
```
此时，在浏览器打开 `http://localhost/project/test.php` 就可以正常看到 `Hello World!` 啦~

#### 关于建立软链接

到上面的步骤，其实就可以简单进行开发了。但是呢萌生了个小需求，就是我的 `Desktop` 里有个 `practice` 文件夹，里面会分类放一些平时简单小练习和测试用代码。所以其实我是想在这个 `practice` 目录下建立一个 `PHP` 文件夹，专门存放像刚刚 `test.php` 之类的文件的。
  
但是要运行 `.php` 文件又得将其置于 Apache 的根目录下。  
所以可以在刚才 Apache 根目录建立的 `project` 文件夹中，  
建立一个软链接， 命令如下：

`ln -s /Users/zhangkaiyi/Desktop/practice/PHP .`

然后，切换到 `Desktop/practice/PHP` 下新建一个和上述一样的 `test.php` (或者直接把之前的文件 `mv` 过去）

在浏览器中打开 `http://localhost/project/PHP/test.php`
此时页面上显示

```
Forbidden

You don't have permission to access /project/PHP/
test.php on this server.

```

查了一下发现是因为在当前的权限设置下，Apache 没有权限读取 `/Users/zhangkaiyi/Desktop` 里的内容，此时 `Desktop` 对应的读写权限是酱紫：`drwx------+`。  

所以，需要改一下读写权限设置：  
运行命令`chmod 755 /Users/zhangkaiyi/Desktop`, 此时 `cd` 一下切换到home目录下再次 `ll` 就可以看到 `Desktop` 的读写权限已经改为 `drwxr-xr-x+`, 这时的 Apache 可以对 `Desktop` 进行 `r-x` 即读和执行的操作。

再次打开 `http://localhost/project/PHP/test.php` 就能看到 `Hello World!` 啦 ~ ~









