>____
>
>* 如何实现视频全屏之后自动横屏?
>        
>         通过重载WebChromeClient.onShowCustomView 和 WebChromeClient.onHideCustomView() 实现。
> 
>____
>* 如何访问UCSDK的缓存文件?
>         
>         UCCore.getResponseByUrl(String url)函数可以实现这个功能  
>
>____
>* 程序启动之后报告“UnsatifiedLinkError, dlopen failed,"******.so is 32-bit instead of 64-bit. 如何处理？  
>
>         App没有任何一个so，安装到64位手机上后，系统以为这个App是64位的，导致dlopen failed，需要放一个32bit的so到App上。
>
>____
>* 指定目录dexDir 为/data/local/tmp，为什么在5.0系统上初始化不成功？
>
>         在5.0机器上此路径并不能正常的加载so。
>         Issue 79480: Android 5.0 linker doesn't load .so files from /data/local/tmp folder anymore.
>         https://code.google.com/p/android/issues/detail?id=79480
>
>____
>* File任意读写漏洞,com/uc/webview/business/a/d#run,存在内容被替换的风险，openFileOutput禁止使用MODE_WORLD_READABLE和MODE_WORLD_WRITABLE
>
>         这个是UC浏览器外壳才会使用到，通过浏览器服务端下发服务器参数，然后保证到文件供sdk使用，mode是MODE_WORLD_READABLE。
>         所以只要不是采用共享UC浏览器的方式使用WebView，都是不会执行这块的代码，即不会创建相关文件。
>
>____
>* pagecache打开的情况下，如何通过其他方式触发js脚本运行?
>
>         使用onpageshow事件，在有pagecache的情况下，前进后退可正常触发js脚本运行
> 
>_____
>* WebView不校验证书漏洞,Lcom/uc/webview/export/internal/a/h#proceed,调用了android/webkit/SslErrorHandler类的proceed方法，可能导致WebView忽略校验证书的步骤。
>
>         不要调用android.webkit.SslErrorHandler的proceed方法。
>
>____
>* UCCore初始化的第一个context参数应该传递ApplicationContext还是Activity的Context?
>
>         应该传递ApplicationContext
>
>____
>* 如何使用UCSDK的remote inspector 如何使用？
>
>         ucsdk的RI功能分如下两种使用方式：
>
>         1.a 链接USB线，使用adb forward tcp:9998 tcp:9998 命令进行端口定向
>         1.b 打开PC版Chrome/UC浏览器，输入 localhost:9998, 回车访问，即可进入调试界面
>
>         2.a 无需链接usb线，但保证PC与手机处于同一局域
>         2.b 打开打开PC版Chrome/UC浏览器，输入 "手机IP地址:9998",回车访问，即可进入调试界面
>
>____
>* UCSDK支持flex吗？
>
>         UC支持这一版flex http://www.w3.org/TR/2009/WD-css3-flexbox-20090723/#property 
>
>
>
>____
>* 使用WebView.getCoreType方法为什么会出现RunTimeException？
>
>         java.lang.RuntimeException: uninited exception. must invoke Core.init first.
>             at com.uc.webview.export.internal.d.p(ProGuard:492)
>             at com.uc.webview.export.internal.d.f(ProGuard:386)
>             at com.uc.webview.export.WebView.getCoreType(ProGuard:1404)
>             at com.uc.webview.export.internal.a.a.a$b.run(ProGuard:191)
>             at android.os.Handler.handleCallback(Handler.java:808)
>             at android.os.Handler.dispatchMessage(Handler.java:103)
>             at android.os.Looper.loop(Looper.java:193)
>             at android.os.HandlerThread.run(HandlerThread.java:61)
>         从上述堆栈可看出，调用getCoreType()之前，需要先初始化ucsdk
>
>____
>* UCSDK支持-webkit-filter: blur吗？
>
>         UCSDK的U3内核并不支持-webkit-filter: blur特性，后续提供的U4新内核可以完美支持。
>
>____
>* 接入UCSDK后为什么还是无法正常使用ucwebview？ 
>
>         原因1、没有找到对应的UCSDK内核资源
>         以下载方式部署、加载UC浏览器共享内核方式部署的情况下，需要确认对应内核是否存在，是否具备相应权限
>
>         原因2、接入的APPKey错误
>         重新审查申请的接入Key
> 
>         原因3、接入过程中，混淆了uc_webview_sdk.jar
>         需要保证keep住所有uc_webview_sdk.jar的类
>
>         原因4、运行的环境是64bit
>         运行应用的手机环境是64bit情况下，app无法正常使用UC浏览器提供的ucsdk内核，需要将app以32bit编译才可解决
>
>____
>* 如何才能更好的定位UCSDK初始化失败的问题？
>
>         UCSDK初始化的调试提供了一个输出日志的接口：UCCore.SetPrintLog(boolean arg) 
>         *使用时机需要在UCSDK初始化之前开启
>         *它的debug logcat tag 为：setup
>
>         它可以输出怎样的日志？    
>         eg. 1、9001:isArchCompatible return false.         //运行环境cpu架构不兼容
>         eg. 2、9002:isFrameworkCompatible return false.    //运行环境Android SDK Framework不兼容
>         eg. 3、9003:isUCSDKAppKeyCorrect return false.     //接入APPKey不正确    
>         eg. 4、9004:isVersionCompatible return false.      //加载内核版本不支持
>         eg. 5、9005:isConfigEnabled return false.          //UC浏览器不存在
>         eg. 6、9006:checkedSoSize return false.            //so大小异常
>
>
>
>____
>* 如何判断用的是UCWebView？
>
>         方法1、通过WebView.getCoreType()方法以Log形式输出内核类型，1为UCwebview，2为sys webview
>
>         方法2、通过UA判断是否带有UCBrowser关键字
>
>         方法3、通过在页面空白位置长按，检查是否弹出"扩选"、"复制"、"搜索"、"分享"的自由复制菜单
>
>____
>* 如何正确使用onWebViewEvent这个回调？
>
>         UCClient.onWebViewEvent(WebView var1, int var2, Object var3){}
>         参数int var2 定义了以下类型
>         public static final int WEBVIEW_EVEkNT_TYPE_LOADING_STATUS_START = 4;//start: 加载开始
>         public static final int WEBVIEW_EVENT_TYPE_LOADING_STATUS_T0 = 5;   //T0: 接收到主文档响应头
>         public static final int WEBVIEW_EVENT_TYPE_LOADING_STATUS_T1 = 6;   //T1: 绘制第一个元素
>         public static final int WEBVIEW_EVENT_TYPE_LOADING_STATUS_T2 = 7;   //T2: 第一次绘制满屏
>         public static final int WEBVIEW_EVENT_TYPE_LOADING_STATUS_T3 = 8;   //T3: 加载结束
>         public static final int WEBVIEW_EVENT_TYPE_EMPTY_SCREEN = 9         //页面白屏
>         当回调 int var2 = 9时，Object var3返回的信息就是白屏的间隔时间，需要将var3 转成int型。
>         只有当白屏的第三秒，第六秒，第九秒才会回调int var2 = 9 的类型。
>
>____
>* 为何中文系统上webview的菜单显示成英文？
>
>         接入应用存在其他模块会对Locale.setDefault(locale); 设置默认语言。
>         如设置成英文，则webview菜单就会显示成英文。
>____
>* UCSDK支持多少种系统语言？
>
>         [ar-sa, en-us, es-la, id, pt-br, ru, vi, zh-cn, zh-tw]
>         以上为UCSDK支持的语种





