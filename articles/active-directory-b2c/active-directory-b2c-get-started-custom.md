---
title: 开始使用自定义策略-Azure Active Directory B2C |Microsoft Docs
description: 了解如何开始使用 Azure Active Directory B2C 中的自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2929c033b4744ea89f8e3d711a5e2e0df6301c14
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66730007"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自定义策略](active-directory-b2c-overview-custom.md)是定义 Azure Active Directory (Azure AD) B2C 租户行为的配置文件。 本文将介绍如何创建支持使用电子邮件地址和密码进行本地帐户注册或登录的自定义策略。 你还要准备好环境以添加标识提供者。

## <a name="prerequisites"></a>必备组件

- 如果没有租户，则需要[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。
- [注册应用程序](tutorial-register-applications.md)创建，以便它能够与 Azure AD B2C 租户中。

## <a name="add-signing-and-encryption-keys"></a>添加签名和加密密钥

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 单击**目录和订阅筛选器**顶部菜单中选择包含你的租户的目录。 
3. 选择 Azure 门户左上角的“所有服务”  ，搜索并选择 **Azure AD B2C**。
4. 在“概述”页上选择“标识体验框架”  。

### <a name="create-the-signing-key"></a>创建签名密钥

1. 选择“策略密钥”  ，然后选择“添加”  。
2. 对于“选项”  ，请选择 `Generate`。
3. 在“名称”  中，请输入 `TokenSigningKeyContainer`。 可能会自动添加前缀 `B2C_1A_`。
4. 对于“密钥类型”  ，请选择“RSA”  。
5. 对于“密钥用法”，请选择“签名”   。
6. 单击**创建**。

### <a name="create-the-encryption-key"></a>创建加密密钥

1. 选择“策略密钥”  ，然后选择“添加”  。
2. 对于“选项”  ，请选择 `Generate`。
3. 在“名称”  中，请输入 `TokenEncryptionKeyContainer`。 可能会自动添加前缀 `B2C_1A`_。
4. 对于“密钥类型”  ，请选择“RSA”  。
5. 对于“密钥用法”，请选择“加密”   。
6. 单击**创建**。

### <a name="create-the-facebook-key"></a>创建 Facebook 密钥

如果已有 [Facebook 应用程序机密](active-directory-b2c-setup-fb-app.md)，请将它作为策略密钥添加到租户。 否则，必须创建包含占位符值的密钥，使策略能够通过验证。

1. 选择“策略密钥”  ，然后选择“添加”  。
2. 对于“选项”  ，请选择 `Manual`。
3. 对于“名称”  ，请输入 `FacebookSecret`。 可能会自动添加前缀 `B2C_1A_`。
4. 在“机密”  中，输入 developers.facebook.com 提供的 Facebook 机密，或输入 `0` 作为占位符。 此值是密码，而不是应用程序 id。
5. 对于“密钥用法”，请选择“签名”   。
6. 单击**创建**。

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求注册两个用于注册和登录用户的应用程序：IdentityExperienceFramework（Web 应用），以及具有 IdentityExperienceFramework 应用委派权限的 ProxyIdentityExperienceFramework（本机应用）。 本地帐户只在租户中存在。 你的用户使用唯一的“电子邮件地址/密码”组合登录，以访问租户注册的应用程序。

### <a name="register-the-identityexperienceframework-application"></a>注册 IdentityExperienceFramework 应用程序

1. 选择**所有服务**在 Azure 门户的左上角，搜索并选择**Azure Active Directory**。
2. 在菜单中，选择**应用注册 （旧版）** 。
3. 选择“新建应用程序注册”  。
4. 对于“名称”  ，请输入 `IdentityExperienceFramework`。
5. 对于“应用程序类型”  ，请选择“Web 应用/API”  。
6. 对于“登录 URL”  ，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是你的 Azure AD B2C 租户域名。 现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。
7. 单击**创建**。 创建后，复制应用程序 ID 并将其保存以供日后使用。

### <a name="register-the-proxyidentityexperienceframework-application"></a>注册 ProxyIdentityExperienceFramework 应用程序

1. 在中**应用注册 （旧版）** ，选择**新应用程序注册**。
2. 对于“名称”  ，请输入 `ProxyIdentityExperienceFramework`。
3. 对于“应用程序类型”，请选择“本机”   。
4. 对于“重定向 URI”，  请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是你的 Azure AD B2C 租户。
5. 单击**创建**。 创建后，复制应用程序 ID 并将其保存以供日后使用。
6. 在“设置”页上，选择“所需权限”  ，然后选择“添加”  。
7. 选择**选择 API**，搜索并选择**IdentityExperienceFramework**，然后单击**选择**。
9. 选择“访问 IdentityExperienceFramework”  旁边的复选框，单击“选择”  ，然后单击“完成”  。
10. 选择“授予权限”  ，然后选择“是”  进行确认。

## <a name="download-starter-pack-and-modify-policies"></a>下载初学者包并修改策略

自定义策略是需要上传到 Azure AD B2C 租户的一组 XML 文件。 提供了初学者文件包来帮助你快速入门。 以下列表中的每个初学者包中包含实现所述方案所需的最少技术配置文件和用户旅程：

- LocalAccounts - 仅启用本地帐户。
- SocialAccounts - 仅启用社交（或联合）帐户。
- SocialAndLocalAccounts - 同时启用本地帐户和社交帐户。
- SocialAndLocalAccountsWithMFA - 启用社交、本地和多重身份验证选项。

每个初学者包中包含：

- 基本文件。 需要对基本文件进行少量的修改。
- 扩展文件。  大多数配置更改会在此文件中进行。
- 信赖方文件。 应用程序调用的任务特定文件。

>[!NOTE]
>如果 XML 编辑器支持验证，则根据初学者包的根目录中的 TrustFrameworkPolicy_0.3.0.0.xsd XML 架构来验证文件。 XML 架构验证在上传之前会识别错误。

1. [下载 .zip 文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或运行：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. 在 SocialAndLocalAccounts 文件夹中，编辑将 `yourtenant` 替换为你的租户名称的所有文件。 例如，`contosoTenant.onmicrosoft.com`。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。

### <a name="add-application-ids-to-the-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 *TrustFrameworkExtensions.xml*。

1. 打开 TrustFrameworkExtensions.xml  文件并找到元素 `<TechnicalProfile Id="login-NonInteractive">`。
2. 将 `IdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的标识体验框架应用程序的应用程序 ID。 将 `ProxyIdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的代理标识体验框架应用程序的应用程序 ID。
3. 保存扩展文件。

## <a name="upload-the-policies"></a>上传策略

1. 在标识体验框架的“自定义策略”页上，选择“上传策略”  。
1. 按此顺序上传 TrustFrameworkBase.xml  、TrustFrameworkExtensions.xml  、SignUpOrSignin.xml  、ProfileEdit.xml  和 PasswordReset.xml  。 上传某个文件时，会在策略文件的名称前面加上 `B2C_1A_`。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在“自定义策略”页上，选择“B2C_1A_signup_signin”  。
2. 有关**选择应用程序**在自定义策略的概述页上，选择名为 web 应用程序*webapp1*以前注册的。 请确保**回复 URL**是`https://jwt.ms`。
3. 选择“立即运行”  。
4. 现在，应该可以使用电子邮件地址注册。
5. 使用相同的帐户登录，以确认配置正确。

## <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者

1. 配置 [Facebook 应用程序](active-directory-b2c-setup-fb-app.md)。
2. 在 TrustFrameworkExtensions.xml  文件中，将 `client_id` 的值替换为 Facebook 应用程序 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. 将 TrustFrameworkExtensions.xml  文件上传到租户。
4. 使用“立即运行”  进行测试，或直接从已注册的应用程序调用该策略。

## <a name="next-steps"></a>后续步骤

- 将 Azure Active Directory 添加为标识提供者。 本入门指南中使用的基本文件已包含添加其他标识提供者所需的一些内容。 有关设置登录的信息，请参阅[使用 Active Directory B2C 自定义策略设置 Azure Active Directory 帐户的注册和登录](active-directory-b2c-setup-aad-custom.md)一文。
