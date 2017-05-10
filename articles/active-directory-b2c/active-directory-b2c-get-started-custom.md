---
title: "Azure Active Directory B2C：自定义策略入门 | Microsoft Docs"
description: "介绍如何开始使用 Azure Active Directory B2C 自定义策略的主题"
services: active-directory-b2c
documentationcenter: 
author: gsacavdm
manager: krassk
editor: parakhj
ms.assetid: 658c597e-3787-465e-b377-26aebc94e46d
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/04/2017
ms.author: gsacavdm
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 93fc922433f0ba60ee438db8d4dcb14527c208fc
ms.contentlocale: zh-cn
ms.lasthandoff: 05/02/2017

---
# <a name="azure-active-directory-b2c-getting-started-with-custom-policies"></a>Azure Active Directory B2C：自定义策略入门

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

完成本文中的步骤后，你的自定义策略将支持使用电子邮件和密码进行“本地帐户”注册或登录。 你还要准备好环境，以便能够添加其他标识提供者（如 Facebook 或 Azure AD）。  在进一步使用 Azure AD B2C 标识体验引擎和介绍许多重要概念之前，必须先完成这些步骤。

## <a name="prerequisites"></a>先决条件

在继续之前，请确保有一个 Azure AD B2C 租户。 Azure AD B2C 租户是保存所有用户、应用、策略等资源的容器。 如果你没有租户，则需要[创建一个](active-directory-b2c-get-started.md)。

### <a name="confirming-your-b2c-tenant"></a>确认 B2C 租户

由于自定义策略仍以个人预览提供，因此请确认为自定义策略上传启用了 Azure AD B2C 租户：

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开 Azure AD B2C 边栏选项卡。
1. 单击“所有策略”。
1. 确保“上传策略”按钮可用。  如果该按钮被禁用，请向 AADB2CPreview@microsoft.com 发送电子邮件。

## <a name="set-up-keys-for-your-custom-policy"></a>设置自定义策略的密钥

使用自定义策略所要执行的第一个步骤是设置密钥。

[!INCLUDE [active-directory-b2c-setup-keys-custom.md](../../includes/active-directory-b2c-setup-keys-custom.md)]

## <a name="download-starter-pack-and-modify-policies"></a>下载初学者包并修改策略

自定义策略是需要上传到 Azure AD B2C 租户的一组 XML 文件。 我们提供了一个帮助你入门的初学者包。 该初学者包中包含：

* 策略的[基本文件](active-directory-b2c-overview-custom.md#policy-files)。 需要对基本文件进行少量的修改。
* 策略的[扩展文件](active-directory-b2c-overview-custom.md#policy-files)。  大多数配置更改将在此文件中进行。

让我们开始吧：

1. 从 GitHub 下载“Azure AD B2C 自定义策略初学者包”。  [下载 zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) 或运行

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. 打开 `SocialIDPAndLocalAccounts` 文件夹。  此文件夹中的基本文件 (`TrustFrameworkBase.xml`) 包含本地帐户和社交/企业帐户所需的内容。 社交内容不会妨碍启动和运行本地帐户的步骤。
1. 打开 `TrustFrameworkBase.xml`。  如果需要 XML 编辑器，请尝试[下载 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型的跨平台编辑器）。
1. 在 `TrustFrameworkPolicy` 根元素中更新 `TenantId` 和 `PublicPolicyUri` 属性，并将 `{tenantName}` 替换为你的 Azure AD B2C 租户：

    ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="{tenantName}.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://{tenantName}.onmicrosoft.com">
    ```

1. 保存文件。
1. 打开 `TrustFrameworkExtensions.xml` 并将 `{tenantName}` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。
1. 打开 `SignUpOrSignIn.xml` 并将 `{tenantName}` 替换为你的 Azure AD B2C 租户，以便进行相同的更改。 保存文件。

>[!NOTE]
>如果 XML 编辑器支持验证，你可能需要根据初学者包根文件夹中的 `TrustFrameworkPolicy_0.3.0.0.xsd` XML 架构文件验证文件。 XML 架构验证在上传之前会识别错误。

## <a name="register-policy-engine-applications"></a>注册策略引擎应用程序

Azure AD B2C 要求注册两个额外的应用程序，引擎使用这些应用程序来为用户注册和登录。

>[!NOTE]
>下面，我们将创建两个用于通过本地帐户实现登录的应用程序：PolicyEngine（Web 应用），以及具有 PolicyEngine 委派的权限的 PolicyEngineProxy（本机应用）。 只需对预期使用本地帐户的 Azure AD B2C 租户执行本部分中的步骤。

### <a name="create-the-policy-engine-application"></a>创建策略引擎应用程序

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)。
1. 打开 `Azure Active Directory` 边栏选项卡（不是 Azure AD B2C 边栏选项卡）。 可能需要单击“> 更多服务”才能找到它。
1. 选择“应用注册”。
1. 单击“+ 新建应用程序注册”。
   * 名称：`PolicyEngine`
   * 应用程序类型：`Web app/API`
   * 登录 URL：`https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`，其中，`{tenantName}` 是你的 Azure AD B2C 租户。
1. 单击“创建” 。
1. 创建后，请选择新建的应用程序 `PolicyEngine`，然后复制并保存应用程序 ID 供稍后使用。

### <a name="create-the-policy-engine-proxy-application"></a>创建策略引擎代理应用程序

1. 选择“应用注册”。
1. 单击“+ 新建应用程序注册”。
   * 名称：`PolicyEngineProxy`
   * 应用程序类型：`Native`
   * 登录 URL：`https://login.microsoftonline.com/{tenantName}.onmicrosoft.com`，其中，`{tenantName}` 是你的 Azure AD B2C 租户。
1. 单击“创建” 。
1. 创建后，请选择应用程序 `PolicyEngineProxy`，然后复制并保存应用程序 ID 供稍后使用。
1. 选择“所需权限”，然后依次选择“+ 添加”、“选择 API”。
1. 搜索名称 `PolicyEngine`，在结果中选择 PolicyEngine，然后单击“选择”。
1. 选中 `Access PolicyEngine` 旁边的复选框，然后单击“选择”。
1. 单击“Done”（完成） 。

### <a name="add-the-application-ids-to-your-custom-policy"></a>将应用程序 ID 添加到自定义策略

若要创建启用本地帐户的自定义策略，需要将应用程序 ID 添加到扩展文件 (`TrustFrameworkExtensions.xml`)。

1. 在扩展文件 (`TrustFrameworkExtensions.xml`) 中，找到元素 `<TechnicalProfile Id="login-NonInteractive">`。
1. 将 `{Policy Engine Proxy Application ID}` 的两个实例替换为[创建的策略引擎代理应用程序](#create-the-policy-engine-proxy-application)的应用程序 ID。
1. 将 `{Policy Engine Application ID}` 的两个实例替换为[创建的策略引擎应用程序](#create-the-policy-engine-application)的应用程序 ID。
1. 保存扩展文件。

## <a name="upload-the-policies-to-your-tenant"></a>将策略上传到租户

1. 在 [Azure 门户](https://portal.azure.com)中，[切换到 Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md)，然后打开 Azure AD B2C 边栏选项卡。
1. 单击“所有策略”。
1. 选择“上传策略”

    >[!WARNING]
    >必须按以下顺序上传自定义策略文件：

1. 上传 `TrustFrameworkBase.xml`。
1. 上传 `TrustFrameworkExtensions.xml`。
1. 上传 `SignUpOrSignin.xml`。

上传某个文件时，将在该文件的名称前面加上 `B2C_1A_`。  而内置策略以 `B2C_1_` 开头。

## <a name="test-the-custom-policy-using-run-now"></a>使用“立即运行”测试自定义策略

1. 打开 **Azure AD B2C 边栏选项卡**并导航到“所有策略”。
1. 选择上传的自定义策略，然后单击“立即运行”按钮。
1. 现在，你应能使用电子邮件地址注册。

## <a name="next-steps"></a>后续步骤

本入门指南中使用的基本文件已包含添加其他标识提供者所需的一些内容。 若要使用 Azure AD 帐户设置登录名，请继续阅读[此文](active-directory-b2c-setup-aad-custom.md)。

## <a name="reference"></a>引用

* **技术配置文件 (TP)** 是一个元素，定义终结点的名称及其元数据和协议，以及标识体验引擎应执行的声明交换的详细信息。  “本地帐户登录”是标识体验引擎用来执行本地帐户登录的技术配置文件。

