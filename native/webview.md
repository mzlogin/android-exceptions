# WebView 闪退

**目录**

<!-- vim-markdown-toc GFM -->

* [Case 1](#case-1)
    * [出错堆栈](#出错堆栈)
    * [出错代码](#出错代码)
    * [崩溃分析](#崩溃分析)
    * [解决方案](#解决方案)
    * [参考链接](#参考链接)

<!-- vim-markdown-toc -->

## Case 1

### 出错堆栈

```
09-14 10:06:28.811 7531-7787/? A/libc: Fatal signal 11 (SIGSEGV), code 1, fault addr 0x98fcd49000 in tid 7787 (Chrome_InProcGp), pid 7531 (org.mazhuang.mall)
```

### 出错代码

就是打开一个 WebView，正常加载一个 URL 而已。

### 崩溃分析

日志里看不出太多有用信息，主要靠搜索 `Chrome_InProcGp` 来找解决方案了。

在 [WebView 闪退][1] 这个链接找到一点参考信息：

> Gradle 版本升级导致的（3.3 -> 3.5），3.5 版本编译的 APP 不能兼容 3.3 版本编译的 APP 运行产生的数据，所以闪退，只要删除 /data/data/packagename/app_webview 文件夹就 OK 了。

我的场景还真是符合，从旧版本 Gradle 编译的升级到的新版本 Gradle 编译的。

### 解决方案

1. 将应用卸载重装；

2. 如果无法避免这种升级场景存在，那么在代码里添加逻辑，应用启动后删除一次上述文件夹，并在 SharedPreferences 里添加一个标记，下次启动时判断标记，如果已经删除过就不再次处理。

### 参考链接

* [WebView 闪退][1]

[1]: http://www.voidcn.com/article/p-drczlbqs-bqz.html
