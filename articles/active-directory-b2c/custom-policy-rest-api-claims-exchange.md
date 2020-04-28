---
title: REST API 声明交换-Azure Active Directory B2C
description: 将 REST API 声明交换添加到 Active Directory B2C 中的自定义策略。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6316165ba08d055be1186995e2fe2ad5a0079fb7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80330722"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>演练：将 REST API 声明交换添加到 Azure Active Directory B2C 中的自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）使标识开发人员能够在用户旅程中集成与 RESTful API 的交互。 本演练结束时，你将能够创建与[RESTful services](custom-policy-rest-api-intro.md)交互的 Azure AD B2C 用户旅程。

在此方案中，我们通过与企业业务线工作流集成来丰富用户的令牌数据。 在注册或使用本地帐户或联合帐户登录期间，Azure AD B2C 调用 REST API 从远程数据源获取用户的扩展配置文件数据。 在此示例中，Azure AD B2C 发送用户的唯一标识符，即 objectId。 然后，REST API 返回用户的帐户余额（随机数字）。 使用此示例作为与你自己的 CRM 系统、市场营销数据库或任何业务线工作流集成的起点。

你还可以将交互设计为验证技术配置文件。 当 REST API 将在屏幕上验证数据并返回声明时，这非常合适。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换来验证用户输入](custom-policy-rest-api-claims-validation.md)。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。
- 了解如何[在 Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>准备 REST API 终结点

对于本演练，您应该有一个 REST API 验证是否在后端系统中注册了用户的 Azure AD B2C objectId。 如果已注册，则 REST API 返回用户帐户余额。 否则，REST API 将在目录中注册新帐户并返回起始平衡`50.00`。

下面的 JSON 代码演示 Azure AD B2C 将发送到 REST API 终结点的数据。 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

一旦您的 REST API 验证数据，它必须返回带有以下 JSON 数据的 HTTP 200 （Ok）：

```json
{
    "balance": "760.50"
}
```

REST API 终结点的设置不在本文的讨论范围内。 我们创建了一个[Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference)示例。 可以在[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)上访问完整的 Azure 函数代码。

## <a name="define-claims"></a>定义声明

声明在 Azure AD B2C 策略执行过程中提供数据的临时存储。 可以在 "[声明架构](claimsschema.md)" 部分中声明声明。 

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到 " [ClaimsSchema](claimsschema.md) " 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到**ClaimsSchema**元素中。  

```xml
<ClaimType Id="balance">
  <DisplayName>Your Balance</DisplayName>
  <DataType>string</DataType>
</ClaimType>
<ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技术配置文件 

[Restful 技术配置文件](restful-technical-profile.md)提供与你自己的 Restful 服务的交互支持。 Azure AD B2C 将数据发送到`InputClaims`集合中的 RESTful 服务，并在`OutputClaims`集合中接收返回的数据。 在<em>**`TrustFrameworkExtensions.xml`**</em>文件中找到**ClaimsProviders**元素并添加新的声明提供程序，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-GetProfile">
      <DisplayName>Get user extended profile Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/GetProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="objectId" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="balance" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

在此示例中， `userLanguage`将以 JSON 有效负载的形式`lang`发送到 REST 服务。 `userLanguage`声明的值包含当前用户语言 ID。 有关详细信息，请参阅[声明解析程序](claim-resolver-overview.md)。

上述`AuthenticationType`注释并`AllowInsecureAuthInProduction`指定移动到生产环境时应进行的更改。 若要了解如何保护用于生产的 RESTful Api，请参阅[Secure RESTFUL api](secure-rest-api.md)。

## <a name="add-an-orchestration-step"></a>添加业务流程步骤

[用户旅程](userjourneys.md)指定显式路径，通过该路径可使信赖方应用程序获取用户所需的声明。 用户旅程表示为成功事务必须遵循的业务流程序列。 你可以添加或减少业务流程步骤。 在这种情况下，你将添加新的业务流程步骤，该步骤用于在用户通过 REST API 调用注册或登录之后为应用程序提供的信息进行扩充。

1. 打开策略的基文件。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 搜索 `<UserJourneys>` 元素。 复制整个元素，然后将其删除。
1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>。
1. 在`<ClaimsProviders>`元素`<UserJourneys>`关闭后，将粘贴到扩展文件中。
1. 找到`<UserJourney Id="SignUpOrSignIn">`，然后在最后一个业务流程步骤之前添加它。

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. 通过将更改`Order`为来`8`重构最后一个业务流程步骤。 最后两个业务流程步骤应该如下所示：

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. 对于**profileedit.xml**和**PasswordReset**用户旅程，重复执行最后两个步骤。


## <a name="include-a-claim-in-the-token"></a>在令牌中包括声明 

若要将`balance`声明返回给信赖方应用程序，请将输出声明添加到<em> `SocialAndLocalAccounts/` </em>该文件。 添加输出声明会在用户成功旅程后向令牌发出声明，并将其发送到应用程序。 修改信赖方部分内的技术配置文件元素，以`balance`将其添加为输出声明。
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="balance" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

对于**profileedit.xml**和**PasswordReset**用户旅程，请重复此步骤。

保存已更改的文件： *trustframeworkbase.xml*、 *trustframeworkextensions.xml*、 *signuporsignin.xml*、 *profileedit.xml*和*PasswordReset*文件中的文件。 

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录 [Azure 门户](https://portal.azure.com)。
1. 在顶部菜单中选择 "**目录 + 订阅**" 筛选器并选择包含 Azure AD 租户的目录，确保使用的是包含 Azure AD 租户的目录。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择 "**标识体验框架**"。
1. 选择 "**上载自定义策略**"，然后上传所更改的策略文件： *trustframeworkbase.xml*、 *trustframeworkextensions.xml*、 *signuporsignin.xml*、 *profileedit.xml*和*PasswordReset*。 
1. 选择已上传的注册或登录策略，并单击“立即运行”按钮。****
1. 应该可以使用电子邮件地址或 Facebook 帐户进行注册。
1. 发送回应用程序的令牌包含 `balance` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584961516,
  "nbf": 1584957916,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584957916,
  "auth_time": 1584957916,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "balance": "202.75"
  ...
}
```

## <a name="next-steps"></a>后续步骤


## <a name="next-steps"></a>后续步骤

若要了解如何保护 Api，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)
- [保护你的 RESTful API](secure-rest-api.md)
- [参考： RESTful 技术配置文件](restful-technical-profile.md)
