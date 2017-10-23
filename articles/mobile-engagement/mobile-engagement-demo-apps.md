---
title: "Azure Mobile Engagement 演示应用 | Microsoft Docs"
description: "描述从何处下载 Azure Mobile Engagement 演示应用、如何进行使用以及使用它有哪些好处"
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: f624d5aa-254b-4ad0-96a3-f00e6c3a2c97
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2016
ms.author: piyushjo
ms.openlocfilehash: 8381edb569e19a85c1259f7791b477cfa6e51ea3
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="azure-mobile-engagement-demo-app"></a>Azure Mobile Engagement 演示应用
我们已经发布适用于 **iOS**、**Android** 和 **Windows** 平台的 Azure Mobile Engagement 演示应用，从而帮助你找到有用的资源并了解有关 Mobile Engagement 的详细信息。

此应用可帮助你：

* 轻松找到 Mobile Engagement 资源（如视频、文档、支持论坛以及在何处可以提交功能请求）的有用链接。
* 体验 Mobile Engagement 所支持的示例通知，从而设计自己的移动应用程序。
* 利用参考实现来了解如何在自己的应用中实施 Mobile Engagement。 可以了解如何：
  
  * 收集分析数据。
  * 实施不同类型的高级通知方案，如*全屏插播式*或*弹出式*。
  * 执行调查和轮询。
  * 实施无提示推送数据和推送方案。   

## <a name="app-installation"></a>应用安装
本应用可从以下应用商店获得：

* **Windows Universal 演示应用**：
  
  * 在 [Windows 应用商店](https://www.microsoft.com/en-us/store/apps/azure-mobile-engagement/9nblggh4qmh2)下载此应用。
  * 此应用作为 Windows 10 Universal 应用而开发。 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-windows) 上提供了源代码。
* **iOS 演示应用**：
  
  * 在 [Apple Store](https://itunes.apple.com/us/app/azure%20mobile%20engagement/id1105090090) 下载此应用。
  * 此应用在 IOS Swift 中开发。 [GitHub](https://github.com/Azure/azure-mobile-engagement-app-ios) 上提供了源代码。
* **Android 演示应用**：
  
  * 在 [Google Play 商店](https://play.google.com/store/apps/details?id=com.microsoft.azure.engagement)下载此应用。
  * [GitHub](https://github.com/Azure/azure-mobile-engagement-app-android) 上提供了源代码。

![Windows Universal 演示应用][1]

![iOS 演示应用][2]
![Android 演示应用][3]

## <a name="usage"></a>使用情况
可以按以下方式使用此应用：

**从前面提供的应用商店链接将应用下载到设备：**

> [!IMPORTANT]
> 不需要 Azure 帐户或将应用连接到后端。 该应用可以独立运行。
> 
> 

* 在设备上安装应用后，可以浏览左侧菜单中的链接，查找有关 Mobile Engagement 的有用资源。
* [服务的 RSS 源](https://aka.ms/azmerssfeed)已添加到此应用程序，因此，始终都会获得最新的产品更新。
* 还可以浏览示例通知方案，以体验 Mobile Engagement 针对不同平台所支持的通知类型。 在本地即可体验这些通知，也就是说可以单击屏幕上的按钮来展示通知体验，这就相当于从 Mobile Engagement 平台发送通知。

![适用于 Windows 的应用菜单][4]

![适用于 iOS 的应用菜单][5]
![适用于 Android 的应用菜单][6]

**从前面提供的 GitHub 链接下载源代码：**

* 下载源代码后，在相应的开发环境（XCode for iOS、Android Studio for Android 和 Visual Studio for Windows XCode）中打开它。
* 接下来遵循 [SDK 集成的基本步骤](mobile-engagement-windows-store-dotnet-get-started.md)，以便能将此应用连接到其自身的 Mobile Engagement 后端实例。
  * 需要在应用中配置连接字符串。
  * 还需要为应用配置推送通知平台。
* 你会注意到，应用本身已在 Mobile Engagement 的检测之下。 因此，将应用连接到后端之后再打开该应用时，可以在“监视”选项卡上看到用户会话、活动、事件等等。
* 还可以从自己的 Mobile Engagement 实例将通知发送到此应用，而不必使用本地通知。
  
  * 此处可以使用应用中的“获取设备 ID”菜单项添加设备作为测试设备。 这样你就有了一个设备 ID 来将测试设备注册到平台后端实例。
    
    ![Windows 上的设备 ID][7]
    
    ![iOS 上的设备 ID][8]
    ![Android 上的设备 ID][9]

## <a name="key-features-of-the-demo-app"></a>演示应用的主要功能
* 如前所述，有了此应用，也就掌握了 Mobile Engagement 的所有关键资源。 可以浏览左侧菜单上的链接。
* 可以体验每个平台的应用外通知。 可以将这些通知以“仅通知”的形式发送，即单击通知只会打开应用程序的本机屏幕（通过使用“深层链接”）；或者以“Web 通知”的形式发送，即单击通知时，可以从 Mobile Engagement 后端传送将要显示的其他 HTML 内容。
  
    ![应用外通知][29]
* 在 iOS 中，必须关闭应用才能查看应用外通知或系统推送通知。 可以在此处查看如何添加**操作按钮**，比如那些添加到此应用外通知以用于*反馈*和*共享*的按钮（以便用户可以直接根据通知本身采取相应的操作）。
  
    ![iOS 中的应用外通知][11] ![iOS 中显示的应用外通知][14]
* 在 Android 中，支持的选项包括将多行文本（**大文本**）或通知图像（**大图像**）添加到通知中，同时还会添加**操作按钮**（只要 iOS 支持）。
  
    ![Android 中的应用外通知][12] ![Android 中显示的应用外通知][15]
* 在 Windows 10 中，可以查看通知在电脑上的显示方式。 此通知还会显示在 Windows 10 的**通知中心**。 Windows SDK 暂不支持添加**操作按钮**。
  
    ![Windows 中的应用外通知][10] ![Windows 中显示的应用外通知][13]
* 可以体验每个平台的默认“应用内”通知。 这是一个两步式体验：首先显示一个“通知”窗口。 单击该窗口会打开一个全屏“公告”，如下面的屏幕截图所示。 此公告的内容来自于 Mobile Engagement 后端实例。 SDK 同时提供了通知模板和公告模板。 可以按照本演示应用所示，通过添加徽标和着色轻松自定义模板。  
  
    ![Windows 中的应用内通知][16]
  
    ![iOS 中的应用内通知][17]  ![Android 中的应用内通知][18]
  
    **iOS**，**Android**
* 还可以使用 Mobile Engagement 的“类别”功能自定义该默认体验。 在演示应用中，我们演示了两种更改通知体验的常用方法。 请注意，Windows SDK 暂不支持“类别”功能。
  
    **全屏插播式：**
  
    ![应用内通知 - 插播式类别][30]
  
    ![iOS 中的插播式类别][21]     ![Android 中的插播式类别][22]
  
    **弹出式通知：**
  
    ![应用内通知 - 弹出式类别][31]
  
    ![iOS 中的弹出式通知][19]    ![Android 中的弹出式通知][20]

**iOS**，**Android**

* Mobile Engagement 还支持一种叫做“轮询”的特殊应用内通知类型。 该通知允许向细分的应用用户发送快速调查。 可以针对每个问题添加额外的问题和选项，如下面的屏幕截图中所示。 这些内容将作为应用内通知显示给应用用户。   
  
    ![“轮询”通知][32]
  
    ![Windows 中的调查][26]
  
    ![iOS 中的调查][27]   ![Android 中的调查][28]

**iOS**，**Android**

* Mobile Engagement 还支持发送无提示**数据推送**通知。 通过这些通知，可以发送来自服务的数据（如下例中的 JSON 数据），然后可以在应用中处理这些数据，并执行某个操作。 比如通过使用数据推送通知有选择地更改某个商品的价格。
  
    ![数据推送通知][33]
  
    ![Windows 中的数据推送通知][23]
  
    ![iOS 中的数据推送通知][24]  ![Android 中的数据推送通知][25]

**iOS**，**Android**

> [!NOTE]
> 可以通过单击任意示例通知屏幕上的“单击此处了解如何从 Mobile Engagement 平台发送这些通知的说明”，查看详细的逐步说明。
> 
> 

[1]: ./media/mobile-engagement-demo-apps/home-windows.png
[2]: ./media/mobile-engagement-demo-apps/home-ios.png
[3]: ./media/mobile-engagement-demo-apps/home-android.png
[4]: ./media/mobile-engagement-demo-apps/menu-windows.png
[5]: ./media/mobile-engagement-demo-apps/menu-ios.png
[6]: ./media/mobile-engagement-demo-apps/menu-android.png
[7]: ./media/mobile-engagement-demo-apps/device-id-windows.png
[8]: ./media/mobile-engagement-demo-apps/device-id-ios.png
[9]: ./media/mobile-engagement-demo-apps/device-id-android.png
[10]: ./media/mobile-engagement-demo-apps/out-of-app-windows.png
[11]: ./media/mobile-engagement-demo-apps/out-of-app-ios.png
[12]: ./media/mobile-engagement-demo-apps/out-of-app-android.png
[13]: ./media/mobile-engagement-demo-apps/out-of-app-display-windows.png
[14]: ./media/mobile-engagement-demo-apps/out-of-app-display-ios.png
[15]: ./media/mobile-engagement-demo-apps/out-of-app-display-android.png
[16]: ./media/mobile-engagement-demo-apps/in-app-windows.png
[17]: ./media/mobile-engagement-demo-apps/in-app-ios.png
[18]: ./media/mobile-engagement-demo-apps/in-app-android.png
[19]: ./media/mobile-engagement-demo-apps/pop-up-ios.png
[20]: ./media/mobile-engagement-demo-apps/pop-up-android.png
[21]: ./media/mobile-engagement-demo-apps/interstitial-ios.png
[22]: ./media/mobile-engagement-demo-apps/interstitial-android.png
[23]: ./media/mobile-engagement-demo-apps/data-push-windows.png
[24]: ./media/mobile-engagement-demo-apps/data-push-ios.png
[25]: ./media/mobile-engagement-demo-apps/data-push-android.png
[26]: ./media/mobile-engagement-demo-apps/survey-windows.png
[27]: ./media/mobile-engagement-demo-apps/survey-ios.png
[28]: ./media/mobile-engagement-demo-apps/survey-android.png
[29]: ./media/mobile-engagement-demo-apps/out-of-app.png
[30]: ./media/mobile-engagement-demo-apps/in-app-interstitial.png
[31]: ./media/mobile-engagement-demo-apps/in-app-pop-up.png
[32]: ./media/mobile-engagement-demo-apps/notification-poll.png
[33]: ./media/mobile-engagement-demo-apps/notification-data-push.png
