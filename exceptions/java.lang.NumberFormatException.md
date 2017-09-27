# java.lang.NumberFormatException

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
java.lang.Integer.parseInt(Integer.java:524)
java.lang.Integer.valueOf(Integer.java:611)
org.mazhuang.MyAdapter$MutableWatcher.afterTextChanged(MyAdapter.java:200)
android.widget.TextView.sendAfterTextChanged(TextView.java:8205)
android.widget.TextView$ChangeWatcher.afterTextChanged(TextView.java:10393)
android.text.SpannableStringBuilder.sendAfterTextChanged(SpannableStringBuilder.java:1218)
android.text.SpannableStringBuilder.replace(SpannableStringBuilder.java:579)
android.text.SpannableStringBuilder.replace(SpannableStringBuilder.java:509)
android.text.SpannableStringBuilder.replace(SpannableStringBuilder.java:508)
android.text.method.NumberKeyListener.onKeyDown(NumberKeyListener.java:121)
android.widget.TextView.doKeyDown(TextView.java:6287)
android.widget.TextView.onKeyDown(TextView.java:6077)
android.view.KeyEvent.dispatch(KeyEvent.java:2678)
android.view.View.dispatchKeyEvent(View.java:9880)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.widget.ListView.dispatchKeyEvent(ListView.java:2240)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
android.view.ViewGroup.dispatchKeyEvent(ViewGroup.java:1667)
com.android.internal.policy.DecorView.superDispatchKeyEvent(DecorView.java:403)
com.android.internal.policy.PhoneWindow.superDispatchKeyEvent(PhoneWindow.java:1800)
android.app.Activity.dispatchKeyEvent(Activity.java:3044)
android.support.v7.app.AppCompatActivity.dispatchKeyEvent(AppCompatActivity.java:541)
android.support.v7.view.WindowCallbackWrapper.dispatchKeyEvent(WindowCallbackWrapper.java:59)
android.support.v7.app.AppCompatDelegateImplBase$AppCompatWindowCallbackBase.dispatchKeyEvent(AppCompatDelegateImplBase.java:319)
android.support.v7.view.WindowCallbackWrapper.dispatchKeyEvent(WindowCallbackWrapper.java:59)
com.android.internal.policy.DecorView.dispatchKeyEvent(DecorView.java:317)
android.view.ViewRootImpl$ViewPostImeInputStage.processKeyEvent(ViewRootImpl.java:4388)
android.view.ViewRootImpl$ViewPostImeInputStage.onProcess(ViewRootImpl.java:4359)
android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3905)
android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:3958)
android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:3924)
android.view.ViewRootImpl$AsyncInputStage.forward(ViewRootImpl.java:4051)
android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:3932)
android.view.ViewRootImpl$AsyncInputStage.apply(ViewRootImpl.java:4108)
android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3905)
android.view.ViewRootImpl$InputStage.onDeliverToNext(ViewRootImpl.java:3958)
android.view.ViewRootImpl$InputStage.forward(ViewRootImpl.java:3924)
android.view.ViewRootImpl$InputStage.apply(ViewRootImpl.java:3932)
android.view.ViewRootImpl$InputStage.deliver(ViewRootImpl.java:3905)
android.view.ViewRootImpl.deliverInputEvent(ViewRootImpl.java:6271)
android.view.ViewRootImpl.doProcessInputEvents(ViewRootImpl.java:6245)
android.view.ViewRootImpl.enqueueInputEvent(ViewRootImpl.java:6206)
android.view.ViewRootImpl$ViewRootHandler.handleMessage(ViewRootImpl.java:3700)
android.os.Handler.dispatchMessage(Handler.java:102)
android.os.Looper.loop(Looper.java:160)
android.app.ActivityThread.main(ActivityThread.java:6200)
java.lang.reflect.Method.invoke(Native Method)
com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:874)
com.android.internal.os.ZygoteInit.main(ZygoteInit.java:764)
```

### 出错代码

layout/fragment_test.xml

```xml
<EditText
    ...
    android:inputType="numberDecimal"
    ...
    />
```

TestFragment.java

```java
editText.addTextChangedWatcher(new TextWatcher() {
            ...

            @Override
            public void afterTextChanged(Editable s) {
                int value = 0;
                if (!TextUtils.isEmpty(s)) {
                    value = Integer.valueOf(s.toString());
                }
                ...
            }

            ...
        });
```

### 崩溃分析

意图想限制 EditText 只能输入数字 0123456789，但是实际限制 `android:inputType="numberDecimal"` 并不能达到目的，在用户输入 `-`、`10.` 这样的值后，`Integer.valueOf` 就会抛出 `java.lang.NumberFormatException`，另外需要注意的是如果用户输入的值大于 Integer 的最大值 2147483647，也会抛出此异常，所以需要结合业务需求，对 maxLength 也做限制。

需要想办法更严格地限制输入，或者在 `afterTextChanged` 里面做更好的异常处理。

### 解决方案

layout/fragment_text.xml

```xml
<EditText
    ...
    android:inputType="number"
    android:digits="0123456789"
    android:maxLength="9"
    ...
    />
```
