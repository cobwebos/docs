---
title: 在 B2C 自定义策略中 REST API 声明交换
titleSuffix: Azure AD B2C
description: 创建与 RESTful 服务交互的 Azure AD B2C 用户旅程简介。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6a6cc8e5931f3e29c242f51a6e062441953228ad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80337410"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-custom-policy"></a>在 Azure AD B2C 自定义策略中集成 REST API 声明交换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

作为 Azure 活动目录 B2C（Azure AD B2C）的基础的标识体验框架，可以在用户旅程中与 RESTful API 集成。 本文演示如何使用[RESTful 技术配置文件](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster)创建与 RESTful 服务交互的用户旅程。

使用 Azure AD B2C，您可以通过调用自己的 RESTful 服务将您自己的业务逻辑添加到用户旅程中。 身份体验框架可以从 RESTful 服务发送和接收数据以交换声明。 例如，你能够：

- **验证用户输入数据**。 例如，您可以验证用户提供的电子邮件地址是否存在于客户的数据库中，如果没有，则显示错误。
- **处理声明**。 如果用户以所有小写字母或所有大写字母输入其名字，则 REST API 只需将第一个字母大写格式，即可将其返回到 Azure AD B2C。
- **通过进一步与企业业务线应用程序集成来丰富用户数据**。 RESTful 服务可以接收用户的电子邮件地址、查询客户的数据库，并向 Azure AD B2C 返回用户的会员号。 然后，返回声明可以存储在用户的 Azure AD 帐户中，在下一个业务流程步骤中计算，或包含在访问令牌中。
- **运行自定义业务逻辑**。 您可以发送推送通知、更新公司数据库、运行用户迁移过程、管理权限、审核数据库和执行任何其他工作流。

![RESTful 服务声明交换图](media/custom-policy-rest-api-intro/restful-service-claims-exchange.png)

## <a name="calling-a-restful-service"></a>调用 RESTful 服务

交互包括 REST API 声明和 Azure AD B2C 之间的声明信息交换。 可通过以下方式来设计与 RESTful 服务的集成：

- **验证技术配置文件**。 对 RESTful 服务的调用发生在指定[自断言技术配置文件](self-asserted-technical-profile.md)的[验证技术配置文件](validation-technical-profile.md)中，或[显示控件](display-controls.md)的[验证显示控制](display-control-verification.md)。 在用户旅程推进之前，验证技术配置文件会验证用户提供的数据。 使用验证技术配置文件，可以：

  - 将声明发送到 REST API。
  - 验证声明，并将显示给用户的自定义错误消息。
  - 将声明从 REST API 发送回后续业务流程步骤。

- **索赔交换**。 可以直接声明交换可以通过直接从[用户旅程](userjourneys.md)的业务流程步骤调用 REST API 技术配置文件来配置。 此定义仅限于：

  - 将声明发送到 REST API。
  - 验证声明，并引发返回到应用程序的自定义错误消息。
  - 将声明从 REST API 发送回后续业务流程步骤。

可在自定义策略定义的用户旅程中的任意步骤中添加 REST API 调用。 例如，可在以下时机调用 REST API：

- 在登录期间，就在 Azure AD B2C 验证凭据之前。
- 登录后立即登录。
- 在 Azure AD B2C 在目录中创建新帐户之前。
- Azure AD B2C 在目录中创建新帐户后。
- 在 Azure AD B2C 发出访问令牌之前。

![验证技术配置文件集合](media/custom-policy-rest-api-intro/validation-technical-profile.png)

## <a name="sending-data"></a>发送数据

在[RESTful 技术配置文件](restful-technical-profile.md)中`InputClaims`，该元素包含要发送到 RESTful 服务的声明列表。 您可以将声明的名称映射到 RESTful 服务中定义的名称、设置默认值以及使用[声明解析器](claim-resolver-overview.md)。

您可以使用 SendClaimsIn 属性配置输入声明如何发送到 RESTful 声明提供程序。 可能的值包括：

- **正文**，以 JSON 格式在 HTTP POST 请求正文中发送。
- **窗体**，以安培和"&"分隔键值格式在 HTTP POST 请求正文中发送。
- **标头**，在 HTTP GET 请求标头中发送。
- **查询字符串**，在 HTTP GET 请求查询字符串中发送。

配置 **"正文"** 选项后，REST API 技术配置文件允许您向终结点发送复杂的 JSON 负载。 有关详细信息，请参阅发送[JSON 有效负载](restful-technical-profile.md#send-a-json-payload)。

## <a name="receiving-data"></a>接收数据

REST `OutputClaims` [技术配置文件](restful-technical-profile.md)的元素包含 REST API 返回的声明列表。 可能需要将策略中定义的声明名称映射到 REST API 中定义的名称。 还可以包括 REST API 标识提供程序未返回的声明，只要您设置了 DefaultValue 属性。

RESTful 声明提供程序分析的输出声明始终希望解析平面 JSON Body 响应，例如：

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

要解析嵌套的 JSON Body 响应，将 ResolveJsonPathsInJonTokens 元数据设置为 true。 在输出声明中，将合作伙伴声明类型设置为要输出的 JSON 路径元素。

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
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="contacts.0.person.name" />
  <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="contacts.0.person.emails.0.email" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="contacts.0.person.loyaltyNumber" />
</OutputClaims>
```

## <a name="security-considerations"></a>安全注意事项

您必须保护 REST API 终结点，以便只有经过身份验证的客户端才能与其通信。 REST API 必须使用 HTTPS 终结点。 将身份验证类型元数据设置为以下身份验证方法之一：

- **客户端证书**使用客户端证书身份验证限制访问。 只有具有适当证书的服务才能访问 API。 将客户端证书存储在 Azure AD B2C 策略密钥中。 详细了解如何使用[客户端证书来保护 RESTful 服务](secure-rest-api.md#https-client-certificate-authentication)。
- **基本**使用 HTTP 基本身份验证来保护 REST API。 只有经验证的用户（包括 Azure AD B2C）可以访问你的 API。 用户名和密码存储在 Azure AD B2C 策略密钥中。 了解如何使用[HTTP 基本身份验证来保护 RESTful 服务](secure-rest-api.md#http-basic-authentication)。
- **承载器**使用客户端 OAuth2 访问令牌限制访问。 访问令牌存储在 Azure AD B2C 策略密钥中。 详细了解如何使用[承载令牌来保护 RESTful 服务](secure-rest-api.md#oauth2-bearer-authentication)。

## <a name="rest-api-platform"></a>REST API 平台
REST API 可以基于任何平台，并编写任何编程语言，只要它是安全的，并且可以发送和接收[RESTful 技术配置文件](restful-technical-profile.md)中指定的声明。

## <a name="localize-the-rest-api"></a>本地化 REST API
在 RESTful 技术配置文件中，您可能希望发送当前会话的语言/区域设置，如有必要，将引发本地化的错误消息。 使用[声明解析器](claim-resolver-overview.md)，可以发送上下文声明，如用户语言。 下面的示例显示了演示此方案的 RESTful 技术配置文件。

```XML
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

REST API 可能需要返回一条错误消息，例如"在 CRM 系统中找不到用户"。 如果发生错误，REST API 应返回 HTTP 409 错误消息（冲突响应状态代码）。 有关详细信息，请参阅[RESTful 技术配置文件](https://identitydivision.visualstudio.com/defaultcollection/Identity%20CXP/_git/GTP?path=%2Fyoelh%2Fdocs%2Frest-api%2Frestful-technical-profile.md&version=GBmaster&anchor=returning-error-message)。

这只能通过从验证技术配置文件调用 REST API 技术配置文件来实现。 这允许用户更正页面上的数据，并在页面提交时再次运行验证。

需要 HTTP 409 响应才能防止处理此业务流程步骤中的任何后续验证技术配置文件。

如果直接从用户旅程中引用 REST API 技术配置文件，则会将用户重定向回依赖方应用程序，并显示相关的错误消息。

## <a name="publishing-your-rest-api"></a>发布 REST API

对 REST API 服务的请求来自 Azure AD B2C 服务器。 REST API 服务必须发布到可公开访问的 HTTPS 终结点。 REST API 调用将从 Azure 数据中心 IP 地址到达。

将 REST API 服务及其基础组件（如数据库和文件系统）设计为高度可用。

## <a name="next-steps"></a>后续步骤

有关使用 RESTful 技术配置文件的示例，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换，作为用户输入的验证](custom-policy-rest-api-claims-validation.md)
- [演练：将 REST API 声明交换添加到 Azure 活动目录 B2C 中的自定义策略](custom-policy-rest-api-claims-validation.md)
- [保护您的 REST API 服务](secure-rest-api.md)
- [参考： RESTful 技术配置文件](restful-technical-profile.md)