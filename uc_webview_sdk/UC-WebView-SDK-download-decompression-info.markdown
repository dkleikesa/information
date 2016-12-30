如下例子所示，UC WebView SDK提供了com.uc.webview.export.utility.UCMCoreDownloader类对zip类文件进行下载解压的功能封装。

**如何下载**

执行UCMCoreDownloader.downloadAndExtract(资源地址, 解压释放的目录, 成功回调, 异常暂停回调, 失败回调)将创建一个异步的下载任务，并在下载完成后执行解压，并在成功后回调通知。

下载过程产生了异常会调用异常回调并暂停下载，用户可主动调用abort()放弃或者resume()继续尝试。

因其他原因产生的异常（例如解压缩失败、文件写入失败）则会调用失败回调通知。

具体的接口形式如下所示：

>      public static void downloadAndExtract(final String url, final String dirPath, final ValueCallback<Void> successCallback, final ValueCallback<String> exceptionPausedCallback, final ValueCallback<String> failedCallback) throws Exception

示例代码

>      import com.uc.webview.export.utility.UCMCoreDownloader;
 
 
>      static final int RETRY_TIME = 2;    //替换为所需的异常中断重试次数
>      static final boolean IS_WIFI_NETWORK = true;    //替换为真实网络环境判断逻辑
>      static final String UCM_CORE_URL = "http://gdown.baidu.com/data/wisegame/65bd17490eeb22fd/UCliulanqi_168.apk";  //TODO: 替换为真实地址
 
>      //指定一个目录，下载并解压uc浏览器内核组件
>      public void downloadAndExtract(String ucmCoreDir) {
>          try {
>              UCMCoreDownloader.downloadAndExtract(UCM_CORE_URL, ucmCoreDir, new ValueCallback<Void>(){
>                  @Override
>                  public void onReceiveValue(Void ret) {
>                      Log.d("UCM", "download and extract success. ");
>                  }
>              }, new ValueCallback<String>(){
>                  int retryLeft = RETRY_TIME;
>                  @Override
>                  public void onReceiveValue(final String exceptionStr) {
>                      Log.d("UCM", "download paused because: " + exceptionStr + ", retryLeft:" + retryLeft );
>                      if (IS_WIFI_NETWORK)
>                          if (retryLeft--  >0)
>                              UCMCoreModuleDownloader.resume();
>                          else
>                              UCMCoreModuleDownloader.abort();   //注意这里是主动放弃，没有失败回调通知。
>                  }
>              }, new ValueCallback<String>(){
>                  @Override
>                  public void onReceiveValue(final String failedStr) {
>                      Log.d("UCM", "download and extract failed because: " + failedStr);
>                  }
>              });
>          } catch (Exception e) {
>              e.printStackTrace();
>          }
>      }
 
>      //主动暂停
>      public void pauseDownload() {
>          UCMCoreDownloader.pause();
>      }
 
>      //主动恢复（即重试、同一进程期间断点续传）
>      public void resumeDownload(){
>          UCMCoreDownloader.resume();
>      }
 
>      //放弃下载
>      public void abortDownload(){
>          UCMCoreDownloader.abort();
>      }

**没有可用的UC内核的回调**

>      com.uc.webview.export.extension.UCCore.setNotAvailableUCListener(NotAvailableUCListener listener)。

需要在UCCore.init之前设置，在init时，如果没有找到合适的uc内核，就会异步回调该接口，可以在该接口里面调用下载接口来下载uc内核的lib。

示例代码

>      UCCore.setNotAvailableUCListener(new NotAvailableUCListener() {
>          @Override
>          public void onNotAvailableUC(int type) {
>              downloadAndExtract(ucmCoreDir);
>          }
>      });

 

 