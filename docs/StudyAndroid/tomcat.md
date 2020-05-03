#TomCat
## Mac电脑Tomcat下载及安装(详细)
1.下载Tomcat
[Tomcat](http://tomcat.apache.org/download-70.cgi)
下载版本为tar.gz
2.解压apache-tomcat-7.0.82文件,最好把他放入/Library(资源库中)
```
打开资源库
 (1).点击finder-->用户-->你电脑的名字-->资源库(有的也叫/Library)
 (2).有些苹果将library目录隐藏起来了，要进入那个目录，需要用到一定的技巧。 
  打开Finder，按下shift+command+g，输入“~/Library”（输入引号里面的），再按回车就到了。
```
3.配置Tomcat
```
(1).对目录进行权限设置：
打开终端输入  sudo chmod 755 /Library/tomcat文件夹名称/bin/*.sh  回车，设置文件的读写执行权限；(这里需要输入管理员密码)
(2).启动Tomcat 
启动方法一:在终端中输入 sudo sh startup.sh
注意:先cd到bin目录下命令才不会报错
 启动方法二:在Library/tomcat/bin中找到startup.sh文件,把文件拖入到终端中回车启动
```

4.验证Tomcat是否启动

```
打开你的safari,然后在网址输入框输入[http://localhost:8080/](http://localhost:8080/)回车
如果能正常打开tomcat首页，说明tomcat 配置启动成功:
```
![启动成功如图](https://upload-images.jianshu.io/upload_images/21988850-5d9a84c730e26c93.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
