---
title: 在 Azure Active Directory B2C 的自定义策略中定义 RESTful 技术配置文件 | Microsoft Docs
description: 在 Azure Active Directory B2C 的自定义策略中定义 RESTful 技术配置文件。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9eb60f9581099813d96cecb9cb88155e64b7caa8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154314"
---
# <a name="define-a-restful-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>在 Azure Active Directory B2C 自定义策略中定义 RESTful 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C 为你自己的 RESTful 服务提供支持。 Azure AD B2C 在输入声明集合中将数据发送到 RESTful 服务，在输出声明集合中接收返回的数据。 使用 RESTful 服务集成，可以：

- **验证用户输入数据** - 防止将格式不当的数据保存到 Azure AD B2C。 如果用户提供的值无效，RESTful 服务会返回错误消息，指示用户提供有效条目。 例如，可以验证用户提供的电子邮件地址是否在客户数据库中存在。
- **覆盖输入声明** - 可以重新设置输入声明中的值的格式。 例如，如果用户使用全小写或全大写字母输入了名字，则你可以设置该名字的格式，只将第一个字母大写。
- **丰富用户数据** - 可以进一步与企业业务线应用程序集成。 例如，RESTful 服务可以接收用户的电子邮件地址、查询客户的数据库，并向 Azure AD B2C 返回用户的会员号。 返回声明可存储、可在后续的业务流程步骤中进行评估，或包含在访问令牌中。
- **运行自定义业务逻辑** - 可以发送推送通知、更新企业数据库、运行用户迁移过程、管理权限、审核数据库，以及执行其他操作。

策略可将输入声明发送到 REST API。 REST API 还可以返回稍后可在策略中使用的输出声明，或者引发错误消息。 可通过以下方式来设计与 RESTful 服务的集成：

- **验证技术配置文件** - 验证技术配置文件调用 RESTful 服务。 在用户旅程继续执行之前，验证技术配置文件将验证用户提供的数据。 使用验证技术配置文件时，错误消息将显示在自我断言的页面上，并在输出声明中返回。
- **声明交换** - 通过业务流程步骤调用 RESTful 服务。 在此方案中，不会在任何用户界面中呈现错误消息。 如果 REST API 返回错误，则将用户重定向回到信赖方应用程序并显示错误消息。

## <a name="protocol"></a>协议

“Protocol”元素的“Name”属性必须设置为 `Proprietary`。 **handler** 属性必须包含 Azure AD B2C 使用的协议处理程序程序集的完全限定名称：`Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null`。

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

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| ServiceUrl | 是 | REST API 终结点的 URL。 | 
| AuthenticationType | 是 | RESTful 声明提供程序所执行的身份验证类型。 可能的值：`None`、`Basic` 或 `ClientCertificate`。 `None` 值表示 REST API 不是匿名的。 `Basic` 值表示使用 HTTP 基本身份验证保护 REST API。 只有经验证的用户（包括 Azure AD B2C）可以访问你的 API。 `ClientCertificate`（建议）值表示 REST API 使用客户端证书身份验证来限制访问。 只有包含相应证书的服务（例如 Azure AD B2C）能够访问你的服务。 | 
| SendClaimsIn | 否 | 指定如何将输入声明发送到 RESTful 声明提供程序。 可能的值：`Body`（默认值）、`Form`、`Header` 或 `QueryString`。 `Body` 值是在请求正文中以 JSON 格式发送的输入声明。 `Form` 值是在请求正文中以“&”分隔键值格式发送的输入声明。 `Header` 值是在请求标头中发送的输入声明。 `QueryString` 值是在请求查询字符串中发送的输入声明。 | 
| ClaimsFormat | 否 | 指定输出声明的格式。 可能的值：`Body`（默认值）、`Form`、`Header` 或 `QueryString`。 `Body` 值是在请求正文中以 JSON 格式发送的输出声明。 `Form` 值是在请求正文中以“&”分隔键值格式发送的输出声明。 `Header` 值是在请求标头中发送的输出声明。 `QueryString` 值是在请求查询字符串中发送的输出声明。 | 
| DebugMode | 否 | 在调试模式下运行技术配置文件。 在调试模式下，REST API 可以返回更多信息。 请参阅“返回错误消息”部分。 | 

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

| 属性 | 必选 | 说明 |
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

| 属性 | 必选 | 说明 |
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

## <a name="returning-error-message"></a>返回错误消息

REST API 可能需要返回错误消息，例如“在 CRM 系统中未找到该用户”。 发生错误时，REST API 应返回包含以下属性的 HTTP 409 错误消息（冲突响应状态代码）：

| 属性 | 必选 | 说明 |
| --------- | -------- | ----------- |
| 版本 | 是 | 1.0.0 | 
| status | 是 | 409 | 
| 代码 | 否 | 来自 RESTful 终结点提供程序的错误代码，启用 `DebugMode` 后会显示。 | 
| requestId | 否 | 来自 RESTful 终结点提供程序的请求标识符，启用 `DebugMode` 后会显示。 | 
| userMessage | 是 | 向用户显示的错误消息。 | 
| developerMessage | 否 | 问题的详细说明及其解决方法，启用 `DebugMode` 后会显示。 | 
| moreInfo | 否 | 指向其他信息的 URI，启用 `DebugMode` 后会显示。 | 

以下示例演示了一个以 JSON 格式返回错误消息的 REST API：

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

以下示例演示了一个返回错误消息的 C# 类：

```C#
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

## <a name="examples"></a>示例：
- [在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](active-directory-b2c-custom-rest-api-netfw.md) 
- [使用 HTTP 基本身份验证保护 RESTful 服务](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
- [使用客户端证书保护 RESTful 服务](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
- [演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](active-directory-b2c-rest-api-validation-custom.md)

 














