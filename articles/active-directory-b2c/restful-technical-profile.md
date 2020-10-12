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
ms.date: 06/08/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 172824a2215e8a102ad4c284c847072960344549
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88041521"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 RESTful 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) 为集成你自己的 RESTful 服务提供支持。 Azure AD B2C 在输入声明集合中将数据发送到 RESTful 服务，在输出声明集合中接收返回的数据。 有关详细信息，请参阅[在 Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)。  

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **handler** 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

以下示例演示了一个 RESTful 技术配置文件：

```xml
<TechnicalProfile Id="REST-UserMembershipValidator">
  <DisplayName>Validate user input data and return loyaltyNumber claim</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  ...
```

## <a name="input-claims"></a>输入声明

**InputClaims** 元素包含要发送到 REST API 的声明列表。 还可将声明名称映射到 REST API 中定义的名称。 以下示例演示策略与 REST API 之间的映射。 **givenName** 声明作为 **firstName** 发送到 REST API，而 **surname** 作为 **lastName** 发送。 **email** 声明的设置保留原样。

```xml
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

```xml
<TechnicalProfile Id="SendGrid">
  <DisplayName>Use SendGrid's email API to send the code the the user</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="ServiceUrl">https://api.sendgrid.com/v3/mail/send</Item>
    <Item Key="AuthenticationType">Bearer</Item>
    <Item Key="SendClaimsIn">Body</Item>
    <Item Key="ClaimUsedForRequestPayload">sendGridReqBody</Item>
    <Item Key="DefaultUserMessageIfRequestFailed">Cannot process your request right now, please try again later.</Item>
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

## <a name="metadata"></a>Metadata

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ServiceUrl | 是 | REST API 终结点的 URL。 |
| AuthenticationType | 是 | RESTful 声明提供程序所执行的身份验证类型。 可能的值：`None`、`Basic`、`Bearer` 或 `ClientCertificate`。 `None`值指示 REST API 是匿名的。 `Basic` 值表示使用 HTTP 基本身份验证保护 REST API。 只有经验证的用户（包括 Azure AD B2C）可以访问你的 API。 `ClientCertificate`（建议）值表示 REST API 使用客户端证书身份验证来限制访问。 只有包含相应证书的服务（例如 Azure AD B2C）才能访问你的 API。 `Bearer` 值表示 REST API 使用客户端 OAuth2 持有者令牌来限制访问。 |
| AllowInsecureAuthInProduction| 否| 指示是否可以在生产环境中将 `AuthenticationType` 设置为 `none`（将 [TrustFrameworkPolicy](trustframeworkpolicy.md) 的 `DeploymentMode` 设为 `Production` 或未指定）。 可能的值：true 或 false（默认值）。 |
| SendClaimsIn | 否 | 指定如何将输入声明发送到 RESTful 声明提供程序。 可能的值：`Body`（默认值）、`Form`、`Header`、`Url` 或 `QueryString`。 `Body` 值是在请求正文中以 JSON 格式发送的输入声明。 `Form` 值是在请求正文中以“&”分隔键值格式发送的输入声明。 `Header` 值是在请求标头中发送的输入声明。 `Url` 值是在 URL 中发送的输入声明，例如 https://{claim1}.example.com/{claim2}/{claim3}?{claim4}={claim5}。 `QueryString` 值是在请求查询字符串中发送的输入声明。 每个输入声明调用的 HTTP 谓词如下所示：<br /><ul><li>`Body`：POST</li><li>`Form`：POST</li><li>`Header`：GET</li><li>`Url`：GET</li><li>`QueryString`：GET</li></ul> |
| ClaimsFormat | 否 | 当前未使用，可以忽略。 |
| ClaimUsedForRequestPayload| 否 | 包含要发送到 REST API 的有效负载的字符串声明名称。 |
| DebugMode | 否 | 在调试模式下运行技术配置文件。 可能的值：`true` 或 `false`（默认值）。 在调试模式下，REST API 可以返回更多信息。 请参阅[返回错误消息](#returning-validation-error-message)部分。 |
| IncludeClaimResolvingInClaimsHandling  | 否 | 对于输入和输出声明，指定[声明解析](claim-resolver-overview.md)是否包含在技术配置文件中。 可能的值：`true` 或 `false` （默认值）。 若要使用技术配置文件中的声明解析程序，请将此项设为 `true`。 |
| ResolveJsonPathsInJsonTokens  | 否 | 指示技术配置文件是否解析 JSON 路径。 可能的值：`true` 或 `false`（默认值）。 使用此元数据从嵌套 JSON 元素中读取数据。 在 [OutputClaim](technicalprofiles.md#outputclaims) 中，将 `PartnerClaimType` 设为要输出的 JSON 路径元素。 例如：`firstName.localized` 或 `data.0.to.0.email`。|
| UseClaimAsBearerToken| 否| 包含持有者令牌的声明的名称。|

## <a name="error-handling"></a>错误处理。

以下元数据可用于配置 REST API 失败时显示的错误消息。 可以将错误消息[本地化](localization-string-ids.md#restful-service-error-messages)。

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| DefaultUserMessageIfRequestFailed | 否 | 所有 REST API 异常的默认自定义错误消息。|
| UserMessageIfCircuitOpen | 否 | 无法访问 REST API 时的错误消息。 如果未指定，则将返回 DefaultUserMessageIfRequestFailed。 |
| UserMessageIfDnsResolutionFailed | 否 | DNS 解析异常的错误消息。 如果未指定，则将返回 DefaultUserMessageIfRequestFailed。 | 
| UserMessageIfRequestTimeout | 否 | 连接超时时的错误消息。如果未指定，则将返回 DefaultUserMessageIfRequestFailed。 | 

## <a name="cryptographic-keys"></a>加密密钥

如果身份验证类型设置为 `None`，则不使用 **CryptographicKeys** 元素。

```xml
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

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| BasicAuthenticationUsername | 是 | 用于身份验证的用户名。 |
| BasicAuthenticationPassword | 是 | 用于身份验证的密码。 |

以下示例演示了使用基本身份验证的技术配置文件：

```xml
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

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| ClientCertificate | 是 | 用于身份验证的 X509 证书（RSA 密钥集）。 |

```xml
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

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| BearerAuthenticationToken | 否 | OAuth 2.0 持有者令牌。 |

```xml
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

## <a name="returning-validation-error-message"></a>返回验证错误消息

REST API 可能需要返回错误消息，例如“在 CRM 系统中未找到该用户”。 如果发生错误，REST API 应返回 HTTP 4xx 错误消息，例如“400 (错误请求)”或“409 (冲突)”响应状态代码。 响应正文包含 JSON 格式的错误消息：

```json
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

| 属性 | 必须 | 说明 |
| --------- | -------- | ----------- |
| 版本 | 是 | REST API 版本。 例如：1.0.1 |
| 状态 | 是 | 必须为 409 |
| code | 否 | 来自 RESTful 终结点提供程序的错误代码，启用 `DebugMode` 后会显示。 |
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
- [演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](custom-policy-rest-api-claims-validation.md)
- [演练：在 Azure Active Directory B2C 中将 REST API 声明交换添加到自定义策略](custom-policy-rest-api-claims-validation.md)
- [保护 REST API 服务](secure-rest-api.md)

