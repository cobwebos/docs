---
title: 如何使用 ADAL 在 Android 上启用跨应用 SSO | Microsoft Docs
description: 如何使用 ADAL SDK 的功能启用跨应用程序单一登录。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 40710225-05ab-40a3-9aec-8b4e96b6b5e7
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: android
ms.devlang: java
ms.topic: article
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 2c7ccd8d2022631e32c240007a782d2382aac518
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2018
ms.locfileid: "52422841"
---
# <a name="how-to-enable-cross-app-sso-on-android-using-adal"></a>如何：使用 ADAL 在 Android 上启用跨应用 SSO

[!INCLUDE [active-directory-develop-applies-v1-adal](../../../includes/active-directory-develop-applies-v1-adal.md)]

单一登录 (SSO) 允许用户仅输入一次其凭据，并让这些凭据跨应用程序以及跨其他应用程序可能使用的平台（例如 Microsoft 帐户，或 Microsoft 365 中的工作帐户）自动工作，无论发行商是谁。

使用 Microsoft 的标识平台以及各种 SDK，可以轻松在你自己的应用套件中启用 SSO，或者使用中转站功能和 Authenticator 应用程序在整个设备中启用 SSO。

在本操作指南中，你将学习如何在应用程序中配置 SDK，以便向客户提供 SSO。

## <a name="prerequisites"></a>先决条件

本操作指南假定你知道如何：

- 使用 Azure Active Directory (Azure AD) 的旧门户预配你的应用。 有关详细信息，请参阅[向 Azure AD v1.0 终结点注册应用](quickstart-v1-add-azure-ad-app.md)。
- 将你的应用程序与 [Azure AD Android SDK](https://github.com/AzureAD/azure-activedirectory-library-for-android) 进行集成。

## <a name="single-sign-on-concepts"></a>单一登录概念

### <a name="identity-brokers"></a>标识中转站

Microsoft 为每个移动平台提供了应用程序，可在来自不同供应商的应用程序之间桥接凭据，并支持需要一个安全位置来验证凭据的特殊增强功能。 这些被称为**中转站**。

在 iOS 和 Android 平台上，中转站通过可下载的应用程序提供，客户可以独立安装这些应用程序，也可以由负责管理其员工的部分或全部设备的公司推送到设备。 中转站支持仅为某些应用程序管理安全性，或者为整个设备管理安全性，具体取决于 IT 管理配置。 在 Windows 中，此功能由内置于操作系统的帐户选择器提供，在技术上被称为 Web 身份验证中转站。

#### <a name="broker-assisted-login"></a>中转站辅助的登录

中转站辅助的登录是一种发生在中转站应用程序中的登录体验，它使用中转站的存储和安全性在设备上应用了标识平台的所有应用程序间共享凭据。 这意味着，应用程序依赖于中转站才能完成用户登录。 在 iOS 和 Android 平台上，通过可下载的应用程序提供这些中转站。可以由客户独立安装这些应用程序，也可以由负责管理其用户的设备的公司将这些应用程序推送到设备。 这种类型的应用程序示例是 iOS 上的 Microsoft Authenticator 应用程序。 在 Windows 中，此功能由内置于操作系统的帐户选择器提供，在技术上被称为 Web 身份验证中转站。
体验因平台而异，如果未正确管理，有时会给用户带来麻烦。 如果已安装 Facebook 应用程序并在另一个应用程序中使用 Facebook Connect，则可能很熟悉这种模式。 标识平台使用相同的模式。

在 Android 上，帐户选择器会在对用户中断较小的基础上显示应用程序。

#### <a name="how-the-broker-gets-invoked"></a>如何调用中转站

如果在设备上安装了 Microsoft Authenticator 应用程序等兼容的中转站，当用户指出他们希望在标识平台上使用任何帐户登录时，标识 SDK 会自动调用中转站。

#### <a name="how-microsoft-ensures-the-application-is-valid"></a>Microsoft 如何确保应用程序有效

必须确保调用中转站的应用程序的标识有效，这对于维护中转站辅助登录的安全性至关重要。 iOS 和 Android 都不会强制实施仅对给定应用程序有效的唯一标识符，因此恶意应用程序可能“假冒”合法应用程序的标识符，并接收对合法应用程序适用的令牌。 为了确保在运行时 Microsoft 始终与适当的应用程序进行通信，我们会要求开发人员在将其应用程序注册到 Microsoft 时提供自定义重定向 URI。 **下面详细讨论开发人员应如何设计此重定向 URI。** 此自定义 redirectURI 包含应用程序的证书指纹，并由 Google Play Store 确保其对应用程序是唯一的。 当应用程序调用中转站时，中转站会请求 Android 操作系统为其提供调用中转站的证书指纹。 在调用我们的标识系统时，中转站会向 Microsoft 提供此证书指纹。 如果应用程序的证书指纹与开发人员在注册期间提供给我们的证书指纹不匹配，我们将拒绝应用程序访问所请求的资源的令牌。 此检查可确保只有开发人员注册的应用程序收到令牌。

SSO 中转登录具有以下优点：

* 无论供应商是谁，用户都可以在所有应用程序中体验 SSO。
* 应用程序可以使用更高级的业务功能，如条件访问，并支持 Intune 方案。
* 应用程序可以支持业务用户使用基于证书的身份验证。
* 登录体验更安全，因为中转站应用程序将使用附加的安全算法和加密来验证应用程序与用户的标识。

下面介绍了该 SDK 如何与应用程序的中转站应用程序配合工作以启用 SSO：

```
+------------+ +------------+   +-------------+
|            | |            |   |             |
|   App 1    | |   App 2    |   |   Someone   |
|            | |            |   |    else's   |
|            | |            |   |     App     |
+------------+ +------------+   +-------------+
|  ADAL SDK  | |  ADAL SDK  |   |  ADAL SDK   |
+-----+------+-+-----+------+-  +-------+-----+
      |              |                  |
      |       +------v------+           |
      |       |             |           |
      |       | Microsoft   |           |
      +-------> Broker      |^----------+
              | Application
              |             |
              +-------------+
              |             |
              |   Broker    |
              |   Storage   |
              |             |
              +-------------+

```

### <a name="turning-on-sso-for-broker-assisted-sso"></a>打开对中转站辅助 SSO 的 SSO

默认情况下不允许应用程序使用设备上安装的任何中转站。 要向中转站使用应用程序必须执行一些额外配置，并将一些代码添加到应用程序。

要遵循的步骤如下：

1. 启用应用程序代码调用到 MS SDK 中的中转站模式
2. 建立新的重定向 URI，并为应用程序和应用程序的注册提供程序
3. 在 Android 清单中设置正确的权限

#### <a name="step-1-enable-broker-mode-in-your-application"></a>步骤 1：在应用程序中启用中转站模式

应用程序使用了中转站的功能被打开的创建的设置或身份验证的实例的初始设置。 在应用中执行此操作：

```
AuthenticationSettings.Instance.setUseBroker(true);
```

#### <a name="step-2-establish-a-new-redirect-uri-with-your-url-scheme"></a>步骤 2：使用 URL 方案建立新的重定向 URI

为了确保由正确的应用程序接收返回的凭据令牌，我们需要确保以 Android 操作系统可以验证的方式回调应用程序。 Android 操作系统使用 Google Play Store 中的证书哈希。 恶意应用程序无法欺骗此证书哈希。 结合中转站应用程序的 URI，Microsoft 可确保令牌返回到正确的应用程序。 必须在应用程序中注册唯一的重定向 URI。

重定向 URI 必须采用正确的格式：

`msauth://packagename/Base64UrlencodedSignature`

例如：*msauth://com.example.userapp/IcB5PxIyvbLkbFVtBI%2FitkW%2Fejk%3D*

可以使用 [Azure 门户](https://portal.azure.com/)在应用注册中注册此重定向 URI。 有关 Azure AD 应用注册的详细信息，请参阅[与 Azure Active Directory 集成](active-directory-how-to-integrate.md)。

#### <a name="step-3-set-up-the-correct-permissions-in-your-application"></a>步骤 3：在应用程序中设置正确的权限

Android 中的中转站应用程序使用 Android OS 的帐户管理器功能来管理跨应用程序的凭据。 若要在 Android 中使用该中转站应用程序清单中必须有权使用 AccountManager 帐户。 [Google 帐户管理器文档](https://developer.android.com/reference/android/accounts/AccountManager.html)中详细介绍了这些权限

具体而言，这些权限如下：

```
GET_ACCOUNTS
USE_CREDENTIALS
MANAGE_ACCOUNTS
```

### <a name="youve-configured-sso"></a>已配置 SSO！

现在，标识 SDK 会自动在应用程序之间共享凭据并调用中转站（如果在设备上存在）。

## <a name="next-steps"></a>后续步骤

* 了解[单一登录 SAML 协议](single-sign-on-saml-protocol.md)