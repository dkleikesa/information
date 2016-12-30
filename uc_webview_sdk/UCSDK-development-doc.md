1.前言
----
**1.1 编写目的**

本文档为UC浏览器开放SDK的使用文档，其编写目的是帮助开发人员从系统WebView迁移到UC浏览器提供的WebView。

**1.2 面向对象**

本文档面向第三方开发人员。

2.简介
----
**2.1 UC WebView的优势**

***性能***

经过不断的性能调优，UC WebView加载速度平均而言比系统的WebView要好30%。

***云加速***

UC独有的私有网络传输协议；针对移动网络，提供云端加速功能（可选），使得UC SDK联网时间比系统webview平均快50%~70%（在3G下快50%；2G下快70%），获取到数据后显示速度比系统webview快100%。这些技术同时能大幅降低流量的消耗。

***稳定性***

通过不断的monkey测试和针对性优化，UC修复很多已知的webkit在android上面的稳定性问题。崩溃率相比webview降低了40%，并建立了一套完整的线上稳定性监控系统，每天监控线上崩溃率变化。

***兼容性***

UC在2.X或4.0，4.1，4.2系统上，也能完美兼容Html5页面的显示，Html5 test跑分比系统浏览器约高出一倍（系统浏览器跑分228分，UC达413分），UC针对所有的Android版本，都能支持所有的WebView api。

***出色的视频播放能力***

UC WebView采用自研的播放器播放视频，支持更多的视频格式，并且加载速度更快。

 
**2.2 运行环境**

手机ROM版本高于或等于2.2版本，编译时需要android-8以上的sdk支持。

（注：部分功能只在4.X机器上提供。）

**2.3 SDK尺寸指标**

1.UC WebView提供的共享版集成JAR包约100KB。

2.UC WebView对应的内核既可以采用共享UC浏览器内核的方式提供，也可以通过内置到App里面的方式提供；

3.如UC内核选择“指定目录加载”，则须额外部署17.8 MB的UC内核文件。如选择“查找已安装UC浏览器产品并共享其内核”选项，则无该部分开销。

4.三方集成SDK到软件后，APK包尺寸增加约100K。（印象数据，稳定后更新）

**2.4 SDK接口原型**

UC SDK的接口基于Android 4.4 WebView设计，将尽可能在所有版本的Android上提供基本一致的功能。

**2.5 动态切换**

UC WebView可以自由切换系统WebView和UC内核的WebView，并且如果采用共享UC浏览器内核的方式，而UC WebView发现没有安装的UC浏览器或者安装的版本不支持的时候，会自动切换系统内核。

 
3.SDK集成步骤
----

**3.1 签名认证**

厂商需要首先通过邮件的方式向u3_standard@ucweb.com发起集成的申请，告知自己App的包名（Package Name）和自己发布签名公钥的SHA-1字符串。

UC会生成一个Token返回给厂商，厂商需要在自己AndroidManifest.xml在application标签内部加入： <meta-data android:name="UCSDKAppKey" android:value="UC返回的Token"> </meta-data>。

**3.2 接口代码加入**

UC 复制uc_webview_sdk.jar到eclipse工程的libs目录下。

 
**3.3 在源代码和xml文件中替换系统包路径**

将工程中的所有对android.webkit.aaa的引用更改为com.uc.webview.export.aaa。

例如import android.webkit.WebView更改为import com.uc.webview.export.WebView。

需要注意的是：请不要在代码里使用import android.* 等类似的通配符import的写法。

**3.4**

|系统内核|SDK内核|
| :-------: |:-----:|
|android.webkit.CookieManager|com.uc.webview.export.CookieManager|
|android.webkit.DownloadListener|com.uc.webview.export.DownloadListener|
|android.webkit.GeolocationPermissions|com.uc.webview.export.GeolocationPermissions|
|android.webkit.HttpAuthHandler|com.uc.webview.export.HttpAuthHandler|
|android.webkit.JsPromptResult|com.uc.webview.export.JsPromptResult|
|android.webkit.JsResult|com.uc.webview.export.JsResult|
|android.webkit.SslErrorHandler|com.uc.webview.export.SslErrorHandler|
|android.webkit.WebBackForwardList|com.uc.webview.export.WebBackForwardList|
|android.webkit.WebChromeClient|com.uc.webview.export.WebChromeClient|
|android.webkit.WebChromeClient.CustomViewCallback|com.uc.webview.export.WebChromeClient.CustomViewCallback|
|android.webkit.WebHistoryItem|com.uc.webview.export.WebHistoryItem|
|android.webkit.WebResourceResponse|com.uc.webview.export.WebResourceResponse|
|android.webkit.WebSettings|com.uc.webview.export.WebSettings|
|android.webkit.WebSettings.LayoutAlgorithm|com.uc.webview.export.WebSettings.LayoutAlgorithm|
|android.webkit.WebView|com.uc.webview.export.WebView|
|android.webkit.WebView.HitTestResult|com.uc.webview.export.WebView.HitTestResult|
|android.webkit.WebViewClient|com.uc.webview.export.WebViewClient|


**内核部署与初始化**

UC WebView采用接口和实现完全分离的方式，所以部署的方法非常灵活。

UC内核的初始化使用函数UCCore.init(Context context, boolean useSystemWebView, String dexFilePath, String soFilePath, BreakpadConfig breakpadConfig)，针对不同的部署方式，需要使用不同的参数进行调用。

UC WebView在启动之后，只能支持选择一次内核，也就是如果初始化为系统内核之后，在运行过程中不能切换UC内核，反之亦然。

***3.4.1 默认初始化***

UC WebView不用初始化也可以正常调起，这个情况下UC WebView会在第一次WebView构造的时候，用UCCore.init(context, false, null, null, null);的方式来初始化UC。实际相当于“使用用户安装好的UC浏览器的内核”的方式，如果不存在符合要求的UC浏览器，会初始化为系统内核。

***3.4.2 使用用户安装好的UC浏览器的内核***

这个方式是UC推荐的方式，由于UC浏览器具有巨大的用户量，这个方式能够很好覆盖App的用户，并且嵌入的成本最小，并且能够享受UC浏览器内核不断的升级。

这个方式的初始化调用方式：

UCCore.init(context, false, null, null, breakpadConfig);

（注：breakpadConfig的说明请看“Error: Reference source not found”。）

由于用户可能没有安装UC浏览器，或者安装的版本过低，UC WebView初始化之后可能无法调起UC内核，这时候UC WebView会转而使用系统WebView内核来提供功能而UCCore.init会抛出RuntimeException来告知用户。

***3.4.3 内置到自己的App里面***

如果App选择把UC WebView的内核内置到自己的安装包里面，其最大的好处就是能够摒弃所有系统WebView的兼容性代码，所有的业务逻辑都可以按UC WebView提供的功能来设计。

这个方式的初始化调用方式：

UCCore.init(context, false, dexFilePath, soFilePath, breakpadConfig);

如果在指定的位置没有找到jar文件，SDK会初始化为系统内核。

如果在dexFilePath指定的位置可以找到jar文件，而soFilePath没有找到so，则会导致程序崩溃。

***3.4.4 动态下载内核***

这个方法的初始化实际上与“内置到自己的App里面”类似，只需要在下载之后指定位置即可。

在没有下载之前，程序可以通过以下代码来尝试使用安装好的UC浏览器的内核:

UCCore.init(context, false, null, null, breakpadConfig);

也可以明确指定使用系统WebView：

UCCore.init(context, true, null, null, breakpadConfig);

需要注意的是，下载好的内核so和jar文件最好放置在/data目录下面，推荐在程序的安装目录下面，因为某些手机不允许加载放到sdcard的so和jar。

***3.4.5 判断是否是UC的内核***

用户创建WebView之后，可以通过webview.getUCExtension() != null的方式来判断是否是UC的内核。

**3.5 View相关API修改**

UC的WebView接口尽可能与系统的WebView一致。不过由于我们需要动态的切换系统内核和UC的内核实现，所以在View的层次上与系统WebView是不一样的。UC的WebView的结构是在系统的WebView和UC的WebView上面进行了一次封装。示意图如下：


 

Ucsdk中的api WebView（即对外可见）主要负责api提供，实际上其实际能力来自于子View（在view hierarchy里面，含有核心逻辑的 core WebView作为子节点添加到api WebView上，对外是不可见的），并且可以在代码中设置使用android自带或者uc提供的内核。基于这样的结构，部分逻辑的实现可能需要更改一下适配逻辑：

***Override WebView的函数***

由于com.uc.webview.export.WebView实际上是一个Wrapper View，所以如果需要Override内核View的函数不能直接进行。

可以通过继承api WebView并改写方法进行扩展，基于上图所示的内部结构，core WebView将部分方法通过提供api WebView对象以coreXXXXX命名方式暴露以供复写。

例如：如果需要在网页上显示一个类似fixed元素表现的广告条，那么你可以继承api WebView后复写coreDraw()方法，在完成super.coreDraw()调用后，在View上绘制所需的广告条。

类似的函数包括：

>      public void coreComputeScroll();

>      public void coreOnConfigurationChanged(Configuration newConfig);

>      public void coreOnVisibilityChanged(View changedView, int visibility);

>      public void coreOnScrollChanged(int l, int t, int oldl, int oldt);

>      public boolean coreDispatchTouchEvent(MotionEvent ev);

>      public void coreDraw(Canvas canvas);

>      public void coreDestroy();

>      public void coreSetVisibility(int visibility);

>      public void coreRequestLayout();

***使用扩展的功能***

WebView除了标准的Android api以外，其他非原生api都被归集到getExtension()获取的子对象中，下列方法除外：

>      View getCoreView();

>      Int getCoreType();

***视图（View）相关的操作可能需要改变***

由于api WebView是core WebView的Parent View，考虑到视图排版api对渲染的影响，要获取真正显示网页内容的视图相关属性，就不能直接获取api WebView的值，需要经由WebView.getCoreView().getXXXX()获取。下面是一个应用场景：

 

>      //获取网页滚动位置

>      com.uc.webview.export.WebView wv= new com.uc.webview.export.WebView(ctx);

>      wv.loadUrl("http://uc.cn/");

>      wv.getCoreView().getScrollY(); //注意不是wv.getScrollY()

**3.6 JavascriptInterface适配**

由于UCSDK同时支持UC WebKit和系统webkit，给接口添加@JavascriptInterface时最好参考以下代码：

>      @com.uc.webview.export.JavascriptInterface

>      @android.webkit.JavascriptInterface

>      public void callTest() {

**3.7 AndroidManifest.xml里加入权限声明**

>      <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

>      <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />

>      <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />

>      <uses-permission android:name="android.permission.INTERNET" />

>      <uses-permission android:name="android.permission.READ_PHONE_STATE" />

4扩展功能
----

UC WebView SDK通过两个类来提供扩展接口，一个是UCCore，一个是UCExtension。扩展功能目前都只能在初始化为UC内核之后才能使用。

**4.1 初始化相关**

***4.1.1 UCCore.init***

>请看“Error: Reference source not found”

注：init初始化如果fallback到系统webview会抛RuntimeException，所以调用该函数需要try catch。

***4.1.2 WebView.getCoreType***

>      public static int getCoreType()

>      Returns: 当前内核类型。CORE_TYPE_ANDROID表示系统内核，CORE_TYPE_U3表示UC内核。

***4.1.3 WebView.getCoreView***

>      public View getCoreView()

返回内核的View，以下行为应该使用内核的View：

Scroll位置相关的View接口，如getScrollX()、getScrollY()、scrollTo()、setHorizontalScrollBarEnabled、setVerticalScrollBarEnabled。

setOnLongClickListener、setOnTouchListener、dispatchTouchEvent.

**4.2 UCExtension**

UCExtension是UC内核定义的扩展。通过API获得。

>      public UCExtension getUCExtension()

>在系统内核的状态下，这个API返回NULL。

***4.2.1 UCClient***

UCExtension中可以设置一个额外的Client，用于获取更多的内核回调。

>      void setClient(UCClient client)

***UCClient包括以下回调***

|void|onFirstLayoutFinished(boolean isPreread, String url) 第一次排版结束后回调|
| ------------- |-------------| 
|void|onFirstVisuallyNonEmptyDraw() 第一次有内容绘制|
|void|onFirstWebkitDraw() 第一次draw结束|
|void|onGeneralPermissionsShowPrompt(Map<String,String> params,显示一个通用的确认对话框|
|void|onMoveCursorToTextInput(UCClient.MoveCursorToTextInputResult result) 当一个输入框获取光标的时候，内核会回调这个函数来告知这个光标是否存在上一个/下一个输入框。 并且如果是通过UCExtension.moveCursorToTextInput(int)移动光标焦点的话，还通过UCClient.MoveCursorToTextInputResult.mSuccess通知结果。|
|void|onPostUploadProgress(WebView view, int fileAmount, int fileIndex, long fileSize, long uploadSize) 上传文件进度通知。|
|void|onWifiSafePolicy(WebView webView, IGenenalSyncResult result) wifi 安全策略回调，用户必须通过result返回结果|
|void|showWebCoreTips(int tipsId, int millisecond) 显示提示信息|



***4.2.2 注入JS***

WebView提供的标准API可以使用evaludateJavascript或者loadUrl函数来注入JS，不过这些JS注入没有办法保证在页面的head标签里面生效，UC内核提供在Head标签开始的时候注入JS的方式。

void setInjectJSProvider(UCExtension.InjestJSProvider listener,

int type)

设置JS注入监听器

Parameters:

listener -

type, - only InjectJSProvider#HEAD_START supported so far.

See Also:

>      UCExtension.InjectJSProvider的定义
>      public interface InjectJSProvider {

>      public static final int TYPE_HEAD_START = 1;

>      /**

>      * 获取要注入的js内容

>      * @param type

>      * @return

>      */

>      public String getJS(int type);

>      }

**4.3 UCCore**

UC内核扩展对象 用于处理内核的全局调用, 这个类的所有函数必须在UC内核初始化成功后才能调用，否则会产生NullPointerException

***4.3.1 Custom Location Service***

可以通过UCCore下面的setLocationManager函数来设置自定义的Location Service Provider。

ILocationManager 里面的函数定义，请参考Android API里面LocationManager的定义。

public static void setLocationManager(ILocationManager locationManager)

设置自定义的位置的服务

Parameters:

locationManager - 自定义的简单LocationManager

>      public interface ILocationManager {

>      public void requestLocationUpdates(String provider, long minTime, float minDistance, LocationListener listener);

>      public void requestLocationUpdatesWithUrl(String provider, long minTime, float minDistance, LocationListener listener , String url);

 

>      public void removeUpdates(LocationListener listener);

>      }

**4.4 崩溃日志收集**

UC SDK内置详细的Native崩溃日志生成功能，我们建议合作伙伴开启这个功能，以帮助发生崩溃后UC这边定位问题。

如果需要启用这个崩溃日志收集功能，可以使用在上述init过程构造BreakpadConfig 参数传入。Breakpad初始化可参考以下代码：

>      BreakpadConfig breakpadConfig = new BreakpadConfig(getApplicationInfo().dataDir);

>      UCCore.init(this, false, null, null, breakpadConfig);

 
**4.5 UCSettings**

是一个扩展的UC专属设置项，里面有两种函数，成员函数和静态函数。

成员函数用于设置某一个webview的属性，静态函数设置uc内核的全局属性。

成员函数：

>      public abstract void setForceUCProxy(boolean enable);

>      public abstract void setUCCookieType(int type);

>      ...

使用例子:

>      mWebView.getUCExtension().getUCSettings().getUCCookieType()

 

 

全局函数：

>      public static void setNightMode(boolean enable);

>      ...

 

>      public static void setGlobalEnableUCProxy(boolean enable)//是否全局打开中转，如果false全面关闭中转

>      public static void setEnableAdblock(boolean enable)//是否打开广告过滤

>      public static void setEnableDispatcher(boolean enable)//是否禁用dispatch，如果不用中转的话，可以设置为false

>      public static void setEnableMultiThreadParser(boolean enable) //是否打开多线程解析，适用复杂页面

具体例子如下：

>      UCSettings.setEnableMultiThreadParser();

 

 
5应用案例
----

**5.1 一个简单的浏览器**

参考UCSDKBrowser案例

**5.2 上传文件适配**

参考UCSDKBrowser案例中SDKBrowserActivity.MyWebChromeClient.onShowFileChooser.

**5.3 错误页面定制**

如果需要定制自己的错误页面，首先需要设置使用自定义的错误页面。

设置项是：UCSettings::setEnableCustomErrorPage

一、网络问题或者域名错误问题，这种情况下会收到WebViewClient::onReceivedError的回调。

可以在该回调中，根据错误信息生成自定义的错误页面的html数据，并使用loadDataWithBaseURL加载这个错误页面。可参考以下代码：

>      服务器返回错误
>      @Override
>      public void onReceivedError(WebView view, int errorCode, String description, String failingUrl) {
>          String errorHtml = createErrorPageHtml(errorCode, description, failingUrl); //通过错误码等信息构建自己想要的错误页面，生成html代码。
>          if (UCSettings.isEnableCustomErrorPage())
>              view.loadDataWithBaseURL(failingUrl, errorPageHtml, "text/html", "UTF-8", failingUrl);
>      }

二、服务器返回错误(例如404)。这种情况可以在接口UCClient::onWillInterceptResponse中判断错误码，并生成加载自己的错误页面。可参考以下代码：

>      网络问题或域名错误
>      class MyUCClient extends UCClient {
>          WebView mWebview;
>          //Context mContext;
>          Handler mHandler;
>          public MyUCClient(WebView webview, Handler handler){
>              mWebview = webview;
>              mHandler = handler;
>          }
>          public boolean onWillInterceptResponse(HashMap<String, String> hashMap) {
>              String statusCode = hashMap.get("httpcode");
>              android.util.Log.d("","intercept response client code="+statusCode);
>              if("404".equals(statusCode)){
>                  mHandler.post(new Runnable(){
>                      public void run(){
>                          mWebview.loadUrl(ALIPAT_URL);
>                      }
>                  });
>                  return true;
>              }
>              return false;
>          }

6.详细的API文档
----

请参考reference.zip


7.意见和建议
----

有关api的意见和建议，可以email至：u3_standard@ucweb.com。