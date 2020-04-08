---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: msmimart
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 491908b651cd2b875fcfeed4c55d34f0e8c5cfa1
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80802503"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制和粘贴此 URL：`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`到![RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png)源阅读器读取器，获得有关何时重新访问此页面以进行更新的通知。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>2021 年 3 月 B2B 更新中的未托管 Azure 活动目录帐户

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
**从 2021 年 3 月 31 日开始**，Microsoft 将不再支持为 B2B 协作方案创建非托管 Azure 活动目录 （Azure AD） 帐户和租户来兑换邀请。 为此，我们鼓励您选择发送电子邮件[一次性密码身份验证](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有默认访问角色的用户将处于预配范围

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 身份生命周期管理
 
从历史上看，具有默认访问角色的用户已没有预配范围。 我们听到反馈，客户希望具有此角色的用户在预配范围内。 我们正在努力部署更改，以便所有新的预配配置都允许预配具有默认访问角色的用户。 逐渐地，我们将更改现有预配配置的行为，以支持使用此角色的预配用户。 无需客户操作。 此更改完成后，我们会向[文档](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)发布更新。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 协作将在 Microsoft Azure 中提供，该协作由 21Vianet（Azure China 21Vianet） 租户运营

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能将在 Microsoft Azure 中提供，该功能将由 21Vianet（Azure China 21Vianet） 租户运营，使 Azure 中国 21Vianet 租户中的用户可以与其他 Azure 中国 21Vianet 租户中的用户无缝协作。 [了解有关 Azure AD B2B 协作的更多](https://docs.microsoft.com/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 协作邀请电子邮件重新设计

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure [emails](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements) AD B2B 协作邀请服务发送的电子邮件将重新设计，以使邀请信息和用户的后续步骤更加清晰。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>HomeRealm 发现策略更改将显示在审核日志中

**类型：** 已修复  
**服务类别：** 审计  
**产品功能：** 监视和报告
 
我们修复了一个错误，其中对[HomeRealm 发现策略](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)的更改未包含在审核日志中。 现在，您将能够查看策略更改的时和方式以及由谁更改。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 应用库中提供的新联合应用 - 2020 年 3 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 3 月，我们在应用库中添加了这 51 个具有联合支持的新应用： 

[思科 AnyConnect，](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect) [Zoho 一中国](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial)， [Plus Plus，](https://test.plusplus.app/auth/login/azuread-outlook/) [Profit.co SAML 应用程序](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial)， [iPoint 服务提供商](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)， contexxt.ai [SPHERE，](https://contexxt-sphere.com/login)[智慧通过 Invictus，](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)[火焰数字标牌](https://spark-dev.pixelnebula.com/login)， [Logz.io - 工程师云可观测性](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial)，[频谱U，](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial) [BizzContact，](https://bizzcontact.app/)[埃尔卡诺 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial)，[市场标志共享](http://www.signshare.com/)，[交叉知识学习套件](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial)， [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)， [FCM HUB，](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial) [RIB A/S Byggeweb移动](https://apps.apple.com/us/app/docia/id529058757)， [GoLinks，](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial)[数据狗](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial)， [Zscaler B2B用户门户](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial)， [LIFT，](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial)[平面视企业一](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial)，[观看团队](https://www.devfinition.com/)， [A 斯特，](https://demo.asterapp.io/login)[技能工作流程](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial)，[节点洞察](https://admin.nodeinsight.com/AADLogin.aspx)， [IP 平台](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)， [InVision](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)，[管道驱动](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)，[展示工作坊](https://app.showcaseworkshop.com/)，[绿灯集成平台](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)，[绿灯合规访问管理](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)，[格罗克学习](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial)， [Miradore在线](https://login.online.miradore.com/)，[霍罗斯护理](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial)，[询问你的Team，](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)[特鲁吉特](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)，[智能豁免](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)，[比扎吉 数字过程自动化工作室](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)， [insuiteX，](https://www.insuite.jp/) [sybo，](https://www.systexsoftware.com.tw/) [Britive，](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial) [WhosOffice，](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)[电子天](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial)，[科洛蒂维奥 SDN，](https://portal.kollective.app/login) [Witivio，](https://app.witivio.com/) [Playvox，](https://my.playvox.com/login) [Korn Ferry 360，](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial)[校园咖啡厅](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)，[渔获点](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial)，[代码42](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure AD B2B 协作在 Azure 政府租户中可用

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能现在在某些 Azure 政府租户之间可用。  要了解租户是否能够使用这些功能，请按照以下说明操作：[如何判断 Azure 美国政府租户中 B2B 协作是否可用？。](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure 日志的 Azure 监视器集成现在在 Azure 政府中可用

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
Azure 监视器与 Azure AD 日志的集成现在在 Azure 政府中可用。 您可以将 Azure AD 日志（审核和登录日志）路由到存储帐户、事件中心和日志分析。 请查看用于[报告和监视](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 方案[的详细文档](https://aka.ms/aadlogsinamd)以及部署计划。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 政府中的标识保护刷新

**类型：** 新功能  
**服务类别：** 身份保护  
**产品功能：** 标识安全和保护

我们很高兴地分享，我们现在在[Microsoft Azure 政府门户](https://portal.azure.us/)中推出了刷新的[Azure AD 标识保护](https://aka.ms/IdentityProtectionDocs) 体验。 有关详细信息，请参阅我们的[公告博客文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>灾难恢复：下载并存储预配配置

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 身份生命周期管理
 
Azure AD 预配服务提供了一组丰富的配置功能。 客户需要能够保存其配置，以便以后可以参考它或回滚到已知良好的版本。 我们添加了将预配配置下载为 JSON 文件并在您需要时上载的能力。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR（自助服务密码重置）现在需要两个门，用于 Microsoft Azure 中的管理员，由 21Vianet （Azure China 21Vianet） 操作 

**类型：** 已更改的功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 标识安全和保护
 
以前在 Microsoft Azure 中由 21Vianet （Azure China 21Vianet） 操作，使用自助服务密码重置 （SSPR） 重置自己的密码的管理员只需要一个"门"（质询）即可证明其身份。 在公共云和其他国家云中，管理员在使用 SSPR 时通常必须使用两个门来证明其身份。 但由于我们不支持 Azure 中国 21Vianet 中的短信或电话，因此允许管理员重置单门密码。

我们正在创建 Azure 中国 21Vianet 和公共云之间的 SSPR 功能奇偶校验。 今后，管理员在使用 SSPR 时必须使用两个门。 将支持短信、电话呼叫和身份验证器应用通知和代码。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密码长度限制为 256 个字符

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
为了确保 Azure AD 服务的可靠性，用户密码的长度现在限制为 256 个字符。 密码长度超过此期限的用户将在后续登录时通过联系其管理员或使用自助服务密码重置功能更改其密码。

此更改于 2020 年 3 月 13 日上午 10 点（18：00 UTC）启用，错误为 AADSTS 50052，无效密码超过最大值长度。 有关详细信息，请参阅[中断更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD 登录日志现在通过 Azure 门户可供所有免费租户使用

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告
 
从现在开始，具有免费租户的客户可以从 Azure 门户访问[Azure AD 登录日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)长达 7 天。 以前，登录日志仅适用于具有 Azure 活动目录高级许可证的客户。 通过此更改，所有租户都可以通过门户访问这些日志。

> [!NOTE]
> 客户仍然需要高级许可证（Azure 活动目录高级 P1 或 P2）才能通过 Microsoft 图形 API 和 Azure 监视器访问登录日志。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>从 Azure 门户上的组常规设置弃用目录范围组选项

**类型：** 已弃用  
**服务类别：** 组管理  
**产品功能：** 协作

为了为客户提供更灵活的方法来创建最能满足其需求的目录范围组，我们已将 Azure 门户中的 **"组** > **常规**"设置中的 **"目录范围组"** 选项替换为指向[动态组文档](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的链接。 我们改进了文档以包含更多说明，以便管理员可以创建包含或排除来宾用户的所有用户组。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>即将对自定义控件的更改

**类型：** 更改计划  
**服务类别：** Mfa  
**产品功能：** 标识安全和保护
 
我们计划用允许合作伙伴提供的身份验证功能与 Azure Active Directory 管理员和最终用户体验无缝协作的方法替换当前自定义控件预览。 如今，合作伙伴 MFA 解决方案面临以下限制：它们仅在输入密码后才工作;它们不作为 MFA 在其他关键方案中进行后续身份验证;并且它们不与最终用户或管理凭据管理功能集成。 新的实现将允许合作伙伴提供的身份验证因子与关键方案（包括注册、使用、MFA 声明、加强身份验证、报告和日志记录）的内置因素一起工作。 

自定义控件将继续在预览版中与新设计一起支持，直到其达到通用性。 届时，我们将让客户有时间迁移到新设计。 由于当前方法的局限性，在新设计可用之前，我们不会加入新的提供程序。 我们正在与客户和供应商密切合作，并将随着我们越来越近而传达时间表。 [了解详细信息](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>身份安全分数 - MFA 改进操作更新

**类型：** 更改计划  
**服务类别：** Mfa  
**产品功能：** 标识安全和保护
 
为了反映企业在应用适用于其业务的策略时确保最高安全性的需求，Microsoft Secure Score 删除了三个以多重身份验证 （MFA） 为中心的改进操作，并添加了两个改进操作。

将删除以下改进操作：

- 注册 MFA 的所有用户
- 要求所有用户执行 MFA
- 需要 MFA 才能获得 Azure AD 特权角色

将添加以下改进操作：

- 确保所有用户都能完成 MFA 进行安全访问
- 需要 MFA 才能担任管理角色

这些新的改进操作将需要在目录中注册您的用户或管理员 MFA，并建立适合组织需求的正确策略集。 主要目标是具有灵活性，同时确保所有用户和管理员都能使用多种因素或基于风险的身份验证提示进行身份验证。 这可以采取设置安全默认值的形式，让 Microsoft 决定何时向用户挑战 MFA，或者有多个策略应用范围决策。 作为这些改进操作更新的一部分，基线保护策略将不再包含在评分计算中。 [阅读更多关于微软安全得分即将推出的内容](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。

---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD 域服务 SKU 选择

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
我们听到反馈说，Azure AD 域服务客户希望在为其实例选择性能级别时更具灵活性。 从 2020 年 2 月 1 日开始，我们从动态模型（Azure AD 根据对象计数确定性能和定价层）切换到自选模型。 现在，客户可以选择与其环境相匹配的性能层。 此更改还允许我们启用资源林等新方案以及高级功能（如每日备份）。 现在，所有 SKU 的对象计数都是无限的，但我们将继续为每个层提供对象计数建议。

**无需立即采取客户操作。** 对于现有客户，2020 年 2 月 1 日使用的动态层决定了新的默认层。 此更改不会对定价或性能产生影响。 今后，Azure AD DS 客户将需要评估性能要求，因为他们的目录大小和工作负载特征发生变化。 服务层之间的切换将继续是一个无停机时间操作，并且我们将不再根据客户目录的增长自动将客户移动到新的层。 此外，不会涨价，新的定价将与我们目前的计费模式一致。 有关详细信息，请参阅 Azure [AD DS SKU 文档](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD 域服务定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 应用库中提供的新联合应用 - 2020 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 2 月，我们在应用库中添加了这 31 个具有联合支持的新应用： 

[IamIP专利平台](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[体验云](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)， [NS1 SSO为 Azure，](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)[梭子鱼电子邮件安全服务](https://ess.barracudanetworks.com/sso/azure)， [ABa 报告](https://myaba.co.uk/client-access/signin/auth/msad)，[在危机的情况下 - 在线门户](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)， [BIC云设计](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)，[养蜂人 Azure AD 数据连接器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial) [Korn， 渡轮评估](https://www.kornferry.com/solutions/kf-digital/kf-assess)， [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)，[飞溅顶](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)， [Syxsense，](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial) [EAB 导航](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)，[新遗物 （ 限量发行）](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [， Thulium](https://admin.thulium.com/login/instance)，[票务经理](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)，[模板选择团队](https://links.officeatwork.com/templatechooser-download-teams)，[蜜蜂](https://www.beesy.me/index.php/site/login)，[健康支持系统](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)， [MURAL，](https://app.mural.co/signup) [Hive，](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [LavaDo，](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)[威克特，](https://wakelet.com/login) [Firmex VDR，](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)[教师和学校，](https://www.thinglink.com/)[科达](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)，[近波达，](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial)，[邀请人](https://invitepeople.com/login)，[重印台 - 文章](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)， 银河[TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，您可以自动创建、更新和删除这些新集成应用的用户帐户：

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

有关如何使用自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动向 SaaS 应用程序预配用户](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>在混合环境中支持 FIDO2 安全密钥

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
我们宣布在混合环境中对 FIDO2 安全密钥的 Azure AD 支持的公共预览。 用户现在可以使用 FIDO2 安全密钥登录到其混合 Azure AD 加入的 Windows 10 设备，并无缝登录到其本地和云资源。 自从我们最初在 Azure AD 加入的设备上启动 FIDO2 支持的公共预览以来，对混合环境的支持一直是我们无密码客户请求最多的功能。 使用生物识别和公钥/私钥加密等先进技术的无密码身份验证在安全保护的同时提供了方便和易用性。 使用此公共预览版，您现在可以使用 FIDO2 安全密钥等现代身份验证来访问传统的 Active Directory 资源。 有关详细信息，请访问[SSO 到本地资源](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)。 

要开始使用，请访问[为租户启用 FIDO2 安全密钥](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)，以获得分步说明。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>新的"我的帐户"体验现已普遍提供

**类型：** 已更改的功能  
**服务类别：** 我的个人资料/帐户  
**产品功能：** 最终用户体验
 
我的帐户，所有终端用户帐户管理需求的一站式商店，现在普遍可用！ 最终用户可以通过 URL 或新的"我的应用"体验的标头访问此新网站。 详细了解新体验[在"我的帐户门户概述"](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中提供的所有自助服务功能。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>我的帐户网站 URL 更新到myaccount.microsoft.com

**类型：** 已更改的功能  
**服务类别：** 我的个人资料/帐户  
**产品功能：** 最终用户体验
 
新的"我的帐户"最终用户体验将在下个月将其 URL`https://myaccount.microsoft.com`更新到。 在["我的帐户"门户帮助](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中，查找有关体验及其向最终用户提供的所有帐户自助服务功能的详细信息。

---
 
## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新的"我的应用"门户现在通常可用

**类型：** 更改计划  
**服务类别：** 我的应用  
**产品功能：** 最终用户体验
 
将您的组织升级到新的"我的应用"门户，该门户现在通常可用！ 在["我的应用"门户上创建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)上查找有关新门户和集合的详细信息。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 中的工作区已重命名为集合

**类型：** 已更改的功能  
**服务类别：** 我的应用   
**产品功能：** 最终用户体验
 
工作区（筛选器管理员可以配置为组织其用户应用）现在称为集合。 在["我的应用"门户上创建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)，有关如何配置它们的详细信息。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自定义策略（公共预览）Azure AD B2C 电话注册和登录

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
通过电话号码注册和登录，开发人员和企业可以允许其客户使用通过 SMS 发送到用户电话号码的一次性密码进行注册和登录。 此功能还允许客户在无法访问其手机时更改其电话号码。 借助自定义策略的强大功能，电话注册和登录允许开发人员和企业通过页面自定义来传达其品牌。 了解如何[在 Azure AD B2C 中使用自定义策略设置电话注册和登录](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 应用程序库中的新预配连接器 - 2020 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，您可以自动创建、更新和删除这些新集成应用的用户帐户：

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

有关如何使用自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动向 SaaS 应用程序预配用户](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 应用库中提供的新联合应用 - 2020 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020 年 1 月，我们在应用库中添加了这 33 个具有联合支持的新应用： 

[JOSA，](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)[快速边缘云](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)， [Terraform 企业](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)， [Spintr SSO，](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)[阿比博特网络科菲克](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)， [SkyKick，](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)[上攻](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)，[离开机器人](https://leavebot.io/#home)，[数据营](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)，[旅行行动](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)，[AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)[智能工作](https://www.intumit.com/english/SmartWork.html)，[EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)[网络监控](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)， [Akari Virtual Assistant](https://akari.io/akari-virtual-assistant/) [SSOGEN - Azure AD SSO 网关甲骨文电子商务套件 - EBS， PeopleSoft， 和 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)， 托管[MyCirqa SSO，](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)[玉湖物业管理平台](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)， [LumApps，](https://sites.lumapps.com/login)[上工企业](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)，[人才软](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)， [SmartDB为微软团队](http://teams.smartdb.jp/login/)，[新闻页面](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)，[合同安全 Saml2 SSO，](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial) [Maxient 行为管理器软件](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)，[帮助转移](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)， [PortalTalk 365，](https://www.portaltalk.com/) [CoreView，](https://portal.coreview.com/) [Squelch 云 Office365 连接器](https://laxmi.squelch.io/login)， [PingFlow 认证](https://app-staging.pingview.io/)，[打印机逻辑 SaaS，](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial) [Sandas](https://app.sandwai.com/) [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="two-new-identity-protection-detections"></a>两个新的身份保护检测

**类型：** 新功能  
**服务类别：** 身份保护  
**产品功能：** 标识安全和保护
 
我们添加了两种新的登录链接检测类型到身份保护：可疑的收件箱操作规则和不可能的旅行。 这些脱机检测由 Microsoft 云应用安全 （MCAS） 发现，并影响身份保护中的用户和登录风险。 有关这些检测的详细信息，请参阅我们的[登录风险类型](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>中断更改：URI 片段不会通过登录重定向进行

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
从 2020 年 2 月 8 日开始，当请求发送到login.microsoftonline.com以登录用户时，服务将附加一个空片段到请求中。  这通过确保浏览器擦除请求中的任何现有片段来防止一类重定向攻击。 任何应用程序都不应依赖于此行为。 有关详细信息，请参阅在 Microsoft 标识平台文档中[分解更改](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>将 SAP 成功因子预配到 Azure AD 和本地 AD（公共预览）

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 身份生命周期管理

现在，您可以在 Azure AD 中集成 SAP 成功因子作为权威标识源。 此集成可帮助您自动执行端到端标识生命周期，包括使用基于 HR 的事件（如新员工或终止）来控制 Azure AD 帐户的预配。

有关如何设置 SAP 成功因子到 Azure AD 的入站预配的详细信息，请参阅[配置 SAP 成功因子自动预配](https://aka.ms/SAPSuccessFactorsInboundTutorial)教程。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>支持 Azure AD B2C 中的自定义电子邮件（公共预览版）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

现在，当用户注册使用你的应用时，可以使用 Azure AD B2C 创建自定义电子邮件。 通过使用 DisplayControls（当前预览版）和第三方电子邮件提供商（如[SendGrid、SparkPost](https://sendgrid.com/)[SparkPost](https://sparkpost.com/)或自定义 REST API），您可以使用自己的电子邮件模板 **"From**地址"和主题文本，以及支持本地化和自定义一次性密码 （OTP） 设置。

有关详细信息，请参阅[Azure 活动目录 B2C 中的自定义电子邮件验证](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>将基准策略更换为安全默认值

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 标识安全和保护

作为身份验证安全默认模型的一部分，我们将从所有租户中删除现有的基线保护策略。 此次删除目标是在 2 月底完成。 这些基线保护策略的替代是安全默认值。 如果您一直在使用基线保护策略，则必须计划迁移到新的安全默认策略或条件访问。 如果您尚未使用这些策略，则无需执行任何操作。

有关新安全默认值的详细信息，请参阅[什么是安全默认值？](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 有关条件访问策略的详细信息，请参阅[通用条件访问策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>支持同一网站属性和 Chrome 80

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

作为 Cookie 安全默认模型的一部分，Chrome 80 浏览器正在改变在没有`SameSite`该属性的情况下处理 Cookie 的方式。 任何未指定属性的`SameSite`Cookie 将被视为 设置为`SameSite=Lax`，这将导致 Chrome 阻止应用可能依赖于的某些跨域 Cookie 共享方案。 要维护较旧的 Chrome 行为，可以使用该`SameSite=None`属性并添加其他`Secure`属性，因此只能通过 HTTPS 连接访问跨站点 Cookie。 Chrome 计划于 2020 年 2 月 4 日前完成此更改。

我们建议所有开发人员使用本指南测试其应用：

- 将 **"使用安全 Cookie"** 设置的默认值设置为 **"是**"。

- 将**SameSite**属性的默认值设置为 **"无**"。

- 添加安全的附加`SameSite`属性**Secure**。

有关详细信息，请参阅[即将推出的 ASP.NET 中的同一网站 Cookie 更改，并在](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/) [Chrome 版本 79 及更高版本中ASP.NET核心和可能中断客户网站和 Microsoft 产品和服务](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>微软身份管理器 （MIM） 2016 服务包 2 （SP2） 的新修补程序

**类型：** 已修复  
**服务类别：** 微软身份管理器  
**产品功能：** 身份生命周期管理

微软标识管理器 （MIM） 2016 服务包 2 （SP2） 提供修补程序汇总包（内部版本 4.6.34.0）。 此汇总包可解决问题并添加"此更新中添加的问题和改进"部分中描述的改进。

有关详细信息并下载修补程序包，请参阅[Microsoft 标识管理器 2016 服务包 2 （内部 4.6.34.0） 更新汇总可用](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>新的 AD FS 应用活动报告可帮助将应用迁移到 Azure AD（公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** Sso

在 Azure 门户中使用新的活动目录联合服务 （AD FS） 应用活动报告，以确定哪些应用能够迁移到 Azure AD。 该报告评估所有 AD FS 应用与 Azure AD 的兼容性，检查任何问题，并提供有关准备各个应用进行迁移的指导。

有关详细信息，请参阅使用[AD FS 应用程序活动报告将应用程序迁移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>用户请求管理员同意的新工作流（公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 访问控制

新的管理员同意工作流为管理员提供了一种授予对需要管理员批准的应用的访问权限的方法。 如果用户尝试访问应用，但无法提供同意，他们现在可以发送管理员批准请求。 请求通过电子邮件发送，并放置在可从 Azure 门户访问的队列中发送给被指定为审阅者的所有管理员。 审阅者对挂起的请求执行操作后，将通知请求用户该操作。

有关详细信息，请参阅[配置管理员同意工作流（预览）。](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>用于管理可选声明的新 Azure AD 应用注册令牌配置体验（公共预览）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验

Azure 门户上的新**Azure AD 应用注册令牌配置**边栏选项卡现在向应用开发人员显示其应用的可选声明的动态列表。 这种新体验有助于简化 Azure AD 应用迁移，并最大限度地减少可选声明错误配置。

有关详细信息，请参阅向[Azure AD 应用提供可选声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 授权管理中新的两阶段审批工作流（公共预览版）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

我们引入了一个新的两阶段审批工作流，允许您要求两个审批人批准用户对访问包的请求。 例如，您可以设置它，以便请求用户的经理必须首先批准，然后也可以要求资源所有者批准。 如果其中一个审批人未批准，则不会授予访问权限。

有关详细信息，请参阅[Azure AD 授权管理中访问包的更改请求和审批设置](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>"我的应用"页面以及新工作区的更新（公共预览）

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 第三方集成

您现在可以自定义组织用户查看和访问刷新的"我的应用"体验的方式。 这种新体验还包括新的工作区功能，使用户能够更轻松地查找和组织应用。

有关新的"我的应用"体验和创建工作区的详细信息，请参阅[在"我的应用"门户上创建工作区](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Google 对 Azure AD B2B 协作的社交 ID 支持（通用发布）

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** 用户身份验证

在 Azure AD 中使用 Google 社交 ID（Gmail 帐户）的新支持有助于简化用户和合作伙伴的协作。 您的合作伙伴不再需要创建和管理新的特定于 Microsoft 的帐户。 Microsoft Teams 现在完全支持所有客户端上的 Google 用户以及公共和租户相关的身份验证终结点。

有关详细信息，请参阅添加[Google 作为 B2B 来宾用户的身份提供商](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>微软边缘移动支持条件访问和单一登录（通用版）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

iOS 和 Android 上的 Microsoft Edge 的 Azure AD 现在支持 Azure AD 单一登录和条件访问：

- **微软边缘单一登录 （SSO）：** 对于所有 Azure AD 连接的应用，现在本机客户端（如 Microsoft Outlook 和 Microsoft Edge）都可以使用单一登录。

- **微软边缘条件访问：** 通过基于应用程序的条件访问策略，您的用户必须使用受 Microsoft Intune 保护的浏览器，如 Microsoft Edge。

有关条件访问和 Microsoft 边缘 SSO 的详细信息，请参阅[Microsoft 边缘移动支持条件访问和单一登录现在一般可用的](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)博客文章。 有关如何使用[基于应用的条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)或[基于设备的条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)设置客户端应用的详细信息，请参阅[使用 Microsoft Intune 策略保护浏览器管理 Web 访问](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 授权管理（常规可用性）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

Azure AD 授权管理是一项新的标识治理功能，可帮助组织大规模管理标识和访问生命周期。 此新功能通过自动跨组、应用和 SharePoint Online 网站实现访问请求工作流、访问分配、审核和过期而有所帮助。

借助 Azure AD 授权管理，您可以更有效地管理员工以及组织外部需要访问这些资源的用户的访问。

有关详细信息，请参阅什么是[Azure AD 授权管理？](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>为这些新支持的 SaaS 应用自动配置用户帐户

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成  

现在，您可以自动创建、更新和删除这些新集成应用的用户帐户：

[SAP云平台身份认证服务](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)，[环中心](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial)，[空间IQ，](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial)[米罗](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial)，[云门](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial)， [Infor云套件](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial)，[办公空间软件](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial)，[优先级矩阵](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

有关如何使用自动用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动向 SaaS 应用程序预配用户](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 应用库中提供的新联合应用 - 2019 年 11 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019 年 11 月，我们在应用库中添加了这 21 个具有联合支持的新应用：

[空桌](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)，[胡特套房](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)，[蓝色访问会员 （BAM）](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)，[位利](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)，[里瓦](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)， [ResLife门户](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)， [NegometrixPortal 单点 （SSO），](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial) [TeamsChamp，](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279) [Motus，](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [MyAryaka，](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial) [BlueMail，](https://loginself1.bluemail.me/) [Beedle，](https://teams-web.beedle.co/#/)[维斯玛](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)， [OneDesk，](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [Foko 零售](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)， [Qmarkets idea&创新管理](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial)，[网科用户身份验证](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)， [UniFLOW在线](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)[，](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial) [Claromentis](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial) [e4enable](https://portal.e4enable.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>新建和改进的 Azure AD 应用程序库

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** Sso

我们更新了 Azure AD 应用程序库，以便更轻松地在 Azure 活动目录租户上查找支持预配、OpenID 连接和 SAML 的预集成应用。

有关详细信息，请参阅[将应用程序添加到 Azure 活动目录租户](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>将应用角色定义长度限制从 120 个字符增加到 240 个字符

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** Sso

我们从客户那里听说，某些应用和服务中应用角色定义值的长度限制太短，为 120 个字符。 作为回应，我们将角色值定义的最大长度增加到 240 个字符。

有关使用特定于应用程序的角色定义的详细信息，请参阅[在应用程序中添加应用角色，并在令牌中接收它们](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>弃用用于 Azure AD 标识保护风险检测的标识RiskEvent API  

**类型：** 更改计划  
**服务类别：** 身份保护  
**产品功能：** 标识安全和保护

为了响应开发人员的反馈，Azure AD 高级 P2 订阅者现在可以使用 Microsoft 图形的新风险检测 API 对 Azure AD 标识保护的风险检测数据执行复杂的查询。 现有的[标识RiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API测试版将在**2020年1月10**日左右停止返回数据。 如果您的组织正在使用标识RiskEvent API，则应过渡到新的风险检测 API。

有关新风险检测 API 的详细信息，请参阅[风险检测 API 参考文档](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>对同一网站属性和 Chrome 80 的应用程序代理支持

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

在 Chrome 80 浏览器发布前几周，我们计划更新应用程序代理 Cookie 如何处理**SameSite**属性。 随着 Chrome 80 的发布，任何未指定**SameSite**属性的 Cookie 将被视为已设置为`SameSite=Lax`。

为了帮助避免此更改的潜在负面影响，我们正在通过以下功能更新应用程序代理访问和会话 Cookie：

- 将 **"使用安全 Cookie"** 设置的默认值设置为 **"是**"。

- 将**SameSite**属性的默认值设置为 **"无**"。

    >[!NOTE]
    > 应用程序代理访问 Cookie 始终通过安全通道独家传输。 这些更改仅适用于会话 Cookie。

有关应用程序代理 Cookie 设置的详细信息，请参阅[用于访问 Azure 活动目录中的本地应用程序的 Cookie 设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>应用程序注册（旧版）和应用程序从应用程序注册门户（apps.dev.microsoft.com）的融合应用管理将不再可用

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 开发人员体验

在不久的将来，具有 Azure AD 帐户的用户将不再能够使用应用程序注册门户 （apps.dev.microsoft.com）注册和管理融合的应用程序，或在 Azure 门户中的应用注册（旧版）体验中注册和管理应用程序。

要了解有关新的应用注册体验，请参阅[Azure 门户培训指南中的应用注册](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>从每个用户 MFA 迁移到基于条件访问的 MFA 期间，用户不再需要重新注册

**类型：** 已修复  
**服务类别：** Mfa  
**产品功能：** 标识安全和保护

我们已经修复了一个已知问题，即当用户需要重新注册是否为每用户多因素身份验证 （MFA） 禁用，然后通过条件访问策略启用 MFA 时。

要要求用户重新注册，可以从 Azure AD 门户中的用户的身份验证方法中选择 **"必需重新注册 MFA"** 选项。 有关将用户从每个用户 MFA 迁移到基于条件访问的 MFA 的详细信息，请参阅[将用户从每个用户 MFA 转换为基于条件访问的 MFA。](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>在 SAML 令牌中转换和发送声明的新功能

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** Sso

我们添加了其他功能，可帮助您在 SAML 令牌中自定义和发送声明。 这些新功能包括：

- 其他声明转换函数，可帮助您修改在索赔中发送的值。

- 能够将多个转换应用于单个索赔。

- 能够根据用户类型和用户所属的组指定声明源。

有关这些新功能的详细信息（包括如何使用这些功能），请参阅[为企业应用程序的 SAML 令牌中发出的自定义声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中最终用户的新"我的登录"页面

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 监视和报告

我们添加了一个新的 **"我的登录"** 页面（https://mysignins.microsoft.com)让组织的用户查看其最近的登录历史记录，以检查是否有任何异常活动。 此新页面允许用户查看：

- 如果有人试图猜测他们的密码。

- 如果攻击者成功登录到他们的帐户和从什么位置。

- 攻击者尝试访问哪些应用。

有关详细信息，请参阅["用户"现在可以查看其登录历史记录，了解异常活动](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)博客。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>Azure AD 域服务 （Azure AD DS） 从经典迁移到 Azure 资源管理器虚拟网络

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

对于被困在经典虚拟网络上的客户，我们为您提供好消息！ 现在，您可以执行从经典虚拟网络到现有资源管理器虚拟网络的一次性迁移。 迁移到资源管理器虚拟网络后，您将能够利用其他和升级的功能，如细粒度密码策略、电子邮件通知和审核日志。

有关详细信息，请参阅预览[- 将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 页面协定布局的更新

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

我们对 Azure AD B2C 的页面协定版本 1.2.0 引入了一些新的更改。 在此更新版本中，您现在可以控制元素的加载顺序，这也有助于阻止加载样式表 （CSS） 时发生的闪烁。

有关对页面协定所做的更改的完整列表，请参阅[版本更改日志](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新到"我的应用"页面以及新的工作区（公共预览）

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 访问控制

现在，您可以自定义组织用户查看和访问全新"我的应用"体验的方式，包括使用新的工作区功能，以便他们更轻松地查找应用。 新的工作区功能充当组织用户已有权访问的应用的筛选器。

有关推出新的"我的应用"体验和创建工作区的详细信息，请参阅[在"我的应用（预览）"门户上创建工作区](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支持每月活动基于用户的计费模型（一般可用性）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

Azure AD B2C 现在支持每月活动用户 （MAU） 计费。 MAU 计费基于日历月中具有身份验证活动的唯一用户数。 现有客户可以随时切换到此新的计费方法。

从 2019 年 11 月 1 日起，所有新客户将自动使用此方法计费。 这种计费方法通过成本效益和提前计划的能力使客户受益。

有关详细信息，请参阅[升级到每月活动用户的计费模型](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 应用库中提供的新联合应用 - 2019 年 10 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019 年 10 月，我们在应用库中添加了这 35 个具有联合支持的新应用：

[在危机的情况下 - 移动](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)，[朱诺之旅](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)，[指数，](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial) [Tact，](https://tact.ai/assistant/) [OpusCapita现金管理](http://cm1.opuscapita.com/tenantname)，[销售，](https://prd.salestim.io/forms)[学习，](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)[迪纳茨](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)， [HunchBuzz，](https://login.hunchbuzz.com/integrations/azure/process)[新鲜作品](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)， [eCornell，](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)[船哈兹马特](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)， [Netskope 云安全](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)，[Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)[内容，](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)[绑定调谐](https://bindtuning.com/login)，[雇用Vue协调 - 欧洲](https://www.hirevue.com/)， [HireVue坐标 - 美国只](https://www.hirevue.com/)，[Cambium Xirrus EasyPass Portal](https://login.xirrus.com/azure-signup)[雇用Vue - 美国](https://www.hirevue.com/)，[威蒂帕罗知识盒](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)， [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)[云](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)[，](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial) [Teamie](https://theteamie.com/)，[速度为团队](https://velocity.peakup.org/teams/login)， [SIGNL4](https://account.signl4.com/manage)， [EAB 导航 IMPL，](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)[屏幕Meet，](https://console.screenmeet.com/)[欧米茄点](https://pi.ompnt.com/)，[为 Intune （iPhone） 说话电子邮件](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)，[为办公室 365 直接 （iPhone/安卓）](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)说话电子邮件 ， [ExactCare SSO，](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial) [iHealthHome护理导航系统](https://ihealthnav.com/account/signin)， [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 门户中的整合安全菜单项

**类型：** 已更改的功能  
**服务类别：** 身份保护  
**产品功能：** 标识安全和保护

现在，您可以在 Azure 门户中的新**安全**菜单项和 **"搜索**"栏访问所有可用的 Azure AD 安全功能。 此外，新的**安全**登录页称为 **"安全 - 入门**"，将提供指向我们的公共文档、安全指南和部署指南的链接。

新的**安全**菜单包括：

- 条件性访问
- 标识保护
- 安全中心
- 标识安全评分
- 身份验证方法
- Mfa
- 风险报告 - 风险用户、风险登录、风险检测
- 更多内容...

有关详细信息，请参阅安全[- 入门](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 组过期策略增强自动续订

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 身份生命周期管理

Office 365 组过期策略已增强，可自动续订其成员正在使用中的组。 组将根据所有 Office 365 应用（包括 Outlook、SharePoint 和团队）的用户活动自动续订。

此增强功能有助于减少组过期通知，并有助于确保活动组继续可用。 如果 Office 365 组已具有活动过期策略，则无需执行任何操作来打开此新功能。

有关详细信息，请参阅为[Office 365 组配置过期策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>更新了 Azure AD 域服务 （Azure AD DS） 创建体验

**类型：** 已更改的功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们更新了 Azure AD 域服务 （Azure AD DS），以包括新的和改进的创建体验，帮助您只需点击三下即可创建托管域！ 此外，您现在可以从模板上载和部署 Azure AD DS。

有关详细信息，请参阅[教程：创建和配置 Azure 活动目录域服务实例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

---
