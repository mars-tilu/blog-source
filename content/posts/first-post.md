+++
date ='2026-06-25T14:12:39+08:00'
draft = false
title = 'GitHub + Hugo博客搭建' 

[cover]
image = "/blog-source/images/post/firstone.png"
alt = "GitHub+Hugo创建博客"
hidden = false # true=只在列表显示，文章内不展示

+++

##### 介绍：

    Git + Hugo 搭建个人博客性能极致，访问飞快，纯静态页面，无后端服务Hugo 本地一次性把所有页面生成 HTML、CSS、JS，部署后服务器只分发静态文件，没有数据库、接口查询，打开速度远超 WordPress、Typecho 等动态博客。搭配 Git 仓库直接部署，不需要购买云服务器、域名（也可绑定自有域名），个人使用无流量、存储限制。

###### 1.Hugo安装

打开官方发布页：

```http
https://github.com/gohugoio/hugo/releases
```

拉到最下方 Assets，找到 Windows 安装包
  下载 zip 压缩包
  放到本地并解压，配置环境变量（不懂配置请百                    度或AI）

###### 2.Github配置及安装（此处不进行详细介绍）  

##### 一、单仓库（Actions 自动打包）

###### 1.仅创建 1 个仓库blog-source，用 GitHub Actions 自动构建并部署 Pages

###### 2.创建一个项目:

```java
hugo new site myblog
```

###### 3.拉取 PaperMod 主题（子模块方式，git 同步不丢文件）

```java
git init
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
git submodule update
```

###### 4.基础配置 hugo.yaml

```java
baseURL: "https://mars-tilu.github.io/blog-source/"
languageCode: "zh-cn"
title: "我的个人博客"
theme: "PaperMod"

# 网站元信息
params:
author: "mars-tilu"
description: "使用Hugo+PaperMod搭建的个人技术博客"
keywords: ["Hugo", "博客", "技术分享", "GitHub Pages"]
DateFormat: "2006-01-02"
defaultTheme: "auto"
disableThemeToggle: false

homeInfoParams:
    Title: "欢迎来到我的博客"
    Content: "记录学习、踩坑与技术分享，持续更新中～"

ShowPostNavLinks: true
ShowBreadCrumbs: true
ShowCodeCopyButtons: true
ShowWordCount: true
ShowReadingTime: true
ShowFullTextinRSS: true

search:
    enable: true
    type: "fuse"
    fuseIndexPath: "index.json"
    searchHidden: true

toc:
    enable: true
    keepStatic: true
    close: false

# 导航菜单
menu:
main:
    - name: "首页"
    url: "/"
    weight: 1
    - name: "文章"
    url: "/posts/"
    weight: 2
    - name: "分类"
    url: "/categories/"
    weight: 3
    - name: "标签"
    url: "/tags/"
    weight: 4
    - name: "关于"
    url: "/about/"
    weight: 5

outputFormats:
RSS:
    baseName: "index"
    isPlainText: false

output:
home: ["HTML", "RSS"]
page: ["HTML"]
section: ["HTML", "RSS"]
taxonomy: ["HTML", "RSS"]
taxonomyTerm: ["HTML", "RSS"]

# 新版Hugo分页配置
pagination:
pagerSize: 8

build:
writeStats: true   
```

###### 5.新建第一篇文章

```java
hugo new content/posts/first-post.md
```
   打开文件，修改draft = false（草稿关闭才能显示），写入正文、封面图配置。

###### 6.创建关于页

```java
hugo new content/about/_index.md
```

  _index.md代表独立页面，访问/about/。

###### 7.静态资源存放（图片、背景图）

​	static/images/：文章配图、背景图，本地hugo server可直接访问，无 404

​	assets/css/：自定义样式文件

​	layouts/partials/extend_head.html：全局注入样式，修改背景、页面留白

###### 8.本地预览调试

```java
hugo server -D --gc
```

###### 9.Git 本地提交 & 推送 GitHub

​	 (1)关联远程仓库（替换为你的仓库地址）:
​    

```java
git remote add origin https://github.com/你的用户名/blog-source.git
```

​	 (2)提交全部修改

```java
git add .
git commit -m "更新文章、背景样式、页面留白优化"
git push origin main
```

###### 10.GitHub Actions 自动部署（核心，自动构建上线）

 (1)在项目根目录新建文件夹 .github/workflows/
    创建文件 deploy.yml，粘贴部署脚本，作用：推送代码后自动构建静态文件，部署到 GitHub Pages

 (2)GitHub 仓库设置：
    Settings → Pages → Build and deployment 选择 GitHub Actions
    推送代码后，仓库 Actions 自动运行，几分钟后访问 

```http
https://你的用户名.github.io/blog-source/
```

##### 补充：deploy.yml文件内容:

```java
# 基础站点配置
baseURL: "https://你的用户名.github.io/blog-source/"
languageCode: "zh-cn"
title: "我的个人博客"
theme: "PaperMod"

# 网站元信息
params:
  author: "你的用户名"
  description: "使用Hugo+PaperMod搭建的个人技术博客"
  keywords: ["Hugo", "博客", "技术分享", "GitHub Pages"]
  DateFormat: "2006-01-02"
  defaultTheme: "auto"
  disableThemeToggle: false

  homeInfoParams:
    Title: "欢迎来到我的博客"
    Content: "记录学习、踩坑与技术分享，持续更新中～"

  ShowPostNavLinks: true
  ShowBreadCrumbs: true
  ShowCodeCopyButtons: true
  ShowWordCount: true
  ShowReadingTime: true
  ShowFullTextinRSS: true

  search:
    enable: true
    type: "fuse"
    fuseIndexPath: "index.json"
    searchHidden: true

  toc:
    enable: true
    keepStatic: true
    close: false

# 导航菜单
menu:
  main:
    - name: "首页"
      url: "/"
      weight: 1
    - name: "文章"
      url: "/posts/"
      weight: 2
    - name: "分类"
      url: "/categories/"
      weight: 3
    - name: "标签"
      url: "/tags/"
      weight: 4
    - name: "关于"
      url: "/about/"
      weight: 5

outputFormats:
  RSS:
    baseName: "index"
    isPlainText: false

output:
  home: ["HTML", "RSS"]
  page: ["HTML"]
  section: ["HTML", "RSS"]
  taxonomy: ["HTML", "RSS"]
  taxonomyTerm: ["HTML", "RSS"]

# 新版Hugo分页配置
pagination:
  pagerSize: 8

build:
  writeStats: true
```