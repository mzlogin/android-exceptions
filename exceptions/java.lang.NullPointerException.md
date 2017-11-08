# java.lang.NullPointerException

**目录**

<!-- vim-markdown-toc GFM -->

* [Case 1](#case-1)
    * [出错堆栈](#出错堆栈)
    * [出错代码](#出错代码)
    * [崩溃分析](#崩溃分析)
    * [解决方案](#解决方案)
    * [参考链接](#参考链接)
* [Case 2](#case-2)
    * [出错堆栈](#出错堆栈-1)
    * [出错代码](#出错代码-1)
    * [崩溃分析](#崩溃分析-1)
    * [解决方案](#解决方案-1)

<!-- vim-markdown-toc -->

## Case 1

### 出错堆栈

```
Exception in thread "main" java.lang.NullPointerException
        at Test.main(Test.java:3)
```

### 出错代码

```java
public class Test {
    public static void main(String[] args) {
        long value = true ? null : 1;
    }
}
```

### 崩溃分析

反汇编以上代码可得：

```java
Compiled from "Test.java"
public class Test {
  public Test();
    Code:
       0: aload_0
       1: invokespecial #1                  // Method java/lang/Object."<init>":()V
       4: return

  public static void main(java.lang.String[]);
    Code:
       0: aconst_null
       1: checkcast     #2                  // class java/lang/Long
       4: invokevirtual #3                  // Method java/lang/Long.longValue:()J
       7: lstore_1
       8: return
}
```

这是由于尝试对一个值为 null 的 Long 类型调用 Long.longValue() 进行自动拆箱导致的。

### 解决方案

对自动拆箱装箱的情况进行留意，避免各种花式 NullPointerException。

### 参考链接

* [从一个 NullPointerException 探究 Java 的自动装箱拆箱机制](http://mazhuang.org/2017/08/20/java-auto-boxing-unboxing/)

## Case 2

### 出错堆栈

```
java.lang.NullPointerException
    Attempt to invoke virtual method 'java.lang.String org.mazhuang.example.entity.Person.getName()' on a null object reference
        org.mazhuang.example.SelectFragment$3.onClick(SelectFragment.java:120)
        android.view.View.performClick(View.java:5619)
        android.view.View$PerformClick.run(View.java:22295)
        android.os.Handler.handleCallback(Handler.java:754)
        android.os.Handler.dispatchMessage(Handler.java:95)
        android.os.Looper.loop(Looper.java:163)
        android.app.ActivityThread.main(ActivityThread.java:6321)
        java.lang.reflect.Method.invoke(Native Method)
        com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:880)
        com.android.internal.os.ZygoteInit.main(ZygoteInit.java:770)
```

### 出错代码

界面上展示了一个 Spinner 和一个按钮，Spinner 的 Adapter 里的 Item 是 Person 实体对象，点击按钮的时候会获取 Spinner 中展示的人名。

```java
// org/mazhuang/example/entity/Person.java
public class Person {
    private String name;

    ...

    String getName() {
        return name;
    }

    ...
}

// org/mazhuang/example/SelectFragment.java
public void onClick(View v) {
    String name = mSpinner.getSelectedItem().getName();
    ...
}
```

### 崩溃分析

实际上当 mSpinner 的 Adapter 里没有内容时，mSpinner.getSelectedItem() 返回总是为 null，于是就相当于调用了 `((Person)null).getName()`，于是抛出异常。

### 解决方案

判断 Spinner 是否有有效的选中项，然后再对应处理。

```java
// org/mazhuang/example/SelectFragment.java
public void onClick(View v) {
    String name = (mSpinner.getSelectedItemPosition() != Spinner.INVALID_POSITION)
        ? mSpinner.getSelectedItem().getName()
        : null;
    ...
}
```
