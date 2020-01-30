---
title: REST API 声明交换-Azure Active Directory B2C
description: 将 REST API 声明交换添加到 Active Directory B2C 中的自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 1caf0b3f6396cc008e77bb96e686fe78f462850b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76849092"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>将 REST API 声明交换添加到 Azure Active Directory B2C 中的自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

可以在 Azure Active Directory B2C （Azure AD B2C）中将与 RESTful API 的交互添加到[自定义策略](custom-policy-overview.md)中。 本文介绍如何创建与 RESTful services 交互的 Azure AD B2C 用户旅程。

交互包括声明 REST API 声明和 Azure AD B2C 之间的信息交换。 声明交换具有以下特征：

- 可以设计为业务流程步骤。
- 可以触发外部操作。 例如，它可能会在外部数据库中记录事件。
- 可用于提取值并将其存储在用户数据库中。
- 可以更改执行流。

本文中所述的方案包括以下操作：

1. 在外部系统中查找用户。
2. 获取用户完成注册所在的城市。
3. 将属性作为声明返回给应用程序。

## <a name="prerequisites"></a>必备组件

- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。
- 要交互的 REST API 终结点。 本文使用简单的 Azure 函数作为示例。 若要创建 Azure 函数，请参阅[在 Azure 门户中创建第一个函数](../azure-functions/functions-create-first-azure-function.md)。

## <a name="prepare-the-api"></a>准备 API

在本部分中，将准备 Azure 函数以接收 `email`的值，然后返回可由 Azure AD B2C 用作声明的 `city` 的值。

更改创建的 Azure 函数的 run.csx 文件，以使用以下代码：

```csharp
#r "Newtonsoft.Json"

using System.Net;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Primitives;
using Newtonsoft.Json;

public static async Task<IActionResult> Run(HttpRequest req, ILogger log)
{
  log.LogInformation("C# HTTP trigger function processed a request.");
  string email = req.Query["email"];
  string requestBody = await new StreamReader(req.Body).ReadToEndAsync();
  dynamic data = JsonConvert.DeserializeObject(requestBody);
  email = email ?? data?.email;

  return email != null
    ? (ActionResult)new OkObjectResult(
      new ResponseContent
      {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        city = "Redmond"
      })
      : new BadRequestObjectResult("Please pass an email on the query string or in the request body");
}

public class ResponseContent
{
    public string version { get; set; }
    public int status { get; set; }
    public string city {get; set; }
}
```

## <a name="configure-the-claims-exchange"></a>配置声明交换

技术配置文件为声明交换提供配置。

打开*trustframeworkextensions.xml*文件，并在**ClaimsProviders**元素中添加以下**ClaimsProvider** xml 元素。

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="email" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="city" PartnerClaimType="city" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

**InputClaims**元素定义发送到 REST 服务的声明。 在此示例中，声明 `givenName` 的值将作为声明 `email`发送到 REST 服务。 **OutputClaims**元素定义了需要来自 REST 服务的声明。

上述注释 `AuthenticationType` 和 `AllowInsecureAuthInProduction` 指定在迁移到生产环境时应进行的更改。 若要了解如何保护用于生产的 RESTful Api，请参阅通过[证书身份验证](secure-rest-api-dotnet-certificate-auth.md)实现基本身份验证和安全 RESTful Api[安全 RESTful api](secure-rest-api-dotnet-basic-auth.md) 。

## <a name="add-the-claim-definition"></a>添加声明定义

在**BuildingBlocks**元素中添加 `city` 的定义。 你可以在 TrustFrameworkExtensions.xml 文件的开头处找到此元素。

```XML
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="city">
      <DisplayName>City</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your city</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

## <a name="add-an-orchestration-step"></a>添加业务流程步骤

在许多用例中，可将 REST API 调用用作业务流程步骤。 用户成功完成某个任务（例如首次注册）后，可以使用业务流程步骤对外部系统进行更新；或者使用它来对配置文件进行更新，以保持信息同步。 在本例中，业务流程步骤用于在完成配置文件编辑后，对提供给应用程序的信息做出补充。

向配置文件编辑用户旅程中添加步骤。 对用户进行身份验证后（以下 XML 中的业务流程步骤1-4），并且用户提供了更新的配置文件信息（步骤5）。 将*trustframeworkbase.xml*文件中的配置文件编辑用户旅程 XML 代码复制到**UserJourneys**元素中的*trustframeworkextensions.xml*文件。 然后作为步骤6进行修改。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

用户旅程的最终 XML 应如下例所示：

```XML
<UserJourney Id="ProfileEdit">
  <OrchestrationSteps>
    <OrchestrationStep Order="1" Type="ClaimsProviderSelection" ContentDefinitionReferenceId="api.idpselections">
      <ClaimsProviderSelections>
        <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange" />
        <ClaimsProviderSelection TargetClaimsExchangeId="LocalAccountSigninEmailExchange" />
      </ClaimsProviderSelections>
    </OrchestrationStep>
    <OrchestrationStep Order="2" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="FacebookExchange" TechnicalProfileReferenceId="Facebook-OAUTH" />
        <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="3" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>localAccountAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserRead" TechnicalProfileReferenceId="AAD-UserReadUsingAlternativeSecurityId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="4" Type="ClaimsExchange">
      <Preconditions>
        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">
          <Value>authenticationSource</Value>
          <Value>socialIdpAuthentication</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <ClaimsExchanges>
        <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="5" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="B2CUserProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <!-- Add a step 6 to the user journey before the JWT token is created-->
    <OrchestrationStep Order="6" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-WebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>添加声明

编辑*profileedit.xml*文件，并将 `<OutputClaim ClaimTypeReferenceId="city" />` 添加到**OutputClaims**元素中。

添加新声明后，技术配置文件如下例所示：

```XML
<TechnicalProfile Id="PolicyProfile">
  <DisplayName>PolicyProfile</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
    <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    <OutputClaim ClaimTypeReferenceId="city" />
  </OutputClaims>
  <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>上传更改并进行测试

1. 可有可无在继续操作之前，保存文件的现有版本（下载）。
2. 上传*trustframeworkextensions.xml*和*profileedit.xml* ，然后选择覆盖现有文件。
3. 选择**B2C_1A_ProfileEdit**。
4. 对于自定义策略的 "概述" 页上的 "**选择应用程序**"，请选择前面注册的名为*webapp1*的 web 应用程序。 请确保 `https://jwt.ms`"**答复 URL** "。
4. 选择 "**立即运行**"。 用你的帐户凭据登录，然后单击 "**继续**"。

如果所有内容都已正确设置，则令牌将包含新声明 `city`，其值为 `Redmond`。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_profileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>后续步骤

还可以将交互设计为验证配置文件。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](custom-policy-rest-api-claims-validation.md)。

[修改配置文件编辑以收集用户的其他信息](custom-policy-custom-attributes.md)

[参考： RESTful 技术配置文件](restful-technical-profile.md)

若要了解如何保护 Api，请参阅以下文章：

* [使用基本身份验证（用户名和密码）保护 RESTful API](secure-rest-api-dotnet-basic-auth.md)
* [使用客户端证书保护 RESTful API](secure-rest-api-dotnet-certificate-auth.md)
