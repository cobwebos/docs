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
ms.openlocfilehash: 4c42959d46aa522042275456a87e590f9e009348
ms.sourcegitcommit: 7a6d8e841a12052f1ddfe483d1c9b313f21ae9e6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70183067"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>使用 Azure AD B2C 保护 Azure API 管理 API

了解如何限制对使用 Azure Active Directory B2C （Azure AD B2C）进行身份验证的客户端的 Azure API 管理（APIM） API 的访问。 按照本文中的步骤创建和测试 APIM 中的入站策略，以将访问权限限制为仅包含有效 Azure AD B2C 颁发的访问令牌的请求。

## <a name="prerequisites"></a>先决条件

在继续执行本文中的步骤之前，需要准备好以下资源：

* [Azure AD B2C 租户](tutorial-create-tenant.md)
* 在租户中[注册的应用程序](tutorial-register-applications.md)
* 在租户中[创建的用户流](tutorial-create-user-flows.md)
* Azure API 管理中[已发布的 API](../api-management/import-and-publish.md)
* 用于测试安全访问的[Postman](https://www.getpostman.com/) （可选）

## <a name="get-azure-ad-b2c-application-id"></a>获取 Azure AD B2C 应用程序 ID

使用 Azure AD B2C 在 Azure API 管理中保护 API 时，需要为在 APIM 中创建的[入站策略](../api-management/api-management-howto-policies.md)提供多个值。 首先，记录你之前在 Azure AD B2C 租户中创建的应用程序的应用程序 ID。 如果使用的是在先决条件中创建的应用程序，请使用*webbapp1*的应用程序 ID。

1. 浏览到[Azure 门户](https://portal.azure.com)中的 Azure AD B2C 租户。
1. 在 "**管理**" 下，选择 "**应用程序**"。
1. 在*webapp1*的**应用程序 ID**中记录值，或在之前创建的另一应用程序中记录值。

  ![B2C 应用程序的应用程序 ID 在 Azure 门户中的位置](media/secure-apim-with-b2c-token/portal-02-app-id.png)

## <a name="get-token-issuer-endpoint"></a>获取令牌颁发者终结点

接下来，获取某个 Azure AD B2C 用户流的已知配置 URL。 还需要在 Azure API 管理中支持令牌颁发者终结点 URI。

1. 浏览到[Azure 门户](https://portal.azure.com)中的 Azure AD B2C 租户。
1. 在 "**策略**" 下，选择 "**用户流（策略）** "。
1. 选择现有策略，例如*B2C_1_signupsignin1*，然后选择 "**运行用户流**"。
1. 在页面顶部附近的 "**运行用户流**" 标题下显示的超链接中记录 URL。 此 URL 是用户流的 OpenID Connect 众所周知的发现终结点，当你在 Azure API 管理中配置入站策略时，将在下一部分中使用此 URL。

    ![Azure 门户的 "立即运行" 页中的已知 URI 超链接](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. 选择超链接，浏览到 OpenID Connect 众所周知的配置页。
1. 在浏览器中打开的页面上，记录`issuer`值，例如：

    `https://your-b2c-tenant.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    在 Azure API 管理中配置 API 时，将在下一部分中使用此值。

你现在应记录了两个 Url 以供下一部分使用： OpenID Connect 众所周知的配置终结点 URL 和颁发者 URI。 例如：

```
https://yourb2ctenant.b2clogin.com/yourb2ctenant.onmicrosoft.com/v2.0/.well-known/openid-configuration?p=B2C_1_signupsignin1
https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>在 Azure API 管理中配置入站策略

你现在已准备好在 Azure API 管理中添加用于验证 API 调用的入站策略。 通过添加用于验证访问令牌中的受众和颁发者的[JWT 验证](../api-management/api-management-access-restriction-policies.md#ValidateJWT)策略，可以确保只接受带有有效令牌的 API 调用。

1. 在 [Azure 门户](https://portal.azure.com)中浏览到自己的 Azure API 管理实例。
1. 选择“API”。
1. 选择要用 Azure AD B2C 保护的 API。
1. 选择“设计”选项卡。
1. 在 "**入站处理**" 下，选择 **\< / \>** 打开 "策略代码编辑器"。
1. 将以下`<validate-jwt>`标记放在`<inbound>`策略中。

    1. 用策略的已知配置`<openid-config>` URL 更新元素中的值。`url`
    1. 用你之前在 B2C 租户中创建的应用程序的应用程序 ID 更新元素（例如，webapp1`<audience>` ）。
    1. 用先前记录的令牌颁发者终结点更新元素。`<issuer>`

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

若要确保只有经过身份验证的调用方可以访问你的 API，你可以通过使用[Postman](https://www.getpostman.com/)调用 API 来验证 Azure API 管理配置。

若要调用 API，需要 Azure AD B2C 颁发的访问令牌和 APIM 订阅密钥。

### <a name="get-an-access-token"></a>获取访问令牌

首先需要一个由 Azure AD B2C 颁发的令牌，以便在 Postman `Authorization`的标头中使用。 你可以通过使用注册/登录用户流的 "**立即运行**" 功能来获取一个，你应该将其创建为先决条件之一。

1. 浏览到[Azure 门户](https://portal.azure.com)中的 Azure AD B2C 租户。
1. 在 "**策略**" 下，选择 "**用户流（策略）** "。
1. 选择现有的注册/登录用户流，例如*B2C_1_signupsignin1*。
1. 对于**应用程序**，请选择 " *webapp1*"。
1. 对于 "**答复 URL**" `https://jwt.ms`，请选择。
1. 选择“运行用户流”。

    ![运行用户流页面，用于在 Azure 门户中注册登录用户流](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. 完成登录过程。 应将重定向到`https://jwt.ms`。
1. 记录浏览器中显示的编码标记值。 将此令牌值用于 Postman 中的 Authorization 标头。

    ![Jwt.ms 上显示的编码标记值](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>获取 API 订阅密钥

调用已发布的 API 的客户端应用程序（在本例中为 Postman）必须在其 HTTP 请求中包含有效的 API 管理订阅密钥。 获取要包含在 Postman HTTP 请求中的订阅密钥：

1. 浏览到[Azure 门户](https://portal.azure.com)中的 Azure API 管理服务实例。
1. 选择 **订阅**。
1. 选择 " **产品" 的省略号："** 无限制"，然后选择 "**显示/隐藏密钥**"。
1. 记录产品**的主密钥**。 在 Postman 的 HTTP 请求中`Ocp-Apim-Subscription-Key` ，将此密钥用于标头。

![在 Azure 门户中选择 "显示/隐藏密钥" 的 "订阅密钥" 页](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>测试安全的 API 调用

在记录了 "访问令牌" 和 "APIM" 订阅密钥后，你就可以测试是否已正确配置对 API 的安全访问。

1. 在 [Postman](https://www.getpostman.com/) 中创建新`GET`请求。 对于 "请求 URL"，请将所发布 API 的 "发言人列表" 终结点指定为先决条件之一。 例如：

    `https://contosoapim.azure-api.net/conference/speakers`

1. 接下来，添加以下标头：

    | Key | ReplTest1 |
    | --- | ----- |
    | `Authorization` | 之前记下的已编码标记值， `Bearer `带有前缀（在 "持有者" 后包括空格） |
    | `Ocp-Apim-Subscription-Key` | 之前记录的 APIM 订阅密钥 |

    **GET**请求 URL 和**标头**应类似于：

    ![显示 GET 请求 URL 和标头的 Postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. 选择 Postman 中的 "**发送**" 按钮以执行请求。 如果已正确配置所有内容，则应该会看到一个 JSON 响应，其中包含一组会议扬声器（此处显示为已截断）：

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

现在，你已成功完成请求，请测试故障情况，以确保使用*无效*令牌对 API 进行的调用被视为预期的情况。 执行测试的一种方法是在令牌值中添加或更改几个字符，然后执行与之前相同`GET`的请求。

1. 向标记值添加几个字符以模拟无效标记。 例如，将 "无效" 添加到令牌值：

    ![显示添加到标记无效的 Postman UI 的标头部分](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. 选择 "**发送**" 按钮执行请求。 如果令牌无效，预期的结果是`401`未授权的状态代码：

    ```JSON
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

如果你看到`401`状态代码，你已经验证了只有具有 Azure AD B2C 颁发的有效访问令牌的调用方可以向你的 Azure api 管理 API 发出成功的请求。

## <a name="support-multiple-applications-and-issuers"></a>支持多个应用程序和颁发者

几个应用程序通常与单个 REST API 交互。 若要允许多个应用程序调用 API，请将其应用程序`<audiences>` id 添加到 APIM 入站策略中的元素。

```XML
<!-- Accept requests from multiple applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同样，若要支持多个令牌颁发者，请将其`<audiences>`终结点 uri 添加到 APIM 入站策略中的元素。

```XML
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://yourb2ctenant.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>迁移到 b2clogin.com

如果你有一个验证旧版`login.microsoftonline.com`终结点颁发的令牌的 APIM api，你应该迁移该 api 和调用它的应用程序以使用[b2clogin.com](b2clogin.md)颁发的令牌。

你可以遵循此常规过程来执行分阶段迁移：

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

有关 Azure API 管理策略的更多详细信息，请参阅[APIM 策略参考索引](../api-management/api-management-policies.md)。

有关将基于 OWIN 的 web Api 及其应用程序迁移到 b2clogin.com 中的信息，请查看将[基于 OWIN 的 WEB Api 迁移到 b2clogin.com](multiple-token-endpoints.md)。
