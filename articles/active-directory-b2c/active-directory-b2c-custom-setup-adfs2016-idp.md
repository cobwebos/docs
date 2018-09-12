---
title: 在 Azure Active Directory B2C 中使用自定义策略添加 ADFS 作为 SAML 标识提供者 | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用 SAML 协议和自定义策略设置 ADFS 2016
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669220"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略添加 ADFS 作为 SAML 标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何让 ADFS 用户帐户使用 Azure Active Directory (Azure AD) B2C 中的[自定义策略](active-directory-b2c-overview-custom.md)登录。

## <a name="prerequisites"></a>先决条件

完成[自定义策略入门](active-directory-b2c-get-started-custom.md)一文中的步骤。

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>将 ADFS 帐户应用程序密钥添加到 Azure AD B2C

与 ADFS 帐户的联合身份验证要求该帐户的客户端密码代表应用程序信任 Azure AD B2C。 需将 ADFS 证书存储在 Azure AD B2C 租户中。 

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 通过在 Azure 门户的右上角切换到包含 Azure AD B2C 租户的目录，确保你正在使用该目录。 选择“切换目录”，然后选择包含所创建的租户的目录。 在本教程中，使用包含名为 contoso0522Tenant.onmicrosoft.com 的租户的 contoso 目录。

    ![切换目录](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. 选择 Azure 门户左上角的“所有服务”，搜索并选择 **Azure AD B2C**。 你现在应使用你的租户。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，查看租户中的可用密钥，然后单击“添加”。
6. 选择“上载”作为选项。
7. 对于名称，请输入 `ADFSSamlCert`。 可能会自动添加前缀 `B2C_1A_`。
8. 浏览并选择带有私钥的证书 .pfx 文件。 此证书（带私钥）应与为 ADFS 信赖方颁发和供其使用的证书相同。
9. 单击“创建”并确认已创建 `B2C_1A_ADFSSamlCert` 密钥。

## <a name="add-a-claims-provider-in-your-extension-policy"></a>在扩展策略中添加声明提供程序

如果希望用户使用 ADFS 帐户登录，则需将该帐户定义为声明提供程序。 可以通过指定要与 Azure AD B2C 通信的终结点来执行此操作。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

通过在扩展策略文件中添加一个 ClaimsProvider 元素，将 ADFS 定义为声明提供程序。

1. 在工作目录中，打开 TrustFrameworkExtensions.xml 策略文件。 如果需要 XML 编辑器，请[尝试使用 Visual Studio Code](https://code.visualstudio.com/download)（一个轻型跨平台编辑器）。
2. 在 ClaimsProviders 元素下添加以下 XML，并将 your-ADFS-domain 替换为你的 ADFS 域名，并将 identityProvider 输出声明的值替换为你的 DNS（表示你的域的任意值），并保存文件。 

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
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
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

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>注册声明提供程序以进行注册和登录

若要在注册和登录页面中提供 ADFS 帐户身份提供程序，需要将其添加到 SignUpOrSignIn 用户旅程中。 

制作现有模板用户旅程的副本，然后对其进行修改，使其包含 ADFS 身份提供程序：

>[!NOTE]
>如果以前已将策略基本文件中的 UserJourneys 元素复制到扩展文件 (TrustFrameworkExtensions.xml)，则可以跳过本部分。

1. 打开策略的基文件。 例如 *TrustFrameworkBase.xml*。
2. 复制 UserJourneys 元素的整个内容。
3. 打开扩展文件 (TrustFrameworkExtensions.xml) 并粘贴扩展文件中复制的 UserJourneys 元素的整个内容。

### <a name="display-the-button"></a>显示按钮

ClaimsProviderSelections 元素定义声明提供程序选择的列表及其顺序。  ClaimsProviderSelection 元素类似于注册或登录页上的标识提供者按钮。 如果为 ADFS 帐户添加 ClaimsProviderSelection 元素，则当用户看到该页时，会显示一个新按钮。 添加此元素：

1. 在复制的用户旅程中标识符为 `SignUpOrSignIn` 的 UserJourney 元素中，找到 `Order="1"` 的 OrchestrationStep 元素。
2. 在 ClaimsProviderSelections 元素下添加以下 ClaimsProviderSelection 元素：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 ADFS 帐户通信以接收令牌。 可通过链接 ADFS 帐户声明提供程序的技术配置文件来将按钮链接到操作：

1. 在 UserJourney 元素下找到 `Order="2"` 的 OrchestrationStep。
2. 在 ClaimsExchanges 元素下添加以下 ClaimsExchange 元素：

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * 确保 `Id` 具有与前一部分中的 `TargetClaimsExchangeId` 相同的值。
> * 确保 `TechnicalProfileReferenceId` 设置为前面创建的技术配置文件 (Contoso-SAML2)。


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[可选] 注册配置文件编辑的声明提供程序

此外，可能需要将 ADFS 帐户标识提供者添加到配置文件编辑用户旅程。

### <a name="display-the-button"></a>显示按钮

1. 打开策略的扩展文件。 例如，TrustFrameworkExtensions.xml。
2. 在复制的用户旅程中标识符为 `ProfileEdit` 的 UserJourney 元素中，找到 `Order="1"` 的 OrchestrationStep 元素。
3. 在 ClaimsProviderSelections 元素下添加以下 ClaimsProviderSelection 元素：

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

1. 在 UserJourney 元素下找到 `Order="2"` 的 OrchestrationStep。
2. 在 ClaimsExchanges 元素下添加以下 ClaimsExchange 元素：

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>将策略上传到租户

1. 在 Azure 门户中，选择“所有策略”。
2. 选择“上传策略”。
3. 启用“覆盖策略(若存在)”。
4. 浏览并选择 TrustFrameworkExtensions.xml 策略文件，然后选择“上载”。 确保验证成功。


## <a name="configure-an-adfs-relying-party-trust"></a>配置 ADFS 信赖方信任

要将 ADFS 用作 Azure AD B2C 中的标识提供者，需要使用 Azure AD B2C SAML 元数据创建 ADFS 信赖方信任。 以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

替换以下值：

- 将 your-tenant 替换为你的租户名称，例如 your-tenant.onmicrosoft.com。
- 将 your-policy 替换为你的策略名称。 使用配置 SAML 提供者技术配置文件的策略或从该策略继承的策略。
- 将 your-technical-profile 替换为 SAML 标识提供者技术配置文件的名称。
 
打开浏览器并导航到此 URL。 确保键入正确的 URL 并且你有权访问 XML 元数据文件。

要通过使用 ADFS 管理管理单元添加新的依赖方信任并手动配置设置，请在联合服务器上执行以下过程。 本地计算机上“管理员”中的成员身份或同等身份是完成此过程所需的最低要求。 查看有关使用适当帐户和[本地和域默认组](http://go.microsoft.com/fwlink/?LinkId=83477)中组成员身份的详细信息。

1. 在“服务器管理器”中，选择“工具”，然后选择“ADFS 管理”。
2. 选择“添加信赖方信任”。
3. 在“欢迎”页上，选择“声明感知”，然后单击“启动”。
4. 在“选择数据源”页上，选择“导入有关依赖方在线或在本地网络上发布的数据”提供 Azure AD B2C 元数据 URL，然后单击“下一步”。
5. 在“指定显示名称”页上，输入一个“显示名称”，在“说明”下输入有关此信赖方信任的描述，然后单击“下一步”。
6. 在“选择访问控制策略”页上选择一个策略，然后单击“下一步”。
7. 在“准备添加信任”页上，查看设置，然后单击“下一步”来保存信赖方信任信息。
8. 在“完成”页上，单击“关闭”，此操作将自动显示“编辑声明规则”对话框。
9. 选择“添加规则”。  
10. 在“声明规则模板”中，选择“以声明方式发送 LDAP 特性”。
11. 提供“声明规则名称”。 有关“属性存储”，选择“选择 Active Directory”添加以下声明，然后单击“完成”和“确定”。

    ![设置规则属性](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  根据证书类型，可能需要设置哈希算法。 在信赖方信任（B2C 演示）属性窗口上，选择“高级”选项卡并将“安全哈希算法”更改为 `SHA-1` 或 `SHA-256`，单击“确定”。  

### <a name="update-the-relying-party-metadata"></a>更新信赖方元数据

更改 SAML 技术配置文件要求你使用更新的元数据版本更新 ADFS。 创建依赖方应用程序时不需要更新元数据，但在进行更改时，会更新 ADFS 中的元数据。

1. 在“服务器管理器”中，选择“工具”，然后选择“ADFS 管理”。
2. 选择所创建的信赖方信任，选择“从联合元数据更新”，然后单击“更新”。 

### <a name="test-the-policy-by-using-run-now"></a>使用“立即运行”测试策略

1.  打开“Azure AD B2C 设置”并转到“标识体验框架”。
2.  打开上传的信赖方 (RP) 自定义策略“B2C_1A_ProfileEdit”。 选择“立即运行”。 应能够使用 ADFS 帐户登录。

## <a name="download-the-complete-policy-files"></a>下载完整的策略文件

可选：可以在完成[“自定义策略入门”](active-directory-b2c-get-started-custom.md)中的步骤后，使用你自己的自定义策略文件来生成方案。 有关示例文件，请参阅[仅供参考的策略示例文件](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app)。
