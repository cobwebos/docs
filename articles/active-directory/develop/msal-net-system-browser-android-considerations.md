---
title: Xamarin Android 系统浏览器注意事项 （MSAL.NET） |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解在 Xamarin Android 上使用系统浏览器与 Microsoft 身份验证库用于 .NET （MSAL.NET） 的注意事项。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132615"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android 系统浏览器使用MSAL.NET

本文讨论了在 Xamarin Android 上使用 Xamarin Android 上的系统浏览器以及用于 .NET （MSAL.NET）的 Microsoft 身份验证库时应考虑的事项。

从 MSAL.NET 2.4.0 预览版开始，MSAL.NET支持 Chrome 以外的浏览器。 它不再需要在 Android 设备上安装 Chrome 进行身份验证。

我们建议您使用支持自定义选项卡的浏览器。 下面是这些浏览器的一些示例：

| 支持自定义选项卡的浏览器 | 包名称 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

除了识别支持自定义选项卡的浏览器外，我们的测试还表明，一些不支持自定义选项卡的浏览器也可用于身份验证。 这些浏览器包括歌剧，歌剧迷你，浏览器和Maxthon。 

## <a name="tested-devices-and-browsers"></a>测试设备和浏览器
下表列出了已测试身份验证兼容性的设备和浏览器。

| 设备 | 浏览者     |  结果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome\* | 通过|
| Huawei/One+ | Edge\* | 通过|
| Huawei/One+ | Firefox\* | 通过|
| Huawei/One+ | Brave\* | 通过|
| One+ | Ecosia\* | 通过|
| One+ | Kiwi\* | 通过|
| Huawei/One+ | Opera | 通过|
| Huawei | OperaMini | 通过|
| Huawei/One+ | InBrowser | 通过|
| One+ | Maxthon | 通过|
| Huawei/One+ | DuckDuckGo | 用户取消身份验证|
| Huawei/One+ | UC 浏览器 | 用户取消身份验证|
| One+ | Dolphin | 用户取消身份验证|
| One+ | CM 浏览器 | 用户取消身份验证|
| Huawei/One+ | 未安装 | Android 活动未发现异常|

\* 支持自定义标签页

## <a name="known-issues"></a>已知问题

如果用户在设备上未启用浏览器，MSAL.NET将引发`AndroidActivityNotFound`异常。  
  - **缓解**：要求用户在其设备上启用浏览器。 建议支持自定义选项卡的浏览器。

如果身份验证失败（例如，如果使用 DuckDuckGo 启动身份验证），MSAL.NET将`AuthenticationCanceled MsalClientException`返回 。 
  - **根问题**：设备上未启用支持自定义选项卡的浏览器。 使用无法完成身份验证的浏览器启动身份验证。 
  - **缓解**：要求用户在其设备上启用浏览器。 建议支持自定义选项卡的浏览器。

## <a name="next-steps"></a>后续步骤
有关详细信息和代码示例，请参阅在 Xamarin Android 和[嵌入式与系统 Web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)[上的嵌入式 Web 浏览器和系统浏览器之间进行选择](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  