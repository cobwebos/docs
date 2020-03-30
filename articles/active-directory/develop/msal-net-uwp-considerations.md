---
title: UWP 注意事项（MSAL.NET） |蔚蓝
titleSuffix: Microsoft identity platform
description: 了解有关将通用 Windows 平台 （UWP） 与 Microsoft 身份验证库一起使用 .NET （MSAL.NET） 的注意事项。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132524"
---
# <a name="considerations-for-using-universal-windows-platform-with-msalnet"></a>使用通用 Windows 平台MSAL.NET的注意事项
使用通用 Windows 平台 （UWP） MSAL.NET的应用程序开发人员应考虑本文介绍的概念。

## <a name="the-usecorporatenetwork-property"></a>UseCorporateNetwork 属性
在 Windows 运行时 （WinRT）`PublicClientApplication`平台上，具有布尔属性`UseCorporateNetwork`。 如果用户登录到具有联合 Azure 活动目录 （Azure AD） 租户的帐户，则此属性使 Windows 8.1 应用程序和 UWP 应用程序受益于集成 Windows 身份验证 （IWA）。 登录到操作系统的用户也可以使用单一登录 （SSO）。 设置属性时，MSAL.NET`UseCorporateNetwork`使用 Web 身份验证代理 （WAB）。

> [!IMPORTANT]
> 将`UseCorporateNetwork`属性设置为 true 假定应用程序开发人员已在应用程序中启用 IWA。 要启用 IWA：
> - 在 UWP 应用程序的`Package.appxmanifest`的 "**功能"** 选项卡上，启用以下功能：
>   - **企业身份验证**
>   - **专用网络(客户端和服务器)**
>   - **共享用户证书**

默认情况下未启用 IWA，因为 Microsoft 应用商店在接受请求企业身份验证或共享用户证书功能的应用程序之前需要高级别的验证。 并非所有开发人员都希望执行此级别的验证。

在 UWP 平台上，基础 WAB 实现在启用条件访问的企业方案中无法正常工作。 当用户尝试使用 Windows Hello 登录时，他们会看到此问题的症状。 当要求用户选择证书时：

- 找不到 PIN 的证书。
- 用户选择证书后，不会提示他们输入 PIN。

您可以使用用户名密码和电话身份验证等替代方法来避免此问题，但体验不是很好。

## <a name="troubleshooting"></a>疑难解答

一些客户在知道具有互联网连接且该连接与公共网络配合使用的特定企业环境中报告了以下登录错误。

```Text
We can't connect to the service you need right now. Check your network connection or try this again later.
```

通过确保 WAB（基础 Windows 组件）允许专用网络来避免此问题。 为此，可以设置一个注册表项：

```Text
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows NT\CurrentVersion\Image File Execution Options\authhost.exe\EnablePrivateNetwork = 00000001
```

有关详细信息，请参阅[Web 身份验证代理 - Fiddler](https://docs.microsoft.com/windows/uwp/security/web-authentication-broker#fiddler)。

## <a name="next-steps"></a>后续步骤
以下示例提供了详细信息。

示例 | Platform | 描述 
|------ | -------- | -----------|
|[active-directory-dotnet-native-uwp-v2](https://github.com/azure-samples/active-directory-dotnet-native-uwp-v2) | UWP | 使用MSAL.NET的 UWP 客户端应用程序。 它访问使用 Azure AD 2.0 终结点进行身份验证的用户的 Microsoft 图形。 <br>![拓扑](media/msal-net-uwp-considerations/topology-native-uwp.png)|
|[活动-目录-xamarin-本机-v2](https://github.com/Azure-Samples/active-directory-xamarin-native-v2) | Xamarin iOS、Android、UWP | 一个简单的 Xamarin 窗体应用，演示如何使用 MSAL 通过 Azure AD 2.0 终结点对 Microsoft 个人帐户和 Azure AD 进行身份验证。 它还演示如何访问 Microsoft 图形并显示生成的令牌。 <br>![拓扑](media/msal-net-uwp-considerations/topology-xamarin-native.png)|
