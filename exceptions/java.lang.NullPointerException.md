# java.lang.NullPointerException

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
