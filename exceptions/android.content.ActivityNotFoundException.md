# android.content.ActivityNotFoundException

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
09-05 09:42:27.892 32688-32688/org.mazhuang.exceptiondemo D/AndroidRuntime: Shutting down VM
09-05 09:42:27.892 32688-32688/org.mazhuang.exceptiondemo E/AndroidRuntime: FATAL EXCEPTION: main
                                                                               Process: org.mazhuang.exceptiondemo, PID: 32688
                                                                               android.content.ActivityNotFoundException: Unable to find explicit activity class {org.mazhuang.exceptiondemo/org.mazhuang.exceptiondemo.activity.SettingActivity}; have you declared this activity in your AndroidManifest.xml?
                                                                                   at android.app.Instrumentation.checkStartActivityResult(Instrumentation.java:1628)
                                                                                   at android.app.Instrumentation.execStartActivity(Instrumentation.java:1424)
                                                                                   at android.app.Activity.startActivityForResult(Activity.java:3424)
                                                                                   at android.support.v4.app.BaseFragmentActivityJB.startActivityForResult(BaseFragmentActivityJB.java:54)
                                                                                   at android.support.v4.app.FragmentActivity.startActivityForResult(FragmentActivity.java:75)
                                                                                   at android.app.Activity.startActivityForResult(Activity.java:3385)
                                                                                   at android.support.v4.app.FragmentActivity.startActivityForResult(FragmentActivity.java:708)
                                                                                   at android.app.Activity.startActivity(Activity.java:3627)
                                                                                   at android.app.Activity.startActivity(Activity.java:3595)
                                                                                   at org.mazhuang.exceptiondemo.MainActivity.onClick(MainActivity.java:121)
                                                                                   at org.mazhuang.exceptiondemo.MainActivity_ViewBinding$6.doClick(MainActivity_ViewBinding.java:83)
                                                                                   at butterknife.internal.DebouncingOnClickListener.onClick(DebouncingOnClickListener.java:22)
                                                                                   at android.view.View.performClick(View.java:4438)
                                                                                   at android.view.View$PerformClick.run(View.java:18422)
                                                                                   at android.os.Handler.handleCallback(Handler.java:733)
                                                                                   at android.os.Handler.dispatchMessage(Handler.java:95)
                                                                                   at android.os.Looper.loop(Looper.java:136)
                                                                                   at android.app.ActivityThread.main(ActivityThread.java:5017)
                                                                                   at java.lang.reflect.Method.invoke(Native Method)
                                                                                   at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:779)
                                                                                   at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:595)
```

### 出错代码

手动加了一个 Activity，但是却未在 AndroidManifest.xml 中声明。

### 崩溃分析

同上。

### 解决方案

在 AndroidManifest.xml 里加上缺少的 Activity 声明：

```xml
<manifest
    ...>
    ...
    <application
        ...>
        ...
        <activity android:name=".activity.SettingActivity" />
    </application>
    ...
</manifest>
```
