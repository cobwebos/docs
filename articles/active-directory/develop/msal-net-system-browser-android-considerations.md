---
title: Xamarin Android 注意事项 （适用于.NET 的 Microsoft 身份验证库） |Azure
description: 使用 Xamarin Android Microsoft 身份验证库和.NET (MSAL.NET) 时，了解有关特定注意事项。
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544429"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>使用 MSAL.NET 特定于 Xamarin Android 的注意事项
使用 Microsoft 身份验证库的 Xamarin Android 上的.NET (MSAL.NET) 使用系统浏览器时，本文将介绍特定注意事项。

从开始 MSAL.NET 2.4.0-preview，MSAL.NET 支持以外 Chrome 浏览器，不再需要 Chrome 进行身份验证在 Android 设备上安装。

我们建议使用支持自定义选项卡，如它们的浏览器：

| 自定义选项卡支持的浏览器 | 包名称 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|猕猴桃 | com.kiwibrowser.browser|
|勇敢 | com.brave.browser|

除了浏览器使用自定义选项卡支持，基于我们的测试，不支持自定义选项卡的几个浏览器也适用于身份验证：Opera、 Opera Mini InBrowser，并使用 Maxthon。 有关详细信息，请阅读[测试结果的表](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)。

## <a name="known-issues"></a>已知问题

- 如果用户具有在设备上启用的浏览器中，不，将引发 MSAL.NET`AndroidActivityNotFound`异常。 
  - **缓解措施**：通知用户他们应启用浏览器 （最好是一个具有自定义选项卡支持） 其设备上。

- 如果身份验证失败 （例如。 身份验证启动 DuckDuckGo），将返回 MSAL.NET `AuthenticationCanceled MsalClientException`。 
  - **根问题**:在设备上未启用自定义选项卡支持的浏览器。 使用备用的浏览器，它不能以完成身份验证启动身份验证。 
  - **缓解措施**：通知用户他们应在其设备上安装浏览器 （最好是一个具有自定义选项卡支持）。

## <a name="devices-and-browsers-tested"></a>设备和浏览器测试
下表列出了设备和浏览器进行了测试。

| | 浏览器&ast;     |  结果  | 
| ------------- |:-------------:|:-----:|
| 华为一个 + | Chrome&ast; | 通过|
| 华为一个 + | Edge&ast; | 通过|
| 华为一个 + | Firefox&ast; | 通过|
| 华为一个 + | 勇敢&ast; | 通过|
| 一个 + | Ecosia&ast; | 通过|
| 一个 + | Kiwi&ast; | 通过|
| 华为一个 + | Opera | 通过|
| Huawei | OperaMini | 通过|
| 华为一个 + | InBrowser | 通过|
| 一个 + | 使用 Maxthon | 通过|
| 华为一个 + | DuckDuckGo | 用户已取消身份验证|
| 华为一个 + | UC 浏览器 | 用户已取消身份验证|
| 一个 + | Dolphin | 用户已取消身份验证|
| 一个 + | CM 浏览器 | 用户已取消身份验证|
| 华为一个 + | 未安装任何内容 | AndroidActivityNotFound ex|

&ast; 支持自定义选项卡

## <a name="next-steps"></a>后续步骤
有关代码段和使用包含 Xamarin Android 系统浏览器的其他信息阅读此[指南](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  