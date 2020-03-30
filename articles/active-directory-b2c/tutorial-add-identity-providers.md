---
title: 教程：将标识提供程序添加到应用
titleSuffix: Azure AD B2C
description: 了解如何使用 Azure 门户向 Azure Active Directory B2C 应用程序添加标识提供程序。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183510"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>教程：在 Azure 活动目录 B2C 中向应用程序添加标识提供程序

在应用程序中，不妨允许用户使用不同的标识提供程序登录。 标识提供程序** 创建、维护和管理标识信息，同时向应用程序提供身份验证服务。 可以使用 Azure 门户将 Azure 活动目录 B2C （Azure AD B2C） 支持的标识提供程序添加到[用户流](user-flow-overview.md)中。

在本文中，学习如何：

> [!div class="checklist"]
> * 创建标识提供程序应用程序
> * 向租户添加标识提供程序
> * 向用户流添加标识提供程序

通常在应用程序中只使用一个标识提供程序，但可以视需要添加更多标识提供程序。 本教程介绍了如何将 Azure AD 标识提供程序和 Facebook 标识提供程序添加到应用程序。 视需要将这两个标识提供程序添加到应用程序。 您还可以添加其他身份提供商，如[亚马逊](identity-provider-amazon.md)[、GitHub、](identity-provider-github.md)[谷歌](identity-provider-google.md)[、LinkedIn、](identity-provider-linkedin.md)[微软](identity-provider-microsoft-account.md)或[推特](identity-provider-twitter.md)。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="prerequisites"></a>先决条件

[创建用户流](tutorial-create-user-flows.md)，以便用户能够注册并登录应用程序。

## <a name="create-applications"></a>创建应用程序

标识提供程序应用程序提供标识符和密钥，以启用与 Azure AD B2C 租户之间的通信。 在本教程的这一部分中，创建 Azure AD 应用程序和 Facebook 应用程序，从中可获取用于将标识提供程序添加到租户中的标识符和密钥。 若要只添加其中一个标识提供程序，只需为相应提供程序创建应用程序。

### <a name="create-an-azure-active-directory-application"></a>创建 Azure Active Directory 应用程序

若要让用户从 Azure AD 登录，需要在 Azure AD 租户中注册应用程序。 Azure AD 租户与 Azure AD B2C 租户不同。

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD 租户的目录，请确保使用的目录包含 Azure AD 租户。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择“新注册”。****
1. 输入应用程序的名称。 例如，`Azure AD B2C App` 。
1. 对于此应用程序，接受选择“仅此组织目录中的帐户”****。
1. 对于**重定向 URI**，接受**Web**的值，并在所有小写字母中输入以下 URL，`your-B2C-tenant-name`替换为 Azure AD B2C 租户的名称。

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    例如，`https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp` 。

    现在，所有 URL 都应使用 [b2clogin.com](b2clogin.md)。

1. 选择 **"注册**"，然后记录在后面的步骤中使用**的应用程序（客户端）ID。**
1. 在应用程序菜单中的 **"管理"** 下，选择**证书&机密**，然后选择 **"新客户端机密**"。
1. 输入客户端机密**的说明**。 例如，`Azure AD B2C App Secret` 。
1. 选择到期期限。 对于此应用程序，接受选项“1 年内”****。
1. 选择 **"添加**"，然后记录在后面的步骤中使用的新客户端机密的值。

### <a name="create-a-facebook-application"></a>创建 Facebook 应用程序

必须创建 Facebook 应用程序，才能将 Facebook 帐户用作 Azure AD B2C 中的标识提供程序。 如果您还没有 Facebook 帐户，您可以在 上[https://www.facebook.com/](https://www.facebook.com/)获取。

1. 使用 Facebook 帐户凭据登录 [Facebook 开发人员](https://developers.facebook.com/)。
1. 如果以前没有登录过，需要以 Facebook 开发人员身份注册。 为此，请在页面右上角选择 **"开始"，** 接受 Facebook 的政策，并完成注册步骤。
1. 选择 **"我的应用**"，然后**创建应用**。
1. 输入“显示名称”**** 和有效的“联系人电子邮件”****。
1. 单击“创建应用程序 ID”****。 这会要求接受 Facebook 平台策略并完成在线安全检查。
1. 选择 **"设置** > **基本**"。
1. 选择“类别”****，例如 `Business and Pages`。 此值是 Facebook 所必需的，但 Azure AD B2C 不使用。
1. 在页面底部，选择“添加平台”****，然后选择“网站”****。
1. 在“站点 URL”**** 中，输入 `https://your-tenant-name.b2clogin.com/`，将 `your-tenant-name` 替换为你的租户名称。
1. 为“隐私策略 URL”**** 输入一个 URL，例如 `http://www.contoso.com/`。 隐私政策 URL 是您为应用程序提供隐私信息而维护的页面。
1. 选择“保存更改”。****
1. 在页面顶部，记录**应用 ID**的值。
1. 在**应用机密**旁边，选择 **"显示**并记录其值"。 您可以使用应用 ID 和应用密钥将 Facebook 配置为租户中的标识提供程序。 **应用密钥**是一个重要的安全凭据，您应该安全地存储。
1. 选择**产品**旁边的加号，然后在 Facebook**登录下**选择 **"设置**"。
1. 在左侧菜单中的**Facebook 登录**菜单下，选择 **"设置**"。
1. 在“有效的 OAuth 重定向 URL”**** 中输入 `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`。 将 `your-tenant-name` 替换为租户的名称。 选择页面底部的 **"保存更改**"。
1. 要使 Facebook 应用程序可供 Azure AD B2C 使用，请单击页面右上角**的状态**选择器，然后将其**打开以公开**应用程序，然后单击"**确认**"。 此时，状态应从 **"发展"** 更改为 **"生活**"。

## <a name="add-the-identity-providers"></a>添加标识提供程序

为要添加的标识提供程序创建应用程序后，将标识提供程序添加到租户。

### <a name="add-the-azure-active-directory-identity-provider"></a>添加 Azure Active Directory 标识提供程序

1. 请确保使用的是包含 Azure AD B2C 租户的目录。 选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含 Azure AD B2C 租户的目录****。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”********。
1. 选择“标识提供者”****，然后选择“新建 OpenID Connect 提供程序”****。
1. 输入“名称”****。 例如，输入“Contoso Azure AD”**。
1. 对于**元数据 URL，** 输入以下 URL`your-AD-tenant-domain`替换为 Azure AD 租户的域名：

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    例如，`https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration` 。

1. 对于“客户端 ID”，请输入之前记录的应用程序 ID****。
1. 对于“客户端机密”，请输入之前记录的客户端机密****。
1. 保留“范围”****、“响应类型”和“响应模式”******** 的默认值。
1. （可选）输入 **** Domain_hint 的值。 例如，*ContosoAD*。 [域提示](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md)是包含在来自应用程序的身份验证请求中的指令。 它们可用于加速将用户转到其联合的 IdP 登录页。 或者可由多租户应用程序用来帮助用户更快地直接转到其租户的品牌 Azure AD 登录页。
1. 在“标识提供者声明映射”**** 下，输入以下声明映射值：

    * **用户 ID**：*oid*
    * **显示名称**：*name*
    * **名字**：*given_name*
    * **姓氏**：*family_name*
    * **电子邮件**：*unique_name*

1. 选择“保存”。****

### <a name="add-the-facebook-identity-provider"></a>添加 Facebook 标识提供程序

1. 选择**身份提供商**，然后选择**Facebook**。
1. 输入“名称”****。 例如 *，Facebook*。
1. 对于**客户端 ID，** 输入您之前创建的 Facebook 应用程序的应用 ID。
1. 对于**客户端密码**，输入你记录的应用机密。
1. 选择“保存”。****

## <a name="update-the-user-flow"></a>更新用户流

在本教程中，你已完成先决条件的一部分，创建了名为“B2C_1_signupsignin1”** 的注册和登录用户流。 在此部分中，将标识提供程序添加到 B2C_1_signupsignin1** 用户流。

1. 依次选择“用户流(策略)”**** 和“B2C_1_signupsignin1”** 用户流。
2. 依次选择“标识提供程序”**** 和所添加的“Facebook”**** 和“Contoso Azure AD”**** 标识提供程序。
3. 选择“保存”。****

## <a name="test-the-user-flow"></a>测试用户流

1. 在创建的用户流的“概述”页上，选择“运行用户流”****。
1. 对于“应用程序”，请选择前面已注册的名为 *webapp1* 的 Web 应用程序。**** “回复 URL”应显示为 `https://jwt.ms`。****
1. 选择 **"运行用户流**"，然后使用以前添加的标识提供程序登录。
1. 对添加的其他标识提供程序重复执行第 1 步到第 3 步。

如果登录操作成功，您将被重定向到`https://jwt.ms`显示解码令牌的标记，类似于：

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>后续步骤

本文介绍了如何执行以下操作：

> [!div class="checklist"]
> * 创建标识提供程序应用程序
> * 向租户添加标识提供程序
> * 向用户流添加标识提供程序

接下来，了解如何自定义作为用户在应用程序中身份体验的一部分显示给用户的页面的 UI：

> [!div class="nextstepaction"]
> [自定义 Azure 活动目录 B2C 中应用程序的用户界面](tutorial-customize-ui.md)
