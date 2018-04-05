---
title: Azure Active Directory B2C：使用自定义策略添加 ADFS 作为 SAML 标识提供者
description: 有关使用 SAML 协议和自定义策略设置 ADFS 2016 的操作指南文章
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: af102bbc3bc7608fe641db19f4af8c760907a564
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-add-adfs-as-a-saml-identity-provider-using-custom-policies"></a>Azure Active Directory B2C：使用自定义策略添加 ADFS 作为 SAML 标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文演示如何通过使用[自定义策略](active-directory-b2c-overview-custom.md)，使用户能够从 ADFS 帐户进行登录。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

这些步骤包括：

1.  创建 ADFS 信赖方信任。
2.  将 ADFS 信赖方信任证书添加到 Azure AD B2C。
3.  向策略添加声明提供程序。
4.  向用户旅程注册 ADFS 帐户声明提供程序。
5.  将策略上传到 Azure AD B2C 租户，并对其进行测试。

## <a name="to-create-a-claims-aware-relying-party-trust"></a>创建声明感知信赖方信任

要将 ADFS 用作 Azure Active Directory (Azure AD) B2C 中的标识提供者，需要创建 ADFS 信赖方信任并向其提供合适的参数。

要通过使用 AD FS 管理管理单元添加新的依赖方信任并手动配置设置，请在联合服务器上执行以下过程。

本地计算机上“管理员”中的成员身份或同等身份是完成此过程所需的最低要求。 查看有关使用适当帐户和[本地和域默认组](http://go.microsoft.com/fwlink/?LinkId=83477)中组成员身份的详细信息

1.  在“服务器管理器”中，单击“工具”，并选择“ADFS 管理”。

2.  单击“添加信赖方信任”。
    ![添加信赖方信任](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-1.png)

3.  在“欢迎”页上，选择“声明感知”，并单击“启动”。
    ![在“欢迎”页上，选择“声明感知”](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-2.png)
4.  在“选择数据源”页面上，单击“手动输入有关信赖方的数据”，然后单击“下一步”。
    ![输入有关信赖方的数据](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-3.png)

5.  在“指定显示名称”页上的“显示名称”中键入一个名称，在“说明”下键入有关此信赖方信任的描述，然后单击“下一步”。
    ![指定显示名称和说明](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-4.png)
6.  可选。 如果你拥有一个可选的令牌加密证书，那么在“配置证书”页上，单击“浏览”找到证书文件，然后单击“下一步”。
    ![配置证书](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-5.png)
7.  在“配置 URL”页上，选择“启用对 SAML 2.0 WebSSO 协议的支持”复选框。 在“信赖方 SAML 2.0 SSO 服务 URL”下，键入此信赖方信任的安全断言标记语言 (SAML) 服务终结点 URL，然后单击“下一步”。  对于“信赖方 SAML 2.0 SSO 服务 URL”，请粘贴 `https://login.microsoftonline.com/te/{tenant}.onmicrosoft.com/{policy}`。 将 {tenant} 替换为租户名称（例如，contosob2c.onmicrosoft.com），并将 {policy} 替换为扩展策略名称（例如，B2C_1A_TrustFrameworkExtensions）。
    > [!IMPORTANT]
    >策略名称是 signup_or_signin 策略继承自其中的名称，在这种情况下为：`B2C_1A_TrustFrameworkExtensions`。
    >例如 URL 可以是：https://login.microsoftonline.com/te/**contosob2c**.onmicrosoft.com/**B2C_1A_TrustFrameworkBase**。

    ![信赖方 SAML 2.0 SSO 服务 URL](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-6.png)
8. 在“配置标识符”页上，指定与上一步相同的 URL，单击“添加”以将其添加到列表，然后单击“下一步”。
    ![信赖方信任标识符](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-7.png)
9.  在“选择访问控制策略”上选择一个策略，然后单击“下一步”。
    ![选择访问控制策略](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-8.png)
10.  在“准备添加信任”页上，查看设置，然后单击“下一步”来保存信赖方信任信息。
    ![保存信赖方信任信息](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-9.png)
11.  在“完成”页上，单击“关闭”，此操作将自动显示“编辑声明规则”对话框。
    ![编辑声明规则](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-rp-10.png)
12. 单击“添加规则”。  
      ![添加新规则](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-1.png)
13.  在“声明规则模板”中，选择“以声明方式发送 LDAP 特性”。
    ![选择“以声明方式发送 LDAP 特性”模板规则](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-2.png)
14.  提供“声明规则名称”。 有关“属性存储”，选择“选择 Active Directory”添加以下声明，然后单击“完成”和“确定”。
    ![设置规则属性](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)
15.  在“服务器管理器”中，选择“信赖方信任”，然后选择你创建的信赖方信任并单击“属性”。
    ![信赖方编辑属性](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-1.png)
16.  在信赖方信任（B2C 演示）属性窗口上，单击“签名”选项卡并单击“添加”。  
    ![设置签名](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-2.png)
17.  添加签名证书（不带私钥的 .cert 文件）。  
    ![添加签名证书](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-3.png)
18.  在信赖方信任（B2C 演示）属性窗口上，单击“高级”选项卡并将“安全哈希算法”更改为“SHA-1”，单击“确定”。  
    ![将安全哈希算法设置为 SHA-1](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-sig-4.png)

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>将 ADFS 帐户应用程序密钥添加到 Azure AD B2C
与 ADFS 帐户的联合身份验证要求 ADFS 帐户的客户端密码代表应用程序信任 Azure AD B2C。 需将 ADFS 证书存储在 Azure AD B2C 租户中。 

1.  转到 Azure AD B2C 租户，然后选择“B2C 设置” > “标识体验框架”
2.  选择“策略密钥”，查看租户中的可用密钥。
3.  单击“+添加”。
4.  对于“选项”，使用“上传”。
5.  使用 `ADFSSamlCert` 作为“名称”。  
    可能会自动添加前缀 `B2C_1A_`。
6.  在“文件上传”中，**选择带有私钥的证书 .pfx 文件。 注意：此证书（带私钥）应与为 ADFS 信赖方颁发和供其使用的证书相同。
![上传策略密钥](media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-cert.png)
7.  单击“创建” 
8.  确认已创建密钥 `B2C_1A_ADFSSamlCert`。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>在扩展策略中添加声明提供程序
如果要允许用户使用 ADFS 帐户登录，则需将 ADFS 帐户定义为声明提供程序。 换言之，需指定要与 Azure AD B2C 通信的终结点。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 `<ClaimsProvider>` 节点，将 ADFS 定义为声明提供程序：

1. 从工作目录打开扩展策略文件 (TrustFrameworkExtensions.xml)。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。
2. 找到 `<ClaimsProviders>` 节
3. 将下面的 XML 代码段添加到 `ClaimsProviders` 元素下，使用你的 DNS（指示域的任意值）替换 `identityProvider`，然后保存文件。 

```xml
<ClaimsProvider>
    <Domain>contoso.com</Domain>
    <DisplayName>Contoso ADFS</DisplayName>
    <TechnicalProfiles>
    <TechnicalProfile Id="Contoso-SAML2">
        <DisplayName>Contoso ADFS</DisplayName>
        <Description>Login with your Contoso account</Description>
        <Protocol Name="SAML2"/>
        <Metadata>
        <Item Key="RequestsSigned">false</Item>
        <Item Key="WantsEncryptedAssertions">false</Item>
        <Item Key="PartnerEntity">https://{your_ADFS_domain}/federationmetadata/2007-06/federationmetadata.xml</Item>
        </Metadata>
        <CryptographicKeys>
        <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
        </CryptographicKeys>
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
        <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
        <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
        </OutputClaims>
        <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
        </OutputClaimsTransformations>
        <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
    </TechnicalProfile>
    </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="register-the-adfs-account-claims-provider-to-sign-up-or-sign-in-user-journey"></a>注册 ADFS 帐户声明提供程序以注册或登录用户旅程
至此，已设置标识提供者。  但是，它不能在任何注册/登录屏幕中使用。 现在需要将 ADFS 帐户标识提供者添加到用户 `SignUpOrSignIn` 用户旅程。 为使其可用，需创建现有模板用户旅程的副本。  然后对其进行修改，以便它包含 ADFS 标识提供者：

>[!NOTE]
>如果以前已将策略基本文件中的 `<UserJourneys>` 元素复制到扩展文件 (TrustFrameworkExtensions.xml)，则可以跳过本部分。

1.  打开策略的基文件（例如 TrustFrameworkBase.xml）。
2.  找到 `<UserJourneys>` 元素并复制整个 `<UserJourneys>` 节点的内容。
3.  打开扩展文件（例如 TrustFrameworkExtensions.xml）并找到 `<UserJourneys>` 元素。 如果该元素不存在，请添加一个。
4.  将复制的整个 `<UserJournesy>` 节点的内容粘贴为 `<UserJourneys>` 元素的子级。

### <a name="display-the-button"></a>显示按钮
`<ClaimsProviderSelections>` 元素定义声明提供程序选择选项的列表及其顺序。  `<ClaimsProviderSelection>` 元素类似于注册/登录页上的标识提供者按钮。 如果为 ADFS 帐户添加 `<ClaimsProviderSelection>` 元素，则当用户进入页面时，会显示一个新按钮。 添加此元素：

1.  在刚刚复制的用户旅程中找到包含 `Id="SignUpOrSignIn"` 的 `<UserJourney>` 节点。
2.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
3.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```
### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 ADFS 帐户通信以接收令牌。 可通过链接 ADFS 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

> [!NOTE]
> * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值。
> * 确保 `TechnicalProfileReferenceId` 设置为前面创建的技术配置文件 (Contoso-SAML2)。

## <a name="upload-the-policy-to-your-tenant"></a>将策略上传到租户
1.  在 [Azure 门户](https://portal.azure.com) 中，切换到 [Azure AD B2C 租户的上下文](active-directory-b2c-navigate-to-b2c-context.md) ，然后打开“Azure AD B2C”边栏选项卡。
2.  选择“标识体验框架”。
3.  打开“所有策略”边栏选项卡。
4.  选择“上传策略”。
5.  选中“覆盖策略(若存在)”框。
6.  上传 TrustFrameworkExtensions.xml，并确保它能够通过验证

## <a name="test-the-custom-policy-by-using-run-now"></a>使用“立即运行”测试自定义策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_signup_signin”。 选择“立即运行”。
3.  应能够使用 ADFS 帐户登录。

## <a name="optional-register-the-adfs-account-claims-provider-to-profile-edit-user-journey"></a>[可选]向配置文件编辑用户旅程注册 ADFS 帐户声明提供程序
此外，你可能需要将 ADFS 帐户标识提供者添加到用户 `ProfileEdit` 用户旅程。 为使其可用，我们将重复最后两个步骤：

### <a name="display-the-button"></a>显示按钮
1.  打开策略的扩展文件（例如 TrustFrameworkExtensions.xml）。
2.  在刚刚复制的用户旅程中找到包含 `Id="ProfileEdit"` 的 `<UserJourney>` 节点。
3.  找到包含 `Order="1"` 的 `<OrchestrationStep>` 节点
4.  将下面的 XML 代码段添加到 `<ClaimsProviderSelections>` 节点下：

```xml
<ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作
1.  在 `<UserJourney>` 节点中找到包含 `Order="2"` 的 `<OrchestrationStep>`。
2.  将下面的 XML 代码段添加到 `<ClaimsExchanges>` 节点下：

```xml
<ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
```

### <a name="test-the-custom-profile-edit-policy-by-using-run-now"></a>使用“立即运行”测试自定义配置文件编辑策略
1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_ProfileEdit”。 选择“立即运行”。
3.  应能够使用 ADFS 帐户登录。

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件
可选：建议在完成“自定义策略入门”演练后，使用你自己的自定义策略文件来构建方案。 [仅供参考的策略示例文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)
