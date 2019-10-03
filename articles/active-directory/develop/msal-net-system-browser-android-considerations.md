---
title: Xamarin Android 注意事项（适用于 .NET 的 Microsoft 身份验证库）| Azure
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532572"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>与 MSAL.NET 配合使用时特定于 Xamarin Android 的注意事项
本文介绍将 Xamarin Android 上的系统浏览器与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的具体注意事项。

从 MSAL.NET 2.4.0-preview 开始，MSAL.NET 支持 Chrome 之外的浏览器，不再要求在 Android 设备上安装 Chrome 进行身份验证。

建议使用支持自定义标签页的浏览器，例如以下这些：

| 支持自定义标签页的浏览器 | 包名称 |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Ecosia | com.ecosia.android|
|Kiwi | com.kiwibrowser.browser|
|Brave | com.brave.browser|

根据我们的测试，除了支持自定义标签页的浏览器，一些不支持自定义标签页的浏览器也适用于身份验证：Opera、Opera Mini、InBrowser 和 Maxthon。 有关详细信息，请阅读[测试结果表](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)。

## <a name="known-issues"></a>已知问题

- 如果用户未在设备上启用浏览器，MSAL.NET 会引发 `AndroidActivityNotFound` 异常。 
  - **缓解措施**：告知用户，他们应该在设备上启用浏览器（首选支持自定义标签页的浏览器）。

- 如果身份验证失败（例如， 身份验证在启动时使用了 DuckDuckGo），MSAL.NET 会返回 `AuthenticationCanceled MsalClientException`。 
  - **根本问题**：未在设备上启用支持自定义标签页的浏览器。 在启动身份验证时使用了其他浏览器，该浏览器无法完成身份验证。 
  - **缓解措施**：告知用户，他们应该在设备上安装浏览器（首选支持自定义标签页的浏览器）。

## <a name="devices-and-browsers-tested"></a>测试的设备和浏览器
下表列出了经过测试的设备和浏览器。

| | 浏览器&ast;     |  结果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One+ | Chrome&ast; | 通过|
| Huawei/One+ | Edge&ast; | 通过|
| Huawei/One+ | Firefox&ast; | 通过|
| Huawei/One+ | Brave&ast; | 通过|
| One+ | Ecosia&ast; | 通过|
| One+ | Kiwi&ast; | 通过|
| Huawei/One+ | Opera | 通过|
| Huawei | OperaMini | 通过|
| Huawei/One+ | InBrowser | 通过|
| One+ | Maxthon | 通过|
| Huawei/One+ | DuckDuckGo | 用户取消的身份验证|
| Huawei/One+ | UC 浏览器 | 用户取消的身份验证|
| One+ | Dolphin | 用户取消的身份验证|
| One+ | CM 浏览器 | 用户取消的身份验证|
| Huawei/One+ | 未安装任何内容 | AndroidActivityNotFound ex|

&ast; 支持自定义标签页

## <a name="next-steps"></a>后续步骤
如需代码片段并详细了解如何将系统浏览器与 Xamarin Android 配合使用，请阅读此[指南](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  