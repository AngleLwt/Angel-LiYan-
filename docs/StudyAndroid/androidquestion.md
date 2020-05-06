# Android 常遇问题
## 解决Invoke-customs are only supported starting with Android (--min-api 26) Message{kind=ERROR,……
在gradle.build中android下添加以下内容：

```

compileOptions {

sourceCompatibility JavaVersion.VERSION_1_8

    targetCompatibility JavaVersion.VERSION_1_8

}

```
Invoke-customs are only supported starting with Android (--min-api 26) Message{kind=ERROR,……
