---
title: Hexo+Github配置个人博客
data: 2018-10-01
categories:  
    - Hexo配置
mathjax: true
---
> 导语：Hexo+Github是利用Hexo框架迅速创建博客网站，并将网站代码托管到GitHub上的一种方式，具有开源、优雅、高效等特点。本文主要记录使用Hexo+Github配置个人博客的过程，包括安装、部署、使用以及美化。

##Hexo与GitHub
Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。GitHub的作用类似于网站的服务器，我们将Hexo生成的静态网页部署到GitHub上，GitHub会提供一个username.github.io作为个人博客的地址。

## 安装Hexo

### 1.安装Node.js 
https://nodejs.org/
### 2.安装Git
https://git-scm.com/downloads
### 3.安装Hexo
cmd输入：

``` python
$ npm install -g hexo-cli
``` 

## Hexo建站
### 1.初始化站点
Git Bash输入：
``` python
$ hexo init <folder> //folder为自定义的文件夹名
$ cd <folder>
$ npm install
``` 

初始化完成后,根目录的文件如下：

<!--more-->
+ _config.yml
博客的配置主文件，网站的主要参数在这里配置，比如网站名称，副标题，描述，作者，语言，主题，部署等等。
详细说明见：https://hexo.io/zh-cn/docs/configuration

+ package.json
记录hexo框架的参数和所依赖插件

+ scaffolds
页面模板文件存放目录，新建文章时，Hexo 会根据 scaffold 来建立文件

+ source
保存文章的目录，文章是.md文件，Hexo会将_post目录下的.md文件编译成html文件，存放到根目录的public文件夹下

+ themes
主题文件存放目录，Hexo中集成了丰富的博客主题，用户可以自己下载并更换

+ public
网页文件的存放目录，网页文件是使用命令自动生成的，部署博客时Hexo会将public下的文件上传至GitHub

### 2.本地访问
Git Bash输入：
``` python
$ hexo server //使用默认端口4000
或者
$hexo server -p 5000 //更改端口
``` 
本地浏览器输入：
``` python
http://localhost:4000/
``` 
### 3.部署到GitHub上
在Github上new repositories，并且Repository name为[username].github.io，[username]指的是github用户名。修改根目录下的_config.yml配置文件,注意冒号后面必须有一个空格
``` python
deploy:
  type: git
  repository: https://github.com/[username]/[username].github.io.git 
  branch: master
``` 
安装部署git的依赖包，根目录下cmd输入：
``` python
npm install hexo-deployer-git --save
``` 
生成页面并部署至GitHub，Git Bash输入：
``` python
hexo generate //hexo g
hexo deploy //hexo d
``` 
浏览器访问[username].github.io即可

##常用命令
``` python
hexo clean //清除缓存
hexo g //保存修改，生成文件 
hexo s 或 hexo s -p 指定端口//启动本地服务，可进行本地预览 
hexo d //发布到远程GitHub 

``` 
上述命令是把public文件夹下的网站页面上传至GitHub,我们还可以使用Git命令将项目的所有代码保存到GitHub上：
``` python
git add . //将项目的所有文件添加到仓库中
git status //查看更改的内容
git commit -m "版本信息" //为更新做注释
git pull origin hexo //多人协作时，代码上传github前需要pull远程仓库代码到本地
git push origin hexo //上传代码到GitHub远程仓库的hexo分支

``` 
##Hexo页面美化
###下载主题
[Hexo主题官网](https://hexo.io/themes/)中提供了丰富的主题模板，我们可以从中选择自己喜欢的主题进行配置，我的个人博客选择的是[NexT主题](http://theme-next.iissnan.com/)，安装步骤如下：
站点根目录下Git Bash输入如下命令，完成后，在根目录的themes文件夹下会增加一个next的目录，里面存放next主题的相关文件
``` python
$ git clone https://github.com/iissnan/hexo-theme-next themes/next
``` 
在根目录的_config.yml配置文件中启用next主题
``` python
theme: next
``` 
重新部署网站即可生效。可以通过修改next文件夹下的_config.yml来改变next主题样式，如：
``` python
#scheme: Muse
#scheme: Mist
scheme: Pisces
``` 
###给文章添加分类和标签
创建categories和tags页面
``` python
$ hexo new page categories
$ hexo new page tags
``` 
创建完成后，在source文件夹下会增加categories和tags两个文件夹，分别打开这两个文件夹下的index.md修改：
``` python
---
title: 文章分类
date: 2018-00-00 13:47:40
type: "categories"
---

---
title: 标签
date: 2018-00-00 13:47:40
type: "tags"
---
``` 
在文章的.md头部属性中增加categories和tags属性：
``` python
---
title: FM(Factorization Machine)模型
date: 2018-10-18
categories:  
    - 推荐与CTR
tags:
    - 矩阵分解
    - CTR预估
---
``` 
###给文章插入图片
安装依赖包：
``` python
npm install hexo-asset-image --save
``` 
source目录下建立images文件夹用于存放图片，文章中插入图片：
``` python
![图片说明文字](图片的相对路径)
``` 
###使用LaTex编辑数学公式
更换依赖包，cmd输入：
``` python
npm uninstall hexo-renderer-marked --save
npm install hexo-renderer-kramed --save
``` 
修改/node_modules/hexo-renderer-kramed/lib/renderer.js：
``` python
// Change inline math rule
function formatText(text) {
    return text;
}
``` 
更换依赖包，cmd输入：
``` python
npm uninstall hexo-math --save
npm install hexo-renderer-mathjax --save
``` 
修改Mathjax的配置文件/node_modules/hexo-renderer-mathjax/mathjax.html：
``` python
<!-- <script src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML"></script> -->
<script src="https://cdnjs.cloudflare.com/ajax/libs/mathjax/2.7.1/MathJax.js?config=TeX-MML-AM_CHTML"></script>
``` 
更改默认转义规则/node_modules\kramed\lib\rules\inline.js：
``` python
//escape: /^\\([\\`*{}\[\]()#$+\-.!_>])/,
//em: /^\b_((?:__|[\s\S])+?)_\b|^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
escape: /^\\([`*\[\]()# +\-.!_>])/,
em: /^\*((?:\*\*|[\s\S])+?)\*(?!\*)/,
``` 
在next目录的_config.yml中开启mathjax
``` python
mathjax:
    enable: true
``` 
文章的.md头部属性中增加mathjax属性：
``` python
--- 
title: FM(Factorization Machine)模型
date: 2018-10-18
categories:  
    - 推荐与CTR
mathjax: true
---
``` 
###给文章添加阅读次数
注册[LeanClound](https://leancloud.cn/)账号，创建一个应用：
![upload successful](/images/1025_pic1.PNG)
进入应用中创建一个名为Counter的Class:
![upload successful](/images/1025_pic2.PNG)
在next目录的_config.yml中增加：
``` python
leancloud_visitors:
  enable: true
  app_id: 
  app_key: 
``` 
app_id和app_key的值来源于下图：
![upload successful](/images/1025_pic3.PNG)

###博客添加站内搜索
安装站内搜索插件
``` python
$  npm install hexo-generator-searchdb --save
``` 
根目录下的_config.yml添加：
``` python
#表示站内搜索
search:
    path: search.xml
    field: post
    format: html
    limit: 10000
``` 
next目录下的_config.yml添加：
``` python
local_search:
  enable: true  #设置为true
  trigger: auto  # auto /  manual，auto 自动搜索、manual：按回车[enter ]键手动搜索
  top_n_per_article: 1
  unescape: true
``` 
