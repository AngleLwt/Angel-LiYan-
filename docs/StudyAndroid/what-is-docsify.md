# Android 笔记

## Android修改状态栏颜色全方位教程
在谷歌官方的material设计文档中定义了新的状态栏设计。
[https://material.io/guidelines/layout/structure.html#structure-system-bars](https://link.jianshu.com?t=https%3A%2F%2Fmaterial.io%2Fguidelines%2Flayout%2Fstructure.html%23structure-system-bars)

默认情况下,状态栏的颜色是黑色的。同时状态栏颜色也可以半透明或是指定任意一种颜色。
1.改变颜色后的状态栏

![image](//upload-images.jianshu.io/upload_images/5022380-2f9c63416ead386b.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

![image](//upload-images.jianshu.io/upload_images/5022380-04f743737df89900.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

2.半透明状态栏

![image](//upload-images.jianshu.io/upload_images/5022380-c06f5e24e2e8c5d5.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

3.黑色状态栏

![image](//upload-images.jianshu.io/upload_images/5022380-03b4b8342022338a.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

黑色icon或文字的状态栏

![image](//upload-images.jianshu.io/upload_images/5022380-d8809c0210144872.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

![image](//upload-images.jianshu.io/upload_images/5022380-aee082fe5dc42abd.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

![image](//upload-images.jianshu.io/upload_images/5022380-09d1b99ebfb12811.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

![image](//upload-images.jianshu.io/upload_images/5022380-60055e8ee00cddd3.png?imageMogr2/auto-orient/strip|imageView2/2/w/720/format/webp)

接下来讲一下具体实现

 一.改变状态栏颜色

**4.4-5.0的处理：**
4.4-5.0还没有API可以直接修改状态栏颜色，所以必须先将状态栏设置为透明，然后在布局中添加一个背景为期望色值的View来作为状态栏的填充。


```
static void setStatusBarColor(Activity activity, int statusColor) {
    Window window = activity.getWindow();
    //设置Window为全透明
    window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);

    ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
    //获取父布局
    View mContentChild = mContentView.getChildAt(0);
    //获取状态栏高度
    int statusBarHeight = getStatusBarHeight(activity);

    //如果已经存在假状态栏则移除，防止重复添加
    removeFakeStatusBarViewIfExist(activity);
    //添加一个View来作为状态栏的填充
    addFakeStatusBarView(activity, statusColor, statusBarHeight);
    //设置子控件到状态栏的间距
    addMarginTopToContentChild(mContentChild, statusBarHeight);
    //不预留系统栏位置
    if (mContentChild != null) {
        ViewCompat.setFitsSystemWindows(mContentChild, false);
    }
    //如果在Activity中使用了ActionBar则需要再将布局与状态栏的高度跳高一个ActionBar的高度，否则内容会被ActionBar遮挡
    int action_bar_id = activity.getResources().getIdentifier("action_bar", "id", activity.getPackageName());
    View view = activity.findViewById(action_bar_id);
    if (view != null) {
       TypedValue typedValue = new TypedValue();
        if (activity.getTheme().resolveAttribute(R.attr.actionBarSize, typedValue, true)) {
            int actionBarHeight = TypedValue.complexToDimensionPixelSize(typedValue.data, activity.getResources().getDisplayMetrics());
            setContentTopPadding(activity, actionBarHeight);
        }
    }
}

```


```
private static void removeFakeStatusBarViewIfExist(Activity activity) {
    Window window = activity.getWindow();
    ViewGroup mDecorView = (ViewGroup) window.getDecorView();

    View fakeView = mDecorView.findViewWithTag(TAG_FAKE_STATUS_BAR_VIEW);
    if (fakeView != null) {
        mDecorView.removeView(fakeView);
    }
}

```

```
private static View addFakeStatusBarView(Activity activity, int statusBarColor, int statusBarHeight) {
    Window window = activity.getWindow();
    ViewGroup mDecorView = (ViewGroup) window.getDecorView();

    View mStatusBarView = new View(activity);
    FrameLayout.LayoutParams layoutParams = new FrameLayout.LayoutParams(ViewGroup.LayoutParams.MATCH_PARENT, statusBarHeight);
    layoutParams.gravity = Gravity.TOP;
    mStatusBarView.setLayoutParams(layoutParams);
    mStatusBarView.setBackgroundColor(statusBarColor);
    mStatusBarView.setTag(TAG_FAKE_STATUS_BAR_VIEW);

    mDecorView.addView(mStatusBarView);
    return mStatusBarView;
}

```

```
private static void addMarginTopToContentChild(View mContentChild, int statusBarHeight) {
    if (mContentChild == null) {
        return;
    }
    if (!TAG_MARGIN_ADDED.equals(mContentChild.getTag())) {
        FrameLayout.LayoutParams lp = (FrameLayout.LayoutParams) mContentChild.getLayoutParams();
        lp.topMargin += statusBarHeight;
        mContentChild.setLayoutParams(lp);
        mContentChild.setTag(TAG_MARGIN_ADDED);
    }
}

```

```
static void setContentTopPadding(Activity activity, int padding) {
     ViewGroup mContentView = (ViewGroup) activity.getWindow().findViewById(Window.ID_ANDROID_CONTENT);
     mContentView.setPadding(0, padding, 0, 0);
}

```

**Android5.0以上的处理：**

```
static void setStatusBarColor(Activity activity, int statusColor) {
    Window window = activity.getWindow();
    //取消状态栏透明
    window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
    //添加Flag把状态栏设为可绘制模式
    window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
    //设置状态栏颜色
    window.setStatusBarColor(statusColor);
    //设置系统状态栏处于可见状态
    window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
    //让view不根据系统窗口来调整自己的布局
    ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
    View mChildView = mContentView.getChildAt(0);
    if (mChildView != null) {
        ViewCompat.setFitsSystemWindows(mChildView, false);
        ViewCompat.requestApplyInsets(mChildView);
    }
}

```

 二.透明状态栏

**4.4-5.0的处理：**

```
static void translucentStatusBar(Activity activity) {
    Window window = activity.getWindow();
    //设置Window为透明
    window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);

    ViewGroup mContentView = (ViewGroup) activity.findViewById(Window.ID_ANDROID_CONTENT);
    View mContentChild = mContentView.getChildAt(0);

    //移除已经存在假状态栏则,并且取消它的Margin间距
    removeFakeStatusBarViewIfExist(activity);
    removeMarginTopOfContentChild(mContentChild, getStatusBarHeight(activity));
    if (mContentChild != null) {
        //fitsSystemWindow 为 false, 不预留系统栏位置.
        ViewCompat.setFitsSystemWindows(mContentChild, false);
    }
}

```

**5.0以上的处理：**

```
static void translucentStatusBar(Activity activity, boolean hideStatusBarBackground) {
    Window window = activity.getWindow();
    //添加Flag把状态栏设为可绘制模式
    window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
    if (hideStatusBarBackground) {
        //如果为全透明模式，取消设置Window半透明的Flag
        window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        //设置状态栏为透明
        window.setStatusBarColor(Color.TRANSPARENT);
        //设置window的状态栏不可见
        window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_STABLE | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
    } else {
        //如果为半透明模式，添加设置Window半透明的Flag
        window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
        //设置系统状态栏处于可见状态
        window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
    }
    //view不根据系统窗口来调整自己的布局
    ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
    View mChildView = mContentView.getChildAt(0);
    if (mChildView != null) {
        ViewCompat.setFitsSystemWindows(mChildView, false);
        ViewCompat.requestApplyInsets(mChildView);
    }
}

```

 三.使用CollapsingToolbarLayout使ToolBar具有折叠效果

类似图片中的效果

首先是XML布局：

```
<?xml version="1.0" encoding="utf-8"?>
<android.support.design.widget.CoordinatorLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:fitsSystemWindows="true">

    <android.support.design.widget.AppBarLayout
        android:id="@+id/appbar"
        android:layout_width="match_parent"
        android:layout_height="256dp"
        android:fitsSystemWindows="true"
        android:theme="@style/ThemeOverlay.AppCompat.Dark.ActionBar">

        <android.support.design.widget.CollapsingToolbarLayout
            android:id="@+id/collapsing_layout"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:fitsSystemWindows="true"
            app:contentScrim="@color/colorPrimary"
            app:expandedTitleMarginEnd="64dp"
            app:expandedTitleMarginStart="48dp"
            app:layout_scrollFlags="scroll|exitUntilCollapsed"
            app:statusBarScrim="@color/colorPrimary">

            <ImageView
                android:id="@+id/image"
                android:layout_width="match_parent"
                android:layout_height="match_parent"
                android:fitsSystemWindows="true"
                android:scaleType="centerCrop"
                android:src="@drawable/timg"
                app:layout_collapseMode="parallax" />

            <android.support.v7.widget.Toolbar
                android:id="@+id/toolbar"
                android:layout_width="match_parent"
                android:layout_height="?attr/actionBarSize"
                app:layout_collapseMode="pin"
                app:popupTheme="@style/ThemeOverlay.AppCompat.Light" />
        </android.support.design.widget.CollapsingToolbarLayout>
    </android.support.design.widget.AppBarLayout>

    <android.support.v4.widget.NestedScrollView
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        app:layout_behavior="@string/appbar_scrolling_view_behavior">

        <LinearLayout
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:orientation="vertical"
            android:paddingTop="24dp">

            <android.support.v7.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_margin="16dip">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="240dip"
                    android:text="A"
                    android:textAppearance="@style/TextAppearance.AppCompat.Title" />
            </android.support.v7.widget.CardView>

            <android.support.v7.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="240dip"
                android:layout_margin="16dip">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="B"
                    android:textAppearance="@style/TextAppearance.AppCompat.Title" />

            </android.support.v7.widget.CardView>

            <android.support.v7.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="wrap_content"
                android:layout_margin="16dip">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="240dip"
                    android:text="C"
                    android:textAppearance="@style/TextAppearance.AppCompat.Title" />
            </android.support.v7.widget.CardView>

            <android.support.v7.widget.CardView
                android:layout_width="match_parent"
                android:layout_height="240dip"
                android:layout_margin="16dip">

                <TextView
                    android:layout_width="match_parent"
                    android:layout_height="wrap_content"
                    android:text="D"
                    android:textAppearance="@style/TextAppearance.AppCompat.Title" />

            </android.support.v7.widget.CardView>
        </LinearLayout>
    </android.support.v4.widget.NestedScrollView>
</android.support.design.widget.CoordinatorLayout>

```

**4.4-5.0的处理：**

```
static void setStatusBarColorForCollapsingToolbar(Activity activity, final AppBarLayout appBarLayout, final CollapsingToolbarLayout collapsingToolbarLayout,
                                                  Toolbar toolbar, int statusColor) {
    Window window = activity.getWindow();
    //设置Window为全透明
    window.addFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
    ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);

    //AppBarLayout,CollapsingToolbarLayout,ToolBar,ImageView的fitsSystemWindow统一改为false, 不预留系统栏位置.
    View mContentChild = mContentView.getChildAt(0);
    mContentChild.setFitsSystemWindows(false);
    ((View) appBarLayout.getParent()).setFitsSystemWindows(false);
    appBarLayout.setFitsSystemWindows(false);
    collapsingToolbarLayout.setFitsSystemWindows(false);
    collapsingToolbarLayout.getChildAt(0).setFitsSystemWindows(false);

    toolbar.setFitsSystemWindows(false);
    //为Toolbar添加一个状态栏的高度, 同时为Toolbar添加paddingTop,使Toolbar覆盖状态栏，ToolBar的title可以正常显示.
    if (toolbar.getTag() == null) {
        CollapsingToolbarLayout.LayoutParams lp = (CollapsingToolbarLayout.LayoutParams) toolbar.getLayoutParams();
        int statusBarHeight = getStatusBarHeight(activity);
        lp.height += statusBarHeight;
        toolbar.setLayoutParams(lp);
        toolbar.setPadding(toolbar.getPaddingLeft(), toolbar.getPaddingTop() + statusBarHeight, toolbar.getPaddingRight(), toolbar.getPaddingBottom());
        toolbar.setTag(true);
    }
    //移除已经存在假状态栏则,并且取消它的Margin间距
    int statusBarHeight = getStatusBarHeight(activity);
    removeFakeStatusBarViewIfExist(activity);
    removeMarginTopOfContentChild(mContentChild, statusBarHeight);
    //添加一个View来作为状态栏的填充
    final View statusView = addFakeStatusBarView(activity, statusColor, statusBarHeight);

    CoordinatorLayout.Behavior behavior = ((CoordinatorLayout.LayoutParams) appBarLayout.getLayoutParams()).getBehavior();
    if (behavior != null && behavior instanceof AppBarLayout.Behavior) {
        int verticalOffset = ((AppBarLayout.Behavior) behavior).getTopAndBottomOffset();
        if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
            statusView.setAlpha(1f);
        } else {
            statusView.setAlpha(0f);
        }
    } else {
        statusView.setAlpha(0f);
    }
    appBarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
        @Override
        public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
            if (Math.abs(verticalOffset) > appBarLayout.getHeight() - collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
                //toolbar被折叠时显示状态栏
                if (statusView.getAlpha() == 0) {
                    statusView.animate().cancel();
                    statusView.animate().alpha(1f).setDuration(collapsingToolbarLayout.getScrimAnimationDuration()).start();
                }
            } else {
                //toolbar展开时显示状态栏
                if (statusView.getAlpha() == 1) {
                    statusView.animate().cancel();
                    statusView.animate().alpha(0f).setDuration(collapsingToolbarLayout.getScrimAnimationDuration()).start();
                }
            }
        }
    });
}

```

**5.0以上的处理：**

```
static void setStatusBarColorForCollapsingToolbar(final Activity activity, final AppBarLayout appBarLayout, final CollapsingToolbarLayout collapsingToolbarLayout,
                                                  Toolbar toolbar, final int statusColor) {
    final Window window = activity.getWindow();
    //取消设置Window半透明的Flag
    window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
    ////添加Flag把状态栏设为可绘制模式
    window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
    //设置状态栏为透明
    window.setStatusBarColor(Color.TRANSPARENT);
    //设置系统状态栏处于可见状态
    window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_VISIBLE);
    //通过OnApplyWindowInsetsListener()使Layout在绘制过程中将View向下偏移了,使collapsingToolbarLayout可以占据状态栏
    ViewCompat.setOnApplyWindowInsetsListener(collapsingToolbarLayout, new OnApplyWindowInsetsListener() {
        @Override
        public WindowInsetsCompat onApplyWindowInsets(View v, WindowInsetsCompat insets) {
            return insets;
        }
    });

    ViewGroup mContentView = (ViewGroup) window.findViewById(Window.ID_ANDROID_CONTENT);
    View mChildView = mContentView.getChildAt(0);
    //view不根据系统窗口来调整自己的布局
    if (mChildView != null) {
        ViewCompat.setFitsSystemWindows(mChildView, false);
        ViewCompat.requestApplyInsets(mChildView);
    }

    ((View) appBarLayout.getParent()).setFitsSystemWindows(false);
    appBarLayout.setFitsSystemWindows(false);
    collapsingToolbarLayout.setFitsSystemWindows(false);
    collapsingToolbarLayout.getChildAt(0).setFitsSystemWindows(false);
    //设置状态栏的颜色
    collapsingToolbarLayout.setStatusBarScrimColor(statusColor);
    toolbar.setFitsSystemWindows(false);
    //为Toolbar添加一个状态栏的高度, 同时为Toolbar添加paddingTop,使Toolbar覆盖状态栏，ToolBar的title可以正常显示.
    if (toolbar.getTag() == null) {
        CollapsingToolbarLayout.LayoutParams lp = (CollapsingToolbarLayout.LayoutParams) toolbar.getLayoutParams();
        int statusBarHeight = getStatusBarHeight(activity);
        lp.height += statusBarHeight;
        toolbar.setLayoutParams(lp);
        toolbar.setPadding(toolbar.getPaddingLeft(), toolbar.getPaddingTop() + statusBarHeight, toolbar.getPaddingRight(), toolbar.getPaddingBottom());
        toolbar.setTag(true);
    }

    appBarLayout.addOnOffsetChangedListener(new AppBarLayout.OnOffsetChangedListener() {
        private final static int EXPANDED = 0;
        private final static int COLLAPSED = 1;
        private int appBarLayoutState;

        @Override
        public void onOffsetChanged(AppBarLayout appBarLayout, int verticalOffset) {
            //toolbar被折叠时显示状态栏
            if (Math.abs(verticalOffset) > collapsingToolbarLayout.getScrimVisibleHeightTrigger()) {
                if (appBarLayoutState != COLLAPSED) {
                    appBarLayoutState = COLLAPSED;//修改状态标记为折叠
                    setStatusBarColor(activity, statusColor);
                }
            } else {
                //toolbar显示时同时显示状态栏
                if (appBarLayoutState != EXPANDED) {
                    appBarLayoutState = EXPANDED;//修改状态标记为展开
                    translucentStatusBar(activity, true);
                }
            }
        }
    });
}

```

 四.更改状态栏字体颜色

![image](//upload-images.jianshu.io/upload_images/5022380-76d14a33682dcdaa.jpg?imageMogr2/auto-orient/strip|imageView2/2/w/240/format/webp)

在Android 6.0的Api中提供了SYSTEM_UI_FLAG_LIGHT_STATUS_BAR这么一个常量，可以使状态栏文字设置为黑色，但对6.0以下是不起作用的。
小米和魅族的手机也可以达到这个效果，要做一些特殊的处理。可以参考小米和魅族的开发者文档。
[Flyme沉浸式状态栏](https://link.jianshu.com?t=http%3A%2F%2Fopen-wiki.flyme.cn%2Findex.php%3Ftitle%3DFlyme%25E7%25B3%25BB%25E7%25BB%259FAPI%23.E4.B8.80.E3.80.81.E6.B2.89.E6.B5.B8.E5.BC.8F.E7.8A.B6.E6.80.81.E6.A0.8F)
[MIUI 6 沉浸式状态栏调用方法](https://link.jianshu.com?t=http%3A%2F%2Fdev.xiaomi.com%2Fdoc%2Fp%3D4769%2F)
[MIUI 9「状态栏黑色字符」实现方法变更通知](https://link.jianshu.com?t=https%3A%2F%2Fdev.mi.com%2Fconsole%2Fdoc%2Fdetail%3FpId%3D1159)

```
public static void setStatusBarLightMode(Activity activity, int color) {
    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {
        //判断是否为小米或魅族手机，如果是则将状态栏文字改为黑色
        if (MIUISetStatusBarLightMode(activity, true) || FlymeSetStatusBarLightMode(activity, true)) {
            //设置状态栏为指定颜色
            if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {//5.0
                activity.getWindow().setStatusBarColor(color);
            } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.KITKAT) {//4.4
                //调用修改状态栏颜色的方法
                setStatusBarColor(activity, color);
            }
        } else if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.M) {
            //如果是6.0以上将状态栏文字改为黑色，并设置状态栏颜色
            activity.getWindow().setBackgroundDrawableResource(android.R.color.transparent);
            activity.getWindow().getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN | View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
            activity.getWindow().setStatusBarColor(color);

            //fitsSystemWindow 为 false, 不预留系统栏位置.
            ViewGroup mContentView = (ViewGroup) activity.getWindow().findViewById(Window.ID_ANDROID_CONTENT);
            View mChildView = mContentView.getChildAt(0);
            if (mChildView != null) {
                ViewCompat.setFitsSystemWindows(mChildView, true);
                ViewCompat.requestApplyInsets(mChildView);
            }
        } 
    }
}

```

小米MiUi修改状态栏方法

```
static boolean MIUISetStatusBarLightMode(Activity activity, boolean darkmode) {
    boolean result = false;
    Class<? extends Window> clazz = activity.getWindow().getClass();
    try {
        int darkModeFlag = 0;
        Class<?> layoutParams = Class.forName("android.view.MiuiWindowManager$LayoutParams");
        Field field = layoutParams.getField("EXTRA_FLAG_STATUS_BAR_DARK_MODE");
        darkModeFlag = field.getInt(layoutParams);
        Method extraFlagField = clazz.getMethod("setExtraFlags", int.class, int.class);
        extraFlagField.invoke(activity.getWindow(), darkmode ? darkModeFlag : 0, darkModeFlag);
        result = true;
    } catch (Exception e) {
        e.printStackTrace();
    }
    return result;
}

```

如果是MIUI9的系统还需要加上这段代码

```
Window window = getWindow();
window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);

```

Flyme修改状态栏方法

```
static boolean FlymeSetStatusBarLightMode(Activity activity, boolean darkmode) {
    boolean result = false;
    try {
        WindowManager.LayoutParams lp = activity.getWindow().getAttributes();
        Field darkFlag = WindowManager.LayoutParams.class
                .getDeclaredField("MEIZU_FLAG_DARK_STATUS_BAR_ICON");
        Field meizuFlags = WindowManager.LayoutParams.class
                .getDeclaredField("meizuFlags");
        darkFlag.setAccessible(true);
        meizuFlags.setAccessible(true);
        int bit = darkFlag.getInt(null);
        int value = meizuFlags.getInt(lp);
        if (darkmode) {
            value |= bit;
        } else {
            value &= ~bit;
        }
        meizuFlags.setInt(lp, value);
        activity.getWindow().setAttributes(lp);
        result = true;
    } catch (Exception e) {
        e.printStackTrace();
    }
    return result;
}

```

 结语：

更详细的参考[Demo](https://link.jianshu.com?t=https%3A%2F%2Fgithub.com%2Fimflyn%2FEyes)在github中

## 刷新样式
飞行气球：DeliveryHeader

掉落盒子：DropBoxHeader

全屏水滴：WaveSwipeHeader

官方下拉Meterial：MaterialHeader

颜色闪烁渐变头：StoreHouseHeader

战争城市游戏头：FunGameBattleCityHeader

打砖块游戏：FunGameHitBlockHeader

弹出圆圈加载：BezierCircleHeader

冲上云霄：TaurusHeader

金色校园：PhoenixHeader
怎样使用上边的这个特殊刷新头

列举一二
```

refreshLayout.setRefreshHeader(new WaveSwipeHeader( App.context ) ); //水滴刷新

refreshLayout.setRefreshHeader(new FunGameHitBlockHeader( App.context ) ); //打砖块
App.context   为全局上下文

refreshLayout.setRefreshFooter(new BallPulseFooter(App.context).setSpinnerStyle( SpinnerStyle.Scale));

```
## Android沉浸式状态栏的实现
在MainActivity中的onCreate方法
```
requestWindowFeature(Window.FEATURE_NO_TITLE);
        Window window = getWindow();
        window.getDecorView().setSystemUiVisibility(
                View.SYSTEM_UI_FLAG_LAYOUT_STABLE | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.LOLLIPOP) {
            window.clearFlags(WindowManager.LayoutParams.FLAG_TRANSLUCENT_STATUS);
            window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN | View.SYSTEM_UI_FLAG_LAYOUT_STABLE);
            window.addFlags(WindowManager.LayoutParams.FLAG_DRAWS_SYSTEM_BAR_BACKGROUNDS);
            window.setStatusBarColor(Color.parseColor("#00000000"));
            window.getDecorView().setSystemUiVisibility(View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN | View.SYSTEM_UI_FLAG_LIGHT_STATUS_BAR);
        }

        ViewGroup contentFrameLayout = (ViewGroup) findViewById(Window.ID_ANDROID_CONTENT);
        View parentView = contentFrameLayout.getChildAt(0);
        if (parentView != null && Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            parentView.setFitsSystemWindows(true);
        }
     
```
## TableLayout实现选择器效果
First
首先我们需要导入以下依赖

```
implementation 'com.android.support:design:28.0.0'
```


一般使用时我们会和Viewpager一起使用，看一下xml里面的内容：
```
<android.support.v4.view.ViewPager
    android:id="@+id/vp"
    android:layout_weight="9"
    android:layout_height="0dp"
    android:layout_width="match_parent">
</android.support.v4.view.ViewPager>

<android.support.design.widget.TabLayout
       android:id="@+id/tb"
       android:layout_weight="1"
       android:layout_height="0dp"
       android:layout_width="match_parent"
//初始化tablelayout文字颜色
   app:tabTextColor="@android:color/black" 
 //点击tablelayout文字颜色   
   app:tabSelectedTextColor="@android:color/holo_red_light">
 </android.support.design.widget.TabLayout>
```
然后就在我们的Activity中使用就好啦，初始化什么的就不说了都懂主要看一下具体使用我把她们放在数组里啦 啦啦啦
```
final String[] name ={"首页","分类","发现","购物车","我的"};
final int[] pic = {R.drawable.sy,R.drawable.fenlei,R.drawable.faxian,R.drawable.gouwuche,R.drawable.wode};
final int[] pic2 = {R.drawable.sy2,R.drawable.fenlei2,R.drawable.faxian2,R.drawable.gouwuche2,R.drawable.wode2};
```
然后最最重要的一步啦，就是我们要让Tablayout 绑定Viewpager

tb.setupWithViewPager(vp);
然后呢 我需要实现点击每一个Tablayout可以变颜色啦，那么就需要使用一个监听事件

```
tb.addOnTabSelectedListener(new TabLayout.OnTabSelectedListener() {
    @Override
//禁止table layout效果
    public void onTabSelected(TabLayout.Tab tab) {
        tb.getTabAt(tab.getPosition()).setIcon(pic2[tab.getPosition()]);
    }
//点击table layout效果
    @Override
    public void onTabUnselected(TabLayout.Tab tab) {
        tb.getTabAt(tab.getPosition()).setIcon(pic[tab.getPosition()]);
    }
    @Override
    public void onTabReselected(TabLayout.Tab tab) {

    }
});
```

Second

在drawer文件夹中
```
<selector xmlns:android="http://schemas.android.com/apk/res/android">

    <item android:color="@color/theme" android:state_checked="true" />
    <item android:color="@color/normal_color" android:state_checked="false" />
</selector>
```
在meun文件夹中
```
<menu xmlns:android="http://schemas.android.com/apk/res/android">
    <item
        android:id="@+id/menu_home"
        android:checked="true"
        android:icon="@mipmap/home"
        android:title="@string/tab_1" />
    <item
        android:id="@+id/menu_project"
        android:icon="@mipmap/project"
        android:title="@string/tab_2" />
    <item
        android:id="@+id/menu_square"
        android:icon="@mipmap/square"
        android:title="@string/tab_3" />
    <item
        android:id="@+id/menu_official_account"
        android:icon="@mipmap/official_account"
        android:title="@string/tab_4" />
    <item
        android:id="@+id/menu_mine"
        android:icon="@mipmap/mine"
        android:title="@string/tab_5" />
</menu>
```
## Progressbar的用法

效果图
![screenshot_20200326_120536.png](https://upload-images.jianshu.io/upload_images/21988850-39327c0dc376546a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![screenshot_20200326_120519.png](https://upload-images.jianshu.io/upload_images/21988850-cfe8558a40c10429.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


```
                new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    Thread.sleep(3000);
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
                getActivity().runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        for (int i = 0; i < 30; i++) {
//                            集合添加数据
                            picBeans.add(new PicBean("asd", "https://www.wanandroid.com/blogimgs/9fc6e10c-b3e8-46bb-928b-05ccd2147335.png"));
//                           数据库添加数据
                            hpSql.insertPic(new PicBean("asd", "https://www.wanandroid.com/blogimgs/9fc6e10c-b3e8-46bb-928b-05ccd2147335.png"));
                        }
                        recyeleAdapter.notifyDataSetChanged();
//                        数据库查询数据
                        ArrayList<PicBean> picBeans = hpSql.SelectPic();
                        Log.d("picBeans",picBeans.toString());
                        Log.d("Sizes", HomeFragment.this.picBeans.size() + "");
//                        Progress的显示与隐藏
                        if (HomeFragment.this.picBeans.size() < 30) {
//                            显示
                            progressBar.setVisibility(View.VISIBLE);
                        } else {
                            隐藏
                            progressBar.setVisibility(View.GONE);
                        }

                    }
                });
            }
        }).start();

```
## 导航页

1.fragment导航

MainActivity页面
```
{

    private ViewPager vp;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
{
        vp = (ViewPager) findViewById(R.id.vp);
//        -------------第一种方法（view）--------
//        ArrayList<String> list = new ArrayList<>();
//        for (int i = 0; i < 4; i++) {
//            list.add(i+"");
//
//        }
//        VPAdapter vpAdapter = new VPAdapter(list, this);
//        vp.setAdapter(vpAdapter);

//        -----------------第二种方法（fragment）--------------
        ArrayList<Fragment> fragments = new ArrayList<>();
        for (int i = 0; i < 4; i++) {
//            创建fragment的对象
            NavigationFragment navigationFragment = new NavigationFragment();
//            创建bundle对象
            Bundle bundle = new Bundle();
//            设置keyzhi和value值
            bundle.putString("a", i + "");
//            设置值
            navigationFragment.setArguments(bundle);
//           添加到集合
            fragments.add(navigationFragment);
        }
        VPFragmentAdapter vpFragmentAdapter = new VPFragmentAdapter(getSupportFragmentManager(), fragments);
        vp.setAdapter(vpFragmentAdapter);

    }
```
Fragment
```
{
    private TextView tv;

    @Nullable
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container, @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.fragment_vp, container, false);
        initView(view);
        return view;
    }

    private void initView(View view) {
        tv = view.findViewById(R.id.tv);

        Bundle bundle = getArguments();
        String key = bundle.getString("key");
        tv.setText(key);
    }
}
```
适配器
```
public class VpFragmentAdapter extends FragmentStatePagerAdapter {

    private ArrayList<Fragment> fragments;

    public VpFragmentAdapter(@NonNull FragmentManager fm, ArrayList<Fragment> fragments) {
        super(fm);
        this.fragments = fragments;
    }

    @NonNull
    @Override
    public Fragment getItem(int position) {
        return fragments.get(position);
    }

    @Override
    public int getCount() {
        return fragments.size();
    }
}

```

2.View导航

MainActiity页面
```

/**
 * viewpager结合view实现导航：
 *
 * 1.ViewPager布局
 * 2.适配器(*****)
 * 3.设置适配器
 */
public class MainActivity extends AppCompatActivity {

    private ViewPager vp;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
        vp = (ViewPager) findViewById(R.id.vp);

        ArrayList<String> list = new ArrayList<>();
        for (int i = 0; i < 3; i++) {
            list.add("第" + i + "页面");
        }

        vp.setAdapter(new VpAdapter(this, list));
    }
}
```
适配器
```
public class VpViewAdapter extends PagerAdapter {

    private Context context;
    private ArrayList<String> list;

    public VpViewAdapter(Context context, ArrayList<String> list) {
        this.context = context;
        this.list = list;
    }

    @Override
    public int getCount() {
        return list.size();
    }

    @Override
    public boolean isViewFromObject(@NonNull View view, @NonNull Object object) {
        return view == object;
    }

    @NonNull
    @Override
    public Object instantiateItem(@NonNull ViewGroup container, int position) {

        View view = LayoutInflater.from(context).inflate(R.layout.view_layout, null);
        TextView tv = view.findViewById(R.id.tv);
        tv.setText(list.get(position));
        container.addView(view);
        return view;
    }

    @Override
    public void destroyItem(@NonNull ViewGroup container, int position, @NonNull Object object) {
        container.removeView((View) object);
    }
}

```
## 动画

## Fragment用法
```
    //1.获得碎片管理器

        FragmentManager manager = getFragmentManager();

        //2.开始事务

        FragmentTransaction tr = manager.beginTransaction();

        //3.构建Fragment对象

        SoundFragment fragment = new SoundFragment();

        
        //4.替换容器中内容 参数1 右边容器的id,参数2 fragment对象

        tr.replace(R.id.container, fragment);

        //5.提交事务

        tr.commit();
```
FragmentTransaction的用法
```
主要的操作都是FragmentTransaction的方法
FragmentTransaction transaction = fm.benginTransatcion();//开启一个事务
transaction.add() 
往Activity中添加一个Fragment
transaction.remove() 
从Activity中移除一个Fragment，如果被移除的Fragment没有添加到回退栈（回退栈后面会详细说），这个Fragment实例将会被销毁。
transaction.replace()
使用另一个Fragment替换当前的，实际上就是remove()然后add()的合体~
transaction.hide()
隐藏当前的Fragment，仅仅是设为不可见，并不会销毁
transaction.show()
显示之前隐藏的Fragment
detach()
会将view从UI中移除,和remove()不同,此时fragment的状态依然由FragmentManager维护。
attach()
重建view视图，附加到UI上并显示。
transatcion.commit()//提交一个事务

```
Fragment生命周期
![image.png](https://upload-images.jianshu.io/upload_images/21988850-069a8394fe1aad43.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

Fragment调用Activity或传递参数
    方法1:调用Fragment的getActivity（）方法即可返回它所在的Activity实例,之后就可调用Activity中的方法或者成员变量
```
 MainActivity activity = (MainActivity) getActivity();
        activity.show("");
```
*`tablayout+fragment翻页/Fragment显示隐藏`
tablayout+fragment翻页
主页面
```
{
    private ViewPager vp;
    private TabLayout tab;
//测试网络接口
    private String tabUrl = "https://www.wanandroid.com/project/tree/json";
    private Gson gson;
//构造全局集合
    private ArrayList<Fragment> fragments;
    private ArrayList<String> titles;

 
    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.projection_fragment, container, false);
//       创建gson对象
        gson = new GsonBuilder().create();
        initView(view);
        initData();

        return view;
    }

    private void initView(View view) {
        vp = view.findViewById(R.id.vp);
        tab = view.findViewById(R.id.tab);
//        创建空集合
        fragments = new ArrayList<>();
        titles = new ArrayList<>();

    }

    private void initData() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                String tabGson = getRespaose(tabUrl);
                final TabBran tabBran = gson.fromJson(tabGson, TabBran.class);
                getActivity().runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
//                        将获取集合便利添加title和创建title对应的fragment
                        for (int i = 0; i < tabBran.getData().size(); i++) {
                            titles.add(tabBran.getData().get(i).getName());
                            fragments.add(new PublicFragment(tabBran.getData().get(i).getId()));
                        }
//                        创建适配器
                        VP_Adapter vp_adapter = new VP_Adapter(getChildFragmentManager(), fragments, titles);
//                       绑定适配器
                        vp.setAdapter(vp_adapter);
//                        tablayout绑定ViewPager
                        tab.setupWithViewPager(vp);


                    }
                });

            }
//           从网络获取字符串
            private String getRespaose(String urls) {

                try {
                    URL url = new URL(urls);
                    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                    int responseCode = connection.getResponseCode();
                    if (responseCode == HttpURLConnection.HTTP_OK) {
                        byte[] bytes = new byte[1024];
                        int length = 0;
                        StringBuffer buffer = new StringBuffer();
                        InputStream inputStream = connection.getInputStream();
                        while ((length = inputStream.read(bytes)) != -1) {
                            buffer.append(new String(bytes, 0, length));
                        }
                        return buffer.toString();
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return null;
            }
        }).start();

    }


}
```
.
Fragment页面
```

public class PublicFragment extends Fragment {


    private RecyclerView recycle;
    private SmartRefreshLayout srl;
    private int id;
    private String publicUrl="https://www.wanandroid.com/project/list/";
    private int page=1;
    private Gson gson;
    private ArrayList<PublicBean.DataBean.DatasBean> datasBeans;
    private PublicAdapter publicAdapter;
// 设置有参ID创建fragment翻页

   public PublicFragment(int id) {
        this.id = id;

    }

    public PublicFragment() {
    }

    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.public_fragment, container, false);
        gson = new GsonBuilder().create();
        initView(view);
        initData();
        return view;
    }

    private void initView(View view) {
        recycle = view.findViewById(R.id.recycle);
        srl = view.findViewById(R.id.srl);
//        创建空集合
        datasBeans = new ArrayList<>();
//        创建适配器
        publicAdapter = new PublicAdapter(datasBeans, getActivity());
//        创建管理器
        recycle.setLayoutManager(new LinearLayoutManager(getActivity()));
//        创建分割线
        recycle.addItemDecoration(new DividerItemDecoration(getActivity(), OrientationHelper.VERTICAL));
//       设置适配器
        recycle.setAdapter(publicAdapter);
//         刷新
        srl.setOnRefreshLoadMoreListener(new OnRefreshLoadMoreListener() {
            @Override
            public void onLoadMore(@NonNull RefreshLayout refreshLayout) {
//                页数+1
                page++;
//                加载数据
                initData();
            }

            @Override
            public void onRefresh(@NonNull RefreshLayout refreshLayout) {
//                当page=1
                page=1;
//                当集合不为空
                if (datasBeans!=null&&datasBeans.size()>0){
//                    清空集合
                    datasBeans.clear();
//                    加载数据
                    initData();
                }
            }
        });

    }

    private void initData() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                String publicGson = getRespaose(publicUrl);
                Log.d("TAG",publicGson);
                final PublicBean publicBean = gson.fromJson(publicGson, PublicBean.class);
                getActivity().runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
//                        将获得数据添加到空集合
                        datasBeans.addAll(publicBean.getData().getDatas());
                        publicAdapter.notifyDataSetChanged();
//                     完成刷行和更新
                        srl.finishLoadMore();
                        srl.finishRefresh();

                    }
                });

            }
//           从网络获取字符串
            private String getRespaose(String urls) {

                try {
                    URL url = new URL(urls+page+"/json?cid="+id);
                    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                    int responseCode = connection.getResponseCode();
                    if (responseCode == HttpURLConnection.HTTP_OK) {
                        byte[] bytes = new byte[1024];
                        int length = 0;
                        StringBuffer buffer = new StringBuffer();
                        InputStream inputStream = connection.getInputStream();
                        while ((length = inputStream.read(bytes)) != -1) {
                            buffer.append(new String(bytes, 0, length));
                        }
                        return buffer.toString();
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return null;
            }
        }).start();

    }


}

```
Fragment显示隐藏

```

public class MainActivity extends AppCompatActivity {

    private FrameLayout framelayout;
    private TabLayout tablelayout;


    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
        framelayout = (FrameLayout) findViewById(R.id.framelayout);
        tablelayout = (TabLayout) findViewById(R.id.tablelayout);
        tablelayout.addTab(tablelayout.newTab().setText("A"));
        tablelayout.addTab(tablelayout.newTab().setText("B"));

        final FragmentManager fm = getSupportFragmentManager();
        final FragmentA fragmentA = new FragmentA();
        final FragmentB fragmentB = new FragmentB();
        fm.beginTransaction()
                .add(R.id.framelayout, fragmentA)
                .add(R.id.framelayout, fragmentB)
                .hide(fragmentB)
                .commit();

        tablelayout.addOnTabSelectedListener(new TabLayout.BaseOnTabSelectedListener() {
            @Override
            public void onTabSelected(TabLayout.Tab tab) {
                switch (tab.getPosition()){
                    case 0:
                        fm.beginTransaction().show(fragmentA).hide(fragmentB).commit();
                        break;
                    case 1:
                        fm.beginTransaction().show(fragmentB).hide(fragmentA).commit();
                        break;
                }
            }

            @Override
            public void onTabUnselected(TabLayout.Tab tab) {

            }

            @Override
            public void onTabReselected(TabLayout.Tab tab) {

            }
        });
    }
}

```

## PoPwindow.Notification

```

{

    private Button bt_PoPwindow;
    private Button bt_Notification;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
    }

    private void initView() {
        bt_PoPwindow = (Button) findViewById(R.id.bt_PoPwindow);
        bt_PoPwindow.setOnClickListener(this);
        bt_Notification = (Button) findViewById(R.id.bt_Notification);
        bt_Notification.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.bt_PoPwindow:
//                弹窗
                popwindow();
                break;
            case R.id.bt_Notification:
//                通知
                notification();
                break;
        }
    }

    private void notification() {
//       创建通知管理器
        NotificationManager manager = (NotificationManager) getSystemService(Context.NOTIFICATION_SERVICE);
        String channelId = "1";
        String channelName = "default";

        if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) {
            NotificationChannel channel = new NotificationChannel(channelId, channelName, NotificationManager.IMPORTANCE_DEFAULT);
            channel.enableLights(true);//开启指示灯,如果设备有的话。
            channel.setLightColor(Color.RED);//设置指示灯颜色
            channel.setShowBadge(true);//检测是否显示角标
            manager.createNotificationChannel(channel);
        }
//        设置传值
        Intent intent = new Intent(this, MainActivity.class);
//      设置padingintent
        PendingIntent activity = PendingIntent.getActivity(this, 100, intent, PendingIntent.FLAG_UPDATE_CURRENT);

        Notification build = new NotificationCompat.Builder(this, channelId)
                .setSmallIcon(R.drawable.gogle)//设置小图
//                .setLargeIcon(BitmapFactory.decodeResource(getResources(), R.drawable.gogle))//设置大
                .setContentText("我是内容")//设置内容
                .setContentTitle("我是标题")//设置标题
                .setContentIntent(activity)//设置延时意图
                .setAutoCancel(true)//设置点击自动消失
                .setNumber(1)//设置角标计数
                .build();
//        发送通知
        manager.notify(100, build);


    }

    //  创建方法
    private void popwindow() {
        View view = LayoutInflater.from(MainActivity.this).inflate(R.layout.pop_one, null);
//        设置大小
        PopupWindow popupWindow = new PopupWindow(view, ViewGroup.LayoutParams.WRAP_CONTENT, ViewGroup.LayoutParams.WRAP_CONTENT);
//        点击范围外关闭pop——window
        popupWindow.setBackgroundDrawable(new ColorDrawable());
        popupWindow.setOutsideTouchable(true);
//       设置透明度
        setBackgroundAlpha(0.5f);
//     设置动画
        popupWindow.setAnimationStyle(R.style.PopAnimation);
//        聚焦
        popupWindow.setFocusable(true);
//        展示位置
        popupWindow.showAsDropDown(bt_PoPwindow, 20, 20, Gravity.CENTER_HORIZONTAL);
        popupWindow.setOnDismissListener(new PopupWindow.OnDismissListener() {
            @Override
            public void onDismiss() {
                setBackgroundAlpha(1);
            }
        });
    }

    private void setBackgroundAlpha(float alpha) {
//        获得窗口
        Window window = getWindow();
//        获得窗口属性
        WindowManager.LayoutParams attributes = window.getAttributes();
        attributes.alpha = alpha;
        window.setAttributes(attributes);
    }
}

```

## 音乐播放器

主页面
```
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

    private Button btn_sdcard;
    private Button btn_net;
    private Button btn_raw;
    private Button btn_assets;
    private Button btn_pause;
    private Button btn_stop;
    private MediaPlayer mp;
    private String path = Environment.getExternalStorageDirectory() + "/Pictures/青花瓷-周杰伦.mp3";
    private String netPath = "http://music.163.com/song/media/outer/url?id=139894.mp3";
    private Button btn_goon;
    private SeekBar seekbar_audio;
    private RecyclerView rv;
    private Button btn_pre;
    private Button btn_next;
    private int position;
    private ArrayList<AudioBean> list;
    private Button btn_go_service;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();

        initListener();
    }

    private void initListener() {
        seekbar_audio.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            /**
             * 进度发生改变回调
             * @param seekBar
             * @param progress
             * @param fromUser
             */
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                Log.e("TAG", "MainActivity onProgressChanged()");
            }

            /**
             * 开始拖动seekbar
             * @param seekBar
             */
            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                Log.e("TAG", "MainActivity onStartTrackingTouch()");
            }

            /**
             * 停止拖动seekbar
             * @param seekBar
             */
            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                Log.e("TAG", "MainActivity onStopTrackingTouch()");
                int progress = seekBar.getProgress();
                mp.seekTo(progress);
            }
        });
    }
//   刷新适配器
    private void updataSeekbar() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (mp.isPlaying()) {
                    try {
                        Thread.sleep(1000);
                        seekbar_audio.setMax(mp.getDuration());
                        seekbar_audio.setProgress(mp.getCurrentPosition());
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }

            }
        }).start();
    }

    private void initView() {
        btn_sdcard = (Button) findViewById(R.id.btn_sdcard);
        btn_net = (Button) findViewById(R.id.btn_net);
        btn_raw = (Button) findViewById(R.id.btn_raw);
        btn_assets = (Button) findViewById(R.id.btn_assets);
        btn_pause = (Button) findViewById(R.id.btn_pause);
        btn_stop = (Button) findViewById(R.id.btn_stop);

        btn_sdcard.setOnClickListener(this);
        btn_net.setOnClickListener(this);
        btn_raw.setOnClickListener(this);
        btn_assets.setOnClickListener(this);
        btn_pause.setOnClickListener(this);
        btn_stop.setOnClickListener(this);
//       获取音乐播放器
        mp = new MediaPlayer();

        btn_goon = (Button) findViewById(R.id.btn_goon);
        btn_goon.setOnClickListener(this);
        seekbar_audio = (SeekBar) findViewById(R.id.seekbar_audio);
        rv = (RecyclerView) findViewById(R.id.rv);

        rv.setLayoutManager(new LinearLayoutManager(this));
        list = new ArrayList<>();
//     获取资源
        Cursor query = getContentResolver().query(MediaStore.Audio.Media.EXTERNAL_CONTENT_URI, null, null, null, null);
        while (query.moveToNext()) {
            String name = query.getString(query.getColumnIndex(MediaStore.Audio.Media.DISPLAY_NAME));
            String path = query.getString(query.getColumnIndex(MediaStore.Audio.Media.DATA));
            AudioBean audioBean = new AudioBean(name, path);
            if (audioBean.getName().endsWith("mp3")) {
                list.add(audioBean);
            }
        }
//     创建适配器
        AudioAdapter adapter = new AudioAdapter(this, list);
        rv. setAdapter(adapter);
//      条目监听
        adapter.setOnItemClickListener(new AudioAdapter.OnItemClickListener() {
            @Override
            public void onItemClick(int position) {
                try {
                    mp.reset();
                    mp.setDataSource(list.get(position).getPath());
                    mp.prepare();
                    mp.start();
                    updataSeekbar();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        });
        btn_pre = (Button) findViewById(R.id.btn_pre);
        btn_pre.setOnClickListener(this);
        btn_next = (Button) findViewById(R.id.btn_next);
        btn_next.setOnClickListener(this);
        btn_go_service = (Button) findViewById(R.id.btn_go_service);
        btn_go_service.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_sdcard:
                sdcard();
                break;
            case R.id.btn_net:
                net();
                break;
            case R.id.btn_raw:
                raw();
                break;
            case R.id.btn_assets:
                asset();
                break;
            case R.id.btn_pause:
                pause();
                break;
            case R.id.btn_stop:
                stop();
                break;
            case R.id.btn_goon:
                goon();
                break;
            case R.id.btn_pre:
                pre();
                break;
            case R.id.btn_next:
                next();
                break;
            case R.id.btn_go_service:
                Intent intent = new Intent(this, AudioServiceActivity.class);
                startActivity(intent);
                break;
        }
    }

    private void next() {
        if (position < list.size() - 1) {
            position++;
        } else {
            position = 0;
        }
        playAudio();
    }

    private void pre() {
        if (position > 0) {
            position--;
        } else {
            position = list.size() - 1;
        }

        playAudio();
    }

    private void playAudio() {
        try {
            mp.reset();
            mp.setDataSource(list.get(position).getPath());
            mp.prepare();
            mp.start();
            updataSeekbar();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void asset() {

        try {
            mp.reset();
            //获取assets管理器
            AssetManager assets = getAssets();

            //打开文件
            AssetFileDescriptor assetFileDescriptor = assets.openFd("qinghuaci.mp3");

            //设置资源
            mp.setDataSource(assetFileDescriptor.getFileDescriptor(), assetFileDescriptor.getStartOffset(), assetFileDescriptor.getLength());

            //开始播放
            mp.prepare();
            mp.start();
        } catch (IOException e) {
            e.printStackTrace();
        }


    }

    private void raw() {
        MediaPlayer mpCreate = MediaPlayer.create(this, R.raw.qinghuaci);
        mpCreate.start();
    }

    private void net() {
        try {
            mp.reset();
            mp.setDataSource(netPath);
            mp.prepare();
            mp.start();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    private void goon() {
        mp.start();
    }

    private void stop() {
        mp.stop();
    }

    private void pause() {
        mp.pause();
    }

    private void sdcard() {
        try {

            mp.reset();
            //创建mediaplaer
            //设置资源
            mp.setDataSource(path);
            //准备
            mp.prepare();

            //设置循环播放
            mp.setLooping(true);

            //开始
            mp.start();
            updataSeekbar();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}

```
Service主页
```
public class AudioServiceActivity extends AppCompatActivity implements View.OnClickListener {

    private Button btn_start;
    private Button btn_pause;
    private Button btn_stop;
    private Button btn_goon;
    private String path = Environment.getExternalStorageDirectory() + "/Pictures/青花瓷-周杰伦.mp3";
    private SeekBar seekbar_audio;
    private AudioService audioService;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_audio_service);
        initView();
        initListener();
    }

    private void initListener() {
        seekbar_audio.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {

            }

            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {

            }

            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                int progress = seekBar.getProgress();
                audioService.seekTo(progress);
            }
        });
    }

    private void initView() {
        btn_start = (Button) findViewById(R.id.btn_start);
        btn_pause = (Button) findViewById(R.id.btn_pause);
        btn_stop = (Button) findViewById(R.id.btn_stop);
        btn_goon = (Button) findViewById(R.id.btn_goon);
        seekbar_audio = (SeekBar) findViewById(R.id.seekbar_audio);

        btn_start.setOnClickListener(this);
        btn_pause.setOnClickListener(this);
        btn_stop.setOnClickListener(this);
        btn_goon.setOnClickListener(this);

        Intent intent = new Intent(this, AudioService.class);
        AudioServiceConnection con = new AudioServiceConnection();
        bindService(intent, con, BIND_AUTO_CREATE);

    }

    class AudioServiceConnection implements ServiceConnection {

        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            AudioService.AudioBinder audioBider = (AudioService.AudioBinder) service;
            audioService = audioBider.getServie();
        }

        @Override
        public void onServiceDisconnected(ComponentName name) {

        }
    }

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.btn_start:
                audioService.start(path, seekbar_audio);
                break;
            case R.id.btn_pause:
                audioService.pause();
                break;
            case R.id.btn_stop:
                audioService.stop();
                break;
            case R.id.btn_goon:
                audioService.goon();
                break;
        }
    }
}

```
Service类
```
public class AudioService extends Service {

    private MediaPlayer mp;
    AudioBinder audioBider = new AudioBinder();

    class AudioBinder extends Binder {
        public AudioService getServie() {
            return AudioService.this;
        }
    }

    @Nullable
    @Override
    public IBinder onBind(Intent intent) {
        return audioBider;
    }

    @Override
    public void onCreate() {
        mp = new MediaPlayer();
        super.onCreate();
    }

    /**
     * 开始播放
     *
     * @param path
     * @param seekBar
     */
    public void start(String path, SeekBar seekBar) {
        try {
            mp.reset();
            mp.setDataSource(path);
            mp.prepare();
            mp.start();
            updataSeekbar(seekBar);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }

    /**
     * 暂停
     */
    public void pause() {
        mp.pause();
    }

    public void stop() {
        mp.stop();
    }

    public void goon() {
        mp.start();
    }

    public void seekTo(int progress) {
        mp.seekTo(progress);
    }

    private void updataSeekbar(final SeekBar seekBar) {
        new Thread(new Runnable() {
            @Override
            public void run() {
                while (mp.isPlaying()) {
                    try {
                        Thread.sleep(1000);
                        seekBar.setMax(mp.getDuration());
                        seekBar.setProgress(mp.getCurrentPosition());
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
            }
        }).start();
    }
}

```

## ContentProvider_ ContentResolver后台service
创建数据库
```

//创建数据库

public class CPHelper extends SQLiteOpenHelper {

//    创建库

    public CPHelper(@Nullable Context context) {

super(context,"cp",null,1);

}

//  创建表

    @Override

    public void onCreate(SQLiteDatabase db) {

db.execSQL("create table cp(name text,age text)");

}

@Override

    public void onUpgrade(SQLiteDatabase db,int oldVersion,int newVersion) {

}

}
```

创建ContentProvider

```
public class CpContentProvider extends ContentProvider {

private CPHelper cpHelper;

//  获得数据库

    @Override

    public boolean onCreate() {

cpHelper =new CPHelper(getContext());

return false;

}

//  查寻

    @Nullable

@Override

    public Cursorquery(@NonNull Uri uri,@Nullable String[]projection,@Nullable String selection,@Nullable String[]selectionArgs,@Nullable String sortOrder) {

SQLiteDatabase writableDatabase =cpHelper.getWritableDatabase();

Cursorcp =writableDatabase.query("cp",projection,selection,selectionArgs,null,null,sortOrder);

return cp;

}

@Nullable

@Override

    public String getType(@NonNull Uri uri) {

return null;

}

/*修改*/

    @Nullable

@Override

    public Uri insert(@NonNull Uri uri,@Nullable ContentValues values) {

SQLiteDatabase writableDatabase =cpHelper.getWritableDatabase();

Log.e("TAG","名字：" +values.getAsString("name") +",年龄：" +values.getAsString("age"));

writableDatabase.insert("cp",null,values);

return null;

}

/*删除*/

    @Override

    public int delete(@NonNull Uri uri,@Nullable String selection,@Nullable String[]selectionArgs) {

SQLiteDatabase writableDatabase =cpHelper.getWritableDatabase();

int cp =writableDatabase.delete("cp",selection,selectionArgs);

Log.e("TAG","删除");

return cp;

}

//  更新

    @Override

    public int update(@NonNull Uri uri,@Nullable ContentValues values,@Nullable String selection,@Nullable String[]selectionArgs) {

SQLiteDatabase writableDatabase =cpHelper.getWritableDatabase();

int cp =writableDatabase.update("cp",values,selection,selectionArgs);

return cp;

}

}

```

创建第二应用contentrereslover
```

public class MainActivity extends AppCompatActivity implements View.OnClickListener {

private EditText et_name;

private EditText et_age;

private Button bt_query;

private Button bt_insert;

private Button bt_update;

private Button bt_delete;

private Button bt_ontact;

private Button bt_stroge;

private Button bt_sms;

private Button bt_phont;

private RecyclerView re;

private ContentResolver contentResolver;

private Uri uri;

@Override

    protected void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.activity_main);

initView();

}

//  设置弹窗

    private void initView() {

contentResolver =getContentResolver();

uri =Uri.parse("content://com.example.premiss.CpContentProvider/cp");

et_name = (EditText)findViewById(R.id.et_name);

et_age = (EditText)findViewById(R.id.et_age);

bt_query = (Button)findViewById(R.id.bt_query);

bt_insert = (Button)findViewById(R.id.bt_insert);

bt_update = (Button)findViewById(R.id.bt_update);

bt_delete = (Button)findViewById(R.id.bt_delete);

bt_ontact = (Button)findViewById(R.id.bt_ontact);

bt_stroge = (Button)findViewById(R.id.bt_stroge);

bt_sms = (Button)findViewById(R.id.bt_sms);

bt_phont = (Button)findViewById(R.id.bt_phont);

bt_query.setOnClickListener(this);

bt_insert.setOnClickListener(this);

bt_update.setOnClickListener(this);

bt_delete.setOnClickListener(this);

bt_ontact.setOnClickListener(this);

bt_stroge.setOnClickListener(this);

bt_sms.setOnClickListener(this);

bt_phont.setOnClickListener(this);

re = (RecyclerView)findViewById(R.id.re);

}

@Override

    public void onClick(View v) {

switch (v.getId()) {

case R.id.bt_query:

query();

break;

case R.id.bt_insert:

insert();

break;

case R.id.bt_update:

update();

break;

case R.id.bt_delete:

delete();

break;

case R.id.bt_ontact:

break;

case R.id.bt_stroge:

break;

case R.id.bt_sms:

break;

case R.id.bt_phont:

break;

}

}

private void delete() {

contentResolver.delete(uri,"name=?",new String[]{"123"});

}

private void update() {

ContentValues values =new ContentValues();

values.put("name","223");

contentResolver.update(uri,values,"name=?",new String[]{"123"});

}

private void insert() {

// validate

        String name =et_name.getText().toString().trim();

if (TextUtils.isEmpty(name)) {

Toast.makeText(this,"name!",Toast.LENGTH_SHORT).show();

return;

}

String age =et_age.getText().toString().trim();

if (TextUtils.isEmpty(age)) {

Toast.makeText(this,"age",Toast.LENGTH_SHORT).show();

return;

}

ContentValues values =new ContentValues();

values.put("name",name);

values.put("age",age);

contentResolver.insert(uri,values);

}

private void query() {

Cursorquery =contentResolver.query(uri,null,null,null,null);

while (query.moveToNext()) {

String name =query.getString(query.getColumnIndex("name"));

String age =query.getString(query.getColumnIndex("age"));
条目布局Log.e("TAG","name=" +name +"age=" +age);

         }

    }

}
```

## 条目布局card view


## 透明状态栏使用方法
透明状态栏使用方法

1.在res文件夹下创建style-V21文件

2.复制style文件

```

<item name="android:statusBarColor">#00ffffff</item>

```

## Service服务类
在Manifest中application注册
```
 <service
            android:name=".service.AngelServer"
            android:enabled="true"
            android:exported="true"/>
```
Service类
```

//创建继承Service方法

public class AngleService extends Service {

//    创建绑定对象

    AnBinder anBinder =new AnBinder();

//    创建绑定方法

    public class AnBinder extends Binder {

public void call(String img) {

methodInService(img);

}

private void methodInService(String img) {

Log.e("TAG",img);

}

}

public AngleService() {

}

// 绑定

    @Override

    public IBinderonBind(Intent intent) {

Log.e("TAG","onBind");

return anBinder;

}

// 创建

    @Override

    public void onCreate() {

super.onCreate();

Log.e("TAG","onCreate");

}

//  创建命令

    @Override

    public int onStartCommand(Intent intent,int flags,int startId) {

Log.e("TAG","onStartCommand");

return super.onStartCommand(intent,flags,startId);

}

// 解绑

    @Override

    public boolean onUnbind(Intent intent) {

Log.e("TAG","onUnbind");

return super.onUnbind(intent);

}

//  销毁

    @Override

    public void onDestroy() {

super.onDestroy();

Log.e("TAG","onDestroy");

}

}

```
MainActivity类
```
public class MainActivity extends AppCompatActivity implements View.OnClickListener {

private Button btn_start_service;

private Button btn_stop_service;

private Button btn_bind_service;

private Button btn_unbind_service;

private AflyConnection aflyConnection;

private Intent intent;

@Override

    protected void onCreate(Bundle savedInstanceState) {

super.onCreate(savedInstanceState);

setContentView(R.layout.activity_main);

initView();

}

private void initView() {

btn_start_service = (Button)findViewById(R.id.btn_start_service);

btn_stop_service = (Button)findViewById(R.id.btn_stop_service);

btn_bind_service = (Button)findViewById(R.id.btn_bind_service);

btn_unbind_service = (Button)findViewById(R.id.btn_unbind_service);

btn_start_service.setOnClickListener(this);

btn_stop_service.setOnClickListener(this);

btn_bind_service.setOnClickListener(this);

btn_unbind_service.setOnClickListener(this);

intent =new Intent(this,AngleService.class);

intent.putExtra("data","this is from Activity Data");

//        创建连接方法

        angleConnection =new AngleConnection();
//绑定服务
bindService(intent, angleConnection, BIND_AUTO_CREATE);

}

@Override

    public void onClick(View v) {

switch (v.getId()) {

case R.id.btn_start_service:

startService(intent);

break;

case R.id.btn_stop_service:

stopService(intent);

break;

case R.id.btn_bind_service:

bindService(intent,angleConnection,BIND_AUTO_CREATE);

break;

case R.id.btn_unbind_service:

unbindService(angleConnection);

break;

}

}

//    创建连接方法

    class AngleConnection implements ServiceConnection {

@Override

        public void onServiceConnected(ComponentName name, IBinderservice) {

AngleService.AnBinder service1 = (AngleService.AnBinder)service;

service1.call("通过绑定数据把Activity传达Service");

}

@Override

        public void onServiceDisconnected(ComponentName name) {

}

}

}
```

## 写入相册权限
写入相册权限
```
Intent intent = new Intent(Intent.ACTION_PICK,MediaStore.Images.Media.EXTERNAL_CONTENT_URI);

startActivityForResult(intent,200);

@Override

protected void onActivityResult(int requestCode,int resultCode,@Nullable Intent data){

    super.onActivityResult(requestCode,resultCode,data);

    if(requestCode == 200 && resultCode ==RESULT_OK){

        Uri data1 = data.getData();

        iv_head_pthot.setImageURI(data1);

    }

}
```
## RecycleView多布局+RecycleView结合banner
`RecycleView结合banner`
主页面
```

public class HomeFragment extends Fragment {

    private RecyclerView recycle;
    private ArrayList<BannerBean.DataBean> banners;
    private ArrayList<ArticleBean.DataBean.DatasBean> articles;
    private Gson gson;
    private String bannerUrl = "https://www.wanandroid.com/banner/json";
    private String articleUrl = "https://www.wanandroid.com/article/list/";
    private int page = 0;
    private HomeAdapter homeAdapter;
    private SmartRefreshLayout sl;


    @Override
    public View onCreateView(@NonNull LayoutInflater inflater, @Nullable ViewGroup container,
                             @Nullable Bundle savedInstanceState) {
        View view = inflater.inflate(R.layout.home_fragment, container, false);
        gson = new GsonBuilder().create();
        initView(view);
        initData();
        return view;

    }

    private void initView(View view) {
//        创建适配器
        recycle = view.findViewById(R.id.recycle);
        banners = new ArrayList<>();
        articles = new ArrayList<>();
        homeAdapter = new HomeAdapter(banners, articles, getActivity());
//        绑定适配器
        recycle.setAdapter(homeAdapter);
//        设置布局管理器
        recycle.setLayoutManager(new LinearLayoutManager(getActivity()));
//        设置分割线
        recycle.addItemDecoration(new DividerItemDecoration(getActivity(), OrientationHelper.VERTICAL));
//        刷新
        sl = view.findViewById(R.id.sl);
//监听
        sl.setOnRefreshLoadMoreListener(new OnRefreshLoadMoreListener() {
            @Override
            public void onLoadMore(@NonNull RefreshLayout refreshLayout) {
                page++;
                initData();

            }

            @Override
            public void onRefresh(@NonNull RefreshLayout refreshLayout) {
                page = 0;
                if (articles != null && articles.size() > 0) {
                    articles.clear();
                    initData();
                }

            }
        });
    }

    private void initData() {
        new Thread(new Runnable() {
            @Override
            public void run() {

//                获得GSON字符串
                String bannerGson = getResponse(bannerUrl);
                Log.e("TAG", bannerGson);
                String articleGson = getResponse(articleUrl + page + "/json");
//                获得数组
                final BannerBean bannerBean = gson.fromJson(bannerGson, BannerBean.class);
                final ArticleBean articleBean = gson.fromJson(articleGson, ArticleBean.class);
                getActivity().runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
//                        添加到空集合
                        banners.addAll(bannerBean.getData());
                        articles.addAll(articleBean.getData().getDatas());
//                        刷新适配器
                        homeAdapter.notifyDataSetChanged();
//                        
                        sl.finishLoadMore();
                        sl.finishRefresh();
                    }
                });
            }
// 从网络获取字符串
            private String getResponse(String urls) {
                try {
                    URL url = new URL(urls);
                    HttpURLConnection connection = (HttpURLConnection) url.openConnection();
                    int responseCode = connection.getResponseCode();
                    if (responseCode == HttpURLConnection.HTTP_OK) {
                        byte[] bytes = new byte[1024];
                        int length = 0;
                        StringBuffer buffer = new StringBuffer();
                        InputStream inputStream = connection.getInputStream();
                        while ((length = inputStream.read(bytes)) != -1) {
                            buffer.append(new String(bytes, 0, length));
                        }
                        return buffer.toString();
                    }
                } catch (MalformedURLException e) {
                    e.printStackTrace();
                } catch (IOException e) {
                    e.printStackTrace();
                }
                return null;
            }
        }).start();
    }

}
  ```
适配器
```
public class RC_Adapter extends RecyclerView.Adapter {
    private ArrayList<BannerBean.DataBean> banners;
    private ArrayList<ArticleBean.DataBean.DatasBean> article;
    private LayoutInflater inflater;
    private Context context;
// 定义多布局
//注意：如果有banner，banner必须为0
    private static final int TYPE_1 = 0;
    private static final int TYPE_2 = 1;

    public RC_Adapter(ArrayList<BannerBean.DataBean> banners, ArrayList<ArticleBean.DataBean.DatasBean> article, Context context) {
        this.banners = banners;
        this.article = article;
        this.context = context;
        this.inflater = LayoutInflater.from(context);
    }
//判断不同的条目布局
    @Override
    public int getItemViewType(int position) {
        if (position == 0) {
            return TYPE_1;
        } else {
            return TYPE_2;
        }
    }
// 创建不同的条目布局
    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        switch (viewType) {
            case TYPE_1:
                View view1 = inflater.inflate(R.layout.layout_typeone, null);
                return new ViewHolder1(view1);
            case TYPE_2:
                View view2 = inflater.inflate(R.layout.layout_typetwo, null);
                return new ViewHolder2(view2);
        }
        return null;
    }
//绑定布局
    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {
        switch (getItemViewType(position)) {
//banner布局
            case TYPE_1:
                ViewHolder1 viewHolder1 = (ViewHolder1) holder;
                viewHolder1.banner.setImages(banners).setImageLoader(new ImageLoader() {
                    @Override
                    public void displayImage(Context context, Object path, ImageView imageView) {
                        BannerBean.DataBean dataBean = (BannerBean.DataBean) path;
                        Glide.with(context).load(dataBean.getImagePath()).into(imageView);
                    }
                }).start();
                break;
//条目布局
            case TYPE_2:
                ArticleBean.DataBean.DatasBean datasBean = article.get(position - 1);
                ViewHolder2 viewHolder2 = (ViewHolder2) holder;
                viewHolder2.tv_title.setText(datasBean.getTitle());
                viewHolder2.tv_time.setText(datasBean.getNiceDate());
                break;
        }

    }
// 确定条目数量
    @Override
    public int getItemCount() {
//因为有banner所以总数量要+1
        return article.size() + 1;
    }

    public static
    class ViewHolder1 extends RecyclerView.ViewHolder {
        public View rootView;
        public Banner banner;

        public ViewHolder1(View rootView) {
            super(rootView);
            this.rootView = rootView;
            this.banner = (Banner) rootView.findViewById(R.id.banner);
        }

    }


    public static
    class ViewHolder2 extends RecyclerView.ViewHolder{
        public View rootView;
        public TextView tv_title;
        public TextView tv_time;

        public ViewHolder2 (View rootView) {
            super(rootView);
            this.rootView = rootView;
            this.tv_title = (TextView) rootView.findViewById(R.id.tv_title);
            this.tv_time = (TextView) rootView.findViewById(R.id.tv_time);
        }

    }
}

```
`RecycleView多布局`

主页 面
```
/**
 * RecyclerView使用步骤
 * 1.添加依赖(版本问题注意)
 * 2.创建布局（宽高必须是充满的）
 * 3.找控件
 * 4.设置布局管理器（三种显示方式：线性布局、网格布局、瀑布流布局）
 * 5.获取数据（切换子线程的方法）
 * 6.创建适配器（*****）-- 重写三个，通过接口回调实现点击事件
 * 7.设置适配器
 */
public class MainActivity extends AppCompatActivity {

    private RecyclerView rv;
    private String foodUrl = "http://www.qubaobei.com/ios/cf/dish_list.php?stage_id=1&limit=10&page=";
    private ArrayList<FoodBean.DataBean> list;
    //    private RvAdapter adapter;
    private MultiLayoutAdapter adapter;
    private int page = 1;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        initView();
        initData();
        initListenr();
    }

    private void initListenr() {
        rv.addOnScrollListener(new RecyclerView.OnScrollListener() {
            /**
             * 滑动状态改变
             * @param recyclerView
             * @param newState
             */
            @Override
            public void onScrollStateChanged(@NonNull RecyclerView recyclerView, int newState) {
                super.onScrollStateChanged(recyclerView, newState);
                Log.e("TAG", "MainActivity onScrollStateChanged()" + newState);
                //获取布局管理
                LinearLayoutManager lm = (LinearLayoutManager) recyclerView.getLayoutManager();
                //获取最后最后一页最后条目的索引
                int lastVisibleItemPosition = lm.findLastVisibleItemPosition();
                int firstVisibleItemPosition = lm.findFirstVisibleItemPosition();
                //获取总共的题目个数
                int itemCount = recyclerView.getAdapter().getItemCount();
                //获取当前页条目个数
                int childCount = recyclerView.getChildCount();

                switch (newState) {
                    case RecyclerView.SCROLL_STATE_IDLE:
                        if (itemCount > 0 && itemCount == childCount + firstVisibleItemPosition) {
                            page++;
                            initData();
                        }
                        break;
                }
            }

            /**
             * 滑动监听
             * @param recyclerView
             * @param dx
             * @param dy
             */
            @Override
            public void onScrolled(@NonNull RecyclerView recyclerView, int dx, int dy) {
                super.onScrolled(recyclerView, dx, dy);
                Log.e("TAG", "MainActivity onScrolled(),dx:" + dx + ",dy:" + dy);
            }
        });
    }

    private void initData() {
        new Thread(new Runnable() {
            @Override
            public void run() {
                try {
                    URL url = new URL(foodUrl + page);
                    HttpURLConnection con = (HttpURLConnection) url.openConnection();
                    int responseCode = con.getResponseCode();
                    if (responseCode == HttpURLConnection.HTTP_OK) {
                        InputStream inputStream = con.getInputStream();
                        String response = streamToString(inputStream);
                        FoodBean foodBean = new Gson().fromJson(response, FoodBean.class);
                        final List<FoodBean.DataBean> data = foodBean.getData();
                        runOnUiThread(new Runnable() {
                            @Override
                            public void run() {

                                list.addAll(data);
                                adapter.notifyDataSetChanged();
                            }
                        });
                    }

                    //获取banners集合
                    //banners.addAll(bannner);
                    //adapter.notifyDataSetChanged();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
        }).start();
    }

    private String streamToString(InputStream inputStream) {
        ByteArrayOutputStream outputStream = new ByteArrayOutputStream();
        int length = -1;
        byte[] bytes = new byte[1024];
        try {
            while ((length = inputStream.read(bytes)) != -1) {
                outputStream.write(bytes, 0, length);
            }

            outputStream.close();
            String result = outputStream.toString();
            return result;
        } catch (Exception e) {
            e.printStackTrace();
        }
        return "";
    }

    private void initView() {
        rv = (RecyclerView) findViewById(R.id.rv);

        //获取布局管理器并设置
        LinearLayoutManager linearLayoutManager = new LinearLayoutManager(this);
        GridLayoutManager gridLayoutManager = new GridLayoutManager(this, 2);
        StaggeredGridLayoutManager staggeredManager = new StaggeredGridLayoutManager(2, StaggeredGridLayoutManager.VERTICAL);
//        linearLayoutManager.setOrientation(RecyclerView.HORIZONTAL);
        rv.setLayoutManager(linearLayoutManager);

        list = new ArrayList<>();

        //分割线
        rv.addItemDecoration(new DividerItemDecoration(this, DividerItemDecoration.VERTICAL));

        //创建适配器
//        adapter = new RvAdapter(list, this);
        adapter = new MultiLayoutAdapter(list, this);

        //设置适配器
        rv.setAdapter(adapter);

    }
}

```
适配器
```
/**
 * 多布局适配器写法：
 * 1.自定义一个适配器继承自RecyclerView.Adapter，泛型是<RecyclerView.ViewHolder>
 * 2.多（相对于单布局）重写一个方法getItemViewType()，条目view分类型
 * 3.重写onCreateViewHolder，分类型加载不同布局
 * 4.重写onBindViewHolder，根据holder获取类型，然后分类型赋值
 */
class MultiLayoutAdapter extends RecyclerView.Adapter<RecyclerView.ViewHolder> {
    private ArrayList<FoodBean.DataBean> list;
    private ArrayList<BannerBean> banners;
    private Context context;
// 定义不同的布局
    private int VIEW_ITEM_TYPE_ONE = 1;
    private int VIEW_ITEM_TYPE_TWO = 2;
    private int VIEW_ITEM_TYPE_THREE = 3;


    public MultiLayoutAdapter(ArrayList<FoodBean.DataBean> list, Context context) {
        this.list = list;
        this.context = context;
    }


    @NonNull
    @Override
    public RecyclerView.ViewHolder onCreateViewHolder(@NonNull ViewGroup parent, int viewType) {
        if (viewType == VIEW_ITEM_TYPE_ONE) {
            View view = LayoutInflater.from(context).inflate(R.layout.item_rv_one, null);
            return new ViewHolderOne(view);
        } else if (viewType == VIEW_ITEM_TYPE_TWO) {
            View view = LayoutInflater.from(context).inflate(R.layout.item_rv_two, null);
            return new ViewHolderTwo(view);
        } else {
            View view = LayoutInflater.from(context).inflate(R.layout.item_rv_two, null);
            return new ViewHolderTwo(view);
        }
    }

    @Override
    public void onBindViewHolder(@NonNull RecyclerView.ViewHolder holder, int position) {

        FoodBean.DataBean dataBean = list.get(position);
        int itemViewType = holder.getItemViewType();

        if (itemViewType == VIEW_ITEM_TYPE_ONE) {
            //banners，position==0
            ViewHolderOne viewHolderOne = (ViewHolderOne) holder;
            viewHolderOne.tv_item.setText(dataBean.getTitle());
            Glide.with(context).load(dataBean.getPic()).into(viewHolderOne.iv_item);
        } else if (itemViewType == VIEW_ITEM_TYPE_TWO) {
            int newPosition = position - 1;//position==1
            FoodBean.DataBean dataBean1 = list.get(newPosition);
            ViewHolderTwo viewHolderTwo = (ViewHolderTwo) holder;
            viewHolderTwo.tv_item.setText(dataBean.getTitle());
            Glide.with(context).load(dataBean.getPic()).into(viewHolderTwo.iv_item);
        } else {
            ViewHolderTwo viewHolderTwo = (ViewHolderTwo) holder;
            viewHolderTwo.tv_item.setText(dataBean.getTitle());
            Glide.with(context).load(dataBean.getPic()).into(viewHolderTwo.iv_item);
        }
    }
//判断返回不同的条目
    @Override
    public int getItemViewType(int position) {
        int i = position % 3;
        if (i == 0) {
            return VIEW_ITEM_TYPE_ONE;
        } else if (i == 1) {
            return VIEW_ITEM_TYPE_TWO;
        } else {
            return VIEW_ITEM_TYPE_THREE;
        }
    }

    @Override
    public int getItemCount() {
        return list.size();
    }

    public class ViewHolderOne extends RecyclerView.ViewHolder {
        private ImageView iv_item;
        private TextView tv_item;

        public ViewHolderOne(@NonNull View itemView) {
            super(itemView);
            iv_item = itemView.findViewById(R.id.iv_item);
            tv_item = itemView.findViewById(R.id.tv_item);
        }
    }

    public class ViewHolderTwo extends RecyclerView.ViewHolder {
        private ImageView iv_item;
        private TextView tv_item;

        public ViewHolderTwo(@NonNull View itemView) {
            super(itemView);
            iv_item = itemView.findViewById(R.id.iv_item);
            tv_item = itemView.findViewById(R.id.tv_item);
        }
    }
}

```

## 打电话（Call Phone）
```
//获得权限
private void initPerssion() {
        PermissionsUtil.requestPermission(this, new PermissionListener() {
            @Override
            public void permissionGranted(@NonNull String[] permission) {
                /*
                权限请求成功
                * */

            }

            @Override
            public void permissionDenied(@NonNull String[] permission) {
                /*
                * 请求失败*/

            }
        }, Manifest.permission.CALL_PHONE, Manifest.permission.READ_CONTACTS, Manifest.permission.READ_SMS, Manifest.permission.READ_EXTERNAL_STORAGE);
    }
```
```
//创建打电话方法
    private void callPhone() {
        Intent intent = new Intent(Intent.ACTION_CALL);
        intent.setData(Uri.parse("tel:" + 10086));
        startActivity(intent);
    }

```
效果图
![](https://upload-images.jianshu.io/upload_images/21988850-18882b46dd34f6ac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 创建Option菜单/Context菜单
>注意事项：如果在fragment使用OptionsMenu需要添加        `setHasOptionsMenu(true);`

`创建ContextMenu`
```

//    创建条目ID
    private static final int ONE = 1;
    private static final int TWO = 2;
//注册菜单
registerForContextMenu(recycle);
//   创建ContextMenu菜单
    @Override
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        //代码创建 ContextMenu条目
        menu.add(0,ONE,0,"删除");
        menu.add(0,TWO,0,"修改");
//        xml创建ContextMenu条目
        getActivity().getMenuInflater().inflate(R.menu.select,menu);
        
    }
//监听ContextMenu菜单条目
    @Override
    public boolean onContextItemSelected(MenuItem item) {
        switch (item.getItemId()){
            case ONE：
                break;
            case TWO：
                break;
        }
        return super.onContextItemSelected(item);

```
`创建OptionsMenu`
```
//    创建条目ID
    private static final int ONE = 1;
    private static final int TWO = 2;
  //  创建OptionsMenu条目
    
    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        //代码创建 OptionsMenu条目
        menu.add(0, ONE, 0, "删除").setShowAsAction(MenuItem.SHOW_AS_ACTION_ALWAYS);//显示在toobar;
        menu.add(0, TWO, 0, "修改");
        //        xml创建OptionsMenu条目
        getMenuInflater().inflate(R.menu.select, menu);

        return super.onCreateOptionsMenu(menu);
    }

    //  监听OptionsMenu条目
    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        switch (item.getItemId()) {
            case ONE:
                break;
            case TWO:
                break;
        }
        return super.onOptionsItemSelected(item);
    }
```

## OkHttp

一、HTTP协议回顾:

1. HTTP协议概述

>WEB浏览器与WEB服务器之间的一问一答的交互过程必须遵循一定的规则，这个规则就是HTTP协议。
2. HTTP是 Hyper Text Transfer Protocol(超文本传输协议)

>它是TCP/IP协议的一个应用层协议，用于定义WEB浏览器与WEB服务器之间交换数据 的过程及数据本身的格式。
Http协议是一种应用层协议，它通过TCP实现了可靠的数据传输，能够保证数据的完整性、正确性
而TCP对于数据传输控制的优点也能够体现在Http协议上，使得Http的数据传输吞吐量、效率得到保证

3. 请求格式(用火狐看源码)

请求：
请求行 ： 请求方式 请求路径 版本
请求头 ： 以key-value形式组成，K：V。。。
空行
请求体 : 用于数据传递：get方式没有请求体（参数地址传递） post方式有请求体

响应:
响应行 ：版本 响应码 响应信息
响应头 ：以key-value形式组成，K：V。。。
空行
响应体 ：响应正文
 4\. 常用请求头

> *   Host: [www.baidu.com](https://links.jianshu.com/go?to=http%3A%2F%2Fwww.baidu.com)
> *   User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:64.0) Gecko/20100101 Firefox/64.0
>     （User Agent用户代理，是Http协议中的一部分，属于头域的组成部分，User Agent也简称UA。它是一个特殊字符串头，是一种向访问网站提供你所使用的浏览器类型及版本、操作系统及版本、浏览器内核、等信息的标识。通过这个标识，用户所访问的网站可以显示不同的排版从而为用户提供更好的体验或者进行信息统计；例如用手机访问谷歌和电脑访问是不一样的，这些是谷歌根据访问者的UA来判断的。UA可以进行伪装。
> *   Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
> *   Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
> *   Accept-Encoding: gzip, deflate, br
> *   Connection: keep-alive
> *   Cache-Control: max-age=0
> *   Content-Type: text/html
> *   Content-Length:120

5. 请求方式

>Get：请求获取Request-URI所标识的资源
POST：在Request-URI所标识的资源后附加新的数据
HEAD： 请求获取由Request-URI所标识的资源的响应信息报头
PUT：请求服务器存储一个资源，并用Request-URI作为其标识
DELETE：请求服务器删除Request-URI所标识的资源
TRACE：请求服务器回送收到的请求信息，主要用于测试或诊断
CONNECT：保留将来使用
OPTIONS：请求查询服务器的性能，或者查询与资源相关的选项
Patch：是对PUT方法的补充，用来对已知资源进行局部更新

`注意：`

>GET方式(以在请求的URL地址后以?的形式带上交给服务器的数据，多个数据之间以&进行分隔,通常传送的数据不超过1kb)，
通过请求URI得到资源。一般用于获取/查询资源信息
POST方式(在请求的正文内容中向服务器发送数据,传送的数据无限制)，
用于向服务器提交新的内容。一般用于更新资源信息
6. 状态码

>200(正常),206请求部分数据
302/307(临时重定向),
304(未修改),
404(找不到),
500(服务器内部错误)
7. Http协议的特点
>①支持客户/服务器模式。
>②简单快速：客户向服务器请求服务时，只需传送请求方法和路径。请求方法常用的有GET、 HEAD、POST。每种方法规定了客户与服务器联系的类型不同。 由于HTTP协议简单，使得HTTP服务器的程序规模小，因而通信速度很快。
③灵活：HTTP允许传输任意类型的数据对象。正在传输的类型由Content-Type加以标记。
④无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求， 并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
⑤无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。 缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每 次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快
8. Http 1.0 与 Http1.1的区别

>1.0协议，客户端与web服务器建立连接后，只能获得一个web资源！ 而1.1协议，允许客户端与web服务器建立连接后，在一个连接上获取多个web资源！

二、网络通信

1. 网络三要素

>IP:主机的唯一表示 （http://202.108.22.5/）
端口号：正在运行的程序（0~65535）
协议：通信规则，TCP以及UDP
-
2. 网络模型

2.1 简介

定义：计算机网络的各层 + 其协议的集合
作用：定义该计算机网络的所能完成的功能
2.2 结构介绍

OSI体系结构：概念清楚 & 理念完整，但复杂 & 不实用

TCP / IP体系结构：含了一系列构成互联网基础的网络协议，是Internet的核心协议 & 被广泛应用于局域网 和 广域网

五层体系结构：融合了OSI 与 TCP / IP的体系结构，目的是为了学习 & 讲解计算机原理
![](https://upload-images.jianshu.io/upload_images/21988850-3f55bee7868bb78e.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. TCP与UDP区别：

TCP:
建立连接
安全可靠协议
以流进行数据传递，无大小限制
三次握手协议，四次挥手

UDP:
不建立连接
不可靠协议
以数据包传递，有大小限制64K

4. Socket以及Http：

Socket：长连接，理论上客户端和服务端一旦建立连接，则不会主动断掉；但是由于各种环境因素可能会是连接断开，比如说：服务器端或客户端主机down了，网络故障，或者两者之间长时间没有数据传输，网络防火墙可能会断开该链接已释放网络资源。所以当一个socket连接中没有数据的传输，那么为了位置连续的连接需要发送心跳消息，具体心跳消息格式是开发者自己定义的。

Http：短连接，即客户端向服务器发送一次请求，服务器端响应后连接即会断掉。

三、异步和同步的区别

同步是阻塞模式，异步是非阻塞模式

同步请求：发送方发出数据后，等接收方发回响应以后才发下一个数据包的通讯方式。
异步请求：发送方发出数据后，不等接收方发回响应，接着发送下个数据包的通讯方式
四、HttpURlconnection发送网络请求:

1. get请求
```
//子线程中执行请求
URL url = new URL(path);
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
    urlConnection.setRequestMethod("GET");
    urlConnection.setConnectTimeout(5000);
    if (urlConnection.getResponseCode() == 200){
        InputStream inputStream = urlConnection.getInputStream();
        
    }
```
2. post请求
```
//子线程中执行请求
URL url = new URL(path);
HttpURLConnection urlConnection = (HttpURLConnection) url.openConnection();
urlConnection.setRequestMethod("POST");
urlConnection.setConnectTimeout(5000);
urlConnection.setReadTimeout(5000);
String content = "name="+URLEncoder.encode(name)+"&pass="+URLEncoder.encode(pass);//数据编解码
urlConnection.setRequestProperty("Content-Type","application/x-www-form-urlencoded");//设置请求头
urlConnection.setRequestProperty("Content-Length",content.length()+"");
urlConnection.setDoOutput(true);//以后就可以使用conn.getOutputStream().write()  
OutputStream outputStream = urlConnection.getOutputStream();
outputStream.write(content.getBytes());
if (urlConnection.getResponseCode() == 200){ }
```

3. json解析:

json解析
```
      bean:
        JSONObject jsonObject = new JSONObject(text);
        String code = jsonObject.getString("resultCode");

    array:
        ArrayList<StuClzInfo> list = new ArrayList<>();
        JSONArray array = new JSONArray(text);
        for (int i = 0; i < array.length(); i++) {
                StuClzInfo info = new StuClzInfo();
                JSONObject object = array.getJSONObject(i);
                info.setCode(object.getString("code"));
                info.setCount(object.getInt("count"));
                list.add(info);
        }

        
    bean中带array:
        JSONObject object = new JSONObject(text);
            String resultCode = object.getString("resultCode");
            if ("1".equals(resultCode)) {
                tv_name.setText(object.getString("school"));
                JSONArray clazz = object.getJSONArray("clazz");
                List<StuClzInfo> list = new ArrayList<>();
                for (int i = 0; i < clazz.length(); i++) {
                    JSONObject o = clazz.getJSONObject(i);
                    StuClzInfo info = new StuClzInfo();
                    info.setCode(o.getString("code"));
                    info.setCount(o.getInt("count"));
                    list.add(info);
                }
            }
```
gson解析(bean,array,bean中带array)
```
    bean:
        Gson gson = new Gson();
        LoginInfo loginInfo = gson.fromJson(text, LoginInfo.class);//bean
        
    array:
        Gson gson = new Gson();
        Type type = new TypeToken<List<StuClzInfo>>() {}.getType();
        List<StuClzInfo> list = gson.fromJson(text, type);//list<bean>
        
    bean中带array:
        SchoolInfo info = gson.fromJson(text, SchoolInfo.class);
        list = info.getClazz();//bean.list<bean>
```
fastjson解析(bean,array,bean中带array)
```
bean:
        LoginInfo loginInfo1 = JSON.parseObject(text, LoginInfo.class);

    array:
        List<StuClzInfo> list = JSON.parseArray(text, StuClzInfo.class);
        
    bean中带array:
        SchoolInfo info = JSON.parseObject(text, SchoolInfo.class);
        List<StuClzInfo> list = info.getClazz();
```
五、OkHttp3讲解

1. OkHttp3简介

1.支持http和https协议,api相同,易用;
2.http使用线程池,https使用多路复用;
3.okhttp支持同步和异步调用;
4.支持普通form和文件上传form;
5.操作请求和响应(日志,请求头,body等);
6.okhttp可以设置缓存;
7.支持透明的gzip压缩响应体

2. OkHttp3 配置

①依赖：`implementation 'com.squareup.okhttp3:okhttp:3.12.0'`
②添加网络权限：`<uses-permission android:name="android.permission.INTERNET"/>`

3. OkHttp3 使用思路

get请求思路

1.获取okHttpClient对象
2.构建Request对象
3.构建Call对象
4.通过Call.enqueue(callback)方法来提交异步请求；execute()方法实现同步请求
post请求思路

1.获取okHttpClient对象
2.创建RequestBody
3.构建Request对象
4.构建Call对象
5.通过Call.enqueue(callback)方法来提交异步请求；execute()方法实现同步请求
4. OkHttp3 发送异步请求（GET）
```
//第一步获取okHttpClient对象
    OkHttpClient client = new OkHttpClient.Builder()
            .build();
    //第二步构建Request对象
    Request request = new Request.Builder()
            .url(url)
            .get()
            .build();
    //第三步构建Call对象
    Call call = client.newCall(request);
    //第四步:异步get请求
    call.enqueue(new Callback() {
        @Override
        public void onFailure(Call call, IOException e) {
            Log.i("onFailure", e.getMessage());
        }

        @Override
        public void onResponse(Call call, Response response) throws IOException {
            //得到的子线程
            String result = response.body().string();
            Log.i("result", result);
        }
    });
```
4. OkHttp3 发送同步请求（GET）
```
//第一步获取okHttpClient对象
    OkHttpClient client = new OkHttpClient.Builder()
            .build();
    //第二步构建Request对象
    Request request = new Request.Builder()
            .url(url)
            .get()
            .build();
    //第三步构建Call对象
    final Call call = client.newCall(request);
    //第四步:同步get请求
    new Thread(new Runnable() {
        @Override
        public void run() {
            try {
                Response response = call.execute();//必须子线程执行
                String result = response.body().string();
                Log.i("response", result);
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }).start();
```
5. OkHttp3 发送异步请求（POST）
```
    //第一步创建OKHttpClient
    OkHttpClient client = new OkHttpClient.Builder()
            .build();
    //第二步创建RequestBody（Form表达）
    RequestBody body = new FormBody.Builder()
            .add("username", "admin")
            .add("password", "123456")
            .build();
    //第三步创建Rquest
    Request request = new Request.Builder()
            .url(url)
            .post(body)
            .build();
    //第四步创建call回调对象
    final Call call = client.newCall(request);
    //第五步发起请求
    call.enqueue(new Callback() {
        @Override
        public void onFailure(Call call, IOException e) {
            Log.i("onFailure", e.getMessage());
        }

        @Override
        public void onResponse(Call call, Response response) throws IOException {
            String result = response.body().string();
            Log.i("result", result);
        }
    });
```
6. 请求头处理（Header）以及超时和缓冲处理以及响应处理
```
    //超时设置
    OkHttpClient client = new OkHttpClient.Builder()
            .connectTimeout(5,TimeUnit.SECONDS)//设置连接时间
            .readTimeout(5,TimeUnit.SECONDS)//设置读取时间
            .writeTimeout(5,TimeUnit.SECONDS)//设置写出时间
    .cache(new Cache(new File(getCacheDir(), "Cache"),10*1024*1024))
            .build();

    //表单提交
    RequestBody requestBody = new FormBody.Builder()
            .add("pno", "1")
            .add("ps","50")
            .add("dtype","son")
            .add("key","4a7cf244fd7efbd17ecbf0cb8e4d1c85")
            .build();

    //请求头设置
    Request request = new Request.Builder()
            .url(url)
    .addHeader("Content-Type", "application/x-www-form-urlencoded;charset=utf-8")
    .header("User-Agent", "OkHttp Example")
            .post(body)
            .build();
  
    //响应处理
    @Override
    public void onResponse(Call call, Response response) throws IOException {
        //响应行
        Log.d("ok", response.protocol() + " " +response.code() + " " + response.message());
        //响应头
        Headers headers = response.headers();
        for (int i = 0; i < headers.size(); i++) {
            Log.d("ok", headers.name(i) + ":" + headers.value(i));
        }
        //响应体
        final String string = response.body().string();

        Log.d("ok", "onResponse: " + string);
        runOnUiThread(new Runnable() {
            @Override
            public void run() {
                tx.setText(string);
            }
        });
    }
```
7. 请求体处理（Form表单，String字符串，流，文件）

①POST方式提交String/JSON application/json;json串
```
//POST方式提交String
MediaType mediaType1 = MediaType.parse("application/x-www-form-urlencoded;charset=utf-8");
String requestBody = "pno=1&ps=50&dtype=son&key=4a7cf244fd7efbd17ecbf0cb8e4d1c85";
        RequestBody requestBody1 = RequestBody.create(mediaType1, requestBody);

//POST方式提交JSON：传递JSON同时设置son类型头（接口找一下）
RequestBody requestBodyJson = RequestBody.create(MediaType.parse("application/json;charset=utf-8"), "{}");
request.addHeader("Content-Type", "application/json")//必须加json类型头
            
//POST方式提交无参
RequestBody requestBody1 = RequestBody.create(MediaType.parse("application/x-www-form-urlencoded;charset=utf-8"), "");

```
② POST方式提交流
```
RequestBody requestBody2 = new RequestBody() {
            @Nullable
            @Override
            public MediaType contentType() {
                return MediaType.parse("application/x-www-form-urlencoded;charset=utf-8");
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                sink.writeUtf8("pno=1&ps=50&dtype=son&key=4a7cf244fd7efbd17ecbf0cb8e4d1c85");
            }
        };
```
③POST方式提交表单
```
RequestBody requestBody4 = new FormBody.Builder()
                .add("pno", "1")
                .add("ps","50")
                .add("dtype","son")
                .add("key","4a7cf244fd7efbd17ecbf0cb8e4d1c85")
                .build();
```
④POST提交文件
```
MediaType mediaType3 = MediaType.parse("text/x-markdown; charset=utf-8");
        File file = new File("test.txt");
        RequestBody requestBody3 = RequestBody.create(mediaType3, file);

        //5.POST方式提交分块请求
        MultipartBody body = new MultipartBody.Builder("AaB03x")
                .setType(MultipartBody.FORM)
                .addPart(
                        Headers.of("Content-Disposition", "form-data; name=\"title\""),
                        RequestBody.create(null, "Square Logo"))
                .addPart(
                        Headers.of("Content-Disposition", "form-data; name=\"image\""),
                        RequestBody.create(MediaType.parse("image/png"), new File("website/static/logo-square.png")))
                .build();
```
PostJson
```
    private void postJson() {
//        创建OK对象
        OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .connectTimeout(5, TimeUnit.SECONDS)
                .writeTimeout(5, TimeUnit.SECONDS)
                .readTimeout(5, TimeUnit.SECONDS)
                .addInterceptor(new LoggingInterceptor())//应用拦截器
                .addNetworkInterceptor(new LoggingInterceptor())//网络拦截器
                .build();
//        创建请求体
        FoodJsonBean foodJsonBean = new FoodJsonBean();
        foodJsonBean.setPage("1");
        foodJsonBean.setLimit("2");
        foodJsonBean.setStage_id("1");
        String json = new Gson().toJson(foodJsonBean);
        MediaType mediaType = MediaType.parse(formType);
        RequestBody requestBody = RequestBody.create(mediaType, json);

//        创建请求对象
        Request request = new Request.Builder()
                .addHeader("Content-Type", "text/html; charset=UTF-8")
                .header("User-Agent", "David@Angle")
                .url(footUrl)
                .post(requestBody)
                .build();
        Call call = okHttpClient.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                Log.d(TAG, "onFailure: " + e.getMessage());
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                final FoodBean foodBean = new Gson().fromJson(response.body().string(), FoodBean.class);
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        postjson.setText(foodBean.getData().get(0).getTitle());
                        Log.d(TAG, "run: dvhuibd" + foodBean.getData().get(0).getTitle());

                    }
                });
            }
        });

    }

```
PostString
```
    private void postString() {
//        创建OK对象
        OkHttpClient ok = new OkHttpClient.Builder()
                .readTimeout(5, TimeUnit.SECONDS)
                .writeTimeout(5, TimeUnit.SECONDS)
                .connectTimeout(5, TimeUnit.SECONDS)
                .build();
        MediaType parse = MediaType.parse(formType);
        RequestBody requestBody = RequestBody.create(parse, paramas);
        Request request = new Request.Builder()
                .url(footUrl)
                .post(requestBody)
                .addHeader("Content-Type", formType)
                .header("User-Agent", "David")
                .build();
        Call call = ok.newCall(request);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                Log.d(TAG, "onFailure: " + e.getMessage());
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
//                响应行
                Log.d(TAG, "onResponse: " + response.protocol() + response.code() + response.message());
//                响应头
                Headers headers = response.headers();
                for (int i = 0; i < headers.size(); i++) {
                    Log.d(TAG, "onResponse: " + headers.name(i) + headers.value(i));
                }
                String json = response.body().string();
                final FoodBean foodBean = new Gson().fromJson(json, FoodBean.class);
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        postString.setText(foodBean.getData().get(0).getTitle());
                    }
                });
            }
        });
    }

```
PostStream
```
    private void postStream() {
//        创建OK对象
        OkHttpClient okHttpClient = new OkHttpClient.Builder().build();
        RequestBody requestBody = new RequestBody() {
            @Override
            public MediaType contentType() {
                return MediaType.parse(formType);
            }

            @Override
            public void writeTo(BufferedSink sink) throws IOException {
                sink.writeUtf8(paramas);
            }
        };
        Request build = new Request.Builder()
                .url(footUrl)
                .post(requestBody)
                .build();
        Call call = okHttpClient.newCall(build);
        call.enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                Log.d(TAG, "onFailure: " + e.getMessage());
            }

            @Override
            public void onResponse(Call call, Response response) throws IOException {
                final FoodBean foodBean = new Gson().fromJson(response.body().string(), FoodBean.class);
                runOnUiThread(new Runnable() {
                    @Override
                    public void run() {
                        stream.setText(foodBean.getData().get(0).getTitle());
                    }
                });
            }
        });

    }

```

8. 拦截器
![](https://upload-images.jianshu.io/upload_images/21988850-1927fa0edfc26679.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
 拦截器是OkHttp 执行网络请求中的重要角色，贯穿了整个请求执行的过程，是一个强有力的机制，能够监控，重写以及重试（请求的）调用，用户可传入的 interceptor 分为两类：Application Intercetor和NetworkInterceptor

Application interceptors 应用拦截器
 okClient.addInterceptor(new LoggingInterceptor())
Application Interceptor 是第一个 Interceptor 因此它会被第一个执行，因此这里的 request 还是最原始的。而对于 response 而言呢，因为整个过程是递归的调用过程，因此它会在 CallServerInterceptor 执行完毕之后才会将 Response 进行返回，因此在 Application Interceptor 这里得到的 response 就是最终的响应，虽然中间有重定向，但是这里只关心最终的 response
 ①不需要去关心中发生的重定向和重试操作。因为它处于第一个拦截器，会获取到最终的响应
 ②只会被调用一次，即使这个响应是从缓存中获取的。
 ③只关注最原始的请求，不去关系请求的资源是否发生了改变，我只关注最后的 response 结果而已。
 ④因为是第一个被执行的拦截器，因此它有权决定了是否要调用其他拦截，也就是 Chain.proceed() 方法是否要被执行。
 ⑤因为是第一个被执行的拦截器，因此它有可以多次调用 Chain.proceed() 方法，其实也就是相当与重新请求的作用了。

Network Interceptors 网络拦截器
 OKClient.addNetworkInterceptor(new LoggingInterceptor())
NetwrokInterceptor 处于第 6 个拦截器中，它会经过 RetryAndFollowIntercptor 进行重定向并且也会通过 BridgeInterceptor 进行 request 请求头和 响应 resposne 的处理，因此这里可以得到的是更多的信息。在打印结果可以看到它内部是发生了一次重定向操作，所以NetworkInterceptor 可以比 Application Interceptor 得到更多的信息了
 ①因为 NetworkInterceptor 是排在第 6 个拦截器中，因此可以操作经过 RetryAndFollowup 进行失败重试或者重定向之后得到的resposne。
 ②为响应直接从 CacheInterceptor 返回了。
 ③观察数据在网络中的传输。
 ④可以获得装载请求的连接。

二、拦截器的应用

1.日志拦截器

在 LoggingInterceptor 中主要做了 3 件事：
①请求前-打印请求信息；
②网络请求-递归去调用其他拦截器发生网络请求；
③网络响应后-打印响应信息

代码：
```
    OkHttpClient client = builder
                .addInterceptor(new LoggingInterceptor())//应用拦截器
                .addNetworkInterceptor(new LoggingInterceptor())//网络拦截器
                .readTimeout(5, TimeUnit.SECONDS)
                .connectTimeout(5, TimeUnit.SECONDS)
                .writeTimeout(5, TimeUnit.SECONDS)
                .build();

    class LoggingInterceptor implements Interceptor {

        private static final String TAG = "LoggingInterceptor";

        @Override
        public Response intercept(Chain chain) throws IOException {
     
            Request request = chain.request();

            //1.请求前--打印请求信息
            long startTime = System.nanoTime();
            Log.d(TAG, String.format("Sending request %s on %s%n%s",
                    request.url(), chain.connection(), request.headers()));

            //2.网络请求
            Response response =  chain.proceed(request);

            //3.网络响应后--打印响应信息
            long endTime = System.nanoTime();
            Log.d(TAG, String.format("Received response for %s in %.1fms%n%s",
                    response.request().url(), (endTime - startTime) / 1e6d, response.headers()));

            return response;
        }
    }
```
2.缓存拦截器（通过adb命令查看data数据）

在 MyCacheinterceptor 中主要做了（post方式无法缓存）
①设置缓存位置
②无网时：设置缓存协议
③有网：加载网络数据；无网：加载缓存数据

代码：

```
    OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .addInterceptor(myCacheinterceptor)//应用拦截器
                .addNetworkInterceptor(myCacheinterceptor)//网络拦截器
                .connectTimeout(5, TimeUnit.SECONDS)
                .cache(new Cache(new File(getCacheDir(), "Cache"), 1024 * 1024 * 10))
                .build();
       
    class MyCacheinterceptor implements Interceptor {
        @Override
        public Response intercept(Chain chain) throws IOException {
             //1.获取请求数据
            Request request = chain.request();
             //2.判断如果无网时，设置缓存协议
            if (!isNetworkAvailable(MainActivity.this)) {
                request = request.newBuilder().cacheControl(CacheControl.FORCE_CACHE).build();

            }
            //3.开始请求网络，获取响应数据
            Response originalResponse = chain.proceed(request);
            //4.判断是否有网
            if (isNetworkAvailable(MainActivity.this)) {、
                //有网，获取网络数据
                int maxAge = 0;
                return originalResponse.newBuilder()
                        .removeHeader("Pragma")
                        .header("Cache-Control", "public ,max-age=" + maxAge)
                        .build();
            } else {
                //没有网络，获取缓存数据
                int maxStale = 15*60;
                return originalResponse.newBuilder()
                        .removeHeader("Pragma")
                        .header("Cache-Control", "public, only-if-cached, max-stale=" + maxStale)
                        .build();
            }

        }
    }

    /**
        检测是否有网
     */
    public static boolean isNetworkAvailable(Context context) {
        if (context != null) {
            ConnectivityManager cm = (ConnectivityManager) context.getSystemService(Context.CONNECTIVITY_SERVICE);
            NetworkInfo info = cm.getActiveNetworkInfo();
            if (info != null) {
                return info.isAvailable();
            }
        }
        return false;
    }
```
9. 注意事项

>①推荐让 OkHttpClient 保持单例，用同一个 OkHttpClient 实例来执行你的所有请求，因为每一个 OkHttpClient 实例都拥有自己的连接池和线程池，重用这些资源可以减少延时和节省资源，如果为每个请求创建一个 OkHttpClient 实例，显然就是一种资源的浪费。
②response.body().string()只调用一次
③每一个Call（其实现是RealCall）只能执行一次，否则会报异常
⑤子线程加载数据后，主线程刷新数据
10. HttpURLconnection及OkHttp3的对比分析
>1.HttpUrlConnection，google官方提供的用来访问网络，但是实现的比较简单，只支持1.0/1.1
2.并没有多路复用，如果碰到app大量网络请求的时候，性能比较差，
3.HttpUrlConnection底层也是用Socket来实现的
4.OkHttp像HttpUrlConnection一样，实现了一个网络连接的过程。
5.OkHttp和HttpUrlConnection是一级的，用socket实现了网络连接，OkHttp更强大，
6.HttpUrlConnection在IO方面用到的是InputStream和OutputStream，但OkHttp用的是sink和source，这两个是在Okio这个开源库里的， feredSink(支持缓冲)、GzipSink（支持Gzip压缩）、ForwardingSink和InflaterSink（后面这两者服务于GzipSink）
7.多个相同host和port的stream可以共同使用一个socket，而RealConnection就是处理连接的，那也就是说一个RealConnection上可能有很多个Stream
8.OkHttp代码比HttpURLConnection精简的多
11. OkHttp源码分析（设计模式，线程池的使用）
![](https://upload-images.jianshu.io/upload_images/21988850-85723d98749401b0.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
  [设计模式](https://links.jianshu.com/go?to=https%3A%2F%2Fblog.csdn.net%2Fqq_23012315%2Farticle%2Fdetails%2F82492165)

   `[线程池]`
  ` 源码分析 new OkHttpClient`

>dispatcher：调度器
proxy：代理
protocols：协议
interceptors：拦截器集合
cache：

okHttpClient.newCall

RealCall.newRealCall

四、单例、多线程安全问题

```
/**
  饿汉式
 */
public class Person {

    //1.构造函数私有化
    private Person(){}

    //2.创建单个私有对象对象
    private static  Person person = new Person();

    //3.提供对外公开访问方法
    public static Person getPerson() {
        return person;
    }
}

/**
 * 懒汉式
 *
 * 多线程安全问题：
 *      1.多个线程同时操作
 *      2.共用同一个资源
 *      3.分步执行操作同一个资源
 *
 * 多线程安全解决方式：
 *      1.同步方法
 *      2.同步代码块
 *      3.锁机制
 */
public class Student {

    //1.构造函数私有化
    private Student(){}

    //2.创建单个私有对象对象
    private static  Student student;

    //3.提供对外公开访问方法
    public static Student getInstance(){

        //解决线程安全问题
        if (student == null){
            synchronized (Student.class){
                if (student == null) {
                    student = new Student();
                }
            }
        }

        return student;
    }
}
```

五、OkHttpUtils工具类抽取

```
public class OkHttpUtils {

    private static OkHttpUtils okHttpUtils;
    private static OkHttpClient okHttpClient;
    private static Handler mHandler;

    /**
     * 构造初始化
     */
    private OkHttpUtils(){
        /**
         * 构建OkHttpClient
         */
        okHttpClient = new OkHttpClient.Builder()
        /**
         * 请求的超时时间
         */
        .readTimeout(5000, TimeUnit.MILLISECONDS)
        /**
         * 设置响应的超时时间
         */
        .writeTimeout(5000, TimeUnit.MILLISECONDS)
        /**
         * 设置连接的超时时间
         */
        .connectTimeout(5000, TimeUnit.MILLISECONDS)
        /**
         * 构建
         */
        .build();


        /**
         * 获取主线程的handler
         */
        mHandler = new Handler(Looper.getMainLooper());
    }

    /**
     * 通过单例模式构造对象
     * @return
     */
    public static OkHttpUtils getInstance(){
        if (OkHttpUtils == null){
            synchronized (OkHttpUtils.class){
                if (okHttpUtils == null){
                    okHttpUtils = new OkHttpUtils();
                }
            }
        }
        return okHttpUtils;
    }

    /**
     * 构造Get请求，封装对用的Request请求，实现方法
     * @param url  访问路径
     * @param realCallback  接口回调
     */
    private void getRequest(String url, final RealCallback realCallback){

        Request request = new Request.Builder().url(url).get().build();
        deliveryResult(realCallback, okHttpClient.newCall(request));
    }

    /**
     * 构造post 请求，封装对用的Request请求，实现方法
     * @param url 请求的url
     * @param requestBody 请求参数
     * @param realCallback 结果回调的方法
     */
    private void postRequest(String url, RequestBody requestBody, final RealCallback realCallback){

        Request request = new Request.Builder().url(url).post(requestBody).build();
        deliveryResult(realCallback, okHttpClient.newCall(request));
    }

    /**
     * 处理请求结果的回调：主线程切换
     * @param realCallback
     * @param call
     */
    private void deliveryResult(final RealCallback realCallback, Call call) {
        call.enqueue(new Callback() {
            @Override
            public void onFailure(final Call call, final IOException e) {
                sendFailureThread(call, e, realCallback);
            }

            @Override
            public void onResponse(final Call call, final Response response) throws IOException {
                sendSuccessThread(call, response, realCallback);
            }
        });
    }

    /**
     * 发送成功的回调
     * @param call
     * @param response
     * @param realCallback
     */
    private void sendSuccessThread(final Call call, final Response response, final RealCallback
            realCallback) {
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                realCallback.onResponse(call,response);
            }
        });
    }

    /**
     * 发送失败的回调
     * @param call
     * @param e
     * @param realCallback
     */
    private void sendFailureThread(final Call call, final IOException e, final RealCallback realCallback) {
        mHandler.post(new Runnable() {
            @Override
            public void run() {
                realCallback.onFailure(call,e);
            }
        });
    }

    //-----------对外公共访问的get/post方法-----------
    /**
     * get请求
     * @param url  请求url
     * @param realCallback  请求回调
     */
    public void get(String url, final RealCallback realCallback){
        getRequest(url,realCallback);
    }

    /**
     * post请求
     * @param url       请求url
     * @param realCallback  请求回调
     * @param requestBody    请求参数
     */
    public void post(String url, RequestBody requestBody, final RealCallback realCallback){
        postRequest(url,requestBody,realCallback);
    }

    /**
     * http请求回调类,回调方法在UI线程中执行
     */
    public static abstract class RealCallback {
        /**
         * 请求成功回调
         * @param response
         */
        public abstract void onResponse(Call call,Response response);
        /**
         * 请求失败回调
         * @param e
         */
        public abstract void onFailure(Call call,IOException e);
    }
 ```

## Retrofit2

1.Retrofit2概述

Retrofit框架是Square公司出品的目前非常流行的网络框架.
效率高，实现简单，运用注解和动态代理.
极大简化了网络请求的繁琐步骤，非常适合REST ful网络请求.
目前Retofit版本是2

Retrofit其实我们可以理解为OkHttp的加强版。
它也是一个网络加载框架。底层是使用OKHttp封装的。
准确来说,网络请求的工作本质上是OkHttp完成，而 Retrofit 仅负责网络请求接口的封装。
它的一个特点是包含了特别多注解，方便简化你的代码量。
并且还支持很多的开源库(著名例子：Retrofit + RxJava)

2.Retrofit2的好处

1.  超级解耦
    解耦？解什么耦？
    我们在请求接口数据的时候，API接口定义和API接口使用总是相互影响，什么传参、回调等，耦合在一块。有时候我们会考虑一下怎么封装我们的代码让这两个东西不那么耦合，这个就是Retrofit的解耦目标，也是它的最大的特点。
2.  可以配置不同HttpClient来实现网络请求，如OkHttp、HttpClient...
3.  支持同步、异步和RxJava
4.  可以配置不同的反序列化工具来解析数据，如json、xml...
5.  请求速度快，使用非常方便灵活

3.Retrofit2配置

1.  [官网](https://links.jianshu.com/go?to=http%3A%2F%2Fsquare.github.io%2Fretrofit%2F)
2.  依赖：
    `implementation 'com.squareup.retrofit2:retrofit:2.5.0'`
3.  添加网络权限：`<uses-permission android:name="android.permission.INTERNET" />`

4，Retrofit2的使用步骤

>定义接口类（封装URL地址和数据请求）
实例化Retrofit
通过Retrofit实例创建接口服务对象
接口服务对象调用接口中的方法，获取Call对象
Call对象执行请求（异步、同步请求）

6.Retrofit2发送GET、POST请求(异步、同步)

Retrofit2发送GET
```
//主机地址
String url = "http://apicloud.mob.com/appstore/health/search?key=1ac78a8602d58&name=转氨酶";
    String BASE_URL = "http://apicloud.mob.com/appstore/health/";//必须以反斜杠结尾

    //接口服务
    public interface BigFlyServer {
        
        //GET
        @GET("search?key=1ac78a8602d58&name=转氨酶")
        Call<ResponseBody> getData1();
    
        @GET("search?")
        Call<ResponseBody> getData2(@Query("key") String key,@Query("name") String key);
    
        @GET("search?")
        Call<ResponseBody> getData3(@QueryMap Map<String,Object> map);
    }


//Get异步
private void initGetEnqueue() {

    //1.创建Retrofit对象
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(MyServer.URL)
                .build();
        
        //2.获取MyServer接口服务对象
        MyServer myServer = retrofit.create(MyServer.class);
        
        //3.获取Call对象
        //方式一
        Call<ResponseBody> call1 = myServer.getData1();

        //方式二
        Call<ResponseBody> call2 = myServer.getData2("908ca46881994ffaa6ca20b31755b675");

        //方式三
        Map<String,Object> map = new HashMap<>();
        map.put("appKey","908ca46881994ffaa6ca20b31755b675");
        Call<ResponseBody> call = myServer.getData3(map);

        //4.Call对象执行请求
        call.enqueue(new Callback<ResponseBody>() {

            @Override
            public void onResponse(Call<ResponseBody> call,Response<ResponseBody> response) {

                try {
                    String result = response.body().string();

                    Log.e("retrofit", "onResponse: "+result);
                    tv.setText(result);//默认直接回调主线程
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }

            @Override
            public void onFailure(Call<ResponseBody> call, Throwable t) {

                Log.e("retrofit", "onFailure: "+t.getMessage());
            }
        });
    }


    //GET同步
    private void initGetExecute() {

        //1.创建Retrofit对象
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(MyServer.URL)
                .build();

        //2.获取MyServer接口服务对象
        MyServer myServer = retrofit.create(MyServer.class);

        //3.获取Call对象
        final Call<ResponseBody> call = myServer.getData1();

        new Thread(){//子线程执行
            @Override
            public void run() {
                super.run();

                try {
        //4.Call对象执行请求
                    Response<ResponseBody> response = call.execute();

                    final String result = response.body().string();

                    Log.e("retrofit", "onResponse: "+result);

                    runOnUiThread(new Runnable() {
                        @Override
                        public void run() {
                            tv.setText(result);
                        }
                    });
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }.start();

    }
```
2.Retrofit2发送POST
```
String URL = "http://apicloud.mob.com/appstore/health/";//必须以反斜杠结尾

public interface MyServer {
    
//POST("search?")    POST("search")相同
    @POST("search?")
    @FormUrlEncoded
    Call<ResponseBody> postData1(@Field("key") String appKey,@Field("name") String appKey);

    @POST("search")  
    @FormUrlEncoded
    Call<ResponseBody> postData2(@FieldMap Map<String,Object> map);
}

//POST异步
private void initPostEnqueue() {

    //1.创建Retrofit对象
    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(MyServer.URL)
            .build();

    //2.获取MyServer接口服务对象
    MyServer myServer = retrofit.create(MyServer.class);

    //3.获取Call对象
    //方式一
    Call<ResponseBody> call1 = myServer.postData1("908ca46881994ffaa6ca20b31755b675");

    //方式二
    Map<String,Object> map = new HashMap<>();
    map.put("appKey","908ca46881994ffaa6ca20b31755b675");
    Call<ResponseBody> call = myServer.postData2(map);

    //4.Call对象执行请求
    call.enqueue(new Callback<ResponseBody>() {
        @Override
        public void onResponse(Call<ResponseBody> call,Response<ResponseBody> response) {

            try {
                String result = response.body().string();

                Log.e("retrofit", "onResponse: "+result);
                tv.setText(result);//默认直接回调主线程
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onFailure(Call<ResponseBody> call, Throwable t) {

            Log.e("retrofit", "onFailure: "+t.getMessage());
        }
    });
}
```
7.Retrofit注解
```
注解代码       请求格式

请求方式：
    @GET           GET请求
    @POST          POST请求
    @DELETE        DELETE请求
    @HEAD          HEAD请求
    @OPTIONS       OPTIONS请求
    @PATCH         PATCH请求

请求头：
    @Headers("K:V") 添加请求头，作用于方法
    @Header("K")    添加请求头，参数添加头
    @FormUrlEncoded 用表单数据提交，搭配参数使用
    @Stream         下载
    @Multipart      用文件上传提交   multipart/form-data

请求参数：
    @Query          替代参数值，通常是结合get请求的
    @QueryMap       替代参数值，通常是结合get请求的
    @Field          替换参数值，是结合post请求的
    @FieldMap       替换参数值，是结合post请求的

请求路径：
    @Path           替换路径
    @Url            路径拼接

请求体：
    @Body(RequestBody)  设置请求体，是结合post请求的

文件处理：
    @Part Multipart.Part
    @Part("key") RequestBody requestBody(单参)
    @PartMap Map<String,RequestBody> requestBodyMap(多参)
    @Body RequestBody requestBody(自定义参数)
```
8.Retrofit2其他常用注解使用
```
String URL = "http://api.shujuzhihui.cn/api/news/";//必须以反斜杠结尾

public interface MyServer {
    
    //Path
    @GET("wages/{wageId}/detail") 
    Call<ResponseBody >getImgData(@Path("wageId") String wageId);

    //Url
    @GET
    Call<ResponseBody >getImgData(@Url String url);

    @GET
    Call<ResponseBody >getImgData(@Url String url,@Query("appKey") String appkey);

    
    //Json
    @POST("categories")
    @Headers("Content-Type:application/json")
    Call<ResponseBody> getFormDataJson(@Body RequestBody requestBody);

    //Form
    @POST("categories")
    @Headers("Content-Type:application/x-www-form-urlencoded")
    Call<ResponseBody> getFormData1(@Body RequestBody requestBody);

    //复用URL
    @POST()
    @Headers("Content-Type:application/x-www-form-urlencoded")
    Call<ResponseBody> getFormData2(@Url String url,@Body RequestBody requestBody);

    //通用
    @POST()
    Call<ResponseBody> getFormData3(@Url String url, @Body RequestBody requestBody, @Header("Content-Type") String contentType);
}

//RequestBody参数拼接
private void initPostBody() {

    Retrofit.Builder builder = new Retrofit.Builder();
    Retrofit retrofit = builder.baseUrl(MyServer.URL)
            .build();

    MyServer myServer = retrofit.create(MyServer.class);

    //Json类型
    String json1 = "{\n" + "    \"appKey\": \"908ca46881994ffaa6ca20b31755b675\"\n" +  "}";
    RequestBody requestBody01 = RequestBody.create(MediaType.parse("application/json,charset-UTF-8"),json1);
    Call<ResponseBody> call01 = myServer.getFormDataJson(requestBody01);

    //String类型
    //有参形式
    RequestBody requestBody02 = RequestBody.create(MediaType.parse("application/x-www-form-urlencoded,charset-UTF-8"),"appKey=908ca46881994ffaa6ca20b31755b675");
    Call<ResponseBody> call02 = myServer.getFormData1(requestBody02);

    //无参形式
    RequestBody requestBody3 = RequestBody.create(MediaType.parse("application/x-www-form-urlencoded,charset-UTF-8"),"");
    Call<ResponseBody> call03 = myServer.getFormData1(requestBody3);

    //RequestBody （Form表单，键值对参数形式）
    //方式一(requestBody)
    FormBody requestBody = new FormBody.Builder()
            .add("appKey","908ca46881994ffaa6ca20b31755b675")
            .build();
    Call<ResponseBody> call1 = myServer.getFormData1(requestBody);

    //方式二(url+requestBody)
    FormBody requestBody = new FormBody.Builder()
            .add("appKey","908ca46881994ffaa6ca20b31755b675")
            .build();
    Call<ResponseBody> call2 = myServer.getFormData2("categories",requestBody);

    //方式三(url+requestBody+header)
    FormBody requestBody = new FormBody.Builder()
            .add("appKey","908ca46881994ffaa6ca20b31755b675")
            .build();
    Call<ResponseBody> call3 = myServer.getFormData3("categories",requestBody,"application/x-www-form-urlencoded");


    call3.enqueue(new Callback<ResponseBody>() {
        @Override
        public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {

            try {
                String result = response.body().string();

                Log.e("retrofit", "onResponse: "+result);
                tv.setText(result);//默认直接回调主线程
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onFailure(Call<ResponseBody> call, Throwable t) {

            Log.e("retrofit", "onFailure: "+t.getMessage());
        }
    });
}
```
9.数据解析器（Converter）

Retrofit支持多种数据解析方式
使用时需要在Gradle添加依赖
Gson：com.squareup.retrofit2:converter-gson:2.0.2
Jackson：com.squareup.retrofit2:converter-jackson:2.0.2
```
Retrofit retrofit = new Retrofit.Builder()
                .addConverterFactory(GsonConverterFactory.create())
                .baseUrl(MyService.URL)
                .build();
```
10.网络请求适配器（CallAdapter）

Retrofit支持多种网络请求适配器方式：guava、Java8和rxjava
guava：com.squareup.retrofit2:adapter-guava:2.0.2
Java8：com.squareup.retrofit2:adapter-java8:2.0.2
rxjava：com.squareup.retrofit2:adapter-rxjava:2.0.2
11.Retrofit2文件上传
```
String FileUpLoadURL = "http://yun918.cn/study/public/";

public interface MyServer {
    
 @POST("file_upload.php")
    @Multipart
    Call<ResponseBody> upLoadFile1(@Part MultipartBody.Part part,@Part("key") RequestBody requestBody);//单参

    @POST("file_upload.php")
    @Multipart
    Call<ResponseBody> upLoadFile2(@Part MultipartBody.Part part, @PartMap Map<String,RequestBody> requestBodyMap); //多参

    @POST("file_upload.php")
    @Multipart
    Call<ResponseBody> upLoadFile3(@Body RequestBody requestBody);  //自定义
}

//文件上传
private void initPostFile() {

    Retrofit retrofit = new Retrofit.Builder()
            .baseUrl(MyServer.FileUpLoadURL)
            .build();

    MyServer myServer = retrofit.create(MyServer.class);

    Call<ResponseBody> call = null;

    //上传文件路径
    File file = new File("/sdcard/Pictures/Screenshorts/dd.png");
    if(file.exists()){

        //方式一
        RequestBody requestBody1 = RequestBody.create(MediaType.parse("image/png"),file);

        MultipartBody.Part part = MultipartBody.Part.createFormData("file", file.getName(), requestBody1);

        RequestBody responseBody2 = RequestBody.create(MediaType.parse("multipart/form-data"),"abc");

        call = myServer.upLoadFile1(part, responseBody2);

        //方式二
        MultipartBody.Builder builder1 = new MultipartBody.Builder().setType(MultipartBody.FORM);

        MultipartBody body = builder1.addFormDataPart("key", "abc")
                .addFormDataPart("file", file.getName(), RequestBody.create(MediaType.parse("image/png"),file))
                .build();

        call = myServer.upLoadFile3(body);

        //方式三
        MultipartBody.Part part2 = MultipartBody.Part.createFormData("file", file.getName(), RequestBody.create(MediaType.parse("image/png"),file));

        Map<String, RequestBody> map = new HashMap<>();
        RequestBody responseBody3 = RequestBody.create(MediaType.parse("multipart/form-data"),"abc");
        map.put("key",responseBody3);

        call = myServer.upLoadFile2(part2, map);

    }

    call.enqueue(new Callback<ResponseBody>() {
        @Override
        public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
            try {
                String result = response.body().string();

                Log.e("retrofit", "onResponse: "+result);
                tv.setText(result);//默认直接回调主线程
            } catch (IOException e) {
                e.printStackTrace();
            }
        }

        @Override
        public void onFailure(Call<ResponseBody> call, Throwable t) {
            Log.e("retrofit", "onFailure: "+t.getMessage());
        }
    });
}
```
12.Form表单语法

1、Form表单语法

>在Form元素的语法中，EncType表明提交数据的格式 用 Enctype 属性指定将数据回发到服务器时浏览器使用的编码类型。
1，application/x-www-form-urlencoded： 窗体数据被编码为名称/值对。这是标准的编码格式。
2，multipart/form-data： 窗体数据被编码为一条消息，页上的每个控件对应消息中的一个部分，这个一般文件上传时用。
3，text/plain： 窗体数据以纯文本形式进行编码，其中不含任何控件或格式字符。

2、常用的编码方式

>form的enctype属性为编码方式，常用有两种：
application/x-www-form-urlencoded（默认）
multipart/form-data
1.x-www-form-urlencoded
浏览器用x-www-form-urlencoded的编码方式把form数据转换成一个字串（name1=value1&name2=value2…）
2.multipart/form-data
浏览器把form数据封装到http body中，然后发送到server。如果没有type=file的控件，用默认的application/x-www-form-urlencoded就可以了。但是如果type=file的话，就要用到multipart/form-data了。

11.Retrofit2及OkHttp3的区别

>Retrofit2使用注解设置请求内容
Retrofit2回调主线程，OkHttp3回调子线程
Retrofit2可以做数据解析转换
Retrofit2可以使用在REST ful网络请求.

12.Retrofit2源码分析（底层OkHttp3，注解了解，反射了解）

>构建者构建
动态代理、反射
注解配置请求
底层基于OkHttpCall调用

源码解析：
1.点击builder，平台选择（platform）、获取工厂、baseURL
```
platform = Platform.get();
callFactory = retrofit.callFactory;
baseUrl = retrofit.baseUrl;
```
2.点击build方法
```
 if (baseUrl == null) {
        throw new IllegalStateException("Base URL required.");
      }

//可以选择客户端，如果传的callFactory是空，默认创建一个ok  
okhttp3.Call.Factory callFactory = this.callFactory;
      if (callFactory == null) {
        callFactory = new OkHttpClient();
      }

//线程池，主要做线程切换
Executor callbackExecutor = this.callbackExecutor;
      if (callbackExecutor == null) {
        callbackExecutor = platform.defaultCallbackExecutor();
      }

 // 添加一个网络切换器
      List<CallAdapter.Factory> callAdapterFactories = new ArrayList<>(this.callAdapterFactories);
      callAdapterFactories.addAll(platform.defaultCallAdapterFactories(callbackExecutor));

// 添加一个数据切换工厂
List<Converter.Factory> converterFactories = new ArrayList<>(
          1 + this.converterFactories.size() + platform.defaultConverterFactoriesSize());

//最后添加完工厂返回一个retrofit对象
`
return new Retrofit(callFactory, baseUrl, unmodifiableList(converterFactories),
          unmodifiableList(callAdapterFactories), callbackExecutor, validateEagerly);
```

MyService myService = retrofit.create(MyService.class);
用到了动态代理（Proxy.newProxyInstance）及反射

```

Proxy.newProxyInstance获取动态代理单例，里边有三个参数，
第一个是代理对象，第二个参数是调用的方法，第三个是方法的参数
return (T) Proxy.newProxyInstance(service.getClassLoader(), new Class<?>[] { service },
        new InvocationHandler() {
          private final Platform platform = Platform.get();
          private final Object[] emptyArgs = new Object[0];

          @Override public Object invoke(Object proxy, Method method, @Nullable Object[] args)
              throws Throwable {
            // If the method is a method from Object then defer to normal invocation.
            if (method.getDeclaringClass() == Object.class) {
              return method.invoke(this, args);
            }
            if (platform.isDefaultMethod(method)) {
              return platform.invokeDefaultMethod(method, service, proxy, args);
            }
            return loadServiceMethod(method).invoke(args != null ? args : emptyArgs);
          }
        });


 ServiceMethod<?> loadServiceMethod(Method method) {
    ServiceMethod<?> result = serviceMethodCache.get(method);
    if (result != null) return result;

    synchronized (serviceMethodCache) {
      result = serviceMethodCache.get(method);
      if (result == null) {
        result = ServiceMethod.parseAnnotations(this, method);
        serviceMethodCache.put(method, result);
      }
    }
    return result;
  }


result = ServiceMethod.parseAnnotations(this, method);
进方法里边看，RequestFactory requestFactory = RequestFactory.parseAnnotations(retrofit, method);
工厂模式，看工厂是这么build出来的
遍历注解


ServiceMethod中返回的东西

HttpServiceMethod

 @Override ReturnT invoke(Object[] args) {
    return callAdapter.adapt(
        new OkHttpCall<>(requestFactory, args, callFactory, responseConverter));
  }


OkHttpCall----enqueue-- response = parseResponse(rawResponse);

T body = responseConverter.convert(catchingBody);

```

## GreenDao

前言：数据库：MySQL、Oracle、Sqlite

一. 复习SQL语句(结构化查询语言)

1.SQL语句分类
DDL数据定义语言
>用于创建、修改、和删除数据库内的数据结构，如：
1.创建和删除数据库(CREATE DATABASE || DROP DATABASE)；
2.创建、修改、重命名、删除表(CREATE TABLE || ALTER TABLE|| RENAME TABLE||DROP TABLE)；
3.创建和删除索引(CREATEINDEX || DROP INDEX)

DML数据操作语言
>修改数据库中的数据，包括插入(INSERT)、更新(UPDATE)和删除(DELETE)

DCL数据控制语言
>用于对数据库的访问，如：1：给用户授予访问权限（GRANT）;2：取消用户访问权限（REMOKE）

DQL数据查询语言

>从数据库中的一个或多个表中查询数据(SELECT)

2.SQL语句

>(1)库
create database aaa; //创建数据库
drop database aaa; //删除数据库
use aaa; //切换使用数据库
show databases; //显示数据库
(2)表
create table abc(name text,sex varchar(100),age int(10));//创建表结构
drop table abc; //删除表
desc abc; //显示表结构
(3)数据
insert into abc(name,sex,age) values('abc','nan',20); //插入数据
update abc set age = 101 【where name = 'abc'】 //更改数据
delete from abc 【where name = 'abc'】 //删除数据
select * from abc 【where age > 18】 //查询数据
(4）约束
主键： primary key
自增： auto_increment
非空： not null
唯一： unique

```
create table abc(
    id  int primary key auto_increment,    主键自增
    name varchar not null unique,           非空唯一
    sex varchar,
);
```
(5）查询
一、基本查询
```
select * from emp;
select empno,ename,sal from emp;
select distinct deptno from emp;    
select sal*1.5 from emp;
select concat('$',sal) from emp;
select concat(sal,'RMB') from emp;
select ifnull(comm,0)+1000 from emp;
select sal as 奖金 from emp;
```

二、条件查询
```
        select * from emp where deptno = 20;
        select * from emp where deptno != 20;
        select * from emp where sal >=20000;
        select * from emp where sal >=10000 and sal <=20000;
        select * from emp where sal<=10000 or sal >=40000;
        select * from emp where comm is null;
        select * from emp where comm is not null;
        select * from emp where sal between 20000 and 40000;
        select * from emp where deptno in(10,30);
```
三、模糊查询 _某一个字符 %多个字符
```
        select * from emp where ename like '张_';
        select * from emp where ename like '张%';
        select * from emp where ename like '_一_';
```
四、排序
```
        select * from emp order by sal asc;
        select * from emp order by sal desc;
```
五、聚合函数
```
        select max(sal) from emp;
        select min(sal) from emp;
        select count(ename) from emp;
        select sum(sal) from emp;
        select avg(sal) from emp;
```
六、分组
-
`select deptno,count(ename) from emp group by deptno;`

总结：
```
select deptno,count(ename)
        from emp
        where sal >= 10000
        group by deptno
        order by deptno asc;
```
二.复习SQLliteDatabase

SQLiteDatabase的创建和实现的方法
SQLiteOpenHelper的使用
onCreate的调用机制，onUpgrade的调用机制；建库、建表
增删改查（使用sql语句方式）
增删改查（使用系统方法）

 三.GreenDao的概述以及特点

*   概述
    ①基于对象关系的映射方式来操作数据库的框架，提供一个接口通过操作对象的方式操作数据库
    ② 适用于 Android 的ORM 框架，现在市面上主流的框架有 OrmLite、SugarORM、Active Android、Realm 与 GreenDAO。
    ③GreenDAO是一种Android数据库ORM(对象映射关系（Object Relation Mapping）)框架，与OrmLite、ActiveOrm、LitePal等数据库相比，单位时间内可以插入、更新和查询更多的数据，而且提供了大量的灵活通用接口。
*   特点
    ①通常我们在使用GreenDao的时候，我们只需定义数据模型，GreenDao框架将创建数据对象(实体)和DAO(数据访问对象)，能够节省部分代码。
    ②不向性能妥协，使用了GreenDao，大多数实体可以以每秒几千个实体的速率进行插入，更新和加载。
    ③GreenDao支持加密数据库来保护敏感数据。
    ④微小的依赖库，GreenDao的关键依赖库大小不超过100kb.
    ⑤如果需要，实体是可以被激活的。而活动实体可以透明的解析关系（我们要做的只是调用getter即可），并且有更新、删除和刷新方法，以便访问持久性功能。
    ⑥GreenDao允许您将协议缓冲区(protobuf)对象直接保存到数据库中。如果您通过protobuf通话到您的服务器，则不需要另一个映射。常规实体的所有持久性操作都可以用于protobuf对象。所以，相信这是GreenDao的独特之处。
    ⑦自动生成代码，我们无需关注实体类以及Dao,因为GreenDao已经帮我们生成了。
    ⑧开源 有兴趣的同学可以查看源码，深入去了解机制。

 四.GreenDao配置依赖

*   [官网](https://links.jianshu.com/go?to=https%3A%2F%2Fgithub.com%2Fgreenrobot%2FgreenDAO%2F)

*   配置:

1.  工程配置:添加插件 更好支持GreenDao

```
buildscript {
    repositories {
    jcenter()
    mavenCentral() // 添加的代码
     }
    dependencies {
     classpath 'com.android.tools.build:gradle:2.3.3'
    classpath 'org.greenrobot:greendao-gradle-plugin:3.2.2' // add plugin
            }
}
```

2.项目配置:添加插件
```
apply plugin: 'com.android.application'
apply plugin: 'org.greenrobot.greendao' // apply plugin
```
3.项目配置:添加依赖
```
dependencies {
//greendao
implementation 'org.greenrobot:greendao:3.2.2' // add library
}
```
4.初始化GreenDao配置
```
//放在Android下
greendao{
    schemaVersion 1 //数据库版本号
    daoPackage 'com.example.liwentuo.dao'  //数据库全路径
    targetGenDir 'src/main/java'  //存放位置
 }
    
schemaVersion--> 指定数据库schema版本号，迁移等操作会用到;
daoPackage --> dao的包名，包名默认是entity所在的包；
targetGenDir --> 生成数据库文件的目录;
```

五.GreenDao的使用思路

配置文件中的设置
设置Green中的DaoMaster/DaoSession/实体类Dao生成路径
设置实体类
```
    @Entity
    public class Student {
    
        @Id(autoincrement = true)
        private Long id;
    
        @Property
        @NotNull
        private String name;
    
        @Property
        private int age;
    }
```
4.文件生成：`Build - > ReBuild Project`
在Application类中完成内容配置(或者使用工具类)

5.Application有自己的生命周期，OnCreate方法必须首先被调用
①本类对象的获取
②DaoMaster、DaoSession对象的获取
③提供方法，获取DaoSession对象

>需要注意：必须在AndroidManifest.xml文件完成配置
```
<application
android:name=".App">
</application>
```

6.获取实体类Dao对象
XXXDao xxxdao = App.getInstance().getDaoSession().getXXXDao();
xxxdao.增删改查();
六.GreenDao注解
```
@Entity   标识实体类，greenDAO会映射成sqlite的一个表，表名为实体类名的大写形式
@Id 标识主键，该字段的类型为long或Long类型，autoincrement设置是否自动增长
@Property       标识该属性在表中对应的列名称, nameInDb设置名称
@Transient      标识该属性将不会映射到表中，也就是没有这列
@NotNull         设置表中当前列的值不可为空
@Convert         指定自定义类型(@linkPropertyConverter)
@Generated 运行所产生的构造函数或者方法，被此标注的代码可以变更或者下次运行时清除
@Index    使用@Index作为一个属性来创建一个索引；
@JoinEntity     定义表连接关系
@JoinProperty         定义名称和引用名称属性关系
@Keep     注解的代码段在GreenDao下次运行时保持不变
@OrderBy        指定排序方式
@ToMany         定义与多个实体对象的关系
@ToOne  定义与另一个实体（一个实体对象）的关系
@Unique 向数据库列添加了一个唯一的约束

/**
 * @Entity
 * @Id(autoincrement = true)  标志主键
 * @NotNull 标志这个字段不能是null
 * @Property(nameInDb = "User")
 * @Transient 表示不存储在数据库中
 * @Index(unique = true)
 * @Unique 用于标志列的值的唯一性。
 */
```
七.GreenDao对外提供的核心类简介

```
1，DaoMaster
DaoMaster保存数据库对象（SQLiteDatabase）并管理特定模式的Dao类。它具有静态方法来创建表或将他们删除。其内部类OpenHelper和DevOpenHelper是在SQLite数据库中创建模式的SQLiteOpenHelper实现。

2，DaoSession
管理特定模式的所有可用Dao对象，您可以使用其中一个getter方法获取。DaoSession还为实体提供了一些通用的持久性方法，如插入，加载，更新，刷新和删除。最后，DaoSession对象也跟踪一个身份范围。

3，XXXDao层
数据访问对象(Dao)持续存在并查询实体。对于每个实体，GreenDao生成一个Dao,它比DaoSession有更多的持久化方法，例如：count,loadAll和insertInTx。

4，实体
持久对象，通常实体是使用标准Java属性(如POJO或JavaBean)来表示数据库的对象。



1、DevOpenHelper：创建SQLite数据库的SQLiteOpenHelper的具体实现。 
2、DaoMaster：GreenDao的顶级对象，作为数据库对象、用于创建表和删除表。 
3、DaoSession：管理所有的Dao对象，Dao对象中存在着增删改查等API。
```
八.GreenDao使用

1.核心使用步骤
```
//1.创建数据库
DaoMaster.DevOpenHelper devOpenHelper = new DaoMaster.DevOpenHelper(MyApp.getMyApp(), "student.db");

//2.获取读写对象
DaoMaster daoMaster = new DaoMaster(devOpenHelper.getWritableDatabase());

//3.获取管理器类
DaoSession daoSession = daoMaster.newSession();

//4.获取表对象
 studentDao = daoSession.getStudentDao();


daoSession.clear();  清除整个session，没有缓存对象返回
```
2.完整使用步骤（Application）
```
public class App extends Application {

    private StudentDao studentDao;//Dao操作类

    private static App app;

    public static App getInstance() {
        return app;
    }

    @Override
    public void onCreate() {
        super.onCreate();
        //本类对象
        app = this;

        //创建数据库以及创建数据表
        createDataBase();
    }

    private void createDataBase() {
        //1.创建数据库
        DaoMaster.DevOpenHelper helper = new DaoMaster.DevOpenHelper(this, "student.db");

        //2.获取读写对象
        DaoMaster daoMaster = new DaoMaster(helper.getWritableDatabase());
        
        //3.获取管理器类
        DaoSession daoSession = daoMaster.newSession();

        //4.获取表对象
        studentDao = daoSession.getStudentDao();
    }

    public StudentDao getStudentDao() {
        return studentDao;
    }
}
```
2.完整使用步骤（工具类）
注册app
```
<application android:name=".MyApp" ></application>
```
```
public class MyApp extends Application {

    private static MyApp myApp;

    @Override
    public void onCreate() {
        super.onCreate();

        myApp = this;
    }

    public static MyApp getMyApp() {
        return myApp;
    }
}
```
创建自定义工具类
```

public class MyDatabaseHelper {

    public StudentDao studentDao;//Dao操作类

    private static MyDatabaseHelper myDatabaseHelper;

    //创建生成数据库
    private MyDatabaseHelper(){

        //1.创建数据库
        DaoMaster.DevOpenHelper devOpenHelper = new DaoMaster.DevOpenHelper(MyApp.getMyApp(), "student.db");

        //2.获取读写对象
        DaoMaster daoMaster = new DaoMaster(devOpenHelper.getWritableDatabase());

        //3.获取管理器类
        DaoSession daoSession = daoMaster.newSession();

        //4.获取表对象
         studentDao = daoSession.getStudentDao();
    }

    public static MyDatabaseHelper getMyDatabaseHelper() {
        if(myDatabaseHelper == null){
            synchronized (MyDatabaseHelper.class){
                if (myDatabaseHelper ==null){
                    myDatabaseHelper = new MyDatabaseHelper();
                }
            }
        }
        return myDatabaseHelper;
    }

    。。。增删改查操作。。。
}
```
九.GreenDao的增删改查

```
1、增     insert
2、删     deleteByKey
3、改     update
4、查     loadAll


//插入
public void insertAll(List<Student> list){
    studentDao.insertInTx(list);
}

public void insert(Student student){
    studentDao.insert(student);
}

public void insert(Student student){
    studentDao.insertOrReplace(student);
}

//删除
public void deleteAll(){
    studentDao.deleteAll();
}

public void delete(Student student){
    studentDao.delete(student);
}


//更改
public void updateAll(List<Student> list){
    studentDao.updateInTx(list);
}

public void update(Student student){
    studentDao.update(student);
}
//单个查询
  public  static User queryOneUser(String name) {

        DaoSession daoSession = MyApplication.getDaoSession();

        return daoSession.queryBuilder(User.class)
                .where(UserDao.Properties.Name.eq(name))//根据条件查询--name
                .build()//建立
//                .list();
                .unique();//唯一的
    }
//查询
public List<Student> queryAll(){
    return  studentDao.queryBuilder().list();
}
public List<Student> queryStudent(Student student){
    return  studentDao.queryBuilder().where(StudentDao.Properties.Name.eq(student.getName())).list();
}
public List<Student> queryStudent2(Student student){
    return  studentDao.queryBuilder().where(StudentDao.Properties.Name.eq(student.getName()),StudentDao.Properties.Age.gt(student.getAge())).list();
}
public List<Student> queryPage(int page,int count){
    return  studentDao.queryBuilder().offset(page*count).limit(count).list();
}

public StudentDao query(PersonInfor studentDao){
    StudentDao oldStudentDao = studentDao.queryBuilder().where(StudentDao.Properties.Id.eq(studentDao.getId())).build().unique();
    return oldStudentDao；
}

//判断是否存在
 public boolean isHash(DbMusicBean dbMusicBean) {
        List<DbMusicBean> list = dbMusicBeanDao.queryBuilder().where(DbMusicBeanDao.Properties.Title.eq(dbMusicBean.getTitle())).list();
        if (list != null && list.size() > 0) {
            return true;
        }
        return false;
    }
```

```
/**
 * 
 * please call {@link #migrate(SQLiteDatabase, Class[])} or {@link #migrate(Database, Class[])}
 * 
 */
public final class MigrationHelper {

    public static boolean DEBUG = false;
    private static String TAG = "MigrationHelper";
    private static final String SQLITE_MASTER = "sqlite_master";
    private static final String SQLITE_TEMP_MASTER = "sqlite_temp_master";

    private static WeakReference<ReCreateAllTableListener> weakListener;

    public interface ReCreateAllTableListener{
        void onCreateAllTables(Database db, boolean ifNotExists);
        void onDropAllTables(Database db, boolean ifExists);
    }

    public static void migrate(SQLiteDatabase db, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        printLog("【The Old Database Version】" + db.getVersion());
        Database database = new StandardDatabase(db);
        migrate(database, daoClasses);
    }

    public static void migrate(SQLiteDatabase db, ReCreateAllTableListener listener, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        weakListener = new WeakReference<>(listener);
        migrate(db, daoClasses);
    }

    public static void migrate(Database database, ReCreateAllTableListener listener, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        weakListener = new WeakReference<>(listener);
        migrate(database, daoClasses);
    }

    public static void migrate(Database database, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        printLog("【Generate temp table】start");
        generateTempTables(database, daoClasses);
        printLog("【Generate temp table】complete");

        ReCreateAllTableListener listener = null;
        if (weakListener != null) {
            listener = weakListener.get();
        }

        if (listener != null) {
            listener.onDropAllTables(database, true);
            printLog("【Drop all table by listener】");
            listener.onCreateAllTables(database, false);
            printLog("【Create all table by listener】");
        } else {
            dropAllTables(database, true, daoClasses);
            createAllTables(database, false, daoClasses);
        }
        printLog("【Restore data】start");
        restoreData(database, daoClasses);
        printLog("【Restore data】complete");
    }

    private static void generateTempTables(Database db, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        for (int i = 0; i < daoClasses.length; i++) {
            String tempTableName = null;

            DaoConfig daoConfig = new DaoConfig(db, daoClasses[i]);
            String tableName = daoConfig.tablename;
            if (!isTableExists(db, false, tableName)) {
                printLog("【New Table】" + tableName);
                continue;
            }
            try {
                tempTableName = daoConfig.tablename.concat("_TEMP");
                StringBuilder dropTableStringBuilder = new StringBuilder();
                dropTableStringBuilder.append("DROP TABLE IF EXISTS ").append(tempTableName).append(";");
                db.execSQL(dropTableStringBuilder.toString());

                StringBuilder insertTableStringBuilder = new StringBuilder();
                insertTableStringBuilder.append("CREATE TEMPORARY TABLE ").append(tempTableName);
                insertTableStringBuilder.append(" AS SELECT * FROM `").append(tableName).append("`;");
                db.execSQL(insertTableStringBuilder.toString());
                printLog("【Table】" + tableName +"\n ---Columns-->"+getColumnsStr(daoConfig));
                printLog("【Generate temp table】" + tempTableName);
            } catch (SQLException e) {
                Log.e(TAG, "【Failed to generate temp table】" + tempTableName, e);
            }
        }
    }

    private static boolean isTableExists(Database db, boolean isTemp, String tableName) {
        if (db == null || TextUtils.isEmpty(tableName)) {
            return false;
        }
        String dbName = isTemp ? SQLITE_TEMP_MASTER : SQLITE_MASTER;
        String sql = "SELECT COUNT(*) FROM `" + dbName + "` WHERE type = ? AND name = ?";
        Cursor cursor=null;
        int count = 0;
        try {
            cursor = db.rawQuery(sql, new String[]{"table", tableName});
            if (cursor == null || !cursor.moveToFirst()) {
                return false;
            }
            count = cursor.getInt(0);
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (cursor != null)
                cursor.close();
        }
        return count > 0;
    }


    private static String getColumnsStr(DaoConfig daoConfig) {
        if (daoConfig == null) {
            return "no columns";
        }
        StringBuilder builder = new StringBuilder();
        for (int i = 0; i < daoConfig.allColumns.length; i++) {
            builder.append(daoConfig.allColumns[i]);
            builder.append(",");
        }
        if (builder.length() > 0) {
            builder.deleteCharAt(builder.length() - 1);
        }
        return builder.toString();
    }


    private static void dropAllTables(Database db, boolean ifExists, @NonNull Class<? extends AbstractDao<?, ?>>... daoClasses) {
        reflectMethod(db, "dropTable", ifExists, daoClasses);
        printLog("【Drop all table by reflect】");
    }

    private static void createAllTables(Database db, boolean ifNotExists, @NonNull Class<? extends AbstractDao<?, ?>>... daoClasses) {
        reflectMethod(db, "createTable", ifNotExists, daoClasses);
        printLog("【Create all table by reflect】");
    }

    /**
     * dao class already define the sql exec method, so just invoke it
     */
    private static void reflectMethod(Database db, String methodName, boolean isExists, @NonNull Class<? extends AbstractDao<?, ?>>... daoClasses) {
        if (daoClasses.length < 1) {
            return;
        }
        try {
            for (Class cls : daoClasses) {
                Method method = cls.getDeclaredMethod(methodName, Database.class, boolean.class);
                method.invoke(null, db, isExists);
            }
        } catch (NoSuchMethodException e) {
            e.printStackTrace();
        } catch (InvocationTargetException e) {
            e.printStackTrace();
        } catch (IllegalAccessException e) {
            e.printStackTrace();
        }
    }

    private static void restoreData(Database db, Class<? extends AbstractDao<?, ?>>... daoClasses) {
        for (int i = 0; i < daoClasses.length; i++) {
            DaoConfig daoConfig = new DaoConfig(db, daoClasses[i]);
            String tableName = daoConfig.tablename;
            String tempTableName = daoConfig.tablename.concat("_TEMP");

            if (!isTableExists(db, true, tempTableName)) {
                continue;
            }

            try {
                // get all columns from tempTable, take careful to use the columns list
                List<TableInfo> newTableInfos = TableInfo.getTableInfo(db, tableName);
                List<TableInfo> tempTableInfos = TableInfo.getTableInfo(db, tempTableName);
                ArrayList<String> selectColumns = new ArrayList<>(newTableInfos.size());
                ArrayList<String> intoColumns = new ArrayList<>(newTableInfos.size());
                for (TableInfo tableInfo : tempTableInfos) {
                    if (newTableInfos.contains(tableInfo)) {
                        String column = '`' + tableInfo.name + '`';
                        intoColumns.add(column);
                        selectColumns.add(column);
                    }
                }
                // NOT NULL columns list
                for (TableInfo tableInfo : newTableInfos) {
                    if (tableInfo.notnull && !tempTableInfos.contains(tableInfo)) {
                        String column = '`' + tableInfo.name + '`';
                        intoColumns.add(column);

                        String value;
                        if (tableInfo.dfltValue != null) {
                            value = "'" + tableInfo.dfltValue + "' AS ";
                        } else {
                            value = "'' AS ";
                        }
                        selectColumns.add(value + column);
                    }
                }

                if (intoColumns.size() != 0) {
                    StringBuilder insertTableStringBuilder = new StringBuilder();
                    insertTableStringBuilder.append("REPLACE INTO `").append(tableName).append("` (");
                    insertTableStringBuilder.append(TextUtils.join(",", intoColumns));
                    insertTableStringBuilder.append(") SELECT ");
                    insertTableStringBuilder.append(TextUtils.join(",", selectColumns));
                    insertTableStringBuilder.append(" FROM ").append(tempTableName).append(";");
                    db.execSQL(insertTableStringBuilder.toString());
                    printLog("【Restore data】 to " + tableName);
                }
                StringBuilder dropTableStringBuilder = new StringBuilder();
                dropTableStringBuilder.append("DROP TABLE ").append(tempTableName);
                db.execSQL(dropTableStringBuilder.toString());
                printLog("【Drop temp table】" + tempTableName);
            } catch (SQLException e) {
                Log.e(TAG, "【Failed to restore data from temp table 】" + tempTableName, e);
            }
        }
    }

    private static List<String> getColumns(Database db, String tableName) {
        List<String> columns = null;
        Cursor cursor = null;
        try {
            cursor = db.rawQuery("SELECT * FROM " + tableName + " limit 0", null);
            if (null != cursor && cursor.getColumnCount() > 0) {
                columns = Arrays.asList(cursor.getColumnNames());
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            if (cursor != null)
                cursor.close();
            if (null == columns)
                columns = new ArrayList<>();
        }
        return columns;
    }

    private static void printLog(String info){
        if(DEBUG){
            Log.d(TAG, info);
        }
    }

    private static class TableInfo {
        int cid;
        String name;
        String type;
        boolean notnull;
        String dfltValue;
        boolean pk;

        @Override
        public boolean equals(Object o) {
            return this == o
                    || o != null
                    && getClass() == o.getClass()
                    && name.equals(((TableInfo) o).name);
        }

        @Override
        public String toString() {
            return "TableInfo{" +
                    "cid=" + cid +
                    ", name='" + name + '\'' +
                    ", type='" + type + '\'' +
                    ", notnull=" + notnull +
                    ", dfltValue='" + dfltValue + '\'' +
                    ", pk=" + pk +
                    '}';
        }

        private static List<TableInfo> getTableInfo(Database db, String tableName) {
            String sql = "PRAGMA table_info(`" + tableName + "`)";
            printLog(sql);
            Cursor cursor = db.rawQuery(sql, null);
            if (cursor == null)
                return new ArrayList<>();

            TableInfo tableInfo;
            List<TableInfo> tableInfos = new ArrayList<>();
            while (cursor.moveToNext()) {
                tableInfo = new TableInfo();
                tableInfo.cid = cursor.getInt(0);
                tableInfo.name = cursor.getString(1);
                tableInfo.type = cursor.getString(2);
                tableInfo.notnull = cursor.getInt(3) == 1;
                tableInfo.dfltValue = cursor.getString(4);
                tableInfo.pk = cursor.getInt(5) == 1;
                tableInfos.add(tableInfo);
                // printLog(tableName + "：" + tableInfo);
            }
            cursor.close();
            return tableInfos;
        }
    }
}
```
自定义工具类

```
public class MyOpenHelper extends DaoMaster.OpenHelper {
    public MyOpenHelper(Context context, String name, SQLiteDatabase.CursorFactory factory) {
        super(context, name, factory);
    }
 
    @Override
    public void onUpgrade(Database db, int oldVersion, int newVersion) {
 
        //把需要管理的数据库表DAO作为最后一个参数传入到方法中
        MigrationHelper.migrate(db, new MigrationHelper.ReCreateAllTableListener() {
 
            @Override
            public void onCreateAllTables(Database db, boolean ifNotExists) {
                DaoMaster.createAllTables(db, ifNotExists);
            }
 
            @Override
            public void onDropAllTables(Database db, boolean ifExists) {
                DaoMaster.dropAllTables(db, ifExists);
            }
        },  BeanDao.class);
    }
}
```

新建一个类，继承DaoMaster.DevOpenHelper，重写onUpgrade(Database db, int oldVersion, int newVersion)方法，在该方法中使用MigrationHelper进行数据库升级以及数据迁移。
然后使用MyOpenHelper替代DaoMaster.DevOpenHelper来进行创建数据库等操作


```
mSQLiteOpenHelper = new MyOpenHelper(MyApplication.getInstance(), DB_NAME, null);//建库
mDaoMaster = new DaoMaster(mSQLiteOpenHelper.getWritableDatabase());
mDaoSession = mDaoMaster.newSession();
```
第二步：
在表实体中，调整其中的变量（表字段），一般就是新增/删除/修改字段。注意：
1）新增的字段或修改的字段，其变量类型应使用基础数据类型的包装类，如使用Integer而不是int，避免升级过程中报错。
2）根据MigrationHelper中的代码，升级后，新增的字段和修改的字段，都会默认被赋予null值。

第三步：
将原本自动生成的构造方法以及getter/setter方法删除，重新Build—>Make Project进行生成。

第四步：
修改Module下build.gradle中数据库的版本号schemaVersion ，递增加1即可，最后运行app


```
greendao {
    //数据库版本号，升级时进行修改
    schemaVersion 2
 
    daoPackage 'com.dev.base.model.db'
    targetGenDir 'src/main/java'
}
```
## o
