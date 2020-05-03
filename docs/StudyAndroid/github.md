# GitHub
## 删除库
1.登陆GitHub进入仓库
![](https://upload-images.jianshu.io/upload_images/21988850-ce30587cd91e64d1.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2.进入库
![](https://upload-images.jianshu.io/upload_images/21988850-5760e0535cec2f57.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.删除库![](https://upload-images.jianshu.io/upload_images/21988850-7eb9c6599418b9ae.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
## Mac 解决GitHub下载速度慢问题
Mac 解决GitHub下载速度太慢问题

GitHub 程序员离不开的网站，但是网速是真的超级慢，今天项目需要从GitHub上下载，出奇的太慢了，忍无可忍的慢，总是中途失败.
只能求助百度了！！！
* * *

### 解决方案是修改hosts，按照以下三步来操作

*   #### 1、打开hosts文件：

终端执行

```
sudo vim /etc/hosts

```

*   #### 2、浏览器访问[https://www.ipaddress.com/](https://links.jianshu.com/go?to=https%3A%2F%2Fwww.ipaddress.com%2F)，分别输入

`github.com`和`github.global.ssl.fastly.net`以获取对应的ip。

![](https://upload-images.jianshu.io/upload_images/21988850-a4be342ccd7a4916.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-d30350f8b1cc7561.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-9b5837422659a46f.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



更改hosts文件：在后面追加
把对应的ip换成你刚刚获取到的即可。如果你在开发中经常涉及到修改hosts文件，建议你使用 **SwitchHosts**这个软件来管理hosts文件，可视化非常方便，[你可以点击这里了解如何使用SwitchHosts](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Ffanrenxiang%2Farticle%2Fdetails%2F80695364)。
![](https://upload-images.jianshu.io/upload_images/21988850-bb781a1a92a85bb5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

*   #### 3、刷新DNS缓存(Mac 系统)，Windows系统命令可以自行查找一下。

```
sudo killall -HUP mDNSResponder

```

### 然后重新下载试试吧，亲测有效，还是非常有效果的。



