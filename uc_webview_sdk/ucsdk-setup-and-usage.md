快速接入
====
      uc webview sdk是一个能够替换Android系统webview并带来更好性能和体验的组件，能够为app提供稳定如一的网页浏览功能。

### 系统支持
* 支持Android 2.2（即Android SDK 8）系统及以上
* 硬件加速特性需4.0以上系统

### 环境准备
安装以下环境并按官方文档配置好相关环境变量：
* Java 1.6以上
* Android SDK
* Android Studio

### 获取SDK以及接入授权码
* SDK由`uc_webview_sdk.jar`和`webview_core_libs_signed.zip`两部分构成
* `uc_webview_sdk.jar`
* `webview_core_libs_signed.zip`也可以如本例一样动态下载部署
* 申请授权码：

> 1.  使用命令keytool -list -v -keystore ~/.android/debug.keystore生成所需证书的SHA1哈希值，例如本例中的5C:A6:35:94:8A:AD:B7:59:C4:7E:C5:9C:B8:D9:F7:39:20:EE:88:AE
>
> 2. 确定样例工程的包名，例如本例com.uc.hellouc
>
> 3. 将上面得到的哈希值和包名，通过邮件发送到seal@list.alibaba-inc.com，授权码将通过邮件回复
>
> 4.     申请格式：
>         应用包名：eg. com.ucsdk.ucsdk
>         签名指纹：eg. 5C:A6:35:94:8A:AD:B7:59:C4:7E:C5:9C:B8:D9:F7:39:20:EE:88:AE
>         预计日活: eg. 100万左右
>         接入类型：eg. 独立应用/基于其他组件

### 样例工程

* 使用Android Studio新建Blank Activity工程
* 在AndroidManifest.xml加入网络访问权限：
>         <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
>         <uses-permission android:name="android.permission.ACCESS_FINE_LOCATION" />
>         <uses-permission android:name="android.permission.INTERNET" />
>         <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
>         <uses-permission android:name="android.permission.READ_PHONE_STATE" />
>         <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
>         <uses-permission android:name="android.permission.VIBRATE" />
>         <uses-permission android:name="android.permission.WAKE_LOCK" />
>         <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
>         <uses-permission android:name="com.android.launcher.permission.INSTALL_SHORTCUT" />
>         <uses-permission android:name="android.permission.RECORD_AUDIO" />
>         <uses-permission android:name="android.permission.GET_TASKS" />


* 将上述下载的uc_webview_sdk.jar文件放在{project_root}/app/libs/目录下，并添加为library
* 将上述获取到的授权码通过UCCore.setup(UCCore.OPTION_PROVIDED_KEYS, 授权码)设入
* 编写UC WebView的使用代码UCWebViewActivity.java如下：

```
package com.uc.hellouc;
 
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
 
import com.uc.webview.export.WebView;
import com.uc.webview.export.WebViewClient;
import com.uc.webview.export.extension.UCCore;
import com.uc.webview.export.utility.download.UpdateTask;
 
public class UCWebViewActivity extends AppCompatActivity {
 
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        startUCWebView();
    }
 
    private void startUCWebView() {
            UCCore
                    .setup(UCCore.OPTION_CONTEXT, getApplicationContext())
                    .setup(UCCore.OPTION_MULTI_CORE_TYPE, true)
                    // pkgname：com.uc.hellouc  SHA1-of-cert：5C:A6:35:94:8A:AD:B7:59:C4:7E:C5:9C:B8:D9:F7:39:20:EE:88:AE
                    .setup(UCCore.OPTION_PROVIDED_KEYS, new String[]{"Xb9YzIBrUy0EAiapTXUz4G+bztB2UEz9Nw3SQ73+P4cV+um7O/BMc/GRx1N8v5La2WocknN1+QGN\nVc0wbefZqg==\n"})
                    .setup(UCCore.OPTION_LOAD_POLICY, UCCore.LOAD_POLICY_SPECIFIED_ONLY)
                    .setup(UCCore.OPTION_DEX_FILE_PATH, UpdateTask.getUpdateRoot(this.getApplicationContext()).getAbsolutePath())
                    .start();
 
            WebView ucwv = new WebView(this);
            ucwv.setWebViewClient(new WebViewClient());
            setContentView(ucwv);
            ucwv.loadUrl("http://www.hao123.com");
 
            try{UCCore.update(getApplicationContext(), "http://gw.alicdn.com/tfscom/TB1hDTyKFXXXXXoXVXXcvNbFXXX.zip", null);}catch (Throwable t){}
    }
}
```
* 下载本例demo[源代码（Android Studio打开）](http://gitlab.alibaba-inc.com/uc-webview-sdk/public-docs/raw/master/samples/hello-uc/hello-uc.zip)。

### 探索UC WebView
>    至此，您第一个运行UC WebView的app已经诞生，由于本例中webview_core_libs_signed.zip在程序运行后下载，因此您第一次启动时将使用系统WebView打开网页，待下载完毕后（wifi情况下一分钟左右），重新启动app将使用UC WebView进行网页渲染。
