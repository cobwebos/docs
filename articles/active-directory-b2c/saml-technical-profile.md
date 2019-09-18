---
title: 定义采用 Azure Active Directory B2C 的自定义策略的 SAML 技术配置文件 | Microsoft Docs
description: 定义采用 Azure Active Directory B2C 的自定义策略的 SAML 技术配置文件。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/21/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 38215ef49bdc5788e43e4ea0fedef2efd32d8213
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063788"
---
# <a name="define-a-saml-technical-profile-in-an-azure-active-directory-b2c-custom-policy"></a>定义采用 Azure Active Directory B2C 的自定义策略的 SAML 技术配置文件

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C （Azure AD B2C）为 SAML 2.0 标识提供程序提供支持。 本文介绍了与支持此标准化协议的声明提供程序进行交互的技术配置文件的详细信息。 使用 SAML 技术配置文件，可以与基于 SAML 的标识提供者（例如 [ADFS](active-directory-b2c-custom-setup-adfs2016-idp.md) 和 [Salesforce](active-directory-b2c-setup-sf-app-custom.md)）联合， 这样用户就可以使用其现有的社交或企业标识登录。

## <a name="metadata-exchange"></a>元数据交换

元数据是 SAML 协议中用于公开 SAML 方（例如服务提供程序或身份提供程序）的配置的信息。 元数据定义服务的位置，例如登录和注销、证书、登录方法和其他信息。 身份提供程序使用元数据来了解如何与 Azure AD B2C 进行通信。 元数据以 XML 格式配置，并且可以用数字签名签名，以便另一方可以验证元数据的完整性。 当 Azure AD B2C 与 SAML 身份提供程序联合时，它将充当发起 SAML 请求并等待 SAML 响应的服务提供程序。 并且，在某些情况下，会接受未经请求的 SAML 身份验证，也称为标识提供者发起的身份验证。

元数据可以在双方中配置为“静态元数据”或“动态元数据”。 在静态模式下，你从一方复制整个元数据并将其设置在另一方中。 在动态模式下，你将 URL 设置为元数据，而另一方则动态读取配置。 原理是相同的，在身份提供程序中设置 Azure AD B2C 技术配置文件的元数据，并在 Azure AD B2C 中设置身份提供程序的元数据。

每个 SAML 身份提供程序都有不同的步骤来公开和设置服务提供程序，在本例的 Azure AD B2C 中，在身份提供程序中设置 Azure AD B2C 元数据。 请查看你的身份提供程序的文档，以获取有关如何执行此操作的指导。

以下示例显示 Azure AD B2C 技术配置文件的 SAML 元数据的 URL 地址：

```
https://your-tenant-name.b2clogin.com/your-tenant-name/your-policy/samlp/metadata?idptp=your-technical-profile
```

替换以下值：

- 将 **your-tenant-name** 替换为你的租户名称，例如 fabrikam.b2clogin.com。
- 将 your-policy 替换为你的策略名称。 使用配置 SAML 提供者技术配置文件的策略或从该策略继承的策略。
- 将 **your-technical-profile** 替换为 SAML 标识提供者技术配置文件名称。

## <a name="digital-signing-certificates-exchange"></a>数字签名证书交换

若要在 Azure AD B2C 与 SAML 身份提供程序之间建立信任，你需要提供带有私钥的有效 X509 证书。 将带私钥的证书（.pfx 文件）上传到 Azure AD B2C 策略密钥存储库。 Azure AD B2C 使用你提供的证书对 SAML 登录请求进行数字签名。

可通过以下方式使用证书：

- Azure AD B2C 使用证书的 Azure AD B2C 私钥生成 SAML 请求并对其进行签名。 SAML 请求将发送到身份提供程序，该提供程序使用证书的 Azure AD B2C 公钥验证请求。 通过技术配置文件元数据可访问 Azure AD B2C 公用证书。 或者，可以将 .cer 文件手动上传到 SAML 身份提供程序。
- 身份提供程序使用身份提供程序的证书私钥对发送到 Azure AD B2C 的数据进行签名。 Azure AD B2C 会使用身份提供程序的公共证书验证数据。 每个标识提供者都有不同的设置步骤，请查看标识提供者的文档，以获取有关如何执行此操作的指导。 在 Azure AD B2C 中，你的策略需要使用身份提供程序的元数据访问证书公钥。

在大多数情况下，自签名证书都是可接受的。 对于生产环境，建议使用由证书颁发机构颁发的 X509 证书。 此外，如本文档后面所述，对于非生产环境，你可以禁用两端的 SAML 签名。

下图显示了元数据和证书交换：

![元数据和证书交换](media/saml-technical-profile/technical-profile-idp-saml-metadata.png)


## <a name="digital-encryption"></a>数字加密

若要加密 SAML 响应断言，身份提供程序应始终在 Azure AD B2C 技术配置文件中使用加密证书的公钥。 当 Azure AD B2C 需要对数据进行解密时，它使用加密证书的专用部分。

若要加密 SAML 响应断言：

1. 将带私钥的有效 X509 证书（.pfx 文件）上传到 Azure AD B2C 策略密钥存储库。
2. 将标识符为 `SamlAssertionDecryption` 的 CryptographicKey 元素添加到技术配置文件 CryptographicKeys 集合。 将 StorageReferenceId 设为在步骤 1 中创建的策略密钥的名称。
3. 将技术配置文件元数据 WantsEncryptedAssertions 设为 `true`。
4. 使用新的 Azure AD B2C 技术配置文件元数据更新身份提供程序。 你应该会看到 KeyDescriptor，其中的“使用”属性设置为包含你的证书的公钥的 `encryption`。

以下示例显示元数据的 Azure AD B2C 技术配置文件加密部分：

```XML
<KeyDescriptor use="encryption">
  <KeyInfo xmlns="https://www.w3.org/2000/09/xmldsig#">
    <X509Data>
      <X509Certificate>valid certificate</X509Certificate>
    </X509Data>
  </KeyInfo>
</KeyDescriptor>
```

## <a name="protocol"></a>Protocol

“协议”元素的“名称”属性必须设置为 `SAML2`。

## <a name="output-claims"></a>输出声明

**OutputClaims** 元素在 `AttributeStatement` 节下包含 SAML 标识提供者返回的声明列表。 可能需要将策略中定义的声明名称映射到标识提供者中定义的名称。 只要设置了 `DefaultValue` 属性，就还可以包含标识提供者不会返回的声明。

若要将 **Subject** 中的 SAML 断言 **NamedId** 读取为规范化的声明，请将声明 **PartnerClaimType** 设置为 `assertionSubjectName`。 确保 **NameId** 是断言 XML 中的第一个值。 定义多个断言时，Azure AD B2C 会选取上一个声明中的主题值。

**OutputClaimsTransformations** 元素可能包含用于修改输出声明或生成新输出声明的 **OutputClaimsTransformation** 元素集合。

以下示例演示 Facebook 标识提供者返回的声明：

- **IssuerUserId**声明映射到**assertionSubjectName**声明。
- **first_name** 声明已映射到 **givenName** 声明。
- **last_name** 声明已映射到 **surname** 声明。
- 没有名称映射的 **displayName** 声明。
- 没有名称映射的 **email** 声明。

技术配置文件还会返回标识提供者不返回的声明：

- **identityProvider** 声明，其中包含标识提供者的名称。
- **authenticationSource** 声明，其默认值为 **socialIdpAuthentication**。

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="assertionSubjectName" />
  <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="first_name" />
  <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="last_name" />
  <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
  <OutputClaim ClaimTypeReferenceId="email"  />
  <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
  <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
</OutputClaims>
```

## <a name="metadata"></a>元数据

| 特性 | 必填 | 描述 |
| --------- | -------- | ----------- |
| PartnerEntity | 是 | SAML 身份提供程序的元数据的 URL。 复制身份提供程序元数据并将其添加到 CDATA 元素 `<![CDATA[Your IDP metadata]]>` |
| WantsSignedRequests | 否 | 指示技术配置文件是否要求对所有传出身份验证请求进行签名。 可能的值：`true` 或 `false`。 默认值为 `true`。 当该值设置为 `true` 时，需要指定 SamlMessageSigning加密密钥，并对所有传出的身份验证请求进行签名。 如果该值设置为 `false`，则请求中将省略 SigAlg 和 Signature 参数（查询字符串或 post 参数）。 此元数据还控制元数据的 AuthnRequestsSigned 属性，该属性在与身份提供程序共享的 Azure AD B2C 技术配置文件的元数据中输出。 如果技术配置文件元数据中的 **WantsSignedRequests** 的值设置为 `false` 且标识提供者元数据 **WantAuthnRequestsSigned** 设置为 `false` 或未指定，则 Azure AD B2C 不会对请求签名。 |
| XmlSignatureAlgorithm | 否 | Azure AD B2C 用于对 SAML 请求进行签名的方法。 此元数据控制 SAML 请求中 SigAlg参数（查询字符串或 post 参数）的值。 可能的值：`Sha256`、`Sha384`、`Sha512` 或 `Sha1`。 确保在两端配置具有相同值的签名算法。 仅使用证书支持的算法。 |
| WantsSignedAssertions | 否 | 指示技术配置文件是否要求对所有传入断言进行签名。 可能的值：`true` 或 `false`。 默认值为 `true`。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的所有断言部分 `saml:Assertion` 必须进行签名。 如果该值设置为 `false`，则身份提供程序不应对断言进行签名，但即使这样做，Azure AD B2C 也不会验证签名。 此元数据还控制元数据标记的 WantsAssertionsSigned 属性，该属性在与身份提供程序共享的 Azure AD B2C 技术配置文件的元数据中输出。 如果禁用断言验证，则还可能想要禁用响应签名验证（有关详细信息，请参阅 ResponsesSigned）。 |
| ResponsesSigned | 否 | 可能的值：`true` 或 `false`。 默认值为 `true`。 如果该值设置为 `false`，则身份提供程序不应对 SAML 响应进行签名，但即使这样做，Azure AD B2C 也不会验证签名。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的 SAML 响应已签名，且必须进行验证。 如果禁用 SAML 响应验证，则还可能想要禁用断言签名验证（有关详细信息，请参阅 WantsSignedAssertions）。 |
| WantsEncryptedAssertions | 否 | 指示技术配置文件是否要求对所有传入断言进行加密。 可能的值：`true` 或 `false`。 默认值为 `false`。 如果该值设置为 `true`，则身份提供程序发送到 Azure AD B2C 的断言必须进行签名，并且需要指定 SamlAssertionDecryption加密密钥。 如果该值设置为 `true`，则 Azure AD B2C 技术配置文件的元数据要包括“加密”部分。 身份提供程序读取元数据并使用 Azure AD B2C 技术配置文件的元数据中提供的公钥加密 SAML 响应断言。 如果启用断言加密，则还可能需要禁用响应签名验证（有关详细信息，请参阅 ResponsesSigned）。 |
| IdpInitiatedProfileEnabled | 否 | 指示是否启用由 SAML 身份提供程序配置文件启动的单一登录会话配置文件。 可能的值：`true` 或 `false`。 默认值为 `false`。 在由身份提供程序启动的流程中，用户在外部进行身份验证，并向 Azure AD B2C 发送未经请求的响应，然后 Azure AD B2C 使用令牌，执行业务流程步骤，然后向信赖方应用发送响应。 |
| NameIdPolicyFormat | 否 | 指定要使用的名称标识符上的约束，使之代表请求的主题。 如果省略此项，则可使用请求的主题对应的标识提供者支持的任何类型的标识符。 例如，`urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`。 **NameIdPolicyFormat** 可以与 **NameIdPolicyAllowCreate** 配合使用。 查看标识提供者的文档，了解哪些名称 ID 策略受支持。 |
| NameIdPolicyAllowCreate | 否 | 使用 **NameIdPolicyFormat** 时，也可指定 **NameIDPolicy** 的 `AllowCreate` 属性。 此元数据的值为 `true` 或 `false`，表示是否允许标识提供者在登录流程中创建新帐户。 请查看你的身份提供程序的文档，以获取有关如何执行此操作的指导。 |
| AuthenticationRequestExtensions | 否 | Azure AD BC 和标识提供者认可的可选协议消息扩展元素。 此扩展以 XML 格式呈现。 将 XML 数据添加到 CDATA 元素 `<![CDATA[Your IDP metadata]]>` 中。 检查标识提供者的文档，看扩展元素是否受支持。 |
| IncludeAuthnContextClassReferences | 否 | 指定一个或多个可标识身份验证上下文类的 URI 引用。 例如，如果只允许用户使用用户名和密码登录，请将值设置为 `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`。 若要允许用户在受保护会话 (SSL/TLS) 期间通过用户名和密码登录，请指定 `PasswordProtectedTransport`。 查看标识提供者的文档，了解受支持的 **AuthnContextClassRef** URI。 |
| IncludeKeyInfo | 否 | 指定在将绑定设置为 `HTTP-POST` 时，SAML 身份验证请求是否包含证书的公钥。 可能的值：`true` 或 `false`。 |

## <a name="cryptographic-keys"></a>加密密钥

<**CryptographicKeys**> 元素包含以下属性：

| 特性 |必填 | 描述 |
| --------- | ----------- | ----------- |
| SamlMessageSigning |是 | X509 证书（RSA 密钥集），用于对 SAML 消息进行签名。 Azure AD B2C 使用此密钥对请求进行签名并将其发送给身份提供程序。 |
| SamlAssertionDecryption |是 | X509 证书（RSA 密钥集），用于解密 SAML 消息。 此证书应由身份提供程序提供。 Azure AD B2C 使用此证书解密身份提供程序发送的数据。 |
| MetadataSigning |否 | X509 证书（RSA 密钥集），用于对 SAML 元数据进行签名。 Azure AD B2C 使用此密钥对元数据进行签名。  |

## <a name="examples"></a>示例

- [使用自定义策略将 ADFS 添加为 SAML 身份提供程序](active-directory-b2c-custom-setup-adfs2016-idp.md)
- [通过 SAML 使用 Salesforce 帐户登录](active-directory-b2c-setup-sf-app-custom.md)













