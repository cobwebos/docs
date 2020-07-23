---
title: 适用于 Apple 设备的 Microsoft 企业 SSO 插件
titleSuffix: Microsoft identity platform | Azure
description: 了解适用于 iOS 和 macOS 设备的 Microsoft Azure Active Directory SSO 插件。
services: active-directory
author: brandwe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/31/2020
ms.author: brandwe
ms.reviewer: brandwe
ms.custom: aaddev
ms.openlocfilehash: a6af1a78582ebf62aed82bad8fa06cac37f511ea
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "80982576"
---
# <a name="microsoft-enterprise-sso-plug-in-for-apple-devices-preview"></a>适用于 Apple 设备的 Microsoft 企业 SSO 插件（预览版）

> [!NOTE]
> 此功能目前以公共预览版提供。
> 此预览版在提供时没有附带服务级别协议，不建议将其用于生产工作负荷。 某些功能可能不受支持或者受限。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

适用于 Apple 设备的 Microsoft 企业 SSO 插件为支持 Apple [企业单一登录](https://developer.apple.com/documentation/authenticationservices)功能的所有应用程序中的 Azure Active Directory (Azure AD) 帐户提供单一登录 (SSO)  。 此插件是在 Microsoft 与 Apple 的密切合作下开发的，可以提高应用程序的可用性，同时提供 Apple 和 Microsoft 能够提供的最佳保护。

在此公共预览版中，企业 SSO 插件仅适用于 iOS 设备，并仅在特定的 Microsoft 应用程序中分发。

企业 SSO 插件最初是与我们的[共享设备模式](msal-ios-shared-devices.md)新功能配合使用的。

## <a name="features"></a>功能

适用于 Apple 设备的 Microsoft 企业 SSO 插件具有以下优势：

- 为支持 Apple 企业单一登录功能的所有应用程序中的 Azure AD 帐户提供 SSO。
- 在 Microsoft Authenticator 中自动交付，可由任何移动设备管理 (MDM) 解决方案启用。

## <a name="requirements"></a>要求

若要使用适用于 Apple 设备的 Microsoft 企业 SSO 插件：

- 必须在设备上安装 iOS 13.0 或更高版本。
- 必须在设备上安装提供适用于 Apple 设备的 Microsoft 企业 SSO 插件的 Microsoft 应用程序。 对于公共预览版，这些应用程序包括[Microsoft Authenticator 应用](../user-help/user-help-auth-app-overview.md)。
- 设备必须已在 MDM 中注册（例如，使用 Microsoft Intune 注册）。
- 必须将配置推送到设备，才能在该设备上启用适用于 Apple 设备的 Microsoft 企业 SSO 插件。 此项安全约束是 Apple 要求的。

## <a name="enable-the-sso-extension-with-mobile-device-management-mdm"></a>使用移动设备管理 (MDM) 启用 SSO 扩展

若要启用适用于 Apple 设备的 Microsoft 企业 SSO 插件，需要通过 MDM 服务向设备发送一个信号。 由于 Microsoft 在[Microsoft Authenticator 应用程序](..//user-help/user-help-auth-app-overview.md)中包含企业 SSO 插件，因此请使用 MDM 将应用程序配置为启用 Microsoft 企业 SSO 插件。

使用以下参数配置适用于 Apple 设备的 Microsoft 企业 SSO 插件：

- **类型**：重定向
- **扩展 ID**：`com.microsoft.azureauthenticator.ssoextension`
- **团队 ID**：`SGGM6D27TK`
- **URL**：
  - `https://login.microsoftonline.com`
  - `https://login.windows.net`
  - `https://login.microsoft.com`
  - `https://sts.windows.net`
  - `https://login.partner.microsoftonline.cn`
  - `https://login.chinacloudapi.cn`
  - `https://login.microsoftonline.de`
  - `https://login.microsoftonline.us`
  - `https://login.usgovcloudapi.net`
  - `https://login-us.microsoftonline.com`

可以使用 Microsoft Intune 作为 MDM 服务来方便配置 Microsoft 企业 SSO 插件。 有关详细信息，请参阅 [Intune 配置文档](https://docs.microsoft.com/intune/configuration/ios-device-features-settings)。

## <a name="using-the-sso-extension-in-your-application"></a>在应用程序中使用 SSO 扩展

[适用于 Apple 设备的 Microsoft 身份验证库 (MSAL)](https://github.com/AzureAD/microsoft-authentication-library-for-objc) 1.1.0 及更高版本支持适用于 Apple 设备的 Microsoft 企业 SSO 插件。

若要支持适用于 Apple 设备的 Microsoft 企业 SSO 插件所提供的共享设备模式，请确保应用程序使用指定的符合最低要求的 MSAL 版本。

## <a name="next-steps"></a>后续步骤

有关 iOS 上的共享设备模式的详细信息，请参阅[适用于 iOS 设备的共享设备模式](msal-ios-shared-devices.md)。
