---
title: REST API 声明交换的 Azure Active Directory B2C |Microsoft Docs
description: 将 REST API 声明交换添加到 Active Directory B2C 中的自定义策略。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bc0cea765816bfac066b05aca65f668fbce0c8ef
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2019
ms.locfileid: "66508773"
---
# <a name="add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>将 REST API 声明交换添加到 Azure Active Directory B2C 中的自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

您可以添加交互与 RESTful API 对你[自定义策略](active-directory-b2c-overview-custom.md)Azure Active Directory (Azure AD) B2C 中。 本文介绍如何创建与 RESTful 服务交互的 Azure AD B2C 用户旅程。

交互操作包括声明交换的 REST API 声明与 Azure AD B2C 之间的信息。 声明交换具有以下特征：

- 可以设计为业务流程步骤。
- 可以触发外部操作。 例如，它可能会在外部数据库中记录事件。
- 可用于提取值并将其存储在用户数据库中。
- 可以更改执行流。 

在这篇文章中表示的方案包括以下操作：

1. 在外部系统中查找用户。
2. 获取用户完成注册所在的城市。
3. 将属性作为声明返回给应用程序。

## <a name="prerequisites"></a>必备组件

- 完成[自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。
- 要交互的 REST API 终结点。 此文章使用一个简单的 Azure 函数作为示例。 若要创建 Azure 函数，请参阅[Azure 门户中创建第一个函数](../azure-functions/functions-create-first-azure-function.md)。

## <a name="prepare-the-api"></a>准备 API

在本部分中，准备要接收的值的 Azure 函数`email`，然后返回的值为`city`，可以通过 Azure AD B2C 使用，以声明方式。

更改为使用下面的代码而创建的 Azure 函数的 run.csx 文件： 

```
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

## <a name="configure-the-claims-exchange"></a>将声明交换配置

技术配置文件提供的声明交换的配置。 

打开*TrustFrameworkExtensions.xml*文件，并添加以下 XML 元素内的**ClaimsProvider**元素。

```XML
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="AzureFunctions-WebHook">
      <DisplayName>Azure Function Web Hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://myfunction.azurewebsites.net/api/HttpTrigger1?code=bAZ4lLy//ZHZxmncM8rI7AgjQsrMKmVXBpP0vd9smOzdXDDUIaLljA==</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
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

**InputClaims**元素定义发送到 REST 服务的声明。 在此示例中，声明的值`givenName`发送到 REST 服务声明为`email`。 **OutputClaims**元素定义预期从 REST 服务的声明。

## <a name="add-the-claim-definition"></a>将声明定义添加

为添加定义`city`内**BuildingBlocks**元素。 你可以在 TrustFrameworkExtensions.xml 文件的开头处找到此元素。

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

## <a name="add-an-orchestration-step"></a>添加一个业务流程的步骤

在许多用例中，可将 REST API 调用用作业务流程步骤。 用户成功完成某个任务（例如首次注册）后，可以使用业务流程步骤对外部系统进行更新；或者使用它来对配置文件进行更新，以保持信息同步。 在本例中，业务流程步骤用于在完成配置文件编辑后，对提供给应用程序的信息做出补充。

向配置文件编辑用户旅程中添加步骤。 用户是后身份验证 （业务流程步骤 1 至 4，在下面的 XML），并且用户已提供更新的配置文件信息 （步骤 5）。 复制该配置文件编辑用户旅程 XML 代码从*TrustFrameworkBase.xml*文件发送到你*TrustFrameworkExtensions.xml*文件**UserJourneys**元素。 请为步骤 6 的修改。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
  </ClaimsExchanges>
</OrchestrationStep>
```

最终用户旅程的 XML 应如以下示例所示：

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
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
      </ClaimsExchanges>
    </OrchestrationStep>
    <OrchestrationStep Order="7" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
  </OrchestrationSteps>
  <ClientDefinition ReferenceId="DefaultWeb" />
</UserJourney>
```

## <a name="add-the-claim"></a>添加声明

编辑*ProfileEdit.xml*文件，并添加`<OutputClaim ClaimTypeReferenceId="city" />`到**OutputClaims**元素。

添加新声明后，技术配置文件看起来如下例所示：

```XML
<DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="city" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
</TechnicalProfile>
```

## <a name="upload-your-changes-and-test"></a>上传更改并进行测试

1. （可选：）在继续操作之前，将现有版本 （通过下载） 保存的文件。
2. 上传*TrustFrameworkExtensions.xml*并*ProfileEdit.xml* ，并选择覆盖现有文件。
3. 选择**b2c_1a_profileedit**。
4. 有关**选择应用程序**在自定义策略的概述页上，选择名为 web 应用程序*webapp1*以前注册的。 请确保**回复 URL**是`https://jwt.ms`。
4. 选择**立即运行**。 使用你的帐户凭据登录，然后单击**继续**。

如果所有设置正确，该令牌还包括新的报销申请`city`，使用值`Redmond`。

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

- 还可以将交互设计为验证配置文件。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](active-directory-b2c-rest-api-validation-custom.md)。
- [修改配置文件编辑以收集用户的其他信息](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
