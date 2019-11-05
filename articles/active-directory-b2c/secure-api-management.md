---
title: 使用 Azure Active Directory B2C 保护 Azure API 管理 API
description: 了解如何使用 Azure Active Directory B2C 颁发的访问令牌来保护 Azure API 管理 API 终结点。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 531f6d86d57be550d0a1147e131d93ae6e298406
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73474769"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保护 Azure API 管理 API

了解如何将 Azure API 管理 (APIM) API 的访问权限限制给已使用 Azure Active Directory B2C (Azure AD B2C) 进行身份验证的客户端。 请遵循本文中的步骤在 APIM 中创建并测试一个入站策略，用于将访问权限限制给包含 Azure AD B2C 所颁发的有效访问令牌的请求。

## <a name="prerequisites"></a>必备组件

在继续执行本文中的步骤之前，需要准备好以下资源：

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的应用程序](tutorial-register-applications.md)
* 在租户中[创建的用户流](tutorial-create-user-flows.md)
* Azure API 管理中[发布的 API](../api-management/import-and-publish.md)
* 用于测试安全访问的 [Postman](https://www.getpostman.com/)（可选）

## <a name="get-azure-ad-b2c-application-id"></a>获取 Azure AD B2C 应用程序 ID

使用 Azure AD B2C 保护 Azure API 管理中的 API 时，需要为 APIM 中创建的[入站策略](../api-management/api-management-howto-policies.md)提供多个值。 首先，请记下以前在 Azure AD B2C 租户中创建的应用程序的 ID。 如果使用的是在先决条件中创建的应用程序，请使用 *webbapp1* 的应用程序 ID。

你可以使用当前**应用程序**体验或我们的新统一**应用注册（预览版）** 体验获取应用程序 ID。 [了解有关预览版体验的详细信息](http://aka.ms/b2cappregintro)。

#### <a name="applicationstabapplications"></a>[应用程序](#tab/applications/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 在“管理”下选择“应用程序”。
1. 记录*webapp1*的 "**应用程序 ID** " 列中的值或之前创建的其他应用程序。

#### <a name="app-registrations-previewtabapp-reg-preview"></a>[应用注册（预览）](#tab/app-reg-preview/)

1. 登录到 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含 Azure AD B2C 租户的目录。
1. 在左侧菜单中，选择 " **Azure AD B2C**"。 或者选择 "**所有服务**"，搜索并选择 " **Azure AD B2C**"。
1. 选择**应用注册（预览）** ，然后选择 "**拥有的应用程序**" 选项卡。
1. 在*webapp1*的**应用程序（客户端） ID**列中记录值，或者在前面创建的其他应用程序中记录值。

* * *

## <a name="get-token-issuer-endpoint"></a>获取令牌颁发者终结点

接下来，获取某个 Azure AD B2C 用户流的已知配置 URL。 还需要获取要在 Azure API 管理中支持的令牌颁发者终结点 URI。

1. 在 [Azure 门户](https://portal.azure.com)中浏览到你的 Azure AD B2C 租户。
1. 在“策略”下，选择“用户流(策略)”。
1. 选择现有的策略（例如 *B2C_1_signupsignin1*），然后选择“运行用户流”。
1. 记下页面顶部附近的“运行用户流”标题下显示的超链接中的 URL。 此 URL 是用户流的 OpenID Connect 已知发现终结点，在下一部分，当你在 Azure API 管理中配置入站策略时，将要使用此 URL。

    ![Azure 门户的“立即运行”页中的“已知 URI”超链接](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 选择该超链接以浏览到“OpenID Connect 已知配置”页。
1. 在浏览器中打开的页上，记下 `issuer` 值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    在下一部分，当你在 Azure API 管理中配置 API 时，将要使用此值。

现在，你应已记下了要在下一部分使用的两个 URL：OpenID Connect 已知配置终结点 URL，以及颁发者 URI。 例如：

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中配置入站策略

现已准备好在 Azure API 管理中添加用于验证 API 调用的入站策略。 通过添加用于验证访问令牌中的受众和颁发者的 [JWT 验证](../api-management/api-management-access-restriction-policies.md#ValidateJWT)策略，可以确保仅接受具有有效令牌的 API 调用。

1. 在 [Azure 门户](https://portal.azure.com)中浏览到自己的 Azure API 管理实例。
1. 选择“API”。
1. 选择要使用 Azure AD B2C 保护的 API。
1. 选择“设计”选项卡。
1. 在“入站处理”下，选择 **\</ 打开策略代码编辑器。\>**
1. 将以下 `<validate-jwt>` 标记放入 `<inbound>` 策略。

    1. 使用策略的已知配置 URL 更新 `url` 元素中的 `<openid-config>` 值。
    1. 使用以前在 B2C 租户中创建的应用程序（例如 `<audience>`webapp1 *）的 ID 更新*  元素。
    1. 使用前面记下的令牌颁发者终结点更新 `<issuer>` 元素。

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>验证安全 API 访问

若要确保只有经过身份验证的调用方可以访问你的 API，可以使用 [Postman](https://www.getpostman.com/) 调用该 API，以此验证 Azure API 管理配置。

若要调用 API，需要使用 Azure AD B2C 颁发的访问令牌以及 APIM 订阅密钥。

### <a name="get-an-access-token"></a>获取访问令牌

首先需要获取 Azure AD B2C 颁发的令牌，该令牌将在 Postman 的 `Authorization` 标头中使用。 可以使用注册/登录用户流（作为先决条件之一，应该事先创建该用户流）的“立即运行”功能获取该令牌。

1. 在 [Azure 门户](https://portal.azure.com)中浏览到你的 Azure AD B2C 租户。
1. 在“策略”下，选择“用户流(策略)”。
1. 选择现有的注册/登录用户流，例如 *B2C_1_signupsignin1*。
1. 对于“应用程序”，请选择“webapp1”。
1. 对于“回复 URL”，请选择 **。** `https://jwt.ms`
1. 选择“运行用户流”。

    ![Azure 门户中注册/登录用户流的“运行用户流”页](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登录过程。 你应会重定向到 `https://jwt.ms`。
1. 记下浏览器中显示的已编码令牌值。 此令牌值将用于 Postman 中的 Authorization 标头。

    ![jwt.ms 中显示的已编码令牌值](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>获取 API 订阅密钥

调用已发布 API 的客户端应用程序（在本例中为 Postman）必须在其对该 API 发出的 HTTP 请求中包含有效的 API 管理订阅密钥。 若要获取 Postman HTTP 请求中包含的订阅密钥：

1. 在 [Azure 门户](https://portal.azure.com)中浏览到你的 Azure API 管理服务实例。
1. 选择 **订阅**。
1. 选择 "**产品：无限制**" 的省略号，然后选择 "**显示/隐藏密钥**"。
1. 记下产品的“主密钥”。 此密钥将用于 Postman 的 HTTP 请求中的 `Ocp-Apim-Subscription-Key` 标头。

![Azure 门户中的“订阅密钥”页，其中已选择“显示/隐藏密钥”](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>测试安全 API 调用

记下访问令牌和 APIM 订阅密钥后，可以开始测试是否正确配置了对 API 的安全访问。

1. 在 `GET`Postman[ 中创建新的 ](https://www.getpostman.com/) 请求。 对于请求 URL，请指定作为先决条件之一发布的 API 的发言人列表终结点。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下来添加以下标头：

    | 键 | 值 |
    | --- | ----- |
    | `Authorization` | 前面记下的已编码令牌值，带有 `Bearer ` 前缀（请在“Bearer”后面包含空格） |
    | `Ocp-Apim-Subscription-Key` | 前面记下的 APIM 订阅密钥 |

    **GET** 请求 URL 和**标头**应类似于：

    ![显示 GET 请求 URL 和标头的 Postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 在 Postman 中选择“发送”按钮以执行该请求。 如果配置全部正确，你应会看到一个 JSON 响应，以及一系列会议发言人（此处的内容已截断）：

    ```JSON
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>测试不安全的 API 调用

成功发出请求后，接下来请测试失败案例，以确保按预期拒绝使用无效令牌对 API 发出调用。 一种测试方法是在令牌值中添加或更改几个字符，然后执行与前面所述相同的 `GET` 请求。

1. 在令牌值中添加几个字符以模拟无效令牌。 例如，将“INVALID”添加到令牌值：

    ![Postman UI 的“标头”部分，其中显示已将 INVALID 添加到令牌](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 选择“发送”按钮以执行该请求。 使用无效令牌时，预期的结果是 `401` 未授权状态代码：

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果看到 `401` 状态代码，则可以确认，只有具有 Azure AD B2C 所颁发的有效访问令牌的调用方才能向你的 Azure API 管理 API 成功发出请求。

## <a name="support-multiple-applications-and-issuers"></a>支持多个应用程序和颁发者

往往会有多个应用程序与单个 REST API 交互。 要使 API 接受用于多个应用程序的令牌，请将其应用程序 ID 添加到 APIM 入站策略中的 `<audiences>` 元素。

```XML
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同样，若要支持多个令牌颁发者，请将其终结点 URI 添加到 APIM 入站策略中的 `<issuers>` 元素。

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>迁移到 b2clogin.com

如果你有一个验证旧 `login.microsoftonline.com` 终结点颁发的令牌的 APIM API，你应该迁移该 API 和调用它的应用程序以使用[b2clogin.com](b2clogin.md)颁发的令牌。

可以遵循以下一般过程来执行分阶段的迁移：

1. 为 b2clogin.com 和 login.microsoftonline.com 颁发的令牌添加 APIM 入站策略支持。
1. 一次更新一个应用程序，以从 b2clogin.com 终结点获取令牌。
1. 所有应用程序都从 b2clogin.com 正确获取令牌后，请从 API 中删除对 login.microsoftonline.com 颁发的令牌的支持。

下面的 APIM 入站策略示例说明了如何接受 b2clogin.com 和 login.microsoftonline.com 颁发的令牌。 此外，它还支持来自两个应用程序的 API 请求。

```XML
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>后续步骤

有关 Azure API 管理策略的更多详细信息，请参阅 [APIM 策略参考索引](../api-management/api-management-policies.md)。

有关将基于 OWIN 的 web Api 及其应用程序迁移到 b2clogin.com 中的信息，请查看将[基于 OWIN 的 WEB Api 迁移到 b2clogin.com](multiple-token-endpoints.md)。
