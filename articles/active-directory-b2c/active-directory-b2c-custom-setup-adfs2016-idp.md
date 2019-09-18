---
title: 在 Azure Active Directory B2C 中使用自定义策略添加 ADFS 作为 SAML 标识提供者 | Microsoft Docs
description: 在 Azure Active Directory B2C 中使用 SAML 协议和自定义策略设置 ADFS 2016
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/07/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 99d21e773fee59780d82004eaa7213598fe7c8bb
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066142"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>在 Azure Active Directory B2C 中使用自定义策略添加 ADFS 作为 SAML 标识提供者

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

本文介绍如何使用 Azure Active Directory B2C （Azure AD B2C）中的[自定义策略](active-directory-b2c-overview-custom.md)来启用 ADFS 用户帐户登录。 可以通过将 [SAML 技术配置文件](saml-technical-profile.md)添加到自定义策略来实现登录。

## <a name="prerequisites"></a>先决条件

- 完成 [Azure Active Directory B2C 中的自定义策略入门](active-directory-b2c-get-started-custom.md)中的步骤。
- 请确保你有权访问包含私钥的证书 .pfx 文件。 可以生成自己的签名证书并将其上传到 Azure AD B2C。 Azure AD B2C 使用此证书对发送到 SAML 标识提供者的 SAML 请求进行签名。
- 为了使 Azure 接受 .pfx 文件密码，必须通过 Windows 证书存储导出实用工具中的 TripleDES-SHA1 选项（而不是 AES256-SHA256）对密码进行加密。

## <a name="create-a-policy-key"></a>创建策略密钥

需要将你的证书存储在 Azure AD B2C 租户中。

1. 登录到 [Azure 门户](https://portal.azure.com/)。
2. 请确保使用的是包含 Azure AD B2C 租户的目录。 在顶部菜单中选择 "**目录 + 订阅**" 筛选器，然后选择包含你的租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 在“概述”页上选择“标识体验框架”。
5. 选择“策略密钥”，然后选择“添加”。
6. 对于“选项”，请选择 `Upload`。
7. 输入策略密钥的**名称**。 例如， `SamlCert` 。 前缀 `B2C_1A_` 会自动添加到密钥名称。
8. 浏览并选择带有私钥的证书 .pfx 文件。
9. 单击“创建”。

## <a name="add-a-claims-provider"></a>添加声明提供程序

如果希望用户使用 ADFS 帐户登录，需将该帐户定义为 Azure AD B2C 可通过终结点与其进行通信的声明提供程序。 该终结点将提供一组声明，Azure AD B2C 使用这些声明来验证特定的用户是否已完成身份验证。

可以通过在策略的扩展文件中将 ADFS 帐户添加到 **ClaimsProvider** 元素，将该帐户定义为声明提供程序。

1. 打开 *TrustFrameworkExtensions.xml*。
2. 找到 **ClaimsProviders** 元素。 如果该元素不存在，请在根元素下添加它。
3. 如下所示添加新的 **ClaimsProvider**：

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your ADFS account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
            <Item Key="XmlSignatureAlgorithm">Sha256</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="userPrincipalName" />
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

4. 将 `your-ADFS-domain` 替换为你的 ADFS 域的名称，将 **identityProvider** 输出声明的值替换为你的 DNS（表示你的域的任意值）。
5. 保存该文件。

### <a name="upload-the-extension-file-for-verification"></a>上传扩展文件以进行验证

现在，你已配置了策略，因此 Azure AD B2C 知道如何与 ADFS 帐户进行通信。 请尝试上传该策略的扩展文件，这只是为了确认它到目前为止不会出现任何问题。

1. 在 Azure AD B2C 租户中的“自定义策略”页上，选择“上传策略”。
2. 启用“覆盖策略(若存在)”，然后浏览到 *TrustFrameworkExtensions.xml* 文件并选中该文件。
3. 单击“上传” 。

> [!NOTE]
> Visual Studio code B2C 扩展使用 "socialIdpUserId"。 ADFS 还需要社交策略。
>

## <a name="register-the-claims-provider"></a>注册声明提供程序

此时，标识提供者已设置，但不会出现在任何注册或登录屏幕中。 若要使其可用，需要创建现有模板用户旅程的副本，并对其进行修改，使其具有 ADFS 标识提供者。

1. 打开初学者包中的 *TrustFrameworkBase.xml* 文件。
2. 找到并复制包含 `Id="SignUpOrSignIn"` 的 **UserJourney** 元素的完整内容。
3. 打开 *TrustFrameworkExtensions.xml* 并找到 **UserJourneys** 元素。 如果该元素不存在，请添加一个。
4. 将复制的 **UserJourney** 元素的完整内容粘贴为 **UserJourneys** 元素的子级。
5. 重命名用户旅程的 ID。 例如， `SignUpSignInADFS` 。

### <a name="display-the-button"></a>显示按钮

**ClaimsProviderSelection** 元素类似于注册或登录屏幕上的标识提供者按钮。 如果为 ADFS 帐户添加 **ClaimsProviderSelection** 元素，则当用户进入页面时，会显示一个新按钮。

1. 在创建的用户旅程中找到包含 `Order="1"` 的 **OrchestrationStep** 元素。
2. 在 **ClaimsProviderSelections** 下，添加以下元素。 将 **TargetClaimsExchangeId** 设置为适当的值，例如 `ContosoExchange`：

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>将按钮链接到操作

准备好按钮后，需将它链接到某个操作。 在本例中，Azure AD B2C 使用该操作来与 ADFS 帐户通信以接收令牌。

1. 在用户旅程中找到包含 `Order="2"` 的 **OrchestrationStep**。
2. 添加以下 **ClaimsExchange** 元素，确保在 ID 和 **TargetClaimsExchangeId** 处使用相同的值：

    ```XML
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

    将 **TechnicalProfileReferenceId** 的值更新为先前创建的技术配置文件的 ID。 例如， `Contoso-SAML2` 。

3. 保存 *TrustFrameworkExtensions.xml* 文件，并再次上传以进行验证。


## <a name="configure-an-adfs-relying-party-trust"></a>配置 ADFS 信赖方信任

要将 ADFS 用作 Azure AD B2C 中的标识提供者，需要使用 Azure AD B2C SAML 元数据创建 ADFS 信赖方信任。 以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

替换以下值：

- 将 your-tenant 替换为你的租户名称，例如 your-tenant.onmicrosoft.com。
- 将 your-policy 替换为你的策略名称。 例如，B2C_1A_signup_signin_adfs。
- **你的技术配置文件**，其中包含 SAML 标识提供者技术配置文件的名称。 例如，Contoso-SAML2。

打开浏览器并导航到此 URL。 确保键入正确的 URL 并且你有权访问 XML 元数据文件。 要通过使用 ADFS 管理管理单元添加新的依赖方信任并手动配置设置，请在联合服务器上执行以下过程。 本地计算机上“管理员”中的成员身份或同等身份是完成此过程所需的最低要求。

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

    | LDAP 特性 | 传出声明类型 |
    | -------------- | ------------------- |
    | User-Principal-Name | userPrincipalName |
    | 姓氏 | family_name |
    | Given-Name | given_name |
    | E-Mail-Address | email |
    | Display-Name | name |

    请注意，这些名称将不会显示在 "传出声明类型" 下拉列表中。 需要在中手动键入。 （下拉列表实际上可编辑）。

12.  根据证书类型，可能需要设置哈希算法。 在信赖方信任（B2C 演示）属性窗口上，选择“高级”选项卡并将“安全哈希算法”更改为 `SHA-256`，然后单击“确定”。
13. 在“服务器管理器”中，选择“工具”，然后选择“ADFS 管理”。
14. 选择所创建的信赖方信任，选择“从联合元数据更新”，然后单击“更新”。

## <a name="create-an-azure-ad-b2c-application"></a>创建 Azure AD B2C 应用程序

通过在租户中创建的应用程序与 Azure AD B2C 进行通信。 本部分列出了可用于创建测试应用程序的可选步骤（如果尚未创建）。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 请确保使用包含 Azure AD B2C 租户的目录，方法是选择顶部菜单中的“目录 + 订阅”筛选器，然后选择包含租户的目录。
3. 选择 Azure 门户左上角的“所有服务”，然后搜索并选择“Azure AD B2C”。
4. 选择“应用程序”，然后选择“添加”。
5. 输入应用程序的名称，例如 *testapp1*。
6. 对于“Web 应用/Web API”，请选择 `Yes`，然后为“回复 URL”输入 `https://jwt.ms`。
7. 单击“创建”。

### <a name="update-and-test-the-relying-party-file"></a>更新和测试信赖方文件

更新用于启动创建的用户旅程的信赖方 (RP) 文件。

1. 在工作目录中创建 *SignUpOrSignIn.xml* 的副本并将其重命名。 例如，将其重命名为 *SignUpSignInADFS.xml*。
2. 打开新文件，并将 **TrustFrameworkPolicy** 的 **PolicyId** 属性的值更新为唯一的值。 例如， `SignUpSignInADFS` 。
3. 将 **PublicPolicyUri** 的值更新为策略的 URI。 例如 `http://contoso.com/B2C_1A_signup_signin_adfs`
4. 更新 **DefaultUserJourney** 中的 **ReferenceId** 属性的值，以匹配所创建的新用户旅程的 ID (SignUpSignInADFS)。
5. 保存更改并上传文件，然后选择列表中的新策略。
6. 确保在“选择应用程序”字段选择你创建的 Azure AD B2C 应用程序，然后单击“立即运行”对其进行测试。

