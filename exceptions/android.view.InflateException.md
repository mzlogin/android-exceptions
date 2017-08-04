# android.view.InflateException

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
android.view.InflateException: Binary XML file line #8: Error inflating class TextView
    at android.view.LayoutInflater.createViewFromTag(LayoutInflater.java:714)
    at android.view.LayoutInflater.rInflate(LayoutInflater.java:756)
    at android.view.LayoutInflater.inflate(LayoutInflater.java:492)
    at android.view.LayoutInflater.inflate(LayoutInflater.java:397)
    at org.mazhuang.androiddebughelper.home.HomeAdapter.onCreateViewHolder(HomeAdapter.java:37)
    at org.mazhuang.androiddebughelper.home.HomeAdapter.onCreateViewHolder(HomeAdapter.java:16)
    at android.support.v7.widget.RecyclerView$Adapter.createViewHolder(RecyclerView.java:6411)
    at android.support.v7.widget.RecyclerView$Recycler.tryGetViewHolderForPositionByDeadline(RecyclerView.java:5597)
    at android.support.v7.widget.RecyclerView$Recycler.getViewForPosition(RecyclerView.java:5482)
    at android.support.v7.widget.RecyclerView$Recycler.getViewForPosition(RecyclerView.java:5478)
    at android.support.v7.widget.LinearLayoutManager$LayoutState.next(LinearLayoutManager.java:2215)
    at android.support.v7.widget.GridLayoutManager.layoutChunk(GridLayoutManager.java:556)
    at android.support.v7.widget.LinearLayoutManager.fill(LinearLayoutManager.java:1502)
    at android.support.v7.widget.LinearLayoutManager.onLayoutChildren(LinearLayoutManager.java:595)
    at android.support.v7.widget.GridLayoutManager.onLayoutChildren(GridLayoutManager.java:170)
    at android.support.v7.widget.RecyclerView.dispatchLayoutStep2(RecyclerView.java:3625)
    at android.support.v7.widget.RecyclerView.onMeasure(RecyclerView.java:3067)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.LinearLayout.measureChildBeforeLayout(LinearLayout.java:1404)
    at android.widget.LinearLayout.measureHorizontal(LinearLayout.java:1052)
    at android.widget.LinearLayout.onMeasure(LinearLayout.java:590)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.FrameLayout.onMeasure(FrameLayout.java:310)
    at android.support.v7.widget.ContentFrameLayout.onMeasure(ContentFrameLayout.java:139)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.support.v7.widget.ActionBarOverlayLayout.onMeasure(ActionBarOverlayLayout.java:391)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.FrameLayout.onMeasure(FrameLayout.java:310)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.LinearLayout.measureChildBeforeLayout(LinearLayout.java:1404)
    at android.widget.LinearLayout.measureVertical(LinearLayout.java:695)
    at android.widget.LinearLayout.onMeasure(LinearLayout.java:588)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewGroup.measureChildWithMargins(ViewGroup.java:5125)
    at android.widget.FrameLayout.onMeasure(FrameLayout.java:310)
    at com.android.internal.policy.impl.PhoneWindow$DecorView.onMeasure(PhoneWindow.java:2291)
    at android.view.View.measure(View.java:16497)
    at android.view.ViewRootImpl.performMeasure(ViewRootImpl.java:1916)
    at android.view.ViewRootImpl.measureHierarchy(ViewRootImpl.java:1113)
    at android.view.ViewRootImpl.performTraversals(ViewRootImpl.java:1295)
    at android.view.ViewRootImpl.doTraversal(ViewRootImpl.java:1000)
    at android.view.ViewRootImpl$TraversalRunnable.run(ViewRootImpl.java:5670)
    at android.view.Choreographer$CallbackRecord.run(Choreographer.java:761)
    at android.view.Choreographer.doCallbacks(Choreographer.java:574)
    at android.view.Choreographer.doFrame(Choreographer.java:544)
    at android.view.Choreographer$FrameDisplayEventReceiver.run(Choreographer.java:747)
    at android.os.Handler.handleCallback(Handler.java:733)
    at android.os.Handler.dispatchMessage(Handler.java:95)
```

### 出错代码

layout/item_test.xml

```xml
<TextView
    android:id="@+id/title"
    ...
    android:background="@color/feature_item_selector"
    ...
    />
```

res/color/feature_item_selector.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:color="@color/feature_item_pressed" android:state_pressed="true" />
    <item android:color="@color/feature_item" />
</selector>
```

### 崩溃分析

不能使用 color selector 作为 background，需要一个 drawable selector。

### 解决方案

res/drawable/feature_item_seelctor.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<selector xmlns:android="http://schemas.android.com/apk/res/android">
    <item android:drawable="@color/feature_item_pressed" android:state_pressed="true" />
    <item android:drawable="@color/feature_item" />
</selector>
```

### 参考链接

* [Selector on background color of TextView](https://stackoverflow.com/questions/3592780/selector-on-background-color-of-textview)
