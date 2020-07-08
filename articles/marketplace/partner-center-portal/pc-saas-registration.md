---
title: 注册 SaaS 应用程序-Azure Marketplace
description: 了解如何使用 Azure 门户注册 SaaS 应用程序并接收 Azure Active Directory 的安全令牌。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: dsindona
ms.openlocfilehash: 630dceedcac36cf6d37d54612d73fabe676d74f6
ms.sourcegitcommit: 845a55e6c391c79d2c1585ac1625ea7dc953ea89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2020
ms.locfileid: "85963710"
---
# <a name="register-a-saas-application"></a>注册 SaaS 应用程序

本文介绍如何使用 Microsoft [Azure 门户](https://portal.azure.com/)注册 SaaS 应用程序以及如何获取发布者的访问令牌（Azure Active Directory 访问令牌）。 发布者将使用此令牌通过调用 SaaS 履单 Api 对 SaaS 应用程序进行身份验证。  履行 Api 使用 OAuth 2.0 客户端凭据授予 Azure Active Directory （1.0）终结点上的流，以发出服务到服务访问令牌请求。

Azure Marketplace 不会对你的 SaaS 服务用于最终用户的身份验证方法施加任何限制。 以下流程仅用于对 Azure Marketplace 中的 SaaS 服务进行身份验证。

有关 Azure AD （Active Directory）的详细信息，请参阅[什么是身份验证](../../active-directory/develop/authentication-scenarios.md)？

## <a name="register-an-azure-ad-secured-app"></a>注册 Azure AD 保护的应用

任何想要使用 Azure AD 功能的应用程序都必须先在 Azure AD 租户中注册。 此注册过程涉及到提供有关应用程序的 Azure AD 一些详细信息。 若要使用 Azure 门户注册新应用程序，请执行以下步骤：

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 如果你的帐户允许访问多个帐户，请单击右上角的帐户，并将门户会话设置为所需的 Azure AD 租户。
3. 在左侧导航窗格中，依次单击“Azure Active Directory”服务、“应用注册”、“新建应用程序注册”。************

    ![SaaS AD 应用注册](./media/saas-offer-app-registration-v1.png)

4. 在“创建”页上，输入应用程序的注册信息：
    -   **名称**：输入有意义的应用程序名称
    -   **应用程序类型**：  
        
        为[客户端应用程序](../../active-directory/develop/active-directory-dev-glossary.md#client-application)选择**WEB 应用/api** ）和[资源/api 应用程序](../../active-directory/develop/active-directory-dev-glossary.md#resource-server)（安装在安全服务器上）。 此设置用于 OAuth 机密[web 客户端](../../active-directory/develop/active-directory-dev-glossary.md#web-client)和[基于公共用户代理的客户端](../../active-directory/develop/active-directory-dev-glossary.md#user-agent-based-client)）。
        相同的应用程序还可以公开客户端和资源/API。

        有关 web 应用程序的特定示例，请查看[Azure AD 开发人员指南](../../active-directory/develop/index.yml)的 "[入门](../../active-directory/develop/quickstart-create-new-tenant.md)" 部分中提供的快速入门指导设置。

5. 完成后，单击 "**注册**"。  Azure AD 向新应用程序分配唯一的*应用程序 ID* 。 建议只将一个应用程序注册为一个租户，并将其注册为单个租户。

6. 若要创建客户端密码，请导航到 "**证书 & 机密" 页**，然后单击 " **+ 新建客户端密钥**"。  请确保复制机密值，以便在代码中使用它。

**Azure AD 应用 id**关联到你的发布者 id，因此请确保你的所有产品/服务中都使用相同的*应用 id* 。

>[!Note]
>如果发布者在合作伙伴中心具有两个不同的帐户，则应使用两个不同的 Azure AD 应用 Id。  伙伴中心中的每个合作伙伴帐户应为通过此帐户发布的所有 SaaS 产品/服务使用唯一的 Azure AD 应用 ID。

## <a name="how-to-get-the-publishers-authorization-token"></a>如何获取发布者的授权令牌

注册应用程序后，可以使用 Azure AD V1 终结点以编程方式请求发布服务器的授权令牌（Azure AD 访问令牌）。 发布者在调用各种 SaaS 履单 Api 时必须使用此令牌。 此令牌仅在一小时内有效。 

有关这些令牌的详细信息，请参阅 [Azure Active Directory 访问令牌](../../active-directory/develop/access-tokens.md)。  请注意，在使用低于 V1 终结点令牌的流中。

### <a name="get-the-token-with-an-http-post"></a>使用 HTTP POST 获取令牌

#### <a name="http-method"></a>HTTP 方法

邮递<br>

##### <a name="request-url"></a>*请求 URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI 参数*

|  参数名称    |  必需         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  True      |  已注册 AAD 应用程序的租户 ID。 |

##### <a name="request-header"></a>请求标头

|  标头名称       |  必需         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  True      |  与请求关联的内容类型。 默认值是 `application/x-www-form-urlencoded`。 |

##### <a name="request-body"></a>*请求正文*

|  属性名称     |  必需         |  描述 |
|  ---------------   |  ---------------  | ------------ |
|  `grant-type`      |  True      |  授权类型。 改用 `"client_credentials"` |
|  `client_id`       |  True      |  与 Azure AD 应用关联的客户端/应用标识符。 |
|  `client_secret`   |  True      |  与 Azure AD 应用相关联的机密。 |
|  `resource`        |  True      |  为其请求令牌的目标资源。 使用， `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` 因为在这种情况下 Marketplace SAAS API 始终是目标资源。 |

##### <a name="response"></a>*响应*

|  名称     |  类型         |  说明 |
|  ------   |  ---------------  | ------------ |
|  200 正常   |  TokenResponse    |  请求成功。 |

##### <a name="tokenresponse"></a>*TokenResponse*

示例响应：

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

`"access_token"`响应中的字段值是在 `<access_token>` 调用所有 SaaS 履单和 Marketplace 计量 api 时将作为授权参数传递的。

## <a name="next-steps"></a>后续步骤

你的 Azure AD 保护的应用现在可以使用[SaaS 履单 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
