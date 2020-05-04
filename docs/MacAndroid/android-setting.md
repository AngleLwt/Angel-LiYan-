# Mac Util
## Homebrew


在Windows系统下，我们一般安装软件就是从网站或者软件中心下载 ***.exe*** 文件，然后点击安装，一步步点点点就完事了。但是在Linux和类Unix系统中安装软件包相对于刚入门的小白来，有时候比较麻烦，在基于Ubuntu的系统中我们有 ***apt-get*** 包管理器，在CentOS系统中有 ***yum*** 包管理器, 这些都是我们常见的，但是在Mac OS X系统中，自带有 ***Homebrew*** 软件包管理器。当然在Mac和Linux系统中也可以直接在软件中心或者软件官方网站下载安装包，比如 ***.rmp*** 、 ***.dmp*** 等安装包也可以像Windows操作系统中一样点击安装。

***Homebrew*** 是一款Mac OS平台下的软件包管理工具，拥有安装、卸载、更新、查看、搜索等很多实用的功能。简单的一条指令，就可以实现包管理，而不用你关心各种依赖和文件路径的情况，十分方便快捷。

 Homebrew 安装 卸载

**macOS安装环境要求:**

*   A 64-bit Intel CPU
*   macOS High Sierra (10.13) (or higher)
*   Command Line Tools (CLT) for Xcode: `xcode-select --install`,[developer.apple.com/downloads](https://links.jianshu.com/go?to=https%3A%2F%2Fdeveloper.apple.com%2Fdownloads) or [Xcode](https://links.jianshu.com/go?to=https%3A%2F%2Fitunes.apple.com%2Fus%2Fapp%2Fxcode%2Fid497799835)
*   A Bourne-compatible shell for installation (e.g. `bash` or `zsh`)

**安装Homebrew命令**，在终端中输入以下命令就安装完成，默认安装目录是 *`/usr/local`*：

```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

```

**卸载Homebrew命令：**

```
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/uninstall)"

```

 Homebrew 常用命令

术语：*`homebrew`* 有“家酿、自制”的*意思*, *`formula`* 有“配方”的意思，这里我们一般表示软件包。

> 我们以安装 *wget* 软件举例:

1.  列出所有已经安装的 *`formula`*

    ### brew list

2.  查看指定 *`formula`* 的基本信息, 比如目前的版本, 依赖, 安装后注意事项等

    ### brew info wget

3.  搜索指定的 *`formula`*

    ### brew search wget

4.  安装指定的 *`formula`*

    ### brew install wget

5.  更新指定的 *`formula`*

    ### brew update wget

6.  卸载并重新安装指定的 *`formula`*

    ### brew reinstall wget

7.  卸载指定的 *`formula`*

    ### brew uninstall wget

8.  升级所有可以升级的 *`formula`*

    ### brew upgrade

9.  清理过期的文件、版本，如果指定了 *`formula`* , 则清理指定的

    ### brew cleanup wget

10.  查看帮助

    ### brew help

11.  浏览器中打开指定 *`formula`*, 如果没指定则打开Homebrew首页

    ### brew home wget

> 注意

有些系统中如果安装过程中遇到权限问题，需要sudo指令

了解完整详细的brew命令，请[查看官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.brew.sh%2FManpage)

如果你对 *`formula`* 感兴趣，请参考[Formula Cookbook](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.brew.sh%2FFormula-Cookbook)

* * *
## Mac版mysql 安装安装mysql下载mysql。

我下载的是：``mysql-8.0.11-macos10.13-x86_64.dmg``
双击打开mysql-8.0.11-macos10.13-x86_64.dmg，然后双击mysql-8.0.11-macos10.13-x86_64.pkg
![](https://upload-images.jianshu.io/upload_images/21988850-e773e4637d280913.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
一路点击继续，傻瓜式安装，没什么好说的
![](https://upload-images.jianshu.io/upload_images/21988850-3bd0f33d7ba21f8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-a57b108579162b8c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-d1db38f3ca3953dd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-3232fd192467d2d3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
此处选择“Use Legacy Password Encryption”，否则使用navicat连接mysql的时候，会报无法加载身份验证的错误。
![](https://upload-images.jianshu.io/upload_images/21988850-3b3d32c3ad32424d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
为“root”用户设置一个密码
![](https://upload-images.jianshu.io/upload_images/21988850-be0580764f5bcbc7.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装完成
![](https://upload-images.jianshu.io/upload_images/21988850-824525de4d82f669.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
安装成功后，使用mysql命令回报：command not found 的错误，是因为还没有配置环境变量。
配置环境变量
首先要知道你使用的Mac OS X是什么样的Shell，
打开终端，输入：echo $SHELL 回车执行
如果输出的是：csh或者是tcsh，那么你用的就是C Shell。
如果输出的是：bash，sh，zsh，那么你的用的可能就是Bourne Shell的一个变种。
Mac OS X 10.2之前默认的是C Shell。
Mac OS X 10.3之后默认的是Bourne Shell。
我的是bash：![](https://upload-images.jianshu.io/upload_images/21988850-593bd8f558315b74.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
输入：``cd /usr/local/mysql``，回车执行
然后输入：``sudo vim .bash_profile `` ，回车执行
需要输入root用户密码。sudo是使用root用户修改环境变量文件。
进入编辑器后，我们先按"i”，即切换到“插入”状态。就可以通过上下左右移动光标，或空格、退格及回车等进行编辑内容了，和WINDOWS是一样的了。![](https://upload-images.jianshu.io/upload_images/21988850-2b998376fe085b23.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

在文档的最下方输入：
``export PATH=${PATH}:/usr/local/mysql/bin``
![](https://upload-images.jianshu.io/upload_images/21988850-bb33ef957dd7729a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)然后按Esc退出insert状态，并在最下方输入:wq保存退出(或直接按``shift+zz``，或者切换到大写模式按ZZ，就可以保存退出了)。
最后输入：``source .bash_profile`` 回车执行，运行环境变量。
再输入mysql命令，即可使用。
![](https://upload-images.jianshu.io/upload_images/21988850-7cdc382b7bb1cf30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
Mysql连接navicat
![](https://upload-images.jianshu.io/upload_images/21988850-e4add9f5a38c4405.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![](https://upload-images.jianshu.io/upload_images/21988850-34514ab76a73c6f0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
连接成功如下图
![](https://upload-images.jianshu.io/upload_images/21988850-d8e200b4d03f4e2d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## sublime3使用及下载
>[sublime3下载地址](http://www.xue51.com/mac/1518.html)

![](https://upload-images.jianshu.io/upload_images/21988850-d6170f05cb3ed7e2.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
符号说明
⌘：command
⌃：control
⌥：option
⇧：shift
↩：enter
⌫：delete
（打开/关闭/前往）快捷键 功能
⌘⇧N 打开一个新的sublime窗口
⌘N 新建文件
⌘⇧W 关闭sublime，关闭所有文件
⌘W 关闭当前文件
⌘P 跳转、前往文件、前往项目、命令提示、前往method等等（Goto anything）
⌘⇧T 重新打开最近关闭的文件
⌘T 前往文件
⌘⌃P 前往项目
⌘R 前往method
⌘⇧P 命令提示
⌃G 前往行
⌘KB 开关侧栏
⌃ 打开控制台
⌃- 光标跳回上一个位置
⌃⇧- 光标恢复位置
（编辑）快捷键 功能
⌘A 全选
⌘L 选择行（重复按下将下一行加入选择）
⌘D 选择词（重复按下时多重选择相同的词进行多重编辑）
⌃⇧M 选择括号的内容
⌘⇧↩ 在当前行前插入新行
⌘↩ 在当前行后插入新行
⌃⇧K 删除行
⌘KK 从光标处删除至行尾
⌘K⌫ 从光标处删除至行首
⌘⇧D 复制（多）行
⌘J 合并（多）行
⌘KU 改为大写
⌘KL 改为小写
⌘C 复制
⌘X 剪切
⌘V 粘贴
⌘/ 注释
⌘⌥/ 块注释
⌘Z 撤销
⌘Y 恢复撤销
⌘⇧V 粘贴并自动缩进
⌘⌥V 从历史中选择粘贴
⌃M 跳转至对应的括号
⌘U 软撤销（可撤销光标移动）
⌘⇧U 软重做（可重做光标移动）
⌘⇧S 保存所有文件
⌘] 向右缩进
⌘[ 向左缩进
⌘⌥T 特殊符号集
⌘⇧L 将选区转换成多个单行选区
（查找/替换）快捷键 功能
⌘f 查找
⌘⌥f 查找并替换
⌘⌥g 查找下一个符合当前所选的内容
⌘⌃g 查找所有符合当前选择的内容进行多重编辑
⌘⇧F 在所有打开的文件中进行查找
（拆分窗口/标签页）快捷键 功能
⌘⌥[1,2,3,4] 单列、双列、三列、四列
⌘⌥5 网格（4组）
⌃[1,2,3,4] 焦点移动到相应的组（分屏编号）
⌃⇧[1,2,3,4] 将当前文件移动到相应的组（分屏编号）
⌘[1,2,3,4] 选择相应的标签页
（快捷操作）快捷键 功能
⌘⌃上下键 两行交换位置
⌘KB 显示/隐藏侧边
安装插件
1. 代码格式化的插件
2.Js和Css语法检查插件
3. JSFormat 插件(只是针对于js文件有效,想要格式化HTML等文件,请参照第一点)
4. Emmet
5. Theme – Soda + sublime Text3 主题修改
6.Sublime Text 3安装React 开发环境插件

##  Mac安装apktool步骤反编译

>注意：
资源和代码是两个工具
资源一般不需要，直接解压即可
防止反编译方法：<混淆加固>

 1.1、使用工具

1.  apktool （资源文件获取） 
2.  dex2jar（源码文件获取）
3.  jd-gui  （源码查看）

 1.2、工具介绍
　apktool  

     　　　　作用：资源文件获取，可以提取出图片文件和布局文件进行使用查看

　　dex2jar

     　　　　作用：将apk反编译成java源码（classes.dex转化成jar文件）

　　jd-gui

     　　　　作用：查看APK中classes.dex转化成出的jar文件，即源码文件

apktool下载地址：

https://ibotpeaches.github.io/Apktool/install/
1、在Chrome中，打开网址，找到图中所示Mac相关部分
![image.png](https://upload-images.jianshu.io/upload_images/21988850-c97012aab3c1a8de.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
2、在Chrome中，打开网址，在“ wrapper script”上点击鼠标右键，选择“链接存储为”
![image.png](https://upload-images.jianshu.io/upload_images/21988850-0ee4cc669eabe8eb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
存储时，必须选择格式为“所有文件”，文件名为“apktool”，没有后缀(貌似有些浏览器不能选择格式，我在Chrome浏览器可以选择文件格式)
![image.png](https://upload-images.jianshu.io/upload_images/21988850-ef677fa1996f07fc.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3、点击“find newest here”进入下载页面，选择合适的版本，下载.jar文件，下载后文件名要改为“apktool.jar”
![image.png](https://upload-images.jianshu.io/upload_images/21988850-89dd59ee4c5de389.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
![image.png](https://upload-images.jianshu.io/upload_images/21988850-db901e53e96d035e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
4、把apktool和apktool.jar文件移动到"/usr/local/bin"目录下，并使用终端命令为两个文件增加执行权限
命令如下：
chmod +x apktool.jar ![](https://upload-images.jianshu.io/upload_images/21988850-bfc5175cab414629.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)




5、验证是否安装成功
在终端输入命令：apktool
打印如下信息：

方法一：使用apktool
1、工具下载：[https://ibotpeaches.github.io/Apktool/install/](https://ibotpeaches.github.io/Apktool/install/)
2、下载script，命名为：apktool.sh
3、下载apktool.jar，命名：apktool.jar
（.sh脚本是自写脚本可不用更新最新，下载的jar文件名必须是apktool.jar，不能是apktool-2.0.1.jar这样的形式）
4、增加这两个文件可执行权限（chmod a+x file ）
在命令提示符下执行：
cd /usr/local/bin
chmod a+x apktool.sh
chmod a+x apktool.jar
5、执行apktool.sh d XXX.apk
注：XXX.apk 是在当前目录下的需要反编译的apk文件
总结：该方法可以反编译出资源文件和smali文件
方法二：使用apktool
步骤
将下载的 dex2jar 压缩包解压。

运行终端，cd 命令到 dex2jar 目录，目录可以直接拖这个文件夹到终端窗口。

将 apk 文件改后缀为 7z，这个比较好解压（电脑其他格式无法解压，也没有装其它软件，可能是懒）我将其中的 classes.dex文件拷贝到 dex2jar目录。

>在终端运行 ./d2j-dex2jar.sh classes.dex
如果出现 Permission Denied 异常，一般报的哪个文件就修改对应文件权限即可

例如： d2j_xxx.sh 文件，然后修改 chmod 777 d2j_xxx.sh

反编译成功后，将目录中生成的 classes-dex2jar.jar 文件用 jd-gui 打开就可以看到代码了，也可以进行导出等操作，当然也可能是混淆后的代码。
(1)打开终端,输入:

```
cd /usr/local/bin

```

如果电脑不存在这个目录，那么创建一个:

```
sudo mkdir bin

```

创建完成后再使用cd命令看看。
(2)打开终端，使用cd命令定位到apktool文件夹:

```
cd Desktop/apktool/

```

使用cp命令把apktool.jar和apktool文件拷贝到/usr/local/bin

```
sudo cp apktool.jar apktool /usr/local/bin

```

之后,使用

```
sudo apktool

```

可以查看apktool的版本
(3)现在可以使用apktool相关命令了,和windows是一样的。
把apk文件放到apktool文件夹,然后回到apktool文件夹下:

```
cd /Desktop/apktool/

```

使用(xxx.apk是你的apk名字)

```
apktool d xxx.apk

```

就能进行反编译了。对于系统apk，需要额外导入框架才能反编译。更多apktool命令可以自行百度Google。

 5.配置dex2jar

(1)下载[dex2jar](https://link.jianshu.com?t=http%3A%2F%2Fmac.softpedia.com%2Fget%2FDeveloper-Tools%2Fdex2jar.shtml),解压
(2)将dex2jar文件夹放在apktool文件夹下
(3)把apk文件解压,可以直接解压或者修改后缀.zip再解压,找到classes.dex文件，把它放进dex2jar文件夹下
(4)定位到dex2jar文件夹(不同版本的dex2jar文件名不同,请作相应替换):

```
cd Desktop/apktool/dex2jar-0.0.9.15/

```

执行命令:

```
sh dex2jar.sh classes.dex

```

这时会在dex2jar文件夹下生成一个classes_dex2jar.jar文件。

6.查看java源码

下载[jd-gui](https://link.jianshu.com?t=http%3A%2F%2Fmac.softpedia.com%2Fget%2FDevelopment%2FJava%2FJD-GUI.shtml),将下载的jd-gui压缩包解压,然后右键上一步生成的classes_dex2jar.jar文件,选择打开方式->JD-GUI，就可以查看java源码了!


## JDK安装
下载网址：[JavaSEDownloads](https://www.oracle.com/java/technologies/javase-downloads.html)
![](https://upload-images.jianshu.io/upload_images/21988850-189f0a1abd0d7f01.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
下载成功傻瓜式安装
![安装成功如图](https://upload-images.jianshu.io/upload_images/21988850-1ef653e771e97903.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


## Mac终端打开文件
>1.打开文件夹的命令很简单，使用 open + 文件夹的路径例如：``open ~/downloads``
2.打开前往中的前往文件夹![](https://upload-images.jianshu.io/upload_images/21988850-cecea8131ed60618.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
如下图![](https://upload-images.jianshu.io/upload_images/21988850-87217b38de9e225e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3.打开资源库
按住Option打开前往就可以看到资源库
## 解决Mac电脑Wi-Fi上网网速慢问题
>1.点击终端:
输入命令：``networksetup -setdnsservers Wi-Fi 114.114.114.114``，或者输入``networksetup -setdnsservers Wi-Fi 180.76.76.76``，然后回车。

>2.输入：``sudo dscacheutil -flushcache``，在终端输入管理员密码。如果你发现无法输入，不要惊慌，输入管理员密码为了保密，你输入看不到输入的字符的。

>3.密码输入正确后，打开浏览器，此时上网上百度网速就会比较快了的。





Mac  电脑安全性关闭/开启 
>进入终端以后，在终端我们需要输入“csrutil disable”这条命令，按下回车，看到下面的提示成功以后我们就解除系统的权限控制了。
关闭了系统的保护程序毕竟不是很安全的事情，所以我们当我们完成关闭保护的需求之后还是需要将保护程序再次打开的。同样的方式重启电脑，然后按住 Command+R 进入到恢复工具界面，打开终端，在终端中输入“csrutil enable”回车即可打开保护。然后重启电脑，就好了。

>[Mac管家下载网址（一款专业的Mac管家）](https://www.lanzous.com/i6s0v9e)

1、打开终端
2、输入cd
3、将想要到达的文件夹拖进终端，点击回车，就到了指定的文件夹了

Missing write access to /usr/local/lib/node_modules npm ERR! path /usr/local/lib/node_modules
文件没有root权限,运行语句前面加上sudo
`sudo npm install -g appium --unsafe-perm=true --allow-root`

一个文件有3种权限，读、写、可执行，你这个文件没有可执行权限，需要加上可执行权限。

1. 终端下先 cd到该文件的目录下

2. 执行命令 chmod a+x ./文件名

也许有些文件是可以解决的，但是如果是 管理员权限的在命令前面加上 sudo 就可以了

最好好在 关闭mac 的保护程序 在开机的时候 command + R 进入终端输入csrutil disable  这样基本问题都解决了
homebrew安装的

直接一条命令

 

1

brew uninstall node

官网下载pkg安装包的

一条命令

 

1

sudo rm -rf /usr/local/{bin/{node,npm},lib/node_modules/npm,lib/node,share/man/*/node.*}

其他路子安装的













