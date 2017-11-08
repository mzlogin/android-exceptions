# java.lang.RuntimeException

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
