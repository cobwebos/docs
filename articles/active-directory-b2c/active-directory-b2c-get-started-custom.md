---
title: "Azure Active Directory B2C：自定义策略入门 | Microsoft Docs"
description: "如何开始使用 Azure Active Directory B2C 自定义策略"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: joroja;parahk;gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 2db2ba16c06f49fd851581a1088df21f5a87a911
ms.openlocfilehash: b72db6a0eb8a8621be5f05da6028615d5d24ba1e
ms.contentlocale: zh-cn
ms.lasthandoff: 05/09/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C：自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文中的步骤后，你的自定义策略将支持使用电子邮件和密码进行“本地帐户”注册或登录。 你还要准备好环境，以便能够添加其他标识提供者（如 Facebook 或 Azure AD）。  建议在进一步使用 Azure AD B2C 标识体验框架和介绍许多重要概念之前，先完成这些步骤。

## <a name="prerequisites"></a>先决条件

在继续之前，请确保有一个 Azure AD B2C 租户。 Azure AD B2C 租户是保存所有用户、应用、策略等资源的容器。 如果你没有租户，则需要[创建一个](active-directory-b2c-get-started.md)。 我们强烈建议所有开发人员在继续下一步之前，先完成 Azure AD B2C 内置策略演练，并使用内置策略配置他们的应用程序。  应用程序将使用两种类型的策略，并对策略名称进行轻微的更改，以调用自定义策略。

访问自定义策略编辑功能需要将一个有效的 Azure 订阅链接到租户。

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>将签名和加密密钥添加到 B2C 租户，供自定义策略使用

1. 导航到 Azure AD B2C 租户设置中的“标识体验框架”边栏选项卡。
2. 选择“策略密钥”，查看租户中的可用密钥。
3. 创建 `B2C_1A_TokenSigningKeyContainer`（如果不存在）：
 * 单击“添加”
 * “选项”> `Generate`
 * “名称”> `TokenSigningKeyContainer` 前缀 B2C_1A_ 可以自动添加。
 * “密钥类型”> `RSA`
 * 日期 - 使用默认值
 * “密钥用法”> `Signature`
 * 单击“创建” 
4. 创建 `B2C_1A_TokenEncryptionKeyContainer`（如果不存在）：
 * 单击“添加”
 * “选项”> `Generate`
 * “名称”> `TokenEncryptionKeyContainer` 前缀 B2C_1A_ 可以自动添加。
 * “密钥类型”> `RSA`
 * 日期 - 使用默认值
 * “密钥用法”> `Encryption`
 * 单击“创建” 
5. 创建 `B2C_1A_FacebookSecret`。 如果已有 Facebook 应用程序机密，请将它作为策略密钥添加到租户。  否则，必须创建包含占位符值的密钥，使策略能够通过验证。
 * 单击“添加”
 * “选项”> `Manual`
 * “名称”> `FacebookSecret` 前缀 B2C_1A_ 可以自动添加。
 * “机密”> 输入 developers.facebook.com 提供的 FacebookSecret，或输入“0”作为占位符。 *这不是 Facebook 应用 ID*
 * “密钥用法”>“签名”
 * 单击“创建”并确认创建

## <a name="register-identity-experience-framework-applications"></a>注册标识体验框架应用程序

Azure AD B2C 要求注册两个额外的应用程序，引擎使用这些应用程序来为用户注册和登录。

>[!NOTE]
>必须创建两个使用本地帐户实现登录的应用程序：IdentityExperienceFramework（Web 应用），以及具有 IdentityExperienceFramework 应用委派的权限的 ProxyIdentityExperienceFramework（本机应用）。 本地帐户只在租户中存在。 最终用户使用唯一的“电子邮件:密码”组合登录，以访问租户注册的应用程序。

### <a name="create-the-identityexperienceframework-application"></a>创建 IdentityExperienceFramework 应用程序

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)。
1. 打开 `Azure Active Directory` 边栏选项卡（不是 Azure AD B2C 边栏选项卡）。 可能需要单击“> 更多服务”才能找到它。
1. 选择“应用注册”。
1. 单击“+ 新建应用程序注册”。
   * 名称：`IdentityExperienceFramework`
   * 应用程序类型：`Web app/API`
   * 登录 URL：`https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中，`yourtenant` 是你的 Azure AD B2C 租户域名。
1. 单击“创建” 。
1. 创建后，请选择新建的应用程序 `IdentityExperienceFramework`，单击“属性”，然后复制并保存应用程序 ID 供稍后使用。

### <a name="create-the-proxy-identity-experience-framework-application"></a>创建标识体验框架代理应用程序

1. 选择“应用注册”。
1. 单击“+ 新建应用程序注册”。
   * 名称：`ProxyIdentityExperienceFramework`
   * 应用程序类型：`Native`
   * 登录 URL：`https://login.microsoftonline.com/yourtenant.onmicrosoft.com`，其中，`yourtenant` 是你的 Azure AD B2C 租户。
1. 单击“创建” 。
1. 创建后，请选择应用程序 `ProxyIdentityExperienceFramework`，单击“属性”，然后复制并保存应用程序 ID 供稍后使用。
1. 选择“所需权限”，然后依次选择“+ 添加”、“选择 API”。
1. 搜索名称 `IdentityExperienceFramework`，在结果中选择“IdentityExperienceFramework”，然后单击“选择”。
1. 选中 `Access IdentityExperienceFramework` 旁边的复选框，然后单击“选择”。
1. 单击“Done”（完成） 。
1. 单击“授予权限”，然后单击“是”确认

## <a name="download-starter-pack-and-modify-policies"></a>下载初学者包并修改策略

自定义策略是需要上传到 Azure AD B2C 租户的一组 XML 文件。 我们提供了初学者包来帮助你快速入门。 以下每个初学者包提供了实现所述方案而最起码需要用到的技术配置文件和用户旅程：
 * LocalAccounts - 仅启用本地帐户
 * SocialAccounts - 仅启用社交（或联合）帐户
 * **SocialAndLocalAccounts** - 用于本演练
 * SocialAndLocalAccountsWithMFA - 此处包含社交、本地和 MFA 选项

每个初学者包包含：

* 策略的[基本文件](active-directory-b2c-overview-custom.md#policy-files)。 需要对基本文件进行少量的修改。
* 策略的[扩展文件](active-directory-b2c-overview-custom.md#policy-files)。  大多数配置更改将在此文件中进行。
* [信赖方文件](active-directory-b2c-overview-custom.md#policy-files)：应用程序针对特定任务调用的、特定于任务的文件。

>[!NOTE]
>如果 XML 编辑器支持验证，你可能需要根据初学者包根文件夹中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 架构文件验证文件。 XML 架构验证在上传之前会识别错误。

让我们开始吧：

1. 从 GitHub 下载“active-directory-b2c-custom-policy-starterpack”。  [下载 zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 或运行

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. 打开 `SocialAndLocalAccounts` 文件夹。  此文件夹中的基本文件 (`TrustFrameworkBase.xml`) 包含本地帐户和社交/企业帐户所需的内容。 社交内容不会妨碍启动和运行本地帐户的步骤。
3. 打开 `TrustFrameworkBase.xml`。  如果需要 XML 编辑器，请尝试[使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型的跨平台编辑器）。
4. 在 `TrustFrameworkPolicy` 根元素中更新 `TenantId` 和 `PublicPolicyUri` 属性，并将 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户的域名：

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
>`PolicyId` 是门户中显示的策略名称，以及其他策略文件引用此策略文件时所依据的名称。

5. 保存文件。
6. 打开 `TrustFrameworkExtensions.xml` 并将 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便完成两处相同的更改。 在 `<TenantId>` 元素中进行相同的替换，因此总共有 3 处更改。  保存文件。
7. 打开 `SignUpOrSignIn.xml` 并将三处出现的 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。
8. 打开密码机密和配置文件编辑文件，并将三处出现的 `yourtenant.onmicrosoft.com` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。

### <a name="add-the-application-ids-to-your-custom-policy"></a>将应用程序 ID 添加到自定义策略
将应用程序 ID 添加到扩展文件 (`TrustFrameworkExtensions.xml`)。

1. 在扩展文件 (`TrustFrameworkExtensions.xml`) 中，找到元素 `<TechnicalProfile Id="login-NonInteractive">`。
2. 将 `IdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的标识体验框架应用程序的应用程序 ID。 下面是一个示例：

```xml
<Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
```

3. 将 `ProxyIdentityExperienceFrameworkAppId` 的两个实例替换为前面创建的代理标识体验框架应用程序的应用程序 ID。
4. 保存扩展文件。

## <a name="upload-the-policies-to-your-tenant"></a>将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开 Azure AD B2C 边栏选项卡。
2. 单击“标识体验框架”
3. 选择“上传策略”以上传策略文件

    >[!WARNING]
    >必须按以下顺序上传自定义策略文件：

1. 上传 `TrustFrameworkBase.xml`。
2. 上传 `TrustFrameworkExtensions.xml`。
3. 上传 `SignUpOrSignin.xml`。
4. 上传其他策略文件。

上传某个文件时，将在策略文件的名称前面加上 `B2C_1A_`。

## <a name="test-the-custom-policy-using-run-now"></a>使用“立即运行”测试自定义策略

1. 打开“Azure AD B2C 设置”并导航到“标识体验框架”。

>[!NOTE]
>**立即运行**：需要在租户中至少预先注册一个应用程序。 必须使用 B2C 或标识体验框架中的“应用程序”菜单选项调用内置策略和自定义策略，在 B2C 租户中注册应用程序。 每个应用程序只需注册一次。 

在 Azure AD B2C [入门](active-directory-b2c-get-started.md)或[应用程序注册](active-directory-b2c-app-registration.md)文章中了解如何注册应用程序。  

2. 打开上传的信赖方 (RP) 自定义策略 `B2C_1A_signup_signin`，然后单击“立即运行”按钮。


3. 现在，你应能使用电子邮件地址注册。
4. 使用相同的帐户登录，以确认配置正确

>[!NOTE]
>登录失败的常见原因是 IdentityExperienceFramework 应用的配置不当。


## <a name="next-steps"></a>后续步骤

### <a name="add-facebook-as-an-identity-provider"></a>将 Facebook 添加为标识提供者
设置 Facebook：
1. [在 developers.facebook.com 中配置 Facebook 应用程序](active-directory-b2c-setup-fb-app.md)
2. [将 Facebook 应用程序机密添加到 Azure AD B2C 租户](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies)
3. 在 `Item Key="client_id"` 中添加 TrustFrameworkExtensions 策略文件中的 Facebook 应用程序 ID：

```xml
<TechnicalProfile Id="Facebook-OAUTH">
  <Metadata>
  <!--Replace the value of client_id in this technical profile with your the Facebook App ID"-->
    <Item Key="client_id">00000000000000</Item>
```
4. 将 TrustFrameworkExtensions.xml 策略文件上传到租户。
5. 使用“立即运行”进行测试，或直接从已注册的应用程序调用该策略。

### <a name="add-azure-active-directory-as-an-identity-provider"></a>将 Azure Active Directory 添加为标识提供者
本入门指南中使用的基本文件已包含添加其他标识提供者所需的一些内容。 若要使用 Azure AD 帐户设置登录名，请继续阅读[此文](active-directory-b2c-setup-aad-custom.md)。


## <a name="reference"></a>引用

使用标识体验框架的 Azure AD B2C 中的自定义策略[概述](active-directory-b2c-overview-custom.md)

