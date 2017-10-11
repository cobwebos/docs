---
title: "Azure Active Directory B2C：业务流程步骤形式的 REST API 声明交换 | Microsoft Docs"
description: "有关与 API 集成的 Azure Active Directory B2C 自定义策略的主题"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: rojasja
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/24/2017
ms.author: joroja
ms.openlocfilehash: dc319c97e64e55861b84cc3943667418077a05d8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-an-orchestration-step"></a>演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换

构成 Azure Active Directory B2C (Azure AD B2C) 基础的标识体验框架 (IEF) 可让标识开发人员在用户旅程中将某种交互与 RESTful API 集成。  

完成本演练后，就可以创建与 RESTful 服务交互的 Azure AD B2C 用户旅程了。

IEF 在声明中发送数据，同时也在声明中接收数据。 REST API 声明交换：

- 可以设计为业务流程步骤。
- 可以触发外部操作。 例如，它可能会在外部数据库中记录事件。
- 可用于提取值并将其存储在用户数据库中。

你可以更高版本使用收到的声明若要更改执行流。

还可以将交互设计为验证配置文件。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换](active-directory-b2c-rest-api-validation-custom.md)。

应用场景为，当用户执行配置文件编辑时，我们需要：

1. 在外部系统中查找用户。
2. 获取用户完成注册所在的城市。
3. 将属性作为声明返回给应用程序。

## <a name="prerequisites"></a>先决条件

- 根据[入门](active-directory-b2c-get-started-custom.md)中所述配置一个 Azure AD B2C 租户，以完成本地帐户注册/登录。
- 要交互的 REST API 终结点。 该演练采用简单的 Azure 函数应用 webhook 作为示例。
- *建议*：完成[验证步骤形式的 REST API 声明交换演练](active-directory-b2c-rest-api-validation-custom.md)。

## <a name="step-1-prepare-the-rest-api-function"></a>步骤 1：准备 REST API 函数

> [!NOTE]
> REST API 函数的设置不在本文的讨论范围内。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) 提供一个绝佳的工具包，用于在云中创建 RESTful 服务。

我们已设置一个用于接收声明 `email` 的 Azure 函数，然后返回具有分配值 `Redmond` 的声明 `city`。 [Github](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) 中提供了示例 Azure 函数。

Azure 函数返回的 `userMessage` 声明在此上下文中是可选的，将被 IEF 忽略。 您可以使用它作为消息传递到应用程序和更高版本提供给用户。

```csharp
if (requestContentAsJObject.email == null)
{
    return request.CreateResponse(HttpStatusCode.BadRequest);
}

var email = ((string) requestContentAsJObject.email).ToLower();

return request.CreateResponse<ResponseContent>(
    HttpStatusCode.OK,
    new ResponseContent
    {
        version = "1.0.0",
        status = (int) HttpStatusCode.OK,
        userMessage = "User Found",
        city = "Redmond"
    },
    new JsonMediaTypeFormatter(),
    "application/json");
```

使用 Azure 函数应用可以轻松获取函数 URL，其中包含特定函数的标识符。 在本例中，该 URL 为：https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==。 可将它用于测试目的。

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworextensionsxml-file"></a>步骤 2：在 TrustFrameworExtensions.xml 文件中将 RESTful API 声明交换配置为技术配置文件

技术配置文件是 RESTful 服务所需的交换的完整配置。 打开 TrustFrameworkExtensions.xml 文件并在元素 `<ClaimsProvider>` 内添加以下 XML 代码片段。

> [!NOTE]
> 在以下 XML 中，RESTful 提供程序 `Version=1.0.0.0` 被描述为协议。 将其视为与外部服务进行交互的函数。 <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```XML
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-LookUpLoyaltyWebHook">
            <DisplayName>Check LookUpLoyalty Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/LookUpLoyaltyWebHook?code=MQuG7BIE3eXBaCZ/YCfY1SHabm55HEphpNLmh1OP3hdfHkvI2QwPrw==</Item>
                <Item Key="AuthenticationType">None</Item>
                <Item Key="SendClaimsIn">Body</Item>
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

`<InputClaims>` 元素定义要从 IEF 发送到 REST 服务的声明。 在本示例中，声明 `givenName` 的内容将以声明 `email` 的形式发送到 REST 服务。  

`<OutputClaims>` 元素定义 IEF 预期从 REST 服务收到的声明。 不管收到了多少个声明，IEF 都只使用此处指定的声明。 在本示例中，以 `city` 形式收到的声明将映射到 IEF 声明 `city`。

## <a name="step-3-add-the-new-claim-city-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>步骤 3：将新的声明 `city` 添加到 TrustFrameworkExtensions.xml 文件的架构

声明 `city` 未在我们架构中的任何位置定义。 因此，请在元素 `<BuildingBlocks>` 中添加一个定义。 你可以在 TrustFrameworkExtensions.xml 文件的开头处找到此元素。

```XML
<BuildingBlocks>
    <!--The claimtype city must be added to the TrustFrameworkPolicy-->
    <!-- You can add new claims in the BASE file Section III, or in the extensions file-->
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

## <a name="step-4-include-the-rest-service-claims-exchange-as-an-orchestration-step-in-your-profile-edit-user-journey-in-trustframeworkextensionsxml"></a>步骤 4：在 TrustFrameworkExtensions.xml 中的配置文件编辑用户旅程内以业务流程步骤的形式加入 REST 服务声明交换

在用户完成身份验证（以下 XML 中的业务流程步骤 1-4）并提供更新的配置文件信息（步骤 5）后，在配置文件编辑用户旅程中添加步骤。

> [!NOTE]
> 在许多用例中，可将 REST API 调用用作业务流程步骤。 用户成功完成某个任务（例如首次注册）后，可以使用业务流程步骤对外部系统进行更新；或者使用它来对配置文件进行更新，以保持信息同步。 在本例中，业务流程步骤用于在完成配置文件编辑后，对提供给应用程序的信息做出补充。

将配置文件编辑用户旅程 XML 代码从 TrustFrameworkBase.xml 文件复制到元素 `<UserJourneys>` 中的 TrustFrameworkExtensions.xml 文件。 然后在步骤 6 中进行修改。

```XML
<OrchestrationStep Order="6" Type="ClaimsExchange">
    <ClaimsExchanges>
        <ClaimsExchange Id="GetLoyaltyData" TechnicalProfileReferenceId="AzureFunctions-LookUpLoyaltyWebHook" />
    </ClaimsExchanges>
</OrchestrationStep>
```

> [!IMPORTANT]
> 如果步骤顺序与你的版本不符，请务必在 `ClaimsExchange` 类型 `SendClaims` 前面以步骤形式插入该代码。

用户旅程最终的 XML 应如下所示：

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

## <a name="step-5-add-the-claim-city-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>步骤 5：将声明 `city` 添加到信赖方策略文件，以便将该声明发送到应用程序

编辑 ProfileEdit.xml 信赖方 (RP) 文件并修改 `<TechnicalProfile Id="PolicyProfile">` 元素以添加如下内容：`<OutputClaim ClaimTypeReferenceId="city" />`。

添加新声明后，技术配置文件应如下所示：

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

## <a name="step-6-upload-your-changes-and-test"></a>步骤 6：上传更改并进行测试

覆盖现有的策略版本。

1.  （可选：）在继续下一步之前，请（通过下载的方式）保存现有的扩展文件版本。 为保证初始复杂度较低，建议不要上传多个扩展文件版本。
2.  （可选：）通过更改 `PolicyId="B2C_1A_TrustFrameworkProfileEdit"` 为策略编辑文件重新命名新版策略 ID。
3.  上传扩展文件。
4.  上传策略编辑 RP 文件。
5.  使用“立即运行”测试策略。 查看 IEF 返回给应用程序的令牌。

如果所有设置正确，令牌将包含值为 `Redmond` 的新声明 `city`。

```JSON
{
  "exp": 1493053292,
  "nbf": 1493049692,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493049692,
  "auth_time": 1493049692,
  "city": "Redmond"
}
```

## <a name="next-steps"></a>后续步骤

[使用 REST API 作为验证步骤](active-directory-b2c-rest-api-validation-custom.md)

[修改配置文件编辑以收集用户的其他信息](active-directory-b2c-create-custom-attributes-profile-edit-custom.md)
