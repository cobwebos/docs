---
title: Xamarin Android 系统浏览器注意事项（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）的 Xamarin Android 系统浏览器的注意事项。
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
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132615"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android 系统浏览器使用 MSAL.NET 的注意事项

本文讨论在 Xamarin Android 上将系统浏览器用于 .NET 的 Microsoft 身份验证库（MSAL.NET）时应考虑的事项。

从 MSAL.NET 2.4.0 Preview 开始，MSAL.NET 支持除 Chrome 之外的浏览器。 不再需要在 Android 设备上安装 Chrome 即可进行身份验证。

建议使用支持自定义选项卡的浏览器。 下面是这些浏览器的一些示例：

| 支持自定义选项卡的浏览器 | 包名称 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org firefox|
|Ecosia | com.ecosia.android|
|猕猴桃 | com.kiwibrowser.browser|
|无畏 | com.brave.browser|

除了标识提供自定义选项卡支持的浏览器之外，我们的测试表明，少数不支持自定义选项卡的浏览器也适用于身份验证。 这些浏览器包括 "Opera"、"InBrowser" 和 "Maxthon"。 

## <a name="tested-devices-and-browsers"></a>已测试设备和浏览器
下表列出了已测试验证兼容性的设备和浏览器。

| 设备 | 浏览器     |  结果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | Chrome\* | 通过|
| Huawei/One + | 边缘\* | 通过|
| Huawei/One + | Firefox\* | 通过|
| Huawei/One + | 无畏\* | 通过|
| One + | Ecosia\* | 通过|
| One + | 猕猴桃\* | 通过|
| Huawei/One + | Opera | 通过|
| Huawei | OperaMini | 通过|
| Huawei/One + | InBrowser | 通过|
| One + | Maxthon | 通过|
| Huawei/One + | DuckDuckGo | 用户取消了身份验证|
| Huawei/One + | UC 浏览器 | 用户取消了身份验证|
| One + | Dolphin | 用户取消了身份验证|
| One + | CM 浏览器 | 用户取消了身份验证|
| Huawei/One + | 未安装 | AndroidActivityNotFound 异常|

\* 支持自定义选项卡

## <a name="known-issues"></a>已知问题

如果用户未在设备上启用浏览器，MSAL.NET 将引发 `AndroidActivityNotFound` 异常。  
  - **缓解**：要求用户在其设备上启用浏览器。 建议使用支持自定义选项卡的浏览器。

如果身份验证失败（例如，如果身份验证以 DuckDuckGo 启动），则 MSAL.NET 将返回 `AuthenticationCanceled MsalClientException`。 
  - **根本问题**：在设备上未启用支持自定义选项卡的浏览器。 使用无法完成身份验证的浏览器启动身份验证。 
  - **缓解**：要求用户在其设备上启用浏览器。 建议使用支持自定义选项卡的浏览器。

## <a name="next-steps"></a>后续步骤
有关详细信息和代码示例，请参阅在 Xamarin Android 和[嵌入式与系统 WEB UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)[上的嵌入式 web 浏览器与系统浏览器之间进行选择](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  