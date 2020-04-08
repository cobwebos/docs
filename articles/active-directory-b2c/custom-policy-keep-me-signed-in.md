---
title: 在 Azure Active Directory B2C 中使我保持登录状态
description: 了解如何在 Azure Active Directory B2C 中设置“使我保持登录状态 (KMSI)”。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 041fb8d881307b52fb170a11618f930debc522a4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80803154"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用“使我保持登录状态 (KMSI)”

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

你可以为在 Azure Active Directory B2C (Azure AD B2C) 目录中拥有本地帐户的 Web 和本机应用程序的用户启用“使我保持登录状态 (KMSI)”功能。 此功能会向返回到应用程序的用户授予访问权限，而不会提示他们重新输入用户名和密码。 当用户注销时，会撤销此访问权限。

用户不应在公用计算机上启用此选项。

![显示“使我保持登录状态”复选框的示例注册登录页](./media/custom-policy-keep-me-signed-in/kmsi.PNG)

## <a name="prerequisites"></a>先决条件

- 配置为允许本地帐户登录的 Azure AD B2C 租户。 外部标识提供者帐户不支持 KMSI。
- 完成[自定义策略入门](custom-policy-get-started.md)中的步骤。

## <a name="configure-the-page-identifier"></a>配置页面标识符

要启用 KMSI，将内容定义`DataUri`元素设置为[页面标识符](contentdefinitions.md#datauri)`unifiedssp`和[页面版本](page-layout.md) *1.1.0*或以上。

1. 打开策略的扩展文件。 例如， <em> `SocialAndLocalAccounts/` </em>. 此扩展文件是自定义策略初学者包中包含的策略文件之一，您应该在先决条件中获取，[从自定义策略开始](custom-policy-get-started.md)。
1. 搜索 BuildingBlocks**** 元素。 如果该元素不存在，请添加该元素。
1. 将**内容定义**元素添加到策略的**构建块**元素。

    自定义策略应类似于以下代码段：

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>将元数据添加到自断言技术配置文件

要将 KMSI 复选框添加到注册和登录页面，请将`setting.enableRememberMe`元数据设置为 true。 覆盖扩展文件中的自断言-本地帐户Signin-电子邮件技术配置文件。

1. 找到 ClaimsProviders 元素。 如果该元素不存在，请添加该元素。
1. 将以下声明提供程序添加到声明提供程序元素：

```XML
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 保存扩展文件。

## <a name="configure-a-relying-party-file"></a>配置依赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 打开自定义策略文件。 例如，SignUpOrSignin.xml**。
1. 如果不存在，请向`<UserJourneyBehaviors>``<RelyingParty>`节点添加子节点。 它必须立即位于`<DefaultUserJourney ReferenceId="User journey Id" />`之后，例如： `<DefaultUserJourney ReferenceId="SignUpOrSignIn" />`。
1. 将以下节点添加为 `<UserJourneyBehaviors>` 元素的子级。

    ```XML
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

    - **会话过期类型**- 指示如何在 和`SessionExpiryInSeconds``KeepAliveInDays`中指定的时间延长会话。 该`Rolling`值（默认值）表示每次用户执行身份验证时都会扩展会话。 该`Absolute`值指示用户在指定的时间段后强制重新进行身份验证。

    - **会话过期时间**-*保持登录*时会话 Cookie 的生存期未启用，或者如果用户未选择*保持我登录*。 会话在通过后`SessionExpiryInSeconds`过期，或者浏览器关闭。

    - **保持AliveInDays** -*保持登录*时会话 Cookie 的生存期已启用，用户选择*保持我登录*。  的值`KeepAliveInDays`优先于`SessionExpiryInSeconds`值，并指示会话到期时间。 如果用户关闭浏览器并在以后重新打开浏览器，则只要浏览器处于"保持 AliveInDays"时间段内，他们仍然可以静默登录。

    有关详细信息，请参阅[用户旅程行为](relyingparty.md#userjourneybehaviors)。

我们建议您将 Session 过期的"秒"值设置为短时间（1200 秒），而 KeepAliveInDays 的值可以设置为相对长的周期（30 天），如以下示例所示：

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

## <a name="test-your-policy"></a>测试策略

1. 保存更改，然后上传文件。
1. 要测试您上传的自定义策略，请在 Azure 门户中转到策略页，然后选择 **"立即运行**"。
1. 键入您的**用户名和密码**，**password**选择 **"保持我登录**"，然后单击 **"登录**"。
1. 返回到 Azure 门户。 转到策略页，然后选择 **"复制"** 以复制登录 URL。
1. 在浏览器地址栏中，删除`&prompt=login`查询字符串参数，该参数强制用户在该请求上输入其凭据。
1. 在浏览器中，单击"**转到**"。 现在，Azure AD B2C 将发出访问令牌，而不会提示您再次登录。 

## <a name="next-steps"></a>后续步骤

[在此处](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)查找示例策略。
