---
title: 不在学校如何使用Zotero和知网
date: 2020-05-08 12:02:47
tags: Zotero
categories: Tools
---

添加代理设置，让 Zotero 能够识别通过 VPN 访问的知网文献。

<!-- more -->

自从师兄那里得知[Zotero](https://www.zotero.org/) 这个开源的文献管理软件，便弃坑 EndNote。配合坚果云的免费存储空间，可以随时同步文献，非常方便。但是最近在家访问知网发现 Zotero Connector 插件无法识别文献，只能保存为网页。按照论坛提供的问题自查，发现完全正常。这个时候我意识到问题很可能出在 Zotero Connector 对 URL 的识别上。  
当正常访问知网的时候，输入浏览器中的 URL 为`https://www.cnki.net/`；而当通过学校 VPN 访问时，URL 会变为`http://elib.cugb.edu.cn/https/77726476706e69737468656265737421e7e056d2243e635930068cb8/`，进入高级检索页面，URL 为`http://elib.cugb.edu.cn/https/77726476706e69737468656265737421fbf952d2243e635930068cb8/kns/brief/result.aspx`。于是猜测， Zotero Connector 无法识别代理后的链接导致了问题。[Zotero 知网 CNKI 网页识别](https://www.linxingzhong.top/posts/2019-11-5/)这篇博文证实并详细解释了原理。  
那接下来需要解决的就是如何让 Zotero Connector 识别新的链接。好在其提供了 Proxies 设置。  
{%asset_img article-image zotero代理设置.png Zotero Connector代理设置%}  
对比代理前后访问知网的 URL，可以发现主域名从<font color=ff6666>`www.cnki.net`</font>变为<font color=ff6666>`elib.cugb.edu.cn/https/77726476706e69737468656265737421e7e056d2243e635930068cb8/`</font>。
根据上图的规则，需要在 **Configured Proxies** 处添加代理配置：
<font color=0fabbc>`elib.cugb.edu.cn/https/77726476706e69737468656265737421fbf952d2243e635930068cb8/%p`</font>，由于当前缺少指向知网的域名，需要为 **Hostnames** 设置为<font color=0fabbc>`www.cnki.net`</font>。
刷新知网检索结果页面:
{%asset_img article-image 成功识别.png 成功识别文献，黄色的提示关闭即可%}  
已经可以识别出文献了,黄色的提示关闭即可。
