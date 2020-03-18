> 记录自己开发过程中遇到的问题

### 切换原因

在进行push代码时候，报错：413 Request entity too large，由于推送的代码体量有四百多兆，远程服务直接挂起，所以尝试将http协议切换成ssh进行尝试解决。如图：

![报错内容](/Users/dush/github笔记/随笔/开发日常/202003/image/errormessage.png)

## 开始进行尝试

背景：项目组使用的git服务器是gerrit，关于它的介绍在这里 [Gerrit Code Review for Git](<https://gerrit-documentation.storage.googleapis.com/Documentation/3.1.3/index.html#_guides>) 。

由于是我自己的电脑，我也拥有其他远程仓库（私人项目），如gitee、github，于是在码云代码托管平台上面找到了 [Git配置多个SSH-Key](<https://gitee.com/help/articles/4229>) 的步骤：

1. 分别生成对应的SSH-Key
2. ~/.ssh/ 目录下配置config内容
3. 验证测试

Gerrit 大体配置相同，最后一步有所差别，github、gitee验证测试是：

```objective-c
$ ssh -T git@gitee.com
$ ssh -T git@github.com
```

而gerrit需要加上端口号，用户名：

```objective-c
$ ssh -p 29418 sshusername@hostname

    ****    Welcome to Gerrit Code Review    ****

    Hi John Doe, you have successfully connected over SSH.

    Unfortunately, interactive shells are disabled.
    To clone a hosted Git repository, use:

    git clone ssh://sshusername@hostname:29418/REPOSITORY_NAME.git

  Connection to hostname closed.
```

验证测试成功之后，

最后一步，直接将本地项目``.git`` 文件夹下面的config文件中的url配置修改为ssh方式

这样切换的工作就完成了：）