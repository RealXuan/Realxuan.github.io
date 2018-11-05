---
title: Linux上配置Jupyter Notebook远程访问
data: 2018-10-05
categories:  
    - Jupyter配置
mathjax: true
---
> 导语：Jupyter Notebook 的本质是一个 Web 应用程序，便于创建和共享文学化程序文档，支持实时代码，数学方程，可视化和 markdown。 用途包括：数据清理和转换，数值模拟，统计建模，机器学习等等。在做大数据量的特征工程以及数据建模的时候，由于个人电脑内存较小，我们往往需要借助于一台内存足够大的Linux服务器。在Linux服务器上配置Jupyter Notebook的远程访问，能够方便我们在本地直接通过浏览器远程使用服务器。

##Linux上修改Jupyter配置文件

###1.生成Jupyter配置文件
``` python
$ /root/.jupyter/jupyter_notebook_config.py  #/root/是服务器的根目录
``` 

###2.生成密码
``` python
$ ipython
``` 
进入python命令编辑界面
``` python
In [1]: from notebook.auth import passwd
In [2]: passwd()
Enter password:(输入你的密码)
Verify password:(再次输入你的密码)

Out[2]:'sha1:sdjh2yu3h2g27y4efuhf2h'(你的密码生成的哈希值，复制它)
``` 

###3.修改Jupyter默认配置文件
``` python
$ gedit /root/.jupyter/jupyter_notebook_config.py
``` 
打开之后，修改一下几项，注意去掉前面的注释符号‘#’

``` python
c.NotebookApp.ip='*' # '*'表示所有ip都可以访问
c.NotebookApp.password=u'sha1:sdjh2yu3h2g27y4efuhf2h' #粘贴上一步骤中的哈希值
c.NotebookApp.open_browser=False
c.NotebookApp.port=9999 #指定访问的端口号
``` 

###4.启动Jupyter Notebook
``` python
$ Jupyter Notebook
``` 

###本地访问
打开浏览器，输入http://服务器ip地址：端口号即可
如果遇到jupyter SSL Error 问题，则可能是SSL协议不对，改成https即可
