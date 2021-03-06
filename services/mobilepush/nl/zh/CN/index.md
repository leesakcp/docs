---

copyright:
 years: 2015, 2016

---

{:new_window: target="_blank"}
{:shortdesc: .shortdesc}
{:screen:.screen}
{:codeblock:.codeblock}

# {{site.data.keyword.mobilepushshort}} 入门
{: #gettingstartedtemplate}
上次更新时间：2016 年 10 月 17 日
{: .last-updated}

{:shortdesc}

{{site.data.keyword.mobilepushshort}} 服务提供统一平台来发送和管理针对 iOS 和、Android 移动平台、Google Chrome、Mozilla Firefox Web 浏览器和 Google Chrome Apps and Extensions 的移动和 Web 推送通知。
{{site.data.keyword.mobilepushshort}} 服务可管理应用程序用户到设备的映射、管理设备平台、Web 浏览器以及处理向用户分派推送通知。使用此服务，作为推送通知，向移动和 Web 浏览器应用程序用户发送广播、单点广播（基于 deviceID）和标记（或主题）。还可以使用 SDK 和 [REST API](https://mobile.{DomainName}/imfpush/) 来进一步开发您的客户机应用程序。

本部分描述了如何设置基本推送通知。使用基本通知时，通知为广播，而不是使用标记发送给一组特定用户。

1. [配置通知提供者凭证](t__main_push_config_provider.html)
2. [使移动应用程序能够接收通知](c_enable_push.html)
3. [发送基本通知](t_send_push_notifications.html)

# 相关链接
{: #rellinks}

* [概述](c_overview_push.md){: new_window}

## 教程和样本 {:id="samples"}
{: #samples}
* [Android helloPush 样本应用程序](https://github.com/ibm-bluemix-mobile-services/bms-samples-android-hellopush/){: new_window}
- [Cordova 样本应用程序](https://github.com/ibm-bluemix-mobile-services/bms-samples-cordova-hellopush){: new_window}
* [iOS helloPush 样本应用程序 (Obj-C)](https://github.com/ibm-bluemix-mobile-services/bms-samples-ios-hellopush/){: new_window}
* [iOS helloPush 样本应用程序 (Swift)](https://github.com/ibm-bluemix-mobile-services/bms-samples-swift-hellopush){: new_window}

## SDK
{: #sdk}
* [Android SDK](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-android-push){: new_window}
* [Cordova SDK](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-cordova-plugin-push){: new_window}
* [iOS SDK (Obj-C)](https://hub.jazz.net/git/bluemixmobilesdk/imf-ios-sdk/archive?revstr=master){: new_window}
* [iOS SDK (Swift)](https://codeload.github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/zip/master){: new_window}

## API 参考
{: #api}
* [Push API 参考 (Android)](https://classicdocs.ng.bluemix.net/docs/api/content/api/mobilefirst/android/push-api-doc/overview-summary.html){: new_window}
* [IMFPush API 参考 (iOS)](https://classicdocs.ng.bluemix.net/docs/api/content/api/mobilefirst/ios/IMFPush_api-doc/html/index.html){: new_window}
* [BMSPush API 参考 iOS (Swift)](https://github.com/ibm-bluemix-mobile-services/bms-clientsdk-swift-push/blob/development/Apple Docs/index.html){: new_window}
* [REST API 参考](https://mobile.{DomainName}/imfpush/){: new_window}
