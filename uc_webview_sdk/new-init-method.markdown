>      // 设置从服务器下发的配置字符串（该字符串可用于动态配置uc内核的行为，如禁用某些不稳定的模块等）
>      UCCore.setParam(paramString);    
 
>      UCCore
>          // 支持app context避免泄露Activity
>          .setup(UCCore.OPTION_CONTEXT, this.getApplicationContext())
>          // 使用系统内核（根据需要强制使用系统内核）
>          .setup(UCCore.OPTION_USE_SYSTEM_WEBVIEW, useSystemWebView)
>          // 是否打开硬件加速（默认还是打开）
>          .setup(UCCore.OPTION_HARDWARE_ACCELERATED, mUseHardwareAC)
>          // 是否加载前对java代码进行安全校验
>          .setup(UCCore.OPTION_VERIFY_POLICY, UCCore.VERIFY_POLICY_ALL)
>          // 是否使用uc所提供的breakpad组件生成崩溃日志，如果为否，则不设置或者设置为null即可
>          .setup(UCCore.OPTION_BREAKPAD_CONFIG, new BreakpadConfig(this.getApplicationInfo().dataDir))
>          // 加载策略：仅对指定目录进行加载
>          .setup(UCCore.OPTION_LOAD_POLICY, UCCore.LOAD_POLICY_SPECIFIED_ONLY)
>          // 指定dex文件目录
>          .setup(UCCore.OPTION_DEX_FILE_PATH, dexPath)
>          // 指定so文件目录（如果另外存放so文件）
>          .setup(UCCore.OPTION_SO_FILE_PATH, soPath)
>          // 创建WebView时，等待初始化完成
>          .setup(UCCore.OPTION_WEBVIEW_POLICY, UCCore.WEBVIEW_POLICY_WAIT_UNTIL_LOADED)
>          // UC初始化完成事件回调（可在此执行仅供UC内核的逻辑）
>          .onEvent("switch", new ValueCallback<SetupTask>() {
>              @Override
>              public void onReceiveValue(SetupTask value) {
>                  Log.d("UCCore.init", "WebView内核初始化完成，内核类型为UC");
>              }
>          })  
>          // UC初始化异常事件回调（可在此收集异常信息供后续解bug）
>          .onEvent("exception", new ValueCallback<SetupTask>() {
>              @Override
>              public void onReceiveValue(SetupTask value) {
>                  Log.w("UCCore.init", "WebView内核初始化异常 exception: " + (value.getException() != null ? value.getException().getMessage() : ""));
>              }
>          })
>          // 开始异步线程工作
>          .start();
 
 
>      WebView wv = new WebView(context);   //该过程将等待初始化过程的完成才返回。
>      if(wv.getCurrentViewCoreType() != CORE_TYPE_ANDROID) {  // 此处可获取当前webview类型
>          // uc内核
>      }