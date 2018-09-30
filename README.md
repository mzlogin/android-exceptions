# android-exceptions

那些年爆过的异常。

## Java 异常

* [android.content.ActivityNotFoundException](./exceptions/android.content.ActivityNotFoundException.md)
* [android.view.InflateException](./exceptions/android.view.InflateException.md)
* [java.lang.ClassNotFoundException](./exceptions/java.lang.ClassNotFoundException.md)
* [java.lang.IllegalArgumentException](./exceptions/java.lang.IllegalArgumentException.md)
* [java.lang.IllegalStateException](./exceptions/java.lang.IllegalStateException.md)
* [java.lang.NullPointerException](./exceptions/java.lang.NullPointerException.md)
* [java.lang.NumberFormatException](./exceptions/java.lang.NumberFormatException.md)
* [java.lang.RuntimeException](./exceptions/java.lang.RuntimeException.md)

## Native 异常

* [WebView 闪退](./native/webview.md)

## 定位和解决问题的一些经验

### 重视 Caused By

最重要的信息往往是在 Caused By 里面，在这里一般能找到引发异常的原因、位置。有些场景下异常堆栈很长，有多处 Caused By，将它们都查看一下，找到根源问题。
