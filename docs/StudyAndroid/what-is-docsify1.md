# Android 笔记2
## RxJava
 1.观察者模式

*   定义：
    定义对象间一对多的依赖依赖关系，当一个对象改变状态时，所有依赖它的对象会收到通知并自动更新。
*   理解：
    其实就是发布订阅模式，发布者发布信息，订阅者获取信息，订阅了就能收到信息，没订阅就收不到信息。属于行为型设计模式的一种。
   使用场景：
    有一个微信公众号服务，不定时发布一些消息，关注公众号就可以收到推送消息，取消关注就收不到推送消息。其中，客户是观察者，公众号是被观察者。

 2.什么是RxJava（ReactiveX.io链式编程）

*   定义：一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库。
    总结：RxJava 是一个 基于事件流、实现异步操作的库
    理解：RXJava是一个响应式编程框架 ，采用观察者设计模式，观察者模式本身的目的就是『后台处理，前台回调』的异步机制

*   优点：
    由于 RxJava是**基于事件流的链式调用**，所以使得 RxJava：

> 逻辑简洁
> 实现优雅
> 使用简单

*   作用：
    实现异步操作

> 类似于 Android中的 AsyncTask 、Handler作用

 3.Rxjava基本概念（观察者模式）

*   案例：按钮点击处理、广播注册
    通过setOnClickListener()方法，Button持有OnClickListener的引用；当用户击时，Button自动调用OnClickListener的onClick()方法。
    Button——>被观察者
    OnClickListener——>观察者
    setOnClickListener ——>订阅
    onClick ——>事件

 4\. RxJava 有3个基本概念及原理

> 1.Observable(被观察者)
> 2.Observer(观察者)
> 3.subscribe(订阅)事件。

*   原理：被观察者`（Observable）` 通过 订阅`（Subscribe）`按顺序发送事件 给观察者`（Observer）`， 观察者`（Observer）` 按顺序接收事件 & 作出对应的响应动作。

*   普通事件 ： onNext() 接收被观察者发送的消息

*   特殊的事件：
    onCompleted() 事件队列完结
    onError () 事件队列异常

 注意：

> 1）RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。
> 2）RxJava 规定，onNext() 接收被观察者发送的消息、可以执行多次；当不会再有新的 onNext () 发出时，需要触发 onCompleted () 方法作为标志。onError():事件队列异常。在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出。
> 3）在一个正确运行的事件序列中, onCompleted() 和 onError () 有且只有一个，并且是事件序列中的最后一个。
> 4）需要注意的是，onCompleted()和 onError () 二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。

 5.调度器

*   RxJava中调度器设置方法

```
subscribeOn():或者叫做事件产生的线程。 
指定 subscribe()所发生的线程，即 Observable.OnSubscribe 被激活时所处的线程。

observeOn():或者叫做事件消费的线程。指定 Subscriber所运行在的线程。

```

 几种调度器

```
在RxJava 中Scheduler——调度器，相当于线程控制器，
RxJava 通过它来指定每一段代码应该运行在什么样的线程。
RxJava 已经内置了几个Scheduler，它们已经适合大多数的使用场景：
  1:Schedulers.immediate():直接在当前线程运行，相当于不指定线程。这是默认的Scheduler
  2:Schedulers.newThread():总是启用新线程，并在新线程执行操作。
  3:Schedulers.io():I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler
      行为模式和 newThread()差不多区别在于 io()的内部实现是是用一个无数量上限的线 
      程池可以重用空闲的线程，因此多数情况下 io()比 newThread()更有效率。不要把计算
      工作放在 io()中可以避免创建不必要的线程。
  4:Schedulers.computation():计算所使用的 Scheduler这个计算指的是 CPU密集型计算，
       即不会被 I/O 等操作限制性能的操作，例如图形的计算。这个 Scheduler使用的固定  
       的线程池，大小为 CPU核数。不要把 I/O 操作放在computation()中，否则 I/O 操作
       的等待时间会浪费CPU。
  5.AndroidSchedulers.mainThread():Android 还有一个专用的
        它指定的操作将在 Android主线程运行。有了这几个 Scheduler，就可以使用
        subscribeOn()和 observeOn()两个方法来对线程进行控制了。 

```

 6.依赖库

```
    //RxJava
    implementation 'io.reactivex.rxjava2:rxjava:2.2.4'
    implementation 'io.reactivex.rxjava2:rxandroid:2.1.0'
    implementation 'com.squareup.retrofit2:retrofit:2.5.0'//retrofit 库
    implementation 'com.squareup.retrofit2:converter-gson:2.5.0'//转换器，请求结果转换成Model
    implementation 'com.squareup.retrofit2:adapter-rxjava2:2.5.0'//配合Rxjava 使用
    implementation 'com.google.code.gson:gson:2.6.2'//Gson 库

```

 7.简单使用

```
    public static void baseRx(){
        //1.创建被观察者
        Observable<String> observable = Observable.create(new ObservableOnSubscribe<String>() {
            @Override
            public void subscribe(ObservableEmitter<String> emitter) {
                emitter.onNext("1111");
                emitter.onNext("2222");
                emitter.onNext("3333");
                emitter.onNext("4444");
                //emitter.onError(new Throwable("abc"));
                //emitter.onComplete();
            }
        });

        //2.创建观察者
        Observer<String> observer = new Observer<String>() {

            @Override
            public void onSubscribe(Disposable d) {//关闭线程
                Log.e(TAG, "onSubscribe: " );
            }

            @Override
            public void onNext(String s) {
                Log.e(TAG, "onNext: "+ s );
            }

            @Override
            public void onError(Throwable e) {//失败
                Log.e(TAG, "onError: "+e.getMessage() );
            }

            @Override
            public void onComplete() {//成功
                Log.e(TAG, "onComplete: " );
            }
        };
        //3.被观察者订阅观察者
        observable.subscribe(observer);

        //线程切换
        observable
                //被订阅者在子线程中
                .subscribeOn(Schedulers.io())
                //订阅者在主线程中
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(observer);

        //观察中可以重复指定线程
        observable
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())//主
                .observeOn(Schedulers.io())//子
                .observeOn(AndroidSchedulers.mainThread())//主
                .subscribe(observer);
    }

```

*   演示链式调用

 8.Android功能使用

```
    private void rxAndroid() {

        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(MyServer.Url)
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        MyServer myServer = retrofit.create(MyServer.class);

        Observable<ResponseBody> call = myServer.getDate();

        call.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<ResponseBody>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(ResponseBody responseBody) {

                        try {
                            Log.e(TAG, "onNext: "+responseBody.string() );
                        } catch (IOException e) {
                            e.printStackTrace();
                        }
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
    }

    private void rxAndroidBean() {

        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(MyServer.Url)
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        MyServer myServer = retrofit.create(MyServer.class);

        Observable<Bean> call = myServer.getDate2();

        call.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<Bean>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(Bean responseBody) {

                        Log.e(TAG, "onNext: "+ responseBody.getRESULT() );
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
    }

```

 9.其他操作符使用（ 查看操作符）

*   创建操作符

```
    //遍历输出
    public static void rxFrom(){
        Integer[] a = {1,2,3,4,5};
        // Observable.fromArray(1,2,3,4)
        //Observable.fromArray("a","b","c")
        Observable.fromArray(a).subscribe(new Consumer<Integer>() {
            @Override
            public void accept(Integer integer) throws Exception {
                Log.e(TAG, "accept: "+integer);
            }
        });
    }

    //数组合并输出
    public static void rxJust(){
        Integer[] a = {1,2,3};
        Integer[] b = {9,8,7};
        Observable.just(a,b).subscribe(new Consumer<Integer[]>() {
            @Override
            public void accept(Integer[] integers) throws Exception {
                for (Integer i: integers) {
                    Log.e(TAG, "accept: "+i);
                }
            }
        });
    }

    //范围输出
    public  static  void rxRange(){
        Observable.range(0,20).subscribe(new Consumer<Integer>() {
            @Override
            public void accept(Integer integer) throws Exception {
                Log.e(TAG, "accept: "+integer );
            }
        });
    }

  //定时器
    public static void rxInterval(){
        Observable.interval(1,1,TimeUnit.SECONDS).subscribe(new Consumer<Long>() {
            @Override
            public void accept(Long aLong) throws Exception {
                Log.e(TAG, "accept: "+aLong );
            }
        });
    }
    //闪屏
    private void rxjavaInterval() {
        final Long time = 5L;
        subscribe = Observable.interval(1, TimeUnit.SECONDS)
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Consumer<Long>() {
                    @Override
                    public void accept(Long aLong) throws Exception {
                        Log.e("TAG", "倒计时：" + aLong);
                        if (aLong < time && !subscribe.isDisposed()) {
                            tv.setText("记录改变生活" + (time - aLong - 1));
                        } else {
                            Intent intent = new Intent(WelcomActivity.this, MainActivity.class);
                            startActivity(intent);
                            finish();
                        }
                    }
                });
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        subscribe.dispose();
        subscribe = null;
    }

```

*   过滤操作符

```
    //过滤输出
    public static void rxFilter(){
        Integer[] a = {1,2,3,4,5};
        Observable.fromArray(a).filter(new Predicate<Integer>() {
            @Override
            public boolean test(Integer integer) throws Exception {
                if (integer>3){
                    return true;
                }
                return false;
            }
        }).subscribe(new Consumer<Integer>() {
            @Override
            public void accept(Integer integer) throws Exception {
                Log.e(TAG, "accept: "+integer );
            }
        });
    }

```

*   变换操作符

①Map：通过指定一个Fun函数将Observeble转换成一个新的Observable对象并发射，观察者收到新的observable处理。

```
    public static void rxMap(){
        Integer[] a = {1,2,3,4,5};
        Observable.fromArray(a).map(new Function<Integer, String>() {
            @Override
            public String apply(Integer integer) {
                return integer+"abc";
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) {
                Log.e(TAG, "accept: "+s );
            }
        });
    }

```

②FlatMap：将被观察者发送的事件序列进行 拆分 & 单独转换，再合并成一个新的事件序列，最后再进行发送

```
    public static void rxFlatMap(){
        Integer[] a = {1,2,3,4,5};
        Observable.fromArray(a).flatMap(new Function<Integer, ObservableSource<String>>() {
            @Override
            public ObservableSource<String> apply(Integer integer) throws Exception {
                String[] strs = new String[3];
                for (int i =0;i<strs.length;i++){
                    strs[i] = integer +  strs[I];
                }
                return Observable.fromArray(strs);
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e(TAG, "accept: "+s );
            }
        });
    }

```

注意：flatmap的合并允许交叉，也就是说可能会交错的发送事件，最终结果的顺序可能并不是由原始发送时候的顺序。
③concatMap：concatMap操作符功能与flatmap功能一致，不过他解决了flatmap交叉问题，提供了一种能够把发射的值连续在一起的函数，而并不是合并他们。具体使用和flatmap一致。

*   组合/合并操作符

```
    //Observable压缩合并ZIP:合并 多个被观察者（Observable）发送的事件，生成一个新的事件序列（即组合过后的事件序列），并最终发送
    public static void rxZip(){
        Integer[] a= {1,2,3};
        Integer[] b={4,5,6};
        Observable<Integer> observableA = Observable.fromArray(a);
        Observable<Integer> observableB = Observable.fromArray(b);

        Observable.zip(observableA, observableB, new BiFunction<Integer, Integer, String>() {

            @Override
            public String apply(Integer integer, Integer integer2) throws Exception {
                return integer + ":" + integer2;
            }
        }).subscribe(new Consumer<String>() {
            @Override
            public void accept(String s) throws Exception {
                Log.e(TAG, "accept: "+s );
            }
        });

    }

    //合并：组合多个被观察者一起发送数据，合并后 按时间线并行执行
    //merge（）组合被观察者数量≤4个,而mergeArray（）则可＞4个
    public static void rxMerge(){
        Integer[] a ={1,2,3};
        String[] b = {"abc","aaa","bbb"};
        char[] c = {'a','b','c'};

        Observable<Integer> A = Observable.fromArray(a);
        Observable<String> B = Observable.fromArray(b);
        Observable<char[]> C = Observable.fromArray(c);

        Observable
                .merge(A,B,C)
                .subscribe(new Consumer<Serializable>() {
                    @Override
                    public void accept(Serializable serializable) throws Exception {
                        Log.e(TAG, "accept: ."+serializable );
                    }
                });
    }

```

*   功能性操作符

```
subscribOn()
observeOn()

```

 10.RxAndroid好处

*   用途
    是一个实现异步操作的库，具有简洁的链式代码，提供强大的数据变换。

*   优势
    异步好简单、代码好简洁，一个简单、一个简洁，这就意味着工作效率。

*   注意
    subscribeOn只能定义一次，除非是在定义doOnSubscribe
    observeOn可以定义多次，决定后续代码所在的线程

 11.RxJava：好处

> 使用Rxjava的好处在于，我们可以方便的切换方法的执行线程，对线程动态切换，该过程无需我们自己手动创建和启动线程。使用Rxjava创建的代码虽然出现在同一个线程中，但是我们可以设置使得不同方法在不同线程中执行。上述功能的实现主要归功于RxJava的Scheduler实现，Scheduler 提供了『后台处理，前台回调』的异步机制。

 12\. AsyncTask与RxJava的区别

*   RxJava 实现异步操作是通过一种扩展的观察者模式来实现的。
*   异步、简洁（逻辑、代码读写）。
*   RxJava 内部支持多线程操作
*   AyncTask是采用线程池的形式实现的。
*   出现错误的处理-rxjava 自身有错误的方法回调，aync无法做到。
*   并发的请求，rxjava 通过操作符能够完成各种并发情况，而AyncTask不行。

## 广播
 1 . 定义

即 广播，是一个全局的监听器，属于`Android`四大组件之一

> Android 广播分为两个角色：广播发送者、广播接收者

 2\. 作用

监听 / 接收 应用 `App` 发出的广播消息，并做出响应

 3\. 应用场景

*   `Android`不同组件间的通信（含 ：应用内 / 不同应用之间）
*   多线程通信
*   与 `Android` 系统在特定情况下的通信
    如：电话呼入时、网络可用时

 4\. 实现原理

 4.1 采用的模型

*   `Android`中的广播使用了设计模式中的**观察者模式**：基于消息的发布 / 订阅事件模型
    因此，Android将广播的发送者 和 接收者 解耦，使得系统方便集成，更易扩展

 4.2 模型讲解

*   模型中有3个角色：

    1.  消息订阅者（广播接收者）
    2.  消息发布者（广播发布者）
    3.  消息中心（`AMS`，即`Activity Manager Service`）

 5\. 使用流程

/angles/
 5.1 自定义广播接收者BroadcastReceiver

*   继承`BroadcastReceivre`基类
*   必须复写抽象方法`onReceive()`方法
    ①广播接收器接收到相应广播后，会自动回调 onReceive() 方法
    ②一般情况下，onReceive方法会涉及 与 其他组件之间的交互，如发送Notification、启动Service等
    ③默认情况下，广播接收器运行在 UI 线程，因此，onReceive()方法不能执行耗时操作，否则将导致ANR

```
// 继承BroadcastReceivre基类
public class MyReceiver extends BroadcastReceiver {

  // 复写onReceive()方法
  // 接收到广播后，则自动调用该方法
  @Override
  public void onReceive(Context context, Intent intent) {
   //写入接收广播后的操作
    }
}

```

 5.2 广播接收器注册

注册的方式分为两种：静态注册、动态注册

 5.2.1 静态注册

*   注册方式：在AndroidManifest.xml里通过**<receive>**标签声明
*   属性说明：

```
<receiver 
    android:enabled=["true" | "false"]
//此broadcastReceiver能否接收其他App的发出的广播
//默认值是由receiver中有无intent-filter决定的：如果有intent-filter，默认值为true，否则为false
    android:exported=["true" | "false"]
    android:icon="drawable resource"
    android:label="string resource"
//继承BroadcastReceiver子类的类名
    android:name=".MyReceiver"
//具有相应权限的广播发送者发送的广播才能被此BroadcastReceiver所接收；
    android:permission="string"
//BroadcastReceiver运行所处的进程
//默认为app的进程，可以指定独立的进程
//注：Android四大基本组件都可以通过此属性指定自己的独立进程
    android:process="string" >

//用于指定此广播接收器将接收的广播类型
//本示例中给出的是用于接收开机的广播
 <intent-filter>
<action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>

```

* 注册示例

Manifest
```
<receiver 
    //此广播接收者类是mBroadcastReceiver
    android:name=".MyReceiver" >
    //用于接收网络状态改变时发出的广播
    <intent-filter>
        <action android:name="android.intent.action.BOOT_COMPLETED"/>
    </intent-filter>
</receiver>

```
java代码
```
//创建intent对象
 Intent intent = new Intent("com.example.broadreciver.AngelReceiver");
//设置组建
        intent.setComponent(new ComponentName(this, AngelReceiver.class));
//发送
        sendBroadcast(intent);
```

当此 `App`首次启动时，系统会**自动**实例化MyReceiver类，并注册到系统中。

 5.2.2 动态注册

*   注册方式：在代码中调用`Context.registerReceiver（）`方法

*   具体代码如下：

```

// 选择在Activity生命周期方法中的onResume()中注册
@Override
  protected void onResume(){
      super.onResume();

    // 1\. 实例化BroadcastReceiver子类 &  IntentFilter
     AngleReceiver mBroadcastReceiver = new AngleReceiver();
     IntentFilter intentFilter = new IntentFilter();

    // 2\. 设置接收广播的类型,网络变化
       IntentFilter intentFilter2 = new IntentFilter("android.net.conn.CONNECTIVITY_CHANGE");


    // 3\. 动态注册：调用Context的registerReceiver（）方法
     registerReceiver(mBroadcastReceiver, intentFilter);
 }

// 注册广播后，要在相应位置记得销毁广播
// 即在onPause() 中unregisterReceiver(mBroadcastReceiver)
// 当此Activity实例化时，会动态将MyBroadcastReceiver注册到系统中
// 当此Activity销毁时，动态注册的MyBroadcastReceiver将不再接收到相应的广播。
 @Override
 protected void onPause() {
     super.onPause();
      //销毁在onResume()方法中的广播
     unregisterReceiver(mBroadcastReceiver);
     }
}

```

 特别注意

*   动态广播最好在`Activity` 的 `onResume()`注册、`onPause()`注销。
*   原因：

> 1.  对于动态广播，有注册就必然得有注销，否则会导致**内存泄露**
> 2.  重复注册、重复注销也不允许

**在onResume()注册、onPause()注销是因为onPause()在App死亡前一定会被执行，从而保证广播在App死亡前一定会被注销，从而防止内存泄露。**

 5.2.3 两种注册方式的区别

/angles/

 5.3 广播发送者向AMS发送广播

 5.3.1 广播的发送

*   广播 是 用”意图（`Intent`）“标识
*   定义广播的本质 = 定义广播所具备的“意图（`Intent`）”
*   广播发送 = 广播发送者 将此广播的“意图（`Intent`）”通过**sendBroadcast（）方法**发送出去

 5.3.2 广播的类型

广播的类型主要分为5类：

*   普通广播（`Normal Broadcast`）
*   系统广播（`System Broadcast`）
*   有序广播（`Ordered Broadcast`）
*   粘性广播（`Sticky Broadcast`）
*   App应用内广播（`Local Broadcast`）

具体说明如下：
**1\. 普通广播（Normal Broadcast）**
即 开发者自身定义 `intent`的广播（最常用）。发送广播使用如下：

```
Intent intent = new Intent("angle");//"angle"是广播的action
//8.0开始自定义广播系统不建议静态注册,如果静态注册,需要加下面这行代码,指定广播的类
//intent.setComponent(new ComponentName(this,MyReceiver.class));
sendBroadcast(intent);

```

若被注册了的广播接收者中注册时`intentFilter`的`action`与上述匹配，则会接收此广播（即进行回调`onReceive()`）。若发送广播有相应权限，那么广播接收者也需要相应权限

**2\. 系统广播（System Broadcast）**

*   Android中内置了多个系统广播：只要涉及到手机的基本操作（如开机、网络状态变化、拍照等等），都会发出相应的广播
*   每个广播都有特定的Intent - Filter（包括具体的action），Android系统广播action如下：

| 系统操作 | action |
| --- | --- |
| 监听网络变化 | android.net.conn.CONNECTIVITY_CHANGE |
| 关闭或打开飞行模式 | Intent.ACTION_AIRPLANE_MODE_CHANGED |
| 充电时或电量发生变化 | Intent.ACTION_BATTERY_CHANGED |
| 电池电量低 | Intent.ACTION_BATTERY_LOW |
| 电池电量充足 | Intent.ACTION_BATTERY_OKAY |
| 系统启动完成后(仅广播一次) | Intent.ACTION_BOOT_COMPLETED |
| 按下照相时的拍照按键(硬件按键) | Intent.ACTION_CAMERA_BUTTON |
| 屏幕锁屏 | Intent.Intent.ACTION_SCREEN_OFF |
| 屏幕解锁 | Intent.Intent.ACTION_SCREEN_ON |
| 设备当前设置被改变时(界面语言、设备方向等) | Intent.ACTION_CONFIGURATION_CHANGED |
| 插入耳机时 | Intent.ACTION_HEADSET_PLUG |
| 未正确移除SD卡但已取出来时(正确移除方法:设置--SD卡和设备内存--卸载SD卡) | Intent.ACTION_MEDIA_BAD_REMOVAL |
| 插入外部储存装置（如SD卡） | Intent.ACTION_MEDIA_CHECKING |
| 成功安装APK | Intent.ACTION_PACKAGE_ADDED |
| 成功删除APK | Intent.ACTION_PACKAGE_REMOVED |
| 重启设备 | Intent.ACTION_REBOOT |
| 屏幕被关闭 | Intent.ACTION_SCREEN_OFF |
| 屏幕被打开 | Intent.ACTION_SCREEN_ON |
| 关闭系统时 | Intent.ACTION_SHUTDOWN |
| 重启设备 | Intent.ACTION_REBOOT |

只能动态注册的广播:

```
android.intent.action.SCREEN_ON//屏幕打开
android.intent.action.SCREEN_OFF//屏幕关闭
android.intent.action.BATTERY_CHANGED//电量变化
android.intent.action.CONFIGURATION_CHANGED//设备的配置信息已经改变
android.intent.action.TIME_TICK//时间相关的广播
android.net.conn.CONNECTIVITY_CHANGE//网络状态发生改变,Android7.0之后只能动态注册

```

网络改变接收器：

```
public class NetReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        ConnectivityManager connectivityManager = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
        NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
        if (activeNetworkInfo != null && activeNetworkInfo.isConnected()) {
            Toast.makeText(context, "net isConnected", Toast.LENGTH_SHORT).show();
            Log.e("TAG", "网络连接成功");
        } else {
            Toast.makeText(context, "net disConnected", Toast.LENGTH_SHORT).show();
            Log.e("TAG", "网络连接失败");
        }
    }
}

```
```
  @Override
    protected void onResume() {
// 创建广播对象
  netReceiver = new NetReceiver();
//创建过滤器
        IntentFilter intentFilter2 = new 
IntentFilter("android.net.conn.CONNECTIVITY_CHANGE");
//注册广播
  registerReceiver(netReceiver, intentFilter2);
}
最后不要忘记在Pause解绑/
```

**3\. 有序广播（Ordered Broadcast）**

*   定义：`发送出去的广播被广播接收者按照先后顺序接收,有序是针对广播接收者而言的`

*   广播接受者接收广播的顺序规则（同时面向静态和动态注册的广播接受者）
    a. 按照Priority属性值从大-小排序；
    b. Priority属性相同者，动态注册的广播优先；

*   特点
    a. 接收广播按顺序接收
    b. 先接收的广播接收者可以对广播进行截断，即后接收的广播接收者不再接收到此广播；
    c. 先接收的广播接收者可以对广播进行修改，那么后接收的广播接收者将接收到被修改后的广播

具体使用
有序广播的使用过程与普通广播非常类似，差异仅在于广播的发送方式：

```

 private void order() {
        Intent intent = new Intent("com.example.broadreciver.AngelOrderReceiver");
        sendOrderedBroadcast(intent, null);

    }
 @Override
    protected void onResume() {
//        获取广播对象

        oneReceiver = new OneReceiver();
        twoReceiver = new TwoReceiver();
//        添加过滤器
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.addAction("com.example.broadreciver.AngelOrderReceiver");
     
        IntentFilter intentFilter1 = new IntentFilter();
        intentFilter1.addAction("com.example.broadreciver.AngelOrderReceiver");
//设置优先级别（同样的优先级动态优先于静态，android 8.0以后取消了静态注册与动态同步）
        intentFilter.setPriority(100);
        intentFilter1.setPriority(200);
//        注册广播
        registerReceiver(oneReceiver, intentFilter);
        registerReceiver(twoReceiver, intentFilter1);
}

```

注册广播:

```
private void register() {

        mCityReceiver = new CityReceiver();
        IntentFilter intentFilter2 = new IntentFilter();
        intentFilter2.setPriority(0);
        intentFilter2.addAction("com.cumulus.xts.AWARD");
        registerReceiver(mCityReceiver, intentFilter2);

        mProvinceReceiver = new ProvinceReceiver();
        IntentFilter intentFilter = new IntentFilter();
        intentFilter.setPriority(1000);
        intentFilter.addAction("com.cumulus.xts.AWARD");
        registerReceiver(mProvinceReceiver, intentFilter);

        mLaoMaReceiver = new LaoMaReceiver();
        IntentFilter intentFilter3 = new IntentFilter();
        intentFilter3.setPriority(-1000);
        intentFilter3.addAction("com.cumulus.xts.AWARD");
        registerReceiver(mLaoMaReceiver, intentFilter3);
    }

    @Override
    protected void onPause() {
        super.onPause();

        unregisterReceiver(mProvinceReceiver);
        unregisterReceiver(mCityReceiver);
        unregisterReceiver(mLaoMaReceiver);
    }

```

```
public class ProvinceReceiver extends BroadcastReceiver {
    private static final String TAG = "ProvinceReceiver";

    @Override
    public void onReceive(Context context, Intent intent) {
        Log.d(TAG, "州里收到通知: "+getResultData());
        //篡改数据
        setResultData("特朗普个奥巴马发了7斤大米");
    }
}

```

**4\. 粘性广播（Sticky Broadcast）**

> 粘性消息在发送后就一直存在于系统的消息容器里面，等待对应的处理器去处理，如果暂时没有处理器处理这个消息则一直在消息容器里面处于等待状态，粘性广播的Receiver如果被销毁，那么下次重建时会自动接收到消息数据。

注意：普通广播和粘性消息不能被截获，而有序广播是可以被截获的。

发送粘性广播需要权限（这里的权限是保存信息的权限和由系统发送未处理的广播的权限）

`<uses-permission android:name="android.permission.BROADCAST_STICKY" />`

对于粘性广播的发送，和普通广播的发送方式是一致的

```
        Intent intent = new Intent("com.xts.sticky");
        intent.putExtra("data","粘性广播来了");
        sendStickyBroadcast(intent);
        //removeStickyBroadcast(intent);//移除广播
    }

```

需要知道的是粘性广播是普通广播的一种，因此也可以使用普通广播接收器来接收，当然粘性广播还有另一种常用的接收方式。

4.1.使用普通广播接收器，注意，必须在Manifiest或者发送之前接收（这和定义有点违背，因为这种方式不是正确的接收方式）

```
public class StickyBroadcastReceiver extends BroadcastReceiver {

    public static final String Action = "com.xts.sticky";
    @Override
    public void onReceive(Context context, Intent intent)
    {
        Log.d("StickyBroadcastReceiver", "register: "+intent.getStringExtra("data"));
    }

}

```

```
<receiver android:name=".StickyBroadcastReceiver"
    <intent-filter >
         <action android:name="com.xts.sticky"/>
    </intent-filter>
</receiver>

```

4.2.使用正确的方式接收(推荐)

正确的接收方式不应该使用BroadcastReceiver就可以接收到
只需要将Activity的onResume稍作修改即可

```
IntentFilter intentFilter4 = new IntentFilter();
       intentFilter4.addAction("com.xts.sticky");
       //注意，不需要接收器，否则可能无法接收到
       Intent intent = registerReceiver(null, intentFilter4);
       if (intent !=null){
           Log.d(TAG, "register: "+intent.getStringExtra("data"));
       }

```

这种广播也可以被移除 ，我们可以接收到 广播后调用 removeStickyBroadcast(intent);

粘性广播在Android5.0 & API 21中已经失效,

**5\. App应用内广播（Local Broadcast）**

*   背景
    Android中的广播可以跨App直接通信（exported对于有intent-filter情况下默认值为true）

*   冲突
    可能出现的问题：

> *   其他App针对性发出与当前App intent-filter相匹配的广播，由此导致当前App不断接收广播并处理；
> *   其他App注册与当前App一致的intent-filter用于接收广播，获取广播具体信息；
> *   即会出现安全性 & 效率性的问题。

*   解决方案

> 使用App应用内广播（Local Broadcast）

> 1.App应用内广播可理解为一种局部广播，广播的发送者和接收者都同属于一个App。
> 2.相比于全局广播（普通广播），App应用内广播优势体现在：安全性高 & 效率高

具体使用1 - 将全局广播设置成局部广播

> 1.  注册广播时将exported属性设置为*false*，使得非本App内部发出的此广播不被接收；
> 2.  在广播发送和接收时，增设相应权限permission，用于权限验证；
> 3.  发送广播时指定该广播接收器所在的包名，此广播将只会发送到此包中的App内与之相匹配的有效广播接收器中。

`通过intent.setPackage(packageName)指定报名`
具体使用2 - 使用封装好的LocalBroadcastManager类
使用方式上与全局广播几乎相同，只是注册/取消注册广播接收器和发送广播时将参数的context变成了LocalBroadcastManager的单一实例

`注：对于LocalBroadcastManager方式发送的应用内广播，只能通过LocalBroadcastManager动态注册，不能静态注册`

```
//注册应用内广播接收器
1.
  private void location() {
//        创建连接对象
        Intent intent = new Intent("com.example.broadreciver.LocalReceiver");
//        本地广播管理器发送广播
        localBroadcastManager.sendBroadcast(intent);
    }
2.
   @Override
    protected void onResume() {
        super.onResume();
// 创建本地广播管理器设置全局
        localBroadcastManager = LocalBroadcastManager.getInstance(this);
//        获取广播对象并设置全局
   localReceiver = new LocalReceiver();
//        添加过滤器
IntentFilter intentFilter3 = new IntentFilter();  intentFilter3.addAction("com.example.broadreciver.LocalReceiver");

//        本地广播管理器注册广播
        localBroadcastManager.registerReceiver(localReceiver, intentFilter3);
}
//解绑广播
   @Override
    protected void onPause() {
        super.onPause();
        unregisterReceiver(localReceiver);
    }
```

 6\. 特别注意

对于不同注册方式的广播接收器回调OnReceive（Context context，Intent intent）中的context返回值是不一样的：

*   对于静态注册（全局+应用内广播），回调onReceive(context, intent)中的context返回值是：ReceiverRestrictedContext；
*   对于全局广播的动态注册，回调onReceive(context, intent)中的context返回值是：Activity Context；
*   对于应用内广播的动态注册（LocalBroadcastManager方式），回调onReceive(context, intent)中的context返回值是：Application Context。
*   对于应用内广播的动态注册（非LocalBroadcastManager方式），回调onReceive(context, intent)中的context返回值是：Activity Context；
*   在广播中启动activity的话，需要为intent加入FLAG_ACTIVITY_NEW_TASK的标记，不然会报错，因为需要一个栈来存放新打开的activity。
*   广播中弹出AlertDialog的话，需要设置对话框的类型为:TYPE_SYSTEM_ALERT不然是无法弹出的。

## MVP
 1.常见项目架构模型

*   你是否遇到过Activity/Fragment中成百上千行代码,完全无法维护,看着头疼?
*   你是否遇到过因后台接口还未写而你不能先写代码逻辑的情况?
*   你是否遇到过用MVC架构写的项目进行单元测试时的深深无奈?
*   开发的时候一般都会使用一些架构,好处就是代码逻辑清晰,将第模块之间的耦合,方便测试,维护;
    常见的的架构:MVC、MVP、MVVM

 2.MVC架构

 ①MVC概述

*   MVC框架模式最早由Trygve Reenskaug 于1978年在Smalltalk-80系统上首次提出。经过了这么多年的发展，当然会演变出不同的版本，但核心没变依旧还是三层模型Model-View-Control。
*   可能由于MVP、MVVM的兴起，MVC在android中的应用变得越来越少了，但MVC是基础，理解好MVC才能更好的理解MVP,MVVM。因为后两种都是基于MVC发展而来的。

![image](//upload-images.jianshu.io/upload_images/5887463-1f603ad30b190091.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/934)

得出：从上图可以看出M层和V层有连接关系,而Activity有时候既充当了控制层又充当了视图层,导致项目维护比较麻烦.

*   视图层(View)
    对应于xml布局文件和java代码动态view部分

*   控制层(Controller)
    MVC中Android的控制层是由Activity来承担的，Activity本来主要是作为初始化页面，展示数据的操作，但是因为XML视图功能太弱，所以Activity既要负责视图的显示又要加入控制逻辑，承担的功能过多。

*   模型层(Model)
    针对业务模型，建立的数据结构和相关的类，它主要负责网络请求，数据库处理，I/O的操作。

由于android中有个god object的存在activity，再加上android中xml布局的功能性太弱，所以activity承担了绝大部分的工作。因为activity扮演了controller和view的工作，所以controller和view不太好彻底解耦，但是在一定程度上我们还是可以解耦的。

 ②MVC架构优缺点

*   A. 缺点

> M层和V层有连接关系,没有解耦,导致维护困难.
> Activity/Fragment中的代码过多,难以维护.
> Activity中有很多关于视图UI的显示代码，因此View视图和Activity控制器并不是完全分离的，当Activity类业务过多的时候，会变得难以管理和维护.尤其是当UI的状态数据，跟持久化的数据混杂在一起，变得极为混乱.

*   B. 优点

> 控制层和View层都在Activity中进行操作，数据操作方便.
> 模块职责划分明确.主要划分层M,V,C三个模块.

 ③MVC总结

*   具有一定的分层，model彻底解耦，controller和view并没有解耦

*   层与层之间的交互尽量使用回调或者去使用消息机制去完成，尽量避免直接持有

*   controller和view在android中无法做到彻底分离，但在代码逻辑层面一定要分清

*   业务逻辑被放置在model层，能够更好的复用和修改增加业务

 3.MVP架构

![image](//upload-images.jianshu.io/upload_images/5887463-ffae303086aa74ae.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/744)

 ①概述

> MVP,即是Model,View,Presenter架构模式.看起来类似MVC,其实不然.从上图能看到Model层和View层没有相连接,完全解耦.

 ②分层

*   M层:模型层(Model),此层和MVC中的M层作用类似.主要是实体类,数据库,网络等存在的层面

*   V层:视图层(View),在MVC中V层只包含XML文件,而MVP中V层包含XML,Activity和Fragment三者.理论上V层不涉及任何逻辑,只负责界面的改变,尽量把逻辑处理放到M层.

*   P层:通知层(Presenter),P层的主要作用就是连接V层和M层,起到一个通知传递数据的作用.

 ③原理

> 用户触碰界面触发事件,View层把事件通知Presenter层,Presenter层通知Model层处理这个事件,Model层处理后把结果发送到Presenter层,Presenter层再通知View层,最后View层做出改变.这是一整套流程.

 ④. MVP架构优缺点

*   A. 缺点

> MVP中接口过多.
> 每一个功能,相比于MVC要多写好几个文件.
> 如果某一个界面中需要请求多个服务器接口,这个界面文件中会实现很多的回调接口,导致代码繁杂.
> 如果更改了数据源和请求中参数,会导致更多的代码修改.
> 额外的代码复杂度及学习成本.

*   B. 优点

> 模块职责划分明显,层次清晰,接口功能清晰.
> Model层和View层分离,解耦.修改View而不影响Model.
> 功能复用度高,方便.一个Presenter可以复用于多个View,而不用更改Presenter的逻辑.
> 有利于测试驱动开发,以前的Android开发是难以进行单元测试.
> 如果后台接口还未写好,但已知返回数据类型的情况下,完全可以写出此接口完整的功能.

 四.MVP架构实战

①MVP简单案例

> 用户点击按钮后,Presenter层通知Model层请求处理网络数据,处理后Model层把结果数据发送给Presenter层,Presenter层再通知View层,然后View层改变TextView显示的内容.

 ②布局：

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:layout_gravity="center"
    android:gravity="center"
    android:orientation="vertical"
    tools:context=".view.SingleInterfaceActivity">

    <Button
        android:id="@+id/button"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="点击" />

    <TextView
        android:id="@+id/textView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_marginTop="100px"
        android:text="请点击上方按钮获取数据" />
</LinearLayout>

```

 ③页面：

```
public class SingleActivity extends AppCompatActivity {

    private Button button;
    private TextView textView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_single_interface);
        button = findViewById(R.id.button);
        textView = findViewById(R.id.textView);

        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {

            }
        });

    }
}

```

 ④Api:

```
public interface ApiServer {

    String baseUrl="http://www.qubaobei.com/ios/cf/";
    
    String param="dish_list.php?stage_id=1&limit=20&page=1";
//git请求方法
    @GET(param)
    Observable<FoodBean> getFood();
}


```

 ⑤bean:

```
public class ReBean {
    private String ERRORCODE;
    private List<String> RESULT;

    public String getERRORCODE() {
        return ERRORCODE;
    }

    public void setERRORCODE(String ERRORCODE) {
        this.ERRORCODE = ERRORCODE;
    }

    public List<String> getRESULT() {
        return RESULT;
    }

    public void setRESULT(List<String> RESULT) {
        this.RESULT = RESULT;
    }
}

```

 ⑥Model：

```
public interface MyModel {

    void getData(String appKey, CallBack<ReBean, String> callBack);
}

public class MyModelImpl implements MyModel {

    @Override
    public void getData(String appKey, final CallBack<ReBean, String> callBack) {

        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(MyServer.Url)
                .addCallAdapterFactory(RxJava2CallAdapterFactory.create())
                .addConverterFactory(GsonConverterFactory.create())
                .build();

        MyServer myServer = retrofit.create(MyServer.class);

        Observable<ReBean> data = myServer.getData(appKey);

        data.subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(new Observer<ReBean>() {
                    @Override
                    public void onSubscribe(Disposable d) {

                    }

                    @Override
                    public void onNext(ReBean reBean) {

                        //成功
                        //txt.setText(reBean.getRESULT().toString());
                        if (reBean!=null){
                            if (reBean.getERRORCODE().equals("0")){
                                callBack.onSuccess(reBean);
                            }else{
                                callBack.onFail("失败");
                            }
                        }else{
                            callBack.onFail("出现错误");
                        }
                    }

                    @Override
                    public void onError(Throwable e) {

                        //失败
                        callBack.onFail("出现错误");
                    }

                    @Override
                    public void onComplete() {

                    }
                });
    }

}

```

 ⑥CallBack:

```
public interface CallBack<K, V> {
    void onSuccess(K data);

    void onFail(V data);
}

```

 ⑦Presenter：

```
public interface MyPresenter {

    void getData(String appKey);
}

public class MyPresenterImpl implements  MyPresenter, CallBack<ReBean,String> {

    private MyModel myModel;
    private MyView myView;

    public MyPresenterImpl(MyModel myModel,MyView myView) {
        this.myModel = myModel;
        this.myView = myView;
    }

    @Override
    public void getData(String appKey) {
        if (myModel!=null){
            myModel.getData(appKey, this);
        }
    }

    @Override
    public void onSuccess(ReBean data) {
        //如果Model层请求数据成功,则此处应执行通知View层的代码   
        if(myView!=null){
            myView.onSuccess(data);
        }
    }

    @Override
    public void onFail(String data) {
        //如果Model层请求数据失败,则此处应执行通知View层的代码
        if(myView!=null){
            myView.onFail(data);
        }
    }

}

```

 ⑧IView：

```
public interface MyView<K,V> {

    void onSuccess(K data);

    void onFail(V data);
}

public class MainActivity extends AppCompatActivity implements View.OnClickListener, MyView<ReBean,String> {

    private Button btn;
    private TextView txt;
    private String appKey = "908ca46881994ffaa6ca20b31755b675";
    private MyPresenterImpl myPresenter;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        myPresenter = new MyPresenterImpl(new MyModelImpl(), this);

        initView();
    }

    private void initView() {
        btn = (Button) findViewById(R.id.btn);
        txt = (TextView) findViewById(R.id.txt);

        btn.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn:
                initData();
                break;
        }
    }

    private void initData() {
        myPresenter.getData(appKey);
    }

    @Override
    public void onSuccess(ReBean data) {
        txt.setText(data.getRESULT().toString());
    }

    @Override
    public void onFail(String data) {
        txt.setText(data);
    }
}
```
## Fragment懒加载
 Fragment的懒加载，在Fragment多的情况下是十分有必要的。否则要么每次切换Fragment请求数据，造成切换过程不流畅；要么设置ViewPager中Fragment的预加载数量为总的数量，一次性请求所有fragment中的数据，造成首次加载特别慢，且请求了一堆用户未必会看的页面的数据。

Fragment中
```
    @Override
    public void setUserVisibleHint(boolean isVisibleToUser) {
        super.setUserVisibleHint(isVisibleToUser);
//        如果当前Fragment可见
        if (isVisibleToUser){
            initData();
        }else {
//            不可见且数据不为空清空
           if (users!=null&&users.size()>0){
               users.clear();
           }
        }
    }

```
适配器
```
//    创建更新方法
    public void update(List<UserBean> data){
//        清空集合
        users.clear();
//        如果数据不为空添加到适配器中
        if (data!=null){
            users.addAll(data);
        }
        notifyDataSetChanged();
    }
```
## 动画
*android动画 补间动画(Tween Animation)*
![](https://upload-images.jianshu.io/upload_images/21988850-927f0be76badd898.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

一. 作用对象
1. 作用对象： 视图控件View。
2. 不可作用于属性或者其它对象。

二. 原理
1.通过: 开始的视图样式 & 结束的视图样式、中间动画变化过程由系统补全来确定一个动画。
2. 只是显示的位置变动，View的实际位置未改变，表现为View移动到其他地方，点击事件仍在原处才能响应。

>缺点：
当平移动画执行完停在最后的位置，结果焦点还在原来的位置(控件的属性没有真的被改变)

>优点：
制作方法简单方便。只需要为动画的第一个关键帧和最后一个关键帧创建内容，两个关键帧之间帧的内容由Flash自动生成，不需要人为处理。
相对于逐帧动画来说，补间动画更为连贯自然。
相对于逐帧动画来说，补间动画的文件更小，占用内存少。
四. 分类
平移（Translate）
旋转（Rotate）
缩放（Scale）
透明渐变（Alpha）
组合动画(AnimationSet)
五. 使用方式

1.通过xml形式
```
res/anim目录下
(1) 平移：translate_animation.xml

<?xml version="1.0" encoding="utf-8"?>
<!-- 平移 -->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="3000">

    <translate
        android:fromXDelta="0"
        android:fromYDelta="0"
        android:toXDelta="100%"
        android:toYDelta="100%"/>

    <!--android:fromXDelta="0"      水平方向x 移动的起始值-->
    <!--android:fromYDelta="0"      竖直方向y 移动的起始值-->
    <!--android:toXDelta="100%"     水平方向x 移动的结束值-->
    <!--android:toYDelta="100%"     竖直方向y 移动的结束值-->
</set>
(2) 旋转：rotate_animation.xml
<?xml version="1.0" encoding="utf-8"?>
<!-- 旋转 -->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="3000">

    <rotate
        android:pivotX="100%"
        android:pivotY="50%"
        android:fromDegrees="0"
        android:toDegrees="360"
        android:drawable="@drawable/ic_launcher_foreground"
        android:visible="true"/>

    <!--android:pivotX="100%"       旋转中心X坐标-->
    <!--android:pivotY="50%"        旋转中心Y坐标-->
    <!--android:fromDegrees="0"     旋转起始角度位置-->
    <!--android:toDegrees="360"     旋转结束角度位置-->

    <!--android:drawable="@drawable/ic_launcher_foreground"   暂时没测试出来-->
    <!--android:visible="true"-->
</set>
(3) 缩放：scale_animation.xml

<?xml version="1.0" encoding="utf-8"?>
<!-- 缩放-->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="3000">
    <scale
        android:fromXScale="1.0"
        android:fromYScale="1.0"
        android:toXScale="2.0"
        android:toYScale="2.0"
        android:pivotX="100%"
        android:pivotY="100%"/>

    <!--0.0表示收缩到没有；1.0表示正常无伸缩-->
    <!--值小于1.0表示收缩；值大于1.0表示放大-->

    <!--android:fromXScale="1.0"    动画在水平方向X的起始缩放倍数-->
    <!--android:fromYScale="1.0"    动画开始前在竖直方向Y的起始缩放倍数-->
    <!--android:toXScale="2.0"      动画在水平方向X的结束缩放倍数-->
    <!--android:toYScale="2.0"      动画在竖直方向Y的结束缩放倍数-->
    <!--android:pivotX="100%"       缩放轴点的x坐标-->
    <!--android:pivotY="100%"       缩放轴点的y坐标-->
</set>
(4) 透明渐变：alpha_animation.xml

<?xml version="1.0" encoding="utf-8"?>
<!-- 渐变 -->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="3000">
    <alpha
        android:fromAlpha="1"
        android:toAlpha="0"/>

    <!--android:fromAlpha="1.0"     动画开始时视图的透明度(取值范围: -1 ~ 1)-->
    <!--android:toAlpha="0.0"       动画结束时视图的透明度(取值范围: -1 ~ 1)-->

</set>
(5) 组合动画：group_animation.xml

<?xml version="1.0" encoding="utf-8"?>
<!-- 组合动画 -->
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:duration="5000"
    android:startOffset="1000"
    android:fillBefore="true"
    android:fillAfter="false"
    android:repeatMode="restart"
    android:shareInterpolator="true">

    <!-- 旋转 -->
    <rotate
        android:duration="3000"
        android:pivotX="50%"
        android:pivotY="50%"
        android:fromDegrees="0"
        android:toDegrees="360"
        android:repeatMode="restart"
        android:repeatCount="2"/>

    <!-- 平移 -->
    <translate
        android:duration="10000"
        android:fromXDelta="0"
        android:fromYDelta="0"
        android:toXDelta="800"
        android:toYDelta="0"
        android:repeatMode="restart"
        android:repeatCount="3"/>




    <!-- 组合动画独特的属性-->
    <!-- android:shareinterpolator = “true”-->
    <!-- 表示组合动画中的动画是否和集合共享同一个差值器 -->
    <!-- 如果集合不指定插值器，那么子动画需要单独设置 -->

    <!--// 以下参数是4种动画效果的公共属性,即都有的属性-->
    <!--android:duration="3000" // 动画持续时间（ms），必须设置，动画才有效果-->
    <!--android:startOffset ="1000" // 动画延迟开始时间（ms）-->
    <!--android:fillBefore = “true” // 动画播放完后，视图是否会停留在动画开始的状态，默认为true-->
    <!--android:fillAfter = “false” // 动画播放完后，视图是否会停留在动画结束的状态，优先于fillBefore值，默认为false-->
    <!--android:fillEnabled= “true” // 是否应用fillBefore值，对fillAfter值无影响，默认为true-->
    <!--android:repeatMode= “restart” // 选择重复播放动画模式，restart代表正序重放，reverse代表倒序回放，默认为restart|-->
    <!--android:repeatCount = “0” // 重放次数（所以动画的播放次数=重放次数+1），为infinite时无限重复-->
    <!--android:interpolator = @[package:]anim/interpolator_resource // 插值器，即影响动画的播放速度,下面会详细讲-->
</set>
Activity中调用xml
         //平移
        Animation translateAnimator = AnimationUtils.loadAnimation(this, R.anim.translate_animator);
        //伸缩
        Animation scaleAnimator = AnimationUtils.loadAnimation(this, R.anim.scale_animator);
        //旋转
        Animation rotateAnimator = AnimationUtils.loadAnimation(this, R.anim.rotate_animator);
        //渐变
        Animation alphaAnimator = AnimationUtils.loadAnimation(this, R.anim.alpha_animator);
        //组合
        Animation groupAnimator = AnimationUtils.loadAnimation(this, R.anim.group_animator);

        textView.startAnimation(groupAnimator);
```

2. 通过java代码形式
```
        //参数类型 构造函数按照(值的类型， 值)这样的形式，如下：百分比
        int type = Animation.RELATIVE_TO_SELF; //百分比

        //平移
        TranslateAnimation ranslateAnimator = new TranslateAnimation(0, 500, 0, 0);
//        TranslateAnimation ranslateAnimator = new TranslateAnimation(type,0, type, 1, type,0, type, 1);
        ranslateAnimator.setDuration(3000);

        //伸缩
        ScaleAnimation scaleAnimation = new ScaleAnimation(1, 2, 1, 2, type, 0.5f, type, 0.5f);
        scaleAnimation.setDuration(3000);

        //旋转
        RotateAnimation rotateAnimation = new RotateAnimation(0, 360, type, 0.5f, type, 0.5f);
        rotateAnimation.setDuration(3000);

        //渐变
        AlphaAnimation alphaAnimation = new AlphaAnimation(1,0);
        alphaAnimation.setDuration(3000);

        //组合动画
        AnimationSet setAnimation = new AnimationSet(true);
        setAnimation.setDuration(3000);
        setAnimation.addAnimation(rotateAnimation);
        setAnimation.addAnimation(ranslateAnimator);

        textView.startAnimation(setAnimation);
```
总结：
xml优点：动画描述的可读性更好
代码优点：动画效果可动态创建

六. 设置监听
```
监听器 Animation.AnimationListener。

public void setListener(Animation animation){
        animation.setAnimationListener(new Animation.AnimationListener() {
            @Override
            public void onAnimationStart(Animation animation) {
                //开始
                Toast.makeText(mContext, "开始", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAnimationEnd(Animation animation) {
                //结束
                Toast.makeText(mContext, "结束", Toast.LENGTH_SHORT).show();
            }

            @Override
            public void onAnimationRepeat(Animation animation) {
                //动画重复执行的时候回调
                Toast.makeText(mContext, "重复执行", Toast.LENGTH_SHORT).show();
            }
        });

    }
```
七. 应用场景
1. 标准视图动画
2. 视图组（ViewGroup）中子元素的出场效果
3. Activity、Fragment切换视图动画



*android动画 逐帧动画(Frame Animation)*

原理：按序播放一组预先定义好的图片。
将动画拆分为 帧 的形式，且定义每一帧 = 每一张图片

自己玩小技巧：
找到自己需要的gif动画
用 gif分解软件将 gif 分解成一张张图片即可
>优缺点
缺点：效果单一，逐帧播放需要很多图片，占用内存空间较大，容易引发OOM
优点：制作简单

使用方式

1. 通过xml方式

```
res/drawable/animation_list.xml
<?xml version="1.0" encoding="utf-8"?>
<animation-list
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:oneshot="false">

    <item android:drawable="@drawable/icon_redwallet_open1"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open2"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open3"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open4"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open5"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open6"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open7"
        android:duration="100"/>
    <item android:drawable="@drawable/icon_redwallet_open8"
        android:duration="100"/>
</animation-list>
Activity中调用:

  iv.setImageResource(R.drawable.animation_list);
  animationDrawable = (AnimationDrawable) iv.getDrawable();
  animationDrawable.start(); //开始动画
  animationDrawable.stop(); //结束动画
2. java代码方式
  animationDrawable = new AnimationDrawable();
        for (int i = 1; i <= 8; i++) {
            //第一个参数为ID名，第二个为资源属性是ID或者是Drawable，第三个为包名
            int id = getResources().getIdentifier("icon_redwallet_open" + i, "drawable", getPackageName());
            Drawable drawable = getResources().getDrawable(id);
            animationDrawable.addFrame(drawable, 100);
            animationDrawable.setOneShot(false); //false循环   true只播放一次
            iv.setImageDrawable(animationDrawable);
        }
  animationDrawable.start(); //开始动画
  animationDrawable.stop(); //结束动画
  ```
五. 应用场景
较为复杂的个性化动画效果。



* android动画 属性动画(property Animation)*

![image](https://upload-images.jianshu.io/upload_images/13519092-ff691c3719ce06b7.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/1200/format/webp)

一. 属性动画出现的原因

传统动画局限性：

1.  作用对象局限：View
2.  没有改变View的属性，只是改变视觉效果
3.  动画效果单一，可扩展性有较大局限性

为了解决补间动画的缺陷，在 Android 3.0（API 11）开始，系统提供了一种全新的动画模式：属性动画（Property Animation）

二. 作用对象

任意java对象和属性

三. 原理

在一定时间间隔内，通过不断对值进行改变，并不断将该值赋给对象的属性，从而实现该对象在该属性上的动画效果。

可是任意对象，任意属性

 四. 分类

1.  ValueAnimator
2.  ObjectAnimator
3.  ViewPropertyAnimator(动画简写)
4.  AnimatorSet(组合动画)

五. 使用方式

 1\. ValueAnimator

**(1) xml方式**
res/animator/value_animator.xml

```
<?xml version="1.0" encoding="utf-8"?>
<animator xmlns:android="http://schemas.android.com/apk/res/android"
    android:valueFrom="0"
    android:valueTo="500"
    android:duration="3000"
    android:valueType="intType"
    android:repeatCount="3">
</animator>

```

Activity中调用

```
public void setXmlValueAnimator() {
        anim = (ValueAnimator)AnimatorInflater.loadAnimator(this, R.animator.value_animator);
        setListener(anim);
    }

    /**
     * 设置监听
     * @param anim
     */
    public void setListener(ValueAnimator anim){
        anim.addUpdateListener(new ValueAnimator.AnimatorUpdateListener() {
            @Override
            public void onAnimationUpdate(ValueAnimator animation) {
                //获取改变后的值
                int value = (Integer) animation.getAnimatedValue();
                //设置值
                tv.setText(value + "");
                tv.setWidth(value);
                //刷新
                tv.requestLayout();
            }
        });
        anim.start();
    }

```

>注意：android:valueType=" "类型，要和需要赋值的类型一致。
例如：android:valueType="intType" 类型为int类型，tv.setWidth()需要的也是int类型，假如不一致可能会出错。

(2) 通过java代码方式

```
public void setValueAnimator(){
        // ofInt() 作用有两个
        // 1\. 创建动画实例
        // 2\. 将传入的多个Int参数进行平滑过渡:此处传入0和1,表示将值从0平滑过渡到1
        // 如果传入了3个Int参数 a,b,c ,则是先从a平滑过渡到b,再从b平滑过渡到C，以此类推
        anim = ValueAnimator.ofInt(0, 500);
        anim.setDuration(5000); //时间
        setListener(anim); //动画监听 参考上面xml中的setListener()方法
    }

```

 2. ObjectAnimator

(1) 通过xml方式


```
res/animator/object_animator.xml

<?xml version="1.0" encoding="utf-8"?>
<objectAnimator xmlns:android="http://schemas.android.com/apk/res/android"
    android:valueFrom="100"
    android:valueTo="500"
    android:duration="3000"
    android:valueType="intType"
    android:repeatCount="3"
    android:propertyName="width">
</objectAnimator>

```

Activity中调用

```
  ObjectAnimator anim = (ObjectAnimator)AnimatorInflater.loadAnimator(this,   R.animator.object_animator);
  anim.setTarget(textView);
  anim.start();

```

>说明：
android:propertyName="width" 属性说明
被作用的对象，必须要有setWidth()这个属性才可以；
假如初始化中没有赋值，那被作用的对象必须要有getWidth()这个方法。

(2) java代码方式

```
ObjectAnimator anim = ObjectAnimator.ofInt(textView, "width", 0, 500);
        anim.setRepeatCount(100);
        anim.setDuration(3000);
        anim.start();

```

>自动赋值过程：
被作用的对象：textView
被作用的属性：width
执行的时候根据值的变化，自动设置textView.setWidth(变化的值)。
注意：假如初始化中没有赋值，那被作用的对象必须要有getWidth()这个方法。***

 3. ViewPropertyAnimator(动画简写)

用法如下： 链式调用设置属性，详细的参数代表什么意思这里就不写了。

```
 textView.animate()  //获取ViewPropertyAnimator对象
            .setDuration(3000)
            .x(0)
            .y(500);

```

>特点：
1、专门针对View对象动画而操作的类。
2、提供了更简洁的链式调用设置多个属性动画，这些动画可以同时进行的。
3、拥有更好的性能，多个属性动画是一次同时变化，只执行一次UI刷新（也就是只调用一次invalidate,而n个ObjectAnimator就会进行n次属性变化，就有n次invalidate）。
4、每个属性提供两种类型方法设置。
5、该类只能通过View的animate()获取其实例对象的引用

AnimatorSet(组合动画)

1.通过xml方式

```
res/animator/set_animator.xml
<?xml version="1.0" encoding="utf-8"?>
<set xmlns:android="http://schemas.android.com/apk/res/android"
    android:ordering="sequentially">

    <!--android:ordering=""-->
    <!--表示Set集合内的动画按顺序进行-->
    <!--ordering的属性值:sequentially & together-->
    <!--sequentially:表示set中的动画，按照先后顺序逐步进行(a 完成之后进行b)-->
    <!--together:表示set中的动画，在同一时间同时进行,为默认值-->

    <objectAnimator
        android:propertyName="TranslationX"
        android:duration="3000"
        android:valueType="floatType"
        android:valueFrom="100"
        android:valueTo="1000" />

    <objectAnimator
        android:propertyName="width"
        android:duration="3000"
        android:valueType="intType"
        android:valueFrom="0"
        android:valueTo="500"/>
</set>

```

Activity中调用

```
        AnimatorSet animatorSet = (AnimatorSet)AnimatorInflater.loadAnimator(this, R.animator.set_animator);
        animatorSet.setTarget(textView);
        animatorSet.start();

```

(2) java代码方式

```
        //1.设置动画
        ObjectAnimator translation = ObjectAnimator.ofFloat(textView, "translationX", 0,500);//平移动画
        ObjectAnimator rotate = ObjectAnimator.ofFloat(textView, "rotation", 0f, 360f); // 旋转动画
        //2\. 创建组合动画的对象
        AnimatorSet animSet = new AnimatorSet();
        //3\. 按顺序组合动画
        animSet.play(translation).after(rotate).after(translation);
        animSet.setDuration(5000);
        //4.播放动画
        animSet.start();

```

 监听
 ```
**1\. 视图动画监听**
Animation.addListener(AnimationListener)

**2\. 属性动画监听**
Animator.addListener(AnimatorListenerAdapter)

**3\. 属性动画监听值的变化**
Animator.addUpdateListener(ValueAnimator.AnimatorUpdateListener)
```
