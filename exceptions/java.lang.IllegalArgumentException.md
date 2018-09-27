# java.lang.IllegalArgumentException

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
2018-09-27 14:16:21.475 31479-31479/org.mazhuang.mall E/AndroidRuntime: FATAL EXCEPTION: main
    Process: org.mazhuang.mall, PID: 31479
    java.lang.RuntimeException: Unable to start activity ComponentInfo{org.mazhuang.mall/org.mazhuang.mall.order.activity.ConfirmOrderActivity}: java.lang.IllegalArgumentException: No Retrofit annotation found. (parameter #1)
        for method BaseApis.calcPromotion
```

### 出错代码

```java
@Headers(HEADER_CONTENT_TYPE_JSON)
@POST("/calcpromotion")
Observable<BaseResponse<List<Pair<String, Float>>>> calcPromotion(List<String> skuList);
```

### 崩溃分析

实际上从出错信息里就能分析出来：

`for method BaseApis.calcPromotion`，还有 `No Retrofit annotion found. (parameter #1)`，这就可以定位到 calcPromotion 方法的第一个参数，缺少必要的 Retrofit 注解。

确实如此，`skuList` 是作为一个 POST 请求的 body。

### 解决方案

给 `skuList` 加上注解 `@Body`。

```java
Observable<BaseResponse<List<Pair<String, Float>>>> calcPromotion(@Body List<String> skuList);
```
