---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Microsoft Docs
description: 在 Azure Active Directory B2C 中指定自定义策略的 TrustFrameworkPolicy 元素。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5a0ba48acf6ec3d221d9c4b5e95b380a2154171f
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67537055"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

自定义策略以一个或多个采用 XML 格式的文件表示，这些文件在分层链中相互引用。 XML 元素定义策略的元素，例如声明架构、声明转换、内容定义、声明提供程序、技术配置文件、用户旅程和业务流程步骤。 每个策略文件在策略文件的顶级 **TrustFrameworkPolicy** 元素中定义。 

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


**TrustFrameworkPolicy** 元素包含以下属性：

| 特性 | 需要 | 描述 |
|---------- | -------- | ----------- |
| PolicySchemaVersion | 是 | 用于执行策略的架构版本。 值必须是 `0.3.0.0` |
| TenantObjectId | 否 | Azure Active Directory (Azure AD) B2C 租户的唯一对象标识符。 |
| TenantId | 是 | 此策略所属的租户的唯一标识符。 |
| PolicyId | 是 | 策略的唯一标识符。 此标识符必须带有 *B2C_1A_* 前缀 |
| PublicPolicyUri | 是 | 策略的 URI，它是租户 ID 和策略 ID 的组合。 |
| DeploymentMode | 否 | 可能的值：`Production`、`Debugging` 或 `Development`。 `Production` 为默认值。 使用此属性来调试策略。 有关详细信息，请参阅[收集日志](active-directory-b2c-troubleshoot-custom.md)。 |
| UserJourneyRecorderEndpoint | 否 | 当 **DeploymentMode** 设置为 `Development` 时使用的终结点。 值必须是 `urn:journeyrecorder:applicationinsights`。 有关详细信息，请参阅[收集日志](active-directory-b2c-troubleshoot-custom.md)。 |


以下示例演示如何指定 **TrustFrameworkPolicy** 元素：

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>继承模型

用户旅程中通常使用以下类型的策略文件：

- **基本**文件：包含大多数定义。 为了帮助进行故障排除和长期维护策略，我们建议对此文件进行极少量的更改。
- **扩展**文件：保存租户的独特配置更改。 此策略文件派生自“基本”文件。 使用此文件可以添加新功能或替代现有功能。 例如，使用此文件可与新的标识提供者联合。
- **信赖方 (RP)** 文件：注重单个任务的文件，由信赖方应用程序（例如 Web、移动或桌面应用程序）直接调用。 每个独特的任务（例如注册或登录、密码重置或配置文件编辑）都需要自身的 RP 策略文件。 此策略文件派生自“扩展”文件。 

信赖方应用程序调用 RP 策略文件来执行特定的任务。 例如，启动登录流。 Azure AD B2C 中的标识体验框架依次从“基本”文件、“扩展”文件和“RP”策略文件中添加所有元素，以组合当前生效的策略。 “RP”文件中具有相同类型和名称的元素将替代“扩展”中的这些元素，“扩展”替代“基本”。 下图显示了策略文件与信赖方应用程序之间的关系。

![继承模型](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

继承模型如下所示：

- 父策略和子策略的架构相同。
- 任何级别的子策略可以继承自父策略，并通过添加新元素来扩展父策略。
- 级别数没有限制。

有关详细信息，请参阅[自定义策略入门](active-directory-b2c-get-started-custom.md)。

## <a name="base-policy"></a>基本策略

若要从另一个策略继承某个策略，必须在策略文件的 **TrustFrameworkPolicy** 元素下声明 **BasePolicy** 元素。 **BasePolicy** 元素是对从中派生此策略的基本策略的引用。  

**BasePolicy** 元素包含以下元素：

| 元素 | 出现次数 | 描述 |
| ------- | ----------- | --------|
| TenantId | 1:1 | Azure AD B2C 租户的标识符。 |
| PolicyId | 1:1 | 父策略的标识符。 |


以下示例演示如何指定基本策略。 此 **B2C_1A_TrustFrameworkExtensions** 策略派生自 **B2C_1A_TrustFrameworkBase** 策略。 

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>策略执行

信赖方应用程序（例如 Web、移动或桌面应用程序）调用[信赖方 (RP) 策略](relyingparty.md)。 RP 策略文件执行特定任务，例如登录、重置密码，或编辑配置文件。 RP 策略将信赖方应用程序收到的声明列表配置为所颁发令牌的一部分。 多个应用程序可以使用同一策略。 所有应用程序都会收到包含声明的相同令牌，用户会经历相同的用户旅程。 单个应用程序可以使用多个策略。

在 RP 策略文件中，指定指向 [UserJourney](userjourneys.md) 的 **DefaultUserJourney** 元素。 用户旅程通常在基本或扩展策略中定义。

B2C_1A_signup_signin 策略：

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase 或 B2C_1A_TrustFrameworkExtensionPolicy：

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

用户旅程定义用户所要经历的业务逻辑。 每个用户旅程是按顺序执行一系列操作，以进行身份验证和收集信息的一组业务流程步骤。 

[初学者包](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies)中的 **SocialAndLocalAccounts** 策略文件包含 SignUpOrSignIn、ProfileEdit 和 PasswordReset 用户旅程。 可为其他方案添加更多的用户旅程，例如，更改电子邮件地址或链接和取消链接社交帐户。 

业务流程步骤可以调用[技术配置文件](technicalprofiles.md)。 技术配置文件提供带有内置机制的框架来与不同类型的参与方通信。 例如，技术配置文件可执行以下操作：

- 呈现用户体验。
- 允许用户使用 Facebook、Microsoft 帐户、Google、Salesforce 等社交或企业帐户或者其他任何标识提供者登录。
- 设置 MFA 的电话验证。
- 在 Azure AD B2C 标识存储中读取和写入数据。
- 调用自定义 Restful API 服务。

![策略执行](./media/trustframeworkpolicy/custom-policy-execution.png)

 **TrustFrameworkPolicy** 元素包含以下元素：

- 前面指定的 BasePolicy
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
