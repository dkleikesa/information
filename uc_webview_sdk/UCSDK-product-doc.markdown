1.前言
----
**1.1编写目的**

本文档为UC浏览器开放SDK的产品说明文档，其编写目的是帮助第三方合作商更好、更快、更全面的了解UC SDK优势与性能。

**1.2面向人员**

本文档面向第三方合作商。

**1.3 UC SDK介绍**

UC浏览器为第三方合作商提供了丰富的API。
第三方合作商接入UC SDK后，即可换上一个强劲的Web引擎，让基于Android的移动应用能够拥有快速高效的浏览体验，UC浏览器强大的扩展服务，事半功倍的Web接入等。

2.UC SDK效能数据
----
**2.1网页打开性能（T1，T2）**

网页打开性能是UC浏览器核心关注的性能指标，通过实验室测试和线上数据进行指标监控，问题发现。实验室测试能进行竞品对比测试，以下主要是介绍实验室测试的方式（WPT）以及数据情况。其中T1，T2是2个主要衡量指标，具体含义如下：

T1:页面第一次显示了可视化内容的时间，即页面刚刚出现内容的时间

T2:显示的内容占满了屏幕的时间，不包括显示屏幕外的内容的时间

***2.1.1Chrome/UC浏览器/QQ浏览器 对比***

测试手机：小米3（4.4.4）

测试版本：Chrome（自带浏览器），UC浏览器，QQ浏览器

测试环境：直连下wifi访问

测试方法：采用摄像头截图（精度为40ms 左右），计算图片饱和度，超过20%为T1，超过90%为T2。

测试结果计算：测试30次，取切尾均值

误差说明：T1为100ms，T2为200ms，即两者绝对值在误差范围内为持平。（网络测试受到手机状态，网络状态，服务器状态影响，存在一定的误差存在。）

![d1](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/b725abb028/d1.png)
![d2](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/c537a6ba50/d2.png)

由T1，T2折线图可以看出，UC浏览器在T1/T2的表现能力略胜QQ浏览器，总体基本持平，两者相比Chrome均有较大优势。

***2.1.2 UC SDK/System Webview 对比***

测试手机：小米2S（4.2.2）

测试版本：UC SDK，System Webview（UC SDK切换System Webview）

测试环境：直连下wifi访问

测试方法：采用摄像头截图（精度为40ms 左右），计算图片饱和度，超过20%为T1，超过90%为T2。

测试结果计算：测试30次，取切尾均值

误差说明：T1为100ms，T2 为200ms，即两者绝对值在误差范围内为持平。（网络测试受到手机状态，网络状态，服务器状态影响，存在一定误差。）

![d3](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/4b6449fdb5/d3.png)
![d4](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/ab167ab695/d4.png)

由T1，T2折线图可以看出，UCSDK相对System Webview性能体验上有明显优势。

**2.2稳定性**

浏览器内核稳定性是一个关键指标，由于内核逻辑复杂，稳定性一直是一个老话题，UC浏览器针对稳定性做了很多问题修复和疑难问题攻关；对比同是使用webkit内核的android webview，UC修复很多webkit已知的在android上面的稳定性问题。

UC浏览器有一套完整的线上稳定性监控系统，在版本发布前，每天进行稳定性集成自动化测试。

测试版本：UC浏览器，系统浏览器（如果没有，则用预装的chrome代替）

测试方法：Monkey脚本同时跑自带浏览器和UC浏览器，监控native crash，一旦发现crash则测试结束，否则一直运行到任务结束（设定运行次数1000000次）

测试脚本：adb shell monkey -p com.UCMobile -p com.android.browser --throttle 300 --monitor-native-crashes -v 1000000 （如无系统浏览器 则用 -p com.android.chrome代替）

|测试机型|手机配置|浏览器对比|结果|
| :----------: |:----------:|:---------:|:-----|
|MB525|512M Android2.3|系统VS UC|正常跑完测试|
|华为荣耀3C|1G Android4.2.2|系统VS UC|系统浏览器发生native崩溃，导致Monkey进程终止，在6000多次时就崩溃，UC浏览器正常跑完|
|I9500|2G Android4.3|chrome VS UC|正常跑完测试|
|Nexus 5|2G Android4.4.4|chrome VS UC|Chrome浏览器发生native崩溃，导致Monkey进程终止，在9000多次时崩溃，UC浏览器正常跑完|

结论：在同等条件下进行monkey脚本测试，一般自带浏览器会更早产生崩溃。

参考结论：UC浏览器pad版本可以切换使用系统webview和UC内核。在使用UC内核的情况，从用户统计数据显示崩溃次数少于系统webview一半以上，但系统webview的用户量级较小，此处不列出具体数据。

**2.3渲染性能**

UC浏览器在渲染方面做不少改进和创新性优化，主要在算法、纹理处理流程和渲染架构等方面，从页面滚动的帧率对比可以有较明显的体现。

测试手机： Nexus 5（4.4.2）

测试版本： UC SDK，System Webview（UC SDK切换System Webview）

测试环境： wifi访问

测试方法： 打开systrace跟踪gfx(graphic)，view(view system)，webview加载测试页面，调用uiautomator的方法触发连续上下滑屏，读取trace的日志分析在滑屏时间内页面有多少是绘制动作，从而算出对应的帧率，所述流程由帧率自动化测试平台完成。

测试结果计算： 测试5次，取切尾均值

误差说明：帧率测试受页面打开状态，网络状态影响，存在一定误差。

![d5](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/9e17407a68/d5.png)
![d6](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/e64b79e59e/d6.png)

从总体看，无论快滑和慢滑，UC浏览器都比自带浏览器流畅，帧率普遍高于自带。

**2.4内存占用**

测试手机： Nexus 5（4.4.4）

测试版本： UC SDK，System Webview（UC SDK切换System Webview）

测试方法： 在启动浏览器，打开URL，拖拽操作，置入后台四种场景中，持续对内存进行监控，并输出多组稳定内存数据。

测试结果计算：测试5次，去除最大最小值，其余3次取均值

误差说明：内存测试受网络，页面打开状态，系统内存分配（小）影响，存在一定误差。

![d7](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/c1db053b61/d7.png)
![d8](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/9271343eb4/d8.png)
![d9](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/f78a49f2a9/d9.png)
![d10](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/ada1efbaf1/d10.png)
![d11](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/6b0281c2f3/d11.png)

从上述系列图表，基本可得出如下结论：

UC SDK与System Webview在内存占用方面，大部分场景下数据基本持平，置入后台场景UC SDK具有较大优势。

在Canvas站点中，UC SDK因开启硬件加速，内存占用会明显高于System Webview。

**2.5流量消耗**

测试手机：小米2S（4.2.2）

测试版本： 系统浏览器，UC SDK，System WebView（UC SDK切换System WebView）

测试环境：wifi访问，各浏览器有省流加速则开启，无则默认配置

测试方法： 启动浏览器，等待20s，打开url，开始截包（只截取TCP及非127.0.0.1数据），20s停止截包，计算截包大小。

测试结果计算：测试10次，取切尾均值

误差说明：理论上无误差

![d12](http://aligitlab.oss-cn-hangzhou-zmf.aliyuncs.com/uploads/uc-webview-sdk/public-docs/fa3e2fa219/d12.png)

从总体上看，UC SDK流量消耗胜于System Webview，最高省流比例达到30%，如sports.sina.cn。

3.UC SDK接入方式
----

UC浏览器提供三种接入方式，以满足第三方合作商的不同需求。

**3.1 UC提供完整的SDK：**

提供的SDK包括Jar和So，App把Jar和So打包到APK里面，一起发布。

APK增加Size：根据所选择的功能，6MB到10MB不等。

**3.2 UC提供两部分内容，一部分是接口部分实现，一部分是动态加载：**

接口部分为200KB 到300KB的 Jar文件，由App打入Apk里面发布；

动态加载部分包括实现的Jar文件和So文件。通过动态下载Jar和So的方式来实现UC Webview的功能，在没有下载之前，UC SDK自动Fallback到系统Webview。

**3.3 UC只提供接口：**

接口为200KB 到300KB的 Jar文件，由App打入Apk里面发布；SDK检测如果手机上安装了UC浏览器，则调用已安装的UC浏览器上的So和Jar文件来创建UC的 Webview；否则UC SDK自动Fallback到系统Webview。

4.合作案例
----
**4.1手机淘宝**

接入方式：上述第三种

UC SDK为其定制扩展WebView的API，提供全面托管网络的能力。（Doing）

**4.2支付宝**

接入方式：上述第一种

UC SDK为其提供Cookie加密等安全方面的定制。（Done）

**4.3 yunOS**

接入方式：上述第一种

UC SDK为其定制提前注入JS代码的接口。（Done）

UC SDK在内核为其抛出geolocation 适配接口。（Done）