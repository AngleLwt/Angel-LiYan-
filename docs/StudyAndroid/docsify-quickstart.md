# Android 杂集1
## 视图绑定取代findViewById
对比使用findviewById的优点

1.Null 安全：并没有R.id.tv 但是还要去findviewByid就会崩溃
2.类型安全：findviewByid一个TextView但是强转成一个imageView就会崩溃
欲使用此功能，请先保证你的android studio 版本不低于3.6
build.gradle 文件中配置 viewBinding 选项:


```
//Android Studio 3.6
android {
    viewBinding {
        enabled = true
    }
}


// Android Studio 4.0
android {
    buildFeatures {
        viewBinding = true
    }
}


```
这个类里面就是对里面所有view的映射

没使用视图绑定：
```
public class MainActivity extends AppCompatActivity {
 @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        TextView textView = findViewById(R.id.text);
        textView.setText("ok");
    }
 }

```
使用了视图绑定:
```
public class MainActivity extends AppCompatActivity {
@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding root = ActivityMainBinding.inflate(LayoutInflater.from(this));
        setContentView(root.getRoot());
        root.text.setText("ok");
    }
 }
```
修改gradle配置文件
`/Applications/Android/Studio.app/Contents/plugins/android/lib/templates/gradle-projects/NewAndroidModule/root/build.gradle.ftl `
谷歌总结
findViewById 是许多用户可见 bug 的来源: 我们很容易传入一个布局中根本不存在的 id，从而导致空指针异常而崩溃；由于此方法类型不安全，也很容易使人写出像 findViewById<TextView>(R.id.image) 这样的，导致类型转换错误的代码。为了解决这些问题，视图绑定把 findViewById 替换成了更加简洁和安全的实现。

## Android 自定义模板
定制自己的文件生成插件：
为了解决上面用别人的插件存在无法定制的问题，下面就开始带大家从0开始，15分钟打造出自己的插件。
废话不多说，先看看效果：

1.安装
打开 Android Studio，Preferences – Plugins – Brown Repositories, 搜索TemplateBuilder 并下载，下载之后重启 Sstudio 即可使用。
2.写模版
下面我举个例子，假设待导出的模板文件是UserActivity类，代码如下：
```
public class UserActivity extends Activity {
    
    private TextView mUserName;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_user);
    mUserName = (TextView) findViewById(R.id.user_name);
    }
  
}
```
代码很简单，假设我们想让引入模板时mUserName属性名是可配的，并且在 Activity 中是否调用 setContentView 方法也是可配的，那我们就需要这样改写该类：
```
public class UserActivity extends Activity {

    private TextView ${textViewName};

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        <#if setContentView>
        setContentView(R.layout.activity_user);
        ${textViewName} = (TextView) findViewById(R.id.user_name);
        </#if>
    }
}
```
3.使用TemplateBuilder

我们用到了textViewName和setContentView两个变量，所以当我们按下ALT + T时就要在对应的 Input data 区域点击Add来添加两个对应的变量。
需要填写的属性如下：![](https://upload-images.jianshu.io/upload_images/21988850-47c3aa3a576b54d4.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

我们用到了textViewName和setContentView两个变量，所以当我们按下ALT + T时就要在对应的 Input data 区域点击Add来添加两个对应的变量。

>ActionID:代表该Action的唯一的ID，一般的格式为：pluginName.ID
ClassName:类名
Name:就是最终插件在菜单上的名称
Description:对这个Action的描述信息

选择这个Action即将存在的位置
添加完模板变量后导出，重启 IDE 选择导入该模板，此时便能看到刚才配置的两个变量，你可以输入不同的值来验证模板的正确性。
//设置模版样式
![](https://upload-images.jianshu.io/upload_images/21988850-8e6823226aa06198.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)



```
    id ：唯一标识，最终会通过它获取字符串 是否选中等等
    name：界面上的左边的提示语
    type : 输入值类型  可以是string int boolean 等等
    constraints：填写值的约束  如文件名不能重复等等
    suggest：建议值，比如填写ActivityName的时候，会给出一个布局文件的建议值。
    default:默认值
    help:显示的帮助提升语
```
注意：这是studio系统自带模板路径
`/Applications/Android/Studio.app/Contents/plugins/android/lib/templates/activities `
点击Finish就完成了，重启studio就可以使用私人定制模板了

我将自己写的模版
EmptyActivity
AngelAcitvity
MVP
上传git
