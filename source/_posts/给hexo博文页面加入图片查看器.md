---
title: 给hexo博文页面加入图片查看器
date: 2020-02-27 23:32:04
tags:
- 博客
- 总结
categories: 
- 博客总结
---
在hexo博客中，Markdown生成的界面无法对图片进行放大等操作，不便于阅读。通过引入viewerjs图片阅读器，可以实现点击图片进行放缩等功能。
---
<!-- more -->
今天在手机上看博客时，我发现博文中插入的脑图无法放大，根本看不清。使用电脑时可以直接放缩页面，但在因为适配了移动端，在手机上无法对页面放缩。
如下图：
{% asset_img article-image 原生Markdown生成的界面无法对图片操作.jpg 480 480 原生Markdown生成的界面无法对图片操作 %}

我在网上搜了一下解决办法，目前好像没有特别好用的插件，看到[为你的Hexo博客添加图片查看器](https://blog.dazo66.com/2018/10/11/viewerjstutorial/)这篇博客，他使用了viewerjs插件，实现的功能正是我所需要的。于是晚上对自己使用的主题配置了一下，加入了viewerjs图片查看插件，点击页面上的图片，即可进行放缩等功能，见下图：
{% asset_img article-image 移动端浏览器使用viewerjs图片查看器的效果.jpg 480 960 移动端浏览器使用viewerjs图片查看器的效果 %}

## 1.安装viewerjs
[viewerjs](https://github.com/fengyuanchen/viewerjs)是一个JavaScript写的图片查看器，可以在网页中查看图片。
在博客根目录下运行：
```
npm install viewerjs
```
安装成功后可以在npm安装路径下`\node_modules`找到`viererjs`。

## 2.导入viewerjs
### （1）在主题模板中引入
如果要在浏览器中使用viewerjs图片查看器，需要在页面上导入：
```
<link  href="/path/to/viewer.css" rel="stylesheet">
<script src="/path/to/viewer.js"></script>
```
但是使用hexo主题，自动生成静态网页，需要在主题模板中导入。我使用的[ocean主题](https://github.com/zhwangart/hexo-theme-ocean)在`head.ejs`的`<head></head>`中，加入：
```
  <!-- 引入图片查看器库类 -->
  <%- css('css/viewer.min.css') %>
  <%- js('/js/viewer.min.js') %>
```

### (2)复制css和js文件
由于hexo主题资源下并没有这两个文件，需要从npm的安装目录(\node_modules\viewerjs\dist)找到viewer.min.css，viewer.min.js这两个文件，分别复制到主题的js和css文件夹中。我使用的[ocean主题](https://github.com/zhwangart/hexo-theme-ocean)在
```
\themes\ocean\source\css
\themes\ocean\source\js
```
这两个文件夹下。
这样，在执行`hexo g`生成页面时，`<head></head>`部分会自动加上：
```
<link  href="/path/to/viewer.css" rel="stylesheet">
<script src="/path/to/viewer.js"></script>
```
### （3）在主题模板中写入js代码
`.getElementsByClassName('class-name');`通过读取HTML标签，可以将所有ClassName为'class-name'的图片获取， [Dazo66's Blog](https://blog.dazo66.com/2018/10/11/viewerjstutorial/)针对hexo博客写了一个完整的配置，我将他的代码引用贴在这里：
```
<!-- 图片查看器实例配置 -->
<script type="text/javascript">
  //默认设置， 可以根据个人需求和喜好进行配置
  //详细参考官方说明
  Viewer.setDefaults({
      //设置初始缩放 default: 1
      zoomRatio : [0.5],
      //设置滚轮缩放比率 default: 0.1
      show: function () {
        this.viewer.zoomTo(0.5);
      },
    });
  //获得content中所有的图片， 不同主题可能有所不同
  //为了和其他的图片区别开来 所以在markdown中插入图片的时候使用独特的记号
  //为了一次性得到所有的图片我这里采用的是class = 'article-image'
  var article = document.querySelector('.post-content');
  var imageList = article.getElementsByClassName('article-image');
  //将获取到的HTMLCollections转化成Array
  var imageArray = new Array();
  Array.prototype.forEach.call(imageList, element => {
    imageArray.push(element);
  });
  //设置每个图片成为图片组
  Array.prototype.forEach.call(imageList, element => {
    var viewer1 = new Viewer(element);
    viewer1.images = imageArray;
    viewer1.length = imageArray.length;
  });
</script>
```
将这段代码放入`article.ejs`代码最后，这样在生成博客页面时，就自动加入到页面代码中了。
在上面的配置中，会一次性将所有`class=artical-image`的图片读入列表，便于使用viewerjs查看器查看，在写博客时，只需在标签中指明`class="article-image"`即可，比如我在插入本文第一张图时，Markdown应写为：
```markdown
{% asset_img article-image 原生Markdown生成的界面无法对图片操作.jpg 480 480 原生Markdown生成的界面无法对图片操作 %}
```

---
本文参考：
- [Dazo66's Blog](https://blog.dazo66.com/2018/10/11/viewerjstutorial/)
- [viewerjs](https://github.com/fengyuanchen/viewerjs)