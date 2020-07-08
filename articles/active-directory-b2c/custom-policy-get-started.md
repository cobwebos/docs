---
title: 自定义策略入门
titleSuffix: Azure AD B2C
description: 了解如何在 Azure Active Directory B2C 中开始使用自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 892e94ba1943b667ffeba63a80f4409b35ea5ec3
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85389286"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Azure Active Directory B2C 中的自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[自定义策略](custom-policy-overview.md)是定义 Azure Active Directory B2C (Azure AD B2C) 租户行为的配置文件。 本文将介绍如何创建支持使用电子邮件地址和密码进行本地帐户注册或登录的自定义策略。 你还要准备好环境以添加标识提供者。

## <a name="prerequisites"></a>先决条件

- 如果没有租户，请[创建链接到 Azure 订阅的 Azure AD B2C 租户](tutorial-create-tenant.md)。
- 在所创建的租户中[注册应用程序](tutorial-register-applications.md)，使其能够与 Azure AD B2C 通信。
- 完成[使用 Facebook 帐户设置注册和登录](identity-provider-facebook.md)中的步骤来配置 Facebook 应用程序。 尽管使用自定义策略不需要 Facebook 应用程序，但在本演练中，将用它来演示如何在自定义策略中实现社交登录。

## <a name="add-signing-and-encryption-keys"></a>添加签名和加密密钥

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在门户工具栏中选择“目录 + 订阅”，然后选择包含 Azure AD B2C 租户的目录。
1. 在 Azure 门户中，搜索并选择“Azure AD B2C”。
1. 在概述页面上的“策略”下，选择“Identity Experience Framework” 。

### <a name="create-the-signing-key"></a>创建签名密钥

1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Generate`。
1. 在“名称”中，请输入 `TokenSigningKeyContainer`。 可能会自动添加前缀 `B2C_1A_`。
1. 对于“密钥类型”，请选择“RSA”。
1. 对于“密钥用法”，请选择“签名” 。
1. 选择“创建”。

### <a name="create-the-encryption-key"></a>创建加密密钥

1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Generate`。
1. 在“名称”中，请输入 `TokenEncryptionKeyContainer`。 可能会自动添加前缀 `B2C_1A`_。
1. 对于“密钥类型”，请选择“RSA”。
1. 对于“密钥用法”，请选择“加密” 。
1. 选择“创建”。

### <a name="create-the-facebook-key"></a>创建 Facebook 密钥

将 Facebook 应用程序的[应用机密](identity-provider-facebook.md)添加为策略密钥。 可将你创建的应用程序的应用机密设为本文先决条件的一部分。

1. 选择“策略密钥”，然后选择“添加”。
1. 对于“选项”，请选择 `Manual`。
1. 对于“名称”，请输入 `FacebookSecret`。 可能会自动添加前缀 `B2C_1A_`。
1. 在“机密”中，输入 developers.facebook.com 提供的 Facebook 应用程序的应用机密。 此值是机密，不是应用程序 ID。
1. 对于“密钥用法”，请选择“签名” 。
1. 选择“创建”。

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求注册两个应用程序，它们分别用于通过本地帐户实现用户注册和登录：IdentityExperienceFramework（一个 Web API），以及 ProxyIdentityExperienceFramework（一个原生应用，它具有对 IdentityExperienceFramework 应用的委派权限） 。 用户可使用电子邮件地址或用户名和密码进行注册来访问租户注册的应用程序，这将创建一个“本地帐户”。 本地帐户仅存在于 Azure AD B2C 租户中。

你只需在 Azure AD B2C 租户中注册这两个应用程序一次。

### <a name="register-the-identityexperienceframework-application"></a>注册 IdentityExperienceFramework 应用程序

若要在 Azure AD B2C 租户中注册应用程序，可以使用**应用注册**体验。

1. 选择“应用注册”，然后选择“新建注册” 。
1. 对于“名称”，请输入 `IdentityExperienceFramework`。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 。
1. 在“重定向 URI”下，选择“Web”，然后输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`，其中 `your-tenant-name` 是 Azure AD B2C 租户域名 。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用。

接下来，通过添加范围公开 API：

1. 在“管理”下，选择“公开 API” 。
1. 选择“添加范围”，然后选择“保存并继续”以接受默认的应用程序 ID URI 。
1. 输入以下值，创建允许在 Azure AD B2C 租户中执行自定义策略的范围：
    * **范围名称**：`user_impersonation`
    * **管理员许可显示名称**：`Access IdentityExperienceFramework`
    * **管理员许可说明**：`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. 选择“添加范围”

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>注册 ProxyIdentityExperienceFramework 应用程序

1. 选择“应用注册”，然后选择“新建注册” 。
1. 对于“名称”，请输入 `ProxyIdentityExperienceFramework`。
1. 在“支持的帐户类型”下，选择“仅此组织目录中的帐户” 。
1. 在“重定向 URI”下，使用下拉选择“公共客户端/本机(移动和桌面)” 。
1. 对于“重定向 URI”，请输入 `myapp://auth`。
1. 在“权限”下，选择“授予对 openid 和 office_access 权限的管理员许可”复选框。
1. 选择“注册”。
1. 记录“应用程序(客户端) ID”，以便在后续步骤中使用。

接下来，指定应将应用程序视为公共客户端：

1. 在“管理”下，选择“身份验证”。 
1. 在“高级设置”下，启用“将应用程序视为公共客户端”（选择“是”）  。 请确保已在应用程序清单中设置 "allowPublicClient": true。 
1. 选择“保存”。

现在，向你先前在 IdentityExperienceFramework 注册中公开的 API 范围授予权限：

1. 在“管理”下选择“API 权限”。
1. 在“已配置权限”下，选择“添加权限”。
1. 选择“我的 API”选项卡，然后选择“IdentityExperienceFramework”应用程序 。
1. 在“权限”下，选择你先前定义的 user_impersonation 范围 。
1. 选择“添加权限”。 按照指示等待几分钟，然后继续下一步。
1. 选择“向(租户名称)授予管理员许可”。
1. 选择当前登录的管理员帐户，或者使用至少分配了“云应用程序管理员”角色的 Azure AD B2C 租户中的帐户登录。
1. 选择“接受”。
1. 选择 "**刷新**"，然后验证 "授权给 ..."显示在作用域 offline_access、openid 和 user_impersonation 的**状态**下方。 传播权限可能需要几分钟时间。

* * *

## <a name="custom-policy-starter-pack"></a>自定义策略新手包

自定义策略是你上传到 Azure AD B2C 租户的一组 XML 文件，用于定义技术配置文件和用户旅程。 我们提供了新手包，其中有多个预建策略来帮助你快速入门。 每个新手包中都有实现所述方案必需的最少数量的技术配置文件和用户旅程：

- **LocalAccounts** - 仅允许使用本地帐户。
- **SocialAccounts** - 仅允许使用社交（或联合）帐户。
- **SocialAndLocalAccounts** - 允许使用本地帐户和社交帐户。
- **SocialAndLocalAccountsWithMFA** - 启用社交、本地和多重身份验证选项。

每个新手包中包含：

- **基本文件** - 需要对基本文件进行少量的修改。 示例：TrustFrameworkBase.xml
- **扩展文件** - 大多数配置更改在此文件中进行。 示例：TrustFrameworkExtensions.xml
- **信赖方文件** - 应用程序调用的任务特定文件。 示例：SignUpOrSignin.xml、ProfileEdit.xml、PasswordReset.xml  

在本文中，你将编辑 SocialAndLocalAccounts 新手包中的 XML 自定义策略文件。 如果需要 XML 编辑器，请尝试使用 [Visual Studio Code](https://code.visualstudio.com/download)，它是一种轻型跨平台编辑器。

### <a name="get-the-starter-pack"></a>获取新手包

从 GitHub 获取自定义策略新手包，然后使用 Azure AD B2C 租户名称更新 SocialAndLocalAccounts 新手包中的 XML 文件。

1. [下载 .zip 文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或克隆存储库：

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 在 SocialAndLocalAccounts 目录中的所有文件中，将字符串 `yourtenant` 替换为 Azure AD B2C 租户的名称。

    例如，如果 B2C 租户的名称为 contosotenant，则 `yourtenant.onmicrosoft.com` 的所有实例都将变为 `contosotenant.onmicrosoft.com`。

### <a name="add-application-ids-to-the-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 *TrustFrameworkExtensions.xml*。

1. 打开 `SocialAndLocalAccounts/``TrustFrameworkExtensions.xml` 并找到元素 `<TechnicalProfile Id="login-NonInteractive">`。
1. 将 `IdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的 IdentityExperienceFramework 应用程序的 ID。
1. 将 `ProxyIdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的 ProxyIdentityExperienceFramework 应用程序的 ID。
1. 保存文件。

## <a name="upload-the-policies"></a>上传策略

1. 在 Azure 门户的 B2C 租户中，选择“Identity Experience Framework”菜单项。
1. 选择“上传自定义策略”。
1. 按照下列顺序上传策略文件：
    1. TrustFrameworkBase.xml
    1. TrustFrameworkExtensions.xml
    1. SignUpOrSignin.xml
    1. ProfileEdit.xml
    1. PasswordReset.xml

上传文件时，Azure 会向每个文件添加前缀 `B2C_1A_`。

> [!TIP]
> 如果 XML 编辑器支持验证，则根据新手包根目录中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 架构来验证文件。 XML 架构验证在上传之前会识别错误。

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 对于自定义策略概述页面上的“选择应用程序”，选择先前注册的名为“webapp1”的 Web 应用程序。
1. 请确保回复 URL 为 `https://jwt.ms`。
1. 选择“立即运行”。
1. 使用电子邮件地址注册。
1. 再次选择“立即运行”。
1. 使用相同的帐户登录，以确认配置正确。

## <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者

如[先决条件](#prerequisites)中所述，使用自定义策略不需要 Facebook，但在此处用它来演示如何在自定义策略中启用联合社交登录。

1. 在 `SocialAndLocalAccounts/``TrustFrameworkExtensions.xml` 文件中，将 `client_id` 的值替换为 Facebook 应用程序 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. 将 TrustFrameworkExtensions.xml 文件上传到租户。
1. 在“自定义策略”下，选择“B2C_1A_signup_signin” 。
1. 选择“立即运行”，然后选择 Facebook，通过 Facebook 登录并测试自定义策略。

## <a name="next-steps"></a>后续步骤

接下来，尝试添加 Azure Active Directory (Azure AD) 作为标识提供者。 本入门指南中使用的基本文件已包含添加其他标识提供者（如 Azure AD）所需的一些内容。

要了解如何将 Azure AD 设置为标识提供者，请参阅[使用 Active Directory B2C 自定义策略设置 Azure Active Directory 帐户的注册和登录](identity-provider-azure-ad-single-tenant-custom.md)。
