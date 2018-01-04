# java.lang.ClassNotFoundException

**目录**

<!-- vim-markdown-toc GFM -->

* [Case 1](#case-1)
    * [出错堆栈](#出错堆栈)
    * [出错代码](#出错代码)
    * [崩溃分析](#崩溃分析)
    * [解决方案](#解决方案)

<!-- vim-markdown-toc -->

## Case 1

### 出错堆栈

```
java.lang.ClassNotFoundException: Didn't find class "org.mazhuang.test.data.db.DbHelper" on path: DexPathList[[zip file "/data/app/org.mazhuang.test-2.apk", zip file "/data/data/org.mazhuang.test/code_cache/secondary-dexes/org.mazhuang.test-2.apk.classes2.zip"],nativeLibraryDirectories=[/data/app-lib/org.mazhuang.test-2, /vendor/lib, /system/lib]]

Failed resolution of: Lorg/mazhuang/test/data/db/DbHelper;
```

### 出错代码

类 org.mazhuang.test.data.db.DbHelper 是存在的，但以前叫 DBHelper，重构改名为 DbHelper。

### 崩溃分析

推测是文件名的大小写变化被编译系统忽略了，Android Studio 在编译的时候复用了之前用旧名字的缓存，所以安装到设备上后找不到 DbHelper 类。

### 解决方案

Rebuild。
