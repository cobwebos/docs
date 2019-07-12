---
title: 在 Azure Active Directory B2C 中使我保持登录状态 | Microsoft Docs
description: 了解如何在 Azure Active Directory B2C 中设置“使我保持登录状态 (KMSI)”。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: e99dacbe7ae0f42919616e04e60bf4f21b9bd985
ms.sourcegitcommit: 64798b4f722623ea2bb53b374fb95e8d2b679318
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67835370"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中启用“使我保持登录状态 (KMSI)”

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

可在 Azure Active Directory (Azure AD) B2C 中为 Web 和本机应用程序启用“使我保持登录状态 (KMSI)”功能。 此功能授予相应的访问权限，在不提示用户重新输入其用户名和密码的情况下，将用户返回到应用程序。 当用户注销时，会撤销此访问权限。

用户不应在公用计算机上启用此选项。

![示例注册登录页中可以看出，保持登录状态复选框](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>先决条件

配置为允许本地帐户注册和登录的 Azure AD B2C 租户。 如果没有租户，可按照[教程：创建 Azure Active Directory B2C 租户](tutorial-create-tenant.md)中的步骤创建一个。

## <a name="add-a-content-definition-element"></a>添加内容定义元素

在扩展文件的 **BuildingBlocks** 元素下，添加 **ContentDefinitions** 元素。

1. 在 **ContentDefinitions** 元素下，添加标识符为 `api.signuporsigninwithkmsi` 的 **ContentDefinition** 元素。
2. 在 **ContentDefinition** 元素下，添加 **LoadUri**、**RecoveryUri** 和 **DataUri** 元素。 **DataUri** 元素的 `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` 值是机器可识别的标识符，它会在登录页中显示一个 KMSI 复选框。 不得更改此值。

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>添加本地帐户的登录声明提供程序

可在策略的扩展文件中，使用 **ClaimsProvider** 元素将本地帐户登录定义为声明提供程序：

1. 从工作目录打开 *TrustFrameworkExtensions.xml* 文件。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下面添加。 [初学者包](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)包含一个本地帐户登录声明提供程序。
3. 按以下示例中所示，添加包含 **DisplayName** 和 **TechnicalProfile** 的 **ClaimsProvider** 元素：

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>将应用程序标识符添加到自定义策略

将应用程序标识符添加到 *TrustFrameworkExtensions.xml* 文件。

1. 在 *TrustFrameworkExtensions.xml* 文件中，找到包含标识符 `login-NonInteractive` 的 **TechnicalProfile** 元素，以及包含标识符 `login-NonInteractive-PasswordChange` 的 **TechnicalProfile** 元素，并根据[入门](active-directory-b2c-get-started-custom.md)中所述，将所有 `IdentityExperienceFrameworkAppId` 值替换为标识体验框架应用程序的应用程序标识符。

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. 根据[入门](active-directory-b2c-get-started-custom.md)中所述，将 `ProxyIdentityExperienceFrameworkAppId` 的所有值替换为代理标识体验框架应用程序的应用程序标识符。
3. 保存扩展文件。

## <a name="create-a-kmsi-enabled-user-journey"></a>创建已启用 KMSI 的用户旅程

将本地帐户的登录声明提供程序添加到用户旅程。

1. 打开策略的基文件。 例如 *TrustFrameworkBase.xml*。
2. 找到 **UserJourneys** 元素，并复制使用标识符 `SignUpOrSignIn` 的 **UserJourney** 元素的整个内容。
3. 打开扩展文件， 例如 *TrustFrameworkExtensions.xml*，并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的整个 **UserJourney** 元素粘贴为 **UserJourneys** 元素的子级。
5. 更改新用户旅程的标识符值。 例如， `SignUpOrSignInWithKmsi` 。
6. 最后，在第一个业务流程步骤中，将 **ContentDefinitionReferenceId** 的值更改为 `api.signuporsigninwithkmsi`。 设置此值会启用用户旅程中的复选框。
7. 保存并上传扩展文件，并确保所有验证成功。

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>创建信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 文件的副本，并将其重命名。 例如 *SignUpOrSignInWithKmsi.xml*。
2. 打开新文件，并使用唯一的值更新 **TrustFrameworkPolicy** 的 **PolicyId** 属性。 这是策略的名称。 例如， `SignUpOrSignInWithKmsi` 。
3. 更改 **DefaultUserJourney** 元素的 **ReferenceId** 属性，以匹配创建的新用户旅程的标识符。 例如， `SignUpOrSignInWithKmsi` 。

    使用 UserJourneyBehaviors  元素并将 SingleSignOn  SessionExpiryType  和 SessionExpiryInSeconds  作为其第一个子元素，来配置 KMSI。 **KeepAliveInDays** 属性控制用户保持登录状态的时间。 在以下示例中，KMSI 会话在 `7` 天后自动过期，不管用户执行无提示身份验证的频率有多大。 将 **KeepAliveInDays** 值设置为 `0` 会禁用 KMSI 功能。 此值默认为 `0`。 如果 **SessionExpiryType** 的值为 `Rolling`，则每当用户执行无提示身份验证时，KMSI 会话会延长 `7` 天。  如果选择了 `Rolling`，应保持最小的天数。

    **SessionExpiryInSeconds** 的值表示 SSO 会话的过期时间。 Azure AD B2C 在内部使用此值来检查 KMSI 的会话是否已过期。 **KeepAliveInDays** 的值确定 Web 浏览器中 SSO Cookie 的 Expires/Max-Age 值。 与 **SessionExpiryInSeconds** 不同，**KeepAliveInDays** 用于防止在关闭浏览器时清除 Cookie。 仅当 SSO 会话 Cookie 存在（由 KeepAliveInDays 控制）且未过期（由 SessionExpiryInSeconds 控制）时，用户才能静默登录   。

    如果用户未在注册和登录页面上启用“使我保持登录”，则超过 SessionExpiryInSeconds 指定的时间后或关闭浏览器后，会话将过期   。 如果用户启用“使我保持登录”，KeepAliveInDays 的值将覆盖 SessionExpiryInSeconds 的值并指定会话过期时间    。 即使用户关闭浏览器后再重新打开，只要在 **KeepAliveInDays** 的时间内，用户仍然可以静默登录。 建议将 SessionExpiryInSeconds 的值设置为较短时间段（1200 秒），而将 KeepAliveInDays 的值设置为较长时间段（7 天），如下例所示   ：

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
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
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. 保存更改，然后上传文件。
5. 若要测试所上传的自定义策略，请在 Azure 门户中转到策略页，并选择“立即运行”  。

可在[此处](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)找到示例策略。








