---
title: REST API 声明交换为验证
titleSuffix: Azure AD B2C
description: 用于创建与 RESTful 服务交互的 Azure AD B2C 用户旅程的演练。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a4902e96cd41a02953b6686b5d52d7912b27809f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80330818"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-to-validate-user-input"></a>演练：在 Azure AD B2C 用户旅程中集成 REST API 声明交换，以验证用户输入

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

支持 Azure 活动目录 B2C（Azure AD B2C）的标识体验框架 （IEF） 使标识开发人员能够在用户旅程中集成与 RESTful API 的交互。  在本演练结束时，您将能够创建 Azure AD B2C 用户旅程，该旅程与[RESTful 服务](custom-policy-rest-api-intro.md)进行交互，以验证用户输入。

在这种情况下，我们将添加用户在 Azure AD B2C 注册页中输入会员号码的能力。 我们将通过将此数据发送到 REST API 来验证此电子邮件和会员号码的组合是否已映射到促销代码。 如果 REST API 找到此用户的促销代码，它将返回到 Azure AD B2C。 最后，促销代码将插入到应用程序使用令牌声明中。

也可以将交互设计为业务流程步骤。 当 REST API 不会在屏幕上验证数据，并且始终返回声明时，这一点是合适的。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)。

## <a name="prerequisites"></a>先决条件

- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。 应准备好一个有效的自定义策略，以便使用本地帐户注册和登录。
- 了解如何在[Azure AD B2C 自定义策略中集成 REST API 声明交换](custom-policy-rest-api-intro.md)。

## <a name="prepare-a-rest-api-endpoint"></a>准备 REST API 终结点

在本演练中，您应该有一个 REST API，用于验证电子邮件地址是否在后端系统中注册了一个会员 ID。 如果注册，REST API 应返回注册促销代码，客户可以使用该代码在您的应用程序中购买商品。 否则，REST API 应返回 HTTP 409 错误消息："忠诚 ID '[会员 ID] 未与 "[电子邮件]"电子邮件地址关联。

以下 JSON 代码说明了 Azure AD B2C 将发送到 REST API 终结点的数据。 

```json
{
    "email": "User email address",
    "language": "Current UI language",
    "loyaltyId": "User loyalty ID"
}
```

REST API 验证数据后，必须返回 HTTP 200 （Ok），并包含以下 JSON 数据：

```json
{
    "promoCode": "24534"
}
```

如果验证失败，REST API 必须返回带有 JSON 元素的`userMessage`HTTP 409（冲突）。 IEF 期望`userMessage`REST API 返回的声明。 如果验证失败，此声明将作为字符串呈现给用户。

```json
{
    "version": "1.0.1",
    "status": 409,
    "userMessage": "LoyaltyId ID '1234' is not associated with 'david@contoso.com' email address."
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
<ClaimType Id="loyaltyId">
  <DisplayName>Your loyalty ID</DisplayName>
  <DataType>string</DataType>
  <UserInputType>TextBox</UserInputType>
</ClaimType>
<ClaimType Id="promoCode">
  <DisplayName>Your promo code</DisplayName>
  <DataType>string</DataType>
  <UserInputType>Paragraph</UserInputType>
</ClaimType>
  <ClaimType Id="userLanguage">
  <DisplayName>User UI language (used by REST API to return localized error messages)</DisplayName>
  <DataType>string</DataType>
</ClaimType>
```

## <a name="configure-the-restful-api-technical-profile"></a>配置 RESTful API 技术配置文件 

[宁静的技术配置文件](restful-technical-profile.md)支持与您自己的 RESTful 服务进行接口。 Azure AD B2C 将数据发送到`InputClaims`集合中的 RESTful 服务，并在`OutputClaims`集合中接收数据。 查找**声明提供程序**元素并添加新的声明提供程序，如下所示：

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="REST-ValidateProfile">
      <DisplayName>Check loyaltyId Azure Function web hook</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-account.azurewebsites.net/api/ValidateProfile?code=your-code</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
        <Item Key="AuthenticationType">None</Item>
        <!-- REMOVE the following line in production environments -->
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <!-- Claims sent to your REST API -->
        <InputClaim ClaimTypeReferenceId="loyaltyId" />
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="userLanguage" PartnerClaimType="lang" DefaultValue="{Culture:LCID}" AlwaysUseDefaultValue="true" />
      </InputClaims>
      <OutputClaims>
        <!-- Claims parsed from your REST API -->
        <OutputClaim ClaimTypeReferenceId="promoCode" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

在此示例中，`userLanguage`将像在 JSON 负载中一样`lang`发送到 REST 服务。 `userLanguage`声明的值包含当前用户语言 ID。 有关详细信息，请参阅[声明解析器](claim-resolver-overview.md)。

上面`AuthenticationType`的注释并`AllowInsecureAuthInProduction`指定在移动到生产环境时应所做的更改。 要了解如何保护用于生产的 RESTful API，请参阅安全[RESTful API](secure-rest-api.md)。

## <a name="validate-the-user-input"></a>验证用户输入

要在注册期间获取用户的会员号码，您必须允许用户在屏幕上输入此数据。 通过将**pageaid**输出声明添加到现有注册技术配置文件部分`OutputClaims`的元素，将 pageaid 输出声明添加到注册页。 指定整个输出声明列表，以控制声明在屏幕上显示的顺序。  

将验证技术配置文件引用添加到注册技术配置文件，该配置文件调用`REST-ValidateProfile`。 新的验证技术配置文件将添加到基本策略中定义的`<ValidationTechnicalProfiles>`集合的顶部。 此行为意味着只有在成功验证后，Azure AD B2C 才会继续在目录中创建帐户。   

1. 找到 **ClaimsProviders** 元素。 添加新的声明提供程序，如下所示：

    ```xml
    <ClaimsProvider>
      <DisplayName>Local Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="newPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true"/>
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surName"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    <ClaimsProvider>
      <DisplayName>Self Asserted</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="SelfAsserted-Social">
          <InputClaims>
            <InputClaim ClaimTypeReferenceId="email" />
          </InputClaims>
            <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="email" />
            <OutputClaim ClaimTypeReferenceId="displayName"/>
            <OutputClaim ClaimTypeReferenceId="givenName"/>
            <OutputClaim ClaimTypeReferenceId="surname"/>
            <!-- Required to present the text box to collect the data from the user -->
            <OutputClaim ClaimTypeReferenceId="loyaltyId"/>
            <!-- Required to pass the promoCode returned from "REST-ValidateProfile" 
            to subsequent orchestration steps and token issuance-->
            <OutputClaim ClaimTypeReferenceId="promoCode" />
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="REST-ValidateProfile"/>
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="include-a-claim-in-the-token"></a>在令牌中包含声明 

要将促销代码声明返回到依赖方应用程序，请向<em>`SocialAndLocalAccounts/`</em>文件添加输出声明。 输出声明将允许在用户成功访问后将声明添加到令牌中，并将发送到应用程序。 修改依赖方节中的技术配置文件元素以添加`promoCode`为输出声明。
 
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
      <OutputClaim ClaimTypeReferenceId="promoCode" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>测试自定义策略

1. 登录到 Azure[门户](https://portal.azure.com)。
1. 通过在顶部菜单中选择**目录 + 订阅**筛选器并选择包含 Azure AD 租户的目录，请确保使用的目录包含 Azure AD 租户。
1. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“应用注册”********。
1. 选择**身份体验框架**。
1. 选择 **"上传自定义策略**"，然后上载您更改的策略文件：*信任框架扩展.xml*和*SignUpOrSignin.xml*。 
1. 选择已上传的注册或登录策略，并单击“立即运行”按钮。****
1. 现在，应该可以使用电子邮件地址注册。
1. 单击"**立即注册"** 链接。
1. 在 **"您的会员 ID"** 中，键入 1234，然后单击"**继续**"。 此时，您应该收到一条验证错误消息。
1. 更改为其他值，然后单击"**继续**"。
1. 发送回应用程序的令牌包含 `promoCode` 声明。

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1584295703,
  "nbf": 1584292103,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1584292103,
  "auth_time": 1584292103,
  "name": "Emily Smith",
  "email": "emily@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "promoCode": "84362"
  ...
}
```

## <a name="next-steps"></a>后续步骤

要了解如何保护 API，请参阅以下文章：

- [演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)
- [保护您的 RESTful API](secure-rest-api.md)
- [参考： RESTful 技术配置文件](restful-technical-profile.md)
