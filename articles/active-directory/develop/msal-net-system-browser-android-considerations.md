---
title: Xamarin Android 注意事项（适用于 .NET 的 Microsoft 身份验证库）
titleSuffix: Microsoft identity platform
description: 了解将 Xamarin Android 与适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）一起使用时的特定注意事项。
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
ms.openlocfilehash: 8f5caad4b136c9ef2686cc4befc70e6720e27855
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72802748"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>适用于 MSAL.NET 的 Xamarin 特定于 Android 的注意事项
本文讨论在 Xamarin Android 上使用系统浏览器和用于 .NET 的 Microsoft 身份验证库（MSAL.NET）时的特定注意事项。

从 MSAL.NET 2.4.0-preview 开始，MSAL.NET 支持除 Chrome 以外的浏览器，而不再需要在 Android 设备上安装 Chrome 进行身份验证。

建议使用支持自定义选项卡的浏览器，如下所示：

| 支持自定义选项卡的浏览器 | 包名称 |
|------| ------- |
|Chrome | .com. chrome|
|Microsoft Edge | emmx|
|Firefox | org firefox|
|Ecosia | ecosia android|
|猕猴桃 | kiwibrowser|
|无畏 | 无畏|

除了使用自定义选项卡的浏览器，根据我们的测试，一些不支持自定义选项卡的浏览器也可以用于身份验证： Opera、Opera 迷你、InBrowser 和 Maxthon。 有关详细信息，请参阅[表中的测试结果](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested)。

## <a name="known-issues"></a>已知问题

- 如果用户未在设备上启用浏览器，MSAL.NET 将引发 `AndroidActivityNotFound` 异常。 
  - **缓解**：通知用户他们应该在其设备上启用浏览器（最好是使用自定义选项卡支持的浏览器）。

- 如果身份验证失败（例如 身份验证使用 DuckDuckGo 启动），MSAL.NET 将返回 `AuthenticationCanceled MsalClientException`。 
  - **根本问题**：设备上未启用具有自定义选项卡支持的浏览器。 使用备用浏览器启动的身份验证无法完成身份验证。 
  - **缓解**：通知用户他们应该在其设备上安装浏览器（最好使用自定义选项卡支持）。

## <a name="devices-and-browsers-tested"></a>已测试设备和浏览器
下表列出了已测试的设备和浏览器。

| | 浏览器&ast;     |  结果  | 
| ------------- |:-------------:|:-----:|
| Huawei/One + | Chrome&ast; | 通过|
| Huawei/One + | 边缘&ast; | 通过|
| Huawei/One + | Firefox&ast; | 通过|
| Huawei/One + | 无畏&ast; | 通过|
| One + | Ecosia&ast; | 通过|
| One + | 猕猴桃&ast; | 通过|
| Huawei/One + | Opera | 通过|
| Huawei | OperaMini | 通过|
| Huawei/One + | InBrowser | 通过|
| One + | Maxthon | 通过|
| Huawei/One + | DuckDuckGo | 用户取消了身份验证|
| Huawei/One + | UC 浏览器 | 用户取消了身份验证|
| One + | Dolphin | 用户取消了身份验证|
| One + | CM 浏览器 | 用户取消了身份验证|
| Huawei/One + | 未安装 | AndroidActivityNotFound ex|

&ast; 支持自定义选项卡

## <a name="next-steps"></a>后续步骤
有关代码片段和将系统浏览器与 Xamarin Android 配合使用的其他信息，请阅读本[指南](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid)。  