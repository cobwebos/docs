---
title: TypingDNA 与 Azure Active Directory B2C
titleSuffix: Azure AD B2C
description: 了解如何将 Azure AD B2C authentication 与 TypingDNA 集成，以帮助根据用户键入模式进行身份验证和校对，提供 ID 验证解决方案来强制执行多重身份验证，并帮助符合支付服务指令 2 (PSD2) 的 SCA 要求。
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: f7d89942ad5209b854b8df486ad3e59a3976edfc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259045"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>有关配置 TypingDNA 与 Azure Active Directory B2C 的教程

在本演练中，了解如何将 Azure Active Directory B2C online 付款应用与 TypingDNA 应用集成在一起。 通过使用 TypingDNA 应用，Azure AD B2C 客户可以通过击键动态和强大的客户身份验证来符合 [支付服务指令 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (PSD2) 交易要求。 [在此处](https://www.typingdna.com/)查找有关 TypingDNA 的详细信息。

 Azure AD B2C 使用 TypingDNA 的技术捕获用户键入特性，并记录和分析它们，以熟悉每个身份验证。 这会添加一个与身份验证风险有关的保护层，并评估风险级别。 Azure AD B2C 可以通过调用 Azure MFA、强制电子邮件验证或方案的任何其他自定义逻辑，来调用其他机制，以便进一步满怀信心地提供用户的身份。

>[!NOTE]
> 此示例策略基于 [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) starter pack。

## <a name="scenario-description"></a>方案描述

![TypingDNA 体系结构图的屏幕截图](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>注册

1. Azure AD B2C 页面使用 TypingDNA 的 JavaScript 库来记录用户的键入模式。 例如，在注册初始注册时记录用户名和密码，然后在每次登录时记录验证。

2. 当用户提交页面时，TypingDNA 库将计算用户的键入特征。 然后，将信息插入 Azure AD B2C 呈现的隐藏文本字段。 此字段已隐藏为 CSS。  

    此 [示例包含 HTML 文件](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml) ，其中包含 JAVASCRIPT 和 CSS 修改，并且由 `api.selfasserted.tdnasignin` 和 `api.selfasserted.tdnasignup` 内容定义引用。 请参阅 [托管页面内容](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#hosting-the-page-content) 以托管 HTML 文件。

3. 现在，在用户提交凭据时，Azure AD B2C 在声明包中具有键入模式。 它必须调用 API (你的) 才能将此数据传递到 TypingDNA REST API 终结点。 此 API 包含在 [ (typingDNA) 的示例 ](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)中。
4. 然后，中间层 API 将键入模式数据传递给 TypingDNA REST API。 注册时，将调用 [检查用户终结点](https://api.typingdna.com/index.html#api-API_Services-GetUser) 以确认用户是否不存在，然后调用 [保存模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 终结点以保存用户的第一种键入模式。

> [!NOTE]
> 对 TypingDNA REST API 终结点的所有调用都将发送 UserId。 这必须是一个哈希值。 Azure AD B2C 使用 `HashObjectIdWithEmail` 声明转换通过随机 salt 和机密对电子邮件进行哈希处理。  

REST API 调用在中进行建模 `validationTechnicalProfiles` `LocalAccountSignUpWithLogonEmail-TDNA` ：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>登录

在后续登录时，用户的键入模式的计算方式与使用 [自定义 HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml)进行注册时相同。 键入配置文件位于 Azure AD B2C 声明包内后，Azure AD B2C 将调用你的 API 来调用 TypingDNA 的 REST API 终结点。 调用 [检查用户](https://api.typingdna.com/index.html#api-API_Services-GetUser) 终结点以确认该用户是否存在。 接下来， [请验证](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) 是否调用了模式终结点以返回 `net_score` 。 这 `net_score` 表明了在注册时如何将键入模式与原始内容接近。

此键入模式在中进行 `validationTechnicalProfiles` 建模 `SelfAsserted-LocalAccountSignin-Email-TDNA` ：

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 如果用户获取的打字模式具有较高的类型 `net_score` ，则可以使用 TypingDNA [保存类型模式](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) 终结点保存此模式。  

如果希望通过  `saveTypingPattern` api) Azure AD B2C (来调用 TypingDNA 保存类型化模式终结点，则 API 必须返回声明。

存储库中的示例包含使用以下属性配置的 API (TypingDNA) 。

- 定型模式-如果用户保存的模式少于两个，则始终提示进行 MFA。

- 如果用户已保存2-5 模式，并且低于 `net_score` 50，则会提示进行 MFA。

- 如果用户保存了 5 + 模式，并且低于 `net_score` 65，则会提示进行 MFA。

应根据用例调整这些阈值。

- API 计算后 `net_score` ，应将布尔声明返回到 B2C `promptMFA` 。

- 在 `promptMFA` 前置条件中使用声明来有条件地执行 AZURE MFA。

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>内置 TypingDNA

1. [在此处](https://www.typingdna.com/)注册 TypingDNA
2. 登录到 TypingDNA 仪表板，获取 **api 密钥** 和 **api**密钥。 稍后会在 API 界面设置中进行此设置

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>将 TypingDNA 与 Azure AD B2C 集成

1. 托管所选提供程序的[TypingDNA-API 接口](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)
2. 将和中的所有实例替换为 `apiKey` `apiSecret` 你的 TypingDNA 仪表板中的凭据，并在 [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) 解决方案中
3. 按照[此处](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-ui-customization#3-configure-cors)的 CORS 要求，在所选的提供程序上托管 HTML 文件
4. 将 `api.selfasserted.tdnasignup` 文件中的和内容定义的 loaduri 来元素 `api.selfasserted.tdnasignin` 分别替换 `TrustFrameworkExtensions.xml` 为托管的 HTML 文件的 URI。
5. 在 **Azure 门户**的 "Azure AD" 边栏选项卡中的 "标识体验框架" 下创建 B2C 策略项。 使用 `Generate` 选项并将此密钥命名为 `tdnaHashedId` 。
6. 替换策略文件中的 TenantId
7. 将所有 TypingDNA REST API 技术配置文件中的 ServiceURLs 替换 (TDNA-VerifyUser，rest-TDNA-SaveUser，REST-TDNA-CheckUser) ，并将其用于 [TYPINGDNA api](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface)。
8. 将 [策略文件](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy) 上传到你的租户。

## <a name="test-the-user-flow"></a>测试用户流

1. 打开 B2C 租户，并选择 "标识体验框架"。
2. 选择以前创建的 **用户流**。
3. 选择 " **运行** 用户流"

    a. **应用程序** -选择注册应用 (示例为 JWT) 

    b. **回复 url** -选择重定向 url

    c. 选择“运行用户流”。
  
4. 浏览注册流并创建帐户
5. 注销
6. 经历登录流
7. 生成的 JWT 结果将显示 TypingDNA 结果

## <a name="live-version"></a>Live 版本

•在此测试版本中已禁用 MFA，但你可以查看在身份验证后声明是否由声明提示的结果 `promptMFA` 。

•在[此处](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)注册并在[此处](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)登录

## <a name="next-steps"></a>后续步骤

有关其他信息，请查看以下文章：

- [AAD B2C 中的自定义策略](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [AAD B2C 中的自定义策略入门](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
