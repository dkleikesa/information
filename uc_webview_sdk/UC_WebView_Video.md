UCSDK视频产品介
==
        本文主要介绍UCSDK视频,及如何接入和一些注意事项。

## 产品介绍
UCSDK提供两种视频显示方式:

>* 页内显示和独立View遮盖页内Video元素显示(简称遮盖显示)。
>* 默认采用的是遮盖显示。

## 初始化时设置页内显示方案
```sh
UCCore
       //其他配置                      
       .setup(UCCore.OPTION_VIDEO_HARDWARE_ACCELERATED, true)
       .start();
```

## 页内显示
页内显示基本原则是尊重前端页面实现，这里主要介绍全屏显示(非全屏与Android WebView一致)。
Android WebView视频全屏只是使得视频充满WebView，如果WebView是竖屏显示，视频只能竖屏显示，
而且WebView通常不是全屏显示的，所以视频也不是全屏的，具体显示依赖WebView Layout。
优点: UCSDK可以做到视频全屏,且屏幕切换到横屏.

>Note: 有些站点实现了的页内全屏，出于尊重页内实现的原则，这些站点无法做到横屏全屏.

____

## 遮盖显示

UCSDK创建了一个视频View遮盖了页内Video元素.

优点: 
* 所有的视频产品表现一致．不同站点前端页面视频实现不一样,所以页内显示不同站点视频产品表现会不一致。
* 良好交互体验．UCSDK视频交互同步了UC浏览器视频产品设计，用户体验可以得到保证。
* 全屏可以做到横屏全屏。

全屏切换不需要做任何适配,只需设置以下配置项。

```
UCSettings.setEnableUCVideoViewFullscreen(true);
```
缺点:
* 页内嵌入在Video元素内的内容会被遮住.






## 播放器解码器
UCSDK可以携带解码能力更强,解码效率更高的UC视频解码器.
添加UC视频解码器有两种方式:
* 把解码器so打到apk中。
* 动态加载。

只需增加以下初始化代码:
```
UCCore.updateUCPlayer(this.getApplicationContext(), UC视频解码器库URL, new Callable<Boolean>() {
                    @Override
                    public Boolean call() throws Exception {
                        return 根据网络状态返回是否需要下载UC视频解码器库;
                    }
                });
```

____
 
## 其他
## Back键退出视频全屏适配
```
public class MyWebChromeClient extends WebChromeClient {
    //视频进入全屏时会调用到onShowCustomView
    @Override
    public void onShowCustomView(View view, CustomViewCallback callback) {
       onShowCustomView(view, 0, callback);
    }
    @Override
    public void onShowCustomView(View view, int requestedOrientation,
            WebChromeClient.CustomViewCallback callback) {
        mCustomViewCallback = callback;
    }
    public void hideCustomView() {
        mCustomViewCallback.onCustomViewHidden();
        mCustomViewCallback = null;
    }
    //视频退出全屏时会调用到onHideCustomView
    @Override
    public void onHideCustomView() {
        hideCustomView();
    }
 
@Override
public boolean onKeyDown(int keyCode, KeyEvent event) {
    if (keyCode == KeyEvent.KEYCODE_BACK && event.getRepeatCount() == 0) {
        if (mChromeClient != null && mChromeClient.mCustomViewCallback != null) {
                //back键优先退出视频全屏
                mChromeClient.mCustomViewCallback.onCustomViewHidden();
            return true;
        }
        //页面前进后退等其他处理
    }
 }
```

## 前端页面实现建议
>
>如果采用页内显示方案，建议视频全屏显示交给客户端实现，并删除Video元素的controls属性。
>如果采用遮盖显示方案，poster图片尽量采用设置Video poster属性方式实现，其他页面嵌入图片方式可能会导致图片大小和Video元素大小不一致，从而导致视频独立View无法很好的遮盖"poster图片"了。
>

## 自动播放设置
视频播放默认需要用户触发，如果需要视频自动方法需要添加以下设置项：
```
settings.setMediaPlaybackRequiresUserGesture(false);
```