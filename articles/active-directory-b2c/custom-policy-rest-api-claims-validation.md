---
title: REST API 声明交换作为验证
titleSuffix: Azure AD B2C
description: 用于创建与 RESTful services 交互的 Azure AD B2C 用户旅程的演练。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7100498d99068941bcd7ca48b6cbcaa271fbb095
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189066"
---
# <a name="walkthrough-integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-on-user-input"></a>演练：在 Azure AD B2C 用户旅程中以用户输入验证的形式集成 REST API 声明交换

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

构成 Azure Active Directory B2C (Azure AD B2C) 基础的标识体验框架 (IEF) 可让标识开发人员在用户旅程中将某种交互与 RESTful API 集成。

完成本演练后，就可以创建与 RESTful 服务交互的 Azure AD B2C 用户旅程了。

IEF 在声明中发送数据，同时也在声明中接收数据。 与 API 的交互：

- 可设计为 REST API 声明交换，或者业务流程步骤中的一个验证配置文件。
- 通常会验证用户提供的输入。 如果用户提供的值被拒绝，该用户可重试输入有效值，同时，系统可能会返回一条错误消息。

也可以将交互设计为业务流程步骤。 有关详细信息，请参阅[演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)。

我们将使用初学者包文件 ProfileEdit.xml 中的配置文件编辑用户旅程作为验证配置文件示例。

我们可以验证用户在配置文件编辑中提供的名称是否为排除列表的一部分。

## <a name="prerequisites"></a>必备条件

- 根据[入门](custom-policy-get-started.md)中所述配置一个 Azure AD B2C 租户，以完成本地帐户注册/登录。
- 要交互的 REST API 终结点。 对于本演练，我们设置了一个名为 [WingTipGames](https://wingtipgamesb2c.azurewebsites.net/) 且包含 REST API 服务的演示站点。

## <a name="step-1-prepare-the-rest-api-function"></a>步骤 1：准备 REST API 函数

> [!NOTE]
> REST API 函数的设置不在本文的讨论范围内。 [Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-reference) 提供一个绝佳的工具包，用于在云中创建 RESTful 服务。

我们创建了一个 Azure 函数，用于接收它所需要的 `playerTag` 声明。 该函数验证是否存在此声明。 可以在 [GitHub](https://github.com/Azure-Samples/active-directory-b2c-advanced-policies/tree/master/AzureFunctionsSamples) 中访问完整的 Azure 函数代码。

```csharp
if (requestContentAsJObject.playerTag == null)
{
  return request.CreateResponse(HttpStatusCode.BadRequest);
}

var playerTag = ((string) requestContentAsJObject.playerTag).ToLower();

if (playerTag == "mcvinny" || playerTag == "msgates123" || playerTag == "revcottonmarcus")
{
  return request.CreateResponse<ResponseContent>(
    HttpStatusCode.Conflict,
    new ResponseContent
    {
      version = "1.0.0",
      status = (int) HttpStatusCode.Conflict,
      userMessage = $"The player tag '{requestContentAsJObject.playerTag}' is already used."
    },
    new JsonMediaTypeFormatter(),
    "application/json");
}

return request.CreateResponse(HttpStatusCode.OK);
```

IEF 需要 Azure 函数返回的 `userMessage` 声明。 如果验证失败，例如前面的示例中返回 409 冲突状态时，此声明将以字符串的形式向用户显示。

## <a name="step-2-configure-the-restful-api-claims-exchange-as-a-technical-profile-in-your-trustframeworkextensionsxml-file"></a>步骤 2：在 TrustFrameworkExtensions.xml 文件中将 RESTful API 声明交换配置为技术配置文件

技术配置文件是 RESTful 服务所需的交换的完整配置。 打开 TrustFrameworkExtensions.xml 文件并在元素 `<ClaimsProviders>` 内添加以下 XML 代码片段。

> [!NOTE]
> 在以下 XML 中，RESTful 提供程序 `Version=1.0.0.0` 被描述为协议。 将其视为与外部服务进行交互的函数。 <!-- TODO: A full definition of the schema can be found...link to RESTful Provider schema definition>-->

```xml
<ClaimsProvider>
    <DisplayName>REST APIs</DisplayName>
    <TechnicalProfiles>
        <TechnicalProfile Id="AzureFunctions-CheckPlayerTagWebHook">
            <DisplayName>Check Player Tag Web Hook Azure Function</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <Metadata>
                <Item Key="ServiceUrl">https://wingtipb2cfuncs.azurewebsites.net/api/CheckPlayerTagWebHook?code=L/05YRSpojU0nECzM4Tp3LjBiA2ZGh3kTwwp1OVV7m0SelnvlRVLCg==</Item>
                <Item Key="SendClaimsIn">Body</Item>
                <!-- Set AuthenticationType to Basic or ClientCertificate in production environments -->
                <Item Key="AuthenticationType">None</Item>
                <!-- REMOVE the following line in production environments -->
                <Item Key="AllowInsecureAuthInProduction">true</Item>
            </Metadata>
            <InputClaims>
                <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="playerTag" />
            </InputClaims>
            <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
            <ValidationTechnicalProfiles>
                <ValidationTechnicalProfile ReferenceId="AzureFunctions-CheckPlayerTagWebHook" />
            </ValidationTechnicalProfiles>
        </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

`InputClaims` 元素定义要从 IEF 发送到 REST 服务的声明。 在本示例中，声明 `givenName` 的内容将以 `playerTag` 的形式发送到 REST 服务。 本示例中，IEF 不需要返回的声明。 相反，它等待来自 REST 服务的响应并根据所接收的状态代码执行操作。

上述注释 `AuthenticationType` 和 `AllowInsecureAuthInProduction` 指定在迁移到生产环境时应进行的更改。 若要了解如何保护用于生产的 RESTful Api，请参阅通过[证书身份验证](secure-rest-api-dotnet-certificate-auth.md)实现基本身份验证和安全 RESTful Api[安全 RESTful api](secure-rest-api-dotnet-basic-auth.md) 。

## <a name="step-3-include-the-restful-service-claims-exchange-in-self-asserted-technical-profile-where-you-want-to-validate-the-user-input"></a>步骤 3：在要验证用户输入的自我断言技术配置文件中包含 RESTful 服务声明交换

验证步骤的最常见用途是与用户交互。 用户预期需要提供输入的所有交互都属于自我断言技术配置文件。 在本示例中，我们会将此验证添加到 Self-Asserted-ProfileUpdate 技术配置文件。 这是信赖方 (RP) 策略文件 `Profile Edit` 所使用的技术配置文件。

将声明交换添加到自我断言技术配置文件：

1. 打开 TrustFrameworkBase.xml 文件并搜索 `<TechnicalProfile Id="SelfAsserted-ProfileUpdate">`。
2. 查看此技术配置文件的配置。 观察如何将用户交换定义为需要用户响应的声明（输入声明），以及预期需要自我断言提供程序返回的声明（输出声明）。
3. 搜索 `TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate`，可以看到此配置文件已作为 `<UserJourney Id="ProfileEdit">` 业务流程的第 5 步调用。

## <a name="step-4-upload-and-test-the-profile-edit-rp-policy-file"></a>步骤 4：上传并测试配置文件编辑 RP 策略文件

1. 上传新版本的 TrustFrameworkExtensions.xml 文件。
2. 使用“立即运行”测试配置文件编辑 RP 策略文件。
3. 通过在“给定名称”字段中提供某个现有名称（例如：mcvinny）来测试验证。 如果所有设置正确，应会收到一条消息，告知用户播放器标记已被使用。

## <a name="next-steps"></a>后续步骤

[修改配置文件编辑和用户注册以便收集用户的其他信息](custom-policy-custom-attributes.md)

[演练：在 Azure AD B2C 用户旅程中以业务流程步骤的形式集成 REST API 声明交换](custom-policy-rest-api-claims-exchange.md)

[参考： RESTful 技术配置文件](restful-technical-profile.md)

若要了解如何保护 Api，请参阅以下文章：

* [使用基本身份验证（用户名和密码）保护 RESTful API](secure-rest-api-dotnet-basic-auth.md)
* [使用客户端证书保护 RESTful API](secure-rest-api-dotnet-certificate-auth.md)
