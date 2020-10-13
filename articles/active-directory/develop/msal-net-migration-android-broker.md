---
title: 使用代理将 Xamarin Android 应用迁移到 MSAL.NET
titleSuffix: Microsoft identity platform
description: 了解如何将使用 Microsoft Authenticator 或 Intune 公司门户的 Xamarin Android 应用从 ADAL.NET 迁移到 MSAL.NET。
author: aiwang
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/31/2020
ms.author: aiwang
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 47902b29da1dfe20fb54d633a0559e6a337fd771
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89183559"
---
# <a name="migrate-android-applications-that-use-a-broker-from-adalnet-to-msalnet"></a>将使用代理的 Android 应用程序从 ADAL.NET 迁移到 MSAL.NET

如果你的 Xamarin Android 应用当前使用适用于 .NET 的 Azure Active Directory 身份验证库 (ADAL.NET) 和[身份验证代理](brokered-auth.md)，则可以迁移到[适用于 .NET 的 Microsoft 身份验证库](msal-overview.md) (MSAL.NET)。

## <a name="prerequisites"></a>先决条件

* Xamarin Android 应用已与需要迁移到 MSAL.NET 的代理 ([Microsoft Authenticator](https://play.google.com/store/apps/details?id=com.azure.authenticator) 或 [Intune 公司门户](https://play.google.com/store/apps/details?id=com.microsoft.windowsintune.companyportal)) 和 ADAL.NET 集成。

## <a name="step-1-enable-the-broker"></a>步骤 1：启用中介

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
在 ADAL.NET 中，代理支持将按身份验证上下文启用。

若要调用代理，必须在 `PlatformParameters` 构造函数中将 `useBroker` 设置为 true：

```CSharp
public PlatformParameters(
        Activity callerActivity,
        bool useBroker)
```

在适用于 Android 的特定于平台的页面呈现器代码中，将 `useBroker` 标志设置为 true：

```CSharp
page.BrokerParameters = new PlatformParameters(
        this,
        true,
        PromptBehavior.SelectAccount);
```

然后，在获取令牌调用中包含参数：

```CSharp
AuthenticationResult result =
        await
            AuthContext.AcquireTokenAsync(
                Resource,
                ClientId,
                new Uri(RedirectURI),
                platformParameters)
                .ConfigureAwait(false);
```

</td><td>
在 MSAL.NET 中，中介支持是按 PublicClientApplication 启用的。

使用 `WithBroker()` 参数（默认设置为 true）以调用代理：

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid)
                .Build();
```

然后，在 AcquireToken 调用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="step-2-set-an-activity"></a>步骤 2：设置活动

在 ADAL.NET 中，已将活动（通常是 MainActivity）作为 PlatformParameters 的一部分传入，如[步骤 1：启用代理](#step-1-enable-the-broker)所示。

MSAL.NET 也使用活动，但这在无代理的常规 Android 使用情况下不是必需的。 若要使用代理，请设置活动以向代理发送响应以及接收来自代理的响应。

<table>
<tr><td>当前 ADAL 代码：</td><td>对应的 MSAL 代码：</td></tr>
<tr><td>
该活动会传递到 Android 特定平台中的 PlatformParameters。

```CSharp
page.BrokerParameters = new PlatformParameters(
          this,
          true,
          PromptBehavior.SelectAccount);
```
</td><td>

在 MSAL.NET 中，请执行以下两项操作，针对 Android 设置该活动：

1. 在 `MainActivity.cs` 中，将 `App.RootViewController` 设置为 `MainActivity`，以确保存在一个活动，其中包含对代理的调用。

    如果未正确设置此项，可能会收到以下错误：`"Activity_required_for_android_broker":"Activity is null, so MSAL.NET cannot invoke the Android broker. See https://aka.ms/Brokered-Authentication-for-Android"`

1. 在 AcquireTokenInteractive 调用中，使用 `.WithParentActivityOrWindow(App.RootViewController)` 并传入对你要使用的活动的引用。 此示例将使用 MainActivity。

例如：

在 App.cs 中：

```CSharp
   public static object RootViewController { get; set; }
```

在 MainActivity.cs 中：

```CSharp
   LoadApplication(new App());
   App.RootViewController = this;
```

在 AcquireToken 调用中：

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

## <a name="next-steps"></a>后续步骤

有关将 MSAL.NET 与 Xamarin 配合使用时特定于 Android 的注意事项的详细信息，请参阅 [Xamarin Android 与 MSAL.NET 配合使用时的配置要求和故障排除提示](msal-net-xamarin-android-considerations.md)。