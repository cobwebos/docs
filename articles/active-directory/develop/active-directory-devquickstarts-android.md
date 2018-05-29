---
title: Azure AD Android 入门 | Microsoft Docs
description: 如何生成一个与 Azure AD 集成以方便登录，并使用 OAuth2.0 调用受 Azure AD 保护 API 的 Android 应用程序。
services: active-directory
documentationcenter: android
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: da1ee39f-89d3-4d36-96f1-4eabbc662343
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 04/30/2018
ms.author: celested
ms.reviewer: dadobali
ms.custom: aaddev
ms.openlocfilehash: 20618fff8d253bfab195ce2847a8848a28960ae4
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2018
ms.locfileid: "34155688"
---
# <a name="azure-ad-android-getting-started"></a>Azure AD Android 入门
[!INCLUDE [active-directory-devquickstarts-switcher](../../../includes/active-directory-devquickstarts-switcher.md)]

如果你要开发 Android 应用程序，Microsoft 可让 Azure Active Directory (Azure AD) 用户拥有简单直接的登录体验。 Azure AD 允许应用程序通过 Microsoft Graph 或你自己的受保护 Web API 访问用户数据。 

Azure AD 身份验证库 (ADAL) Android 库使用行业标准的 OAuth2 和 OpenID Connect 来支持 [Microsoft Azure Active Directory 帐户](https://azure.microsoft.com/services/active-directory/)，从而使你的应用能够开始使用 [Microsoft Azure 云](https://cloud.microsoft.com) & [Microsoft Graph API](https://graph.microsoft.io)。 本示例演示了应用程序应经历的所有正常生命周期，其中包括：

* 获取 Microsoft Graph 的令牌
* 刷新令牌
* 调用 Microsoft Graph
* 注销用户

若要开始操作，你需要一个可在其中创建用户和注册应用程序的 Azure AD 租户。 如果还没有租户，请[了解如何获取租户](active-directory-howto-tenant.md)。

## <a name="scenario-sign-in-users-and-call-the-microsoft-graph"></a>方案：让用户登录并调用 Microsoft Graph

![拓扑](./media/active-directory-android-topology.png)

此应用可用于所有 Azure AD 帐户。 它既支持单组织方案也支持多组织方案（分步讨论）。 它演示了开发人员如何生成应用来连接企业用户并通过 Microsoft Graph 访问其 Azure 和 O365 数据。 在身份验证流中，最终用户需要登录该应用程序并同意其权限，某些情况下可能需要管理员同意该应用。 本示例中的大部分逻辑展示了如何对最终用户进行身份验证并对 Microsoft Graph 进行基本调用。

## <a name="example-code"></a>示例代码

可以[在 Github 上](https://github.com/Azure-Samples/active-directory-android)找到完整的示例代码。 

```Java
// Initialize your app with MSAL
AuthenticationContext mAuthContext = new AuthenticationContext(
        MainActivity.this, 
        AUTHORITY, 
        false);


// Perform authentication requests
mAuthContext.acquireToken(
    getActivity(), 
    RESOURCE_ID, 
    CLIENT_ID, 
    REDIRECT_URI,  
    PromptBehavior.Auto, 
    getAuthInteractiveCallback());

// ...

// Get tokens to call APIs like the Microsoft Graph
mAuthResult.getAccessToken()
```

## <a name="steps-to-run"></a>运行步骤

### <a name="register-and-configure-your-app"></a>注册并配置应用 
需要使用 [Azure 门户](https://portal.azure.com)向 Microsoft 注册一个本机客户端应用程序。 

1. 开始注册应用
    - 导航到 [Azure 门户](https://aad.portal.azure.com)。 
    - 选择“Azure Active Directory” > “应用注册”。 

2. 创建应用程序
    - 选择“新建应用程序注册”。 
    - 在“名称”字段中输入应用名称。 
    - 在“应用程序类型”中选择“本机”。 
    - 在“重定向 URI”中输入 `http://localhost`。 

3. 配置 Microsoft Graph
    - 选择“设置”>“所需权限”。
    - 选择“添加”，在“选择 API”中选择“Microsoft Graph”。 
    - 选择“登录并读取用户配置文件”权限，然后按“选择”进行保存。 
        - 此权限映射到 `User.Read` 作用域。 
    - 可选：在“所需权限”>“Windows Azure Active Directory”内删除选定权限“登录并读取用户配置文件”。 这将避免用户同意页面两次列出该权限。 

4. 恭喜！ 应用已配置成功。 在下一部分中，你需要：
    - `Application ID`
    - `Redirect URI`

### <a name="get-the-sample-code"></a>获取示例代码

1. 克隆代码。
    ```
    git clone https://github.com/Azure-Samples/active-directory-android
    ```
2. 在 Android Studio 中打开示例。
    - 选择“打开现有 Android Studio 项目”。

### <a name="configure-your-code"></a>配置代码

可以在 ***src/main/java/com/azuresamples/azuresampleapp/MainActivity.java*** 文件中找到此代码示例的所有配置。 

1. 将常量 `CLIENT_ID` 替换为 `ApplicationID`。
2. 将常量 `REDIRECT URI` 替换为之前配置的 `Redirect URI` (`http://localhost`)。 

### <a name="run-the-sample"></a>运行示例

1. 选择“生成”>“清理项目”。 
2. 选择“运行”>“运行应用”。 
3. 该应用应生成并显示一些基本的 UX。 单击 `Call Graph API` 按钮时，它将提示登录，然后自动使用新令牌调用 Microsoft Graph API。 

## <a name="important-info"></a>重要信息

1. 有关库机制以及如何配置新方案和功能的详细信息，请查看 [ADAL Android Wiki](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki)。 
2. 在本机方案中，应用将使用嵌入式 Web 视图，并且不会退出应用。 `Redirect URI` 可以是任意值。 
3. 发现任何问题或有任何要求？ 可以在 Stackoverflow 上使用标记 `azure-active-directory` 创建问题或发贴。 

### <a name="cross-app-sso"></a>跨应用 SSO
了解[如何使用 ADAL 在 Android 上启用跨应用 SSO](active-directory-sso-android.md)。 

### <a name="auth-telemetry"></a>身份验证遥测
ADAL 库提供身份验证遥测来帮助应用开发人员了解其应用的行为并创造更好的体验。 这使你可以捕获成功的登录、活跃用户以及其他一些有趣的见解。 使用身份验证遥测时，需要应用开发人员建立遥测服务来聚合和存储事件。

若要了解有关身份验证遥测的详细信息，请查看 [ADAL Android auth telemetry](https://github.com/AzureAD/azure-activedirectory-library-for-android/wiki/Telemetry)（ADAL Android 身份验证遥测）。 

[!INCLUDE [active-directory-devquickstarts-additional-resources](../../../includes/active-directory-devquickstarts-additional-resources.md)]
