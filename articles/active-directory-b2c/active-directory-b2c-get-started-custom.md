---
title: 自定义策略入门-Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中开始自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b7eb004dbeba499e6f67f98165b72d7ec8615f1b
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065844"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自定义策略](active-directory-b2c-overview-custom.md)是定义 Azure Active Directory B2C （Azure AD B2C）租户行为的配置文件。 本文将介绍如何创建支持使用电子邮件地址和密码进行本地帐户注册或登录的自定义策略。 你还要准备好环境以添加标识提供者。

## <a name="prerequisites"></a>先决条件

- 如果没有租户，则需要[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。
- 在所创建的租户中[注册应用程序](tutorial-register-applications.md), 以便它可以与 Azure AD B2C 通信。

## <a name="add-signing-and-encryption-keys"></a>添加签名和加密密钥

1. 以 Azure AD B2C 租户的全局管理员身份登录 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含你的租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。
4. 在“概述”页上选择“标识体验框架”。

### <a name="create-the-signing-key"></a>创建签名密钥

1. 选择“策略密钥”，然后选择“添加”。
2. 对于“选项”，请选择 `Generate`。
3. 在“名称”中，请输入 `TokenSigningKeyContainer`。 可能会自动添加前缀 `B2C_1A_`。
4. 对于“密钥类型”，请选择“RSA”。
5. 对于“密钥用法”，请选择“签名”。
6. 单击“创建”。

### <a name="create-the-encryption-key"></a>创建加密密钥

1. 选择“策略密钥”，然后选择“添加”。
2. 对于“选项”，请选择 `Generate`。
3. 在“名称”中，请输入 `TokenEncryptionKeyContainer`。 可能会自动添加前缀 `B2C_1A`_。
4. 对于“密钥类型”，请选择“RSA”。
5. 对于“密钥用法”，请选择“加密”。
6. 单击“创建”。

### <a name="create-the-facebook-key"></a>创建 Facebook 密钥

如果已有 [Facebook 应用程序机密](active-directory-b2c-setup-fb-app.md)，请将它作为策略密钥添加到租户。 否则，必须创建包含占位符值的密钥，使策略能够通过验证。

1. 选择“策略密钥”，然后选择“添加”。
2. 对于“选项”，请选择 `Manual`。
3. 对于“名称”，请输入 `FacebookSecret`。 可能会自动添加前缀 `B2C_1A_`。
4. 在“机密”中，输入 developers.facebook.com 提供的 Facebook 机密，或输入 `0` 作为占位符。 此值是机密, 而不是应用程序 ID。
5. 对于“密钥用法”，请选择“签名”。
6. 单击“创建”。

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求注册两个用于注册和登录用户的应用程序：IdentityExperienceFramework（Web 应用），以及具有 IdentityExperienceFramework 应用委派权限的 ProxyIdentityExperienceFramework（本机应用）。 本地帐户只在租户中存在。 你的用户使用唯一的“电子邮件地址/密码”组合登录，以访问租户注册的应用程序。

### <a name="register-the-identityexperienceframework-application"></a>注册 IdentityExperienceFramework 应用程序

1. 选择 Azure 门户左上角的 "**所有服务**"。
1. 在搜索框中输入 `Azure Active Directory`。
1. 在搜索结果中选择“Azure Active Directory”。
1. 在左侧菜单中的 "**管理**" 下, 选择 "**应用注册 (旧)** "。
1. 选择“新建应用程序注册”。
1. 对于“名称”，请输入 `IdentityExperienceFramework`。
1. 对于“应用程序类型”，请选择“Web 应用/API”。
1. 对于“登录 URL”，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是你的 Azure AD B2C 租户域名。 现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。
1. 单击“创建”。 创建后，复制应用程序 ID 并将其保存以供日后使用。

### <a name="register-the-proxyidentityexperienceframework-application"></a>注册 ProxyIdentityExperienceFramework 应用程序

1. 在**应用注册 (旧版)** 中, 选择 "**新应用程序注册**"。
2. 对于“名称”，请输入 `ProxyIdentityExperienceFramework`。
3. 对于“应用程序类型”，请选择“本机”。
4. 对于“重定向 URI”，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是你的 Azure AD B2C 租户。
5. 单击“创建”。 创建后，复制应用程序 ID 并将其保存以供日后使用。
6. 在“设置”页上，选择“所需权限”，然后选择“添加”。
7. 选择 "**选择 API**", 搜索并选择 " **IdentityExperienceFramework**", 然后单击 "**选择**"。
9. 选择“访问 IdentityExperienceFramework”旁边的复选框，单击“选择”，然后单击“完成”。
10. 选择“授予权限”，然后选择“是”进行确认。

## <a name="custom-policy-starter-pack"></a>自定义策略初学者包

自定义策略是上传到 Azure AD B2C 租户的一组 XML 文件, 用于定义技术配置文件和用户旅程。 我们提供了一些入门包, 其中包含多个预先构建的策略, 可让你快速开始。 其中每个初学者包都包含实现所述方案所需的最少技术配置文件和用户旅程:

- **LocalAccounts** -仅启用本地帐户。
- **SocialAccounts** -仅允许使用社交 (或联合) 帐户。
- **SocialAndLocalAccounts** -允许同时使用本地帐户和社交帐户。
- **SocialAndLocalAccountsWithMFA** -启用社交、本地和多重身份验证选项。

每个初学者包中包含：

- **基本文件**-基本需要修改少量项。 例如：*Trustframeworkbase.xml*
- **扩展文件**-在此文件中进行大部分配置更改。 例如：*Trustframeworkextensions.xml*
- **依赖方文件**-应用程序调用的特定于任务的文件。 例如：*Signuporsignin.xml*、 *profileedit.xml*、 *PasswordReset*

本文介绍如何在**SocialAndLocalAccounts**初学者包中编辑 XML 自定义策略文件。 如果需要 XML 编辑器, 请尝试[Visual Studio Code](https://code.visualstudio.com/download)轻型跨平台编辑器。

### <a name="get-the-starter-pack"></a>获取 starter pack

从 GitHub 获取自定义策略初学者包, 然后用 Azure AD B2C 租户名称更新 SocialAndLocalAccounts 初学者包中的 XML 文件。

1. [下载 .zip 文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或克隆存储库:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在**SocialAndLocalAccounts**目录中的所有文件中, 将字符串`yourtenant`替换为 Azure AD B2C 租户的名称。

    例如, 如果 B2C 租户的名称为*contosotenant*, 则的`yourtenant.onmicrosoft.com`所有实例都将变为。 `contosotenant.onmicrosoft.com`

### <a name="add-application-ids-to-the-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 *TrustFrameworkExtensions.xml*。

1. 打开`SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`并**找到元素。`TrustFrameworkExtensions.xml`**
1. 将的`IdentityExperienceFrameworkAppId`两个实例替换为前面创建的 IdentityExperienceFramework 应用程序的应用程序 ID。
1. 将的`ProxyIdentityExperienceFrameworkAppId`两个实例替换为前面创建的 ProxyIdentityExperienceFramework 应用程序的应用程序 ID。
1. 保存该文件。

## <a name="upload-the-policies"></a>上传策略

1. 在 Azure 门户中选择 B2C 租户中的 "**标识体验框架**" 菜单项。
1. 选择 "**上载自定义策略**"。
1. 按照此顺序上传策略文件:
    1. *Trustframeworkbase.xml*
    1. *Trustframeworkextensions.xml*
    1. *Signuporsignin.xml*
    1. *Profileedit.xml*
    1. *PasswordReset*

上传文件时, Azure 会将前缀`B2C_1A_`添加到每个文件中。

> [!TIP]
> 如果你的 xml 编辑器支持验证, 则根据`TrustFrameworkPolicy_0.3.0.0.xsd`位于 starter 包根目录中的 XML 架构验证文件。 XML 架构验证在上传之前会识别错误。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在 "**自定义策略**" 下，选择**B2C_1A_signup_signin**。
1. 对于自定义策略的 "概述" 页上的 "**选择应用程序**", 请选择前面注册的名为*webapp1*的 web 应用程序。
1. 请确保**回复 URL**是`https://jwt.ms`。
1. 选择“立即运行”。
1. 使用电子邮件地址注册。
1. 选择 "**立即运行**"。
1. 使用相同的帐户登录，以确认配置正确。

## <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者

1. 完成[设置使用 facebook 帐户的注册和登录](active-directory-b2c-setup-fb-app.md)中的步骤, 以配置 facebook 应用程序。
1. 在文件中`client_id` , 将的值替换为 Facebook 应用程序 ID: `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`**

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 将 TrustFrameworkExtensions.xml 文件上传到租户。
1. 在 "**自定义策略**" 下，选择**B2C_1A_signup_signin**。
1. 选择 "**立即运行**", 然后选择 "facebook", 以通过 facebook 登录并测试自定义策略。 或者, 直接从已注册的应用程序调用该策略。

## <a name="next-steps"></a>后续步骤

接下来, 尝试添加 Azure Active Directory (Azure AD) 作为标识提供者。 本入门指南中使用的基本文件已包含添加其他标识提供程序 (如 Azure AD) 所需的一些内容。

有关将 Azure AD 设置为和标识提供程序的信息, 请参阅[使用 Active Directory B2C 自定义策略设置 Azure Active Directory 帐户的注册和登录](active-directory-b2c-setup-aad-custom.md)。
