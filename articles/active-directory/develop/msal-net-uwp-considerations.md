---
title: UWP 注意事项（MSAL.NET） |Microsoft
titleSuffix: Microsoft identity platform
description: 了解使用适用于 .NET 的 Microsoft 身份验证库（MSAL.NET）的通用 Windows 平台（UWP）的注意事项。
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eeec28569cf31af4542d6cd7aca1fb27d77b1e0
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132524"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>将通用 Windows 平台与 MSAL.NET 一起使用时的注意事项
与 MSAL.NET 一起使用通用 Windows 平台（UWP）的应用程序开发人员应考虑本文中所述的概念。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 属性
在 Windows 运行时（WinRT）平台上，`PublicClientApplication` 具有 `UseCorporateNetwork`的布尔属性。 如果用户登录到具有联合 Azure Active Directory （Azure AD）租户的帐户，则通过此属性，Windows 8.1 应用程序和 UWP 应用程序可以受益于集成的 Windows 身份验证（IWA）。 登录到操作系统的用户还可以使用单一登录（SSO）。 设置 `UseCorporateNetwork` 属性时，MSAL.NET 将使用 web 身份验证代理（WAB）。

> [!IMPORTANT]
> 如果将 `UseCorporateNetwork` 属性设置为 true，则假定应用程序开发人员已在应用程序中启用了 IWA。 若要启用 IWA：
> - 在 UWP 应用程序的 `Package.appxmanifest`中，在 "**功能**" 选项卡上启用以下功能：
>   - **企业身份验证**
>   - **专用网络(客户端和服务器)**
>   - **共享用户证书**

默认情况下，IWA 不启用，因为在接受请求企业身份验证或共享用户证书功能的应用程序之前，Microsoft Store 需要高级验证级别。 并非所有开发人员都希望执行此级别的验证。

在 UWP 平台上，基础 WAB 实现在启用条件访问的企业方案中无法正常运行。 用户在尝试使用 Windows Hello 登录时，会看到此问题的症状。 当要求用户选择证书时：

- 找不到该 PIN 的证书。
- 用户选择证书后，不会提示他们输入 PIN。

您可以尝试通过使用其他方法（如用户名-密码和电话身份验证）来避免此问题，但体验并不好。

## <a name="troubleshooting"></a>故障排除

有些客户在特定企业环境中报告了以下登录错误，他们知道他们有 internet 连接并且该连接适用于公用网络。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

可以通过确保 WAB （底层 Windows 组件）允许专用网络来避免此问题。 可以通过设置注册表项来执行此操作：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

有关详细信息，请参阅[Web 身份验证 Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>后续步骤
以下示例提供了详细信息。

示例 | 平台 | 说明 
|------ | -------- | -----------|
|[active-dotnet-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用 MSAL.NET 的 UWP 客户端应用程序。 它为使用 Azure AD 2.0 终结点进行身份验证的用户访问 Microsoft Graph。 <br>![拓扑](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[active directory-xamarin-native-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin Forms 应用，演示如何通过 Azure AD 2.0 终结点使用 MSAL 对 Microsoft 个人帐户和 Azure AD 进行身份验证。 它还演示了如何访问 Microsoft Graph 并显示生成的令牌。 <br>![拓扑](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
