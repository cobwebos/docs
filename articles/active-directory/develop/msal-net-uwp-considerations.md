---
title: UWP 注意事项 (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: 了解将通用 Windows 平台 (UWP) 与适用于 .NET 的 Microsoft 身份验证库 (MSAL.NET) 配合使用时的注意事项。
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/16/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: 0654bce86cf5fb0b5bd117e444721e95f137dd47
ms.sourcegitcommit: d662eda7c8eec2a5e131935d16c80f1cf298cb6b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82652699"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>将通用 Windows 平台与 MSAL.NET 配合使用时的注意事项
将通用 Windows 平台 (UWP) 与 MSAL.NET 配合使用的应用程序的开发人员应当考虑本文中所述的概念。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 属性
在 Windows 运行时 (WinRT) 平台上，`PublicClientApplication` 具有布尔属性 `UseCorporateNetwork`。 使用此属性，Windows 8.1 应用程序和 UWP 应用程序在用户登录到具有联合 Azure Active Directory (Azure AD) 租户的帐户时可以利用集成 Windows 身份验证 (IWA)。 已登录到操作系统的用户还可以使用单一登录 (SSO)。 设置 `UseCorporateNetwork` 属性后，MSAL.NET 将使用 Web 身份验证代理 (WAB)。

> [!IMPORTANT]
> 将 `UseCorporateNetwork` 属性设置为 true 时，将假定应用程序开发人员已在应用程序中启用了 IWA。 若要启用 IWA，请执行以下操作：
> - 在 UWP 应用程序的 `Package.appxmanifest` 中，在“功能”  选项卡上，启用以下功能：
>   - **企业身份验证**
>   - **专用网络(客户端和服务器)**
>   - **共享用户证书**

默认情况下不会启用 IWA，因为 Microsoft Store 在接受请求企业身份验证或共享用户证书功能的应用程序之前，需要进行高级别验证。 并非所有开发人员都希望执行此级别的验证。

在 UWP 平台上，基础 WAB 实现在启用了条件访问的企业方案中无法正常工作。 用户在尝试使用 Windows Hello 登录时，会看到此问题的症状。 当要求用户选择证书时：

- 找不到 PIN 的证书。
- 用户选择证书后，不提示他们输入 PIN。

你可以尝试使用替代方法来避免此问题，例如，用户名密码和电话身份验证，但体验不是很好。

## <a name="troubleshooting"></a>故障排除

有些客户在特定企业环境中报告了以下登录错误，他们知道他们有 Internet 连接并且该连接在公共网络中工作良好。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

可以通过确保 WAB（Windows 基础组件）允许专用网络来避免此问题。 为此，可以设置一个注册表项：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

有关详细信息，请参阅 [Web 身份验证代理 - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>后续步骤
以下示例提供了详细信息。

示例 | 平台 | 说明 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 一个使用 MSAL.NET 的 UWP 客户端应用程序。 它为使用 Azure AD 2.0 终结点进行身份验证的用户访问 Microsoft Graph。 <br>![拓扑](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active-directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，演示如何通过 Azure AD 2.0 终结点使用 MSAL 对 Microsoft 个人帐户和 Azure AD 进行身份验证。 它还展示了如何访问 Microsoft Graph 并显示了生成的令牌。 <br>![拓扑](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
