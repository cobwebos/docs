---
title: Xamarin Android 系统浏览器注意事项 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 上的系统浏览器与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。
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
ms.openlocfilehash: 8e37d443d75dccae8704bc1ff7856e1d24131766
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652700"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android 系统浏览器的 MSAL.NET 使用注意事项

本文介绍将 Xamarin Android 上的系统浏览器与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。

从 MSAL.NET 2.4.0 预览版开始，MSAL.NET 支持除 Chrome 之外的浏览器。 它不再要求在 Android 设备上安装 Chrome 进行身份验证。

建议使用支持自定义标签页的浏览器。 下面是这些浏览器的一些示例：

| 提供自定义标签页支持的浏览器 | 包名称 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

除了标识提供自定义标签页支持的浏览器之外，我们的测试还表明，一些不支持自定义标签页的浏览器也适用于身份验证。 这些浏览器包括 Opera、Opera Mini、InBrowser 和 Maxthon。 

## <a name="tested-devices-and-browsers"></a>测试的设备和浏览器
下表列出了经过身份验证兼容性测试的设备和浏览器。

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
| Huawei/One+ | DuckDuckGo | 用户已取消身份验证|
| Huawei/One+ | UC 浏览器 | 用户已取消身份验证|
| One+ | Dolphin | 用户已取消身份验证|
| One+ | CM 浏览器 | 用户已取消身份验证|
| Huawei/One+ | 未安装任何内容 | AndroidActivityNotFound exception|

\* 支持自定义标签页

## <a name="known-issues"></a>已知问题

如果用户未在设备上启用浏览器，MSAL.NET 会引发 `AndroidActivityNotFound` 异常。  
  - **缓解措施**：要求用户在其设备上启用浏览器。 建议使用支持自定义标签页的浏览器。

如果身份验证失败（例如，如果身份验证在启动时使用了 DuckDuckGo），MSAL.NET 会返回 `AuthenticationCanceled MsalClientException`。 
  - **根本问题**：未在设备上启用支持自定义标签页的浏览器。 使用无法完成身份验证的浏览器启动了身份验证。 
  - **缓解措施**：要求用户在其设备上启用浏览器。 建议使用支持自定义标签页的浏览器。

## <a name="next-steps"></a>后续步骤
有关详细信息和代码示例，请参阅[在 Xamarin Android 上的嵌入式 Web 浏览器与系统浏览器之间选择](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)和[嵌入式 Web UI 与系统 Web UI](msal-net-web-browsers.md#embedded-vs-system-web-ui)。  