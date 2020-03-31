---
title: 与 B2B - Azure AD 的标识提供程序直接联合
description: 与 SAML 或 WS-Fed 标识提供程序直接联合，以便来宾可以登录到 Azure AD 应用
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 02/27/2019
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2b99a80a90df8fcfc5efe6dfa0c2cd7e8e5e04e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80050868"
---
# <a name="direct-federation-with-ad-fs-and-third-party-providers-for-guest-users-preview"></a>与 AD FS 和第三方提供商直接联合，供来宾用户使用（预览版）
|     |
| --- |
| 直接联合是 Azure 活动目录的公共预览功能。 有关预览的详细信息，请参阅 Microsoft [Azure 预览的补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

本文介绍如何与其他组织建立 B2B 协作的直接联合。 您可以与标识提供程序 （IdP） 支持 SAML 2.0 或 WS-Fed 协议的任何组织建立直接联合。
与合作伙伴的 IdP 建立直接联合时，来自该域的新来宾用户可以使用自己的 IdP 托管组织帐户登录到 Azure AD 租户并开始与您协作。 来宾用户无需创建单独的 Azure AD 帐户。
> [!NOTE]
> 直接联合来宾用户必须使用包含租户上下文的链接（例如，`https://myapps.microsoft.com/?tenantid=<tenant id>`或`https://portal.azure.com/<tenant id>`，或 ，或在已验证域的情况下，）`https://myapps.microsoft.com/\<verified domain>.onmicrosoft.com`登录。 也可以使用应用程序和资源的直接链接，只要这些链接包含租户上下文即可。 直接联合用户当前无法使用没有租户上下文的公用终结点登录。 例如，使用`https://myapps.microsoft.com`或`https://portal.azure.com``https://teams.microsoft.com`将导致错误。
 
## <a name="when-is-a-guest-user-authenticated-with-direct-federation"></a>来宾用户何时通过直接联合进行身份验证？
与组织建立直接联合后，您邀请的任何新来宾用户都将使用直接联合身份验证。 请务必注意，设置直接联合不会更改已兑换您邀请的来宾用户的身份验证方法。 下面是一些示例：
 - 如果来宾用户已兑换您的邀请，并且您随后与其组织建立了直接联合，则这些来宾用户将继续使用他们在设置直接联合之前使用的相同身份验证方法。
 - 如果与合作伙伴组织建立直接联合并邀请来宾用户，然后合作伙伴组织稍后移动到 Azure AD，则已兑换邀请的来宾用户将继续使用直接联合，只要直接租户中存在联合策略。
 - 如果删除与合作伙伴组织的直接联合，当前使用直接联合的任何来宾用户将无法登录。

在上述任何方案中，您可以通过从目录中删除来宾用户帐户并重新邀请来宾用户的身份验证方法来更新它们。

直接联合绑定到域名空间，如contoso.com和fabrikam.com。 当使用 AD FS 或第三方 IdP 建立直接联合配置时，组织会将一个或多个域名空间与这些 IdP 相关联。 

## <a name="end-user-experience"></a>最终用户体验 
使用直接联合，来宾用户使用自己的组织帐户登录到 Azure AD 租户。 当他们访问共享资源并提示他们登录时，直接联合用户将重定向到其 IdP。 成功登录后，它们将返回到 Azure AD 以访问资源。 直接联合用户的刷新令牌的有效期为 12 小时，这是 Azure AD 中[传递刷新令牌的默认长度](../develop/active-directory-configurable-token-lifetimes.md#exceptions)。 如果联合 IdP 启用了 SSO，则用户将遇到 SSO，并在初始身份验证后看不到任何登录提示。

## <a name="limitations"></a>限制

### <a name="dns-verified-domains-in-azure-ad"></a>Azure AD 中的 DNS 验证域
要联合的域***不得***在 Azure AD 中进行 DNS 验证。 允许您设置与未托管（电子邮件验证或"病毒"）Azure AD 租户的直接联合，因为它们未经过 DNS 验证。

### <a name="authentication-url"></a>身份验证 URL
仅当身份验证 URL 的域与目标域匹配或身份验证 URL 是这些允许的标识提供程序之一的策略时，才允许直接联合（此列表可能会更改）：
-   accounts.google.com
-   pingidentity.com
-   login.pingone.com
-   okta.com
-   my.salesforce.com
-   federation.exostar.com
-   federation.exostartest.com

例如，在为**fabrikam.com**设置直接联合时，身份验证 URL`https://fabrikam.com/adfs`将通过验证。 同一域中的主机也将通过，例如`https://sts.fabrikam.com/adfs`。 但是，身份验证 URL `https://fabrikamconglomerate.com/adfs` `https://fabrikam.com.uk/adfs`或同一域不会通过。

### <a name="signing-certificate-renewal"></a>签名证书续订
如果在标识提供程序设置中指定元数据 URL，Azure AD 将在签名证书过期时自动续订。 但是，如果证书在过期时间之前由于任何原因进行旋转，或者未提供元数据 URL，则 Azure AD 将无法续订它。 在这种情况下，您需要手动更新签名证书。

### <a name="limit-on-federation-relationships"></a>限制联合关系
目前，最多支持 1，000 个联合关系。 此限制包括[内部联合和](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0)直接联合。

### <a name="limit-on-multiple-domains"></a>限制多个域
我们目前不支持与来自同一租户的多个域的直接联合。

## <a name="frequently-asked-questions"></a>常见问题
### <a name="can-i-set-up-direct-federation-with-a-domain-for-which-an-unmanaged-email-verified-tenant-exists"></a>我能否与存在非托管（电子邮件验证）租户的域建立直接联合？ 
是的。 如果域尚未验证，并且租户尚未进行[管理员接管](../users-groups-roles/domains-admin-takeover.md)，则可以与该域建立直接联合。 当用户兑换 B2B 邀请或使用当前不存在的域对 Azure AD 执行自助服务注册时，将创建未托管或电子邮件验证的租户。 您可以设置与这些域的直接联合。 如果尝试在 Azure 门户或通过 PowerShell 与 DNS 验证的域设置直接联合，您将看到错误。
### <a name="if-direct-federation-and-email-one-time-passcode-authentication-are-both-enabled-which-method-takes-precedence"></a>如果同时启用了直接联合和电子邮件一次性密码身份验证，则哪种方法优先？
当与合作伙伴组织建立直接联合时，它优先于来自该组织的新来宾用户的电子邮件一次性密码身份验证。 如果在您设置直接联合之前使用一次性密码身份验证兑换了邀请，他们将继续使用一次性密码身份验证。 
### <a name="does-direct-federation-address-sign-in-issues-due-to-a-partially-synced-tenancy"></a>由于部分同步的租约，直接联合身份验证地址登录问题是否？
否，在这种情况下应使用[电子邮件一次性密码](one-time-passcode.md)功能。 "部分同步租约"是指本地用户标识未完全同步到云的合作伙伴 Azure AD 租户。 其身份尚不存在的访客，但尝试兑换 B2B 邀请的客人将无法登录。 一次性密码功能将允许此来宾登录。 直接联合功能解决了来宾拥有自己的 IdP 托管组织帐户但组织根本没有 Azure AD 状态的情况。

## <a name="step-1-configure-the-partner-organizations-identity-provider"></a>第 1 步：配置合作伙伴组织的标识提供程序
首先，您的合作伙伴组织需要配置其标识提供程序与所需的声明和依赖方信任。 

> [!NOTE]
> 为了说明如何为直接联合配置标识提供程序，我们将使用 Active 目录联合服务 （AD FS） 作为示例。 请参阅文章["配置与 AD FS 的直接联合](direct-federation-adfs.md)"，其中提供了如何将 AD FS 配置为 SAML 2.0 或 WS-Fed 标识提供程序以准备直接联合的示例。

### <a name="saml-20-configuration"></a>SAML 2.0 配置

Azure AD B2B 可以配置为与使用 SAML 协议且具有下面列出的特定要求的标识提供程序联合。 有关在 SAML 标识提供程序和 Azure AD 之间设置信任的详细信息，请参阅[使用 SAML 2.0 标识提供程序 （IdP） 进行单一登录](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-saml-idp)。  

> [!NOTE]
> 直接联合的目标域不得在 Azure AD 上进行 DNS 验证。 身份验证 URL 域必须与目标域匹配，或者必须是允许的标识提供程序的域。 有关详细信息，请参阅[限制](#limitations)部分。 

#### <a name="required-saml-20-attributes-and-claims"></a>所需的 SAML 2.0 属性和声明
下表显示了必须在第三方标识提供程序配置的特定属性和声明的要求。 要设置直接联合，必须在标识提供程序的 SAML 2.0 响应中接收以下属性。 可以通过链接到在线安全令牌服务 XML 文件或手动输入它们来配置这些属性。

来自 IdP 的 SAML 2.0 响应所需的属性：

|特性  |“值”  |
|---------|---------|
|AssertionConsumerService     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |


IdP 颁发的 SAML 2.0 令牌所需的声明：

|特性  |“值”  |
|---------|---------|
|名称 ID 格式     |`urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

### <a name="ws-fed-configuration"></a>WS-Fed 配置 
Azure AD B2B 可以配置为与使用 WS-Fed 协议且具有某些特定要求的标识提供程序联合，如下所示。 目前，两个 WS-Fed 提供程序已测试与 Azure AD 的兼容性，包括 AD FS 和 Shibboleth。 有关使用 Azure AD 在符合 WS-Fed 的提供程序之间建立依赖方信任的详细信息，请参阅[Azure AD 标识提供程序兼容性文档](https://www.microsoft.com/download/details.aspx?id=56843)中提供的"使用 WS 协议的 STS 集成文件"。

> [!NOTE]
> 直接联合的目标域不得在 Azure AD 上进行 DNS 验证。 身份验证 URL 域必须与目标域或允许的标识提供程序的域匹配。 有关详细信息，请参阅[限制](#limitations)部分。 

#### <a name="required-ws-fed-attributes-and-claims"></a>所需的 WS-Fed 属性和声明

下表显示了必须在第三方 WS-Fed 标识提供程序配置的特定属性和声明的要求。 要设置直接联合，必须在来自标识提供程序的 WS-Fed 消息中接收以下属性。 可以通过链接到在线安全令牌服务 XML 文件或手动输入它们来配置这些属性。

来自 IdP 的 WS-Fed 消息中所需的属性：
 
|特性  |“值”  |
|---------|---------|
|被动请求者端点     |`https://login.microsoftonline.com/login.srf`         |
|读者     |`urn:federation:MicrosoftOnline`         |
|颁发者     |合作伙伴 IdP 的颁发者 URI，例如`http://www.example.com/exk10l6w90DHM0yi...`         |

IdP 颁发的 WS-Fed 令牌所需的声明：

|特性  |“值”  |
|---------|---------|
|不可改变ID     |`http://schemas.microsoft.com/LiveID/Federation/2008/05/ImmutableID`         |
|emailaddress     |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`         |

## <a name="step-2-configure-direct-federation-in-azure-ad"></a>第 2 步：在 Azure AD 中配置直接联合 
接下来，您将配置与 Azure AD 中步骤 1 中配置的标识提供程序的联合。 您可以使用 Azure AD 门户或 PowerShell。 可能需要 5-10 分钟才能使直接联合策略生效。 在此期间，不要尝试为直接联合域兑换邀请。 需要以下属性：
- 合作伙伴 IdP 的颁发者 URI
- 合作伙伴 IdP 的被动身份验证终结点（仅支持 https）
- 证书

### <a name="to-configure-direct-federation-in-the-azure-ad-portal"></a>在 Azure AD 门户中配置直接联合

1. 转到[Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”****。 
2. 选择“组织关系”。****
3. 选择**标识提供程序**，然后选择新的**SAML/WS-Fed IdP**。

    ![屏幕截图显示用于添加新 SAML 或 WS-Fed IdP 的按钮](media/direct-federation/new-saml-wsfed-idp.png)

4. 在新**SAML/WS-Fed IdP**页面上，在**标识提供程序协议**下，选择**SAML**或**WS-FED**。

    ![在 SAML 或 WS-Fed IdP 页面上显示解析按钮的屏幕截图](media/direct-federation/new-saml-wsfed-idp-parse.png)

5. 输入合作伙伴组织的域名，该域名将成为直接联合的目标域名
6. 您可以上载元数据文件以填充元数据详细信息。 如果选择手动输入元数据，请输入以下信息：
   - 合作伙伴 IdP 的域名
   - 合作伙伴 IdP 的实体 ID
   - 合作伙伴 IdP 的被动请求器终结点
   - 证书
   > [!NOTE]
   > 元数据 URL 是可选的，但我们强烈建议它。 如果提供元数据 URL，Azure AD 可以在签名证书过期时自动续订。 如果在过期时间之前出于任何原因旋转证书，或者未提供元数据 URL，则 Azure AD 将无法续订它。 在这种情况下，您需要手动更新签名证书。

7. 选择“保存”。**** 

### <a name="to-configure-direct-federation-in-azure-ad-using-powershell"></a>使用 PowerShell 在 Azure AD 中配置直接联合

1. 安装最新版本的 Azure AD PowerShell for Graph 模块 ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview))。 （如果需要详细步骤，添加来宾用户的快速入门包括[安装最新的 AzureAD 预览模块](b2b-quickstart-invite-powershell.md#install-the-latest-azureadpreview-module)的部分。 
2. 运行以下命令： 
   ```powershell
   Connect-AzureAD
   ```
1. 根据登录提示使用托管的全局管理员帐户登录。 
2. 运行以下命令，替换联合元数据文件中的值。 对于 AD FS 服务器和 Okta，联合文件为联合元数据.xml，`https://sts.totheclouddemo.com/federationmetadata/2007-06/federationmetadata.xml`例如： 。 

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

## <a name="step-3-test-direct-federation-in-azure-ad"></a>第 3 步：在 Azure AD 中测试直接联合
现在，通过邀请新的 B2B 来宾用户来测试直接联合设置。 有关详细信息，请参阅[在 Azure 门户中添加 Azure AD B2B 协作用户](add-users-administrator.md)。
 
## <a name="how-do-i-edit-a-direct-federation-relationship"></a>如何编辑直接联合关系？

1. 转到[Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”****。 
2. 选择“组织关系”。****
3. 选择**标识提供程序**
4. 在**SAML/WS-Fed 标识提供程序**下，选择提供程序。
5. 在标识提供程序详细信息窗格中，更新值。
6. 选择“保存”。****


## <a name="how-do-i-remove-direct-federation"></a>如何删除直接联合？
您可以删除直接联合设置。 如果兑换了，则已兑换其邀请的直接联合来宾用户将无法登录。 但是，您可以通过从目录中删除资源并重新邀请资源来再次授予他们对资源的访问权限。 要删除与 Azure AD 门户中的标识提供程序的直接联合：：

1. 转到[Azure 门户](https://portal.azure.com/)。 在左窗格中选择“Azure Active Directory”****。 
2. 选择“组织关系”。****
3. 选择**标识提供程序**。
4. 选择标识提供程序，然后选择 **"删除**"。 
5. 选择“是”**** 以确认删除。 

要使用 PowerShell 删除与标识提供程序的直接联合：
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
