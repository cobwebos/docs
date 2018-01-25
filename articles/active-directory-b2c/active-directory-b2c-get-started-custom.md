---
title: "Azure Active Directory B2C：自定义策略入门 | Microsoft Docs"
description: "如何开始使用 Azure Active Directory B2C 自定义策略"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: mtillman
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja;parahk;gsacavdm
ms.openlocfilehash: 86b86c7c670b34b4f3303adbcb55aff8d5edb53a
ms.sourcegitcommit: 2a70752d0987585d480f374c3e2dba0cd5097880
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/19/2018
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C：自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文中的步骤后，你的自定义策略将支持通过电子邮件地址和密码进行“本地帐户”注册或登录。 此外，还要准备好环境，以便能够添加标识提供者（如 Facebook 或 Azure Active Directory）。 建议先完成这些步骤，然后再阅读 Azure Active Directory (Azure AD) B2C 标识体验框架的其他用法。

## <a name="prerequisites"></a>先决条件

确保你拥有 Azure AD B2C 租户（用于保存所有用户、应用、策略等资源的容器），然后再继续操作。 如果没有，则需要[创建 Azure AD B2C 租户](active-directory-b2c-get-started.md)。 我们强烈建议所有开发人员在继续下一步之前，先完成 Azure AD B2C 内置策略演练，并使用内置策略配置他们的应用程序。 对策略名称进行略微更改后，应用程序会使用两种类型的策略，以调用自定义策略。

>[!NOTE]
>访问自定义策略编辑功能需要将一个有效的 Azure 订阅链接到租户。 如果尚未[将 Azure AD B2C 租户链接到 Azure 订阅](active-directory-b2c-how-to-enable-billing.md)，或者你的 Azure 订阅已被禁用，则“标识体验框架”按钮将不可用。

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>将签名和加密密钥添加到 B2C 租户，供自定义策略使用

1. 打开 Azure AD B2C 租户设置中的“标识体验框架”边栏选项卡。
2. 选择“策略密钥”，查看租户中的可用密钥。
3. 如果不存在 B2C_1A_TokenSigningKeyContainer，请创建一个：<br>
    a. 选择“添加”。 <br>
    b. 然后选择“生成”。<br>
    c. 使用 `TokenSigningKeyContainer` 作为“名称”。 <br> 
    可能会自动添加前缀 `B2C_1A_`。<br>
    d.单击“下一步”。 使用 RSA 作为“密钥类型”。<br>
    e. 对“日期”使用默认值。 <br>
    f. 使用“签名”作为“密钥用法”。<br>
    g. 选择“创建”。<br>
4. 如果不存在 B2C_1A_TokenEncryptionKeyContainer，请创建一个：<br>
 a. 选择“添加”。<br>
 b. 然后选择“生成”。<br>
 c. 使用 `TokenEncryptionKeyContainer` 作为“名称”。 <br>
   可能会自动添加前缀 `B2C_1A`_。<br>
 d.单击“下一步”。 使用 RSA 作为“密钥类型”。<br>
 e. 对“日期”使用默认值。<br>
 f. 使用“加密”作为“密钥用法”。<br>
 g. 选择“创建”。<br>
5. 创建 B2C_1A_FacebookSecret。 <br>
如果已有 Facebook 应用程序机密，请将它作为策略密钥添加到租户。 否则，必须创建包含占位符值的密钥，使策略能够通过验证。<br>
 a. 选择“添加”。<br>
 b. 对于“选项”，使用“手动”。<br>
 c. 使用 `FacebookSecret` 作为“名称”。 <br>
 可能会自动添加前缀 `B2C_1A_`。<br>
 d.单击“下一步”。 在“机密”框中，输入 developers.facebook.com 提供的 FacebookSecret，或输入 `0` 作为占位符。 *这不是 Facebook 应用 ID。* <br>
 e. 使用“签名”作为“密钥用法”。 <br>
 f. 选择“创建”并确认创建。

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求注册两个额外的应用程序，引擎使用这些应用程序来为用户注册和登录。

>[!NOTE]
>必须创建两个使用本地帐户实现登录的应用程序：IdentityExperienceFramework（Web 应用），以及具有 IdentityExperienceFramework 应用委派的权限的 ProxyIdentityExperienceFramework（本机应用）。 本地帐户只在租户中存在。 你的用户使用唯一的“电子邮件地址/密码”组合登录，以访问租户注册的应用程序。

### <a name="create-the-identityexperienceframework-application"></a>创建 IdentityExperienceFramework 应用程序

1. 在 [Azure 门户](https://portal.azure.com) 中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)。
2. 打开“Azure Active Directory”边栏选项卡（不是“Azure AD B2C”边栏选项卡）。 可能需要选择“更多服务”才能找到它。
3. 选择“应用注册”。
4. 选择“新建应用程序注册”。
   * 使用 `IdentityExperienceFramework` 作为“名称”。
   * 对于“应用程序类型”，使用“Web 应用/API”。
   * 对于“登录 URL”，使用 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中 `yourtenant` 是你的 Azure AD B2C 租户域名。
5. 选择“创建”。
6. 创建后，选择新创建的应用程序 IdentityExperienceFramework。<br>
   * 选择“属性”。<br>
   * 复制应用程序 ID 并保存以备后用。

### <a name="create-the-proxyidentityexperienceframework-application"></a>创建 ProxyIdentityExperienceFramework 应用程序

1. 选择“应用注册”。
1. 选择“新建应用程序注册”。
   * 使用 `ProxyIdentityExperienceFramework` 作为“名称”。
   * 对于“应用程序类型”，使用“本机”。
   * 对于“重定向 URI”，使用 `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中 `yourtenant` 是你的 Azure AD B2C 租户。
1. 选择“创建”。
1. 创建后，选择应用程序 ProxyIdentityExperienceFramework。<br>
   * 选择“属性”。 <br>
   * 复制应用程序 ID 并保存以备后用。
1. 选择“所需权限”。
1. 选择“添加”。
1. 选择“选择 API”。
1. 搜索名称 IdentityExperienceFramework。 在结果中选择 IdentityExperienceFramework，然后单击“选择”。
1. 选择“访问 IdentityExperienceFramework”旁边的复选框，然后单击“选择”。
1. 选择“完成”。
1. 选择“授予权限”，然后选择“是”进行确认。

## <a name="download-starter-pack-and-modify-policies"></a>下载初学者包并修改策略

自定义策略是需要上传到 Azure AD B2C 租户的一组 XML 文件。 我们提供了初学者包来帮助你快速入门。 以下列表中的每个初学者包中包含实现所述方案所需的最少技术配置文件和用户旅程：
 * LocalAccounts。 仅启用本地帐户。
 * SocialAccounts。 仅启用社交（或联合）帐户。
 * SocialAndLocalAccounts。 我们使用此文件进行演练。
 * SocialAndLocalAccountsWithMFA。 包含社交、本地和多重身份验证选项。

每个初学者包中包含：

* 策略的[基本文件](active-directory-b2c-overview-custom.md#policy-files)。 需要对基本文件进行少量的修改。
* 策略的[扩展文件](active-directory-b2c-overview-custom.md#policy-files)。  大多数配置更改会在此文件中进行。
* [信赖方文件](active-directory-b2c-overview-custom.md#policy-files)是应用程序调用的任务特定文件。

>[!NOTE]
>如果 XML 编辑器支持验证，则根据初学者包的根目录中的 TrustFrameworkPolicy_0.3.0.0.xsd XML 架构来验证文件。 XML 架构验证在上传之前会识别错误。

 让我们开始吧：

1. 从 GitHub 下载“active-directory-b2c-custom-policy-starterpack”。 [下载 .zip 文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)或运行

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. 打开 SocialAndLocalAccounts 文件夹。  此文件夹中的基本文件 (TrustFrameworkBase.xml) 包含本地帐户和社交/企业帐户所需的内容。 社交内容不会妨碍启动和运行本地帐户的步骤。
3. 打开 TrustFrameworkBase.xml。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。
4. 在根 `TrustFrameworkPolicy` 元素中更新 `TenantId` 和 `PublicPolicyUri` 属性，并将 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户的域名：
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` 是在门户中显示的策略名称，以及其他策略文件引用此策略文件时所依据的名称。

5. 保存文件。
6. 打开 TrustFrameworkExtensions.xml。 将 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便完成两处相同的更改。 在 `<TenantId>` 元素中进行相同的替换，因此总共有 3 处更改。 保存文件。
7. 打开 SignUpOrSignIn.xml。 将三处出现的 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。
8. 打开密码重置和配置文件编辑文件。 将每个文件中三处出现的 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。

### <a name="add-the-application-ids-to-your-custom-policy"></a>将应用程序 ID 添加到自定义策略
将应用程序 ID 添加到扩展文件 (`TrustFrameworkExtensions.xml`)：

1. 在扩展文件 (TrustFrameworkExtensions.xml) 中找到 `<TechnicalProfile Id="login-NonInteractive">` 元素。
2. 将 `IdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的标识体验框架应用程序的应用程序 ID。 下面是一个示例：

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. 将 `ProxyIdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的代理标识体验框架应用程序的应用程序 ID。
4. 保存扩展文件。

## <a name="upload-the-policies-to-your-tenant"></a>将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com) 中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md) ，然后打开“Azure AD B2C”边栏选项卡。
2. 选择“标识体验框架”。
3. 选择“上传策略”。

    >[!WARNING]
    >必须按以下顺序上传自定义策略文件：

1. 上传 TrustFrameworkBase.xml。
2. 上传 TrustFrameworkExtensions.xml。
3. 上传 SignUpOrSignIn.xml。
4. 上传其他策略文件。

上传某个文件时，会在策略文件的名称前面加上 `B2C_1A_`。

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略

1. 打开“Azure AD B2C 设置”并转到“标识体验框架”。

   >[!NOTE]
   >“立即运行”需要在租户中至少预先注册一个应用程序。 必须使用 Azure AD B2C 中的“应用程序”菜单选项或者使用标识体验框架调用内置策略和自定义策略，在 B2C 租户中注册应用程序。 每个应用程序只需注册一次。<br><br>
   在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。  

2. 打开上传的信赖方 (RP) 自定义策略 B2C_1A_signup_signin。 选择“立即运行”。

3. 现在，应该可以使用电子邮件地址注册。

4. 使用相同的帐户登录，以确认配置正确。

>[!NOTE]
>登录失败的常见原因是 IdentityExperienceFramework 应用的配置不当。


## <a name="next-steps"></a>后续步骤

### <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者
若要设置 Facebook，请执行以下操作：
1. [在 developers.facebook.com 中配置 Facebook 应用程序](active-directory-b2c-setup-fb-app.md)。
2. [将 Facebook 应用程序机密添加到 Azure AD B2C 租户](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)。
3. 在 TrustFrameworkExtensions 策略文件中将 `client_id` 的值替换为 Facebook 应用程序 ID：

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. 将 TrustFrameworkExtensions.xml 策略文件上传到租户。
5. 使用“立即运行”进行测试，或直接从已注册的应用程序调用该策略。

### <a name="add-azure-active-directory-as-an-identity-provider"></a>将 Azure Active Directory 添加为标识提供者
本入门指南中使用的基本文件已包含添加其他标识提供者所需的一些内容。 有关设置登录名的信息，请参阅 [Azure Active Directory B2C：使用 Azure AD 帐户登录](active-directory-b2c-setup-aad-custom.md)一文。

有关使用标识体验框架的 Azure AD B2C 中的自定义策略概述，请参阅 [Azure Active Directory B2C：自定义策略](active-directory-b2c-overview-custom.md)一文。 
