---
title: Hexo博客搭建总结
date: 2020-01-31 16:37:56
tags:
---
# Hexo博客搭建总结
去年9月份搭建了自己的第一个Hexo框架的博客，在使用过程中，遇到了台式机和笔记本两台设备怎样写博客的问题。根据网上的教程，为Git仓库新建了一个用来编辑的分支。由于对Hexo框架不太了解，文件结构被自己搞得有点乱。由于病毒疫情不能出门，这次假期稍显无聊，便想起来把博客文件结构整理一下，结果搞成了404，于是决定趁机重新部署。读了一遍[Hexo文档](https://hexo.io/zh-cn/docs/)之后，觉得以前遇到的问题都可以容易地解决了。
总结一下主要的问题有：

- [x] Hexo博客多设备同步
- [x] Git多账号管理
- [x] Hexo博客多平台一键部署
- [x] Markdown写作插图

## 1.Hexo博客多设备同步
> hexo文档推荐的解决方案：建立两个分支，一个用于部署，一个用于同步博客文档。

首次安装参考[Hexo文档](https://hexo.io/zh-cn/docs/)即可顺利进行。

接下来的工作是为了多设备同步：

**1.新建分支**

在Github远程仓库新建分支（我命名为*hexosync*分支，并设为default），用于同步编辑博客所需的全部文件。

**2.复制文件**

clone *hexosync*分支到本地，把除.git之外的文件删除，将安装博客的文件（除.deploy_git。node_modules也不必复制，反正会重装）复制到当前hexosync分支的仓库内，如图：
{% asset_img 复制文件.png %}

**3.修改参数**

根据自己情况，比如需要同时部署到GitPages和Gitee,修改.gitignore,_config.yml的部分配置参数。没这些需求就不改，也完全可以。

> **注意：**_config.yml里面deploy参数，分支应为master。

有一个小插曲，我遇到一个报错，是YAML的转义字符。要在单引号`''`内使用单引号`'`，比如`'Welcome to Wei's blog.'`，在YAML语法中需要写为`'Welcome to Wei''s blog.'`

**4.更换设备后**

更换设备后，将远程仓库`clone`到本地，执行`npm install hexo`、`npm install`、`npm install hexo-deployer-git`(安装部署插件)，就可以编辑、更新博客了。

每次更新博文后，执行`git add .`、`git commit -m "..."`、`git push origin `提交网站相关的文件；执行`hexo g -d`生成网站并部署到GitHub上。
参考[使用hexo，如果换了电脑怎么更新博客？ - CrazyMilk的回答 - 知乎](
https://www.zhihu.com/question/21193762/answer/79109280)。


## 2.Git多账号管理
因为我有一个Github账号，一个实验室公用仓库的Gitee账号，一个Gitee个人用账号，共三个账号，在设置时容易引发冲突。

参考[Git配置多个SSH-Key](https://gitee.com/help/articles/4229#article-Kheader0)，为每个账号创建了SSH-Key，并在 ~/.ssh 目录下新建一个config文件，添加如下内容:
```
# github
Host github.com
HostName ssh.github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/github_id_rsa
# gitee
Host gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_id_rsa
# gitee
Host wyh.gitee.com
HostName gitee.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/gitee_wyh_rsa
 ```
 其中Host和HostName填写git服务器的域名，IdentityFile指定私钥的路径，Host不能重复，但可以指向同一个HostName,因此给第二个gitee Host命其他名。

 ## 3.Hexo博客多平台一键部署
 因为考虑到在国内，Gitee Pages要比Github Pages访问速度快得多，便将博客同步部署到Gitee一份。

我实践了两种办法：

**方法一、同步Github仓库到Gitee**

首先，导入Github仓库到Gitee，码云和Github的导入和同步参见：[GitHub仓库快速导入Gitee及同步更新](https://gitee.com/help/articles/4284)。

然后，每次部署完之后，同步一次就可以。
  > 注意：Gitee Pages服务需要在仓库`服务`选项中手动开启，如果博客显示404，检查是否忘了开启。（比较坑的是，需要绑定手机号。）
  > {% asset_img giteepages服务.png %} 

**方法二、`hexo d`一键部署到两个平台**

 在博客的配置文件`_config.yml`中，添加：
 ```
 deploy:
- type: git
  repo: https://github.com/.../xxx.github.io.git
  branch: master
- type: git
  repo: https://gitee.com/xxx/xxx.git
  branch: master
  ```
  将repo改为自己的仓库地址。

  运行```hexo d```命令后，将依次部署到Github Pages和Gitee Pages。


由于Gitee仓库名称和自己的个性地址不一致，会导致一些资源404，具体解决方法见[码云Pages](https://gitee.com/help/articles/4136)。

还有一个小插曲，部署时，Gitee提示权限错误：
  {% asset_img gitee权限报错.png %} 
发现错误原因是之前保存了实验室Gitee账号的账号密码，在控制面板\用户帐户\凭据管理器将其删除即可。
  {% asset_img Hexo博客搭建总结/管理凭据.png %}

然后重新执行`hexo d`就可以成功部署两个平台了：
{% asset_img gitee权限成功.jpg %}

## 4.Markdown写作插图
由于写博文的时候，需要插入图片，利用网上的图床总有这那的缺点，因为图片不多，干脆直接放到文章对应的资源文件夹里。文章资源文件夹可以在`_config.yml`中修改配置`post_asset_folder`为`true`：
```
post_asset_folder: true
```
在执行`$ hexo new post_name`命令后，会自动生成同名文件夹，~~将图片放在其中利用相对路径访问即可（参考[Hexo博客搭建之在文章中插入图片 - yanyinhong的个人博客](https://yanyinhong.github.io/2017/05/02/How-to-insert-image-in-hexo-post/)）~~。
由于Hexo 3.0版本以后，使用相对路径的常规 markdown 语法`![](/example.jpg)`，图片将不会正常显示，因此采用官方推荐的标签插件`{% asset_img example.jpg This is an example image %}`，不过这样在写MarkDown的时候，无法实时预览，感觉有点值得吐槽。
