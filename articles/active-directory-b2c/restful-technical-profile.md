---
title: 在自定义策略中定义 RESTful 技术配置文件
titleSuffix: Azure AD B2C
description: 在 Azure Active Directory B2C 的自定义策略中定义 RESTful 技术配置文件。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 410f413fc8450c0ee33c3ca95e860a3e8de34107
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80332611"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 RESTful 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活动目录 B2C（Azure AD B2C）支持集成您自己的 RESTful 服务。 Azure AD B2C 在输入声明集合中将数据发送到 RESTful 服务，在输出声明集合中接收返回的数据。 有关详细信息，请参阅在[Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)。  

## <a name="protocol"></a>协议

"**Name****协议"** 元素的名称属性需要设置为`Proprietary`。 **handler** 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

以下示例演示了一个 RESTful 技术配置文件：

```XML
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>输入声明

**InputClaims** 元素包含要发送到 REST API 的声明列表。 还可将声明名称映射到 REST API 中定义的名称。 以下示例演示策略与 REST API 之间的映射。 **givenName** 声明作为 **firstName** 发送到 REST API，而 **surname** 作为 **lastName** 发送。 **email** 声明的设置保留原样。

```XML
<InputClaims>
  <InputClaim ClaimTypeReferenceId="email" />
  <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
  <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
</InputClaims>
```

**InputClaimsTransformations** 元素可以包含用于修改输入声明，或者先生成新输入声明，再将其发送到 REST API 的 **InputClaimsTransformation** 元素集合。

## <a name="send-a-json-payload"></a>发送 JSON 有效负载

使用 REST API 技术配置文件可以将复杂的 JSON 有效负载发送到终结点。

发送复杂的 JSON 有效负载：

1. 生成具备 [GenerateJson](json-transformations.md) 声明转换的 JSON 有效负载。
1. 在 REST API 技术配置文件中执行以下操作：
    1. 添加具有对 `GenerateJson` 声明转换的引用的输入声明转换。
    1. 将 `SendClaimsIn` 元数据选项设置为 `body`
    1. 将 `ClaimUsedForRequestPayload` 元数据选项设置为包含 JSON 有效负载的声明名称。
    1. 在输入声明中，添加对包含 JSON 有效负载的输入声明的引用。

以下示例 `TechnicalProfile` 使用第三方电子邮件服务（在本例中为 SendGrid）发送验证电子邮件。

```XML
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_SendGridApiKey" />
  </CryptographicKeys>
  <InputClaimsTransformations>
    <InputClaimsTransformation ReferenceId="GenerateSendGridRequestBody" />
  </InputClaimsTransformations>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="sendGridReqBody" />
  </InputClaims>
</TechnicalProfile>
```

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素包含 REST API 返回的声明列表。 可能需要将策略中定义的声明名称映射到 REST API 中定义的名称。 如果设置了 `DefaultValue` 属性，则还可以包含 REST API 标识提供者不会返回的声明。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

以下示例演示 REST API 返回的声明：

- 映射到 **loyaltyNumber** 声明名称的 **MembershipId** 声明。

技术配置文件还会返回标识提供者不返回的声明：

- 默认值设置为 `true` 的 **loyaltyNumberIsNew** 声明。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="MembershipId" />
  <OutputClaim ClaimTypeReferenceId="loyaltyNumberIsNew" DefaultValue="true" />
</OutputClaims>
```

## <a name="metadata"></a>元数据

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| ServiceUrl | 是 | REST API 终结点的 URL。 |
| AuthenticationType | 是 | RESTful 声明提供程序所执行的身份验证类型。 可能的值：`None`、`Basic`、`Bearer` 或 `ClientCertificate`。 `None` 值表示 REST API 不是匿名的。 `Basic` 值表示使用 HTTP 基本身份验证保护 REST API。 只有经验证的用户（包括 Azure AD B2C）可以访问你的 API。 `ClientCertificate`（建议）值表示 REST API 使用客户端证书身份验证来限制访问。 只有包含相应证书的服务（例如 Azure AD B2C）才能访问你的 API。 `Bearer` 值表示 REST API 使用客户端 OAuth2 持有者令牌来限制访问。 |
| 允许不安全生产| 否| `AuthenticationType`指示是否可以`none`在生产环境中设置为`DeploymentMode`（[信任框架策略](trustframeworkpolicy.md)设置为`Production`， 或未指定）。 可能的值：true 或 false（默认值）。 |
| SendClaimsIn | 否 | 指定如何将输入声明发送到 RESTful 声明提供程序。 可能的值：`Body`（默认值）、`Form`、`Header` 或 `QueryString`。 `Body` 值是在请求正文中以 JSON 格式发送的输入声明。 `Form` 值是在请求正文中以“&”分隔键值格式发送的输入声明。 `Header` 值是在请求标头中发送的输入声明。 `QueryString` 值是在请求查询字符串中发送的输入声明。 每个输入声明调用的 HTTP 谓词如下所示：<br /><ul><li>`Body`： 邮递</li><li>`Form`： 邮递</li><li>`Header`： 获取</li><li>`QueryString`： 获取</li></ul> |
| ClaimsFormat | 否 | 当前未使用，可以忽略。 |
| ClaimUsedForRequestPayload| 否 | 包含要发送到 REST API 的有效负载的字符串声明名称。 |
| DebugMode | 否 | 在调试模式下运行技术配置文件。 可能的值：`true` 或 `false`（默认值）。 在调试模式下，REST API 可以返回更多信息。 请参阅[返回错误消息](#returning-error-message)部分。 |
| 包括索赔解决索赔处理  | 否 | 对于输入和输出声明，指定[索赔解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true`或`false` （默认值）。 如果要在技术配置文件中使用声明解析器，则将此解决方案设置为`true`。 |
| 解决JsonPathinJonTokens  | 否 | 指示技术配置文件是否解析 JSON 路径。 可能的值：`true` 或 `false`（默认值）。 使用此元数据可以从嵌套的 JSON 元素读取数据。 在[输出声明](technicalprofiles.md#outputclaims)中，`PartnerClaimType`将 设置为要输出的 JSON 路径元素。 例如：`firstName.localized` 或 `data.0.to.0.email`。|
| 使用声明标记| 否| 包含无记名令牌的声明的名称。|

## <a name="cryptographic-keys"></a>加密密钥

如果身份验证类型设置为 `None`，则不使用 **CryptographicKeys** 元素。

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">None</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
</TechnicalProfile>
```

如果身份验证类型设置为 `Basic`，则 **CryptographicKeys** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | 是 | 用于身份验证的用户名。 |
| BasicAuthenticationPassword | 是 | 用于身份验证的密码。 |

以下示例演示了使用基本身份验证的技术配置文件：

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Basic</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
    <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
  </CryptographicKeys>
</TechnicalProfile>
```

如果身份验证类型设置为 `ClientCertificate`，则 **CryptographicKeys** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| ClientCertificate | 是 | 用于身份验证的 X509 证书（RSA 密钥集）。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">ClientCertificate</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
  </CryptographicKeys>
</TechnicalProfile>
```

如果身份验证类型设置为 `Bearer`，则 **CryptographicKeys** 元素包含以下属性：

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | 否 | OAuth 2.0 持有者令牌。 |

```XML
<TechnicalProfile Id="REST-API-SignUp">
  <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="BearerAuthenticationToken" StorageReferenceId="B2C_1A_B2cRestClientAccessToken" />
  </CryptographicKeys>
</TechnicalProfile>
```

## <a name="returning-error-message"></a>返回错误消息

REST API 可能需要返回错误消息，例如“在 CRM 系统中未找到该用户”。 如果发生错误，REST API 应返回 HTTP 4xx 错误消息，例如 400（错误请求）或 409（冲突）响应状态代码。 响应正文包含在 JSON 中格式化的错误消息：

```JSON
{
  "version": "1.0.0",
  "status": 409,
  "code": "API12345",
  "requestId": "50f0bd91-2ff4-4b8f-828f-00f170519ddb",
  "userMessage": "Message for the user",
  "developerMessage": "Verbose description of problem and how to fix it.",
  "moreInfo": "https://restapi/error/API12345/moreinfo"
}
```

| 特性 | 必选 | 描述 |
| --------- | -------- | ----------- |
| 版本 | 是 | REST API 版本。 例如： 1.0.1 |
| status | 是 | 必须为 409 |
| 代码 | 否 | 来自 RESTful 终结点提供程序的错误代码，启用 `DebugMode` 后会显示。 |
| requestId | 否 | 来自 RESTful 终结点提供程序的请求标识符，启用 `DebugMode` 后会显示。 |
| userMessage | 是 | 向用户显示的错误消息。 |
| developerMessage | 否 | 问题的详细说明及其解决方法，启用 `DebugMode` 后会显示。 |
| moreInfo | 否 | 指向其他信息的 URI，启用 `DebugMode` 后会显示。 |


以下示例演示了一个返回错误消息的 C# 类：

```csharp
public class ResponseContent
{
  public string version { get; set; }
  public int status { get; set; }
  public string code { get; set; }
  public string userMessage { get; set; }
  public string developerMessage { get; set; }
  public string requestId { get; set; }
  public string moreInfo { get; set; }
}
```

## <a name="next-steps"></a>后续步骤

有关使用 RESTful 技术配置文件的示例，请参阅以下文章：

- [在 Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)
- [演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换，作为用户输入的验证](custom-policy-rest-api-claims-validation.md)
- [演练：将 REST API 声明交换添加到 Azure 活动目录 B2C 中的自定义策略](custom-policy-rest-api-claims-validation.md)
- [保护您的 REST API 服务](secure-rest-api.md)

