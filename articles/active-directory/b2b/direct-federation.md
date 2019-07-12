---
title: 将 B2B-Azure Active Directory 的标识提供者的直接联合身份验证设置 |Microsoft Docs
description: 与 SAML 或 WS 联合身份验证标识提供者直接联合，以便来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/01/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1bc3c1325e8379082134e2cbec1586f7d338ee61
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797941"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>使用 AD FS 和来宾用户 （预览） 的第三方提供程序的直接联合身份验证
|     |
| --- |
| 直接联合身份验证是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何设置直接与另一个组织的 B2B 协作的联合身份验证。 您可以设置与任何组织的标识提供者 (IdP) 支持 SAML 2.0 或 WS 联合身份验证协议的直接联合身份验证。
如果设置了直接与合作伙伴的 IdP 的联合身份验证时，在这个域中新的来宾用户可以使用自己 IdP 托管的组织帐户登录到你的 Azure AD 租户并开始与你协作。 若要创建一个单独的来宾用户无需 Azure AD 帐户。
> [!NOTE]
> 直接联合身份验证来宾用户必须使用包含租户上下文的链接登录 (例如，`https://myapps.microsoft.com/?tenantid=<tenant id>`或`https://portal.azure.com/<tenant id>`，或者如果已验证的域，是`https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`)。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 直接联合身份验证的用户目前不能使用没有租户的上下文的公共终结点进行登录。 例如，使用`https://myapps.microsoft.com`， `https://portal.azure.com`，或`https://teams.microsoft.com`将导致错误。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>进行来宾用户身份验证时使用直接联合身份验证？
设置与组织的直接联合身份验证后，将使用直接联合身份验证验证任何新的来宾用户邀请。 请务必注意设置直接联合身份验证不会更改你的邀请已兑换的来宾用户的身份验证方法。 下面是一些可能的恶意活动：
 - 如果来宾用户已兑换邀请你，并且你随后设置直接使用其组织的联合身份验证，这些来宾用户将继续使用相同的身份验证方法设置直接联合身份验证之前使用它们。
 - 如果设置了直接与合作伙伴组织的联合身份验证，并邀请来宾用户，然后将合作伙伴组织更高版本移到 Azure AD，已兑换邀请的来宾用户将继续使用直接联合身份验证，只要直接你的租户中的联合身份验证策略存在。
 - 如果删除与某个合作伙伴组织的直接联合身份验证时，当前正在使用直接联合身份验证任何来宾用户将无法登录。

在任何这些情况下，您可以通过从你的目录中删除来宾用户帐户和 reinviting 它们更新来宾用户的身份验证方法。

直接联合身份验证被绑定到域命名空间，例如 contoso.com 和 fabrikam.com。 当建立与 AD FS 或第三方 IdP 的直接联合身份验证配置时，组织将关联到这些 Idp 的一个或多个域命名空间。 

## <a name="end-user-experience"></a>最终用户体验 
使用直接联合身份验证，来宾用户登录到 Azure AD 租户使用其自己的组织帐户。 当用户访问共享的资源，并将提示您输入签入时，直接联合身份验证的用户将重定向到其 IdP。 成功登录后，它们将返回到 Azure AD 访问的资源。 直接联合身份验证用户的刷新令牌的有效期为 12 小时[默认传递刷新令牌的长度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)Azure AD 中。 如果联合的 IdP 有 SSO 已启用，用户将体验 SSO，并将初始身份验证后没有看到任何登录提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 验证域
直接联合身份验证只允许在域***不***DNS 验证 Azure AD 中。 对于非托管 （电子邮件验证或"活跃"） 的 Azure AD 租户，因为它们不是 DNS 验证，允许直接联合身份验证。
### <a name="authentication-url"></a>身份验证 URL
身份验证 URL 的域与目标域中，匹配之处或身份验证 URL 时 （此列表并发生更改） 这些允许的标识提供程序之一的策略只允许直接联合身份验证：
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，当设置为直接联合身份验证**fabrikam.com**，身份验证 URL`https://fabrikam.com/adfs`将通过验证。 在同一个域中的主机也将，例如`https://sts.fabrikam.com/adfs`。 但是，身份验证 URL`https://fabrikamconglomerate.com/adfs`或`https://fabrikam.com.uk/adfs`的同一个域不会传递。

### <a name="signing-certificate-renewal"></a>签名证书续订
如果标识提供程序设置中指定的元数据 URL，Azure AD 将自动续订签名证书过期后。 但是，如果证书出于任何原因旋转早于过期时间，或如果未提供元数据 URL，Azure AD 将不能续订它。 在这种情况下，你将需要手动更新签名证书。
## <a name="frequently-asked-questions"></a>常见问题
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>可以设置与域存在 （电子邮件验证） 的非托管的租户的直接联合身份验证？ 
是的。 如果尚未验证域和租户尚未经过[管理员接管](../users-groups-roles/domains-admin-takeover.md)，可以设置直接联合身份验证。 在用户兑换 B2B 邀请或执行使用当前不存在的域的 Azure AD 中的自助注册时，会创建受管理，或电子邮件验证的租户。 您可以将直接与这些域的联合身份验证设置。 如果尝试建立直接联合身份验证与 DNS 验证域，在 Azure 门户中或通过 PowerShell，您将看到一个错误。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同时启用直接联合身份验证和电子邮件的一次性密码身份验证，哪种方法将优先？
与合作伙伴组织建立直接联合身份验证后，它将优先于该组织的新来宾用户的电子邮件的一次性密码身份验证。 如果来宾用户兑换邀请之前设置直接联合身份验证使用一次性密码身份验证，它们将继续使用一次性密码身份验证。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>由于部分已同步租户直接联合身份验证解决登录问题？
否，[电子邮件发送一次性密码](one-time-passcode.md)功能应在此方案中使用。 "部分同步租户"是指合作伙伴 Azure AD 租户的本地用户标识不完全同步到云。 在云中的标识尚不存在但谁在尝试兑换 B2B 邀请来宾将无法登录。 一次性密码功能将允许此来宾登录。 直接联合身份验证功能来解决在来宾机器具有其自己 IdP 管理组织帐户，但组织不存在 Azure AD 在所有方案。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步骤 1：配置合作伙伴组织的标识提供者
首先，你的合作伙伴组织需要的必需的声明和信赖方信任配置其标识提供者。 

> [!NOTE]
> 若要说明如何配置直接联合身份验证的标识提供程序，我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 请参阅文章[直接联合身份验证配置与 AD FS](direct-federation-adfs.md)，其中提供了如何将 AD FS 配置为在准备进行直接联合身份验证的 SAML 2.0 或 WS 联合身份验证标识提供者的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 配置

可以配置 azure AD B2B 进行联合身份验证使用与下面列出的特定要求使用 SAML 协议的标识提供者。 有关设置 SAML 标识提供者与 Azure AD 之间建立信任的详细信息，请参阅[使用单一登录的 SAML 2.0 标识提供者 (IdP)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 请注意直接联合身份验证目标域不能 DNS 验证 Azure AD 上。 身份验证 URL 域必须匹配目标域也必须是允许的标识提供程序的域。 请参阅[限制](#limitations)部分了解详细信息。 

#### <a name="required-saml-20-attributes-and-claims"></a>需要 SAML 2.0 特性和声明
下表显示的特定属性，以及必须在第三方标识提供者配置的声明的要求。 若要设置直接联合身份验证，必须从标识提供程序的 SAML 2.0 响应中收到以下属性。 通过将链接到在线安全令牌服务的 XML 文件或通过手动输入，可以配置这些属性。

从 IdP 的 SAML 2.0 响应所需的属性：

|特性  |ReplTest1  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |颁发者 URI 的示例中的 IdP，合作伙伴 `http://www.example.com/exk10l6w90DHM0yi...`         |


必需的 IdP 颁发的 SAML 2.0 令牌的声明：

|特性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS 联合身份验证配置 
可以配置 azure AD B2B 进行联合身份验证与具有某些特定要求使用 WS 联合身份验证协议，如下所示的标识提供程序。 目前，Azure AD 与兼容性包括 AD FS 和 Shibboleth 两个 WS 联合身份验证提供程序已经过测试。 有关建立符合 WS 联合身份验证提供程序与 Azure AD 之间的信赖方信任的详细信息，请参阅"使用 WS 协议 STS 集成纸"中提供[Azure AD 标识提供程序兼容性文档](https://www.microsoft.com/download/details.aspx?id=56843)。

> [!NOTE]
> 直接联合身份验证目标域必须不是 DNS 验证 Azure AD 上。 身份验证 URL 域必须与目标域或允许的标识提供程序的域匹配。 请参阅[限制](#limitations)部分了解详细信息。 

#### <a name="required-ws-fed-attributes-and-claims"></a>需要 WS 联合身份验证属性和声明

下表显示的特定属性，以及必须在第三方 WS 联合身份验证标识提供者配置的声明的要求。 若要设置直接联合身份验证，必须从标识提供程序的 WS 联合身份验证消息中收到以下属性。 通过将链接到在线安全令牌服务的 XML 文件或通过手动输入，可以配置这些属性。

从 IdP WS 联合身份验证消息中的必需的属性：
 
|特性  |值  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |颁发者 URI 的示例中的 IdP，合作伙伴 `http://www.example.com/exk10l6w90DHM0yi...`         |

必需的 IdP 颁发的 WS 联合身份验证令牌的声明：

|特性  |值  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置直接联合身份验证 
接下来，你将使用在 Azure AD 中配置在步骤 1 中的标识提供程序配置联合身份验证。 可以使用 Azure AD 门户或 PowerShell。 可能需要 5 到 10 分钟的直接联合身份验证策略才能生效。 在此期间，请勿尝试兑换直接联合身份验证域的邀请。 需要以下属性：
- 颁发者 URI 的合作伙伴 IdP
- 伙伴 （支持仅 https） 的 IdP 的被动身份验证终结点
- 证书

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>若要配置 Azure AD 门户中的直接联合身份验证

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择**标识提供者**，然后选择**新 SAML/Ws-fed IdP**。

    ![添加新 SAML 或 Ws-fed IdP 的屏幕截图显示按钮](media/direct-federation/new-saml-wsfed-idp.png)

4. 上**新 SAML/Ws-fed IdP**页面上，在**标识提供者协议**，选择**SAML**或者**WS-FED**。

    ![在 SAML 或 Ws-fed IdP 页上的屏幕截图显示分析按钮](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 输入合作伙伴组织的域名，它将是直接联合身份验证在目标域名称
6. 可以上传元数据文件来填充元数据详细信息。 如果你选择手动输入元数据，输入以下信息：
   - 合作伙伴 IdP 的域名
   - 合作伙伴 IdP 实体 ID
   - 合作伙伴 IdP 的被动请求者终结点
   - 证书
   > [!NOTE]
   > 但是，我们强烈建议，是可选的元数据 URL。 如果提供的元数据 URL，Azure AD 可以自动续订签名证书过期后。 如果在到期时间之前证书旋转出于任何原因或未提供元数据 URL，Azure AD 将不能续订它。 在这种情况下，你将需要手动更新签名证书。

7. 选择**保存**。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>若要使用 PowerShell 的 Azure AD 中配置直接联合身份验证

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 (如果需要详细的步骤，添加来宾用户快速入门教程包括的部分[安装最新的 AzureADPreview 模块](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)。) 
2. 运行下面的命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 根据登录提示使用托管的全局管理员帐户登录。 
2. 运行以下命令，将联合身份验证元数据文件中的值。 有关 AD FS 服务器和 Okta，联合身份验证文件是 federationmetadata.xml，例如： `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步骤 3：在 Azure AD 中测试直接联合身份验证
现在通过新的 B2B 来宾用户邀请测试直接联合身份验证设置。 有关详细信息，请参阅[在 Azure 门户中的添加 Azure AD B2B 协作用户](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何编辑直接联合身份验证关系？

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择**标识提供者**
4. 下**SAML/Ws-fed 标识提供者**，选择的提供程序。
5. 在标识提供程序的详细信息窗格中，更新的值。
6. 选择**保存**。


## <a name="how-do-i-remove-direct-federation"></a>如何删除直接联合身份验证？
您可以删除你的直接联合身份验证设置。 如果这样做，直接联合身份验证已兑换其邀请的来宾用户将无法登录。 但您可以授予他们访问你的资源再次通过从目录删除它们，reinviting 它们。 若要在 Azure AD 门户中删除的标识提供者的直接联合身份验证：

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择**标识提供者**。
4. 选择标识提供程序，并选择**删除**。 
5. 选择“是”  以确认删除。 

若要使用 PowerShell 删除直接联合身份验证的标识提供者：
1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。
2. 运行下面的命令： 
   ```powershell
   Connect-AzureAD
   ```
3. 根据登录提示使用托管的全局管理员帐户登录。 
4. 输入以下命令：
   ```powershell
   Remove-AzureADExternalDomainFederation -ExternalDomainName  $domainName
   ```
