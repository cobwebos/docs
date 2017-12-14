---
title: "Azure Active Directory B2C：KMSI | Microsoft Docs"
description: "演示如何设置“使我保持登录状态”的主题"
services: active-directory-b2c
documentationcenter: 
author: vigunase
manager: mtillman
ms.assetid: 926e9711-71c0-49e8-b658-146ffb7386c0
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2016
ms.author: vigunase
ms.openlocfilehash: a3d78945f862d1ae12cec05da0cf0ea7df511f43
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/11/2017
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C：启用“使我保持登录状态 (KMSI)”  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C 现在允许 Web 和本机应用程序启用“使我保持登录状态 (KMSI)”功能。 此功能可授予相应的访问权限，在不提示用户重新输入用户名和密码的情况下，将用户返回到应用程序。 当用户注销时，会吊销此访问权限。 

我们不建议用户在公用计算机上选中此选项。 

![img](images/kmsi.PNG)


## <a name="prerequisites"></a>先决条件

已根据[入门](active-directory-b2c-get-started-custom.md)中所述，配置了一个 Azure AD B2C 租户用于完成本地帐户注册/登录。

## <a name="how-to-enable-kmsi"></a>如何启用 KMSI

在信任框架扩展策略中进行以下更改。

## <a name="adding-a-content-definition-element"></a>添加内容定义元素 

扩展文件的 `BuildingBlocks` 节点必须包含 `ContentDefinitions` 元素。 

1. 在 `ContentDefinitions` 节中，定义 ID 为 `api.signuporsigninwithkmsi` 的新 `ContentDefinition`。
2. 新 `ContentDefinition` 必须包含 `LoadUri`、`RecoveryUri` 和 `DataUri`，如下所示。
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` 机器可识别的标识符，它会在登录页中显示一个 KMSI 复选框。 切勿更改此值。 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>添加本地帐户登录声明提供程序 

可在策略扩展文件的 `<ClaimsProvider>` 节点中，将“本地帐户登录”定义为声明提供程序：

1. 从工作目录打开扩展文件 (TrustFrameworkExtensions.xml)。 
2. 找到 `<ClaimsProviders>` 节。 如果该节不存在，请在根节点的下面添加它。
3. [入门](active-directory-b2c-get-started-custom.md)中提供的初学者包随附了“本地帐户登录”声明提供程序。 
4. 如果没有，请按如下所示添加新的 `<ClaimsProvider>` 节点：

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
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>将应用程序 ID 添加到自定义策略

将应用程序 ID 添加到扩展文件 (`TrustFrameworkExtensions.xml`)：

1. 在扩展文件 (TrustFrameworkExtensions.xml) 中，找到元素 `<TechnicalProfile Id="login-NonInteractive">` 和 `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. 将 `IdentityExperienceFrameworkAppId` 的所有实例替换为[入门](active-directory-b2c-get-started-custom.md)中所述的标识体验框架应用程序的应用程序 ID。 下面是一个示例：

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. 将 `ProxyIdentityExperienceFrameworkAppId` 的所有实例替换为[入门](active-directory-b2c-get-started-custom.md)中所述的代理标识体验框架应用程序的应用程序 ID。

4. 保存扩展文件。

## <a name="create-a-kmsi-in-enabled-user-journey"></a>在启用的用户旅程中创建 KMSI

现在，需将“本地帐户登录”声明提供方添加到用户旅程。 

1. 打开策略的基文件（例如 TrustFrameworkBase.xml）。
2. 找到 `<UserJourneys>` 元素并复制包含 `Id="SignUpOrSignIn"` 的整个 `<UserJourney>`。
3. 打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4. 将复制的整个 `<UserJourney>` 节点粘贴为 `<UserJourneys>` 元素的子级。
5. 重命名新用户旅程的 ID（例如，重命名为 `SignUpOrSignInWithKmsi`）。
6. 最后，在 `OrchestrationStep 1` 中，将 `ContentDefinitionReferenceId` 更改为在前面步骤中定义的 `api.signuporsigninwithkmsi`。 这样，便会在用户旅程中启用相应的复选框。 
7. 现已完成修改扩展文件。 保存并上传次文件。 确保所有验证都成功。

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

## <a name="create-a-relying-party-rp-file"></a>创建信赖方 (RP) 文件

接下来，更新用于启动创建的用户旅程的信赖方 (RP) 文件：

1. 在工作目录中创建 SignUpOrSignIn.xml 的副本。 然后，将此文件重命名（例如 SignUpOrSignInWithKmsi.xml）。

2. 打开新文件，并使用唯一值更新 `<TrustFrameworkPolicy>` 的 `PolicyId` 属性。 这是策略的名称（例如 SignUpOrSignInWithKmsi）。

3. 修改 `<DefaultUserJourney>` 中的 `ReferenceId` 属性，使其与创建的新用户旅程的 `Id` 匹配（例如 SignUpOrSignInWithKmsi）。

4. KMSI 在 `UserJourneyBehaviors` 中配置。 

5. **`KeepAliveInDays`** 控制用户保持登录状态的时间长短。 在以下示例中，KMSI 会话在 14 天后自动过期，不管用户执行无提示身份验证的频率有多大。

   将 `KeepAliveInDays` 值设置为 0 会关闭 KMSI 功能。 默认情况下，此值为 0

6. 如果 **`SessionExpiryType`** 为 *Rolling*，则每次用户执行无提示身份验证后， KMSI 会话会延期 14 天。  如果选择了 *Rolling*，我们建议保持最小的天数。 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
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

7. 保存更改，然后上传文件。

8. 若要测试所上传的自定义策略，请在 Azure 门户中转到策略边栏选项卡，并单击“立即运行”。


## <a name="link-to-sample-policy"></a>示例策略的链接

可在[此处](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)找到示例策略。








