---
title: 设置与用于 B2B Azure Active Directory 的标识提供者的直接联合身份验证 |Microsoft Docs
description: 直接与 SAML 或 WS-FEDERATION 标识提供者联合, 使来宾可以登录到 Azure AD 的应用程序
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 07/15/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 113e178d39ec776b63a0b38c55035f3493586ea2
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2019
ms.locfileid: "68233870"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>与来宾用户的 AD FS 和第三方提供程序的直接联合 (预览)
|     |
| --- |
| 直接联合是 Azure Active Directory 的公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何为 B2B 协作设置与其他组织的直接联盟。 你可以设置与标识提供者 (IdP) 支持 SAML 2.0 或 WS 送单协议的任何组织的直接联合。
设置与合作伙伴 IdP 的直接联合时, 该域中的新来宾用户可以使用其自己的 IdP 管理的组织帐户登录到 Azure AD 租户并开始与你合作。 来宾用户无需创建单独的 Azure AD 帐户。
> [!NOTE]
> 直接联合身份验证来宾用户必须使用包含租户上下文的链接 (例如`https://myapps.microsoft.com/?tenantid=<tenant id>`或`https://portal.azure.com/<tenant id>`, 或在验证域`https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`的情况下) 登录。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 直接联合用户当前无法使用没有租户上下文的通用终结点登录。 例如, 使用`https://myapps.microsoft.com`、 `https://portal.azure.com`或`https://teams.microsoft.com`将导致错误。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>来宾用户何时使用直接联合身份验证？
设置与组织的直接联盟后, 邀请的任何新来宾用户将使用直接联合身份验证。 需要特别注意的是, 设置直接联合并不会更改已兑换你邀请的来宾用户的身份验证方法。 下面是一些可能的恶意活动：
 - 如果来宾用户已经兑换了你的邀请, 并且你随后设置了与组织的直接联盟, 则这些来宾用户将继续使用在设置直接联合身份验证之前使用的相同身份验证方法。
 - 如果设置与合作伙伴组织的直接联盟, 并邀请来宾用户, 然后合作伙伴组织随后移到 Azure AD, 则已兑换邀请的来宾用户将继续使用直接联盟, 前提是直接租户中的联合身份验证策略存在。
 - 如果删除与合作伙伴组织的直接联盟, 则当前使用直接联合的所有来宾用户将无法登录。

在上述任何情况下, 都可以通过从目录中删除来宾用户帐户并对其进行 reinviting 来更新来宾用户的身份验证方法。

直接联盟与域命名空间 (如 contoso.com 和 fabrikam.com) 相关联。 使用 AD FS 或第三方 IdP 建立直接联合身份验证时, 组织将一个或多个域命名空间与这些 Idp 相关联。 

## <a name="end-user-experience"></a>最终用户体验 
使用直接联合身份验证, 来宾用户使用其自己的组织帐户登录到你的 Azure AD 租户。 当用户访问共享资源并提示登录时, 直接联合用户会重定向到其 IdP。 成功登录后, 会返回到 Azure AD 访问资源。 直接联合身份验证用户的刷新令牌有效期为12小时, Azure AD 中[传递刷新令牌的默认长度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)。 如果联合 IdP 启用了 SSO, 用户将会体验到 SSO, 并在初始身份验证后不会看到任何登录提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 验证域
仅允许直接联盟 Azure AD 中***未***进行 DNS 验证的域。 允许直接联合身份验证 (通过电子邮件验证或 "病毒") Azure AD 租户, 因为未对其进行 DNS 验证。
### <a name="authentication-url"></a>身份验证 URL
仅当身份验证 URL 的域与目标域匹配或者身份验证 URL 是这些允许的标识提供者之一 (此列表可能会更改) 时, 才允许直接联合身份验证:
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如, 为**fabrikam.com**设置直接联合时, 身份验证 URL `https://fabrikam.com/adfs`将通过验证。 例如`https://sts.fabrikam.com/adfs`, 同一个域中的主机也将通过。 但是, 同一域的`https://fabrikamconglomerate.com/adfs`身份`https://fabrikam.com.uk/adfs`验证 URL 或不会通过。

### <a name="signing-certificate-renewal"></a>签名证书续订
如果在标识提供程序设置中指定元数据 URL, Azure AD 将在签名证书过期时自动续订该证书。 但是, 如果出于任何原因在过期时间之前旋转证书, 或未提供元数据 URL, Azure AD 将无法续订该证书。 在这种情况下, 你将需要手动更新签名证书。

### <a name="limit-on-federation-relationships"></a>联合关系限制
目前最多支持1000个联合关系。 此限制包括[内部联合](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)和直接联合。
## <a name="frequently-asked-questions"></a>常见问题
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>是否可以设置与非托管 (电子邮件验证) 租户所在的域的直接联合？ 
是的。 如果未验证域并且租户尚未完成[管理员接管](../users-groups-roles/domains-admin-takeover.md), 则可以设置直接联合。 未经管理或通过电子邮件验证的租户, 当用户兑换 B2B 邀请或使用当前不存在的域对 Azure AD 执行自助注册时, 会创建租户。 你可以设置与这些域的直接联合。 如果尝试通过 DNS 验证的域设置直接联合身份验证, 请在 "Azure 门户" 或 "通过 PowerShell" 中看到一个错误。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同时启用了直接联合身份验证和电子邮件一次性密码身份验证, 则哪个方法优先？
与合作伙伴组织建立直接联合时, 它将优先于该组织中的新来宾用户的电子邮件一次性密码身份验证。 如果来宾用户在设置直接联合之前使用一次性密码身份验证兑换了邀请, 则他们将继续使用一次性密码身份验证。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>是否由于部分同步的租户导致了直接联合地址登录问题？
不需要, 此方案中应使用[电子邮件一次性密码](one-time-passcode.md)功能。 "部分同步的租户" 指的是一个合作伙伴 Azure AD 租户, 其中本地用户标识未完全同步到云。 其标识尚不存在于云中但试图兑换 B2B 邀请的来宾将无法登录。 通过一次性密码功能, 此来宾可以登录。 直接联合身份验证功能解决了这样的情况: 来宾具有其自己的 IdP 托管帐户, 但组织根本没有 Azure AD 的状态。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>步骤 1：配置合作伙伴组织的标识提供者
首先, 你的合作伙伴组织需要为其标识提供者配置所需的声明和信赖方信任。 

> [!NOTE]
> 为了说明如何配置直接联合的标识提供程序, 我们将使用 Active Directory 联合身份验证服务 (AD FS) 作为示例。 请参阅 "[配置与 AD FS 的直接联合](direct-federation-adfs.md)" 一文, 其中提供了有关如何将 AD FS 配置为 SAML 2.0 或 ws-addressing 标识提供程序以准备直接联合的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 配置

Azure AD B2B 可以配置为与使用 SAML 协议的标识提供者联合, 该标识提供程序具有以下列出的特定要求。 有关在 SAML 标识提供者和 Azure AD 之间设置信任的详细信息, 请参阅[将 saml 2.0 标识提供者 (IdP) 用于单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 请注意, 不能在 Azure AD 上 DNS 验证直接联合的目标域。 身份验证 URL 域必须与目标域相匹配, 或者必须是允许的标识提供者的域。 有关详细信息, 请参阅[限制](#limitations)部分。 

#### <a name="required-saml-20-attributes-and-claims"></a>必需的 SAML 2.0 属性和声明
下表显示了必须在第三方标识提供程序中配置的特定属性和声明的要求。 若要设置直接联合身份验证, 必须从标识提供者的 SAML 2.0 响应接收以下属性。 可以通过链接到联机 security token service XML 文件或手动输入这些属性来配置这些属性。

来自 IdP 的 SAML 2.0 响应的必需属性:

|特性  |ReplTest1  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI, 例如`http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 颁发的 SAML 2.0 令牌所需的声明:

|特性  |值  |
|---------|---------|
|NameID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS 送送配置 
Azure AD B2B 可以配置为与使用 WS 送单协议的标识提供者联合, 其中列出了下面列出的特定要求。 目前, 两个 WS-ADDRESSING 提供程序已测试兼容性 Azure AD 包括 AD FS 和 Shibboleth。 有关在与 Azure AD 的 WS 兼容的提供程序之间建立信赖方信任的详细信息, 请参阅[Azure AD 标识提供程序兼容性文档](https://www.microsoft.com/download/details.aspx?id=56843)中提供的 "使用 ws 协议的 STS 集成白皮书"。

> [!NOTE]
> 在 Azure AD 上, 不能对直接联合的目标域进行 DNS 验证。 身份验证 URL 域必须与目标域或允许的标识提供者的域相匹配。 有关详细信息, 请参阅[限制](#limitations)部分。 

#### <a name="required-ws-fed-attributes-and-claims"></a>必需的 WS 馈送属性和声明

下表显示了必须在第三方 WS-FEDERATION 标识提供程序中配置的特定属性和声明的要求。 若要设置直接联合身份验证, 必须在来自标识提供程序的 WS 送送消息中接收下列属性。 可以通过链接到联机 security token service XML 文件或手动输入这些属性来配置这些属性。

来自 IdP 的 WS 送送消息中的必需属性:
 
|特性  |ReplTest1  |
|---------|---------|
|PassiveRequestorEndpoint     |`https://login.microsoftonline.com/login.srf`         |
|目标受众     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI, 例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-AT 令牌所需的声明:

|特性  |ReplTest1  |
|---------|---------|
|ImmutableID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>步骤 2：在 Azure AD 中配置直接联合 
接下来, 你将配置与 Azure AD 中的步骤1中配置的标识提供程序的联合。 可以使用 Azure AD 门户或 PowerShell。 直接联合身份验证策略生效之前可能需要5-10 分钟。 在此期间, 不要尝试兑换直接联盟域的邀请。 需要以下属性：
- Partner IdP 的颁发者 URI
- 合作伙伴 IdP 的被动身份验证终结点 (仅支持 https)
- 证书

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置直接联合

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择 "**标识提供者**", 然后选择 "**新建 SAML/WS-送 IdP**"。

    ![显示用于添加新 SAML 或 WS-ADDRESSING IdP 的按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp.png)

4. 在 "**新建 saml/WS 送纸 IdP** " 页的 "**标识提供者协议**" 下, 选择 " **SAML** " 或 " **WS 进纸**"。

    ![显示 SAML 或 WS-FEDERATION IdP 页上的 "分析" 按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 输入合作伙伴组织的域名, 该域名将是直接联合的目标域名
6. 您可以上载元数据文件以填充元数据详细信息。 如果选择手动输入元数据, 请输入以下信息:
   - 合作伙伴 IdP 的域名
   - 合作伙伴 IdP 的实体 ID
   - 合作伙伴 IdP 的被动请求者终结点
   - 证书
   > [!NOTE]
   > 元数据 URL 是可选的, 但我们强烈建议这样做。 如果提供元数据 URL, Azure AD 可以在签名证书过期时自动续订该证书。 如果出于任何原因在过期时间之前旋转证书, 或者未提供元数据 URL, 则 Azure AD 将无法续订该证书。 在这种情况下, 你将需要手动更新签名证书。

7. 选择**保存**。 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>使用 PowerShell 在 Azure AD 中配置直接联合

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 (如果需要详细步骤, 则添加来宾用户的快速入门包括[安装最新的 AzureADPreview 模块](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)部分。) 
2. 运行下面的命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 根据登录提示使用托管的全局管理员帐户登录。 
2. 运行以下命令, 并替换联合元数据文件中的值。 对于 AD FS Server 和 Okta, 联合文件为 federationmetadata.xml, 例如: `https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`。 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>步骤 3：测试 Azure AD 中的直接联合
现在, 邀请新的 B2B 来宾用户来测试直接联合身份验证设置。 有关详细信息, 请参阅[在 Azure 门户中添加 AZURE AD B2B 协作用户](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何实现编辑直接联合身份验证关系？

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择**标识提供者**
4. 在 " **SAML/WS 送纸标识提供**者" 下, 选择提供程序。
5. 在 "标识提供者详细信息" 窗格中, 更新值。
6. 选择**保存**。


## <a name="how-do-i-remove-direct-federation"></a>如何实现删除直接联合？
你可以删除直接联合身份验证设置。 如果这样做, 已经兑换邀请的直接联合来宾用户将无法登录。 不过, 你可以通过从目录中删除并 reinviting 资源来向用户授予对资源的访问权限。 在 Azure AD 门户中删除与标识提供者的直接联合:

1. 转到 [Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”  。 
2. 选择“组织关系”。 
3. 选择 "**标识提供者**"。
4. 选择标识提供程序, 然后选择 "**删除**"。 
5. 选择“是”  以确认删除。 

使用 PowerShell 删除与标识提供者的直接联合:
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
