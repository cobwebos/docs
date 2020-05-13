---
title: 存档 Azure Active Directory 中的新增功能 | Microsoft Docs
description: 此内容集的“概述”部分中的新增功能发行说明包含 6 个月的活动。 6 个月过后，这些项目将从主文章中删除并放入此存档文章中。
services: active-directory
author: msmimart
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro, seo-update-azuread-jan, has-adal-ref
ms.collection: M365-identity-device-management
ms.openlocfilehash: b117309a3ccc9893890d82c2d0b0d2cb0c6e7bca
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83197889"
---
# <a name="archive-for-whats-new-in-azure-active-directory"></a>存档 Azure Active Directory 中的新增功能

[Azure Active Directory 中的新增功能？发行说明](whats-new.md)文章包含过去六个月的更新，而本文包含所有旧的信息。

Azure Active Directory 中有哪些新功能？发行说明提供了有关以下内容的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>弃用用于 Azure AD Identity Protection 风险检测的 identityRiskEvent API

**键入：** 规划更改**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

为了应对开发人员的反馈，Azure AD Premium P2 订户现在可以使用适用于 Microsoft Graph 的新的 riskDetection API 对 Azure AD Identity Protection 的风险检测数据执行复杂的查询。 现有的[identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API beta 版本将停止返回**2020 年1月 10**日附近的数据。 如果你的组织正在使用 identityRiskEvent API，你应转换为新的 riskDetection API。

有关新的 riskDetection API 的详细信息，请参阅[风险检测 api 参考文档](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>应用程序代理支持 SameSite 属性和 Chrome 80

**键入：** 规划更改**服务类别：** 应用代理**产品功能：** 访问控制

在 Chrome 80 浏览器发布之前的几周，我们计划更新应用程序代理 cookie 如何处理**SameSite**属性。 在版本的 Chrome 80 中，未指定**SameSite**属性的任何 cookie 都将被视为已设置为 `SameSite=Lax` 。

为了帮助避免由于此更改而产生的负面影响，我们将通过以下方式更新应用程序代理访问和会话 cookie：

- 将 "**使用安全 Cookie** " 设置的默认值设置为 **"是"**。

- 将**SameSite**属性的默认值设置为**None**。

    >[!NOTE]
    > 应用程序代理访问 cookie 始终是通过安全通道完全传输的。 这些更改仅适用于会话 cookie。

有关应用程序代理 cookie 设置的详细信息，请参阅[Azure Active Directory 中用于访问本地应用程序的 cookie 设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-app-management-in-the-application-registration-portal-appsdevmicrosoftcom-is-no-longer-available"></a>应用程序注册门户（apps.dev.microsoft.com）中的应用注册（旧）和应用管理功能不再可用

**键入：** 规划更改**服务类别：** N/A**产品功能：** 开发人员体验

具有 Azure AD 帐户的用户不能再使用应用程序注册门户（apps.dev.microsoft.com）注册或管理应用程序，也不能在 Azure 门户的应用注册（旧）体验中注册和管理应用程序。

若要了解有关新应用注册体验的详细信息，请参阅[Azure 门户培训指南中的应用注册](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>在从每个用户 MFA 到基于条件访问的 MFA 的迁移过程中，不再需要用户重新注册

**键入：** 已修复的**服务类别：** MFA**产品功能：** 标识安全 & 保护

我们修复了一个已知问题，即，如果用户被禁用按用户多重身份验证（MFA），然后通过条件性访问策略启用了 MFA，则用户需要重新注册。

若要要求用户重新注册，可以在 Azure AD 门户中的用户身份验证方法中选择**所需的 "重新注册 MFA** " 选项。 有关将用户从每用户 MFA 迁移到基于条件访问的 MFA 的详细信息，请参阅[将用户从每用户 Mfa 转换为基于条件访问的 mfa](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)。

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>用于转换和发送 SAML 令牌中的声明的新功能

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

我们添加了其他功能，可帮助你在 SAML 令牌中自定义和发送声明。 这些新功能包括：

- 其他声明转换函数，有助于修改在声明中发送的值。

- 能够将多个转换应用于单个声明。

- 基于用户类型和用户所属的组指定声明源的能力。

有关这些新功能的详细信息，包括如何使用它们，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中的最终用户的 "我的新登录" 页面

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 监视 & 报告

我们添加了一个新的 **"我的登录"** 页（ https://mysignins.microsoft.com) 以便让组织的用户查看其最近的登录历史记录以检查是否存在异常活动。 此新页面允许用户查看：

- 如果有人试图猜测其密码。

- 如果攻击者成功登录到其帐户和位置。

- 攻击者尝试访问的应用。

有关详细信息，请参阅[用户现在可以检查异常活动博客的登录历史记录](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>将 Azure AD 域服务（Azure AD DS）从经典部署模型迁移到 Azure 资源管理器虚拟网络

**键入：** 新功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

对于已陷入经典虚拟网络的客户，我们为你提供了精彩新闻！ 你现在可以执行从经典虚拟网络到现有资源管理器虚拟网络的一次性迁移。 移到资源管理器虚拟网络后，你将能够利用附加功能和升级功能，如细化密码策略、电子邮件通知和审核日志。

有关详细信息，请参阅[预览-将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 页面协定布局的更新

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

我们针对 Azure AD B2C 的页面协定的版本1.2.0 引入了一些新的更改。 在此更新的版本中，现在可以控制元素的加载顺序，这也有助于停止加载样式表（CSS）时发生的闪烁。

有关对页协定所做更改的完整列表，请参阅[版本更改日志](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新到 "我的应用程序" 页和新工作区（公共预览版）

**键入：** 新功能**服务类别：** 我的应用**产品功能：** 访问控制

你现在可以自定义组织用户查看和访问全新 "我的应用" 体验的方式，包括使用新的工作区功能，使其更易于查找应用。 新的工作区功能充当组织用户已有权访问的应用的筛选器。

有关推出新的 "我的应用" 体验和创建工作区的详细信息，请参阅在["我的应用（预览版）" 门户中创建工作区](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支持每月活动的基于用户的计费模型（正式发布）

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

Azure AD B2C 现在支持每月活动用户（MAU）计费。 MAU 计费基于日历月中具有身份验证活动的唯一用户数。 现有客户可以随时切换到此新计费方法。

从2019年11月1日开始，所有新客户都将使用此方法自动收费。 此计费方法通过成本优势和计划提前计划，为客户带来好处。

有关详细信息，请参阅[升级到每月活动用户计费模式](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年10月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年10月，我们已向应用程序库添加了这些支持联合身份验证的35新应用：

[在出现危机时–移动](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)、 [Juno 旅程](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)、 [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[即可](https://tact.ai/assistant/)、 [OpusCapita 现金管理](http://cm1.opuscapita.com/tenantname)、 [Salestim](https://prd.salestim.io/forms)、 [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、 [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、 [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、 [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、 [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)、 [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)、 [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、 [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、 [Bindtuning](https://bindtuning.com/login)、 [HireVue 协调-欧洲](https://www.hirevue.com/)、 [HireVue 协调-USOnly](https://www.hirevue.com/)， [HireVue 坐标-US](https://www.hirevue.com/)， [WittyParrot 知识 Box](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)， [Cloudmore](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)Visit.org [，](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)Cambium [Xirrus EasyPass 门户，](https://login.xirrus.com/azure-signup)Paylocity [，](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)Mail[好运！，](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)Teamie [，](https://theteamie.com/)SIGNL4，EAB， [IMPL，ScreenMeet](https://account.signl4.com/manage)，ExactCare， [iHealthHome，Qubie](https://pi.ompnt.com/) [，用于 Intune](https://velocity.peakup.org/teams/login) [（iphone），](https://speaking.email/FAQ/98/email-access-via-microsoft-intune) [ExactCare SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [，护理](https://console.screenmeet.com/)[导航系统，](https://ihealthnav.com/account/signin) [365](https://qubie.azurewebsites.net/static/adminTab/authorize.html) [EAB Navigate IMPL](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 门户中的 "合并安全" 菜单项

**键入：** 已更改的功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

你现在可以从 "新建**安全**" 菜单项和 "**搜索**栏" 中的 Azure 门户访问所有可用 Azure AD 安全功能。 此外，新的**安全**登录页称为 "**安全性-** 入门"，它提供了公共文档、安全指南和部署指南的链接。

新的**安全**菜单包括：

- 条件性访问
- 标识保护
- 安全中心
- 标识安全评分
- 身份验证方法
- MFA
- 风险报告-有风险的用户、有风险的登录、风险检测
- 更多内容...

有关详细信息，请参阅[安全性-入门](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 组过期策略已通过自动续订增强

**键入：** 已更改的功能**服务类别：** 组管理**产品功能：** 标识生命周期管理

Office 365 组过期策略已增强，可自动续订其成员正在使用的组。 将基于所有 Office 365 应用中的用户活动（包括 Outlook、SharePoint 和团队） autorenewed 组。

此增强功能有助于减少组过期通知，并有助于确保活动组继续可用。 如果你已为 Office 365 组启用了有效过期策略，则无需执行任何操作来启用此新功能。

有关详细信息，请参阅为[Office 365 组配置过期策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>更新 Azure AD 域服务（Azure AD DS）创建体验

**键入：** 已更改的功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

我们已更新 Azure AD 域服务（Azure AD DS）以包括新的和改进的创建体验，只需三次单击即可创建托管域！ 此外，你现在可以从模板上传和部署 Azure AD DS。

有关详细信息，请参阅[教程：创建和配置 Azure Active Directory 域服务实例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>规划更改：弃用 Power BI 内容包

**键入：** 规划更改**服务类别：** 报表**产品功能：** 监视 & 报表

从2019年10月1日开始，Power BI 将开始弃用所有内容包，包括 Azure AD Power BI 内容包。 作为此内容包的替代方法，你可以使用 Azure AD 工作簿来深入了解你 Azure AD 相关的服务。 还有其他工作簿，其中包括有关仅限报表模式下的条件性访问策略的工作簿、基于应用同意的见解，等等。

有关工作簿的详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 有关内容包弃用的详细信息，请参阅[公告 Power BI 模板应用正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)发布博客文章。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>我的配置文件正在重命名并与 Microsoft Office 帐户页面集成

**键入：** 规划更改**服务类别：** 我的个人资料/帐户**产品功能：** 协作

从10月开始，"我的个人资料经验" 将成为我的帐户。 作为此更改的一部分，当前说， **"我的配置文件**" 将更改为 **"** 我的帐户"。 在命名更改和某些设计改进的基础上，更新的体验将提供与 Microsoft Office 帐户页面的其他集成。 具体而言，你将能够从 "**概述帐户**" 页访问 office 安装和订阅，还可以从 "**隐私**" 页访问与 Office 相关的联系人首选项。

有关 "我的个人资料（预览版）" 体验的详细信息，请参阅[我的个人资料（预览版）门户概述](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 门户中使用 CSV 文件批量管理组和成员（公共预览版）

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

我们非常高兴地宣布 Azure AD 门户中的批量组管理体验的公开预览版。 你现在可以使用 CSV 文件和 Azure AD 门户来管理组和成员列表，其中包括：

- 添加或删除组中的成员。

- 正在从目录下载组列表。

- 正在下载特定组的组成员的列表。

有关详细信息，请参阅[批量添加成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[批量删除成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[批量下载成员列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)和[批量下载组列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>现在，通过新的管理员许可终结点支持动态许可

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们已经创建了一个新的管理员许可终结点来支持动态同意，这对于想要在 Microsoft 标识平台上使用动态同意模式的应用很有用。

有关如何使用此新终结点的详细信息，请参阅[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年9月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年9月，我们已向应用库添加了以下29个新应用和联合支持：

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)， [MS Azure SSO Access for Ethidex 合规性 &trade; Office-单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial)， [iServer Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial)， [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial)， [Concur 旅行和支出](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial)， [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial)， `https://apps.yeeflow.com/` ， [ARC 设施](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial)， [Luware Stratus 团队](https://stratus.emea.luware.cloud/login)，[范围创意](https://wideideas.online/wideideas/)， [Prisma cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)，JDLT[客户端中心](https://clients.jdlt.co.uk/login)， [RENRAKU](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial)， [SealPath 安全浏览器](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)， [Prisma cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)， `https://app.penneo.com/` ， `https://app.testhtm.com/settings/email-integration` [Amelio](https://app.amelio.co/)， [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial) [Cintoo cloud](https://aec.cintoo.com/login) [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial)， [Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial)，[托管的遗产联机 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial)， [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial) [Coo Kai Team Build](https://ms-contacts.coo-kai.jp/) [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial)， [Discovery Benefits SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial) [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial)CakeHR，`https://itask.yipinapp.com/`

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-global-reader-role"></a>新 Azure AD 全局读者角色

**键入：** 新功能**服务类别：** RBAC**产品功能：** 访问控制

从2019年9月24日开始，我们将开始推出称为全局读取器的新 Azure Active Directory （AD）角色。 此推出将从生产和全球云客户（GCC）开始，在10月的全球范围内完成。

全局读者角色是全局管理员的只读对应角色。 此角色中的用户可以跨 Microsoft 365 服务读取设置和管理信息，但不能执行管理操作。 我们已经创建了全局读者角色，以帮助减少组织中全局管理员的数量。 由于全局管理员帐户功能强大并且容易受到攻击，因此我们建议你的全局管理员少于五台。 建议使用全局读者角色进行计划、审核或调查。 我们还建议将全局读者角色与其他有限的管理员角色（如 Exchange 管理员）结合使用，以帮助完成工作而不需要全局管理员角色。

全局读者角色适用于新的 Microsoft 365 管理中心、Exchange 管理中心、团队管理中心、安全中心、合规性中心、Azure AD 管理中心和设备管理管理中心。

>[!NOTE]
> 公共预览版开始时，全局读者角色将无法使用： SharePoint、Privileged Access Management、客户密码箱、敏感度标签、团队生命周期、团队报告 & 呼叫分析、团队 IP 电话设备管理和团队应用程序目录。

有关详细信息，请参阅[Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理从 Power BI 移动版应用访问本地报表服务器

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 访问控制

通过 Power BI 移动应用与 Azure AD 应用程序代理之间的新集成，你可以安全地登录到 Power BI 移动应用，并查看本地 Power BI 报表服务器上托管的任何组织的报表。

有关 Power BI 移动版应用的信息，包括下载应用的位置，请参阅[Power BI 站点](https://powerbi.microsoft.com/mobile/)。 有关如何使用 Azure AD 应用程序代理设置 Power BI 移动应用的详细信息，请参阅[使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>新版本的 AzureADPreview PowerShell 模块可用

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 目录

新的 cmdlet 已添加到 AzureADPreview 模块中，可帮助定义和分配 Azure AD 中的自定义角色，其中包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 目录

我们发布了自动升级客户的更新版本 Azure AD Connect。 此新版本包括几项新功能、改进和 bug 修复。 有关此新版本的详细信息，请参阅[Azure AD Connect：版本发布历史记录](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>现在可以使用 Azure 多重身份验证（MFA）服务器版本8.0。2

**键入：** 已修复的**服务类别：** MFA**产品功能：** 标识安全 & 保护

如果你是在2019年7月1日之前激活了 MFA 服务器的现有客户，则现在可以下载最新版本的 MFA 服务器（8.0.2 版）。 在这个新版本中，我们：

- 修复了这样的问题：当 Azure AD 同步将用户从禁用更改为启用时，会向用户发送电子邮件。

- 修复了一个问题，使客户能够成功升级，同时继续使用标记功能。

- 添加了科索沃（+ 383）国家/地区代码。

- 向 Multifactorauthsvc.log 添加了一次性跳过审核日志记录。

- 提高了 Web 服务 SDK 的性能。

- 修复了其他次要错误。

从2019年7月1日开始，Microsoft 停止为新部署提供 MFA 服务器。 需要多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 有关详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="august-2019"></a>2019 年 8 月

### <a name="enhanced-search-filtering-and-sorting-for-groups-is-available-in-the-azure-ad-portal-public-preview"></a>Azure AD 门户（公共预览版）中提供了组的增强搜索、筛选和排序功能

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

我们非常高兴地宣布 Azure AD 门户中的增强组相关体验的公共预览版。 这些增强功能可帮助你更好地管理组和成员列表，方法是提供：

- 高级搜索功能，如对组列表的子字符串搜索。
- 成员和所有者列表上的高级筛选和排序选项。
- 成员和所有者列表的新搜索功能。
- 大型组的更准确的组计数。

有关详细信息，请参阅[Azure 门户中的管理组](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-members-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="new-custom-roles-are-available-for-app-registration-management-public-preview"></a>新的自定义角色可用于应用注册管理（公共预览版）

**键入：** 新功能**服务类别：** RBAC**产品功能：** 访问控制

自定义角色（与 Azure AD P1 或 P2 订阅一起提供）现在可以通过让你创建具有特定权限的角色定义，然后将这些角色分配给特定资源，帮助提供精细的访问权限。 目前，你通过使用管理应用注册的权限，然后将该角色分配给特定应用来创建自定义角色。 有关自定义角色的详细信息，请参阅[Azure Active Directory （预览版）中的自定义管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-custom-overview)。

如果需要其他权限或支持的资源（当前未显示），可以将反馈发送到我们的[Azure 反馈网站](https://feedback.azure.com/forums/169401-azure-active-directory?category_id=166032)，我们会将你的请求添加到我们的更新路线图。

---

### <a name="new-provisioning-logs-can-help-you-monitor-and-troubleshoot-your-app-provisioning-deployment-public-preview"></a>新的预配日志可帮助你监视应用预配部署并对其进行故障排除（公共预览版）

**键入：** 新功能**服务类别：** 应用预配**产品功能：** 标识生命周期管理

提供了新的预配日志，可帮助你监视用户和组预配部署。 这些新日志文件包含以下信息：

- 已成功在[ServiceNow](https://docs.microsoft.com/azure/active-directory/saas-apps/servicenow-provisioning-tutorial)中创建哪些组
- 从 Amazon Web Services 导入的角色[（AWS）](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-web-service-tutorial#configure-and-test-azure-ad-single-sign-on-for-amazon-web-services-aws)
- 员工未从[Workday](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)导入的内容

有关详细信息，请参阅[在 Azure Active Directory 门户（预览版）中预配报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-provisioning-logs)。

---

### <a name="new-security-reports-for-all-azure-ad-administrators-general-availability"></a>所有 Azure AD 管理员的新安全报表（公开发布）

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

默认情况下，所有 Azure AD 管理员很快就可以访问 Azure AD 内的新式安全报告。 在9月底结束之前，你将能够使用新式安全报表顶部的横幅返回到旧报表。

新式安全报告将提供更早版本的其他功能，包括：

- 高级筛选和排序
- 大容量操作，如消除用户风险
- 确认泄露或安全实体
- 风险状态，涵盖：有风险，已解除、已修正和已确认泄露
- 新的与风险相关的检测（可用于 Azure AD Premium 订户）

有关详细信息，请参阅有[风险的用户](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users)、有风险的[登录](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)和[风险检测](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risk-detections)。

---

### <a name="user-assigned-managed-identity-is-available-for-virtual-machines-and-virtual-machine-scale-sets-general-availability"></a>用户分配的托管标识可用于虚拟机和虚拟机规模集（正式发布）

**键入：** 新功能**服务类别：** Azure 资源的托管标识**产品功能：** 开发人员体验

用户分配的托管标识现已正式用于虚拟机和虚拟机规模集。 作为此过程的一部分，Azure 可以在所使用的订阅所信任的 Azure AD 租户中创建标识，并且可以将其分配给一个或多个 Azure 服务实例。 有关用户分配的托管标识的详细信息，请参阅[Azure 资源的托管标识是什么？](https://aka.ms/azuremanagedidentity)。

---

### <a name="users-can-reset-their-passwords-using-a-mobile-app-or-hardware-token-general-availability"></a>用户可以使用移动应用或硬件令牌来重置其密码（正式发布）

**键入：** 更改的功能**服务类别：** 自助服务密码重置**产品功能：** 用户身份验证

向你的组织注册了移动应用的用户现在可以通过从 Microsoft Authenticator 应用批准通知或通过从其移动应用或硬件令牌输入代码来重置其自己的密码。

有关详细信息，请参阅[工作方式： Azure AD 自助服务密码重置](https://aka.ms/authappsspr)。 有关用户体验的详细信息，请参阅[重置你自己的工作或学校密码概述](https://docs.microsoft.com/azure/active-directory/user-help/user-help-password-reset-overview)。

---

### <a name="adalnet-ignores-the-msalnet-shared-cache-for-on-behalf-of-scenarios"></a>ADAL.NET 为代表的方案忽略 MSAL.NET 共享缓存

**键入：** 已修复的**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 Azure AD 身份验证库（ADAL.NET）第5.0.0 版-预览版开始，应用开发人员必须[为 web 应用和 Web api 每个帐户序列化一个缓存](https://github.com/AzureAD/azure-activedirectory-library-for-dotnet/wiki/Token-cache-serialization#custom-token-cache-serialization-in-web-applications--web-api)。 否则，某些使用代理[流](https://docs.microsoft.com/azure/active-directory/develop/scenario-web-api-call-api-app-configuration#on-behalf-of-flow)的方案以及某些特定用例 `UserAssertion` 可能会导致权限提升。 为避免出现这种情况，ADAL.NET 会为代表方案忽略用于 dotnet （MSAL.NET）共享缓存的 Microsoft 身份验证库。

有关此问题的详细信息，请参阅[Azure Active Directory Authentication Library 提升权限漏洞](https://portal.msrc.microsoft.com/security-guidance/advisory/CVE-2019-1258)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2019"></a>Azure AD 应用库中提供了新的联合应用程序-2019 年8月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年8月，我们已将这26个具有联合支持的新应用添加到应用库：

[市政平台](https://docs.microsoft.com/azure/active-directory/saas-apps/civic-platform-tutorial)， [Amazon Business](https://docs.microsoft.com/azure/active-directory/saas-apps/amazon-business-tutorial)， [ProNovos Ops 经理](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-ops-manager-tutorial)， [Cognidox](https://docs.microsoft.com/azure/active-directory/saas-apps/cognidox-tutorial)， [Viareport，Inativ 门户（欧洲）](https://docs.microsoft.com/azure/active-directory/saas-apps/viareports-inativ-portal-europe-tutorial)， [Azure Databricks](https://azure.microsoft.com/services/databricks)，[机构](https://docs.microsoft.com/azure/active-directory/saas-apps/robin-tutorial)，[学院](https://docs.microsoft.com/azure/active-directory/saas-apps/academy-attendance-tutorial)教育，[优先级矩阵](https://sync.appfluence.com/pmwebng/)，Cousto [MySpace](https://cousto.platformers.be/account/login)， [Uploadcare](https://uploadcare.com/accounts/signup/)， [Carbonite Endpoint Backup](https://docs.microsoft.com/azure/active-directory/saas-apps/carbonite-endpoint-backup-tutorial)， [CPQSync](https://docs.microsoft.com/azure/active-directory/saas-apps/cpqsync-by-cincom-tutorial)，Cincom， [Chargebee](https://docs.microsoft.com/azure/active-directory/saas-apps/chargebee-tutorial)， [，前端， &trade; ](https://portal.deliver.media)，stashcat [，Vocoli](https://docs.microsoft.com/azure/active-directory/saas-apps/frontline-education-tutorial)， [ProNovos，Sigstr](https://www.f5.com/products/security/access-policy-manager) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/blink-tutorial) [Darwinbox，EAB](https://www.stashcat.com) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/pronovos-analytics-tutorial)[观看](https://docs.microsoft.com/azure/active-directory/saas-apps/watch-by-colors-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/darwinbox-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/vocoli-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/sigstr-tutorial)[导航战略性护理](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-strategic-care-tutorial) [Harness](https://docs.microsoft.com/azure/active-directory/saas-apps/harness-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-versions-of-the-azuread-powershell-and-azureadpreview-powershell-modules-are-available"></a>提供了新版本的 AzureAD PowerShell 和 AzureADPreview PowerShell 模块

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 目录

AzureAD 和 AzureAD 预览版 PowerShell 模块的新更新可用：

- 已将新 `-Filter` 参数添加到 `Get-AzureADDirectoryRole` AzureAD 模块中的参数。 此参数可帮助你筛选 cmdlet 返回的目录角色。
- 新的 cmdlet 已添加到 AzureADPreview 模块中，可帮助定义和分配 Azure AD 中的自定义角色，其中包括：

    - `Get-AzureADMSRoleAssignment`
    - `Get-AzureADMSRoleDefinition`
    - `New-AzureADMSRoleAssignment`
    - `New-AzureADMSRoleDefinition`
    - `Remove-AzureADMSRoleAssignment`
    - `Remove-AzureADMSRoleDefinition`
    - `Set-AzureADMSRoleDefinition`

---

### <a name="improvements-to-the-ui-of-the-dynamic-group-rule-builder-in-the-azure-portal"></a>Azure 门户中动态组规则生成器的 UI 改进

**键入：** 已更改的功能**服务类别：** 组管理**产品功能：** 协作

我们对动态组规则生成器进行了一些 UI 改进，该生成器在 Azure 门户中提供，可帮助你更轻松地设置新规则或更改现有规则。 此设计改进允许您创建最多包含五个表达式（而不只是一个）的规则。 我们还更新了设备属性列表，以删除不推荐使用的设备属性。

有关详细信息，请参阅[管理动态成员身份规则](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)。

---

### <a name="new-microsoft-graph-app-permission-available-for-use-with-access-reviews"></a>可以与访问评审一起使用的新 Microsoft Graph 应用权限

**键入：** 已更改的功能**服务类别：** 访问评审**产品功能：** 标识调控

我们引入了新的 Microsoft Graph 应用权限， `AccessReview.ReadWrite.Membership` 它允许应用自动创建和检索组成员身份和应用分配的访问评审。 此权限可由计划作业或自动化的一部分使用，无需登录的用户上下文。

有关详细信息，请参阅[示例如何使用 PowerShell 博客创建 Azure AD 访问评审 Microsoft Graph 应用权限](https://techcommunity.microsoft.com/t5/Azure-Active-Directory/Example-how-to-create-Azure-AD-access-reviews-using-Microsoft/m-p/807241)。

---

### <a name="azure-ad-activity-logs-are-now-available-for-government-cloud-instances-in-azure-monitor"></a>Azure AD 活动日志现在适用于 Azure Monitor 中的政府云实例

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

我们很高兴地宣布，Azure AD 活动日志现在可用于 Azure Monitor 中的政府云实例。 你现在可以将 Azure AD 日志发送到你的存储帐户或事件中心，以便与 SIEM 工具（如[Sumologic](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-sumologic)、 [Splunk](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-splunk)和[ArcSight](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-arcsight)）集成。

有关设置 Azure Monitor 的详细信息，请参阅[Azure Monitor 中 Azure AD 活动日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-activity-logs-azure-monitor#cost-considerations)。

---

### <a name="update-your-users-to-the-new-enhanced-security-info-experience"></a>将用户更新为新的增强安全信息体验

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

2019年9月25日，我们将关闭用于注册和管理用户安全信息的旧的、不安全的安全信息体验，并只启用新的[增强版本](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。 这意味着你的用户将无法再使用旧体验。

有关增强的安全信息体验的详细信息，请参阅我们的[管理员文档](https://aka.ms/securityinfodocs)和我们的[用户文档](https://aka.ms/securityinfoguide)。

#### <a name="to-turn-on-this-new-experience-you-must"></a>若要启用这种新体验，你必须：

1. 以全局管理员或用户管理员身份登录到 Azure 门户。

2. 请参阅**Azure Active Directory > 用户设置 > 管理访问面板预览功能的设置**。

3. 在 "**用户可以使用预览功能注册和管理安全信息-增强**区域" 中，选择 "已**选择**"，然后选择一组用户或选择 "**全部**"，为租户中的所有用户启用此功能。

4. 在 "用户可以使用预览功能注册和管理安全性 * * 信息" 区域中，选择 "**无**"。

5. 保存设置。

    保存设置后，将无法再访问旧的安全信息体验。

>[!Important]
>如果未在2019年9月25日之前完成这些步骤，则会自动为你的 Azure Active Directory 租户启用增强体验。 如果有疑问，请通过联系我们 registrationpreview@microsoft.com 。

---

### <a name="authentication-requests-using-post-logins-will-be-more-strictly-validated"></a>使用 POST 登录名的身份验证请求将受到严格验证

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 标准

从2019年9月2日开始，使用 POST 方法的身份验证请求将更严格地根据 HTTP 标准进行验证。 具体而言，将不再从请求窗体值中删除空格和双引号（""）。 这些更改不会中断任何现有的客户端，并且有助于确保每次都能可靠地处理发送到 Azure AD 的请求。

有关详细信息，请参阅[Azure AD 重大更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#post-form-semantics-will-be-enforced-more-strictly---spaces-and-quotes-will-be-ignored)。

---

## <a name="july-2019"></a>2019 年 7 月

### <a name="plan-for-change-application-proxy-service-update-to-support-only-tls-12"></a>计划更改：应用程序代理服务更新仅支持 TLS 1。2

**键入：** 规划更改**服务类别：** 应用代理**产品功能：** 访问控制

为了帮助提供最强大的加密，我们将开始限制应用程序代理服务只能访问 TLS 1.2 协议。 此限制最初会向已使用 TLS 1.2 协议的客户推出，因此你不会看到这种影响。 TLS 1.0 和 TLS 1.1 协议的完整弃用将于8月 2019 31 日完成。 仍在使用 TLS 1.0 和 TLS 1.1 的客户将收到有关为此更改做好准备的高级通知。

若要在此更改期间保持与应用程序代理服务的连接，建议你确保客户端-服务器和浏览器-服务器组合已更新为使用 TLS 1.2。 我们还建议确保包含员工使用的任何客户端系统，以访问通过应用程序代理服务发布的应用。

有关详细信息，请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)。

---

### <a name="plan-for-change-design-updates-are-coming-for-the-application-gallery"></a>规划更改：应用程序库的设计更新

**键入：** 规划更改**服务类别：** 企业应用**产品功能：** SSO

新用户界面更改将从 "**添加应用程序**" 边栏选项卡的 **"库**" 区域中的 "添加" 设计。 这些更改将帮助你更轻松地查找支持自动预配、OpenID Connect、安全断言标记语言（SAML）和密码单一登录（SSO）的应用。

---

### <a name="plan-for-change-removal-of-the-mfa-server-ip-address-from-the-office-365-ip-address"></a>计划更改：从 Office 365 IP 地址中删除 MFA 服务器 IP 地址

**键入：** 规划更改**服务类别：** MFA**产品功能：** 标识安全 & 保护

我们正在从[Office 365 Ip 地址和 URL Web 服务](https://docs.microsoft.com/office365/enterprise/office-365-ip-web-service)中删除 MFA 服务器的 ip 地址。 如果你当前依赖这些页面来更新防火墙设置，则必须确保还包括 azure[多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy#azure-multi-factor-authentication-server-firewall-requirements)文章入门的**azure 多重身份验证服务器防火墙要求**部分中所述的 IP 地址列表。

---

### <a name="app-only-tokens-now-require-the-client-app-to-exist-in-the-resource-tenant"></a>仅限应用的令牌现在要求客户端应用存在于资源租户中

**键入：** 已修复的**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

2019年7月26日，我们更改了我们通过[客户端凭据 grant](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)提供仅限应用的令牌的方式。 以前，无论客户端应用是否在租户中，应用都可以获取令牌来调用其他应用。 我们更新了此行为，因此只能由资源租户中存在的客户端应用调用单租户资源（有时称为 Web Api）。

如果你的应用程序不在资源租户中，你将收到一条错误消息，指出， `The service principal named <app_name> was not found in the tenant named <tenant_name>. This can happen if the application has not been installed by the administrator of the tenant.` 若要解决此问题，你必须使用[管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#using-the-admin-consent-endpoint)或[通过 PowerShell](https://docs.microsoft.com/azure/active-directory/develop/howto-authenticate-service-principal-powershell)在租户中创建客户端应用服务主体，这样可确保你的租户已授予应用在租户内操作的权限。

有关详细信息，请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#app-only-tokens-for-single-tenant-applications-are-only-issued-if-the-client-app-exists-in-the-resource-tenant)。

> [!NOTE]
> 客户端与 API 之间的现有同意将继续进行。 应用仍应执行其自己的授权检查。

---

### <a name="new-passwordless-sign-in-to-azure-ad-using-fido2-security-keys"></a>New 无密码使用 FIDO2 安全密钥登录到 Azure AD

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

Azure AD 客户现在可以设置策略以管理其组织的用户和组的 FIDO2 安全密钥。 最终用户还可以自行注册其安全密钥，在 FIDO 支持的设备上使用密钥登录到网站上的 Microsoft 帐户，以及登录到其 Azure AD 的 Windows 10 设备。

有关详细信息，请参阅[Enable 无密码 sign in for Azure AD （预览版）](/azure/active-directory/authentication/concept-authentication-passwordless) ，以获取管理员相关信息，并[将安全信息设置为对最终用户相关信息使用安全密钥（预览）](https://docs.microsoft.com/azure/active-directory/user-help/security-info-setup-security-key) 。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2019"></a>Azure AD 应用库中提供了新的联合应用程序-2019 年7月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年7月，我们已将以下18个新应用和联合支持添加到应用库：

[Ungerboeck Software](https://docs.microsoft.com/azure/active-directory/saas-apps/ungerboeck-software-tutorial)，[鲜模式对全渠道 Contact Center](https://docs.microsoft.com/azure/active-directory/saas-apps/bright-pattern-omnichannel-contact-center-tutorial)，[聪明 Nelly](https://docs.microsoft.com/azure/active-directory/saas-apps/clever-nelly-tutorial)， [AcquireIO](https://docs.microsoft.com/azure/active-directory/saas-apps/acquireio-tutorial)， [Looop](https://www.looop.co/schedule-a-demo/)， [productboard](https://docs.microsoft.com/azure/active-directory/saas-apps/productboard-tutorial)， [MS Azure SSO Access for Ethidex 相容性 &trade; Office](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on#password-based-sso)，[夸大宣传](https://docs.microsoft.com/azure/active-directory/saas-apps/hype-tutorial)， [Abstract](https://docs.microsoft.com/azure/active-directory/saas-apps/abstract-tutorial)， [Ascentis](https://docs.microsoft.com/azure/active-directory/saas-apps/ascentis-tutorial)，Flipsnack [，Wandera，TwineSocial](https://docs.microsoft.com/azure/active-directory/saas-apps/wandera-tutorial)， [Kallidus](https://docs.microsoft.com/azure/active-directory/saas-apps/kallidus-tutorial) [，HyperAnna，PharmID](https://docs.microsoft.com/azure/active-directory/saas-apps/hyperanna-tutorial) [WasteWitness](https://pharmid.com/) [，](https://twinesocial.com/) [i2B Connect](https://www.i2b-online.com/sign-up-to-use-i2b-connect-here-sso-access/)， [JFrog Artifactory](https://docs.microsoft.com/azure/active-directory/saas-apps/jfrog-artifactory-tutorial) [Flipsnack](https://www.flipsnack.com/accounts/sign-in-sso.html)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 监视 & 报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="new-azure-ad-domain-services-service-tag-for-network-security-group"></a>网络安全组的新 Azure AD 域服务服务标记

**键入：** 新功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

如果你厌倦了管理 IP 地址和范围的长列表，则可以使用 Azure 网络安全组中的 new **AzureActiveDirectoryDomainServices** network service 标记来帮助保护到 Azure AD 域服务虚拟网络子网的入站流量。

有关此新服务标记的详细信息，请参阅[Azure AD 域服务的网络安全组](../../active-directory-domain-services/network-considerations.md#network-security-groups-and-required-ports)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**键入：** 新功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务门户将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），安全审核可帮助提供对身份验证服务的关键深入了解。

有关详细信息，请参阅[为 Azure AD 域服务（预览版）启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-authentication-methods-usage--insights-public-preview"></a>新的身份验证方法用法 & insights （公共预览版）

**键入：** 新功能**服务类别：** 自助服务密码重置**产品功能：** 监视 & 报告

使用 & insights 报表的新的身份验证方法，可帮助你了解 Azure 多重身份验证和自助密码重置等功能在你的组织中的注册和使用方式，包括每项功能的注册用户数量、用于重置密码的自助密码重置以及重置发生的方法。

有关详细信息，请参阅[身份验证方法用法 & insights （预览版）](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-methods-usage-insights)。

---

### <a name="new-security-reports-are-available-for-all-azure-ad-administrators-public-preview"></a>所有 Azure AD 管理员都可以使用新的安全报告（公共预览版）

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

所有 Azure AD 管理员现在可以选择现有安全报告（如**标记为风险**报告的用户）顶部的横幅，以开始使用有**风险的用户**和有**风险的登录**报告中所示的新安全体验。 随着时间的推移，所有安全报告都将从较旧的版本转移到新版本，同时提供了以下附加功能：

- 高级筛选和排序

- 大容量操作，如消除用户风险

- 确认泄露或安全实体

- 风险状态，涵盖：有风险，已解除、已修正和已确认泄露

有关详细信息，请参阅有[风险的用户报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-users)和有[风险的登录报告](https://docs.microsoft.com/azure/active-directory/identity-protection/howto-investigate-risk#risky-sign-ins)。

---

### <a name="new-security-audits-for-azure-ad-domain-services-public-preview"></a>Azure AD 域服务的新安全审核（公共预览版）

**键入：** 新功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

我们很高兴地宣布发布 Azure AD 域服务安全审核到公共预览版。 通过使用 Azure AD 域服务门户将安全审核事件流式传输到目标资源（包括 Azure 存储、Azure Log Analytics 工作区和 Azure 事件中心），安全审核可帮助提供对身份验证服务的关键深入了解。

有关详细信息，请参阅[为 Azure AD 域服务（预览版）启用安全审核](https://docs.microsoft.com/azure/active-directory-domain-services/security-audit-events)。

---

### <a name="new-b2b-direct-federation-using-samlws-fed-public-preview"></a>使用 SAML/WS 进纸进行新的 B2B 直接联合身份验证（公共预览版）

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C

通过使用支持 SAML 或 WS 送标准的标识系统，直接联合身份验证可让你更轻松地使用 IT 托管标识解决方案不 Azure AD 的合作伙伴。 设置与合作伙伴的直接联合身份验证关系后，你从该域邀请的任何新来宾用户都可以使用其现有组织帐户与你协作，使你的来宾的用户体验更顺畅。

有关详细信息，请参阅[guest 用户的直接联合身份验证 AD FS 和第三方提供程序（预览）](https://docs.microsoft.com/azure/active-directory/b2b/direct-federation)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 监视 & 报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Dialpad](https://docs.microsoft.com/azure/active-directory/saas-apps/dialpad-provisioning-tutorial)

- [Federated Directory](https://docs.microsoft.com/azure/active-directory/saas-apps/federated-directory-provisioning-tutorial)

- [Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-provisioning-tutorial)

- [Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-provisioning-tutorial)

- [Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-provisioning-tutorial)

- [Smartsheet](https://docs.microsoft.com/azure/active-directory/saas-apps/smartsheet-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-check-for-duplicate-group-names-in-the-azure-ad-portal"></a>新检查 Azure AD 门户中的重复组名称

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

现在，当你在 Azure AD 门户中创建或更新组名称时，我们将执行检查以查看你是否在资源中复制现有组名。 如果我们确定名称已被另一个组使用，系统会要求你修改名称。

有关详细信息，请参阅[Azure AD 门户中的 "管理组"](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)。

---

### <a name="azure-ad-now-supports-static-query-parameters-in-reply-redirect-uris"></a>现在 Azure AD 在答复（重定向） Uri 中支持静态查询参数

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

Azure AD 应用现在可以使用静态查询参数（例如， `https://contoso.com/oauth2?idp=microsoft` ）为 OAuth 2.0 请求注册和使用回复（重定向） uri。 静态查询参数服从于回复 URI 的字符串匹配，就像回复 URI 的任何其他部分一样。 如果没有任何已注册的字符串与 URL 解码的重定向 uri 匹配，则会拒绝该请求。 如果找到了回复 URI，则使用整个字符串重定向用户，包括静态查询参数。

动态回复 Uri 仍被禁止，因为它们表示安全风险，不能用于在身份验证请求中保留状态信息。 为此，请使用 `state` 参数。

目前，Azure 门户的应用注册屏幕仍会阻止查询参数。 但是，您可以手动编辑应用程序清单以在应用程序中添加和测试查询参数。 有关详细信息，请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#redirect-uris-can-now-contain-query-string-parameters)。

---

### <a name="activity-logs-ms-graph-apis-for-azure-ad-are-now-available-through-powershell-cmdlets"></a>现在可通过 PowerShell Cmdlet 使用 Azure AD 的活动日志（MS Graph Api）

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

我们很高兴地宣布 Azure AD 活动日志（审核和登录报告）现已通过 Azure AD PowerShell 模块提供。 以前，你可以使用 MS 图形 API 终结点创建自己的脚本，现在我们已将该功能扩展到 PowerShell cmdlet。

有关如何使用这些 cmdlet 的详细信息，请参阅[Azure AD PowerShell cmdlet 用于报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-powershell-reporting)。

---

### <a name="updated-filter-controls-for-audit-and-sign-in-logs-in-azure-ad"></a>更新了 Azure AD 中的审核和登录日志的筛选器控件

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

我们更新了审核和登录日志报告，因此你现在可以应用多个筛选器，而无需将它们作为列添加到报表屏幕中。 此外，您现在可以决定要在屏幕上显示多少个筛选器。 这些更新将全部工作，以使报表更易于阅读，并使其更适合你的需求。

有关这些更新的详细信息，请参阅[筛选审核日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#filtering-audit-logs)和[筛选登录活动](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins#filter-sign-in-activities)。

---

## <a name="june-2019"></a>2019 年 6 月

### <a name="new-riskdetections-api-for-microsoft-graph-public-preview"></a>适用于 Microsoft Graph 的新 riskDetections API （公共预览版）

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

我们很高兴地宣布推出新的 riskDetections API，以便 Microsoft Graph 现在为公共预览版。 可以使用此新 API 查看组织的标识保护相关用户和登录风险检测的列表。 你还可以使用此 API 更有效地查询你的风险检测，包括有关检测类型、状态和级别等的详细信息。

有关详细信息，请参阅[风险检测 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskdetection?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年6月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年6月，我们已将这22个新应用和联合支持添加到应用库：

[AZURE AD SAML 工具包](https://docs.microsoft.com/azure/active-directory/saas-apps/saml-toolkit-tutorial)， [Otsuka Shokai （大塚商会）](https://docs.microsoft.com/azure/active-directory/saas-apps/otsuka-shokai-tutorial)、 [ANAQUA](https://docs.microsoft.com/azure/active-directory/saas-apps/anaqua-tutorial)、 [Azure VPN Client](https://portal.azure.com/)、 [ExpenseIn](https://docs.microsoft.com/azure/active-directory/saas-apps/expensein-tutorial)、 [helper helper](https://docs.microsoft.com/azure/active-directory/saas-apps/helper-helper-tutorial)、 [Costpoint](https://docs.microsoft.com/azure/active-directory/saas-apps/costpoint-tutorial)， [GlobalOne](https://docs.microsoft.com/azure/active-directory/saas-apps/globalone-tutorial)， [Mercedes-Benz Office](https://me.secure.mercedes-benz.com/)， [Skore](https://app.justskore.it/)， [ORACLE 云基础结构控制台](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-tutorial)， [CyberArk SAML Authentication](https://docs.microsoft.com/azure/active-directory/saas-apps/cyberark-saml-authentication-tutorial)， [Scrible Edu](https://www.scrible.com/sign-in/#/create-account)， [PandaDoc](https://docs.microsoft.com/azure/active-directory/saas-apps/pandadoc-tutorial)， [PERCEPTYX](https://apexdata.azurewebsites.net/docs.microsoft.com/azure/active-directory/saas-apps/perceptyx-tutorial)， [Proptimise OS](https://proptimise.co.uk/software/)， [Vtiger CRM （SAML）](https://docs.microsoft.com/azure/active-directory/saas-apps/vtiger-crm-saml-tutorial)，适用于 oracle 零售营销的 oracle access manager，oracle IDCS for IDCS，oracle PeopleSoft for IDCS JD Edwards

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 监视 & 报告

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [缩放](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

- [Envoy](https://docs.microsoft.com/azure/active-directory/saas-apps/envoy-provisioning-tutorial)

- [Proxyclick](https://docs.microsoft.com/azure/active-directory/saas-apps/proxyclick-provisioning-tutorial)

- [4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)

---

### <a name="view-the-real-time-progress-of-the-azure-ad-provisioning-service"></a>查看 Azure AD 预配服务的实时进度

**键入：** 已更改的功能**服务类别：** 应用预配**产品功能：** 标识生命周期管理

我们更新了 Azure AD 预配体验，以包含一个新的进度栏，显示你在用户预配过程中的距离。 此更新的体验还提供了有关在当前周期中预配的用户数以及已设置为 "日期" 的用户数的信息。

有关详细信息，请参阅[检查用户预配的状态](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-when-will-provisioning-finish-specific-user)。

---

### <a name="company-branding-now-appears-on-sign-out-and-error-screens"></a>公司品牌现在显示在 "注销" 和 "错误" 屏幕上

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

我们更新了 Azure AD，使公司品牌现在显示在 "注销" 和 "错误" 屏幕以及登录页面上。 无需执行任何操作即可启用此功能，Azure AD 只需使用已在 Azure 门户的 "**公司品牌**" 区域中设置的资产。

有关设置公司品牌的详细信息，请参阅[将品牌添加到组织的 Azure Active Directory 页面](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="azure-multi-factor-authentication-mfa-server-is-no-longer-available-for-new-deployments"></a>Azure 多重身份验证（MFA）服务器不再适用于新部署

**键入：** 弃用的**服务类别：** MFA**产品功能：** 标识安全 & 保护

从2019年7月1日起，Microsoft 将不再为新部署提供 MFA 服务器。 希望在其组织中需要多重身份验证的新客户现在必须使用基于云的 Azure 多重身份验证。 在7月1日前激活 MFA 服务器的客户不会看到更改。 你仍可以下载最新版本，获取将来的更新，并生成激活凭据。

有关详细信息，请参阅[Azure 多重身份验证服务器](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfaserver-deploy)入门。 有关基于云的 Azure 多重身份验证的详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---

## <a name="may-2019"></a>2019 年 5 月

### <a name="service-change-future-support-for-only-tls-12-protocols-on-the-application-proxy-service"></a>服务更改：未来仅支持应用程序代理服务上的 TLS 1.2 协议

**键入：** 规划更改**服务类别：** 应用代理**产品功能：** 访问控制

为帮助我们的客户提供一流的加密，我们仅限制对应用程序代理服务上的 TLS 1.2 协议的访问。 此更改将逐步推出给已仅使用 TLS 1.2 协议的客户，因此你不会看到任何更改。

弃用 TLS 1.0 和 TLS 1.1 的情况在8月 2019 31 日发生，但我们将提供额外的高级通知，因此你有时间为此更改做好准备。 为准备此更改，请确保客户端服务器和浏览器的组合（包括用户用于访问通过应用程序代理发布的应用的任何客户端）已更新为使用 TLS 1.2 协议来维护与应用程序代理服务的连接。 有关详细信息，请参阅[在 Azure Active Directory 中通过应用程序代理添加用于远程访问的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#before-you-begin)。

---

### <a name="use-the-usage-and-insights-report-to-view-your-app-related-sign-in-data"></a>使用使用量和 insights 报表查看与应用相关的登录数据

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 监视 & 报告

你现在可以使用 "Azure 门户的"**企业应用程序**"区域中的" 使用情况和 insights "报表，以应用程序为中心查看登录数据，其中包括有关以下内容的信息：

- 适用于你的组织的热门应用

- 登录失败次数最多的应用程序

- 每个应用的最常见登录错误

有关此功能的详细信息，请参阅[Azure Active Directory 门户中的使用情况和见解报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-usage-insights-report)

---

### <a name="automate-your-user-provisioning-to-cloud-apps-using-azure-ad"></a>使用 Azure AD 自动完成用户预配到云应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 监视 & 报告

请按照以下新教程使用 Azure AD 预配服务，为以下基于云的应用自动创建、删除和更新用户帐户：

- [Comeet](https://docs.microsoft.com/azure/active-directory/saas-apps/comeet-recruiting-software-provisioning-tutorial)

- [DynamicSignal](https://docs.microsoft.com/azure/active-directory/saas-apps/dynamic-signal-provisioning-tutorial)

- [KeeperSecurity](https://docs.microsoft.com/azure/active-directory/saas-apps/keeper-password-manager-digitalvault-provisioning-tutorial)

你还可以遵循这个新的[Dropbox 教程](https://docs.microsoft.com/azure/active-directory/saas-apps/dropboxforbusiness-provisioning-tutorial)，该教程提供了有关如何预配组对象的信息。

有关如何通过自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="identity-secure-score-is-now-available-in-azure-ad-general-availability"></a>标识安全分数现已在 Azure AD 中提供（正式发布）

**键入：** 新功能**服务类别：** N/A**产品功能：** 标识安全 & 保护

你现在可以通过使用 Azure AD 中的 "标识" 安全分数功能来监视和改善标识安全状况。 标识安全分数功能使用单个仪表板来帮助你：

- 根据1到223之间的评分，客观地度量标识的安全状况。

- 规划标识安全改进

- 查看安全改进的成功率

有关标识安全分数功能的详细信息，请参阅 " [Azure Active Directory 中的标识安全分数是多少？"](https://docs.microsoft.com/azure/active-directory/fundamentals/identity-secure-score)。

---

### <a name="new-app-registrations-experience-is-now-available-general-availability"></a>现已推出新的应用注册体验（正式发布）

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 开发人员体验

全新的[应用注册](https://aka.ms/appregistrations)体验现已正式推出。 这种新体验包括你在 Azure 门户和应用程序注册门户中熟悉的所有关键功能，并通过以下方式改善它们：

- **更好地管理应用程序。** 你现在可以在一个位置看到所有应用，而不是跨不同门户查看应用。

- **简化的应用注册。** 从改进的导航体验到改进权限选择体验，现在可以更轻松地注册和管理应用。

- **更多详细信息。** 您可以找到有关您的应用程序的更多详细信息，包括快速入门指南等。

有关详细信息，请参阅[Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/)和[应用注册体验现已正式发布！](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/) 博客公告。

---

### <a name="new-capabilities-available-in-the-risky-users-api-for-identity-protection"></a>用于标识保护的有风险用户 API 中可用的新功能

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

我们很高兴地宣布，你现在可以使用有风险的用户 API 来检索用户的风险历史记录、解除有风险的用户以及确认用户是否受到侵害。 此更改有助于更有效地更新用户的风险状态并了解其风险历史记录。

有关详细信息，请参阅有[风险的用户 API 参考文档](https://docs.microsoft.com/graph/api/resources/riskyuser?view=graph-rest-beta)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2019"></a>Azure AD 应用库中提供了新的联合应用-可能为2019

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

在5月2019，我们已将这21个新应用和联合支持添加到应用库：

[Freedcamp](https://docs.microsoft.com/azure/active-directory/saas-apps/freedcamp-tutorial)， [Real Links](https://docs.microsoft.com/azure/active-directory/saas-apps/real-links-tutorial)， [Kianda](https://app.kianda.com/sso/OpenID/AzureAD/)，[简单符号](https://docs.microsoft.com/azure/active-directory/saas-apps/simple-sign-tutorial)， [Braze](https://docs.microsoft.com/azure/active-directory/saas-apps/braze-tutorial)， [Displayr](https://docs.microsoft.com/azure/active-directory/saas-apps/displayr-tutorial)， [Templafy](https://docs.microsoft.com/azure/active-directory/saas-apps/templafy-tutorial)， [Marketo 销售接洽](https://toutapp.com/login)， [ACLP](https://docs.microsoft.com/azure/active-directory/saas-apps/aclp-tutorial)， [OutSystems](https://docs.microsoft.com/azure/active-directory/saas-apps/outsystems-tutorial)， [Meta4 Global HR](https://docs.microsoft.com/azure/active-directory/saas-apps/meta4-global-hr-tutorial)，[量子工作区](https://docs.microsoft.com/azure/active-directory/saas-apps/quantum-workplace-tutorial)，[钴](https://docs.microsoft.com/azure/active-directory/saas-apps/cobalt-tutorial)， [webMethods API 云](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)， [RedFlag](https://pocketstop.com/redflag/) [，Whatfix，](https://docs.microsoft.com/azure/active-directory/saas-apps/whatfix-tutorial)JOBHUB [JOBHUB](https://docs.microsoft.com/azure/active-directory/saas-apps/jobhub-tutorial) [，NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/control-tutorial) [NEOGOV](https://docs.microsoft.com/azure/active-directory/saas-apps/neogov-tutorial) [Foodee](https://docs.microsoft.com/azure/active-directory/saas-apps/foodee-tutorial) [MyVR](https://docs.microsoft.com/azure/active-directory/saas-apps/myvr-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="improved-groups-creation-and-management-experiences-in-the-azure-ad-portal"></a>在 Azure AD 门户中改进了组的创建和管理体验

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

我们已改进了 Azure AD 门户中与组相关的体验。 这些改进使管理员能够更好地管理组列表、成员列表，并提供其他创建选项。

改进包括：

- 按成员身份类型和组类型进行的基本筛选。

- 添加新列，如源和电子邮件地址。

- 可以选择多个组、成员和所有者列表以便于删除。

- 可以选择电子邮件地址，并在创建组的过程中添加所有者。

有关详细信息，请参阅[使用 Azure Active Directory 创建基本组以及添加成员](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-general-availability"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（正式发布）

**键入：** 已更改的功能**服务类别：** 组管理**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词，这些字词在组名称中是不允许的（例如，"CEO，工资单，HR"）。

有关详细信息，请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="microsoft-graph-api-endpoints-are-now-available-for-azure-ad-activity-logs-general-availability"></a>Microsoft Graph API 终结点现可用于 Azure AD 活动日志（正式发布）

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

我们非常高兴地宣布 Microsoft Graph API 终结点支持 Azure AD 活动日志。 在此版本中，现在可以使用 Azure AD 审核日志的1.0 版，以及登录日志 Api。

有关详细信息，请参阅[Azure AD 审核日志 API 概述](https://docs.microsoft.com/graph/api/resources/azure-ad-auditlog-overview?view=graph-rest-1.0)。

---

### <a name="administrators-can-now-use-conditional-access-for-the-combined-registration-process-public-preview"></a>管理员现在可以将条件访问用于合并的注册过程（公共预览版）

**键入：** 新功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

管理员现在可以创建条件性访问策略以供组合注册页面使用。 这包括在以下情况下应用策略以允许注册：

- 用户处于受信任的网络中。

- 用户的登录风险较低。

- 用户在被管理的设备上。

- 用户同意组织的使用条款（TOU）。

有关条件访问和密码重置的详细信息，请参阅[Azure AD 组合 MFA 和密码重置注册体验博客文章的条件性访问](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Conditional-access-for-the-Azure-AD-combined-MFA-and-password/ba-p/566348)。 有关组合注册过程的条件性访问策略的详细信息，请参阅[组合注册的条件性访问策略](https://docs.microsoft.com/azure/active-directory/authentication/howto-registration-mfa-sspr-combined#conditional-access-policies-for-combined-registration)。 有关 Azure AD 使用条款功能的详细信息，请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-as-part-of-azure-ad-identity-protection"></a>新 Azure AD 威胁情报检测现在作为 Azure AD Identity Protection 的一部分提供

**键入：** 新功能**服务类别：** Azure AD Identity Protection**产品功能：** 标识安全 & 保护

Azure AD 威胁情报检测现在作为更新的 Azure AD Identity Protection 功能的一部分提供。 这一新功能可以帮助为特定用户或活动指出异常的用户活动，该活动与基于 Microsoft 内部和外部威胁智能源的已知攻击模式一致。

有关 Azure AD Identity Protection 的刷新版本的详细信息，请参阅[公共预览博客中的四个主要 Azure AD Identity Protection 增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)，以及[Azure Active Directory Identity Protection （已刷新）是什么？](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 部分。 有关 Azure AD 威胁智能检测的详细信息，请参阅[Azure Active Directory Identity Protection 风险检测](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks)一文。

---

### <a name="azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure AD 授权管理现已推出（公共预览版）

**键入：** 新功能**服务类别：** 标识调控**产品功能：** 标识调控

Azure AD 的权利管理，现在提供公共预览版，可帮助客户委托访问包的管理，该管理包定义了员工和业务合作伙伴如何请求访问权限、必须批准的人员以及他们有权访问的时间长短。 访问包可以管理 Azure AD 和 Office 365 组中的成员身份、企业应用程序中的角色分配以及 SharePoint Online 网站的角色分配。 有关权利管理的详细信息，请参阅[Azure AD 的权利管理概述](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要详细了解 Azure AD Identity Governance 功能的广度，包括 Privileged Identity Management、访问评审和使用条款，请参阅[什么是 Azure AD Identity Governance？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 门户中为 Office 365 组配置命名策略（公共预览版）

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

管理员现在可以使用 Azure AD 门户配置 Office 365 组的命名策略。 此更改有助于为组织中的用户创建或编辑的 Office 365 组强制实施一致的命名约定。

可以通过两种不同的方式为 Office 365 组配置命名策略：

- 定义自动添加到组名称中的前缀或后缀。

- 为你的组织上载一组自定义的阻止字词，这些字词在组名称中是不允许的（例如，"CEO，工资单，HR"）。

有关详细信息，请参阅为[Office 365 组强制实施命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活动日志现在 Azure Monitor 中可用（公开上市）

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

为了帮助解决有关使用 Azure AD 活动日志的可视化效果的反馈，我们在 Log Analytics 中引入了一个新的 Insights 功能。 此功能可帮助你使用称为工作簿的交互式模板获取有关 Azure AD 资源的见解。 这些预建工作簿可以提供应用程序或用户的详细信息，包括：

- **登录。** 提供应用和用户的详细信息，包括登录位置、正在使用的操作系统或浏览器客户端和版本，以及成功或失败的登录数。

- **旧身份验证和条件访问。** 提供有关使用旧身份验证的应用和用户的详细信息，包括条件访问策略触发的多重身份验证使用情况、使用条件性访问策略的应用等。

- **登录失败分析。** 帮助你确定登录错误是否是由于用户操作、策略问题或基础结构而发生的。

- **自定义报表。** 你可以创建新的工作簿或编辑现有工作簿，以帮助为你的组织自定义 Insights 功能。

有关详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年4月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年4月，我们已向应用程序库添加了以下21个具有联合支持的新应用：

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)， [HRworks 单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)， [Percolate](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)， [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)， [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)， [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)， [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)， [MileIQ](https://mileiq.onelink.me/991934284/7e980085)， [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)， [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)，RStudio[连接](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)， [AMMS](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)，Mitel[连接](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)，Alibaba [Cloud （基于角色的 SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)， [Certent 权益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial) [，Sectigo](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)[证书管理器](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)，GreenOrbit [，Workgrid，monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial) [SurveyMonkey Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)， [Indiggo](https://indiggolead.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>新的访问评审频率选项和多个角色选择

**键入：** 新功能**服务类别：** 访问评审**产品功能：** 标识调控

Azure AD 访问评审中的新更新可让你：

- 除了每周、每月、每季度和每年的现有选项外，还可以将访问评审的频率更改为**半年**。

- 创建单个访问评审时，请选择多个 Azure AD 和 Azure 资源角色。 在这种情况下，所有角色均设置为具有相同的设置，并同时通知所有审阅者。

有关如何创建访问评审的详细信息，请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>Azure AD Connect 电子邮件警报系统正在转换，为某些客户发送新的电子邮件发件人信息

**键入：** 已更改的功能**服务类别：** AD Sync**产品功能：** 平台

Azure AD Connect 正在转换电子邮件警报系统，可能会向某些客户显示新的电子邮件发件人。 若要解决此情况，你必须将添加 `azure-noreply@microsoft.com` 到你的组织的允许列表，否则无法继续接收来自 Office 365、Azure 或你的同步服务的重要警报。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN 后缀更改现在在 Azure AD Connect 中的联合域之间已成功完成

**键入：** 固定**服务类别：** AD Sync**产品功能：** 平台

你现在可以成功地将用户的 UPN 后缀从一个联合域更改为 Azure AD Connect 中的另一个联合域。 此修补程序意味着在同步周期内，你将不会再遇到 FederatedDomainChangeError 错误消息，也不会收到通知电子邮件，指出： "由于属性 [FederatedUser] 无效，无法在 Azure Active Directory 中更新此对象。 更新本地目录服务中的值。

有关详细信息，请参阅[在同步过程中解决错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD 中使用基于应用保护的条件性访问策略提高了安全性（公共预览版）

**键入：** 新功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

现在，可以使用 "**需要应用保护**策略" 来使用基于应用保护的条件性访问。 这一新策略有助于防止以下情况，从而提高组织的安全性：

- 无 Microsoft Intune 许可证即可访问应用的用户。

- 用户无法获取 Microsoft Intune 的应用保护策略。

- 用户访问应用时，无需配置 Microsoft Intune 应用保护策略。

有关详细信息，请参阅[如何使用条件性访问来要求适用于云应用访问的应用保护策略](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge 中对 Azure AD 单一登录和条件访问的全新支持（公共预览版）

**键入：** 新功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

我们已增强了对 Microsoft Edge 的 Azure AD 支持，包括提供对 Azure AD 单一登录和条件访问的全新支持。 如果以前使用过 Microsoft Intune Managed Browser，现在可以改为使用 Microsoft Edge。

有关使用条件性访问设置和管理设备和应用的详细信息，请参阅要求使用条件性访问的[云应用访问托管设备](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)，并[要求使用条件性访问为 cloud app access 批准客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 若要详细了解如何使用 Microsoft Intune 策略来管理使用 Microsoft Edge 的访问权限，请参阅[使用 Microsoft Intune 策略保护的浏览器管理 Internet 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>Azure Active Directory B2C 中的标识体验框架和自定义策略支持现已推出（GA）

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

你现在可以在 Azure AD B2C 中创建自定义策略，其中包括以下任务，这些任务是按比例支持的，并且在我们的 Azure SLA 下提供：

- 使用自定义策略创建并上传自定义身份验证用户旅程。

- 将用户旅程逐步描述为声明提供程序之间的交换。

- 在用户旅程中定义条件分支。

- 转换和映射要在实时决策和通信中使用的声明。

- 在自定义身份验证用户旅程中使用启用 REST API 的服务。 例如，通过电子邮件提供商、Crm 和专有授权系统。

- 与兼容 OpenIDConnect 协议的标识提供者联合。 例如，通过多租户 Azure AD、社交帐户提供程序或双因素验证提供程序。

有关创建自定义策略的详细信息，请参阅[Azure Active Directory B2C 中自定义策略的开发人员说明](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom)，并阅读[Alex Simon 的博客文章，包括案例研究](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年3月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年3月向应用程序库添加了这14个新应用和联合支持：

[ISEC7 移动 Exchange 委托](https://www.isec7.com/english/)， [MediusFlow](https://office365.cloudapp.mediusflow.com/)， [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial)， [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial)， [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial)，[基于解释的审核系统](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial)，[精益](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial)， [Powerschool 性能问题](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial)， [Cinode](https://cinode.com/)， [Iris Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial)， [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)， [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)， [Confirmit 视野](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)， [TAS](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>Azure AD 库中的新的 Zscaler 和 Atlassian 预配连接器-2019 年3月

**键入：** 新功能**服务类别：** 应用设置**产品功能：** 第三方集成

自动创建、更新和删除以下应用的用户帐户：

[Zscaler](https://aka.ms/ZscalerProvisioning)， [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning)， [Zscaler One](https://aka.ms/ZscalerOneProvisioning)， [Zscaler 2](https://aka.ms/ZscalerTwoProvisioning)， [Zscaler 3](https://aka.ms/ZscalerThreeProvisioning)， [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning)， [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

有关如何通过自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://aka.ms/ProvisioningDocumentation)。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>在 Azure AD 门户中还原和管理已删除的 Office 365 组

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

你现在可以从 Azure AD 门户查看和管理已删除的 Office 365 组。 此更改可帮助你查看可还原哪些组，并允许你永久删除你的组织不需要的任何组。

有关详细信息，请参阅[还原过期或删除的组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>单一登录现在可用于通过应用程序代理 Azure AD SAML 保护的本地应用（公共预览版）

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 访问控制

你现在可以为本地的、通过身份验证的应用提供单一登录（SSO）体验，还可以通过应用程序代理对这些应用进行远程访问。 有关如何为本地应用设置 SAML SSO 的详细信息，请参阅[具有应用程序代理（预览版）的本地应用程序的 saml 单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>将中断请求循环中的客户端应用，以提高可靠性和用户体验

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

客户端应用程序可能会在短时间内错误地发出数百个相同的登录请求。 这些请求（无论是否成功）都对 IDP 的用户体验和提升的工作负荷产生了影响，增加了所有用户的延迟，降低了 IDP 的可用性。

此更新向 `invalid_grant` 客户端应用发送错误：在 `AADSTS50196: The server terminated an operation because it encountered a loop while processing a request` 短时间内多次发出重复请求，超出正常操作范围。 遇到此问题的客户端应用应显示交互式提示，要求用户重新登录。 有关此更改以及在遇到此错误时如何修复应用的详细信息，请参阅[身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>现已推出新的审核日志用户体验

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

我们创建了一个新的 Azure AD**审核日志**"页，以帮助提高可读性和搜索信息的方式。 若要查看新的 "**审核日志**" 页，请在 Azure AD 的 "**活动**" 部分中选择 "**审核日志**"。

![新的 "审核日志" 页，包含示例信息](media/whats-new/audit-logs-page.png)

有关新的 "**审核日志**" 页的详细信息，请参阅[Azure Active Directory 门户中的审核活动报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指南，可帮助防止意外管理员从错误配置的条件访问策略中锁定

**键入：** 已更改的功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

为了帮助防止管理员因配置错误的条件性访问策略而意外地锁定自己的租户，我们在 Azure 门户中创建了新的警告和更新指南。 有关新指南的详细信息，请参阅[什么是服务依赖关系 Azure Active Directory 条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>改善了移动设备上最终用户的使用体验

**键入：** 已更改的功能**服务类别：** 使用条款**产品功能：** 调控

我们更新了现有的使用条款，以帮助改进你查看和同意移动设备上使用条款的方式。 现在可以放大和缩小，返回，下载信息，并选择 "超链接"。 有关更新的使用条款的详细信息，请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>新 Azure AD 活动日志下载体验可用

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

你现在可以直接从 Azure 门户下载大量活动日志。 此更新可让你：

- 下载最多250000行。

- 下载完成后获取通知。

- 自定义文件名。

- 确定输出格式，可以是 JSON 或 CSV。

有关此功能的详细信息，请参阅[快速入门：使用 Azure 门户下载审核报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大更改： Exchange ActiveSync （EAS）对条件评估的更新

**键入：** 规划更改**服务类别：** 条件访问**产品功能：** 访问控制

我们正在更新 Exchange ActiveSync （EAS）如何评估下列条件：

- 用户位置，基于国家/地区、区域或 IP 地址

- 登录风险

- 设备平台

如果以前在条件性访问策略中使用这些条件，请注意条件行为可能会发生更改。 例如，如果你以前在策略中使用了用户位置条件，你可能会发现现在会根据用户的位置跳过策略。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可配置 Azure AD SAML 令牌加密（公共预览版）

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

你现在可以配置任何受支持的 SAML 应用以接收加密的 SAML 令牌。 当配置并与应用一起使用时，Azure AD 使用从存储在 Azure AD 中的证书获取的公钥来加密发出的 SAML 断言。

有关配置 SAML 令牌加密的详细信息，请参阅[Configure AZURE AD saml token encryption](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 访问评审为组或应用创建访问评审

**键入：** 新功能**服务类别：** 访问评审**产品功能：** 调控

你现在可以将多个组或应用添加到组成员身份或应用分配的单个 Azure AD 访问评审中。 将使用相同的设置设置访问评审，并同时通知所有包含的审阅者。

有关如何使用 Azure AD 访问评审创建访问评审的详细信息，请参阅[在 Azure AD 访问评审中创建组或应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年2月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2019年2月，我们已将这27个新应用和联合支持添加到应用库：

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial)， [MINDTICKLE](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)， [FAT FINGER](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)， [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)， [Oracle 合成 ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial)， [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial)， [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)， [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)， [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)， [Soloinsight-CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)，权限单击， [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)， [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)， [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)， [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial)，[地震](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial)，[共享梦想](https://www.shareadream.org/how-it-works)， [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial)，webMethods [Integration Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)，[随时随地 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial) [，Periscope](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial) [，Netop，](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)smartvid.io [，PureCloud，](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial) [Genesys，ClickUp](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)[生产力平台](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增强组合的 MFA/SSPR 注册

**键入：** 更改的功能**服务类别：** 自助服务密码重置**产品功能：** 用户身份验证

为了响应客户反馈，我们增强了合并的 MFA/SSPR 注册预览体验，帮助用户更快地注册 MFA 和 SSPR 的安全信息。

**若要为你的用户提供增强的体验，请执行以下步骤：**

1. 以全局管理员或用户管理员身份登录到 Azure 门户，并**Azure Active Directory > "用户设置" > "管理访问面板预览功能的设置"**。

2. 在**可以使用 "注册和管理安全信息的预览功能-刷新**" 选项的用户中，选择为**选定的一组用户**或为**所有用户**启用功能。

在接下来的几周，我们将删除为尚未打开的租户启用旧的组合 MFA/SSPR 注册预览体验的功能。

**若要查看是否将为租户删除控件，请执行以下步骤：**

1. 以全局管理员或用户管理员身份登录到 Azure 门户，并**Azure Active Directory > "用户设置" > "管理访问面板预览功能的设置"**。

2. 如果**可以使用 "注册和管理安全信息的预览功能**" 选项的用户设置为 "**无**"，则将从租户中删除该选项。

无论先前是否为用户启用了旧组合的 MFA/SSPR 注册预览体验，都将在将来的某个日期关闭旧体验。 为此，我们强烈建议尽快迁移到新的增强体验。

有关增强的注册体验的详细信息，请参阅[Azure AD 结合了 MFA 和密码重置注册体验中的超酷增强功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>为用户流更新的策略管理体验

**键入：** 已更改的功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

我们更新了用户流（以前称为内置策略）的策略创建和管理过程。 这种新体验现在是所有 Azure AD 租户的默认体验。

可以通过使用门户屏幕顶部的 "**向我们发送反馈**" 区域中的笑脸或哭脸图标来提供其他反馈和建议。

有关新策略管理体验的详细信息，请参阅[Azure AD B2C 现在有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>选择 Azure AD B2C 提供的特定页面元素版本

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

你现在可以选择 Azure AD B2C 提供的页面元素的特定版本。 通过选择特定版本，你可以在更新出现在页面上之前对其进行测试，你可以获得可预测的行为。 此外，你现在可以选择强制实施特定页面版本以允许 JavaScript 自定义。 若要启用此功能，请转到用户流中的 "**属性**" 页。

有关选择页面元素特定版本的详细信息，请参阅 " [Azure AD B2C 现在有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>B2C （GA）的可配置最终用户密码要求

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

你现在可以为最终用户设置组织的密码复杂性，而不必使用本机 Azure AD 密码策略。 从用户流的 "**属性**" 边栏选项卡（以前称为内置策略），可以选择**简单**或**强**的密码复杂性，也可以创建一组**自定义**的要求。

有关密码复杂性要求配置的详细信息，请参阅[在 Azure Active Directory B2C 中配置密码的复杂性要求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>用于自定义品牌身份验证体验的新默认模板

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

你可以使用新的默认模板，这些模板位于用户流（以前称为内置策略）的 "**页面布局**" 边栏选项卡上，为用户创建自定义品牌的身份验证体验。

有关使用模板的详细信息，请参阅[Azure AD B2C 现在具有 JavaScript 自定义和更多新功能](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---

## <a name="january-2019"></a>2019 年 1 月

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>使用一次性密码身份验证（公共预览版）的 Active Directory B2B 协作

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C

已为无法通过 Azure AD、Microsoft 帐户 (MSA) 或 Google 联合身份验证等其他方式进行身份验证的 B2B 来宾用户引入了一次性密码认证 (OTP)。 这种新的身份验证方法意味着来宾用户无需创建新的 Microsoft 帐户。 相反，在兑换邀请或访问共享资源时，来宾用户可以请求将临时代码发送到电子邮件地址。 使用此临时代码，访客便可以继续登录。

有关详细信息，请参阅[电子邮件一次性密码身份验证（预览）](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)和博客 [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)（Azure AD 使拥有任意帐户的任何用户都能无缝地共享和协作）。

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>新的 Azure AD 应用程序代理 Cookie 设置

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 访问控制

我们引入了三种新的 Cookie 设置，这些设置可以供通过应用程序代理发布的应用使用：

- **使用仅限 HTTP 的 cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **HTTPOnly** 标志。 启用此设置会带来更多的安全好处，例如有助于防止用户通过客户端脚本复制或修改 Cookie。 建议启用此标志（选择“是”****）以享受增加的安全好处。

- **使用安全 cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **Secure** 标志。 启用此设置可确保 Cookie 仅通过 TLS 安全通道（例如 HTTPS）传输，因此会带来更多的安全好处，。 建议启用此标志（选择“是”****）以享受增加的安全好处。

- **使用持久性 cookie。** 防止访问 Cookie 在关闭 Web 浏览器时过期。 这些 Cookie 可以持续到访问令牌的生存期结束。 不过，如果过期时间已到，或者用户手动删除了 Cookie，则 Cookie 会重置。 建议保持默认设置“否”，只有在应用较旧且不需在进程之间共享 Cookie 时，才启用该设置。****

有关新 Cookie 的详细信息，请参阅[用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 1 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2019 年 1 月将这 35 款支持联合的新应用添加到了应用库中：

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial)、[Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial)、[Talent Palette](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial)、[Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)、[Cisco Umbrella](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial)、[Zscaler Internet Access Administrator](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial)、[Expiration Reminder](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)、[InstaVR Viewer](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial)、[CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial)、[Verb](https://app.verb.net/login)、[OpenLattice](https://openlattice.com/agora)、[TheOrgWiki](https://www.theorgwiki.com/signup)、[Pavaso Digital Close](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial)、[GoodPractice Toolkit](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial)、[Cloud Service PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial)、[AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial)、[iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial)、[Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)、[CallPlease](https://webapp.callplease.com/create-account/create-account.html)、[GTNexus SSO System](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial)、[CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial)、[Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial)、[Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial)、[Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial)、[ARES for Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial)、[K2 for Office 365](https://www.k2.com/O365)、[Xledger](https://www.xledger.net/)、[iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial)、[HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial)、[Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)、[Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial)、[Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial)、[Adoddle cSaas Platform](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>新的“Azure AD 标识保护”增强功能（公共预览版）

**键入：** 已更改的功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

我们很高兴地宣布，我们已为“Azure AD 标识保护”公开预览版套餐添加以下增强功能，其中包括：

- 经过更新后集成度更高的用户界面

- 其他 API

- 通过机器学习改进风险评估

- 针对风险用户和风险登录实现产品范围的符合性

有关增强功能的详细信息，请参阅[什么是 Azure Active Directory 标识保护（已刷新）？](https://aka.ms/IdentityProtectionDocs) 以进行详细的了解并通过产品内提示共享自己的想法。

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS 和 Android 设备上的 Microsoft Authenticator 应用的新应用锁定功能

**键入：** 新功能**服务类别：** Microsoft Authenticator 应用**产品功能：** 标识安全 & 保护

若要使你的一次性密码、应用信息和应用设置更加安全，可以在 Microsoft Authenticator 应用中开启应用锁定功能。 启用应用锁定意味着每次打开 Microsoft Authenticator 应用时，系统都会要求使用 PIN 或生物识别进行身份验证。

有关详细信息，请参阅 [Microsoft Authenticator app FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)（Microsoft Authenticator 应用常见问题解答）。

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>增强的 Azure AD Privileged Identity Management (PIM) 导出功能

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

Privileged Identity Management (PIM) 管理员现在可以为特定资源导出所有活动的和符合资格的角色分配，其中包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。

有关详细信息，请参阅[在 PIM 中查看 Azure 资源角色的活动和审核历史记录](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>从同步范围删除的用户不再切换到仅限云的帐户

**键入：** 固定**服务类别：** 用户管理**产品功能：** 目录

>[!Important]
>我们已听说并理解你因为此修复而遭受到的挫折感。 因此，我们已将此更改还原，直到我们可以让你更轻松地在组织中实施此修复。

我们修复了一个 bug，当 Active Directory 域服务（AD DS）对象从同步作用域中排除，然后移到下一同步周期的 Azure AD 中时，用户的 DirSyncEnabled 标志将错误地切换为**False** 。 进行此修复以后，如果将用户从同步范围中排除，然后又将其从 Azure AD 回收站还原，则用户帐户会保持预期的从本地 AD 同步的状态，不能在云中托管，因为其授权源 (SoA) 仍旧为本地 AD。

在进行此修复之前，将 DirSyncEnabled 标志切换为 False 会出现问题。 它给人一个错误印象：这些帐户已转换为仅限云的对象，并且可以在云中托管。 但是，这些帐户仍将其 SoA 作为本地项保留，并保留所有来自本地 AD 的已同步属性（影子属性）。 这种情况在 Azure AD 和其他特定的云工作负荷（例如 Exchange Online）中导致多个问题，这些工作负荷预期将这些帐户作为从 AD 同步的帐户处理，但现在这些帐户的表现就像是仅限云的帐户一样。

目前，若要将从 AD 同步的帐户真正地转换为仅限云的帐户，唯一的方法是在租户级别禁用 DirSync，以便触发一项传输 SoA 的后端操作。 此类 SoA 更改要求（但不限于）清除所有本地的相关属性（例如 LastDirSyncTime 属性和影子属性）并将一个信号发送到其他云工作负荷，以便将相应的对象也转换为仅限云的帐户。

因此，此修复可以防止对从 AD 同步的用户的 ImmutableID 属性进行直接更新，这种更新在过去的某些情况下是必需的。 顾名思义，根据设计，Azure AD 中对象的 ImmutableID 是不可更改的。 在 Azure AD Connect Health 和 Azure AD Connect Synchronization 客户端中实现的新功能用于解决以下方案的问题：

- **分阶段对多个用户进行大规模 ImmutableID 更新**

  例如，需要进行耗时很长的 AD DS 林间迁移。 解决方案：使用 Azure AD Connect 来**配置源锚**，并在用户进行迁移时，将 Azure AD 中的现有 ImmutableID 值复制到新林的本地 AD DS 用户的 "MS-DS-一致性" 属性。 有关详细信息，请参阅[将 msDS-ConsistencyGuid 用作 sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)。

- **一次性对多个用户进行大规模 ImmutableID 更新**

  例如，在实现 Azure AD Connect 时犯错，现在需要更改 SourceAnchor 属性。 解决方案：在租户级别禁用 DirSync，并清除所有无效的 ImmutableID 值。 有关详细信息，请参阅[禁用 Office 365 的目录同步](/office365/enterprise/turn-off-directory-synchronization)。

- **将本地用户与 Azure AD 中的现有用户重新匹配** 例如，在 AD DS 中重新创建的用户会在 Azure AD 帐户中生成一个重复的用户，系统不会将其与现有的 Azure AD 帐户（孤立对象）重新匹配。 解决方案：使用 Azure 门户中 Azure AD Connect Health 重新映射源定位点 ImmutableID。 有关详细信息，请参阅[孤立对象场景](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大更改：通过 Azure Monitor 更新审核和登录日志架构

**键入：** 已更改的功能**服务类别：** 报表**产品功能：** 监视 & 报表

目前，我们正在通过 Azure Monitor 发布审核和登录日志流，使你能够将日志文件与 SIEM 工具或 Log Analytics 无缝集成。 根据客户的反馈，同时为此功能的正式版通告做好准备，我们正在对架构进行以下更改。 在 1 月份的第一周，我们将完成这些架构更改并更新其相关的文档。

#### <a name="new-fields-in-the-audit-schema"></a>审核架构中的新字段
我们将添加一个新的“操作类型”字段，以提供针对资源执行的操作类型。**** 例如“添加”、“更新”或“删除”。************

#### <a name="changed-fields-in-the-audit-schema"></a>审核架构中已更改的字段
审核架构中的以下字段即将发生变化：

|字段名称|更改内容|旧值|新值|
|----------|------------|----------|----------|
|类别|以前它是“服务名称”字段，**** 现在是“审核类别”字段。**** “服务名称”已重命名为“loggedByService”字段。********|<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|<ul><li>用户管理</li><li>组管理</li><li>应用管理</li></ul>|
|targetResources|包括顶层的 **TargetResourceType**。|&nbsp;|<ul><li>策略</li><li>应用</li><li>用户</li><li>组</li></ul>|
|loggedByService|提供生成审核日志的服务的名称。|Null|<ul><li>帐户预配</li><li>核心目录</li><li>自助式密码重置</li></ul>|
|结果|提供审核日志的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失败</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>登录架构中已更改的字段
登录架构中的以下字段即将发生变化：

|字段名称|更改内容|旧值|新值|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|以前它是“conditionalaccessPolicies”字段，**** 现在是“appliedConditionalAccessPolicies”字段。****|没有变化|没有变化|
|conditionalAccessStatus|在登录时提供条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>禁用</li></ul>|
|appliedConditionalAccessPolicies: result|在登录时提供单个条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>禁用</li></ul>|

有关架构的详细信息，请参阅[解释 Azure Monitor（预览版）中的 Azure AD 审核日志架构](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>对监督式机器学习模型和风险评分引擎做出的“标识保护”改进

**键入：** 已更改的功能**服务类别：** 标识保护**产品功能：** 风险评分

对“标识保护”相关的用户和登录风险评估引擎所做的改进有助于提高用户风险评估的准确度和覆盖度。 管理员可能会注意到，用户风险级别不再与特定检测的风险级别直接相关，并且有风险登录事件的数量和级别都已增加。

风险检测现在由监督的机器学习模型进行评估，该模型通过使用用户登录的其他功能和检测模式来计算用户风险。 基于此模型，管理员可以发现风险评分较高的用户，即使与该用户关联的检测具有中低型的风险。

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理员可以使用 Microsoft Authenticator 应用（公共预览版）重置自己的密码

**键入：** 更改的功能**服务类别：** 自助服务密码重置**产品功能：** 用户身份验证

现在，Azure AD 管理员可以使用 Microsoft Authenticator 应用通知或者任何移动 Authenticator 应用或硬件令牌提供的代码重置自己的密码。 若要重置自己的密码，管理员现在可以使用以下两种方法：

- Microsoft Authenticator 应用通知

- 其他移动 Authenticator 应用/硬件令牌代码

- 电子邮件

- 电话呼叫

- 短信

有关使用 Microsoft Authenticator 应用重置密码的详细信息，请参阅 [Azure AD 自助密码重置 - 移动应用和 SSPR（预览版）](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新的 Azure AD 云设备管理员角色（公共预览版）

**键入：** 新功能**服务类别：** 设备注册和管理**产品功能：** 访问控制

管理员可将用户分配到新的云设备管理员角色，以执行云设备管理员任务。 分配有“云设备管理员”角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。

有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>在 Azure AD 中使用新的活动时间戳管理设备（公共预览版）

**键入：** 新功能**服务类别：** 设备注册和管理**产品功能：** 设备生命周期管理

我们认识到，随着时间的推移，你必须在 Azure AD 中刷新和停用组织的设备，以避免环境中存在过时设备。 为了帮助完成此过程，Azure AD 现在会使用新的活动时间戳更新设备，以帮助管理设备生命周期。

有关如何获取和使用此时间戳的详细信息，请参阅[如何：在 Azure AD 管理过时设备](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理员可以要求用户接受每个设备上的使用条款

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 调控

管理员现在可以打开 "**要求用户在每个设备上同意**" 选项，以要求用户在其租户上使用的每个设备上接受使用条款。

有关详细信息，请参阅[Azure Active Directory 使用条款功能的按设备使用条款部分](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理员可以根据定期计划将使用条款配置为过期

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 调控


管理员现在可以打开 "**过期同意**" 选项，根据指定的定期计划，使所有用户的使用条款过期。 可以实施每年、半年、每季或每月计划。 使用期限过期后，用户必须面向。

有关详细信息，请参阅[Azure Active Directory 使用条款功能的添加使用条款部分](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理员可以根据每个用户的计划将使用条款配置为过期

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 调控

管理员现在可以指定用户必须面向的使用期限。 例如，管理员可以指定用户必须面向每90天使用一次使用条款。

有关详细信息，请参阅[Azure Active Directory 使用条款功能的添加使用条款部分](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。

---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 角色的新 Azure AD Privileged Identity Management (PIM) 电子邮件

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

使用 Azure AD Privileged Identity Management (PIM) 的客户现在可以接收每周摘要电子邮件，其中包括过去七天的以下信息：

- 最符合条件的和永久性的角色分配概述

- 激活角色的用户数

- 已分配到 PIM 中的角色的用户数

- 已分配到 PIM 外部的角色的用户数

- PIM 中“永久”的用户数

有关 PIM 和可用电子邮件通知的详细信息，请参阅 [PIM 中的电子邮件通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)。

---

### <a name="group-based-licensing-is-now-generally-available"></a>基于组的许可现已推出正式版

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 目录

基于组的许可已过公共预览期，现已推出正式版。 正式版提高了此功能的可伸缩性，并添加了为单个用户重新处理基于组的许可分配的功能，以及结合 Office 365 E3/A3 许可证使用基于组的许可的功能。

有关基于组的许可的详细信息，请参阅 [Azure Active Directory 中基于组的许可是什么？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 11 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已在 2018 年 11 月将这 26 款支持联合的新应用添加到了应用库：

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 日志现在可与 Azure Log Analytics（公共预览版）配合使用

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

我们很高兴地宣布，现在可将 Azure AD 日志转发到 Azure Log Analytics！ 这项呼声最高的功能有助于更好地访问业务、运营和安全分析数据，以及监视基础结构。 有关详细信息，请参阅博客文章 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)（Azure Log Analytics 中的 Azure Active Directory 活动日志现已提供）。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 10 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已在 2018 年 10 月将这 14 款支持联合的新应用添加到了应用库：

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial)、[Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)、ambyint、[MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial)、[BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)、Dialpad、[ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial)、[Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial)、[Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)、[Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial)、[Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial)、[Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial)、[Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD 域服务电子邮件通知

**键入：** 新功能**服务类别：** Azure AD 域服务**产品功能：** Azure AD 域服务

Azure AD 域服务在 Azure 门户中提供有关托管域配置错误或问题的警报。 这些警报包含分步引导，使你能够在不联系支持人员的情况下尝试解决问题。

从 10 月份开始，可以自定义托管域的通知设置，以便在发生新警报时，向指定的一组人员发送电子邮件，而无需频繁地在门户中检查最新信息。

有关详细信息，请参阅 [Azure AD 域服务中的通知设置](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD 门户支持使用 ForceDelete 域 API 删除自定义域

**键入：** 已更改的功能**服务类别：** 目录管理**产品功能：** 目录

我们很高兴地宣布，现在可以使用 ForceDelete 域 API，通过将用户、组和应用等引用从自定义域名 (contoso.com) 异步重命名回到初始默认域名 (contoso.onmicrosoft.com)，来删除自定义域名。

如果组织不再使用自定义域名，或者你需要使用其他 Azure AD 的域名，此项更改可帮助你更快地删除自定义域名。

有关详细信息，请参阅[删除自定义域名](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)。

---

## <a name="september-2018"></a>2018 年 9 月

### <a name="updated-administrator-role-permissions-for-dynamic-groups"></a>已更新动态组的管理员角色权限

**键入：** 已修复的**服务类别：** 组管理**产品功能：** 协作

我们修复了相关的问题，使特定的管理员角色无需成为组的所有者，即可创建和更新动态成员身份规则。

这些角色为：

- 全局管理员

- Intune 管理员

- 用户管理员

有关详细信息，请参阅[创建动态组和检查状态](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule)

---

### <a name="simplified-single-sign-on-sso-configuration-settings-for-some-third-party-apps"></a>简化了某些第三方应用的单一登录 (SSO) 配置设置

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

我们已认识到，由于每种应用配置的独特性，为软件即服务 (SaaS) 应用设置单一登录 (SSO) 可能有一定的难度。 我们构建了一个简化的配置体验，可以自动填充以下第三方 SaaS 应用的 SSO 配置设置：

- Zendesk

- ArcGis Online

- Jamf Pro

若要开始使用这一单击体验，请参阅应用的**Azure 门户**  >  **SSO 配置**页。 有关详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

---

### <a name="azure-active-directory---where-is-your-data-located-page"></a>“Azure Active Directory - 数据位于何处?”页

**键入：** 新功能**服务类别：** 其他**产品功能：** GoLocal

在“Azure Active Directory - 数据位于何处”页中选择公司所在的区域，查看哪个 Azure 数据中心托管了所有 Azure AD 服务的 Azure AD 静态数据。**** 可以根据公司所在区域的特定 Azure AD 服务来筛选信息。

若要访问此功能和详细信息，请参阅 [Azure Active Directory - 数据位于何处](https://aka.ms/AADDataMap)。

---

### <a name="new-deployment-plan-available-for-the-my-apps-access-panel"></a>适用于“我的应用”访问面板的新部署计划

**键入：** 新功能**服务类别：** 我的应用**产品功能：** SSO

查看适用于“我的应用”访问面板的新部署计划 (https://aka.ms/deploymentplans)。
“我的应用”访问面板为用户提供查找和访问其应用的单一位置。 此门户还为用户提供自助服务功能，例如，请求访问应用和组，或代表他人管理对这些资源的访问。

有关详细信息，请参阅[什么是“我的应用”门户？](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)

---

### <a name="new-troubleshooting-and-support-tab-on-the-sign-ins-logs-page-of-the-azure-portal"></a>Azure 门户“登录日志”页上的新“故障排除和支持”选项卡

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

Azure 门户的 "**登录**" 页上的 "新的**故障排除和支持**" 选项卡旨在帮助管理员和支持工程师排查与 Azure AD 登录相关的问题。此新选项卡提供错误代码、错误消息和修正建议（如果有），以帮助解决问题。 如果无法解决问题，我们还提供使用“复制到剪贴板”体验创建支持票证的新方法，该体验可在支持票证中填充日志文件的“请求 ID”和“日期(UTC)”字段。************

![显示 "新建" 选项卡的登录日志](media/whats-new/troubleshooting-and-support.png)

---

### <a name="enhanced-support-for-custom-extension-properties-used-to-create-dynamic-membership-rules"></a>增强了用于创建动态成员身份规则的自定义扩展属性的支持

**键入：** 已更改的功能**服务类别：** 组管理**产品功能：** 协作

借助此项更新，在为用户创建动态成员身份规则时，现在在动态用户组规则生成器中单击“获取自定义扩展属性”链接，输入唯一的应用 ID，然后即可收到要使用的完整自定义扩展属性列表。**** 还可以刷新此列表，以获取该应用的任何新自定义扩展属性。

有关为动态成员身份规则使用自定义扩展属性的详细信息，请参阅[扩展属性和自定义扩展属性](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership#extension-properties-and-custom-extension-properties)

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>基于 Azure AD 应用的条件访问的新批准的客户端应用

**键入：** 规划更改**服务类别：** 条件访问**产品功能：** 标识安全和保护

以下应用包含在[批准的客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)列表中：

- 微软待办

- Microsoft Stream

有关详细信息，请参见:

- [Azure AD 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="new-support-for-self-service-password-reset-from-the-windows-7881-lock-screen"></a>Windows 7/8/8.1 锁屏界面中新的自助密码重置支持

**键入：** 新功能**服务类别：** SSPR**产品功能：** 用户身份验证

设置此新功能后，运行 Windows 7、8 或 Windows 8.1 的设备的**锁屏**界面中会向用户显示一个用于重置密码的链接。 单击该链接时，系统会引导用户完成与 Web 浏览器中相同的密码重置流程。

有关详细信息，请参阅[如何在 Windows 7、8 和 8.1 中启用密码重置](https://aka.ms/ssprforwindows78)

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>变更通知：授权代码不再可重复使用

**键入：** 规划更改**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 9 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已在 2018 年 9 月将这 16 款支持联合的新应用添加到了应用库：

[Uberflip](https://docs.microsoft.com/azure/active-directory/saas-apps/uberflip-tutorial)、[Comeet Recruiting Software](https://docs.microsoft.com/azure/active-directory/saas-apps/comeetrecruitingsoftware-tutorial)、[Workteam](https://docs.microsoft.com/azure/active-directory/saas-apps/workteam-tutorial)、[ArcGIS Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/arcgisenterprise-tutorial)、[Nuclino](https://docs.microsoft.com/azure/active-directory/saas-apps/nuclino-tutorial)、[JDA Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/jdacloud-tutorial)、[Snowflake](https://docs.microsoft.com/azure/active-directory/saas-apps/snowflake-tutorial)、NavigoCloud、[Figma](https://docs.microsoft.com/azure/active-directory/saas-apps/figma-tutorial)、join.me、[ZephyrSSO](https://docs.microsoft.com/azure/active-directory/saas-apps/zephyrsso-tutorial)、[Silverback](https://docs.microsoft.com/azure/active-directory/saas-apps/silverback-tutorial)、Riverbed Xirrus EasyPass、[Rackspace SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/rackspacesso-tutorial)、Enlyft SSO for Azure、SurveyMonkey、[Convene](https://docs.microsoft.com/azure/active-directory/saas-apps/convene-tutorial)、[dmarcian](https://docs.microsoft.com/azure/active-directory/saas-apps/dmarcian-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="support-for-additional-claims-transformations-methods"></a>对其他声明转换方法的支持

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

我们引入了新的声明转换方法 ToLower() 和 ToUpper()，可以在基于 SAML 的“单一登录配置”页中将这些方法应用到 SAML 令牌。****

有关详细信息，请参阅[如何为 Azure AD 中的企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

---

### <a name="updated-saml-based-app-configuration-ui-preview"></a>更新了基于 SAML 的应用配置 UI（预览版）

**键入：** 已更改的功能**服务类别：** 企业应用**产品功能：** SSO

已更新的基于 SAML 的应用配置 UI 提供：

- 用于配置基于 SAML 的应用的已更新演练体验。

- 为配置中缺失或错误的设置提供更直观的显示。

- 为证书过期通知添加多个电子邮件地址的功能。

- 新的声明转换方法 ToLower() 和 ToUpper() 等。

- 为企业应用上传你自己的令牌签名证书的方法。

- 为 SAML 应用设置 NameID 格式的方法，以及将 NameID 值设置为目录扩展的方法。

若要启用此更新视图，请在“单一登录”页的顶部单击“尝试新体验”链接。******** 有关详细信息，请参阅[教程：通过 Azure Active Directory 为应用程序配置基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications)。

---

## <a name="august-2018"></a>2018 年 8 月

### <a name="changes-to-azure-active-directory-ip-address-ranges"></a>对 Azure Active Directory IP 地址范围的更改

**键入：** 规划更改**服务类别：** 其他**产品功能：** 平台

我们正在为 Azure AD 引入更大的 IP 范围，这意味着如果你已为防火墙、路由器或网络安全组配置了 Azure AD IP 地址范围，则需要更新它们。 我们正在进行此更新，因此，在 Azure AD 添加新的终结点时，你不必再次更改防火墙、路由器或网络安全组 IP 范围配置。

在接下来的两个月中，网络流量将迁移到这些新范围。 若要保持不间断地提供服务，必须在 2018 年 9 月 10 日之前将这些更新的值添加到你的 IP 地址：

- 20.190.128.0/18

- 40.126.0.0/18

我们强烈建议不要删除旧的 IP 地址范围，直到你的所有网络流量都已迁移到新范围。 若要了解有关迁移的更新并了解何时可以删除旧范围，请参阅 [Office 365 URL 和 IP 地址范围](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)。

---

### <a name="change-notice-authorization-codes-will-no-longer-be-available-for-reuse"></a>变更通知：授权代码不再可重复使用

**键入：** 规划更改**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

从 2018 年 11 月 15 日起，Azure AD 不再允许对应用使用以前用过的身份验证代码。 此项安全变更有助于使 Azure AD 与 OAuth 规范保持一致，将在 v1 和 v2 终结点上强制实施。

如果应用重复使用授权代码来获取多个资源的令牌，则我们建议使用该代码获取刷新令牌，然后使用该刷新令牌获取其他资源的其他令牌。 授权代码只能使用一次，但刷新令牌可对多个资源使用多次。 尝试在 OAuth 代码流期间重用身份验证代码的任何应用都将收到 invalid_grant 错误。

有关此项更改和其他与协议相关的更改，请参阅[身份验证新增功能的完整列表](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes)。

---

### <a name="converged-security-info-management-for-self-service-password-sspr-and-multi-factor-authentication-mfa"></a>为自助密码重置 (SSPR) 和多重身份验证 (MFA) 融合了安全信息管理

**键入：** 新功能**服务类别：** SSPR**产品功能：** 用户身份验证

此新功能可帮助用户在单个位置和体验中管理 SSPR 和 MFA 的安全信息（例如，电话号码、移动应用等），而以前必须在两个不同的位置进行管理。

此融合体验也适用于使用 SSPR 或 MFA 的用户。 此外，如果组织未强制实施 MFA 或 SSPR 注册，则用户仍可通过“我的应用”门户注册组织允许的任何 MFA 或 SSPR 安全信息方法。

这是一个选用的公共预览版。 管理员可以针对选定的一组用户或者租户中的所有用户启用新体验（如果需要）。 有关融合体验的详细信息，请参阅[融合体验博客](https://cloudblogs.microsoft.com/enterprisemobility/2018/08/06/mfa-and-sspr-updates-now-in-public-preview/)

---

### <a name="new-http-only-cookies-setting-in-azure-ad-application-proxy-apps"></a>Azure AD 应用程序代理应用中的新 HTTP-Only Cookie 设置

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 访问控制

应用程序代理应用中有一个名为“HTTP-Only Cookie”的新设置。**** 此设置在应用程序代理的访问 Cookie 和会话 Cookie 的 HTTP 响应标头中包含 HTTPOnly 标志，阻止从客户端侧脚本访问 Cookie，并进一步阻止复制或修改 Cookie 等操作，以此提供更高的安全性。 尽管以前未使用过此标志，但始终使用 TLS 连接对 cookie 进行加密和传输，以帮助防止不适当的修改。

此项设置与使用 ActiveX 控件的应用（例如远程桌面）不兼容。 如果遇到这种情况，我们建议禁用此设置。

有关 HTTP-Only Cookie 设置的详细信息，请参阅[使用 Azure AD 应用程序代理发布应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-publish-azure-portal)。

---

### <a name="privileged-identity-management-pim-for-azure-resources-supports-management-group-resource-types"></a>Azure 资源的 Privileged Identity Management (PIM) 支持管理组资源类型

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

现在，可将实时激活和分配设置应用到管理组资源类型，就像应用到订阅、资源组和资源（例如 VM、应用服务等）一样。 此外，对管理组拥有管理员访问权限的任何人都可以在 PIM 中发现和管理该资源。

有关 PIM 和 Azure 资源的详细信息，请参阅[使用 Privileged Identity Management 发现和管理 Azure 资源](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-resource-roles-discover-resources)

---

### <a name="application-access-preview-provides-faster-access-to-the-azure-ad-portal"></a>使用“应用程序访问”（预览版）可以更快地访问 Azure AD 门户

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

目前，在使用 PIM 激活某个角色时，可能需要 10 分钟以上才能让权限生效。 如果选择使用“应用程序访问”（目前以公共预览版提供），则管理员可以在激活请求完成后立即访问 Azure AD 门户。

目前，“应用程序访问”仅支持 Azure AD 门户体验和 Azure 资源。 有关 PIM 和“应用程序访问”的详细信息，请参阅 [Azure AD Privileged Identity Management 是什么？](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-configure)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---august-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 8 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已在 2018 年 8 月将这 16 款支持联合的新应用添加到了应用库：

[Hornbill](https://docs.microsoft.com/azure/active-directory/saas-apps/hornbill-tutorial)、[Bridgeline Unbound](https://docs.microsoft.com/azure/active-directory/saas-apps/bridgelineunbound-tutorial)、[Sauce Labs - Mobile and Web Testing](https://docs.microsoft.com/azure/active-directory/saas-apps/saucelabs-mobileandwebtesting-tutorial)、[Meta Networks Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/metanetworksconnector-tutorial)、[Way We Do](https://docs.microsoft.com/azure/active-directory/saas-apps/waywedo-tutorial)、[Spotinst](https://docs.microsoft.com/azure/active-directory/saas-apps/spotinst-tutorial)、[ProMaster (by Inlogik)](https://docs.microsoft.com/azure/active-directory/saas-apps/promaster-tutorial)、SchoolBooking、[4me](https://docs.microsoft.com/azure/active-directory/saas-apps/4me-tutorial)、[Dossier](https://docs.microsoft.com/azure/active-directory/saas-apps/DOSSIER-tutorial)、[N2F - Expense reports](https://docs.microsoft.com/azure/active-directory/saas-apps/n2f-expensereports-tutorial)、[Comm100 Live Chat](https://docs.microsoft.com/azure/active-directory/saas-apps/comm100livechat-tutorial)、[SafeConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/safeconnect-tutorial)、[ZenQMS](https://docs.microsoft.com/azure/active-directory/saas-apps/zenqms-tutorial)、[eLuminate](https://docs.microsoft.com/azure/active-directory/saas-apps/eluminate-tutorial)、[Dovetale](https://docs.microsoft.com/azure/active-directory/saas-apps/dovetale-tutorial)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="native-tableau-support-is-now-available-in-azure-ad-application-proxy"></a>Azure AD 应用程序代理现已提供本机 Tableau 支持

**键入：** 已更改的功能**服务类别：** 应用代理**产品功能：** 访问控制

随着预身份验证协议已从 OpenID Connect 更新为 OAuth 2.0 代码授予协议，不再需要进行任何附加的配置就能在应用程序代理中使用 Tableau。 此项协议变更还有助于应用程序代理使用仅限 HTTP 的重定向（通常在 JavaScript 和 HTML 标记中受支持）来更好地支持更多新式应用。

有关 Tableau 本机支持的详细信息，请参阅 [Azure AD 应用程序代理现已提供本机 Tableau 支持](https://blogs.technet.microsoft.com/applicationproxyblog/2018/08/14/azure-ad-application-proxy-now-with-native-tableau-support)。

---

### <a name="new-support-to-add-google-as-an-identity-provider-for-b2b-guest-users-in-azure-active-directory-preview"></a>将 Google 添加为 Azure Active Directory 中 B2B 来宾用户的标识提供者的新支持（预览版）

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C

在组织中设置 Google 联合时，可让受邀的 Gmail 用户使用其现有 Google 帐户登录到共享的应用和资源，而无需创建个人 Microsoft 帐户 (MSA) 或 Azure AD 帐户。

这是一个选用的公共预览版。 有关 Google 联合的详细信息，请参阅[将 Google 添加为 B2B 来宾用户的标识提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

## <a name="july-2018"></a>2018 年 7 月

### <a name="improvements-to-azure-active-directory-email-notifications"></a>对 Azure Active Directory 电子邮件通知的改进

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 标识生命周期管理

通过以下服务发送的 Azure Active Directory (Azure AD) 电子邮件现在采用更新的设计，并应用对发件人电子邮件地址和发件人显示名称的更改：

- Azure AD 访问评审
- Azure AD Connect Health
- Azure AD 标识保护
- Azure AD 特权标识管理
- 企业应用到期证书通知
- 企业应用预配服务通知

电子邮件通知将从以下电子邮件地址和显示名称发送：

- 电子邮件地址：azure-noreply@microsoft.com
- 显示名称：Microsoft Azure

有关一些新电子邮件设计的示例以及详细信息，请参阅 [Azure AD PIM 中的电子邮件通知](https://go.microsoft.com/fwlink/?linkid=2005832)。

---

### <a name="azure-ad-activity-logs-are-now-available-through-azure-monitor"></a>Azure AD 活动日志现在通过 Azure Monitor 提供

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

Azure AD 活动日志现已推出适用于 Azure Monitor（Azure 的平台级监视服务）的公共预览版。 Azure Monitor 提供长期保留和无缝集成，此外还做出了以下方面的改进：

- 通过将日志文件路由到 Azure 存储帐户来实现长期保留。

- 无缝 SIEM 集成，无需编写或维护自定义脚本。

- 与自己的自定义解决方案、分析工具或事件管理解决方案无缝集成。

有关这些新功能的详细信息，请参阅博客文章 [Azure AD activity logs in Azure Monitor diagnostics is now in public preview](https://cloudblogs.microsoft.com/enterprisemobility/2018/07/26/azure-ad-activity-logs-in-azure-monitor-diagnostics-now-in-public-preview/)（Azure Monitor 诊断中的 Azure AD 活动日志现已推出公共预览版）和文档 [Azure Monitor 中的 Azure Active Directory 活动日志（预览版）](https://docs.microsoft.com/azure/active-directory/reporting-azure-monitor-diagnostics-overview)。

---

### <a name="conditional-access-information-added-to-the-azure-ad-sign-ins-report"></a>添加到 Azure AD 登录报告的条件性访问信息

**键入：** 新功能**服务类别：** 报表**产品功能：** 标识安全 & 保护

通过此项更新，可以查看用户登录时会评估哪些策略以及策略结果。 此外，报告现在包括用户使用的客户端应用类型，以便可以识别旧式协议流量。 现在还可以在报告条目中搜索关联 ID，这可以在面向用户的错误消息中找到。此 ID 可用于识别匹配的登录请求及排查其问题。

---

### <a name="view-legacy-authentications-through-sign-ins-activity-logs"></a>通过登录活动日志查看旧式身份验证

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

由于在登录活动日志中引入了“客户端应用”字段，客户现在可以查看使用旧式身份验证的用户****。 客户将可以使用登录 Microsoft Graph API 或通过 Azure AD 门户中的登录活动日志访问此信息，你可以使用**客户端应用程序**控件来筛选旧身份验证。 查看文档可了解更多详细信息。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---july-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 7 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2018 年 7 月将这 16 款支持联合的新应用添加到了应用库中：

[Innovation Hub](https://docs.microsoft.com/azure/active-directory/saas-apps/innovationhub-tutorial)、[Leapsome](https://docs.microsoft.com/azure/active-directory/saas-apps/leapsome-tutorial)、[Certain Admin SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/certainadminsso-tutorial)、PSUC Staging、[iPass SmartConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/ipasssmartconnect-tutorial)、[Screencast-O-Matic](https://docs.microsoft.com/azure/active-directory/saas-apps/screencast-tutorial)、PowerSchool Unified Classroom、[Eli Onboarding](https://docs.microsoft.com/azure/active-directory/saas-apps/elionboarding-tutorial)、[Bomgar Remote Support](https://docs.microsoft.com/azure/active-directory/saas-apps/bomgarremotesupport-tutorial)、[Nimblex](https://docs.microsoft.com/azure/active-directory/saas-apps/nimblex-tutorial)、[Imagineer WebVision](https://docs.microsoft.com/azure/active-directory/saas-apps/imagineerwebvision-tutorial)、[Insight4GRC](https://docs.microsoft.com/azure/active-directory/saas-apps/insight4grc-tutorial)、[SecureW2 JoinNow Connector](https://docs.microsoft.com/azure/active-directory/saas-apps/securejoinnow-tutorial)、[Kanbanize](../saas-apps/kanbanize-tutorial.md)、[SmartLPA](../saas-apps/smartlpa-tutorial.md)、[Skills Base](https://docs.microsoft.com/azure/active-directory/saas-apps/skillsbase-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-user-provisioning-saas-app-integrations---july-2018"></a>新用户预配 SaaS 应用集成 - 2018 年 7 月

**键入：** 新功能**服务类别：** 应用设置**产品功能：** 第三方集成

可以通过 Azure AD 自动创建、维护和删除 SaaS 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 对于 2018 年 7 月版本，我们为 Azure AD 应用库中的以下应用程序添加了用户预配支持：

- [Cisco WebEx](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial)

- [Bonusly](https://docs.microsoft.com/azure/active-directory/saas-apps/bonusly-provisioning-tutorial)

有关 Azure AD 库中支持用户预配的所有应用程序的列表，请参阅 [SaaS 应用程序与 Azure Active Directory 的集成](https://aka.ms/appstutorial)。

---

### <a name="connect-health-for-sync---an-easier-way-to-fix-orphaned-and-duplicate-attribute-sync-errors"></a>Connect Health for Sync - 解决孤立和重复属性同步错误的更简单方法

**键入：** 新功能**服务类别：** AD Connect**产品功能：** 监视 & 报告

Azure AD Connect Health 引入了自助补救，以帮助突出显示和解决同步错误。 此功能可以排查重复属性同步错误，并修复从 Azure AD 孤立的对象。 此诊断功能具有以下优点：

- 缩小重复属性同步错误的范围，并提供具体的修复方法

- 对专用 Azure AD 方案应用修复，通过一个步骤解决错误

- 无需升级或配置即可启用此功能

有关详细信息，请参阅[诊断并修正重复的属性同步错误](https://docs.microsoft.com/azure/active-directory/connect-health/active-directory-aadconnect-health-diagnose-sync-errors)

---

### <a name="visual-updates-to-the-azure-ad-and-msa-sign-in-experiences"></a>对 Azure AD 和 MSA 登录体验做了视觉更新

**键入：** 已更改的功能**服务类别：** Azure AD**产品功能：** 用户身份验证

我们已更新 Office 365 和 Azure 等 Microsoft 联机服务登录体验的 UI。 此项更改使得屏幕更简洁、更直观。 有关此项更改的详细信息，请参阅博客文章 [Upcoming improvements to the Azure AD sign-in experience](https://cloudblogs.microsoft.com/enterprisemobility/2018/04/04/upcoming-improvements-to-the-azure-ad-sign-in-experience/)（即将对 Azure AD 登录体验的改进）。

---

### <a name="new-release-of-azure-ad-connect---july-2018"></a>Azure AD Connect 新版本 - 2018 年 7 月

**键入：** 已更改的功能**服务类别：** 应用预配**产品功能：** 标识生命周期管理

Azure AD Connect 的最新版本包括：

- Bug 修复和支持性更新

- Ping-Federate 集成正式版

- 对最新版 SQL 2012 客户端的更新

有关此项更新的详细信息，请参阅 [Azure AD Connect：版本发行历史记录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history)

---

### <a name="updates-to-the-terms-of-use-end-user-ui"></a>使用条款更新使用最终用户 UI

**键入：** 已更改的功能**服务类别：** 使用条款**产品功能：** 调控

我们正在更新 TOU 最终用户 UI 中的“接受”字符串。

**当前文本。** 若要访问 [tenantName] 资源，必须接受使用条款。<br>**新文本。** 若要访问 [tenantName] 资源，必须阅读使用条款。

**当前文本：** 选择接受表示你同意上述所有使用条款。<br>**新文本：** 请单击“接受”，确认已阅读并理解使用条款。

---

### <a name="pass-through-authentication-supports-legacy-protocols-and-applications"></a>直通身份验证支持旧式协议和应用程序

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

直通身份验证现在支持旧式协议和应用。 现在完全支持以下限制：

- 用户无需完成新式新式身份验证，即可登录到旧式 Office 客户端应用程序 Office 2010 和 Office 2013。

- 仅在 Office 2010 上可以访问 Exchange 混合环境中的日历共享功能和闲/忙信息。

- 用户无需完成新式身份验证，即可登录到 Skype for Business 客户端应用程序。

- 用户登录到 PowerShell 版本 1.0。

- 使用 iOS 设置助手的 Apple 设备注册计划 (Apple DEP)。

---

### <a name="converged-security-info-management-for-self-service-password-reset-and-multi-factor-authentication"></a>为自助密码重置和多重身份验证融合了安全信息管理

**键入：** 新功能**服务类别：** SSPR**产品功能：** 用户身份验证

此新功能可让用户在单个体验中管理自助密码重置 (SSPR) 和多重身份验证 (MFA) 的安全信息（例如，电话号码、电子邮件地址、移动应用等）。 用户不再需要在两个不同的体验中为 SSPR 和 MFA 注册相同的安全信息。 此新体验也适用于具有 SSPR 或 MFA 的用户。

如果组织未强制实施 MFA 或 SSPR 注册，则用户可以通过“我的应用”门户注册安全信息。**** 在此门户中，用户可以注册针对 MFA 或 SSPR 启用的任何方法。

这是一个选用的公共预览版。 管理员可为租户中选定的一组用户或所有用户启用新体验（如果需要）。

---

### <a name="use-the-microsoft-authenticator-app-to-verify-your-identity-when-you-reset-your-password"></a>重置密码时使用 Microsoft Authenticator 应用验证身份

**键入：** 已更改的功能**服务类别：** SSPR**产品功能：** 用户身份验证

此功能可让非管理员使用 Microsoft Authenticator（或其他任何验证器应用）提供的通知或代码验证其身份。 管理员启用此自助密码重置方法后，已通过 aka.ms/mfasetup 或 aka.ms/setupsecurityinfo 注册移动应用的用户可以在重置密码时，使用其移动应用作为验证方法。

只能在要求使用两种方法重置密码的策略中启用移动应用通知。

---

## <a name="june-2018"></a>2018 年 6 月

### <a name="change-notice-security-fix-to-the-delegated-authorization-flow-for-apps-using-azure-ad-activity-logs-api"></a>更改通知：对使用 Azure AD 活动日志 API 的应用程序的委派授权流的安全修补

**键入：** 规划更改**服务类别：** 报表**产品功能：** 监视 & 报表

由于安全性更强，因此，我们必须对使用委派授权流访问[Azure AD 活动日志 api](https://aka.ms/aadreportsapi)的应用程序的权限进行更改。 此更改在 **2018 年 6 月 26 日**前生效。

如果你的任何应用使用 Azure AD 活动日志 Api，请按照以下步骤操作，以确保在发生更改后应用不会中断。

**更新应用权限**

1. 登录 Azure 门户，选择“Azure Active Directory”，然后选择“应用注册”********。
2. 选择使用 Azure AD 活动日志 API 的应用，依次选择“设置”、“所需权限”和“Windows Azure Active Directory”API************。
3. 在“启用访问权限”边栏选项卡的“委派的权限”******** 区域中，选中“读取目录数据”**** 旁边的框，然后选择“保存”****。
4. 选择“授予权限”，然后选择“是”********。

    >[!Note]
    >你必须是全局管理员才能向应用授予权限。

有关详细信息，请参阅访问 Azure AD 报告 API 的先决条件文章的[授予权限](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-prerequisites-azure-portal#grant-permissions)部分。

---

### <a name="configure-tls-settings-to-connect-to-azure-ad-services-for-pci-dss-compliance"></a>配置 TLS 设置以连接到 Azure AD 服务从而实现 PCI DSS 符合性

**键入：** 新功能**服务类别：** N/A**产品功能：** 平台

传输层安全性 (TLS) 是一种在两个通信应用程序间提供隐私和数据完整性的协议，是目前使用最广泛的安全协议。

[PCI 安全标准委员会](https://www.pcisecuritystandards.org/)已决定禁用早期版本的 TLS 和安全套接字层 (SSL)，以此支持启用新的和更安全的应用协议，该决定于 2018 年 6 月 30 日**** 开始生效。 这一改变意味着如果连接到 Azure AD 服务并要求符合 PCI DSS，则必须禁用 TLS 1.0。 有多个 TLS 版本可供使用，但 TLS 1.2 是可供 Azure Active Directory 服务使用的最新版本。 强烈建议直接为客户端/服务器和浏览器/服务器组合使用 TLS 1.2。

过时的浏览器可能不支持较新的 TLS 版本，例如 TLS 1.2。 若要查看浏览器支持哪些版本的 TLS，请转到 [Qualys SSL 实验室](https://www.ssllabs.com/)网站，然后单击“Test your browser”（测试浏览器）****。 建议将 Web 浏览器升级到最新版本，并且最好只启用 TLS 1.2。

**在各种浏览器中启用 TLS 1.2**

- **Microsoft Edge和 Internet Explorer（均使用 Internet Explorer 设置）**

    1. 打开 internet Explorer，选择 "**工具**" "  >  **Internet 选项**" "  >  **高级**"。
    2. 在“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”************。
    3. 关闭所有浏览器窗口并重启 Internet Explorer。

- **Google Chrome**

    1. 打开 Google Chrome，在地址栏键入“chrome://settings/”，然后按 Enter******。
    2. 展开“高级”选项，转到“系统”部分，然后选择“打开代理设置”************。
    3. 在“Internet 属性”框中，选择“高级”选项卡，转到“安全”部分，选择“使用 TLS 1.2”，然后选择“确定”********************。
    4. 关闭所有浏览器窗口并重启 Google Chrome。

- **Mozilla Firefox**

    1. 打开 Firefox，在地址栏键入“about:config”，然后按 Enter******。
    2. 搜索 TLS 一词，然后选择“security.tls.version.max”条目******。
    3. 将值设置为 3，强制浏览器使用最高 TLS 1.2 的版本，然后选择“确定”********。

        >[!NOTE]
        >Firefox 版本 60.0 支持 TLS 1.3，因此还可将 security.tls.version.max 值设置为 4****。

    4. 关闭所有浏览器窗口并重启 Mozilla Firefox。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---june-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 6 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2018 年 6 月将这 15 款支持联合的新应用添加到了应用库中：

[Skytap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skytap-tutorial)[Settling music](https://docs.microsoft.com/azure/active-directory/active-directory-saas-settlingmusic-tutorial)[SAML 1.1 Token enabled LOB App](https://docs.microsoft.com/azure/active-directory/active-directory-saas-saml-tutorial)[Supermood](https://docs.microsoft.com/azure/active-directory/active-directory-saas-supermood-tutorial)[Autotask](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)[Endpoint Backup](https://docs.microsoft.com/azure/active-directory/active-directory-saas-autotaskendpointbackup-tutorial)[Skyhigh Networks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-skyhighnetworks-tutorial)Smartway2、[TonicDM](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tonicdm-tutorial)[Moconavi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-moconavi-tutorial)[Zoho One](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zohoone-tutorial)[SharePoint on-premises](https://docs.microsoft.com/azure/active-directory/active-directory-saas-sharepoint-on-premises-tutorial)[ForeSee CX Suite](https://docs.microsoft.com/azure/active-directory/active-directory-saas-foreseecxsuite-tutorial)[Vidyard](https://docs.microsoft.com/azure/active-directory/active-directory-saas-vidyard-tutorial)[ChronicX](https://docs.microsoft.com/azure/active-directory/active-directory-saas-chronicx-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="azure-ad-password-protection-is-available-in-public-preview"></a>公共预览版中提供 Azure AD 密码保护功能

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 用户身份验证

使用 Azure AD 密码保护有助于杜绝环境中出现易于猜到的密码。 消除这些密码有助于降低遭受密码喷射型攻击时密码泄露的风险。

具体来说，Azure AD 密码保护有助于：

- 保护 Azure AD 和 Windows Server Active Directory (AD) 中的组织帐户。
- 阻止用户使用最常用密码列表上的密码，该列表包含 500 多个密码，以及这些密码的 100 多万个字符替换变体。
- 从 Azure AD 门户中的单一位置管理 Azure AD 密码保护，既适用于 Azure AD，也适用于本地 Windows Server AD。

有关 Azure AD 密码保护的详细信息，请参阅[消除组织中的劣质密码](https://aka.ms/aadpasswordprotectiondocs)。

---

### <a name="new-all-guests-conditional-access-policy-template-created-during-terms-of-use-creation"></a>创建使用条款期间创建的新 "所有来宾" 条件性访问策略模板

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 调控

创建使用条款时，还会为 "所有来宾" 和 "所有应用" 创建新的条件访问策略模板。 此全新的策略模板采用新创建的 ToU，简化了来宾的创建和执行过程。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="new-custom-conditional-access-policy-template-created-during-terms-of-use-creation"></a>创建使用条款期间创建的新的 "自定义" 条件访问策略模板

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 调控

创建使用条款时，还会创建一个新的 "自定义" 条件性访问策略模板。 此新策略模板允许你创建 ToU，然后立即转到 "条件性访问策略创建" 边栏选项卡，而无需手动导航门户。

有关详细信息，请参阅 [Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="new-and-comprehensive-guidance-about-deploying-azure-multi-factor-authentication"></a>有关部署 Azure 多重身份验证的全新详尽指南

**键入：** 新功能**服务类别：** 其他**产品功能：** 标识安全 & 保护

我们发布了有关如何在组织中部署 Azure 多重身份验证 (MFA) 的新分步指南。

若要查看 MFA 部署指南，请转到 GitHub 上的[身份部署指南](https://aka.ms/DeploymentPlans)存储库。 若要提供有关部署指南的反馈，请使用[部署计划反馈表](https://aka.ms/deploymentplanfeedback)。 如对部署指南有任何疑问，请通过 [IDGitDeploy](mailto:idgitdeploy@microsoft.com) 与我们联系。

---

### <a name="azure-ad-delegated-app-management-roles-are-in-public-preview"></a>Azure AD 委派的应用管理角色处于公共预览状态

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 访问控制

管理员现可委派应用管理任务，无需分配全局管理员角色。 新的角色和功能有：

- **新的标准 Azure AD 管理员角色：**

    - **应用程序管理员。** 授予管理所有应用各个方面的权限，包括注册、SSO 设置、应用分配和许可、应用代理设置和同意（Azure AD 资源除外）。

    - **云应用程序管理员。** 授予所有应用程序管理员权限，但应用代理除外，因为它不提供本地访问权限。

    - **应用程序开发人员。** 即使“允许用户注册应用”**** 选项已关闭，该角色也可授予创建应用注册的权限。

- **所有权（设置每个应用注册和每个企业应用，类似于群组所有权流程：**

    - **应用注册所有者。** 授予管理自有应用注册各个方面的权限，包括应用清单和添加其他所有者。

    - **企业应用所有者。** 授予管理自有企业应用许多方面的权限，包括 SSO 设置、应用分配和同意（Azure AD 资源除外）。

有关公共预览版的详细信息，请参阅 [Azure AD 委派的应用程序管理角色处于公共预览状态！](https://cloudblogs.microsoft.com/enterprisemobility/2018/06/13/hallelujah-azure-ad-delegated-application-management-roles-are-in-public-preview/) 博客。 有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal)。

---

## <a name="may-2018"></a>2018 年 5 月

### <a name="expressroute-support-changes"></a>ExpressRoute 支持更改

**键入：** 规划更改**服务类别：** 身份验证（登录）**产品功能：** 平台

软件即服务产品/服务，例如 Azure Active Directory (Azure AD)，设计为直接通过 Internet 时工作性能最好，不需要使用 ExpressRoute 或任何其他专用 VPN 隧道。 因此，在 **2018 年 8 月 1 日**，我们将停止支持将 ExpressRoute 用于使用 Azure 公共对等互连的 Azure AD 服务和 Microsoft 对等互连中的 Azure 社区。 受此更改影响的所有服务可能会注意到 Azure AD 流量逐步从 ExpressRoute 转移到 Internet。

同时我们正在改变我们的支持，我们也知道，在某些情况下，你可能仍然需要为身份验证流量使用专用的线路集。 因此，Azure AD 将继续使用 ExpressRoute 支持每租户 IP 范围限制，并继续通过“其他 Office 365 联机服务”社区支持 Microsoft 对等互连上已有的服务。 如果服务受到影响，但你需要 ExpressRoute，则必须执行以下操作：

- **如果当前在使用 Azure 公共对等互连。** 迁移到 Microsoft 对等互连并注册其他 Office 365 联机服务 (12076:5100) 社区****。 有关如何从 Azure 公共对等互连迁移到 Microsoft 对等互连的详细信息，请参阅[将公共对等互连迁移到 Microsoft 对等互连](https://docs.microsoft.com/azure/expressroute/how-to-move-peering)一文。

- **如果当前在使用 Microsoft 对等互连。** 注册其他 Office 365 联机服务 (12076:5100) 社区****。 有关路由要求的详细信息，请参阅 ExpressRoute 路由要求一文中的[对 BGP 社区的支持](https://docs.microsoft.com/azure/expressroute/expressroute-routing#bgp)部分。

如果必须继续使用专用线路，则需要与你的 Microsoft 帐户团队沟通如何获得授权来使用**其他 Office 365 联机服务 (12076:5100)** 社区。 MS Office 托管的评审委员会将验证你是否需要那些线路并确保你理解保留它们的技术影响。 尝试为 Office 365 创建路由筛选器的未经授权订阅将收到错误消息。

---

### <a name="microsoft-graph-apis-for-administrative-scenarios-for-tou"></a>使用 Microsoft Graph API 实现 TOU 管理方案

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 开发人员体验

我们添加了 Microsoft Graph Api，用于管理 Azure AD 使用条款的操作。 你可以创建、更新、删除使用条款对象。

---

### <a name="add-azure-ad-multi-tenant-endpoint-as-an-identity-provider-in-azure-ad-b2c"></a>在 Azure AD B2C 中将 Azure AD 多租户终结点添加为标识提供者

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

现在可以使用自定义策略在 Azure AD B2C 中将 Azure AD 常用终结点添加为标识提供者。 这样就可以为登录到应用程序的所有 Azure AD 用户提供单个入口点。 有关详细信息，请参阅 [Azure Active Directory B2C：让用户使用自定义策略登录到多租户 Azure AD 标识提供者](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-setup-commonaad-custom)。

---

### <a name="use-internal-urls-to-access-apps-from-anywhere-with-our-my-apps-sign-in-extension-and-the-azure-ad-application-proxy"></a>通过“我的应用登录扩展”和 Azure AD 应用程序代理，使用内部 URL 从任何位置访问应用

**键入：** 新功能**服务类别：** 我的应用**产品功能：** SSO

用户现在可以使用适用于 Azure AD 的“我的应用”安全登录扩展通过内部 URL 访问应用程序，即使在公司网络之外也是如此。 这适用于使用 Azure AD 应用程序代理发布的任何应用程序，适用于任何也安装了访问面板浏览器扩展的浏览器。 URL 重定向功能在用户登录此扩展后自动启用。 此扩展可以在 [Microsoft Edge](https://go.microsoft.com/fwlink/?linkid=845176)、[Chrome](https://go.microsoft.com/fwlink/?linkid=866367) 和 [Firefox](https://go.microsoft.com/fwlink/?linkid=866366) 上下载。

---

### <a name="azure-active-directory---data-in-europe-for-europe-customers"></a>Azure Active Directory - 将欧洲客户的数据保留在欧洲

**键入：** 新功能**服务类别：** 其他**产品功能：** GoLocal

根据隐私法和欧洲法律，欧洲客户的数据需保留在欧洲，不得复制到欧洲数据中心以外的区域。 此[文章](https://go.microsoft.com/fwlink/?linkid=872328)详述了哪些标识信息会存储在欧洲数据中心内，哪些标识信息会存储在欧洲数据中心外。

---

### <a name="new-user-provisioning-saas-app-integrations---may-2018"></a>新用户预配 SaaS 应用集成 - 2018 年 5 月

**键入：** 新功能**服务类别：** 应用设置**产品功能：** 第三方集成

可以通过 Azure AD 自动创建、维护和删除 SaaS 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 对于 2018 年 5 月版本，我们为 Azure AD 应用库中的以下应用程序添加了用户预配支持：

- [BlueJeans](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bluejeans-provisioning-tutorial)

- [Cornerstone OnDemand](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cornerstone-ondemand-provisioning-tutorial)

- [Zendesk](https://docs.microsoft.com/azure/active-directory/active-directory-saas-zendesk-provisioning-tutorial)

有关 Azure AD 库中支持用户预配的所有应用程序的列表，请参阅 [https://aka.ms/appstutorial](https://aka.ms/appstutorial) 。

---

### <a name="azure-ad-access-reviews-of-groups-and-app-access-now-provides-recurring-reviews"></a>针对组和应用访问的 Azure AD 访问评审功能现在提供定期评审

**键入：** 新功能**服务类别：** 访问评审**产品功能：** 调控

现在可以正式通过 Azure AD Premium P2 对组和应用进行访问评审。  管理员可以对组成员身份和应用程序分配的访问评审进行配置，使之自动按固定时间间隔（例如按月或按季）定期进行。

---

### <a name="azure-ad-activity-logs-sign-ins-and-audit-are-now-available-through-ms-graph"></a>现在可通过 MS Graph 获取 Azure AD 活动日志（登录和审核）

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

Azure AD 活动日志（包括登录和审核日志）现在可通过 Microsoft Graph API 获得。 我们已通过 Microsoft Graph API 公开了两个终结点来访问这些日志。 请查看我们的[文档](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)，了解如何以编程方式访问入门所需的 Azure AD 报告 API。

---

### <a name="improvements-to-the-b2b-redemption-experience-and-leave-an-org"></a>对 B2B 兑换体验和离开组织的体验的改进

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C

**即将兑换：** 使用 B2B API 与来宾用户共享资源后，你无需发送特殊邀请电子邮件。 大多数情况下，来宾用户可以访问资源，并可及时获得兑换体验。 再也不会因错过电子邮件而受到影响。 不会再询问你的来宾用户 "你是否单击了系统发送给你的兑换链接？"。 这意味着，在 SPO 使用邀请管理器以后，云附件可以对所有用户（包括内部用户和外部用户）使用同一个规范的 URL，不管兑换状态如何。

**新式兑换体验：** 再也不会有拆分的屏幕兑换登陆页。 用户会看到包含邀请组织的隐私声明的新式许可体验，就像使用第三方应用一样。

**来宾用户可以离开组织：** 用户与组织之间的关系结束后，他们可以自行处理，使其离开组织。 不再需要将邀请组织的管理员 "删除" 调用，就不再需要提高支持票证。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---may-2018"></a>Azure AD 应用库中推出的全新联合应用 - 2018 年 5 月

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2018 年 5 月将这 18 款支持联合的新应用添加到了我们的应用库中：

[AwardSpring](https://docs.microsoft.com/azure/active-directory/active-directory-saas-awardspring-tutorial)，Infogix Data3Sixty 控制， [Yodeck](https://docs.microsoft.com/azure/active-directory/active-directory-saas-infogix-tutorial)， [Jamf PRO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-jamfprosamlconnector-tutorial)， [KnowledgeOwl，Envi](https://docs.microsoft.com/azure/active-directory/active-directory-saas-knowledgeowl-tutorial) [MMIS](https://docs.microsoft.com/azure/active-directory/active-directory-saas-envimmis-tutorial)， [LaunchDarkly](https://docs.microsoft.com/azure/active-directory/active-directory-saas-launchdarkly-tutorial)， [Adobe CAPTIVATE 质数](https://docs.microsoft.com/azure/active-directory/active-directory-saas-adobecaptivateprime-tutorial)，[蒙太奇 Online](https://docs.microsoft.com/azure/active-directory/active-directory-saas-montageonline-tutorial)，[まなびポケット](https://docs.microsoft.com/azure/active-directory/active-directory-saas-manabipocket-tutorial)，OpenReel， [Arc 发布-SSO](https://docs.microsoft.com/azure/active-directory/active-directory-saas-arc-tutorial)，PlanGrid， [iWellnessNow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-iwellnessnow-tutorial)， [Proxyclick](https://docs.microsoft.com/azure/active-directory/active-directory-saas-proxyclick-tutorial)， [Riskware](https://docs.microsoft.com/azure/active-directory/active-directory-saas-riskware-tutorial)， [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-flock-tutorial) [Flock](https://docs.microsoft.com/azure/active-directory/active-directory-saas-plangrid-tutorial)， [Reviewsnap](https://docs.microsoft.com/azure/active-directory/active-directory-saas-reviewsnap-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="new-step-by-step-deployment-guides-for-azure-active-directory"></a>Azure Active Directory 的新分步部署指南

**键入：** 新功能**服务类别：** 其他**产品功能：** 目录

有关如何部署 Azure Active Directory （Azure AD）的循序渐进指南，包括自助服务密码重置（SSPR）、单一登录（SSO）、条件性访问（CA）、应用代理、用户预配、Active Directory 联合身份验证服务（ADFS）到传递身份验证（PTA）和 ADFS 到密码哈希同步（PHS）。

若要查看部署指南，请转到 GitHub 上的[身份部署指南](https://aka.ms/DeploymentPlans)存储库。 若要提供有关部署指南的反馈，请使用[部署计划反馈表](https://aka.ms/deploymentplanfeedback)。 如对部署指南有任何疑问，请通过 [IDGitDeploy](mailto:idgitdeploy@microsoft.com) 与我们联系。

---

### <a name="enterprise-applications-search---load-more-apps"></a>企业应用程序搜索 - 加载更多应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

找不到应用程序/服务主体？ 我们增加了在“企业应用程序”下的“所有应用程序”列表中加载更多应用程序的功能。 默认显示 20 个应用程序。 现在可以单击 "**加载更多**"，以查看其他应用程序。

---

### <a name="the-may-release-of-aadconnect-contains-a-public-preview-of-the-integration-with-pingfederate-important-security-updates-many-bug-fixes-and-new-great-new-troubleshooting-tools"></a>AADConnect 的 5 月发布内容包括与 PingFederate 的集成、重要安全更新、许多 Bug 修复和功能强大的全新故障排除工具的公共预览版。

**键入：** 已更改的功能**服务类别：** AD Connect**产品功能：** 标识生命周期管理

AADConnect 的 5 月发布内容包括与 PingFederate 的集成、重要安全更新、许多 Bug 修复和功能强大的全新故障排除工具的公共预览版。 [此处](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-version-history#118190)提供发行说明。

---

### <a name="azure-ad-access-reviews-auto-apply"></a>Azure AD 访问评审：自动应用

**键入：** 已更改的功能**服务类别：** 访问评审**产品功能：** 调控

现在可以正式通过 Azure AD Premium P2 对组和应用进行访问评审。 管理员可以进行配置，以便访问评审完成后自动应用评审者对该组或应用所做的更改。 管理员还可以指定在评审者未响应、删除访问权限、保留访问权限或采用系统建议的情况下，用户进行后续访问时会发生什么。

---

### <a name="id-tokens-can-no-longer-be-returned-using-the-query-response_mode-for-new-apps"></a>对于新应用，无法再使用 query response_mode 返回 ID 令牌。

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

在 2018 年 4 月 25 日当天或以后创建的应用将不再能够使用 query response_mode 来请求 id_token********。  这样可以为 Azure AD 提供内联的 OIDC 规范，有助于减少应用的受攻击面。  在 2018 年 4 月 25 之前创建的应用可以将 query response_mode 与值为 id_token 的 response_type 配合使用********。  从 AAD 请求 id_token 时返回的错误是**AADSTS70007：请求令牌时，"query" 不是受支持的 "response_mode" 值**。

fragment 和 form_post response_mode 继续有效 - 创建新的具有特定用途（例如，供应用代理使用）的应用程序对象时，请确保使用这两个 response_mode 中的一个，然后才能创建新应用程序********。

---

## <a name="april-2018"></a>2018 年 4 月

### <a name="azure-ad-b2c-access-token-are-ga"></a>Azure AD B2C 访问令牌已发布正式版

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

现在可以使用访问令牌访问受 Azure AD B2C 保护的 Web API。 该功能将从公共预览版过渡到正式版。 改进了配置 Azure AD B2C 应用程序和 Web API 的 UI 体验，并进行了其他微小改进。

有关详细信息，请参阅 [Azure AD B2C：请求访问令牌](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-access-tokens)。

---

### <a name="test-single-sign-on-configuration-for-saml-based-applications"></a>测试基于 SAML 的应用程序的单一登录配置

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

配置基于 SAML 的 SSO 应用程序时，能够在配置页上测试集成。 如果在登录期间遇到错误，可以在测试体验中提供错误，Azure AD 将提供用于解决特定问题的解决方法步骤。

有关详细信息，请参见:

- [针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)
- [如何在 Azure Active Directory 中调试对应用程序进行基于 SAML 的单一登录](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

---

### <a name="azure-ad-terms-of-use-now-has-per-user-reporting"></a>Azure AD 使用条款现在具有每用户报告

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性

管理员现在可以选择给定 ToU，查看已同意该 ToU 的所有用户以及同意发生的日期/时间。

有关详细信息，请参阅 [Azure AD 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="azure-ad-connect-health-risky-ip-for-ad-fs-extranet-lockout-protection"></a>Azure AD Connect Health：AD FS Extranet 锁定保护的风险 IP

**键入：** 新功能**服务类别：** 其他**产品功能：** 监视 & 报告

Connect Health 现在支持按小时或按天检测超过失败 U/P 登录次数阈值的 IP 地址的功能。 该功能提供的功能包括：

- 使用可自定义阈值按小时/按天生成的综合报告，显示失败登录的 IP 地址和次数。
- 基于电子邮件的警报，当特定 IP 地址按小时/按天超出了失败 U/P 登录次数阈值时显示。
- 用于对数据进行详细分析的下载选项

有关详细信息，请参阅[风险 IP 报表](https://aka.ms/aadchriskyip)。

---

### <a name="easy-app-config-with-metadata-file-or-url"></a>包含元数据文件或 URL 的简单应用配置

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

在“企业应用程序”页上，管理员可以上传 SAML 元数据文件，以便为 AAD 库和非库应用程序配置基于 SAML 的登录。

此外，还可以使用 Azure AD 应用程序联合元数据 URL 为目标应用程序配置 SSO。

有关详细信息，请参阅[针对不在 Azure Active Directory 应用程序库中的应用程序配置单一登录](https://docs.microsoft.com/azure/active-directory/active-directory-saas-custom-apps)。

---

### <a name="azure-ad-terms-of-use-now-generally-available"></a>Azure AD 使用条款现已正式发布

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性


Azure AD 使用条款已从公共预览版移动到正式发布。

有关详细信息，请参阅 [Azure AD 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="allow-or-block-invitations-to-b2b-users-from-specific-organizations"></a>允许或阻止向特定组织中的 B2B 用户发送邀请

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C


现在可以在 Azure AD B2B 协作中指定要与之共享和协作的合作伙伴组织。 为此，可以选择创建具体允许或拒绝域的列表。 使用这些功能阻止某个域时，员工可以不再向该域中的人员发送邀请。

这可帮助你控制对资源的访问，同时可使获得批准的用户的体验更流畅。

此 B2B 协作功能适用于所有 Azure Active Directory 的客户，可与条件性访问和标识保护等 Azure AD Premium 功能结合使用，以更精细地控制外部业务用户如何登录和获取访问权限。

有关详细信息，请参阅[允许或阻止向特定组织中的 B2B 用户发送邀请](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-allow-deny-list)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2018 年 4 月将这 13 款支持联合的新应用添加到了我们的应用库中：

Criterion HCM、[FiscalNote](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fiscalnote-tutorial)、[Secret Server (On-Premises)](https://docs.microsoft.com/azure/active-directory/active-directory-saas-secretserver-on-premises-tutorial)、[Dynamic Signal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-dynamicsignal-tutorial)、[mindWireless](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mindwireless-tutorial)、[OrgChart Now](https://docs.microsoft.com/azure/active-directory/active-directory-saas-orgchartnow-tutorial)、[Ziflow](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ziflow-tutorial)、[AppNeta Performance Monitor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-appneta-tutorial)、[Elium](https://docs.microsoft.com/azure/active-directory/active-directory-saas-elium-tutorial)、[Fluxx Labs](https://docs.microsoft.com/azure/active-directory/active-directory-saas-fluxxlabs-tutorial)、[Cisco Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-ciscocloud-tutorial)、Shelf、[SafetyNet](https://docs.microsoft.com/azure/active-directory/active-directory-saas-safetynet-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications-public-preview"></a>向 Azure AD 中 B2B 用户授予对本地应用程序（公共预览版）的访问权限

**键入：** 新功能**服务类别：** b2b**产品功能：** b2b/B2C

使用 Azure Active Directory (Azure AD) B2B 协作功能邀请合作伙伴组织中的来宾用户加入 Azure AD 的组织现在可以向这些 B2B 用户提供本地应用的访问权限。 这些本地应用可以结合 Kerberos 约束委派 (KCD) 使用基于 SAML 的身份验证或 Windows 集成身份验证 (IWA)。

有关详细信息，请参阅[向 B2B 用户授予 Azure AD 访问本地应用程序的权限](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-hybrid-cloud-to-on-premises)。

---

### <a name="get-sso-integration-tutorials-from-the-azure-marketplace"></a>从 Azure 市场获取 SSO 集成教程

**键入：** 已更改的功能**服务类别：** 其他**产品功能：** 第三方集成

如果[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps?page=1)中列出的应用程序支持基于 SAML 的单一登录，请单击 "**立即获取**"，提供与该应用程序关联的集成教程。

---

### <a name="faster-performance-of-azure-ad-automatic-user-provisioning-to-saas-applications"></a>加快 Azure AD 对 SaaS 应用程序的自动用户预配性能

**键入：** 已更改的功能**服务类别：** 应用设置**产品功能：** 第三方集成

以前，在以下情况下，将 Azure Active Directory 用户预配连接器用于 SaaS 应用程序（例如，Salesforce、ServiceNow 和 Box）的客户可能会遇到性能缓慢的情况：其 Azure AD 租户包含的组合用户和组超过 100,000 个，并且他们使用用户和组分配来确定应预配哪些用户。

在 2018 年 4 月 2 日，非常显著的性能增强功能已部署到 Azure AD 预配服务，以大幅减少执行 Azure Active Directory 和目标 SaaS 应用程序之间的初始同步所需的时间。

因此，与应用的初始同步需要多天或从未完成的许多客户现在可以在大约几分钟或几小时内完成。

有关详细信息，请参阅[预配期间会发生什么情况？](/azure//active-directory/app-provisioning/how-provisioning-works)

---

### <a name="self-service-password-reset-from-windows-10-lock-screen-for-hybrid-azure-ad-joined-machines"></a>从已加入混合 Azure AD 的计算机的 Windows 10 锁定屏幕进行自助服务密码重置

**键入：** 更改的功能**服务类别：** 自助服务密码重置**产品功能：** 用户身份验证

我们已更新 Windows 10 SSPR 功能，以支持已加入混合 Azure AD 的计算机。 此功能在 Windows 10 RS4 中提供，允许用户从 Windows 10 计算机的锁屏界面重置其密码。 已启用并已注册自助服务密码重置的用户可以利用此功能。

有关详细信息，请参阅[从登录屏幕进行 Azure AD 密码重置](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-sspr-windows)。

---

## <a name="march-2018"></a>2018 年 3月

### <a name="certificate-expire-notification"></a>证书过期通知

**键入：** 已修复的**服务类别：** 企业应用**产品功能：** SSO

当库或非库应用程序的证书即将过期时，Azure AD 会发送通知。

对于配置了基于 SAML 的单一登录的企业应用程序，某些用户无法收到通知。 此问题已解决。 如果证书在 7、30 和 60 天内过期，Azure AD 会发送通知。 可以在审核日志中查看此事件。

有关详细信息，请参见:

- [在 Azure Active Directory 中管理用于联合单一登录的证书](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs)
- [Azure Active Directory 门户中的“审核活动”报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-audit-logs)

---

### <a name="twitter-and-github-identity-providers-in-azure-ad-b2c"></a>Azure AD B2C 中的 Twitter 和 GitHub 标识提供者

**键入：** 新功能**服务类别：** B2C-消费者标识管理**产品功能：** B2B/B2C

现在能以标识提供者的身份在 Azure AD B2C 中添加 Twitter 或 GitHub。 Twitter 将从公共预览版过渡到正式版。 GitHub 即将发布公共预览版。

有关详细信息，请参阅[什么是 Azure AD B2B 协作？](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)

---

### <a name="restrict-browser-access-using-intune-managed-browser-with-azure-ad-application-based-conditional-access-for-ios-and-android"></a>使用 Intune Managed Browser Azure AD 适用于 iOS 和 Android 的基于应用程序的条件性访问，限制浏览器访问

**键入：** 新功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

**现在处于公开预览状态！**

**Intune Managed Browser SSO：** 员工可以将跨本机客户端（如 Microsoft Outlook）的单一登录和 Intune Managed Browser 用于 Azure AD 连接的所有应用。

**Intune Managed Browser 条件访问支持：** 现在，你可以要求员工使用基于应用程序的条件性访问策略的 Intune 托管浏览器。

有关详细信息，请阅读我们的[博客文章](https://cloudblogs.microsoft.com/enterprisemobility/2018/03/15/the-intune-managed-browser-now-supports-azure-ad-sso-and-conditional-access/)。

有关详细信息，请参见:

- [设置基于应用程序的条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

- [配置 Managed browser 策略](https://aka.ms/managedbrowser)

---

### <a name="app-proxy-cmdlets-in-powershell-ga-module"></a>PowerShell GA 模块中的应用代理 Cmdlet

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 访问控制

目前，PowerShell GA 模块中提供了对应用程序代理 cmdlet 的支持！ 这确实要求你在 PowerShell 模块上保持更新-如果你的年后超过一年，某些 cmdlet 可能会停止工作。

有关详细信息，请参阅 [AzureAD](https://docs.microsoft.com/powershell/module/Azuread/?view=azureadps-2.0)。

---

### <a name="office-365-native-clients-are-supported-by-seamless-sso-using-a-non-interactive-protocol"></a>使用非交互式协议的无缝 SSO 支持 Office 365 本机客户端

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

使用 Office 365 本机客户端（16.0.8730.xxxx 和更高版本）的用户在使用无缝 SSO 时会获得静默登录体验。 这项支持是通过在 Azure AD 中添加非交互式协议 (WS-Trust) 提供的。

有关详细信息，请参阅[如何使用无缝 SSO 在本机客户端上进行登录？](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso-how-it-works#how-does-sign-in-on-a-native-client-with-seamless-sso-work)

---

### <a name="users-get-a-silent-sign-on-experience-with-seamless-sso-if-an-application-sends-sign-in-requests-to-azure-ads-tenant-endpoints"></a>如果应用程序将登录请求发送到 Azure AD 的租用终结点，则用户在使用无缝 SSO 时会获得静默登录体验

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

如果应用程序（例如 `https://contoso.sharepoint.com`）向 Azure AD 的租户终结点（即 `https://login.microsoftonline.com/contoso.com/<..>` 或 `https://login.microsoftonline.com/<tenant_ID>/<..>`）而不是 Azure AD 的普通终结点 (`https://login.microsoftonline.com/common/<...>`) 发送登录请求，用户在使用无缝 SSO 时可以获得静默登录体验。

有关详细信息，请参阅[Azure Active Directory 无缝单一登录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)。

---

### <a name="need-to-add-only-one-azure-ad-url-instead-of-two-urls-previously-to-users-intranet-zone-settings-to-roll-out-seamless-sso"></a>只需将一个 Azure AD URL（以前为两个 URL）添加到用户的 Intranet 区域设置，即可实施无缝 SSO

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

若要向用户实施无缝 SSO，只需使用 Active Directory 中的组策略将一个 Azure AD URL 添加到用户的 Intranet 区域设置：`https://autologon.microsoftazuread-sso.com`。 以前，客户需要添加两个 URL。

有关详细信息，请参阅[Azure Active Directory 无缝单一登录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2018 年 3 月将这 15 款支持联合的新应用添加到了我们的应用库中：

[Boxcryptor](https://docs.microsoft.com/azure/active-directory/active-directory-saas-boxcryptor-tutorial)、[CylancePROTECT](https://docs.microsoft.com/azure/active-directory/active-directory-saas-cylanceprotect-tutorial)、Wrike、[SignalFx](https://docs.microsoft.com/azure/active-directory/active-directory-saas-signalfx-tutorial)、Assistant by FirstAgenda、[YardiOne](https://docs.microsoft.com/azure/active-directory/active-directory-saas-yardione-tutorial)、Vtiger CRM、inwink、[Amplitude](https://docs.microsoft.com/azure/active-directory/active-directory-saas-amplitude-tutorial)、[Spacio](https://docs.microsoft.com/azure/active-directory/active-directory-saas-spacio-tutorial)、[ContractWorks](https://docs.microsoft.com/azure/active-directory/active-directory-saas-contractworks-tutorial)、[Bersin](https://docs.microsoft.com/azure/active-directory/active-directory-saas-bersin-tutorial)、[Mercell](https://docs.microsoft.com/azure/active-directory/active-directory-saas-mercell-tutorial)、[Trisotech Digital Enterprise Server](https://docs.microsoft.com/azure/active-directory/active-directory-saas-trisotechdigitalenterpriseserver-tutorial)、[Qumu Cloud](https://docs.microsoft.com/azure/active-directory/active-directory-saas-qumucloud-tutorial)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="pim-for-azure-resources-is-generally-available"></a>Azure 资源的 PIM 已发布正式版

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

如果将 Azure AD Privileged Identity Management 用于目录角色，现在可将 PIM 的时限访问和分配功能用于 Azure 资源角色，例如订阅、资源组、虚拟机和 Azure 资源管理器支持的其他任何资源。 实时激活角色时强制实施多重身份验证，并根据批准的更改时间范围计划激活。 此外，此版本添加了公共预览版中未提供的增强功能，包括更新的 UI、审批工作流，并且能够延长即将过期的角色，以及续订过期的角色。

有关详细信息，请参阅 [Azure 资源的 PIM（预览）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)

---

### <a name="adding-optional-claims-to-your-apps-tokens-public-preview"></a>将可选声明添加到应用令牌（公共预览版）

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

Azure AD 应用现在可以在 JWT 或 SAML 令牌中请求自定义或可选声明。  因为大小或适用性方面的约束，这些有关用户或租户的声明默认不会包含在令牌中。  此功能目前已在 v1.0 和 v2.0 终结点上的 Azure AD 应用公共预览版中提供。  请参阅文档，以了解可添加的声明，以及如何编辑应用程序清单来请求这些声明。

有关详细信息，请参阅 [Azure AD 中的可选声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)。

---

### <a name="azure-ad-supports-pkce-for-more-secure-oauth-flows"></a>Azure AD 支持使用 PKCE 来提高 OAuth 流的安全性

**键入：** 新功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

已更新 Azure AD 文档来指明对 PKCE 的支持。使用 PKCE 可以在执行 OAuth 2.0 授权代码授予流期间提高通信的安全性。  v1.0 和 v2.0 终结点上同时支持 S256 和纯文本 code_challenges。

有关详细信息，请参阅[请求授权代码](https://docs.microsoft.com/azure/active-directory/develop/active-directory-v2-protocols-oauth-code#request-an-authorization-code)。

---

### <a name="support-for-provisioning-all-user-attribute-values-available-in-the-workday-get_workers-api"></a>Workday Get_Workers API 中提供预配所有用户属性值的支持

**键入：** 新功能**服务类别：** 应用设置**产品功能：** 第三方集成

从 Workday 到 Active Directory 和 Azure AD 的入站预配公共预览版现在支持提取和预配 Workday Get_Workers API 中可用的所有属性值。 除 Workday 入站预配连接器初始版本随附的属性以外，还支持数百个其他标准和自定义属性。

有关详细信息，请参阅：[自定义 Workday 用户属性列表](https://docs.microsoft.com/azure/active-directory/active-directory-saas-workday-inbound-tutorial#customizing-the-list-of-workday-user-attributes)

---

### <a name="changing-group-membership-from-dynamic-to-static-and-vice-versa"></a>将组成员身份从动态更改为静态，或反之

**键入：** 新功能**服务类别：** 组管理**产品功能：** 协作

可更改在组中管理成员身份的方式。 想要在系统中保留相同的组名称和 ID，使针对组的任何现有引用仍然有效时，这很有用；创建新组需要更新这些引用。
我们已更新 Azure AD 管理中心，以支持此功能。 现在，客户可将现有组从动态成员身份转换为分配的成员身份，或反之。 现有的 PowerShell cmdlet 仍可用。

有关详细信息，请参阅[Azure Active Directory 中组的动态成员身份规则](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)

---

### <a name="improved-sign-out-behavior-with-seamless-sso"></a>改进了无缝 SSO 的注销行为

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

以前，即使用户显式注销受 Azure AD 保护的应用程序，但如果他们尝试在其企业网络中从已加入域的设备再次访问 Azure AD 应用程序，系统也仍会使用无缝 SSO 自动将其登录。 实施此项更改后，将会支持注销。  这可以让用户选择使用相同或不同的 Azure AD 帐户登录，而不是使用无缝 SSO 自动登录。

有关详细信息，请参阅 [Azure Active Directory 无缝单一登录](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-sso)

---

### <a name="application-proxy-connector-version-154020-released"></a>应用程序代理连接器 1.5.402.0 版已发布

**键入：** 已更改的功能**服务类别：** 应用代理**产品功能：** 标识安全 & 保护

此连接器版本将在 11 月之前逐步推出。 此新连接器版本包含以下更改：

- 连接器现在会设置域级别而不是子域级别的 Cookie。 这可以确保更顺畅的 SSO 体验，并避免多余的身份验证提示。
- 支持区块编码请求
- 改进了连接器运行状况监视
- 多项 bug 修复和稳定性改进

有关详细信息，请参阅[了解 Azure AD 应用程序代理连接器](https://docs.microsoft.com/azure/active-directory/application-proxy-understand-connectors)。

---

## <a name="february-2018"></a>2018 年 2 月

### <a name="improved-navigation-for-managing-users-and-groups"></a>改进了用于管理用户和组的导航界面

**键入：** 规划更改**服务类别：** 目录管理**产品功能：** 目录

已简化用于管理用户和组的导航体验。 现在，可以从目录概述直接导航到所有用户的列表，并更轻松地访问已删除用户的列表。 还可以从目录概述直接导航到所有组的列表，并更轻松地访问组管理设置。 在目录概述页中，还可以搜索用户、组、企业应用程序或应用注册。

---

### <a name="availability-of-sign-ins-and-audit-reports-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>世纪互联运营的 Microsoft Azure（Azure 中国区世纪互联）提供登录和审核报告

**键入：** 新功能**服务类别：** Azure Stack**产品功能：** 监视 & 报告

世纪互联运营的 Microsoft Azure（Azure 中国区世纪互联）实例现在提供 Azure AD 活动日志报告。 包括以下日志：

- **登录活动日志** - 包括与租户关联的所有登录日志。

- **自助密码审核日志** - 包括所有 SSPR 审核日志。

- **目录管理审核日志** - 包括目录管理（例如用户管理、应用管理等）相关的所有审核日志。

使用这些日志，可以洞察环境的工作情况。 可以将提供的数据用于：

- 确定用户如何使用你的应用和服务。

- 排查妨碍用户完成其工作的问题。

有关如何使用这些报告的详细信息，请参阅 [Azure Active Directory 报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)。

---

### <a name="use-report-reader-role-non-admin-role-to-view-azure-ad-activity-reports"></a>使用“报告读取者”角色（非管理员角色）查看 Azure AD 活动报告

**键入：** 新功能**服务类别：** 报告**产品功能：** 监视 & 报告

作为使非管理员角色能够访问 Azure AD 活动日志的客户反馈，我们为 "报表读者" 角色中的用户启用了此功能，以便访问 Azure 门户中的登录和审核活动，并使用 Microsoft Graph API。

有关如何使用这些报告的详细信息，请参阅 [Azure Active Directory 报告](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-azure-portal)。

---

### <a name="employeeid-claim-available-as-user-attribute-and-user-identifier"></a>以用户属性和用户标识符的形式提供 EmployeeID 声明

**键入：** 新功能**服务类别：** 企业应用**产品功能：** SSO

可以通过企业应用程序 UI，将基于 SAML 登录的应用程序中的成员用户和 B2B 来宾的 **EmployeeID** 配置为用户标识符和用户属性。

有关详细信息，请参阅[在 Azure Active Directory 中为企业应用程序自定义 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="simplified-application-management-using-wildcards-in-azure-ad-application-proxy"></a>在 Azure AD 应用程序代理中使用通配符简化了应用程序管理

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 用户身份验证

为了简化应用程序部署并减少管理开销，我们现在支持使用通配符发布应用程序。 若要发布通配符应用程序，可以遵循标准的应用程序发布流，但需要在内部和外部 URL 中使用通配符。

有关详细信息，请参阅 [Azure Active Directory 应用程序代理中的通配符应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-wildcard)。

---

### <a name="new-cmdlets-to-support-configuration-of-application-proxy"></a>开发了新的 cmdlet 用于支持应用程序代理配置

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 平台

最新版本的 AzureAD PowerShell 预览版模块包含新的 cmdlet，可让客户使用 PowerShell 来配置应用程序代理应用程序。

新 cmdlet 如下：

- Get-AzureADApplicationProxyApplication
- Get-AzureADApplicationProxyApplicationConnectorGroup
- Get-AzureADApplicationProxyConnector
- Get-AzureADApplicationProxyConnectorGroup
- Get-AzureADApplicationProxyConnectorGroupMembers
- Get-AzureADApplicationProxyConnectorMemberOf
- New-AzureADApplicationProxyApplication
- New-AzureADApplicationProxyConnectorGroup
- Remove-AzureADApplicationProxyApplication
- Remove-AzureADApplicationProxyApplicationConnectorGroup
- Remove-AzureADApplicationProxyConnectorGroup
- Set-AzureADApplicationProxyApplication
- Set-AzureADApplicationProxyApplicationConnectorGroup
- Set-AzureADApplicationProxyApplicationCustomDomainCertificate
- Set-AzureADApplicationProxyApplicationSingleSignOn
- Set-AzureADApplicationProxyConnector
- Set-AzureADApplicationProxyConnectorGroup

---

### <a name="new-cmdlets-to-support-configuration-of-groups"></a>开发了新的 cmdlet 用于支持组配置

**键入：** 新功能**服务类别：** 应用代理**产品功能：** 平台

最新版本的 AzureAD PowerShell 模块包含用于在 Azure AD 中管理组的 cmdlet。 这些 cmdlet 以前在 AzureADPreview 模块中提供，现已添加到 AzureAD 模块

现已推出正式版的 Goup cmdlet 为：

- Get-AzureADMSGroup
- New-AzureADMSGroup
- Remove-AzureADMSGroup
- Set-AzureADMSGroup
- Get-AzureADMSGroupLifecyclePolicy
- New-AzureADMSGroupLifecyclePolicy
- Remove-AzureADMSGroupLifecyclePolicy
- Add-AzureADMSLifecyclePolicyGroup
- Remove-AzureADMSLifecyclePolicyGroup
- Reset-AzureADMSLifeCycleGroup
- Get-AzureADMSLifecyclePolicyGroup

---

### <a name="a-new-release-of-azure-ad-connect-is-available"></a>推出了新版 Azure AD Connect

**键入：** 新功能**服务类别：** AD Sync**产品功能：** 平台

Azure AD Connect 是在 Azure AD 与本地数据源（包括 Windows Server Active Directory 和 LDAP）之间同步数据的首选的工具。

>[!Important]
>此版本引入了架构和同步规则更改。 Azure AD Connect 同步服务在升级后将触发完全导入和完全同步步骤。 有关如何更改此行为的信息，请参阅[如何在升级后推迟完全同步](https://docs.microsoft.com/azure/active-directory/connect/active-directory-aadconnect-upgrade-previous-version#how-to-defer-full-synchronization-after-upgrade)。

此版本提供以下更新和更改：

**已修复的问题**

- 修复了在切换到下一页时，“分区筛选”页的后台任务的计时窗口问题。

- 修复了在 ConfigDB 自定义操作过程中导致访问冲突的 Bug。

- 修复了 Bug，因此可以从 SQL 连接超时恢复。

- 修复了带 SAN 通配符的证书无法通过先决条件检查的 Bug。

- 修复了在 AAD 连接器导出过程中导致 miiserver.exe 崩溃的 Bug。

- 修复了在运行 AAD Connect 向导来更改配置后，可以通过不断地尝试密码登录 DC 的 Bug

**新增功能和改进**

- 应用程序遥测 - 管理员可以切换此类数据的开/关设置。

- Azure AD 运行状况数据 - 管理员必须访问运行状况门户才能控制其运行状况设置。 等到服务策略更改以后，代理就会读取并强制实施它。

- 添加了设备写回配置操作以及用于页面初始化的进度栏。

- 改进了 HTML 报表的常规诊断功能以及 ZIP-Text/HTML 报表的完整数据收集功能。

- 提高了自动升级的可靠性并增加了更多的遥测，确保可以确定服务器的运行状况。

- 限制提供给以 AD 连接器帐户为基础的特权帐户的权限。 进行全新安装时，向导会限制特权帐户拥有的针对 MSOL 帐户的权限（前提是 MSOL 帐户已创建）。 这些更改会影响使用 Auto-Create 帐户执行的快速安装和自定义安装。

- 更改了安装程序。在进行 AADConnect 全新安装时，不需要 SA 特权。

- 添加了新的实用工具用于排查特定对象的同步问题。 目前，该实用程序用于检查以下问题：

    - Azure AD 租户中的已同步用户对象和用户帐户之间出现 UserPrincipalName 不匹配的情况。

    - 是否已通过域筛选将对象从同步中筛选出来

    - 是否已通过组织单位 (OU) 筛选将对象从同步中筛选出来

- 添加了新的实用工具，用于同步当前的密码哈希，该哈希存储在针对特定用户帐户的本地 Active Directory 中。 该实用程序不需要更改密码。

---

### <a name="applications-supporting-intune-app-protection-policies-added-for-use-with-azure-ad-application-based-conditional-access"></a>Intune 应用保护添加的应用程序，用于 Azure AD 基于应用程序的条件性访问

**键入：** 已更改的功能**服务类别：** 条件访问**产品功能：** 标识安全 & 保护

我们添加了更多的应用程序，这些应用程序支持基于应用程序的条件性访问。 现在，可以使用这些批准的客户端应用来访问 Office 365 和其他已连接到 Azure AD 的云应用。

在 2 月底之前将添加以下应用程序：

- Microsoft Power BI

- Microsoft Launcher

- Microsoft Invoicing

有关详细信息，请参见:

- [批准的客户端应用要求](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-update-to-mobile-experience"></a>使用条款移动体验的更新

**键入：** 已更改的功能**服务类别：** 使用条款**产品功能：** 合规性

显示使用条款后，现在可以单击 "**查看时遇到问题"。单击此处**。 单击此链接会在设备本地打开使用条款。 不管文档中的字体大小或设备屏幕大小如何，都可以根据需要进行缩放，以方便阅读文档。

---

## <a name="january-2018"></a>2018 年 1 月

### <a name="new-federated-apps-available-in-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

2018 年 1 月，应用库中添加了支持联合的以下新应用：

[IBM OpenPages](https://go.microsoft.com/fwlink/?linkid=864698)、[OneTrust 隐私管理软件](https://go.microsoft.com/fwlink/?linkid=861660)、[Dealpath](https://go.microsoft.com/fwlink/?linkid=863526)IriusRisk Federated Directory 和 [Fidelity NetBenefits](https://go.microsoft.com/fwlink/?linkid=864701)。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="sign-in-with-additional-risk-detected"></a>登录时检测到其他风险

**键入：** 新功能**服务类别：** 标识保护**产品功能：** 标识安全 & 保护

针对检测到的风险检测获得的见解与 Azure AD 订阅相关联。 使用 Azure AD Premium P2 版本时，可以获取有关所有基础检测的最详细的信息。

使用 Azure AD Premium P1 edition 时，许可证未涵盖的检测将显示为检测到其他风险检测的登录。

有关详细信息，请参阅 [Azure Active Directory 风险检测](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-risk-events)。

---

### <a name="hide-office-365-applications-from-end-users-access-panels"></a>在最终用户的访问面板中隐藏 Office 365 应用程序

**键入：** 新功能**服务类别：** 我的应用**产品功能：** SSO

现在，可以通过新的用户设置来更好地管理 Office 365 应用程序显示用户访问面板的方式。 如果只想在 Office 门户中显示 Office 应用，可以借助此选项来减少用户访问面板中的应用数量。 该设置位于“用户设置”中，带有“用户只能在 Office 365 门户中查看 Office 365 应用”标签********。

有关详细信息，请参阅[在 Azure Active Directory 的用户体验中隐藏应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。

---

### <a name="seamless-sign-into-apps-enabled-for-password-sso-directly-from-apps-url"></a>直接从应用的 URL 无缝登录到启用了密码 SSO 的应用

**键入：** 新功能**服务类别：** 我的应用**产品功能：** SSO

现已通过一个便捷工具提供“我的应用”浏览器扩展。该工具以快捷方式的形式显示在浏览器中，可实现“我的应用”单一登录功能。 安装后，用户将在浏览器中看到一个堆积圆点图标，单击该图标可快速访问应用。 用户现在可以：

- 可以从应用的登录页面直接登录到基于密码 SSO 的应用
- 使用快速搜索功能启动任何应用
- 在扩展中使用快捷方式访问最近使用的应用
- 此扩展适用于 Microsoft Edge、Chrome 和 Firefox。

有关详细信息，请参阅[我的应用安全登录扩展](../user-help/my-apps-portal-end-user-access.md#download-and-install-the-my-apps-secure-sign-in-extension)。

---

### <a name="azure-ad-administration-experience-in-azure-classic-portal-has-been-retired"></a>Azure 经典门户中的 Azure AD 管理体验已停用

**键入：** 弃用的**服务类别：** Azure AD**产品功能：** 目录

从 2018 年 1 月 8 日起，Azure 经典门户中的 Azure AD 管理体验已停用。 Azure 经典门户本身在同一时间也已停用。 今后，应使用 [Azure AD 管理员中心](https://aad.portal.azure.com)来执行所有基于门户的 Azure AD 管理。

---

### <a name="the-phonefactor-web-portal-has-been-retired"></a>PhoneFactor Web 门户已停用

**键入：** 弃用的**服务类别：** Azure AD**产品功能：** 目录

从 2018 年 1 月 8 日起，PhoneFactor Web 门户已停用。 此门户用于管理 MFA 服务器，但这些功能已移至 Azure 门户 (portal.azure.com)。

MFA 配置位于：“Azure Active Directory”\>“MFA 服务器”****

---

### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告

**键入：** 弃用的**服务类别：** 报告**产品功能：** 标识生命周期管理


随着新 Azure Active Directory 管理控制台的正式发布以及用于活动和安全报告的新 API 的推出，“/reports”终结点下面的报告 API 从 2017 年 12 月 31 日开始已停用。

**可用功能**

在过渡到新管理控制台的过程中，有 2 个新 API 可用于检索 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在可以通过 Microsoft Graph 中的 Identity Protection 风险检测 API 来访问以前通过安全报告提供的数据。

有关详细信息，请参见:

- [Azure Active Directory 报告 API 入门](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-api-getting-started-azure-portal)

- [Azure Active Directory 标识保护和 Microsoft Graph 入门](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection-graph-getting-started)

---

## <a name="december-2017"></a>2017 年 12 月

### <a name="terms-of-use-in-the-access-panel"></a>访问面板中的使用条款

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性

现在，可以转到访问面板并查看以前接受的使用条款。

请执行这些步骤：

1. 转到 [MyApps 门户](https://myapps.microsoft.com)并登录。

2. 在右上角选择自己的姓名，然后从列表中选择“个人资料”。****

3. 在“个人资料”中选择“查看使用条款”。********

4. 现在，可以查看已接受的使用条款。

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="new-azure-ad-sign-in-experience"></a>新 Azure AD 登录体验

**键入：** 新功能**服务类别：** Azure AD**产品功能：** 用户身份验证

Azure AD 和 Microsoft 帐户标识系统 UI 经过重新设计，现在拥有一致的外观。 此外，Azure AD 登录页会先收集用户名，然后在第二个屏幕上收集凭据。

有关详细信息，请参阅[现已推出新 Azure AD 登录体验的公共预览版](https://cloudblogs.microsoft.com/enterprisemobility/2017/08/02/the-new-azure-ad-signin-experience-is-now-in-public-preview/)。

---

### <a name="fewer-sign-in-prompts-a-new-keep-me-signed-in-experience-for-azure-ad-sign-in"></a>更少的登录提示：Azure AD 登录的新“使我保持登录状态”体验

**键入：** 新功能**服务类别：** Azure AD**产品功能：** 用户身份验证

Azure AD 登录页上的“使我保持登录状态”复选框已被替换为新提示，成功通过身份验证后会显示该提示****。

如果对此提示回答“是”，则服务会提供永久刷新令牌****。 此行为与在旧体验中选中“使我保持登录状态”复选框时相同****。 对于联合租户，此提示在使用联合服务成功完成身份验证后显示。

有关详细信息，请参阅[更少的登录提示：现已推出 Azure AD 新的“使我保持登录状态”体验的预览版](https://cloudblogs.microsoft.com/enterprisemobility/2017/09/19/fewer-login-prompts-the-new-keep-me-signed-in-experience-for-azure-ad-is-in-preview/)。

---

### <a name="add-configuration-to-require-the-terms-of-use-to-be-expanded-prior-to-accepting"></a>添加配置以要求在接受使用条款之前先将其展开。

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性

为管理员添加了一个选项，要求其用户在接受使用条款之前先将其展开。

对于要求用户展开使用条款这一选项，可选择“打开”或“关闭”。******** “打开”设置要求用户在接受使用条款之前先查看条款。****

有关详细信息，请参阅 [Azure AD 使用条款功能（预览版）](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="scoped-activation-for-eligible-role-assignments"></a>符合条件的角色分配的作用域激活

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

可以使用范围激活功能来激活符合条件的 Azure 资源角色分配，其自治性比原始分配的默认值小。 例如，你被分配为租户中某个订阅的所有者。 使用范围激活可以激活订阅中包含的最多五个资源（例如资源组和虚拟机）的“所有者”角色。 划分激活范围可能会降低对关键 Azure 资源执行不必要更改的可能性。

有关详细信息，请参阅[什么是 Azure AD Privileged Identity Management？](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure)。

---

### <a name="new-federated-apps-in-the-azure-ad-app-gallery"></a>Azure AD 应用库中提供了新的联合应用

**键入：** 新功能**服务类别：** 企业应用**产品功能：** 第三方集成

我们已于 2017 年 12 月将这些支持联合的新应用添加到了我们的应用库中：

[Accredible](https://go.microsoft.com/fwlink/?linkid=863523)、Adobe Experience Manager、[EFI Digital StoreFront](https://go.microsoft.com/fwlink/?linkid=861685)、[Communifire](https://go.microsoft.com/fwlink/?linkid=861676) CybSafe、[FactSet](https://go.microsoft.com/fwlink/?linkid=863525)、[IMAGE WORKS](https://go.microsoft.com/fwlink/?linkid=863517)、[MOBI](https://go.microsoft.com/fwlink/?linkid=863521)、[MobileIron Azure AD 集成](https://go.microsoft.com/fwlink/?linkid=858027)、[Reflektive](https://go.microsoft.com/fwlink/?linkid=863518)、[SAML SSO for Bamboo by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863520)、[SAML SSO for Bitbucket by resolution GmbH](https://go.microsoft.com/fwlink/?linkid=863519)、[Vodeclic](https://go.microsoft.com/fwlink/?linkid=863522)、WebHR、Zenegy Azure AD 集成。

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。

要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://docs.microsoft.com/azure/active-directory/develop/active-directory-app-gallery-listing)。

---

### <a name="approval-workflows-for-azure-ad-directory-roles"></a>Azure AD 目录角色的审批工作流

**键入：** 已更改的功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

Azure AD 目录角色的审批工作流程已正式发布。

通过审批工作流程，特权角色管理员可要求符合条件的角色成员先请求角色激活才能使用特权角色。 可向多个用户和组委派审批职责。 符合条件的角色成员会在审批完成且角色激活时收到通知。

---

### <a name="pass-through-authentication-skype-for-business-support"></a>传递身份验证 - Skype for Business 支持

**键入：** 已更改的功能**服务类别：** 身份验证（登录）**产品功能：** 用户身份验证

传递身份验证现在支持用户登录到支持新式身份验证的 Skype for Business 客户端应用程序，包括联机和混合拓扑。

有关详细信息，请参阅[支持新式身份验证的 Skype for Business 拓扑](https://technet.microsoft.com/library/mt803262.aspx)。

---

### <a name="updates-to-azure-ad-privileged-identity-management-for-azure-rbac-preview"></a>更新到 Azure RBAC 的 Azure AD Privileged Identity Management（预览版）

**键入：** 已更改的功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

通过 Azure 基于角色的访问控制 (RBAC) 的 Azure AD Privileged Identity Management (PIM) 公共预览版刷新，现在可以：

* 使用 Just Enough Administration。
* 需要批准才能激活资源角色。
* 安排将来激活需要批准 Azure AD 和 Azure RBAC 角色的角色。

有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management（预览版）](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

---

## <a name="november-2017"></a>2017 年 11 月

### <a name="access-control-service-retirement"></a>访问控制服务停用

**键入：** 规划更改**服务类别：** 访问控制服务**产品功能：** 访问控制服务

Azure Active Directory 访问控制（也称作访问控制服务）将在 2018 年底停用。 接下来的几周内会提供更多信息，包括详细的计划和高级迁移指南。 有关访问控制服务的任何问题，请在本页面留言，团队成员将予以解答。

---

### <a name="restrict-browser-access-to-the-intune-managed-browser"></a>限制对 Intune 托管浏览器的浏览器访问

**键入：** 规划更改**服务类别：** 条件访问**产品功能：** 标识安全和保护

可以使用 Intune 托管浏览器作为批准的应用，限制对 Office 365 以及其他已连接 Azure AD 的云应用的浏览器访问。

你现在可以为基于应用程序的条件访问配置以下条件：

**客户端应用：** 浏览器

**此更改会产生什么影响？**

目前，使用此条件时会阻止访问。 推出预览版后，所有访问都需要使用托管的浏览器应用程序。

可在即将发布的博客和发行说明中了解此功能和详细信息。

有关详细信息，请参阅[Azure AD 中的条件访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>基于 Azure AD 应用的条件访问的新批准的客户端应用

**键入：** 规划更改**服务类别：** 条件访问**产品功能：** 标识安全和保护

以下应用包含在[批准的客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)列表中：

- [Microsoft Kaizala](https://www.microsoft.com/garage/profiles/kaizala/)
- Microsoft StaffHub

有关详细信息，请参见:

- [批准的客户端应用要求](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="terms-of-use-support-for-multiple-languages"></a>使用条款支持多种语言

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性

管理员现在可以创建包含多个 PDF 文档的新使用条款。 可以使用相应语言来标记这些 PDF 文档。 将会根据用户的偏好，以匹配的语言显示 PDF。 如果没有匹配的语言，则以默认语言显示。

---

### <a name="real-time-password-writeback-client-status"></a>实时密码写回客户端状态

**键入：** 新功能**服务类别：** 自助服务密码重置**产品功能：** 用户身份验证

现在可以查看本地密码写回客户端的状态。 此选项位于[密码重置页的“本地集成”部分中****](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/PasswordReset)。

如果与本地写回客户端的连接出现问题，将会显示一条错误消息，其中包含：

- 有关无法连接到本地写回客户端的原因的信息。
- 可帮助解决此问题的文档链接。

有关详细信息，请参阅[本地集成](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-how-it-works#on-premises-integration)。

---

### <a name="azure-ad-app-based-conditional-access"></a>Azure AD 基于应用的条件性访问

**键入：** 新功能**服务类别：** Azure AD**产品功能：** 标识安全和保护

你现在可以使用[基于应用的条件访问 Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)，将对 Office 365 和其他 Azure AD 连接的云应用的访问限制到支持 Intune 应用保护策略的[批准的客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)。 Intune 应用保护策略用于配置和保护这些客户端应用程序中的公司数据。

通过结合基于[应用](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)和[基于设备](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-policy-connected-applications)的条件访问策略，你可以灵活地保护个人和公司设备的数据。

以下条件和控件现在可用于基于应用的条件访问：

**支持的平台条件**

- iOS
- Android

**客户端应用条件**

- 移动应用和桌面客户端

**访问控制**

- 需要已批准的客户端应用

有关详细信息，请参阅[Azure AD 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。

---

### <a name="manage-azure-ad-devices-in-the-azure-portal"></a>在 Azure 门户中管理 Azure AD 设备

**键入：** 新功能**服务类别：** 设备注册和管理**产品功能：** 标识安全和保护

现在可在一个位置找到连接到 Azure AD 的所有设备以及与设备相关的活动。 在 Azure 门户中管理所有设备标识和设置有新的管理体验。 在此版本中，可以：

- 在 Azure AD 中查看可用于条件性访问的所有设备。
- 查看属性，包括已加入混合 Azure AD 的设备。
- 查找已加入 Azure AD 的设备的 BitLocker 密钥、使用 Intune 管理设备，等等。
- 管理与 Azure AD 设备相关的设置。

有关详细信息，请参阅[使用 Azure 门户管理设备](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal)。

---

### <a name="support-for-macos-as-a-device-platform-for-azure-ad-conditional-access"></a>支持 macOS 作为 Azure AD 条件访问的设备平台

**键入：** 新功能**服务类别：** 条件访问**产品功能：** 标识安全和保护

你现在可以在 Azure AD 条件性访问策略中包括（或排除） macOS 作为设备平台条件。 通过将 macOS 添加到支持的设备平台，可以：

- **使用 Intune 登记和管理 macOS 设备。** 类似于 iOS 和 Android 等其他平台，macOS 可通过公司门户应用程序执行统一注册。 可以使用适用于 macOS 的新公司门户应用，通过 Intune 登记设备并将其注册到 Azure AD。
- **确保 macOS 设备符合组织在 Intune 中定义的符合性策略。** 可以在 Azure 门户上的 Intune 中设置 macOS 设备的符合性策略。
- **只允许符合条件的 macOS 设备访问 Azure AD 中的应用程序。** 条件性访问策略创作已 macOS 为单独的设备平台选项。 现在，你可以为 Azure 中的目标应用程序集创作特定于 macOS 的条件性访问策略。

有关详细信息，请参见:

- [使用 Intune 创建适用于 macOS 设备的设备符合性策略](https://aka.ms/macoscompliancepolicy)
- [Azure AD 中的条件性访问](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

---

### <a name="network-policy-server-extension-for-azure-multi-factor-authentication"></a>适用于 Azure 多重身份验证的网络策略服务器扩展

**键入：** 新功能**服务类别：** 多重身份验证**产品功能：** 用户身份验证

适用于 Azure 多重身份验证的网络策略服务器扩展使用现有的服务器在身份验证基础结构中添加基于云的多重身份验证功能。 使用网络策略服务器扩展，可将电话呼叫、短信或电话应用验证添加到现有的身份验证流。 无需安装、配置和维护新服务器。

此扩展是为想要保护虚拟专用网络连接，但不部署 Azure 多重身份验证服务器的组织创建的。 网络策略服务器扩展充当 RADIUS 与基于云的 Azure 多重身份验证之间的适配器，以为联合用户或已同步用户提供身份验证的第二个因素。

有关详细信息，请参阅[将现有网络策略服务器基础结构与 Azure 多重身份验证集成](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-nps-extension)。

---

### <a name="restore-or-permanently-remove-deleted-users"></a>还原或永久删除已删除的用户

**键入：** 新功能**服务类别：** 用户管理**产品功能：** 目录

在 Azure AD 管理中心，可以：

- 还原已删除的用户。
- 永久删除用户。

**试试看：**

1. 在 Azure AD 管理中心，选择 "**管理**" 部分中的 "[所有用户](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/UserManagementMenuBlade/All)"。

2. 从“显示”列表中，选择“最近删除的用户”********。

3. 选择一个或多个最近删除的用户，然后将其还原或永久删除。

---

### <a name="new-approved-client-apps-for-azure-ad-app-based-conditional-access"></a>基于 Azure AD 应用的条件访问的新批准的客户端应用

**键入：** 已更改的功能**服务类别：** 条件访问**产品功能：** 标识安全和保护

以下应用已添加到[批准的客户端应用](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)列表：

- Microsoft Planner
- Azure 信息保护

有关详细信息，请参见:

- [批准的客户端应用要求](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-conditions#client-apps-preview)
- [Azure AD 基于应用的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)

---

### <a name="use-or-between-controls-in-a-conditional-access-policy"></a>在条件访问策略中的控件之间使用 "OR"

**键入：** 已更改的功能**服务类别：** 条件访问**产品功能：** 标识安全和保护

你现在可以使用 "OR" （需要一个选定的控件）进行条件性访问控制。 可以使用此功能创建在访问控制条件之间包含“OR”的策略。 例如，可以使用此功能创建一个策略，要求用户使用多重身份验证登录，或要求用户在符合条件的设备上操作。

有关详细信息，请参阅[Azure AD 条件访问中的控件](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-controls)。

---

### <a name="aggregation-of-real-time-risk-detections"></a>实时风险检测的聚合

**键入：** 已更改的功能**服务类别：** 标识保护**产品功能：** 标识安全和保护

在 Azure AD Identity Protection 中，所有源自给定日期的相同 IP 地址的实时风险检测现在都将针对每种风险检测类型进行聚合。 此更改限制显示的风险检测量，而不会对用户安全进行任何更改。

每当用户登录时，基础实时检测都会运行。 如果针对多重身份验证或阻止访问设置了登录风险安全策略，则每次存在风险的登录期间都会触发该设置。

---

## <a name="october-2017"></a>2017 年 10 月

### <a name="deprecate-azure-ad-reports"></a>弃用 Azure AD 报告

**键入：** 规划更改**服务类别：** 报表**产品功能：** 标识生命周期管理

Azure 门户提供：

- 新的 Azure AD 管理控制台。
- 用于活动和安全报告的新 API。

由于推出了这些新功能，/reports 终结点下的报告 API 将在 2017 年 12 月 10 日停用。

---

### <a name="automatic-sign-in-field-detection"></a>自动登录字段检测

**键入：** 已修复的**服务类别：** 我的应用**产品功能：** 单一登录

对于显示 HTML 用户名和密码字段的应用程序，Azure AD 支持自动登录字段检测。 [如何自动捕获应用程序的登录字段](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications-problems#manually-capture-sign-in-fields-for-an-app)中介绍了这些步骤。 在 [Azure 门户](https://aad.portal.azure.com)中的“企业应用程序”页面上添加一个“非库”应用程序，即可找到此功能。****** 此外，可在此新应用程序中将“单一登录”模式配置为“基于密码的单一登录”，输入 Web URL，然后保存页面。********

由于某个服务问题，此功能曾经暂时禁用过。 该问题现已得到解决，自动登录字段检测功能再次可用。

---

### <a name="new-multi-factor-authentication-features"></a>新的多重身份验证功能

**键入：** 新功能**服务类别：** 多重身份验证**产品功能：** 标识安全和保护

多重身份验证 (MFA) 是保护组织不可或缺的组成部分。 为使凭证的适应能力更强，体验更顺畅，添加了以下功能：

- 将多重身份验证质询结果直接集成到 Azure AD 登录报告，包括以编程方式访问 MFA 结果。
- 将 MFA 配置更深入地集成到 Azure 门户中的 Azure AD 配置体验。

在此公共预览版中，MFA 管理和报告集成到核心 Azure AD 配置体验中。 现在，可以在 Azure AD 体验中管理 MFA 管理门户功能。

有关详细信息，请参阅 [Azure 门户中的 MFA 报告参考](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins-mfa)。

---

### <a name="terms-of-use"></a>使用条款

**键入：** 新功能**服务类别：** 使用条款**产品功能：** 合规性

可以使用 Azure AD 的使用条款功能向用户显示法律要求或符合性要求的相关免责声明。

可在以下情况下使用 Azure AD 使用条款：

- 针对组织中所有用户的常规使用条款
- 基于用户属性（例如，动态组中的医生与护士或国内员工和国际员工）的具体使用条款
- 有关访问业务影响性较高的应用（例如 Salesforce）的具体使用条款

有关详细信息，请参阅 [Azure AD 使用条款](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use)。

---

### <a name="enhancements-to-privileged-identity-management"></a>Privileged Identity Management 的增强

**键入：** 新功能**服务类别：** Privileged Identity Management**产品功能：** Privileged Identity Management

使用 Azure AD Privileged Identity Management，可以管理、控制和监视对组织中 Azure 资源（预览版）的访问：

- 订阅
- 资源组
- 虚拟机

Azure 门户中使用 Azure RBAC 功能的所有资源都可以利用 Azure AD Privileged Identity Management 提供的所有安全和生命周期管理功能。

有关详细信息，请参阅 [Azure 资源的 Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

---

### <a name="access-reviews"></a>访问评审

**键入：** 新功能**服务类别：** 访问评审**产品功能：** 合规性

组织可以使用访问评审（预览版）有效管理组成员身份以及对企业应用程序的访问权限：

- 可以使用访问评审鉴定来宾用户对应用程序的访问权限及其组成员身份。 评审者可以根据访问评审提供的见解，有效决定是否允许来宾继续访问。
- 可以使用访问评审再次验证员工对应用程序和组成员身份的访问权限。

可以收集与组织相关的程序中的访问评审控件，跟踪符合性或风险敏感应用程序的审阅。

有关详细信息，请参阅 [Azure AD 访问评审](https://docs.microsoft.com/azure/active-directory/active-directory-azure-ad-controls-access-reviews-overview)。

---

### <a name="hide-third-party-applications-from-my-apps-and-the-office-365-app-launcher"></a>在“我的应用”和 Office 365 应用启动器中隐藏第三方应用程序

**键入：** 新功能**服务类别：** 我的应用**产品功能：** 单一登录

现在，可以通过“隐藏应用”属性更好地管理用户门户中显示的应用。**** 如果为后端服务显示的应用磁贴或重复的磁贴导致用户的应用启动器变得混杂，隐藏应用可帮助解决问题。 切换开关位于第三方应用的“属性”部分中，带有“对用户可见?”标签******** 还可以通过 PowerShell 以编程方式隐藏应用。

有关详细信息，请参阅[在 Azure AD 的用户体验中隐藏第三方应用程序](https://docs.microsoft.com/azure/active-directory/active-directory-coreapps-hide-third-party-app)。


**可用功能**

 在过渡到新管理控制台的过程中，有两个新 API 可用于检索 Azure AD 活动日志。 新的 API 集提供更丰富的筛选和排序功能，此外还提供更丰富的审核和登录活动。 现在，可以通过 Microsoft Graph 中的 Identity Protection 风险检测 API 来访问以前通过安全报告提供的数据。


## <a name="september-2017"></a>2017 年 9 月

### <a name="hotfix-for-identity-manager"></a>适用于 Identity Manager 的修补程序

**键入：** 已更改的功能**服务类别：** 标识管理器**产品功能：** 标识生命周期管理

现已推出 Identity Manager 2016 Service Pack 1 截止 2017 年 9 月 25 日的修补程序汇总包（内部版本 4.4.1642.0）。 此汇总包：

- 解决了一些问题并添加了改进项。
- 是一项累积更新，可取代 Identity Manager 2016 内部版本 4.4.1459.0 及其之前的所有 Identity Manager 2016 Service Pack 1 更新。
- 要求安装 Identity Manager 2016 内部版本 4.4.1302.0。

有关详细信息，请参阅[为 Identity Manager 2016 Service Pack 1 推出了修补程序汇总包（内部版本 4.4.1642.0）](https://support.microsoft.com/help/4021562)。

---
