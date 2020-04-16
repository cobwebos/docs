---
title: 自定义策略入门
titleSuffix: Azure AD B2C
description: 了解如何在 Azure 活动目录 B2C 中开始自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407290"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自定义策略](custom-policy-overview.md)是定义 Azure 活动目录 B2C （Azure AD B2C） 租户行为的配置文件。 本文将介绍如何创建支持使用电子邮件地址和密码进行本地帐户注册或登录的自定义策略。 你还要准备好环境以添加标识提供者。

## <a name="prerequisites"></a>先决条件

- 如果还没有，请创建链接到 Azure 订阅的[Azure AD B2C 租户](tutorial-create-tenant.md)。
- 在创建的租户中[注册应用程序](tutorial-register-applications.md)，以便它可以与 Azure AD B2C 通信。
- 使用 Facebook[帐户完成设置注册和登录](identity-provider-facebook.md)中的步骤，以配置 Facebook 应用程序。 尽管使用自定义策略不需要 Facebook 应用程序，但本演练中使用它来演示自定义策略中的启用社交登录。

## <a name="add-signing-and-encryption-keys"></a>添加签名和加密密钥

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”图标，然后选择包含 Azure AD B2C 租户的目录****。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”****。
1. 在概述页上，在 **"策略"** 下，选择 **"身份体验框架**"。

### <a name="create-the-signing-key"></a>创建签名密钥

1. 选择“策略密钥”****，然后选择“添加”****。
1. 对于“选项”****，请选择 `Generate`。
1. 在“名称”**** 中，请输入 `TokenSigningKeyContainer`。 可能会自动添加前缀 `B2C_1A_`。
1. 对于“密钥类型”****，请选择“RSA”****。
1. 对于“密钥用法”，请选择“签名”********。
1. 选择“创建”  。

### <a name="create-the-encryption-key"></a>创建加密密钥

1. 选择“策略密钥”****，然后选择“添加”****。
1. 对于“选项”****，请选择 `Generate`。
1. 在“名称”**** 中，请输入 `TokenEncryptionKeyContainer`。 可能会自动添加前缀 `B2C_1A`_。
1. 对于“密钥类型”****，请选择“RSA”****。
1. 对于“密钥用法”，请选择“加密”********。
1. 选择“创建”  。

### <a name="create-the-facebook-key"></a>创建 Facebook 密钥

将 Facebook 应用程序[的应用密钥](identity-provider-facebook.md)添加为策略密钥。 您可以使用作为本文先决条件一部分创建的应用程序的应用机密。

1. 选择“策略密钥”****，然后选择“添加”****。
1. 对于“选项”****，请选择 `Manual`。
1. 对于“名称”  ，请输入 `FacebookSecret`。 可能会自动添加前缀 `B2C_1A_`。
1. 在 **"秘密**"中，从developers.facebook.com输入 Facebook*应用程序的应用程序机密*。 此值是机密，而不是应用程序 ID。
1. 对于“密钥用法”，请选择“签名”********。
1. 选择“创建”  。

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求您注册两个应用程序，用于使用本地帐户注册和登录用户：*身份体验框架*、Web API 和*代理身份体验框架*，一个具有委派到身份体验框架应用的本机应用。 您的用户可以使用电子邮件地址或用户名和密码注册以访问租户注册的应用程序，这将创建一个"本地帐户"。 本地帐户仅存在于 Azure AD B2C 租户中。

只需在 Azure AD B2C 租户中注册这两个应用程序一次。

### <a name="register-the-identityexperienceframework-application"></a>注册 IdentityExperienceFramework 应用程序

要在 Azure AD B2C 租户中注册应用程序，可以使用**应用注册（旧版）** 体验或我们新的统一**应用注册（预览）** 体验。 [详细了解此新体验](https://aka.ms/b2cappregintro)。

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在 Azure 门户中，搜索并选择“Azure Active Directory”。****
1. 在**Azure 活动目录**概述菜单中，在 **"管理**"下，选择**应用注册（旧版）。**
1. 选择“新建应用程序注册”****。
1. 对于“名称”  ，请输入 `IdentityExperienceFramework`。
1. 对于“应用程序类型”****，请选择“Web 应用/API”****。
1. 对于“登录 URL”****，请输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是你的 Azure AD B2C 租户域名。 现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。
1. 选择“创建”  。 创建后，复制应用程序 ID 并将其保存以供日后使用。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 选择“应用注册(预览版)”，然后选择“新建注册”********。
1. 对于“名称”  ，请输入 `IdentityExperienceFramework`。
1. 在 **"支持帐户类型**"下，**选择"仅在此组织目录中的帐户**"。
1. 在**重定向 URI**下，选择**Web** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，然后`your-tenant-name`输入 Azure AD B2C 租户域名所在的 位置。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框******。
1. 选择“注册”  。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用****。

接下来，通过添加作用域来公开 API：

1. 在“管理”下，选择“公开 API”********。
1. 选择 **"添加作用域**"，然后选择 **"保存并继续**接受默认应用程序 ID URI"。
1. 输入以下值以创建允许在 Azure AD B2C 租户中执行自定义策略的范围：
    * **范围名称**：`user_impersonation`
    * **管理员同意显示名称**：`Access IdentityExperienceFramework`
    * **管理员同意说明**：`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. **** 选择“添加范围”

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>注册 ProxyIdentityExperienceFramework 应用程序

#### <a name="applications"></a>[应用程序](#tab/applications/)

1. 在**应用注册（旧版）** 中，选择 **"新建应用程序注册**"。
1. 对于“名称”  ，请输入 `ProxyIdentityExperienceFramework`。
1. 对于“应用程序类型”，请选择“本机”********。
1. 对于**重定向 URI，** 请`myapp://auth`输入 。
1. 选择“创建”  。 创建后，复制应用程序 ID 并将其保存以供日后使用。
1. 选择 **"设置**"，然后选择 **"必需权限**"，然后选择"**添加**"。
1. 选择 **"选择 API"，** 搜索并选择 **"身份体验框架**"，然后单击"**选择**"。
1. 选择“访问 IdentityExperienceFramework”**** 旁边的复选框，单击“选择”****，然后单击“完成”****。
1. 选择 **"授予权限**"，然后选择"**是**"进行确认。

#### <a name="app-registrations-preview"></a>[应用注册（预览版）](#tab/app-reg-preview/)

1. 选择“应用注册(预览版)”，然后选择“新建注册”********。
1. 对于“名称”  ，请输入 `ProxyIdentityExperienceFramework`。
1. 在 **"支持帐户类型**"下，**选择"仅在此组织目录中的帐户**"。
1. 在“重定向 URI”下，使用下拉选择“公共客户端/本机(移动和桌面)”********。
1. 对于**重定向 URI，** 请`myapp://auth`输入 。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框******。
1. 选择“注册”  。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用****。

接下来，指定应用程序应视为公共客户端：

1. 在“管理”下，选择“身份验证”。********
1. 选择“尝试新体验”（如果已显示）。****
1. 在 **"高级设置**"下，启用**将应用程序视为公共客户端**（选择 **"是**" 。
1. 选择“保存”。 

现在，向*在身份体验框架*注册中前面公开的 API 作用域授予权限：

1. 在“管理”下选择“API 权限”  。 
1. 在“已配置权限”  下，选择“添加权限”  。
1. 选择"**我的 API"** 选项卡，然后选择 **"身份体验框架"** 应用程序。
1. 在 **"权限**"下，选择之前定义的**user_impersonation**作用域。
1. 选择“添加权限”  。 按照指示等待几分钟，然后继续下一步。
1. 选择“向(租户名称)授予管理员许可”  。
1. 选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”** 角色的 Azure AD B2C 租户中的帐户登录。
1. 选择“接受”****。
1. 选择“刷新”，然后确认两个范围的“状态”下是否均显示“已授予...”。   传播权限可能需要几分钟时间。

* * *

## <a name="custom-policy-starter-pack"></a>自定义策略启动包

自定义策略是一组 XML 文件，用于定义技术配置文件和用户旅程。 我们提供入门包与几个预先构建的策略，让您快速前进。 每个初学者包都包含实现上述方案所需的最少数量的技术配置文件和用户旅程：

- **本地帐户**- 仅启用本地帐户。
- **社交帐户**- 仅启用社交（或联合）帐户。
- **社交和本地帐户**- 支持使用本地帐户和社会帐户。
- **社交和本地帐户与MFA** - 启用社交、本地和多重身份验证选项。

每个初学者包中包含：

- **基本文件**- 基本文件不需要修改。 示例：*信任框架基础.xml*
- **扩展文件**- 此文件是进行大多数配置更改的地方。 示例：*信任框架扩展.xml*
- **依赖方文件**- 应用程序调用的任务特定文件。 示例：*注册或Signin.xml*，*配置文件编辑.xml*，*密码重置.xml*

在本文中，您可以在**社交和本地帐户**初学者包中编辑 XML 自定义策略文件。 如果您需要 XML 编辑器，请尝试[可视化工作室代码](https://code.visualstudio.com/download)，一个轻量级的跨平台编辑器。

### <a name="get-the-starter-pack"></a>获取入门包

从 GitHub 获取自定义策略初学者包，然后使用 Azure AD B2C 租户名称更新社交和本地帐户初学者包中的 XML 文件。

1. [下载 .zip 文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或克隆存储库：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在**社交和本地帐户**目录中的所有文件中，将字符串`yourtenant`替换为 Azure AD B2C 租户的名称。

    例如，如果 B2C 租户的名称是*contoso 租户*，则 的所有`yourtenant.onmicrosoft.com`实例都`contosotenant.onmicrosoft.com`变为 。

### <a name="add-application-ids-to-the-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 *TrustFrameworkExtensions.xml*。

1. 打开`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 并查找元素`<TechnicalProfile Id="login-NonInteractive">`。
1. 将 中的`IdentityExperienceFrameworkAppId`两个实例替换为您之前创建的标识体验框架应用程序的应用程序 ID。
1. 将 中的`ProxyIdentityExperienceFrameworkAppId`两个实例替换为之前创建的代理身份体验框架应用程序的应用程序 ID。
1. 保存文件。

## <a name="upload-the-policies"></a>上传策略

1. 在 Azure 门户中的 B2C 租户中选择 **"身份体验框架"** 菜单项。
1. 选择 **"上载自定义策略**"。
1. 按此顺序上载策略文件：
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *注册或Signin.xml*
    1. *配置文件编辑.xml*
    1. *密码重置.xml*

上载文件时，Azure 会将前缀`B2C_1A_`添加到每个文件。

> [!TIP]
> 如果 XML 编辑器支持验证，请根据位于初学者`TrustFrameworkPolicy_0.3.0.0.xsd`包根目录中的 XML 架构验证文件。 XML 架构验证在上传之前会识别错误。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在**自定义策略**下，选择**B2C_1A_signup_signin。**
1. 有关在自定义策略的概述页上**选择应用程序**，请选择以前注册的名为*Webapp1*的 Web 应用程序。
1. 确保**回复 URL**为`https://jwt.ms`。
1. 选择“立即运行”****。
1. 使用电子邮件地址注册。
1. 再次选择 **"立即运行**"。
1. 使用相同的帐户登录，以确认配置正确。

## <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者

如[先决条件](#prerequisites)中所述，Facebook*不需要*使用自定义策略，但在此处用于演示如何在自定义策略中启用联合社交登录。

1. 在`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`** 文件中，将 的值`client_id`替换为 Facebook 应用程序 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 将 TrustFrameworkExtensions.xml** 文件上传到租户。
1. 在**自定义策略**下，选择**B2C_1A_signup_signin。**
1. 选择 **"立即运行**"，选择 Facebook 以使用 Facebook 登录并测试自定义策略。

## <a name="next-steps"></a>后续步骤

接下来，尝试添加 Azure 活动目录 （Azure AD） 作为标识提供程序。 此入门指南中使用的基本文件已包含添加其他标识提供程序（如 Azure AD）所需的一些内容。

有关将 Azure AD 设置为 和标识提供程序的信息，请参阅[使用活动目录 B2C 自定义策略使用 Azure 活动目录帐户设置注册和登录](identity-provider-azure-ad-single-tenant-custom.md)。
