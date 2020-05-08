---
title: 注册 SaaS 应用程序-Azure Marketplace
description: 了解如何使用 Azure 门户注册 SaaS 应用程序并接收 Azure Active Directory 的安全令牌。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: dsindona
ms.openlocfilehash: b3c20d25917d66cba8ae3d811eddaa6455b87722
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/05/2020
ms.locfileid: "82792949"
---
# <a name="register-a-saas-application"></a>注册 SaaS 应用程序

本文介绍如何使用 Microsoft [Azure 门户](https://portal.azure.com/)注册 SaaS 应用程序。  成功注册后，你会收到一个 Azure Active Directory （Azure AD）安全令牌，可用于访问 SaaS 履单 Api。  有关 Azure AD 的详细信息，请参阅[什么是身份验证？](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios)

## <a name="service-to-service-authentication-flow"></a>服务到服务身份验证流

下图显示了新客户的订阅流，以及这些 API 的使用时机：

![SaaS 套餐 API 流](./media/saas-offer-publish-api-flow-v1.png)

Azure 不会对 SaaS 服务公开给其最终用户的身份验证施加任何约束。 但是，通过使用 Azure AD 安全令牌（通常通过 Azure 门户注册 SaaS 应用）来执行使用 SaaS 履单 Api 进行的身份验证。 

## <a name="register-an-azure-ad-secured-app"></a>注册 Azure AD 保护的应用

任何想要使用 Azure AD 功能的应用程序都必须先在 Azure AD 租户中注册。 此注册过程涉及到提供有关应用程序的 Azure AD 详细信息，例如，该应用程序所在位置的 URL、对用户进行身份验证后用于发送答复的 URL、用于标识应用程序的 URI，等等。  若要使用 Azure 门户注册新应用程序，请执行以下步骤：

1.  登录 [Azure 门户](https://portal.azure.com/)。
2.  如果你的帐户允许访问多个帐户，请单击右上角的帐户，并将门户会话设置为所需的 Azure AD 租户。
3.  在左侧导航窗格中，依次单击“Azure Active Directory”服务、“应用注册”、“新建应用程序注册”。************

    ![SaaS AD 应用注册](./media/saas-offer-app-registration-v1.png)

4.  在“创建”页上，输入应用程序的注册信息：
    -   **名称**：输入有意义的应用程序名称
    -   **应用程序类型**： 
        - 对于在设备上本地安装的[客户端应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)，选择 "**本机**"。 此设置用于 OAuth 公共[本机客户端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#native-client)。
        - 为安装在安全服务器上的[客户端应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#client-application)和[资源/API 应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#resource-server)选择“Web 应用/API”****。 此设置用于 OAuth 机密[web 客户端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#web-client)和基于公共[用户代理的客户端](https://docs.microsoft.com/azure/active-directory/develop/active-directory-dev-glossary#user-agent-based-client)。
        相同的应用程序还可以公开客户端和资源/API。
    -   **登录 URL**：对于“Web 应用/API”应用程序，请提供应用的基 URL。 例如， **http://localhost:31544**可能是在本地计算机上运行的 web 应用的 URL。 然后，用户将使用此 URL 登录到 Web 客户端应用程序。
    -   **重定向 uri**：对于本机应用程序，提供 Azure AD 用来返回令牌响应的 uri。 输入特定于应用程序的值，例如**http://MyFirstAADApp**。

        ![SaaS AD 应用注册](./media/saas-offer-app-registration-v1-2.png)

        有关 web 应用程序或本机应用程序的特定示例，请查看[Azure AD 开发人员指南](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide)的 "*入门*" 部分中提供的快速入门指导设置。

5.  完成后，单击“创建”。  Azure AD 将唯一的*应用程序 ID*分配给应用程序，\'并将其转到\'应用程序的主注册页。 根据应用程序是 Web 应用程序还是本机应用程序，会提供不同的选项用于将更多功能添加到应用程序。

>[!Note]
>默认情况下，新注册的应用程序配置为只允许同一租户中的用户登录到应用程序。

## <a name="using-the-azure-ad-security-token"></a>使用 Azure AD 安全令牌

注册应用程序后，可以以编程方式请求 Azure AD 安全令牌。  发行者应使用此令牌，并发出请求以解决此问题。  当使用各种履单 Api 时，当用户从 Azure 重定向到 SaaS 网站时，令牌查询参数将在 URL 中。  此令牌仅在一小时内有效。  此外，在使用之前，应从浏览器对令牌值进行 URL 解码。

有关这些令牌的详细信息，请参阅[Azure Active Directory 访问令牌](https://docs.microsoft.com/azure/active-directory/develop/access-tokens)。


### <a name="get-a-token-based-on-the-azure-ad-app"></a>基于 Azure AD 应用获取令牌

HTTP 方法

`POST`

*请求 URL*

**https://login.microsoftonline.com/*{tenantId}*/oauth2/token**

*URI 参数*

|  **参数名称**  | **必需**  | **说明**                               |
|  ------------------  | ------------- | --------------------------------------------- |
| tenantId             | True          | 已注册的 AAD 应用程序的租户 ID   |
|  |  |  |


*请求标头*

|  **标头名称**  | **必需** |  **说明**                                   |
|  --------------   | ------------ |  ------------------------------------------------- |
|  Content-Type     | True         | 与请求关联的内容类型。 默认值为 `application/x-www-form-urlencoded`。  |
|  |  |  |


*请求正文*

| **属性名称**   | **必需** |  **说明**                                                          |
| -----------------   | -----------  | ------------------------------------------------------------------------- |
|  Grant_type         | True         | 授权类型。 默认值为 `client_credentials`。                    |
|  Client_id          | True         |  与 Azure AD 应用关联的客户端/应用标识符。                  |
|  client_secret      | True         |  与 Azure AD 应用关联的密码。                               |
|  资源           | True         |  为其请求令牌的目标资源。 默认值为 `62d94f6c-d599-489b-a797-3e10e42fbe22`。 |
|  |  |  |


*响应*

|  **名称**  | **类型**       |  **说明**    |
| ---------- | -------------  | ------------------- |
| 200 正常    | TokenResponse  | 请求成功   |
|  |  |  |

*TokenResponse*

示例响应令牌：

``` json
  {
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "62d94f6c-d599-489b-a797-3e10e42fbe22",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }               
```

## <a name="next-steps"></a>后续步骤

你的 Azure AD 保护的应用现在可以使用[SaaS 履单 API 版本 2](./pc-saas-fulfillment-api-v2.md)。
