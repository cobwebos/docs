---
title: 使我保持登录 Azure Active Directory B2C
description: 了解如何在 Azure Active Directory B2C 中设置“使我保持登录状态 (KMSI)”。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/27/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 84ba68c97f69872e39121915a6edf23aa029fa75
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "78161680"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用“使我保持登录状态 (KMSI)”

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

对于在 Azure Active Directory B2C （Azure AD B2C）目录中具有本地帐户的 web 和本机应用程序的用户，可以启用 "使我保持登录（KMSI）" 功能。 此功能为返回到应用程序的用户授予访问权限，而不会提示用户重新输入其用户名和密码。 当用户注销时，会撤销此访问权限。

用户不应在公用计算机上启用此选项。

![显示 "使我保持登录状态" 复选框的示例注册登录页](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>必备条件

- 配置为允许本地帐户登录的 Azure AD B2C 租户。 外部标识提供者帐户不支持 KMSI。
- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。

## <a name="configure-the-page-identifier"></a>配置页面标识符 

若要启用 KMSI，请将内容定义 `DataUri` 元素设置为[页标识符](contentdefinitions.md#datauri)`unifiedssp` 和[页版本](page-layout.md) *1.1.0*或更高版本。

1. 打开策略的扩展文件。 例如， <em>`SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** </em> 。 此扩展文件是自定义策略初学者包中包含的策略文件之一，你应该已在先决条件中获取[自定义策略入门](custom-policy-get-started.md)。
1. 搜索 BuildingBlocks 元素。 如果该元素不存在，请添加该元素。
1. 将**ContentDefinitions**元素添加到策略的**BuildingBlocks**元素中。

    自定义策略应类似于以下代码片段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```
    
1. 保存扩展文件。



## <a name="configure-a-relying-party-file"></a>配置信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml。
1. 如果它尚不存在，请将 `<UserJourneyBehaviors>` 子节点添加到 `<RelyingParty>` 节点。 它必须立即位于 `<DefaultUserJourney ReferenceId="User journey Id" />`之后，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **Ssosession** -指示会话在 `SessionExpiryInSeconds` 和 `KeepAliveInDays`中指定的时间的扩展方式。 "`Rolling` 值" （默认值）指示每次用户执行身份验证时会话都将扩展。 `Absolute` 值指示用户在指定的时间段后被迫重新进行身份验证。
 
    - **SessionExpiryInSeconds** -未启用 "*使我保持登录*状态" 时会话 cookie 的生存期，或用户未选择 "*使我保持登录*状态"。 会话在 `SessionExpiryInSeconds` 过去之后过期，或浏览器已关闭。
 
    - **KeepAliveInDays** -启用 "*使我保持登录*状态" 时会话 cookie 的生存期，用户选择 "*使我保持登录*状态"。  `KeepAliveInDays` 的值优先于 `SessionExpiryInSeconds` 值，并指示会话到期时间。 如果用户关闭浏览器并稍后重新打开它，则只要它在 KeepAliveInDays 时间段内，它们仍可无提示登录。
    
    有关详细信息，请参阅[用户旅程行为](relyingparty.md#userjourneybehaviors)。
 
建议将 SessionExpiryInSeconds 的值设置为短时间（1200秒），而 KeepAliveInDays 的值可以设置为相对较长的时间（30天），如下面的示例中所示：

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
    <SessionExpiryType>Absolute</SessionExpiryType>
    <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
  </UserJourneyBehaviors>
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
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

4. 保存更改，然后上传文件。
5. 若要测试所上传的自定义策略，请在 Azure 门户中转到策略页，并选择“立即运行”。

可在[此处](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)找到示例策略。
