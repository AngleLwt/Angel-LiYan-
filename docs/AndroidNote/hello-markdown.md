一、布局方式：

（1）线性布局LinearLayout
（2）表格布局TableLayout
（3）帧布局FrameLayout
（4）相对布局RelativeLayout
（5）网格布局GridLayout
（6）绝对布局AbsoluteLayout

二、常用距离单位：

px（像素）
dip或dp（device independent pixels 设备独立像素）
sp（scaled pixels 比例像素）
in（英寸）
mm（毫米）
pt（磅）

三、事件处理机制：

（1）基于监听的事件处理。
（2）基于回调的事件处理。

四、Activity的主要职责为：完成界面初始化工作。

五、Configuration 描述手机设备上的配置信息。

六、Handle类：

（1）在新启动的线程中发送消息。
（2）在主线程中获取、处理消息。

七、Activity的4种加载模式：

（1）standard： 标准模式
（2）singleTop： Task栈顶单例模式
（3）singleTask: Task内单例模式
（4）singleInstance: 全局单例模式（新的Task，且次Task只包含这个Activity）。
**在AndroidManifest.xml中，设置launchMode
**<activity android:name=".SecondActivity" android:label="@string/app_name" android:launchMode="singleTask"></activity>

八、Fragment为Activity的片段，拥有自己的声明周期，也可以接受它自己的输入事件。必须“嵌入”Activity中使用。

子类：
DialogFragment： 对话框界面的Fragment。
ListFragment： 实现列表界面的Fragment。
PreferenceFragment： 选项设置界面的Fragment。
WebViewFragment： WebView界面的Fragment。

必须实现方法：
onCreate()： 初始化想要在Fragment中保持的必要组件。
onCreateView(): 绘制界面组件。
onPause(): 当用户离开该Fragment时将会调用。

生命周期：

onAttach(): 当该Fragment被添加到Activity时被回调。 该方法只会被调用一次。
onCreate(): 创建Fragment时被回调。 该方法只会被调用一次。
onCreateView(): 每次创建、绘制该Fragment的View组件时回调该方法。
onActivityCreated():当Fragment所在的Activity被启动完成后回调该方法。
onStart(): 启动Fragment时被回调。
onResume(): 回复Fragment时被回调，在onStart()方法后一定会回调onResume()方法。
onPause(): 暂停Fragment时被回调。
onStop(): 停止Fragment时被回调。
onDestroyView(): 销毁该Fragment所包含的View组件时调用。
onDestroy():销毁Fragment时被回调。 该方法只会被调用一次。
onDetach(): 将该Fragment从Activity中删除、替换完成时回调该方法，在onDestroy()方法后一定会回调onDetach()方法。 该方法只会被调用一次。

Activity和Fragment通讯：

Fragment获取它所在的Activity：调用Fragment的getActivity().
Activity获取它包含的Fragment：调用Activity关联的FragmentManager的findFragmentById()或findFragmentByTag()方法.
Activity向Fragment传递数据：在Activity中创建Bundle数据包，调用Fragment的setArguments(Bundle bundle)方法即可将bundle数据包传给Fragment。
Fragment向Activity传递数据或Activity需要在Fragment运行中进行实时通信：在Fragment中定义一个内部回调接口，再让包含该Fragment的Activity实现该回调接口。

九、Android上的Style分为：

（1）Theme是针对窗体级别的，改变窗体样式。
（2）Style是针对窗体元素级别的，改变指定空间或者Layout的样式。 （Android系统的themes.xml和style.xml包含了很多系统定义好的style，建议在里面挑个合适的，然后再继承修改）

十、gravity的布局：

（1）”left|right” 绝对的对齐
（2）“start|end” 基阅读顺序的对齐

十一、Adapter 是连接后端数据和前端显示的适配器接口，是数据和UI（View）之间一个重要的纽带。

有点像cell，定义好layout，并将modle传入。

十二、android studio实现父类的抽象方法的快捷方法：

（1）ctrl + enter
（2） 选择 Implement Methods
就可以自动生成需要的方法。

十三、ScrollView can host only one direct child的提示，因为scrollview中只能有一个子元素，即所有元素需要放到LinearLayout 或 RelativeLayout中。

十四、

android:gravity 针对控件里的元素来说的，用来控制元素在该控件里的显示位置。
android:layout_gravity 针对控件本身而言，用来控制该控件在包含该控件的父控件中的位置。

出现"layout"就是控件对整个布局的操作

十五、android:layout_weight = "1”

只能在LinearLayout中使用，不能在RelativeLayout中使用。

十六、MVP的逻辑：

网络请求声明接口，在APIService中完成
Model层编写完成接口请求
Presenter中实现接口的返回操作
View中定义界面操作接口
Presenter调用View接口
在Activity中实现View的接口

十七、

private: 只有在本类中才能访问
public: 正好和private相反，在任何地方都能访问
protected: 本包内能访问，而在包外只有它的子类能访问;

十八、

Log.v(tag,message); //verbose模式,打印最详细的日志
Log.d(tag,message); //debug级别的日志
Log.i(tag,message); //info级别的日志
Log.w(tag,message); //warn级别的日志

Log.e(tag,message); //error级别的日志

十九、native关键字： 一个java调用非java代码的接口

二十、Java中用final标识常量，不用const

二十一、StringBuffer 为可变的String， 提供了append方法

二十二、equale 和 == 的区别：

equal： 检查字符串的值是否相等
==： 检查对象是否相等

二十三、Collection集合包括 Set， List，Map。

Set
HashSet: 不允许出现重复元素；不保证集合中元素的顺序；允许包含一个null元素。（LinkedHashSet为有序的）
TreeSet：可以实现排序的集合。（使用Comparator进行排序）

List
ArrayList： 可变的数组列表。
LinkedList: 链表结构实现了List接口。
Vector： 类似与ArrayList。不同处：同一时刻只可以有一个线程操作。
Stack类： 数据结构中的堆栈。

Map

HashMap: 哈希表实现映射集合结构。
TreeMap： 按Map映射中的元素按照键进行升序排序。

二十四、Enumeration和Iterator的区别：

Enumeration只能在Vector和HashTable中使用。Iterator使用所有结合。
Enumeration遍历集合时不可移除元素，Iterator可以。

二十五、

String.format("￥%.2f", mOrderInfo.getOrder_amount());
将float保留两位小数，转为 string

二十六、Context.MODE_WORLD_WRITEABLE为每个APP创建一个文件夹。

二十七、

public void onClick(View v) {
        new Thread(new Runnable() {
            public void run() {
                final Bitmap bitmap = loadImageFromNetwork("http://example.com/image.png");
                mImageView.post(new Runnable() {
                    // run方法会在UI线程中执行  
                    public void run() {
                        mImageView.setImageBitmap(bitmap);
                    }
                });
            }
        }).start();
    }
**在主线程中，更新UI组件。
**
调用以下方法:
Activity.runOnUiThread(Runnable)
View.post(Runnable)
View.postDelayed(Runnable, long)

如果在工作线程中调用了这3个方法, 那么方法中Runnable参数封装的操作会在UI线程中执行.

二十八、View事件：

View.OnClickListener 单击事件
View.OnCreateContextMenuListener 创建上下文菜单事件
View.OnFocusChangeListener 焦点改变事件
View.OnKeyListener 按键事件
View.OnLongClickListener 长按事件
View.OnTouchListener 触摸事件

二十九、

layout可以创建横屏和竖屏的布局。
Configuration 可以获取系统的信息

三十、Activity的基类：

FragmentActivity：实现Fragment，必须继承这类
AccountAuthenticatorActivity: 实现账户管理界面的Activity
TabActivity：实现Tab界面的Activity
ListActivity：实现列表界面的Activity
LauncherActivity：实现Activity列表界面的Activity，当单击列表项时，所对应Activity被启动
PreferenceActivity：实现程序参数设置，存储界面的Activity
AliasActivity：别名Activity的基类，启动其他Activity时结束自己
ExpandableListActivity：可扩展的list，单击某个item后，又可显示一个子list。

三十一、Context抽象类：一个访问application环境全局信息的接口，可以访问application的资源和相关的类，功能有：

启动Activity
启动和停止Service
发送广播信息（Intent）
注册广播信息（Intent）接受者
可以访问APK中各种资源（如Resources和AssetManager等）
可以访问Package的相关信息
APK的各种权限管理

三十二、Android应用要求所有应用程序组件（Activity、Service、ContentProvider、BroadcastReceiver）都必须显式配置。

Activity的配置：
name：指定Activity的实现类的类名
icon：指定Activity对应的图标
label：指定Activity的标签
exported：指定Activity是否允许被其他应用调用
launchMode：指定Activity的加载模式。（standard，singleTop，singleTask和singleInstance）

三十三、Activity的生命周期：

4种状态：
运行状态
暂停状态
停止状态
销毁状态

回调方法：
onCreate(Bundle savedStatus): 创建Activity时被回调。该方法只会被调用一次。
onStart(): 启动Activity时被回调。
onRestart(): 重新启动Activity时被回调。
onResume(): 恢复Activity时被回调。在onStart()方法后一定会回调onResume()方法。
onPause(): 暂停Activity时被回调。
onStop(): 停止Activity时被回调。
onDestroy(): 销毁Activity时被回调。 该方法只会被调用一次。

三十四、Fragment事务

FragmentManager fragmentManager = getFragmentManager();FragmentTransaction fragmentTransaction = fragmentManager.beginTransaction();
Activity中获取Fragment事务，调用add()、remove()、 replace()操作，调用commit() 提交事务。

三十五、Bundle类：一个key-value对。是一个final类。

三十六、Android包含三种重要组件：Activity，Service，BroadcastReceiver。

三十七、Intent用于值传递：

Intent对象包含Component，Action，Category，Data，Type，Extra和Flag属性。
Component：包名
Action：动作
Category：附加类别信息
Data： Action属性提供操作的数据，接受一个Uri对象
Type：Data属性所指定Uri对应的MIME类型
Extra：Bundle对象，用于数据交互
Flag：控制旗标

三十八、TabActivity： Layout中需要设置TabHost，并添加FrameLayout和TabWidget，用来显示tab栏和内容。

三十九、

Drawable资源：StateListDrawable资源： 随目标组件状态的改变而自动切换。
LayerDrawable资源： 数组顺序绘制。（layer-list 覆盖绘制）
ShapeDrawable资源：几何图形（如矩形、圆形、线条）。（shape 绘制）
ClipDrawable资源：其他位图上截取一个“图片片段”。（clip 截图）
AnimationDrawable资源：动画。

四十、主题和样式的区别：主题不能作用于单个的View组件，主题应该对整个应用中的所有Activity起作用，或对指定的Activity起作用。

主题定义的格式应该是改变窗口外观的格式，例如窗口标题、窗口边框。

四十一、原始资源：位于/res/raw/目录下和位于/assets/目录下

四十二、invalidate() 实现界面刷新，会重新调用DrawView()方法。 不能在线程中调用。postInvalidate() 界面刷新。 可在线程中调用。

四十三、SharedPreferences与iOS的UserDefault一样，用于保存简单信息到本地。

四十四、开发Activity步骤：（1）开发Activity子类。 （2）在AndroidManifest.xml文件中配置Activity。

四十五、AdapterView( 相当于iOS的UITableView)：子类有

ListView
ExpandableListView
GridView
Spinner
Gallery
AdapterViewFlipper
StackView

Adapter接口的实现类有：
HeaderViewListAdapter
BaseAdapter
CursorAdapter
ResourceCursorAdapter
SimpleCursorAdapter
ArrayAdapter
SimpleAdapter

四十六、获取SDCard中的路径：

File sdCardDir = Environment.getExternalStorageDirectory();

四十七、在WebView中显示页面

webView = (WebView) findViewById(R.id.webView); 
    //WebView加载web资源 
    webView.loadUrl("http://baidu.com"); 
    //覆盖WebView默认使用第三方或系统默认浏览器打开网页的行为，使网页用WebView打开 
    webView.setWebViewClient(new WebViewClient() 
    { 
        @Override public boolean shouldOverrideUrlLoading(WebView view, String url) 
        { 
            // TODO Auto-generated method stub 
            // 返回值是true的时候控制去WebView打开，为false调用系统浏览器或第三方浏览器 
            view.loadUrl(url); 
            return true; 
            }
    });
如果不设置，则直接在系统的浏览器中打开

四十八、String，StringBuffer，StringBuilder之间的区别

对String，你创建了一个String，你能通过set方法改变它的长度length吗？显然是不行的！
但 StringBuffer 可以！
String 字符串常量StringBuffer 字符串变量（线程安全）StringBuilder 字符串变量（非线程安全）

基本来说都是在性能上都是 StringBuilder > StringBuffer > String

四十九、show.loadUrl("file:///android_asset/test.html");

**访问资源文件
**

五十、Call requires permission which may be rejected by user: code should explicitly check to see if permission

警告出现，将需要执行的代码放到Try Catch块中。

五十一、常用第三方库：

com.google.zxing:core 实现二维码生成和解析
io.reactivex:rxandroid 处理网络请求
io.reactivex:rxjava 异步操作，链式操作
com.squareup.retrofit2:retrofit 网络请求框架
com.squareup.retrofit2:adapter-rxjava 支持rxjava
com.squareup.retrofit2:converter-gson Gson做为json的转换器
org.greenrobot:eventbus “发布/订阅”模式的事件总线
com.umeng.analytics:analytics 友盟
com.android.support:multidex 解决Dex包超过65535
com.flipboard:bottomsheet-core 底部滑出面板
com.flipboard:bottomsheet-commons 底部滑出面板
com.github.zhaokaiqiang.klog Log开源项目
com.github.bumptech.glide 图片加载框架
com.readystatesoftware.systembartint 沉浸式状态栏
com.jcodecraeer:xrecyclerview recycleView
com.mylhyl:acp 权限控制

五十二、View显示状态切换

显示 View.VISIBLE
隐藏 View.INVISIBLE
移除 View.GONE

五十三、

五十四、
