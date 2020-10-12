---
title: B2C 自定义策略中的 REST API 声明交换
titleSuffix: Azure AD B2C
description: 介绍如何创建与 RESTful 服务交互的 Azure AD B2C 用户旅程。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 05/18/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc2b72779460c2b7e3999204ace50ca57388b9a2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89594180"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中集成 REST API 声明交换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

构成 Azure Active Directory B2C (Azure AD B2C) 的基础的 Identity Experience Framework 可在用户旅程中与 RESTful API 相集成。 本文介绍如何使用 [RESTful 技术配置文件](restful-technical-profile.md)创建与 RESTful 服务交互的用户旅程。

使用 Azure AD B2C 可以通过调用 RESTful 服务，将自己的业务逻辑添加到用户旅程中。 Identity Experience Framework 可以在 RESTful 服务中发送和接收数据，以交换声明。 例如，可以：

- 验证用户输入数据。 例如，可以验证用户提供的电子邮件地址是否在客户数据库中存在，如果不存在，则显示错误消息。
- 处理声明。 如果用户使用全小写或全大写字母输入了其名字，REST API 可以设置该名字的格式，只将第一个字母，然后将此名字返回到 Azure AD B2C。
- 通过进一步与企业业务线应用程序集成来丰富用户数据。 RESTful 服务可以接收用户的电子邮件地址、查询客户的数据库，并向 Azure AD B2C 返回用户的会员号。 返回声明可以存储在用户 Azure AD 帐户中、在后续的业务流程步骤中进行评估，或包含在访问令牌中。
- 运行自定义业务逻辑。 可以发送推送通知、更新企业数据库、运行用户迁移过程、管理权限、审核数据库，以及执行任何其他工作流。

![RESTful 服务声明交换示意图](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

> [!NOTE]
> 如果 RESTful 服务对 Azure AD B2C 的响应速度缓慢或没有响应，则超时为 30 秒，重试计数为 2 次（这意味着总共有 3 次尝试）。 超时和重试计数设置当前不可配置。

## <a name="calling-a-restful-service"></a>调用 RESTful 服务

交互包括 REST API 声明与 Azure AD B2C 之间的声明信息交换。 可通过以下方式来设计与 RESTful 服务的集成：

- 验证技术配置文件。 对 RESTful 服务的调用在指定的[自我断言技术配置文件](self-asserted-technical-profile.md)的[验证技术配置文件](validation-technical-profile.md)中发生，或者在[显示控制](display-controls.md)的[验证显示控制](display-control-verification.md)中发生。 在用户旅程推进之前，验证技术配置文件会验证用户提供的数据。 使用验证技术配置文件，可以：

  - 将声明发送到 REST API。
  - 验证声明，并引发向用户显示的自定义错误消息。
  - 将 REST API 中的声明发回到后续的业务流程步骤。

- 声明交换。 可以通过从[用户旅程](userjourneys.md)的业务流程步骤直接调用 REST API 技术配置文件，来配置直接声明交换。 此定义仅限于：

  - 将声明发送到 REST API。
  - 验证声明，并引发返回给应用程序的自定义错误消息。
  - 将 REST API 中的声明发回到后续的业务流程步骤。

可在自定义策略定义的用户旅程中的任意步骤中添加 REST API 调用。 例如，可在以下时机调用 REST API：

- 登录期间在 Azure AD B2C 验证凭据之前的那一刻。
- 登录后立即调用。
- Azure AD B2C 在目录中创建新帐户之前。
- Azure AD B2C 在目录中创建新帐户之后。
- Azure AD B2C 颁发访问令牌之前。

![验证技术配置文件集合](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>发送数据

在 [RESTful 技术配置文件](restful-technical-profile.md)中，`InputClaims` 元素包含要发送到 RESTful 服务的声明列表。 可将声明的名称映射到 RESTful 服务中定义的名称，设置默认值，然后使用[声明解析程序](claim-resolver-overview.md)。

可以使用 SendClaimsIn 特性来配置如何将输入声明发送到 RESTful 声明提供程序。 可能的值包括：

- Body，以 JSON 格式在 HTTP POST 请求正文中发送。
- Form，以“&”符号分隔的键值格式在 HTTP POST 请求正文中发送。
- Header，在 HTTP GET 请求标头中发送。
- QueryString，在 HTTP GET 请求查询字符串中发送。

配置 Body 选项时，REST API 技术配置文件允许将复杂的 JSON 有效负载发送到终结点。 有关详细信息，请参阅[发送 JSON 有效负载](restful-technical-profile.md#send-a-json-payload)。

## <a name="receiving-data"></a>接收数据

[RESTful 技术配置文件](restful-technical-profile.md)的 `OutputClaims` 元素包含 REST API 返回的声明列表。 可能需要将策略中定义的声明名称映射到 REST API 中定义的名称。 只要设置了 DefaultValue 特性，就还可以包含 REST API 标识提供者不会返回的声明。

RESTful 声明提供程序分析的输出声明始终预期分析平面 JSON 正文响应，例如：

```json
{
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "loyaltyNumber":  1234
}
```

输出声明应如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" />
</OutputClaims>
```

若要分析嵌套的 JSON 正文响应，请将 ResolveJsonPathsInJsonTokens 的元数据设置为 true。 在输出声明中，将 PartnerClaimType 设置为要输出的 JSON 路径元素。

```json
"contacts": [
  {
    "id": "MAINCONTACT_1",
    "person": {
      "name": "Emily Smith",
      "loyaltyNumber":  1234,
      "emails": [
        {
          "id": "EMAIL_1",
          "type": "WORK",
          "email": "email@domain.com"
        }
      ]
    }
  }
],
```


输出声明应如下所示：

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts[0].person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts[0].person.emails[0].email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts[0].person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>安全注意事项

必须保护 REST API 终结点，以便只有经过身份验证的客户端才能与其通信。 REST API 必须使用 HTTPS 终结点。 将 AuthenticationType 元数据设置为以下身份验证方法之一：

- “客户端证书”使用客户端证书身份验证来限制访问。 只有具有适当证书的服务才能访问你的 API。 将客户端证书存储在 Azure AD B2C 策略密钥中。 详细了解如何[使用客户端证书保护 RESTful 服务](secure-rest-api.md#https-client-certificate-authentication)。
- “基本”通过 HTTP 基本身份验证保护 REST API。 只有经验证的用户（包括 Azure AD B2C）可以访问你的 API。 用户名和密码存储在 Azure AD B2C 策略密钥中。 了解如何[使用 HTTP 基本身份验证保护 RESTful 服务](secure-rest-api.md#http-basic-authentication)。
- “持有者”使用客户端 OAuth2 访问令牌限制访问。 访问令牌存储在 Azure AD B2C 策略密钥中。 详细了解如何[使用持有者令牌保护 RESTful 服务](secure-rest-api.md#oauth2-bearer-authentication)。

## <a name="rest-api-platform"></a>REST API 平台
REST API 可以基于任何平台，并可以使用任何编程语言编写，前提是该 API 安全，并可以根据 [RESTful 技术配置文件](restful-technical-profile.md)中的指定发送和接收声明。

## <a name="localize-the-rest-api"></a>本地化 REST API
在 RESTful 技术配置文件中，你可能想要发送当前会话的语言/区域设置，并在必要时引发本地化的错误消息。 使用[声明解析程序](claim-resolver-overview.md)可以发送上下文声明，例如用户语言。 以下示例中的 RESTful 技术配置文件演示了此方案。

```xml
<TechnicalProfile Id="REST-ValidateUserData">
  <DisplayName>Validate user input data</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app.azurewebsites.net/api/identity</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="IncludeClaimResolvingInClaimsHandling">true</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="userLanguage" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="emailAddress" />
  </InputClaims>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
</TechnicalProfile>
```

## <a name="handling-error-messages"></a>处理错误消息

REST API 可能需要返回错误消息，例如“在 CRM 系统中未找到该用户”。 如果发生错误，REST API 应返回 HTTP 409 错误消息（冲突响应状态代码）。 有关详细信息，请参阅 [RESTful 技术配置文件](restful-technical-profile.md#returning-validation-error-message)。

只能通过从验证技术配置文件调用 REST API 技术配置文件来实现此目的。 这样，用户就可以更正页面上的数据，并在提交页面后再次运行验证。

需要使用 HTTP 409 响应来阻止处理此业务流程步骤中的任何后续验证技术配置文件。

如果直接从用户旅程引用 REST API 技术配置文件，用户将通过相关的错误消息重定向回到信赖方应用程序。

## <a name="publishing-your-rest-api"></a>发布 REST API

对 REST API 服务的请求来自 Azure AD B2C 服务器。 必须将 REST API 服务发布到可公开访问的 HTTPS 终结点。 REST API 调用将从 Azure 数据中心 IP 地址抵达。

请对 REST API 服务及其底层组件（例如数据库和文件系统）采用高可用性设计。

## <a name="next-steps"></a>后续步骤

有关使用 RESTful 技术配置文件的示例，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](custom-policy-rest-api-claims-validation.md)
- [演练：在 Azure Active Directory B2C 中将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-validation.md)
- [保护 REST API 服务](secure-rest-api.md)
- [参考：RESTful 技术配置文件](restful-technical-profile.md)
