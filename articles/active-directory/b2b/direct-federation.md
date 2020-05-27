---
title: 与 B2B 标识提供者的直接联合 - Azure AD
description: 直接与 SAML 或 WS-Fed 标识提供者联合，使来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 299b0a677e7ca7bea9481d94ecf98c993af0a6ed
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83591210"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>与面向来宾用户的 AD FS 和第三方提供者的直接联合（预览）
|     |
| --- |
| 直接联合是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何为实现 B2B 协作建立与其他组织的直接联合。 可以与其标识提供者 (IdP) 支持 SAML 2.0 或 WS-Fed 协议的任何组织建立直接联合。
与合作伙伴的 IdP 建立直接联合时，该域中的新来宾用户可以使用其自己的 IdP 管理的组织帐户登录到 Azure AD 租户，并开始与你合作。 来宾用户无需创建单独的 Azure AD 帐户。
> [!NOTE]
> 直接联合来宾用户必须使用包含租户上下文的链接登录（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>` 或 `https://portal.azure.com/<tenant id>`；对于经过验证的域，请使用 `https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`）。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 直接联合用户目前无法使用不包含租户上下文的通用终结点登录。 例如，使用 `https://myapps.microsoft.com`、`https://portal.azure.com` 或 `https://teams.microsoft.com` 将会导致错误。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>来宾用户何时使用直接联合进行身份验证？
与组织建立直接联合后，邀请的任何新来宾用户都将使用直接联合进行身份验证。 特别要注意的是，建立直接联合并不会更改已兑换邀请的来宾用户的身份验证方法。 下面是一些示例：
 - 如果来宾用户已经兑换了邀请，而你随后与其组织建立了直接联合，则这些来宾用户将继续使用在建立直接联合之前使用的相同身份验证方法。
 - 如果与合作伙伴组织建立直接联合并邀请来宾用户，然后合作伙伴组织稍后转到 Azure AD，则已兑换邀请的来宾用户将继续使用直接联合，前提是你租户中的直接联合策略已存在。
 - 如果删除与合作伙伴组织的直接联合，则当前使用直接联合的所有来宾用户都将无法登录。

在上述任何情况下，都可以通过从目录中删除来宾用户帐户并对其重新发出邀请，来更新来宾用户的身份验证方法。

直接联合与域命名空间（如 contoso.com 和 fabrikam.com）相关联。 使用 AD FS 或第三方 IdP 建立直接联合配置时，组织会将一个或多个域命名空间与这些 Idp 相关联。 

## <a name="end-user-experience"></a>最终用户体验 
使用直接联合，来宾用户使用其自己的组织帐户登录到你的 Azure AD 租户。 当用户访问共享资源并提示登录时，直接联合用户会重定向到其 IdP。 成功登录后，他们会返回到 Azure AD 来访问资源。 直接联合用户的刷新令牌有效期为 12 小时，这是 Azure AD 中[传递刷新令牌的默认长度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)。 如果联合 IdP 启用了 SSO，用户将体验 SSO，并在初始身份验证后不会看到任何登录提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 验证域
要与之联合的域不得在 Azure AD 中进行 DNS 验证。 允许你建立与非托管（经电子邮件验证或“病毒性”）Azure AD 租户的直接联合，因为未对其进行 DNS 验证。

### <a name="authentication-url"></a>身份验证 URL
直接联合只允许在身份验证 URL 的域与目标域匹配的策略中使用，或者在身份验证 URL 是这些允许的标识提供者之一的策略中使用（此列表可能会更改）：

-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   oktapreview.com
-   okta-emea.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，为 fabrikam.com 建立直接联合时，身份验证 URL `https://fabrikam.com/adfs` 将通过验证。 同一域中的主机也将通过验证，例如 `https://sts.fabrikam.com/adfs`。 但是，同一域的身份验证 URL `https://fabrikamconglomerate.com/adfs` 或 `https://fabrikam.com.uk/adfs` 不会通过验证。

### <a name="signing-certificate-renewal"></a>签名证书续订
如果在标识提供者设置中指定元数据 URL，Azure AD 将在签名证书过期时自动续订该证书。 但是，如果出于任何原因在过期之前轮换证书，或未提供元数据 URL，Azure AD 将无法续订该证书。 在这种情况下，你将需要手动更新签名证书。

### <a name="limit-on-federation-relationships"></a>联合关系限制
目前最多支持 1000 个联合关系。 此限制包括[内部联合](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)和直接联合。

### <a name="limit-on-multiple-domains"></a>多个域限制
目前，我们不支持同一个租户中多个域的直接联合。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>是否可以与非托管（经电子邮件验证）租户所在的域建立直接联合？ 
是的。 如果未验证域且租户尚未完成[管理员接管](../users-groups-roles/domains-admin-takeover.md)，则可以与该域建立直接联合。 当用户使用当前不存在的域兑换 B2B 邀请或执行 Azure AD 的自助注册时，将创建非托管或经电子邮件验证的租户。 可以与这些域建立直接联合。 如果尝试在 Azure 门户或通过 PowerShell 建立与经 DNS 验证的域的直接联合，则会看到一个错误。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同时启用了直接联合和电子邮件一次性密码身份验证，那么哪个方法优先？
与合作伙伴组织建立直接联合时，对于该组织中的新来宾用户而言，它将优先于电子邮件一次性密码身份验证。 如果来宾用户在建立直接联合之前使用一次性密码身份验证兑换了邀请，则他们将继续使用一次性密码身份验证。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>是否由于部分同步的租户导致了直接联合地址登录问题？
否。在此场景中，应使用[电子邮件一次性密码](one-time-passcode.md)功能。 “部分同步的租户”指的是合作伙伴 Azure AD 租户，其中本地用户标识未完全同步到云。 其标识尚不存在于云中但尝试兑换 B2B 邀请的来宾将无法登录。 使用一次性密码功能，此来宾可以登录。 直接联合功能可以解决以下情况：来宾具有其自己的 IdP 托管的组织帐户，但组织没有 Azure AD。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步骤 1：配置合作伙伴组织的标识提供者
首先，你的合作伙伴组织需要为其标识提供者配置所需的声明和信赖方信任。 

> [!NOTE]
> 为了说明如何为直接联合配置标识提供者，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 请参阅文章[使用 AD FS 配置直接联合](direct-federation-adfs.md)，其中提供了有关如何将 AD FS 配置为 SAML 2.0 或 WS-Fed 标识提供者，以准备直接联合的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 配置

可以将 Azure AD B2B 配置为与使用 SAML 协议的标识提供者联合，具体要求如下。 有关在 SAML 标识提供者和 Azure AD 之间建立信任的详细信息，请参阅[使用 SAML 2.0 标识提供者 (IdP) 进行单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 在 Azure AD 上，不能对直接联合的目标域进行 DNS 验证。 身份验证 URL 域必须与目标域相匹配，或者必须是所允许标识提供者的域。 有关详细信息，请参阅[限制](#limitations)部分。 

#### <a name="required-saml-20-attributes-and-claims"></a>SAML 2.0 的必需属性和声明
下表显示了必须在第三方标识提供者中配置的特定属性和声明的要求。 若要建立直接联合，必须从标识提供者的 SAML 2.0 响应中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。

来自 IdP 的 SAML 2.0 响应的必需属性：

|Attribute  |值  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 颁发的 SAML 2.0 令牌的必需声明：

|Attribute  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 配置 
可以将 Azure AD B2B 配置为与使用 WS-Fed 协议的标识提供者联合，具体要求如下。 目前，已通过 Azure AD 对两个 WS-Fed 提供者的兼容性进行测试，包括 AD FS 和 Shibboleth。 有关通过 Azure AD 在 WS-Fed 兼容的提供者之间建立信赖方信任的详细信息，请参阅 [Azure AD 标识提供者兼容性文档](https://www.microsoft.com/download/details.aspx?id=56843)中提供的“使用 WS 协议的 STS 集成论文”。

> [!NOTE]
> 在 Azure AD 上，不能对直接联合的目标域进行 DNS 验证。 身份验证 URL 域必须与目标域相匹配，或者必须是所允许标识提供者的域。 有关详细信息，请参阅[限制](#limitations)部分。 

#### <a name="required-ws-fed-attributes-and-claims"></a>WS-Fed 的必需属性和声明

下表显示了必须在第三方 WS-Fed 标识提供者中配置的特定属性和声明的要求。 若要建立直接联合，必须从标识提供者的 WS-Fed 消息中接收以下属性。 可以通过链接到联机安全令牌服务 XML 文件或手动输入来配置这些属性。

来自 IdP 的 WS-Fed 消息中的必需属性：
 
|Attribute  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如 `http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-Fed 令牌的必需声明：

|Attribute  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置直接联合 
接下来，你将配置与 Azure AD 中的步骤 1 中配置的标识提供者的联合。 可以使用 Azure AD 门户或 PowerShell。 直接联合策略生效之前可能需要 5-10 分钟。 在此期间，不要尝试兑换直接联合域的邀请。 需要以下属性：
- 合作伙伴 IdP 的颁发者 URI
- 合作伙伴 IdP 的被动身份验证终结点（仅支持 https）
- 证书

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置直接联合

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识” > “所有标识提供者”。
3. 然后选择“新的 SAML/WS-Fed IdP”。

    ![显示用于添加新 SAML 或 WS-Fed IdP 的按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp.png)

4. 在“新的 SAML/WS-Fed IdP”页上，在“标识提供者协议”下，选择“SAML”或“WS-FED”。

    ![显示 SAML 或 WS-Fed IdP 页上的“分析”按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 输入合作伙伴组织的域名，该域名将是直接联合的目标域名
6. 可以上传元数据文件以填充元数据详细信息。 如果选择手动输入元数据，请输入以下信息：
   - 合作伙伴 IdP 的域名
   - 合作伙伴 IdP 的实体 ID
   - 合作伙伴 IdP 的被动请求者终结点
   - 证书
   > [!NOTE]
   > 元数据 URL 是可选的，但我们强烈建议使用。 如果提供元数据 URL，Azure AD 可以在签名证书过期时自动续订该证书。 如果出于任何原因在过期之前轮换证书，或未提供元数据 URL，Azure AD 将无法续订该证书。 在这种情况下，你将需要手动更新签名证书。

7. 选择“保存”。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>在 Azure AD 中使用 PowerShell 配置直接联合

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 （如果需要详细步骤，则添加来宾用户的快速入门包括[安装最新的 AzureADPreview 模块](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)部分。） 
2. 运行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 根据登录提示使用托管的全局管理员帐户登录。 
2. 运行以下命令，并替换联合元数据文件中的值。 对于 AD FS Server 和 Okta，联合文件为 federationmetadata.xml，例如：`https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

   ```powershell
   $federationSettings = New-Object Microsoft.Open.AzureAD.Model.DomainFederationSettings
   $federationSettings.PassiveLogOnUri ="https://sts.totheclouddemo.com/adfs/ls/"
   $federationSettings.LogOffUri = $federationSettings.PassiveLogOnUri
   $federationSettings.IssuerUri = "http://sts.totheclouddemo.com/adfs/services/trust"
   $federationSettings.MetadataExchangeUri="https://sts.totheclouddemo.com/adfs/services/trust/mex"
   $federationSettings.SigningCertificate= <Replace with X509 signing cert’s public key>
   $federationSettings.PreferredAuthenticationProtocol="WsFed" OR "Samlp"
   $domainName = <Replace with domain name>
   New-AzureADExternalDomainFederation -ExternalDomainName $domainName  -FederationSettings $federationSettings
   ```

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步骤 3：在 Azure AD 中测试直接联合
现在，邀请新的 B2B 来宾用户来测试直接联合设置。 有关详细信息，请参阅[在 Azure 门户中添加 Azure AD B2B 协作用户](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何编辑直接联合关系？

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”
4. 在“SAML/WS-Fed 标识提供者”下，选择提供者。
5. 在“标识提供者详细信息”窗格中，更新值。
6. 选择“保存”。


## <a name="how-do-i-remove-direct-federation"></a>如何删除直接联合？
可以删除直接联合设置。 如果这样做，已兑换邀请的直接联合来宾用户将无法登录。 不过，你可以通过从目录中删除用户并重新邀请，再次向用户授予对资源的访问权限。 在 Azure AD 门户中删除与标识提供者的直接联合：

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”。 
2. 选择“外部标识”。
3. 选择“所有标识提供者”。
4. 选择标识提供者，然后选择“删除”。 
5. 选择“是”以确认删除。 

使用 PowerShell 删除与标识提供者的直接联合：
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
3. 根据登录提示使用托管的全局管理员帐户登录。 
4. 输入以下命令：
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
