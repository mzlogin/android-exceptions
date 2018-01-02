# java.lang.RuntimeException

**目录**

<!-- vim-markdown-toc GFM -->

* [Case 1](#case-1)
    * [出错堆栈](#出错堆栈)
    * [出错代码](#出错代码)
    * [崩溃分析](#崩溃分析)
    * [解决方案](#解决方案)
* [Case 2](#case-2)
    * [出错堆栈](#出错堆栈-1)
    * [出错代码](#出错代码-1)
    * [崩溃分析](#崩溃分析-1)
    * [解决方案](#解决方案-1)

<!-- vim-markdown-toc -->

## Case 1

### 出错堆栈

```
11-07 13:35:33.980 2020-2035/org.mazhuang.androiduidemos E/AndroidRuntime: FATAL EXCEPTION: Thread-77
    java.lang.RuntimeException: Can't create handler inside thread that has not called Looper.prepare()
        at android.widget.Toast$TN.<init>(Toast.java:390)
        at android.widget.Toast.<init>(Toast.java:114)
        at android.widget.Toast.makeText(Toast.java:277)
        at android.widget.Toast.makeText(Toast.java:267)
        at org.mazhuang.androiduidemos.MainActivity$1.run(MainActivity.java:27)
        at java.lang.Thread.run(Thread.java:856)
```

### 出错代码

```java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {

        ...

        new Thread(new Runnable() {
            @Override
            public void run() {
                Toast.makeText(MainActivity.this, "Call toast on non-UI thread", Toast.LENGTH_SHORT)
                        .show();
            }
        }).start();
    }
}
```

### 崩溃分析

从出错堆栈中我们得到的关键信息是 `Can't create handler inside thread that has not called Looper.prepare()`，实际通过 [对 Toast 的源码解读](https://github.com/mzlogin/rtfsc-android/blob/master/0x003-start-from-toast.md) 可以得知，在非 UI 线程中调用 Toast 需要该线程有 Looper。

### 解决方案

将线程代码改成下面这样：

```java
new Thread(new Runnable() {
    @Override
    public void run() {
        final int MSG_TOAST = 101;
        final int MSG_QUIT = 102;

        Looper.prepare();

        final Handler handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {

                switch (msg.what) {
                    case MSG_TOAST:
                        Toast.makeText(MainActivity.this, "Call toast on non-UI thread", Toast.LENGTH_SHORT)
                                .show();
                        sendEmptyMessageDelayed(MSG_QUIT, 4000);
                        return;

                    case MSG_QUIT:
                        Looper.myLooper().quit();
                        return;
                }

                super.handleMessage(msg);
            }
        };

        handler.sendEmptyMessage(MSG_TOAST);

        Looper.loop();
    }
}).start();
```

或者在线程里使用 Activity.runOnUiThread 或 View.post 等方法将 Toast 调用切换到 UI 线程执行（UI 线程有 Looper）。

## Case 2

### 出错堆栈

```
01-02 05:31:33.366 2053-2053/org.mazhuang.test E/AndroidRuntime: FATAL EXCEPTION: main
     Process: org.mazhuang.test, PID: 2053
     java.lang.RuntimeException: Unable to start receiver org.mazhuang.test.receiver.AutoBootReceiver: android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?
         at android.app.ActivityThread.handleReceiver(ActivityThread.java:2426)
         at android.app.ActivityThread.access$1700(ActivityThread.java:135)
         at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1272)
         at android.os.Handler.dispatchMessage(Handler.java:102)
         at android.os.Looper.loop(Looper.java:136)
         at android.app.ActivityThread.main(ActivityThread.java:5017)
         at java.lang.reflect.Method.invokeNative(Native Method)
         at java.lang.reflect.Method.invoke(Method.java:515)
         at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:779)
         at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:595)
         at dalvik.system.NativeStart.main(Native Method)
      Caused by: android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?
         at android.app.ContextImpl.startActivity(ContextImpl.java:1034)
         at android.app.ContextImpl.startActivity(ContextImpl.java:1021)
         at android.content.ContextWrapper.startActivity(ContextWrapper.java:311)
         at android.content.ContextWrapper.startActivity(ContextWrapper.java:311)
         at org.mazhuang.test.receiver.AutoBootReceiver.onReceive(AutoBootReceiver.java:26)
         at android.app.ActivityThread.handleReceiver(ActivityThread.java:2419)
         at android.app.ActivityThread.access$1700(ActivityThread.java:135) 
         at android.app.ActivityThread$H.handleMessage(ActivityThread.java:1272) 
         at android.os.Handler.dispatchMessage(Handler.java:102) 
         at android.os.Looper.loop(Looper.java:136) 
         at android.app.ActivityThread.main(ActivityThread.java:5017) 
         at java.lang.reflect.Method.invokeNative(Native Method) 
         at java.lang.reflect.Method.invoke(Method.java:515) 
         at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:779) 
         at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:595) 
         at dalvik.system.NativeStart.main(Native Method)
```

### 出错代码

```java
public class AutoBootReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (TextUtils.isEmpty(action)) {
            return;
        }

        if (Intent.ACTION_BOOT_COMPLETED.equals(action)) {
            LogUtils.i("receive boot_completed broadcast");
            context.startActivity(new Intent(context, InitializeActivity.class));
        }
    }
}
```

### 崩溃分析

代码的意图是通过一个静态注册的 BroadcastReceiver 来实现开机启动，关键代码行是 onReceive 方法里的 `context.startActivity`。

从出错堆栈中我们能得到的关键信息是

```
java.lang.RuntimeException: Unable to start receiver org.mazhuang.test.receiver.AutoBootReceiver: android.util.AndroidRuntimeException: Calling startActivity() from outside of an Activity  context requires the FLAG_ACTIVITY_NEW_TASK flag. Is this really what you want?
```

由此可知 onReceive 方法的入参 `context` 不是 Activity 实例，它没有关联的 task 来放置新启动的 Activity，使用这样的 Context 来启动 Activity 需要指定 `FLAG_ACTIVITY_NEW_TASK` 来将其放置到一个独立的 task。

参考 [Context#startActivity(android.content.Intent, android.os.Bundle)](https://developer.android.com/reference/android/content/Context.html#startActivity(android.content.Intent,%20android.os.Bundle)) 的说明：

```
Note that if this method is being called from outside of an Activity Context, then the Intent must include the FLAG_ACTIVITY_NEW_TASK launch flag. This is because, without being started from an existing Activity, there is no existing task in which to place the new activity and thus it needs to be placed in its own separate task.
```

### 解决方案

为 Intent 加上 `FLAT_ACTIVITY_NEW_TASK` launch flag，修改后的代码如下：

```java
public class AutoBootReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        if (TextUtils.isEmpty(action)) {
            return;
        }

        if (Intent.ACTION_BOOT_COMPLETED.equals(action)) {
            LogUtils.i("receive boot_completed broadcast");
            Intent i = new Intent(context, InitializeActivity.class);
            i.addFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
            context.startActivity(i);
        }
    }
}
```
