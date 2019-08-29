---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: eross-msft
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 07/31/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: afab194c984dcbfa96a9342f46b892c7ec08d321
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70135672"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制并粘贴此 URL, 获取有关何时通过复制并粘贴此 URL 来`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`重新访问![此页面的通知](./media/whats-new/feed-icon-16x16.png) : 到 rss 源读者图标源读者。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 为代表的方案忽略 MSAL.NET 共享缓存

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

从 Azure AD 身份验证库 (ADAL.NET) 第5.0.0 版-预览版开始, 应用开发人员必须[为 web 应用和 Web api 每个帐户序列化一个缓存](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)。 否则, 某些使用代理[流](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的方案以及某些特定用例`UserAssertion`可能会导致权限提升。 为避免出现这种情况, ADAL.NET 会为代表方案忽略用于 dotnet (MSAL.NET) 共享缓存的 Microsoft 身份验证库。

有关此问题的详细信息, 请参阅[Azure Active Directory Authentication Library 提升权限漏洞](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>计划更改:应用程序代理服务更新, 仅支持 TLS 1。2

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

为了帮助提供最强大的加密, 我们将开始限制应用程序代理服务只能访问 TLS 1.2 协议。 此限制最初会向已使用 TLS 1.2 协议的客户推出, 因此你不会看到这种影响。 TLS 1.0 和 TLS 1.1 协议的完整弃用将于8月 2019 31 日完成。 仍在使用 TLS 1.0 和 TLS 1.1 的客户将收到有关为此更改做好准备的高级通知。

若要在此更改期间保持与应用程序代理服务的连接, 建议你确保客户端-服务器和浏览器-服务器组合已更新为使用 TLS 1.2。 我们还建议确保包含员工使用的任何客户端系统, 以访问通过应用程序代理服务发布的应用。

有关详细信息, 请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>计划更改:适用于应用程序库的设计更新

**类型：** 更改计划  
**服务类别：** 企业应用  
**产品功能：** SSO

新用户界面更改将从 "**添加应用程序**" 边栏选项卡的 **"库**" 区域中的 "添加" 设计。 这些更改将帮助你更轻松地查找支持自动预配、OpenID Connect、安全断言标记语言 (SAML) 和密码单一登录 (SSO) 的应用。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>计划更改:从 Office 365 IP 地址中删除 MFA 服务器 IP 地址

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全性和保护

我们正在从[Office 365 Ip 地址和 URL Web 服务](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)中删除 MFA 服务器的 ip 地址。 如果你当前依赖这些页面来更新防火墙设置, 则必须确保还包括 " **Azure 多重身份验证服务器防火墙要求**" 部分的 "入门" 中所述的 IP 地址列表[Azure 多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)一文。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>仅限应用的令牌现在要求客户端应用存在于资源租户中

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

2019年7月26日, 我们更改了我们通过[客户端凭据 grant](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)提供仅限应用的令牌的方式。 以前, 无论客户端应用是否在租户中, 应用都可以获取令牌来调用其他应用。 我们更新了此行为, 因此只能由资源租户中存在的客户端应用调用单租户资源 (有时称为 Web Api)。

如果你的应用程序不在资源租户中, 你将收到一条错误消息, `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.`指出, 若要解决此问题, 必须使用[管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)或[通过 PowerShell 在租户中创建客户端应用服务主体](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell), 确保你的租户已为租户提供了在租户内操作的权限。

有关详细信息, 请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 客户端与 API 之间的现有同意将继续进行。 应用仍应执行其自己的授权检查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>New 无密码使用 FIDO2 安全密钥登录到 Azure AD

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

Azure AD 客户现在可以设置策略以管理其组织的用户和组的 FIDO2 安全密钥。 最终用户还可以自行注册其安全密钥, 在 FIDO 支持的设备上使用密钥登录到网站上的 Microsoft 帐户, 以及登录到其 Azure AD 联接的 Windows 10 设备。

有关详细信息, 请参阅[Enable 无密码 sign in for Azure AD (预览版)](/azure/active-directory/authentication/concept-authentication-passwordless) , 以获取管理员相关信息, 并[将安全信息设置为对最终用户相关信息使用安全密钥 (预览)](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) 。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD 应用库中提供了新的联合应用程序-2019 年7月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年7月, 我们已将以下18个新应用和联合支持添加到应用库:

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial),[鲜模式对全渠道 Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial),[聪明 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial), [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial), [Looop](https://www.looop.co/schedule-a-demo/), [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial), [MS Azure SSO Access for Ethidex 相容 Office™](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso),[夸大宣传](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial), [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)、 [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)、 [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)、 [Wandera](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)、 [TwineSocial](https://twinesocial.com/)、 [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial)、 [HyperAnna](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial)、 [PharmID WasteWitness](https://www.pharmid.com/)、 [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)、 [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [联合目录](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息, 请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>网络安全组的新 Azure AD 域服务服务标记

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

如果你厌倦了管理 IP 地址和范围的长列表, 则可以使用 Azure 网络安全组中的 new **AzureActiveDirectoryDomainServices** network service 标记来帮助保护到 Azure AD 域服务虚拟的入站流量网络子网。

有关此新服务标记的详细信息, 请参阅[Azure AD 域服务的网络安全组](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核 (公共预览版)

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务将安全审核事件流式传输到目标资源 (包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心), 安全审核可帮助你深入了解身份验证服务端口.

有关详细信息, 请参阅[为 Azure AD 域服务 (预览版) 启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新的身份验证方法用法 & insights (公共预览版)

**类型：** 新功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 监视和报告

使用 & insights 报表的新的身份验证方法, 可帮助你了解 Azure 多重身份验证和自助密码重置等功能在你的组织中的注册和使用方式, 包括已注册的数量每项功能的用户、使用自助服务密码重置重置密码的频率以及重置的方式。

有关详细信息, 请参阅[身份验证方法用法 & insights (预览版)](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 管理员都可以使用新的安全报告 (公共预览版)

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

所有 Azure AD 管理员现在可以选择现有安全报告 (如**标记为风险**报告的用户) 顶部的横幅, 以开始使用有**风险的用户**和有**风险的登录**报告中所示的新安全体验. 随着时间的推移, 所有安全报告都将从较旧的版本转移到新版本, 同时提供了以下附加功能:

- 高级筛选和排序

- 大容量操作, 如消除用户风险

- 确认泄露或安全实体

- 风险状态, 涵盖:有风险, 已解除、修正和确认已泄露

有关详细信息, 请参阅有[风险的用户报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-users-report)和有[风险的登录报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risky-users-signins#risky-sign-ins-report)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核 (公共预览版)

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务将安全审核事件流式传输到目标资源 (包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心), 安全审核可帮助你深入了解身份验证服务端口.

有关详细信息, 请参阅[为 Azure AD 域服务 (预览版) 启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS 进纸进行新的 B2B 直接联合身份验证 (公共预览版)

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C

通过使用支持 SAML 或 WS 送标准的标识系统, 直接联合身份验证可让你更轻松地使用 IT 托管标识解决方案不 Azure AD 的合作伙伴。 设置与合作伙伴的直接联合身份验证关系后, 你从该域邀请的任何新来宾用户都可以使用其现有组织帐户与你协作, 使你的来宾的用户体验更顺畅。

有关详细信息, 请参阅[guest 用户的直接联合身份验证 AD FS 和第三方提供程序 (预览)](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户:

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [联合目录](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息, 请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>新检查 Azure AD 门户中的重复组名称

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

现在, 当你在 Azure AD 门户中创建或更新组名称时, 我们将执行检查以查看你是否在资源中复制现有组名。 如果我们确定名称已被另一个组使用, 系统会要求你修改名称。

有关详细信息, 请参阅[Azure AD 门户中的 "管理组"](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>现在 Azure AD 在答复 (重定向) Uri 中支持静态查询参数

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

Azure AD 应用现在可以使用静态查询参数 (例如, `https://contoso.com/oauth2?idp=microsoft`) 为 OAuth 2.0 请求注册和使用回复 (重定向) uri。 静态查询参数服从于回复 URI 的字符串匹配, 就像回复 URI 的任何其他部分一样。 如果没有任何已注册的字符串与 URL 解码的重定向 uri 匹配, 则会拒绝该请求。 如果找到了回复 URI, 则使用整个字符串重定向用户, 包括静态查询参数。

动态回复 Uri 仍被禁止, 因为它们表示安全风险, 不能用于在身份验证请求中保留状态信息。 为此, 请使用`state`参数。

目前, Azure 门户的应用注册屏幕仍会阻止查询参数。 但是, 您可以手动编辑应用程序清单以在应用程序中添加和测试查询参数。 有关详细信息, 请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>现在可通过 PowerShell Cmdlet 使用 Azure AD 的活动日志 (MS Graph Api)

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们很高兴地宣布 Azure AD 活动日志 (审核和登录报告) 现已通过 Azure AD PowerShell 模块提供。 以前, 你可以使用 MS 图形 API 终结点创建自己的脚本, 现在我们已将该功能扩展到 PowerShell cmdlet。

有关如何使用这些 cmdlet 的详细信息, 请参阅[Azure AD PowerShell cmdlet 用于报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>更新了 Azure AD 中的审核和登录日志的筛选器控件

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们更新了审核和登录日志报告, 因此你现在可以应用多个筛选器, 而无需将它们作为列添加到报表屏幕中。 此外, 您现在可以决定要在屏幕上显示多少个筛选器。 这些更新将全部工作, 以使报表更易于阅读, 并使其更适合你的需求。

有关这些更新的详细信息, 请参阅[筛选审核日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)和[筛选登录活动](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 7 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>适用于 Microsoft Graph 的新 riskDetections API (公共预览版)

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

我们很高兴地宣布推出新的 riskDetections API, 以便 Microsoft Graph 现在为公共预览版。 可以使用此新 API 查看组织的标识保护相关用户和登录风险检测的列表。 你还可以使用此 API 更有效地查询你的风险检测, 包括有关检测类型、状态和级别等的详细信息。

有关详细信息, 请参阅[风险检测 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年6月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年6月, 我们已将这22个新应用和联合支持添加到应用库:

[AZURE AD SAML 工具包](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)、 [Otsuka Shokai (大塚商会)](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、 [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、 [Azure VPN 客户端](https://portal.azure.com/)、 [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、 [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、 [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)、 [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)、 [Mercedes](https://me.secure.mercedes-benz.com/)、Benz、[Skore](https://app.justskore.it/)、[Oracle 云基础结构控制台](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial), [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial), [Scrible Edu](https://www.scrible.com/sign-in/#/create-account), [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial), [Perceptyx](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial), [Proptimise OS](https://proptimise.co.uk/software/), [Vtiger CRM (SAML)](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial), oracle Access Manager for oracle零售促销营销, oracle Access Manager for Oracle 电子商务套件, oracle IDCS for 电子商务套件, Oracle IDCS for PeopleSoft, Oracle IDCS for JD Edwards

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户:

- [Zoom](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息, 请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 预配服务的实时进度

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

我们更新了 Azure AD 预配体验, 以包含一个新的进度栏, 显示你在用户预配过程中的距离。 此更新的体验还提供了有关在当前周期中预配的用户数以及已设置为 "日期" 的用户数的信息。

有关详细信息, 请参阅[检查用户预配的状态](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司品牌现在显示在 "注销" 和 "错误" 屏幕上

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

我们更新了 Azure AD, 使公司品牌现在显示在 "注销" 和 "错误" 屏幕以及登录页面上。 无需执行任何操作即可启用此功能, Azure AD 只需使用已在 Azure 门户的 "**公司品牌**" 区域中设置的资产。

有关设置公司品牌的详细信息, 请参阅[将品牌添加到组织的 Azure Active Directory 页面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure 多重身份验证 (MFA) 服务器不再适用于新部署

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全性和保护

从2019年7月1日起, Microsoft 将不再为新部署提供 MFA 服务器。 希望在其组织中需要多重身份验证的新客户现在必须使用基于云的 Azure 多重身份验证。 在7月1日前激活 MFA 服务器的客户不会看到更改。 你仍可以下载最新版本, 获取将来的更新, 并生成激活凭据。

有关详细信息, 请参阅[Azure 多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)入门。 有关基于云的 Azure 多重身份验证的详细信息, 请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服务更改:未来仅支持应用程序代理服务上的 TLS 1.2 协议

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

为帮助我们的客户提供一流的加密, 我们仅限制对应用程序代理服务上的 TLS 1.2 协议的访问。 此更改将逐步推出给已仅使用 TLS 1.2 协议的客户, 因此你不会看到任何更改。

弃用 TLS 1.0 和 TLS 1.1 的情况在8月 2019 31 日发生, 但我们将提供额外的高级通知, 因此你有时间为此更改做好准备。 为准备此更改, 请确保客户端服务器和浏览器-服务器组合 (包括用户用于访问通过应用程序代理发布的应用的任何客户端) 都已更新为使用 TLS 1.2 协议来维护与应用程序的连接代理服务。 有关详细信息, 请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用使用量和 insights 报表查看与应用相关的登录数据

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

你现在可以使用 "Azure 门户的"**企业应用程序**"区域中的" 使用情况和 insights "报表, 以应用程序为中心查看登录数据, 其中包括有关以下内容的信息:

- 适用于你的组织的热门应用

- 登录失败次数最多的应用程序

- 每个应用的最常见登录错误

有关此功能的详细信息, 请参阅[Azure Active Directory 门户中的使用情况和见解报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 自动完成用户预配到云应用

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 监视和报告

请按照以下新教程使用 Azure AD 预配服务, 为以下基于云的应用自动创建、删除和更新用户帐户:

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

你还可以遵循这个新的[Dropbox 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial), 该教程提供了有关如何预配组对象的信息。

有关如何通过自动用户帐户预配更好地保护组织的详细信息, 请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>标识安全分数现已在 Azure AD 中提供 (正式发布)

**类型：** 新功能  
**服务类别：** 不可用  
**产品功能：** 标识安全性和保护

你现在可以通过使用 Azure AD 中的 "标识" 安全分数功能来监视和改善标识安全状况。 标识安全分数功能使用单个仪表板来帮助你:

- 根据1到223之间的评分, 客观地度量标识的安全状况。

- 规划标识安全改进

- 查看安全改进的成功率

有关标识安全分数功能的详细信息, 请参阅 " [Azure Active Directory 中的标识安全分数是多少？"](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>现已推出新的应用注册体验 (正式发布)

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 开发人员体验

全新的[应用注册](https://aka.ms/appregistrations)体验现已正式推出。 这种新体验包括你在 Azure 门户和应用程序注册门户中熟悉的所有关键功能, 并通过以下方式改善它们:

- **更好地管理应用程序。** 你现在可以在一个位置看到所有应用, 而不是跨不同门户查看应用。

- **简化的应用注册。** 从改进的导航体验到改进权限选择体验, 现在可以更轻松地注册和管理应用。

- **更多详细信息。** 您可以找到有关您的应用程序的更多详细信息, 包括快速入门指南等。

有关详细信息, 请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)和[应用注册体验现已正式发布!](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 博客公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>用于标识保护的有风险用户 API 中可用的新功能

**类型：** 新功能  
**服务类别：** Identity Protection  
**产品功能：** 标识安全性和保护

我们很高兴地宣布, 你现在可以使用有风险的用户 API 来检索用户的风险历史记录、解除有风险的用户以及确认用户是否受到侵害。 此更改有助于更有效地更新用户的风险状态并了解其风险历史记录。

有关详细信息, 请参阅有[风险的用户 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 应用库中提供了新的联合应用-可能为2019

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

在5月 2019, 我们已将这21个新应用和联合支持添加到应用库:

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial), [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial), [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/),[简单符号](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial), [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial), [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial), [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial), [Marketo 销售接洽](https://toutapp.com/login), [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial), [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial), [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial),[量子工作区](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial),[钴](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial), [webMethods API Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial), [RedFlag](https://pocketstop.com/redflag/), [Whatfix](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial), [Control](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial), [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial), [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial), [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial), [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>在 Azure AD 门户中改进了组的创建和管理体验

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们已改进了 Azure AD 门户中与组相关的体验。 这些改进使管理员能够更好地管理组列表、成员列表, 并提供其他创建选项。

改进包括：

- 按成员身份类型和组类型进行的基本筛选。

- 添加新列, 如源和电子邮件地址。

- 可以选择多个组、成员和所有者列表以便于删除。

- 可以选择电子邮件地址, 并在创建组的过程中添加所有者。

有关详细信息, 请参阅[使用 Azure Active Directory 创建基本组和添加成员](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 门户中为 Office 365 组配置命名策略 (正式发布)

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略:

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词, 这些字词在组名称中是不允许的 (例如, "CEO, 工资单, HR")。

有关详细信息, 请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 终结点现可用于 Azure AD 活动日志 (正式发布)

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们非常高兴地宣布 Microsoft Graph API 终结点支持 Azure AD 活动日志。 在此版本中, 现在可以使用 Azure AD 审核日志的1.0 版, 以及登录日志 Api。

有关详细信息, 请参阅[Azure AD 审核日志 API 概述](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理员现在可以将条件访问用于合并的注册过程 (公共预览版)

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护  

管理员现在可以创建条件性访问策略以供组合注册页面使用。 这包括在以下情况下应用策略以允许注册:

- 用户处于受信任的网络中。

- 用户的登录风险较低。

- 用户在被管理的设备上。

- 用户同意组织的使用条款 (TOU)。

有关条件访问和密码重置的详细信息, 请参阅[Azure AD 组合 MFA 和密码重置注册体验博客文章的条件性访问](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 有关组合注册过程的条件性访问策略的详细信息, 请参阅[组合注册的条件性访问策略](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 有关 Azure AD 使用条款功能的详细信息, 请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新 Azure AD 威胁情报检测现在作为 Azure AD Identity Protection 的一部分提供

**类型：** 新功能  
**服务类别：** Azure AD Identity Protection  
**产品功能：** 标识安全性和保护

Azure AD 威胁情报检测现在作为更新的 Azure AD Identity Protection 功能的一部分提供。 这一新功能可以帮助为特定用户或活动指出异常的用户活动, 该活动与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致。

有关 Azure AD Identity Protection 的刷新版本的详细信息, 请参阅[公共预览博客中的四个主要 Azure AD Identity Protection 增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935), 以及[Azure Active Directory Identity Protection (已刷新) 是什么？](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 下文. 有关 Azure AD 威胁智能检测的详细信息, 请参阅[Azure Active Directory Identity Protection 风险检测](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 授权管理现已推出 (公共预览版)

**类型：** 新功能  
**服务类别：** 标识监管  
**产品功能：** 标识监管

Azure AD 的权利管理, 现在提供公共预览版, 可帮助客户委托访问包的管理, 该管理包定义了员工和业务合作伙伴如何请求访问权限、必须批准的人员以及他们有权访问的时间长短。 访问包可以管理 Azure AD 和 Office 365 组中的成员身份、企业应用程序中的角色分配以及 SharePoint Online 网站的角色分配。 有关权利管理的详细信息, 请参阅[Azure AD 的权利管理概述](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要详细了解 Azure AD Identity Governance 功能的广度, 包括 Privileged Identity Management、访问评审和使用条款, 请参阅[什么是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 门户中为 Office 365 组配置命名策略 (公共预览版)

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略:

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词, 这些字词在组名称中是不允许的 (例如, "CEO, 工资单, HR")。

有关详细信息, 请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活动日志现在 Azure Monitor 中可用 (公开上市)

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

为了帮助解决有关使用 Azure AD 活动日志的可视化效果的反馈, 我们在 Log Analytics 中引入了一个新的 Insights 功能。 此功能可帮助你使用称为工作簿的交互式模板获取有关 Azure AD 资源的见解。 这些预建工作簿可以提供应用程序或用户的详细信息, 包括:

- **登录。** 提供应用和用户的详细信息, 包括登录位置、正在使用的操作系统或浏览器客户端和版本, 以及成功或失败的登录数。

- **旧身份验证和条件访问。** 提供有关使用旧身份验证的应用和用户的详细信息, 包括条件访问策略触发的多重身份验证使用情况、使用条件性访问策略的应用等。

- **登录失败分析。** 帮助你确定登录错误是否是由于用户操作、策略问题或基础结构而发生的。

- **自定义报表。** 你可以创建新的工作簿或编辑现有工作簿, 以帮助为你的组织自定义 Insights 功能。

有关详细信息, 请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年4月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年4月, 我们已向应用程序库添加了以下21个具有联合支持的新应用:

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial), [HRworks 单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial), [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial), [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial), [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial), [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial), [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial), [MileIQ](https://mileiq.onelink.me/991934284/7e980085), [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial), [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial), [RStudio 连接](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)、 [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)、 [Mitel Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)、 [ALIBABA Cloud (基于角色的 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)、 [Certent 权益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)、 [Sectigo 证书管理器](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)、 [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)、 [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)、 [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)、[SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)、 [Indiggo](https://indiggolead.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的访问评审频率选项和多个角色选择

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识监管

Azure AD 访问评审中的新更新可让你:

- 除了每周、每月、每季度和每年的现有选项外, 还可以将访问评审的频率更改为**半年**。

- 创建单个访问评审时, 请选择多个 Azure AD 和 Azure 资源角色。 在这种情况下, 所有角色均设置为具有相同的设置, 并同时通知所有审阅者。

有关如何创建访问评审的详细信息, 请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 电子邮件警报系统正在转换, 为某些客户发送新的电子邮件发件人信息

**类型：** 已更改的功能  
**服务类别：** AD Sync  
**产品功能：** 平台

Azure AD Connect 正在转换电子邮件警报系统, 可能会向某些客户显示新的电子邮件发件人。 若要解决此情况, 你`azure-noreply@microsoft.com`必须将添加到你的组织的允许列表, 否则无法继续接收来自 Office 365、Azure 或你的同步服务的重要警报。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN 后缀更改现在在 Azure AD Connect 中的联合域之间已成功完成

**类型：** 已修复  
**服务类别：** AD Sync  
**产品功能：** 平台

你现在可以成功地将用户的 UPN 后缀从一个联合域更改为 Azure AD Connect 中的另一个联合域。 此修补程序意味着在同步周期内您不会再遇到 FederatedDomainChangeError 错误消息, 也不会收到通知电子邮件, 指出, "无法在 Azure Active Directory 中更新此对象, 因为属性 [FederatedUser] 无效。 更新本地目录服务中的值。

有关详细信息, 请参阅[在同步过程中解决错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD 中使用基于应用保护的条件性访问策略提高了安全性 (公共预览版)

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护

现在, 可以使用 "**需要应用保护**策略" 来使用基于应用保护的条件性访问。 这一新策略有助于防止以下情况, 从而提高组织的安全性:

- 无 Microsoft Intune 许可证即可访问应用的用户。

- 用户无法获取 Microsoft Intune 的应用保护策略。

- 用户访问应用时, 无需配置 Microsoft Intune 应用保护策略。

有关详细信息, 请参阅[如何使用条件性访问来要求适用于云应用访问的应用保护策略](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中对 Azure AD 单一登录和条件访问的全新支持 (公共预览版)

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护

我们已增强了对 Microsoft Edge 的 Azure AD 支持, 包括提供对 Azure AD 单一登录和条件访问的全新支持。 如果以前使用过 Microsoft Intune Managed Browser, 现在可以改为使用 Microsoft Edge。

有关使用条件性访问设置和管理设备和应用的详细信息, 请参阅要求使用条件性访问的[云应用访问托管设备](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices), 并[需要使用条件性访问的批准的客户端应用进行云应用访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access). 若要详细了解如何使用 Microsoft Intune 策略来管理使用 Microsoft Edge 的访问权限, 请参阅[使用 Microsoft Intune 策略保护的浏览器管理 Internet 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 中的标识体验框架和自定义策略支持现已推出 (GA)

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

你现在可以在 Azure AD B2C 中创建自定义策略, 其中包括以下任务, 这些任务是按比例支持的, 并且在我们的 Azure SLA 下提供:

- 使用自定义策略创建并上传自定义身份验证用户旅程。

- 将用户旅程逐步描述为声明提供程序之间的交换。

- 在用户旅程中定义条件分支。

- 转换和映射要在实时决策和通信中使用的声明。

- 在自定义身份验证用户旅程中使用启用 REST API 的服务。 例如, 通过电子邮件提供商、Crm 和专有授权系统。

- 与兼容 OpenIDConnect 协议的标识提供者联合。 例如, 通过多租户 Azure AD、社交帐户提供程序或双因素验证提供程序。

有关创建自定义策略的详细信息, 请参阅[Azure Active Directory B2C 中自定义策略的开发人员说明](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom), 并阅读[Alex Simon 的博客文章, 包括案例研究](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年3月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年3月向应用程序库添加了这14个新应用和联合支持:

[ISEC7 移动 Exchange 委托](https://www.isec7.com/english/), [MediusFlow](https://office365.cloudapp.mediusflow.com/), [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial), [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial), [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial),[基于解释的审核系统](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial),[精益](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial), [Powerschool 性能问题](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial), [Cinode](https://cinode.com/), [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial), [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial), [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial), [Confirmit 视野](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial), [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD 库中的新的 Zscaler 和 Atlassian 预配连接器-2019 年3月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

自动创建、更新和删除以下应用的用户帐户:

[Zscaler](https://aka.ms/ZscalerProvisioning), [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning), [Zscaler One](https://aka.ms/ZscalerOneProvisioning), [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning), [Zscaler 3](https://aka.ms/ZscalerThreeProvisioning), [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning), [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

有关如何通过自动用户帐户预配更好地保护组织的详细信息, 请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://aka.ms/ProvisioningDocumentation)。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>在 Azure AD 门户中还原和管理已删除的 Office 365 组

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

你现在可以从 Azure AD 门户查看和管理已删除的 Office 365 组。 此更改可帮助你查看可还原哪些组, 并允许你永久删除你的组织不需要的任何组。

有关详细信息, 请参阅[还原过期或删除的组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>单一登录现在可用于通过应用程序代理 Azure AD SAML 保护的本地应用 (公共预览版)

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

你现在可以为本地的、通过身份验证的应用提供单一登录 (SSO) 体验, 还可以通过应用程序代理对这些应用进行远程访问。 有关如何为本地应用设置 SAML SSO 的详细信息, 请参阅[具有应用程序代理 (预览版) 的本地应用程序的 saml 单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>将中断请求循环中的客户端应用, 以提高可靠性和用户体验

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

客户端应用程序可能会在短时间内错误地发出数百个相同的登录请求。 这些请求 (无论是否成功) 都对 IDP 的用户体验和提升的工作负荷产生了影响, 增加了所有用户的延迟, 降低了 IDP 的可用性。

此更新向客户`invalid_grant`端应用`AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`发送错误: 在短时间内多次发出重复请求, 超出正常操作范围。 遇到此问题的客户端应用应显示交互式提示, 要求用户重新登录。 有关此更改以及在遇到此错误时如何修复应用的详细信息, 请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>现已推出新的审核日志用户体验

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们创建了一个新的 Azure AD**审核日志**"页, 以帮助提高可读性和搜索信息的方式。 若要查看新的 "**审核日志**" 页, 请在 Azure AD 的 "**活动**" 部分中选择 "**审核日志**"。

![新的 "审核日志" 页, 包含示例信息](media/whats-new/audit-logs-page.png)

有关新的 "**审核日志**" 页的详细信息, 请参阅[Azure Active Directory 门户中的审核活动报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指南, 可帮助防止意外管理员从错误配置的条件访问策略中锁定

**类型：** 已更改的功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全性和保护

为了帮助防止管理员因配置错误的条件性访问策略而意外地锁定自己的租户, 我们在 Azure 门户中创建了新的警告和更新指南。 有关新指南的详细信息, 请参阅[什么是服务依赖关系 Azure Active Directory 条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>改善了移动设备上最终用户的使用体验

**类型：** 已更改的功能  
**服务类别：** 使用条款  
**产品功能：** 调控

我们更新了现有的使用条款, 以帮助改进你查看和同意移动设备上使用条款的方式。 现在可以放大和缩小, 返回, 下载信息, 并选择 "超链接"。 有关更新的使用条款的详细信息, 请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新 Azure AD 活动日志下载体验可用

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

你现在可以直接从 Azure 门户下载大量活动日志。 此更新可让你:

- 下载最多250000行。

- 下载完成后获取通知。

- 自定义文件名。

- 确定输出格式, 可以是 JSON 或 CSV。

有关此功能的详细信息, 请[参阅快速入门:使用 Azure 门户下载审核报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大更改：通过 Exchange ActiveSync (EAS) 对条件评估进行的更新

**类型：** 更改计划  
**服务类别：** 条件访问  
**产品功能：** 访问控制

我们正在更新 Exchange ActiveSync (EAS) 如何评估下列条件:

- 用户位置, 基于国家/地区、区域或 IP 地址

- 登录风险

- 设备平台

如果以前在条件性访问策略中使用这些条件, 请注意条件行为可能会发生更改。 例如, 如果你以前在策略中使用了用户位置条件, 你可能会发现现在会根据用户的位置跳过策略。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可配置 Azure AD SAML 令牌加密(公共预览版) 

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

你现在可以配置任何受支持的 SAML 应用以接收加密的 SAML 令牌。 当配置并与应用一起使用时, Azure AD 使用从存储在 Azure AD 中的证书获取的公钥来加密发出的 SAML 断言。

有关配置 SAML 令牌加密的详细信息, 请参阅[Configure AZURE AD saml token encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 访问评审创建组或应用的访问评审

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 调控

你现在可以将多个组或应用添加到组成员身份或应用分配的单个 Azure AD 访问评审中。 将使用相同的设置设置访问评审, 并同时通知所有包含的审阅者。

有关如何使用 Azure AD 访问评审创建访问评审的详细信息, 请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2019年2月, 我们已将这27个新应用和联合支持添加到应用库:

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial), [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial), [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7), [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial), [Oracle 合成 ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial), [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial), [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial), [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial), [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial), [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial),权限单击, [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial), [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial), [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial), [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial),[地震](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial),[共享梦想](https://www.shareadream.org/how-it-works), [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial), [webMethods Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial),[知识任何地方 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial)、 [OU 校园](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)、 [Periscope 数据](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)、 [Netop 门户](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)、 [Smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial)、 [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)、 [ClickUp 生产力平台](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增强了组合 MFA/SSPR 注册

**类型：** 已更改的功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 用户身份验证

为了响应客户反馈, 我们增强了合并的 MFA/SSPR 注册预览体验, 帮助用户更快地注册 MFA 和 SSPR 的安全信息。 

**若要为你的用户提供增强的体验, 请执行以下步骤:**

1. 以全局管理员或用户管理员身份登录到 Azure 门户, 并**Azure Active Directory > "用户设置" > "管理访问面板预览功能的设置"** 。 

2. 在**可以使用 "注册和管理安全信息的预览功能-刷新**" 选项的用户中, 选择为**选定的一组用户**或为**所有用户**启用功能。

在接下来的几周, 我们将删除为尚未打开的租户启用旧的组合 MFA/SSPR 注册预览体验的功能。

**若要查看是否将为租户删除控件, 请执行以下步骤:**

1. 以全局管理员或用户管理员身份登录到 Azure 门户, 并**Azure Active Directory > "用户设置" > "管理访问面板预览功能的设置"** 。  

2. 如果**可以使用 "注册和管理安全信息的预览功能**" 选项的用户设置为 "**无**", 则将从租户中删除该选项。

无论先前是否为用户启用了旧组合的 MFA/SSPR 注册预览体验, 都将在将来的某个日期关闭旧体验。 为此, 我们强烈建议尽快迁移到新的增强体验。

有关增强的注册体验的详细信息, 请参阅[Azure AD 结合了 MFA 和密码重置注册体验中的超酷增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>更新了用户流的策略管理体验

**类型：** 已更改的功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

我们更新了用户流 (以前称为内置策略) 的策略创建和管理过程。 这种新体验现在是所有 Azure AD 租户的默认体验。

可以通过使用门户屏幕顶部的 "**向我们发送反馈**" 区域中的笑脸或哭脸图标来提供其他反馈和建议。

有关新策略管理体验的详细信息, 请参阅[Azure AD B2C 现在有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>选择由 Azure AD B2C 提供的特定页元素版本

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

你现在可以选择 Azure AD B2C 提供的页面元素的特定版本。 通过选择特定版本, 你可以在更新出现在页面上之前对其进行测试, 你可以获得可预测的行为。 此外, 你现在可以选择强制实施特定页面版本以允许 JavaScript 自定义。 若要启用此功能, 请转到用户流中的 "**属性**" 页。

有关选择页面元素特定版本的详细信息, 请参阅 " [Azure AD B2C 现在有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>可配置的 B2C (GA) 最终用户密码要求

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

你现在可以为最终用户设置组织的密码复杂性, 而不必使用本机 Azure AD 密码策略。 从用户流的 "**属性**" 边栏选项卡 (以前称为内置策略), 可以选择**简单**或**强**的密码复杂性, 也可以创建一组**自定义**的要求。

有关密码复杂性要求配置的详细信息, 请参阅[在 Azure Active Directory B2C 中配置密码的复杂性要求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>自定义品牌的身份验证体验的新的默认模板

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

你可以使用新的默认模板, 这些模板位于用户流 (以前称为内置策略) 的 "**页面布局**" 边栏选项卡上, 为用户创建自定义品牌的身份验证体验。

有关使用模板的详细信息, 请参阅[Azure AD B2C 现在具有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---
