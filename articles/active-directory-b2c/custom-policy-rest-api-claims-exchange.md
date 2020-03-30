---
title: REST API 声明交换 - Azure 活动目录 B2C
description: 将 REST API 声明交换添加到活动目录 B2C 中的自定义策略。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330722"
---
# <a name="walkthrough-add-rest-api-claims-exchanges-to-custom-policies-in-azure-active-directory-b2c"></a>演练：将 REST API 声明交换添加到 Azure 活动目录 B2C 中的自定义策略

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure 活动目录 B2C（Azure AD B2C）使身份开发人员能够在用户旅程中集成与 RESTful API 的交互。 在本演练结束时，您将能够创建与[RESTful 服务](custom-policy-rest-api-intro.md)交互的 Azure AD B2C 用户旅程。

在此方案中，我们通过与企业业务线工作流集成来丰富用户的令牌数据。 在注册或使用本地或联合帐户登录期间，Azure AD B2C 调用 REST API 从远程数据源获取用户的扩展配置文件数据。 在此示例中，Azure AD B2C 会发送用户的唯一标识符对象 Id。 然后，REST API 返回用户的帐户余额（随机数）。 使用此示例作为起点，与您自己的 CRM 系统、营销数据库或任何业务线工作流集成。

您还可以将交互设计为验证技术配置文件。 当 REST API 将验证屏幕上的数据并返回声明时，这一点是合适的。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换以验证用户输入](custom-policy-rest-api-claims-validation.md)。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。
- 了解如何在[Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>准备 REST API 终结点

在本演练中，应该有一个 REST API，用于验证用户的 Azure AD B2C 对象 Id 是否已在后端系统中注册。 如果注册，REST API 将返回用户帐户余额。 否则，REST API 将新帐户注册到目录中并返回起始余额`50.00`。

以下 JSON 代码说明了 Azure AD B2C 将发送到 REST API 终结点的数据。 

```json
{
    "objectId": "User objectId",
    "language": "Current UI language"
}
```

REST API 验证数据后，必须返回 HTTP 200 （Ok），并包含以下 JSON 数据：

```json
{
    "balance": "760.50"
}
```

REST API 终结点的设置不在本文的讨论范围之内。 我们创建了[Azure 函数](https://docs.microsoft.com/azure/azure-functions/functions-reference)示例。 您可以在[GitHub](https://github.com/azure-ad-b2c/rest-api/tree/master/source-code/azure-function)访问完整的 Azure 函数代码。

## <a name="define-claims"></a>定义声明

声明在 Azure AD B2C 策略执行期间提供数据的临时存储。 您可以在[声明架构](claimsschema.md)部分中声明声明声明。 

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 搜索 BuildingBlocks[](buildingblocks.md) 元素。 如果该元素不存在，请添加该元素。
1. 找到[声明架构](claimsschema.md)元素。 如果该元素不存在，请添加该元素。
1. 将以下声明添加到**声明架构**元素。  

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

[宁静的技术配置文件](restful-technical-profile.md)支持与您自己的 RESTful 服务进行接口。 Azure AD B2C 将数据发送到`InputClaims`集合中的 RESTful 服务，并在`OutputClaims`集合中接收数据。 在文件中<em>**`TrustFrameworkExtensions.xml`**</em>查找**声明提供程序**元素，并添加新的声明提供程序，如下所示：

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

在此示例中，`userLanguage`将像在 JSON 负载中一样`lang`发送到 REST 服务。 `userLanguage`声明的值包含当前用户语言 ID。 有关详细信息，请参阅[声明解析器](claim-resolver-overview.md)。

上面`AuthenticationType`的注释并`AllowInsecureAuthInProduction`指定在移动到生产环境时应所做的更改。 要了解如何保护用于生产的 RESTful API，请参阅安全[RESTful API](secure-rest-api.md)。

## <a name="add-an-orchestration-step"></a>添加业务流程步骤

[用户旅程](userjourneys.md)指定策略允许依赖方应用程序获取用户所需的声明的显式路径。 用户旅程表示为成功事务必须遵循的业务流程序列。 您可以添加或减去业务流程步骤。 在这种情况下，您将添加一个新的业务流程步骤，用于在用户通过 REST API 调用注册或登录后增强提供给应用程序的信息。

1. 打开策略的基文件。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 搜索 `<UserJourneys>` 元素。 复制整个元素，然后将其删除。
1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>.
1. 在`<ClaimsProviders>`元素`<UserJourneys>`关闭后，将 粘贴到扩展文件中。
1. 找到`<UserJourney Id="SignUpOrSignIn">`， 并在最后一个业务流程步骤之前添加以下业务流程步骤。

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>
    ```

1. 通过将 更改为 重`Order`构最后一个业务流程步骤`8`。 最后两个业务流程步骤应如下所示：

    ```XML
    <OrchestrationStep Order="7" Type="ClaimsExchange">
      <ClaimsExchanges>
        <ClaimsExchange Id="RESTGetProfile" TechnicalProfileReferenceId="REST-GetProfile" />
      </ClaimsExchanges>
    </OrchestrationStep>

    <OrchestrationStep Order="8" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
    ```

1. 重复**配置文件编辑**和**密码重置**用户旅程的最后两个步骤。


## <a name="include-a-claim-in-the-token"></a>在令牌中包含声明 

要将`balance`声明返回到依赖方应用程序，请向<em>`SocialAndLocalAccounts/`</em>文件添加输出声明。 添加输出声明将在用户成功旅程后将声明发送到令牌中，并将发送到应用程序。 修改依赖方部分中的技术配置文件元素以添加`balance`为输出声明。
 
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

对**配置文件编辑.xml**和**PasswordReset.xml**用户旅程重复此步骤。

保存您更改的文件：*信任框架Base.xml*， 和*信任框架扩展.xml*， *SignUpOrSignin.xml*，*配置文件编辑.xml*， 和*密码Reset.xml*. . 

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD 租户的目录，请确保使用的目录包含 Azure AD 租户。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择**身份体验框架**。
1. 选择 **"上传自定义策略**"，然后上传您更改的策略文件 *：TrustFrameworkBase.xml*和*TrustFramework 扩展.xml、SignUpOrSignin.xml、ProfileEdit.xml*和*PasswordReset.xml*。 *SignUpOrSignin.xml* *ProfileEdit.xml* 
1. 选择已上传的注册或登录策略，并单击“立即运行”按钮。****
1. 您应该能够使用电子邮件地址或 Facebook 帐户进行注册。
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

要了解如何保护 API，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)
- [保护您的 RESTful API](secure-rest-api.md)
- [参考： RESTful 技术配置文件](restful-technical-profile.md)
