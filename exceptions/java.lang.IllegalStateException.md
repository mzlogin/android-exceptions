# java.lang.IllegalStateException

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
05-09 09:41:27.356 1128-1128/org.mazhuang.multicastdemo E/AndroidRuntime: FATAL EXCEPTION: main
    Process: org.mazhuang.multicastdemo, PID: 1128
    java.lang.IllegalStateException: The content of the adapter has changed but ListView did not receive a notification. Make sure the content of your adapter is not modified from a background thread, but only from the UI thread. Make sure your adapter calls notifyDataSetChanged() when its content changes. [in ListView(2131165270, class android.widget.ListView) with Adapter(class android.widget.SimpleAdapter)]
        at android.widget.ListView.layoutChildren(ListView.java:1618)
        at android.widget.AbsListView.onLayout(AbsListView.java:2161)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1741)
        at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1585)
        at android.widget.LinearLayout.onLayout(LinearLayout.java:1494)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
        at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.support.v7.widget.ActionBarOverlayLayout.onLayout(ActionBarOverlayLayout.java:443)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
        at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.widget.LinearLayout.setChildFrame(LinearLayout.java:1741)
        at android.widget.LinearLayout.layoutVertical(LinearLayout.java:1585)
        at android.widget.LinearLayout.onLayout(LinearLayout.java:1494)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.widget.FrameLayout.layoutChildren(FrameLayout.java:323)
        at android.widget.FrameLayout.onLayout(FrameLayout.java:261)
        at com.android.internal.policy.DecorView.onLayout(DecorView.java:727)
        at android.view.View.layout(View.java:17548)
        at android.view.ViewGroup.layout(ViewGroup.java:5614)
        at android.view.ViewRootImpl.performLayout(ViewRootImpl.java:2383)
        at android.view.ViewRootImpl.performTraversals(ViewRootImpl.java:2110)
        at android.view.ViewRootImpl.doTraversal(ViewRootImpl.java:1287)
        at android.view.ViewRootImpl$TraversalRunnable.run(ViewRootImpl.java:6363)
        at android.view.Choreographer$CallbackRecord.run(Choreographer.java:873)
        at android.view.Choreographer.doCallbacks(Choreographer.java:685)
        at android.view.Choreographer.doFrame(Choreographer.java:621)
        at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:859)
        at android.os.Handler.handleCallback(Handler.java:754)
        at android.os.Handler.dispatchMessage(Handler.java:95)
        at android.os.Looper.loop(Looper.java:163)
        at android.app.ActivityThread.main(ActivityThread.java:6337)
        at java.lang.reflect.Method.invoke(Native Method)
        at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:880)
        at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:770)
```

### 出错代码

```java
new Thread() {
    @Override
    public void run() {
        // ...
        mData.add(data);
        MainActivity.this.runOnUiThread(() -> {
            mAdapter.notifyDataSetChanged();
        }
        // ...
    }
}.start();
```

### 崩溃分析

根据出错堆栈里的关键提示：

```
The content of the adapter has changed but ListView did not receive a notification. Make sure the content of your adapter is not modified from a background thread, but only from the UI thread. Make sure your adapter calls notifyDataSetChanged() when its content changes.
```

更新 Adapter 里的数据并 notifyDataSetChanged 需要在 UI 线程里进行。如果在子线程里更新了数据，而在 UI 线程里稍后更新代码，就有可能在一个时间差内数据与界面状态不一致，从而导致崩溃。

### 解决方案

在 UI 线程里面更新 Adapter 里的数据，并 notifyDataSetChanged，即：

```java
new Thread() {
    @Override
    public void run() {
        // ...
        MainActivity.this.runOnUiThread(() -> {
            mData.add(data);
            mAdapter.notifyDataSetChanged();
        }
        // ...
    }
}.start();
```
