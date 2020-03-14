---
title: 新增功能？ 发行说明 - Azure Active Directory | Microsoft Docs
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
ms.date: 02/27/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5cf12c28dc04d0dbb8a680d45c8d8f5f5faa2d82
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79262095"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制并粘贴以下 URL，获取有关何时通过复制和粘贴此 URL 来重新访问此页面的通知： `https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us` 到 ![RSS 源读者 "图标](./media/whats-new/feed-icon-16x16.png) 源读者。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="february-2020"></a>2020 年 2 月
 
### <a name="identity-secure-score---mfa-improvement-action-updates"></a>标识安全分数-MFA 改善操作更新

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
为了反映企业在应用与业务相关的策略时是否需要确保过的话很安全性，Microsoft 安全分数会删除三个围绕多重身份验证（MFA）的改进操作，并添加两个。

将删除以下改进操作：

- 注册 MFA 的所有用户
- 要求所有用户执行 MFA
- Azure AD 特权角色需要 MFA

将添加以下改进操作：

- 确保所有用户都可以完成 MFA 以实现安全访问
- 需要对管理角色进行 MFA

这些新的改进操作要求在目录中注册用户或管理员，并建立符合组织需求的正确策略集。 主要目标是具有灵活性，同时确保所有用户和管理员都可以使用多个因素或基于风险的标识验证提示进行身份验证。 这可以采用设置安全性默认值的形式，此默认设置允许 Microsoft 决定何时质询用户进行 MFA，或具有多个应用范围决定的策略。 作为这些改进操作的一部分，将不再在评分计算中包含基线保护策略。 [详细了解 Microsoft 安全分数中的内容](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score-whats-coming?view=o365-worldwide)。
 
---

### <a name="azure-ad-domain-services-sku-selection"></a>Azure AD 域服务 SKU 选择

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务
 
我们听说 Azure AD 域服务客户需要更灵活地选择其实例的性能级别。 从2020年2月1日开始，我们从动态模型（其中 Azure AD 根据对象计数确定性能和定价层）切换到自选择模型。 现在，客户可以选择与其环境匹配的性能层。 此更改还允许我们启用新方案，如资源林和高级功能，如每日备份。 对象计数现在对所有 Sku 均无限制，但我们将继续为每个层提供对象计数建议。

**无需立即进行客户操作。** 对于现有客户，在2020年2月1日使用的动态层确定了新的默认层。 此更改的结果不会影响定价或性能。 今后，Azure AD DS 客户需要评估性能要求，因为它们的目录大小和工作负荷特征发生变化。 在服务层之间切换将继续进行无中断操作，并且我们将不再自动根据其目录的增长将客户移动到新的层。 此外，将不会有任何价格增加，新定价将与当前计费模型保持一致。 有关详细信息，请参阅[AZURE AD DS sku 文档](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD 域服务定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。


---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 应用库中提供了新的联合应用程序-2020 年2月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年2月，我们已向应用库添加了这31个新应用和联合支持： 

[IamIP 专利平台](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[体验 Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)，[适用于 Azure](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)的导航 SSO， [Barracuda 电子邮件安全服务](https://ess.barracudanetworks.com/sso/azure)， [ABa 报告](https://myaba.co.uk/client-access/signin/auth/msad)，以防出现[危机在线门户](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial)， [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)， [Beekeeper Azure AD 数据连接器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)， [Korn 运送评估](https://www.kornferry.com/solutions/kf-digital/kf-assess)， [Verkada Command](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)， [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)， [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)， [EAB](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)，Relic [，Thulium，](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [，](https://admin.thulium.com/login/instance) [Ticket Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial) [适用于团队](https://links.officeatwork.com/templatechooser-download-teams)、 [Beesy](https://www.beesy.me/index.php/site/login)、[运行状况支持的系统](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)、 [MURAL](https://app.mural.co/signup)、 [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)、 [LavaDo](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview)、 [Wakelet](https://wakelet.com/login)、 [Firmex vdr-fp-xzo](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)和 ThingLink 的选择器，[适用于教师和学校](https://www.thinglink.com/)、 [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)、 [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product)、 [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial)、 [InvitePeople](https://invitepeople.com/login)、 [Reprints](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年2月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="azure-ad-support-for-fido2-security-keys-in-hybrid-environments"></a>Azure AD 对混合环境中 FIDO2 安全密钥的支持

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
我们宣布 Azure AD 支持在混合环境中 FIDO2 安全密钥。 用户现在可以使用 FIDO2 安全密钥登录到其混合 Azure AD 加入的 Windows 10 设备，并无缝登录到本地和云资源。 对混合环境的支持是无密码客户最常请求的功能，因为我们最初在 Azure AD 联接的设备中启动了 FIDO2 支持公共预览版。 使用生物识别和公钥/私钥加密等高级技术进行无密码身份验证可在安全时提供便利和易用性。 使用此公共预览版，现在可以使用 FIDO2 安全密钥等新式身份验证来访问传统 Active Directory 资源。 有关详细信息，请参阅[SSO 到本地资源](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key-on-premises)。 

若要开始，请访问为[你的租户启用 FIDO2 安全密钥](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-passwordless-security-key)以获取分步说明。 

---
 
### <a name="the-new-my-account-experience-is-now-generally-available"></a>这个新的我的帐户体验现已正式发布 

**类型：** 已更改的功能  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 最终用户体验
 
我的帐户，一站式用于满足所有最终用户帐户管理需求，现已正式发布！ 最终用户可以通过 URL 或新的 "我的应用" 体验的标头访问此新站点。 若要详细了解所有自助服务功能，请参阅我的帐户[门户概述](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中的新体验。

---
 
### <a name="my-account-site-url-updating-to-myaccountmicrosoftcom"></a>我的帐户网站 URL 正在更新到 myaccount.microsoft.com

**类型：** 已更改的功能  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 最终用户体验
 
新的我的帐户最终用户体验会将其 URL 更新到下个月 https://myaccount.microsoft.com。 可在[我的帐户门户帮助](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中找到有关其提供给最终用户的体验和所有帐户自助服务功能的详细信息。

---
 
## <a name="january-2020"></a>2020 年 1 月
 
### <a name="the-new-my-apps-portal-is-now-generally-available"></a>新的 "我的应用" 门户现已正式发布

**类型：** 更改计划  
**服务类别：** 我的应用  
**产品功能：** 最终用户体验
 
将你的组织升级到现已正式发布的 "我的应用" 门户！ 在 "[我的应用" 门户上的 "创建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)" 中找到有关新门户和集合的详细信息。

---
 
### <a name="workspaces-in-azure-ad-have-been-renamed-to-collections"></a>Azure AD 中的工作区已重命名为集合

**类型：** 已更改的功能  
**服务类别：** 我的应用   
**产品功能：** 最终用户体验
 
工作区，管理员可以配置筛选器来组织其用户应用，现在称为集合。 若要详细了解如何在["我的应用" 门户上创建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)，请参阅。

---
 
### <a name="azure-ad-b2c-phone-sign-up-and-sign-in-using-custom-policy-public-preview"></a>使用自定义策略 Azure AD B2C 手机注册和登录（公共预览版）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
通过电话号码注册和登录，开发人员和企业可以允许其客户使用通过短信发送到用户的电话号码的一次性密码进行注册和登录。 此功能还允许客户在电话号码失去访问权限时更改其电话号码。 使用自定义策略的强大功能，通过电话注册和登录，开发人员和企业可以通过页面自定义来传达其品牌。 了解如何[在 Azure AD B2C 中设置自定义策略的手机注册和登录](https://docs.microsoft.com/azure/active-directory-b2c/phone-authentication)。
 
---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---january-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年1月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Promapp]( https://docs.microsoft.com/azure/active-directory/saas-apps/promapp-provisioning-tutorial)
- [Zscaler Private Access](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-private-access-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2020"></a>Azure AD 应用库中提供了新的联合应用程序-2020 年1月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年1月，我们已向应用程序库添加了这些支持联合身份验证的33新应用： 

[TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)适用 Azure AD [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial) [365 于](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access) [Abibot Netlogistik](https://1030-review-develop-3zknud.netlogistik.com/) [ORACLE 电子商务套件-EBS、Abibot 和 Netlogistik](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)、SkyKick、Upshotly、 [LeaveBot、](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)DataCamp [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial) [、TripActions](https://www.intumit.com/english/SmartWork.html)、SmartWork [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、Dotcom、SSOGEN、PeopleSoft [Dotcom-Monitor](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、JDE、MyCirqa、Yuhu、LumApps [、、](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)、 [、、](https://sites.lumapps.com/login)、 [JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、 [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial) [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial) [LeaveBot](https://leavebot.io/#home)[Upwork Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)、 [Talentsoft](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)、 [SmartDB for Microsoft 团队](http://teams.smartdb.jp/login/)， [PressPage](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)， [ContractSafe Saml2 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)， [Maxient 筹办 Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)， [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)， [PortalTalk 365](https://www.portaltalk.com/)， [CoreView](https://portal.coreview.com/)， [Squelch 云 Office365 连接器](https://laxmi.squelch.io/login)， [PingFlow Authentication](https://app-staging.pingview.io/)， [PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)， [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)， [Sandwai](https://app.sandwai.com/)， [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial)， [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)， [Akari虚拟助手](https://akari.io/akari-virtual-assistant/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="two-new-identity-protection-detections"></a>两个新的标识保护检测

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
我们已将两个新的登录链接检测类型添加到 Identity Protection：可疑收件箱操作规则和不可能旅行。 这些脱机检测由 Microsoft Cloud App Security （MCAS）发现，并影响用户和在 Identity Protection 中登录的风险。 有关这些检测的详细信息，请参阅[登录风险类型](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-risks#sign-in-risk)。
 
---
 
### <a name="breaking-change-uri-fragments-will-not-be-carried-through-the-login-redirect"></a>重大更改：不会通过登录重定向传输 URI 片段

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
从2020年2月8日开始，当请求发送到 login.microsoftonline.com 以登录用户时，服务将向请求追加一个空片段。  这可以确保浏览器擦除请求中的任何现有片段，从而防止重定向攻击类。 应用程序不应依赖于此行为。 有关详细信息，请参阅 Microsoft 标识平台文档中的[重大更改](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#february-2020)。

---

## <a name="december-2019"></a>2019 年 12 月

### <a name="integrate-sap-successfactors-provisioning-into-azure-ad-and-on-premises-ad-public-preview"></a>将 SAP SuccessFactors 预配集成到 Azure AD 和本地 AD （公共预览版）

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理

你现在可以将 SAP SuccessFactors 作为 Azure AD 中的权威标识源进行集成。 此集成可帮助你自动完成端到端标识生命周期，包括使用基于 HR 的事件（例如新员工或终止）来控制 Azure AD 帐户的预配。

有关如何设置 SAP SuccessFactors 入站预配以 Azure AD 的详细信息，请参阅[配置 Sap SuccessFactors 自动预配](https://aka.ms/SAPSuccessFactorsInboundTutorial)教程。

---

### <a name="support-for-customized-emails-in-azure-ad-b2c-public-preview"></a>Azure AD B2C 中的自定义电子邮件支持（公共预览版）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

你现在可以使用 Azure AD B2C 在用户注册使用你的应用时创建自定义的电子邮件。 通过使用 DisplayControls （当前为预览版）和第三方电子邮件提供商（例如， [SendGrid](https://sendgrid.com/)、 [SparkPost](https://sparkpost.com/)或自定义 REST API），可以使用自己的电子邮件模板、**发件**人地址和主题文本，以及支持本地化和自定义的一次性密码（OTP）设置。

有关详细信息，请参阅[Azure Active Directory B2C 中的自定义电子邮件验证](https://docs.microsoft.com/azure/active-directory-b2c/custom-email)。

---

### <a name="replacement-of-baseline-policies-with-security-defaults"></a>用安全默认值替换基准策略

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 标识安全和保护

作为身份验证的默认安全模型的一部分，我们将从所有租户中删除现有的基线保护策略。 在2月结束时，此删除操作面向完成。 这些基准保护策略的替换是安全默认值。 如果你一直在使用基线保护策略，则必须计划移到 "新建安全默认策略" 或 "条件性访问"。 如果尚未使用这些策略，则不需要采取任何措施。

有关新安全默认值的详细信息，请参阅[什么是安全默认值？](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults) 有关条件性访问策略的详细信息，请参阅[常见的条件访问策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。

---

## <a name="november-2019"></a>2019 年 11 月

### <a name="support-for-the-samesite-attribute-and-chrome-80"></a>支持 SameSite 属性和 Chrome 80

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

作为 cookie 的默认默认模型的一部分，Chrome 80 浏览器正在改变它如何处理 cookie，而没有 `SameSite` 特性。 任何未指定 `SameSite` 特性的 cookie 都将被视为 `SameSite=Lax`，这将导致 Chrome 阻止你的应用程序可能依赖的某些跨域 cookie 共享方案。 若要维护较旧的 Chrome 行为，可以使用 `SameSite=None` 特性并添加其他 `Secure` 属性，因此只能通过 HTTPS 连接访问跨站点 cookie。 Chrome 计划在2020年2月4日完成此更改。

我们建议所有开发人员使用本指南来测试其应用程序：

- 将 "**使用安全 Cookie** " 设置的默认值设置为 **"是"** 。

- 将**SameSite**属性的默认值设置为 "**无**"。

- 添加一个**安全**的其他 `SameSite` 特性。

有关详细信息，请参阅 SameSite ASP.NET ASP.NET Core 和[更高79版本的客户网站和 Microsoft 产品和服务的潜在中断](https://support.microsoft.com/help/4522904/potential-disruption-to-microsoft-services-in-chrome-beta-version-79)[中的即将发生的 Cookie 更改](https://devblogs.microsoft.com/aspnet/upcoming-samesite-cookie-changes-in-asp-net-and-asp-net-core/)。

---

### <a name="new-hotfix-for-microsoft-identity-manager-mim-2016-service-pack-2-sp2"></a>Microsoft Identity Manager （MIM） 2016 Service Pack 2 （SP2）的新修补程序

**类型：** 已修复  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理

修补程序汇总包（build 4.6.34.0）可用于 Microsoft Identity Manager （MIM） 2016 Service Pack 2 （SP2）。 此汇总包解决了问题并增加了在此更新中添加了 "修复和改进问题" 部分中描述的改进功能。

有关详细信息和下载修补程序包，请参阅[Microsoft Identity Manager 2016 Service Pack 2 （生成4.6.34.0）更新汇总可用](https://support.microsoft.com/help/4512924/microsoft-identity-manager-2016-service-pack-2-build-4-6-34-0-update-r)。

---

### <a name="new-ad-fs-app-activity-report-to-help-migrate-apps-to-azure-ad-public-preview"></a>新 AD FS 应用活动报表，可帮助将应用迁移到 Azure AD （公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

使用 "Azure 门户" 中的 "新建 Active Directory 联合身份验证服务（AD FS）" 应用活动报表来确定哪些应用程序能够迁移到 Azure AD。 该报表将评估所有 AD FS 的应用程序的兼容性 Azure AD、检查是否有任何问题，并提供有关准备要迁移的单个应用程序的指南。

有关详细信息，请参阅[使用 AD FS 应用程序活动报告将应用程序迁移到 Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity)。

---

### <a name="new-workflow-for-users-to-request-administrator-consent-public-preview"></a>用户的新工作流，用于请求管理员同意（公共预览版）

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 访问控制

管理员可以通过新的管理员许可工作流向需要管理员批准的应用授予访问权限。 如果用户尝试访问某个应用但无法提供许可，则他们现在可以发送管理员批准请求。 通过电子邮件发送请求，并将其放在可从 Azure 门户访问的队列中，该队列已指定为审阅者。 在审阅者对挂起的请求执行操作之后，会向请求用户通知该操作。

有关详细信息，请参阅[配置管理员许可工作流（预览）](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-admin-consent-workflow)。

---

### <a name="new-azure-ad-app-registrations-token-configuration-experience-for-managing-optional-claims-public-preview"></a>用于管理可选声明的新 Azure AD 应用注册令牌配置体验（公共预览版）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验

Azure 门户上的 "新建**Azure AD 应用注册令牌配置**" 边栏选项卡现在向应用开发人员显示其应用的可选声明的动态列表。 这种新体验可帮助简化 Azure AD 应用迁移，并最大限度地减少可选的声明配置错误。

有关详细信息，请参阅向[Azure AD 应用提供可选声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-optional-claims)。

---

### <a name="new-two-stage-approval-workflow-in-azure-ad-entitlement-management-public-preview"></a>Azure AD 授权管理（公共预览版）中的新的两阶段审批工作流

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

我们引入了新的两阶段审批工作流，使你可以要求两个审批者批准用户对访问包的请求。 例如，你可以对其进行设置，以便请求用户的经理必须首先批准，然后你还可以要求资源所有者批准。 如果其中一个审批者未批准，则不授予访问权限。

有关详细信息，请参阅[Azure AD 授权管理中的访问包的更改请求和批准设置](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-request-policy)。

---

### <a name="updates-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新 "我的应用" 页面和新工作区（公共预览版）

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 第三方集成

你现在可以自定义组织用户查看和访问刷新的应用体验的方式。 这种新体验还包括新的工作区功能，使用户可以更轻松地查找和组织应用。

有关新的 "我的应用" 体验和创建工作区的详细信息，请参阅[在 "我的应用" 门户中创建工作区](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="google-social-id-support-for-azure-ad-b2b-collaboration-general-availability"></a>Azure AD B2B 协作（公开上市）的 Google 社交 ID 支持

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** 用户身份验证

在 Azure AD 中使用 Google 社交 Id （Gmail 帐户）的新支持有助于使用户和合作伙伴更轻松地进行协作。 你的合作伙伴不再需要创建和管理新的特定于 Microsoft 的帐户。 Microsoft 团队现在完全支持所有客户端上的 Google 用户，以及公共和与租户相关的身份验证终结点。

有关详细信息，请参阅[添加 Google 作为 B2B 来宾用户的标识提供者](https://docs.microsoft.com/azure/active-directory/b2b/google-federation)。

---

### <a name="microsoft-edge-mobile-support-for-conditional-access-and-single-sign-on-general-availability"></a>Microsoft Edge 移动支持条件访问和单一登录（正式发布）

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

IOS 和 Android 上的 Microsoft Edge Azure AD 现在支持 Azure AD 单一登录和条件访问：

- **Microsoft Edge 单一登录（SSO）：** 对于所有连接 Azure AD 的应用，本地客户端（例如 Microsoft Outlook 和 Microsoft Edge）现在均可使用单一登录。

- **Microsoft Edge 条件性访问：** 通过基于应用程序的条件性访问策略，用户必须使用 Microsoft Intune 保护的浏览器，如 Microsoft Edge。

有关条件访问和 SSO 与 Microsoft Edge 的详细信息，请参阅[Microsoft Edge 移动支持条件访问和单一登录现已公开发布](https://techcommunity.microsoft.com/t5/Intune-Customer-Success/Microsoft-Edge-Mobile-Support-for-Conditional-Access-and-Single/ba-p/988179)的博客文章。 有关如何使用[基于应用的条件访问](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)或[基于设备的条件性访问](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)设置客户端应用的详细信息，请参阅[使用 Microsoft Intune 策略保护的浏览器管理 web 访问](https://docs.microsoft.com/intune/apps/app-configuration-managed-browser)。

---

### <a name="azure-ad-entitlement-management-general-availability"></a>Azure AD 的权利管理（正式发布）

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理

Azure AD 授权管理是一项新的标识调控功能，可帮助组织大规模管理标识和访问生命周期。 这项新功能可帮助实现跨组、应用和 SharePoint Online 站点的访问请求工作流、访问分配、审核和过期。

利用 Azure AD 的权利管理，你可以更有效地管理员工的访问权限，还可以更有效地管理组织外需要访问这些资源的用户的访问权限。

有关详细信息，请参阅[什么是 Azure AD 权限管理？](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview#license-requirements)

---

### <a name="automate-user-account-provisioning-for-these-newly-supported-saas-apps"></a>自动执行这些新支持的 SaaS 应用的用户帐户预配

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成  

你现在可以为这些新集成的应用自动创建、更新和删除用户帐户：

[SAP Cloud Platform Identity Authentication Service](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-hana-cloud-platform-identity-authentication-tutorial)， [RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-provisioning-tutorial)， [SpaceIQ](https://docs.microsoft.com/azure/active-directory/saas-apps/spaceiq-provisioning-tutorial)， [Miro](https://docs.microsoft.com/azure/active-directory/saas-apps/miro-provisioning-tutorial)， [Cloudgate](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-provisioning-tutorial)， [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloudsuite-provisioning-tutorial)， [officespace software Software](https://docs.microsoft.com/azure/active-directory/saas-apps/officespace-software-provisioning-tutorial)， [Priority Matrix](https://docs.microsoft.com/azure/active-directory/saas-apps/priority-matrix-provisioning-tutorial)

有关如何使用自动用户帐户预配来更好地保护组织的详细信息，请参阅[使用 Azure AD 自动完成 SaaS 应用程序的用户预配](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年11月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年11月，我们已向应用程序库添加了以下21个新应用和联合支持：

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)、 [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)、[成员（BAM）](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)、 [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)、 [RIVA](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)、 [ResLife 门户](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、 [NegometrixPortal 单一登录（SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial)、 [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、 [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial)、 [MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial) [、BlueMail、](https://loginself1.bluemail.me/) [Beedle](https://teams-web.beedle.co/#/)、 [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)、 [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial)、 [&](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial) [Foko](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial)、Qmarkets、Netskope、uniFLOW、Claromentis [、、](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)、、、 [、](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)、 [、](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial)， [Jisc Student 选民 Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial)， [e4enable](https://portal.e4enable.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-and-improved-azure-ad-application-gallery"></a>新的和改进的 Azure AD 应用程序库

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们更新了 Azure AD 应用程序库，使你能够更轻松地查找 Azure Active Directory 租户上支持预配、OpenID Connect 和 SAML 的预先集成的应用。

有关详细信息，请参阅[将应用程序添加到 Azure Active Directory 租户](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal)。

---

### <a name="increased-app-role-definition-length-limit-from-120-to-240-characters"></a>应用角色定义长度限制从120到240个字符

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们听说，客户在某些应用和服务中的应用角色定义值的长度限制太短，超过120个字符。 作为响应，我们增加了 role 值定义的最大长度为240个字符。

有关使用特定于应用程序的角色定义的详细信息，请参阅在[应用程序中添加应用程序角色并在令牌中接收它们](https://docs.microsoft.com/azure/active-directory/develop/howto-add-app-roles-in-azure-ad-apps)。

---

## <a name="october-2019"></a>2019 年 10 月

### <a name="deprecation-of-the-identityriskevent-api-for-azure-ad-identity-protection-risk-detections"></a>弃用用于 Azure AD Identity Protection 风险检测的 identityRiskEvent API  

**类型：** 更改计划  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

为了应对开发人员的反馈，Azure AD Premium P2 订户现在可以使用适用于 Microsoft Graph 的新的 riskDetection API 对 Azure AD Identity Protection 的风险检测数据执行复杂的查询。 现有的[identityRiskEvent](https://docs.microsoft.com/graph/api/resources/identityriskevent?view=graph-rest-beta) API beta 版本将停止返回**2020 年1月 10**日附近的数据。 如果你的组织正在使用 identityRiskEvent API，你应转换为新的 riskDetection API。

有关新的 riskDetection API 的详细信息，请参阅[风险检测 api 参考文档](https://aka.ms/RiskDetectionsAPI)。

---

### <a name="application-proxy-support-for-the-samesite-attribute-and-chrome-80"></a>应用程序代理支持 SameSite 属性和 Chrome 80

**类型：** 更改计划  
**服务类别：** 应用代理  
**产品功能：** 访问控制

在 Chrome 80 浏览器发布之前的几周，我们计划更新应用程序代理 cookie 如何处理**SameSite**属性。 在版本的 Chrome 80 中，未指定**SameSite**属性的任何 cookie 都将被视为已设置为 `SameSite=Lax`。

为了帮助避免由于此更改而产生的负面影响，我们将通过以下方式更新应用程序代理访问和会话 cookie：

- 将 "**使用安全 Cookie** " 设置的默认值设置为 **"是"** 。

- 将**SameSite**属性的默认值设置为**None**。

    >[!NOTE]
    > 应用程序代理访问 cookie 始终是通过安全通道完全传输的。 这些更改仅适用于会话 cookie。

有关应用程序代理 cookie 设置的详细信息，请参阅[Azure Active Directory 中用于访问本地应用程序的 cookie 设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="app-registrations-legacy-and-converged-app-management-from-the-application-registration-portal-appsdevmicrosoftcom-will-no-longer-be-available"></a>应用程序注册门户（apps.dev.microsoft.com）中的应用注册（旧）和聚合应用管理将不再可用

**类型：** 更改计划  
**服务类别：** N/A  
**产品功能：** 开发人员体验

在不久的将来，具有 Azure AD 帐户的用户将无法再使用应用程序注册门户（apps.dev.microsoft.com）注册和管理聚合应用程序，或注册和管理应用注册中的应用程序（旧版）Azure 门户中的体验。

若要了解有关新应用注册体验的详细信息，请参阅[Azure 门户培训指南中的应用注册](../develop/app-registrations-training-guide-for-app-registrations-legacy-users.md)。

---

### <a name="users-are-no-longer-required-to-re-register-during-migration-from-per-user-mfa-to-conditional-access-based-mfa"></a>在从每个用户 MFA 到基于条件访问的 MFA 的迁移过程中，不再需要用户重新注册

**类型：** 已修复  
**服务类别：** MFA  
**产品功能：** 标识安全和保护

我们修复了一个已知问题，即，如果用户被禁用按用户多重身份验证（MFA），然后通过条件性访问策略启用了 MFA，则用户需要重新注册。

若要要求用户重新注册，可以在 Azure AD 门户中的用户身份验证方法中选择**所需的 "重新注册 MFA** " 选项。 有关将用户从每用户 MFA 迁移到基于条件访问的 MFA 的详细信息，请参阅[将用户从每用户 Mfa 转换为基于条件访问的 mfa](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted#convert-users-from-per-user-mfa-to-conditional-access-based-mfa)。

---

### <a name="new-capabilities-to-transform-and-send-claims-in-your-saml-token"></a>用于转换和发送 SAML 令牌中的声明的新功能

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

我们添加了其他功能，可帮助你在 SAML 令牌中自定义和发送声明。 这些新功能包括：

- 其他声明转换函数，有助于修改在声明中发送的值。

- 能够将多个转换应用于单个声明。

- 基于用户类型和用户所属的组指定声明源的能力。

有关这些新功能的详细信息，包括如何使用它们，请参阅[自定义用于企业应用程序的 SAML 令牌中颁发的声明](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)。

---

### <a name="new-my-sign-ins-page-for-end-users-in-azure-ad"></a>Azure AD 中的最终用户的 "我的新登录" 页面

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 监视和报告

我们添加了新的 **"我的登录"** 页（ https://mysignins.microsoft.com)，让组织的用户查看最近的登录历史记录以检查是否有任何异常活动。 此新页面允许用户查看：

- 如果有人试图猜测其密码。

- 如果攻击者成功登录到其帐户和位置。

- 攻击者尝试访问的应用。

有关详细信息，请参阅[用户现在可以检查异常活动博客的登录历史记录](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Users-can-now-check-their-sign-in-history-for-unusual-activity/ba-p/916066)。

---

### <a name="migration-of-azure-ad-domain-services-azure-ad-ds-from-classic-to-azure-resource-manager-virtual-networks"></a>将 Azure AD 域服务（Azure AD DS）从经典部署模型迁移到 Azure 资源管理器虚拟网络

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

对于已陷入经典虚拟网络的客户，我们为你提供了精彩新闻！ 你现在可以执行从经典虚拟网络到现有资源管理器虚拟网络的一次性迁移。 移到资源管理器虚拟网络后，你将能够利用附加功能和升级功能，如细化密码策略、电子邮件通知和审核日志。

有关详细信息，请参阅[预览-将 Azure AD 域服务从经典虚拟网络模型迁移到资源管理器](https://docs.microsoft.com/azure/active-directory-domain-services/migrate-from-classic-vnet)。

---

### <a name="updates-to-the-azure-ad-b2c-page-contract-layout"></a>Azure AD B2C 页面协定布局的更新

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

我们针对 Azure AD B2C 的页面协定的版本1.2.0 引入了一些新的更改。 在此更新的版本中，现在可以控制元素的加载顺序，这也有助于停止加载样式表（CSS）时发生的闪烁。

有关对页协定所做更改的完整列表，请参阅[版本更改日志](https://docs.microsoft.com/azure/active-directory-b2c/page-layout#120)。

---

### <a name="update-to-the-my-apps-page-along-with-new-workspaces-public-preview"></a>更新到 "我的应用程序" 页和新工作区（公共预览版）

**类型：** 新功能  
**服务类别：** 我的应用  
**产品功能：** 访问控制

你现在可以自定义组织用户查看和访问全新 "我的应用" 体验的方式，包括使用新的工作区功能，使其更易于查找应用。 新的工作区功能充当组织用户已有权访问的应用的筛选器。

有关推出新的 "我的应用" 体验和创建工作区的详细信息，请参阅在["我的应用（预览版）" 门户中创建工作区](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-workspaces)。

---

### <a name="support-for-the-monthly-active-user-based-billing-model-general-availability"></a>支持每月活动的基于用户的计费模型（正式发布）

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C

Azure AD B2C 现在支持每月活动用户（MAU）计费。 MAU 计费基于日历月中具有身份验证活动的唯一用户数。 现有客户可以随时切换到此新计费方法。

从2019年11月1日开始，所有新客户都将使用此方法自动收费。 此计费方法通过成本优势和计划提前计划，为客户带来好处。

有关详细信息，请参阅[升级到每月活动用户计费模式](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing#upgrade-to-monthly-active-users-billing-model)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年10月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年10月，我们已向应用程序库添加了这些支持联合身份验证的35新应用：

[在出现危机时–移动](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-mobile-tutorial)、 [Juno 旅程](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-tutorial)、 [ExponentHR](https://docs.microsoft.com/azure/active-directory/saas-apps/exponenthr-tutorial)、[即可](https://tact.ai/assistant/)、 [OpusCapita 现金管理](http://cm1.opuscapita.com/tenantname)、 [Salestim](https://prd.salestim.io/forms)、 [Learnster](https://docs.microsoft.com/azure/active-directory/saas-apps/learnster-tutorial)、 [Dynatrace](https://docs.microsoft.com/azure/active-directory/saas-apps/dynatrace-tutorial)、 [HunchBuzz](https://login.hunchbuzz.com/integrations/azure/process)、 [Freshworks](https://docs.microsoft.com/azure/active-directory/saas-apps/freshworks-tutorial)、 [eCornell](https://docs.microsoft.com/azure/active-directory/saas-apps/ecornell-tutorial)、 [SHIPHAZMAT](https://docs.microsoft.com/azure/active-directory/saas-apps/shiphazmat-tutorial)、 [Netskope Cloud Security](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-cloud-security-tutorial)、 [Contentful](https://docs.microsoft.com/azure/active-directory/saas-apps/contentful-tutorial)、 [Bindtuning](https://bindtuning.com/login)、 [HireVue 协调-EU](https://www.hirevue.com/)、 [HireVue 协调-USOnly](https://www.hirevue.com/)、 [HireVue 协调-美国](https://www.hirevue.com/)、 [WittyParrot知识盒](https://wittyapi.wittyparrot.com/wittyparrot/api/provision/trail/signup)， [Cloudmore](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudmore-tutorial)， [Visit.org](https://docs.microsoft.com/azure/active-directory/saas-apps/visitorg-tutorial)， [Cambium Xirrus EasyPass 门户](https://login.xirrus.com/azure-signup)， [Paylocity](https://docs.microsoft.com/azure/active-directory/saas-apps/paylocity-tutorial)， [Mail 祝您好运！](https://docs.microsoft.com/azure/active-directory/saas-apps/mail-luck-tutorial)， [Teamie](https://theteamie.com/) [，SIGNL4，EAB](https://velocity.peakup.org/teams/login) [，IMPL](https://account.signl4.com/manage)， [ScreenMeet，](https://console.screenmeet.com/) [ExactCare，IHEALTHHOME](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-impl-tutorial)，，， [Omega 点](https://pi.ompnt.com/)，[用于 Intune 的](https://speaking.email/FAQ/98/email-access-via-microsoft-intune)电子邮件（ [365](https://speaking.email/FAQ/126/email-access-via-microsoft-office-365-direct)iphone）， [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/exactcare-sso-tutorial)，[护理导航系统](https://ihealthnav.com/account/signin)， [Qubie](https://qubie.azurewebsites.net/static/adminTab/authorize.html)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="consolidated-security-menu-item-in-the-azure-ad-portal"></a>Azure AD 门户中的 "合并安全" 菜单项

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

你现在可以从 "新建**安全**" 菜单项和 "**搜索**栏" 中的 Azure 门户访问所有可用 Azure AD 安全功能。 此外，新的**安全**登录页称为 "**安全性-** 入门"，它提供了公共文档、安全指南和部署指南的链接。

新的**安全**菜单包括：

- 条件性访问
- 标识保护
- 安全中心
- 标识安全评分
- 身份验证方法
- MFA
- 风险报告-有风险的用户、有风险的登录、风险检测
- 等等...

有关详细信息，请参阅[安全性-入门](https://portal.azure.com/#blade/Microsoft_AAD_IAM/SecurityMenuBlade/GettingStarted)。

---

### <a name="office-365-groups-expiration-policy-enhanced-with-autorenewal"></a>Office 365 组过期策略已通过自动续订增强

**类型：** 已更改的功能  
**服务类别：** 组管理  
**产品功能：** 标识生命周期管理

Office 365 组过期策略已增强，可自动续订其成员正在使用的组。 将基于所有 Office 365 应用中的用户活动（包括 Outlook、SharePoint 和团队） autorenewed 组。

此增强功能有助于减少组过期通知，并有助于确保活动组继续可用。 如果你已为 Office 365 组启用了有效过期策略，则无需执行任何操作来启用此新功能。

有关详细信息，请参阅为[Office 365 组配置过期策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-lifecycle)。

---

### <a name="updated-azure-ad-domain-services-azure-ad-ds-creation-experience"></a>更新 Azure AD 域服务（Azure AD DS）创建体验

**类型：** 已更改的功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

我们已更新 Azure AD 域服务（Azure AD DS）以包括新的和改进的创建体验，只需三次单击即可创建托管域！ 此外，你现在可以从模板上传和部署 Azure AD DS。

有关详细信息，请参阅[教程：创建和配置 Azure Active Directory 域服务实例](https://docs.microsoft.com/azure/active-directory-domain-services/tutorial-create-instance)。

---

## <a name="september-2019"></a>2019 年 9 月

### <a name="plan-for-change-deprecation-of-the-power-bi-content-packs"></a>规划更改：弃用 Power BI 内容包

**类型：** 更改计划  
**服务类别：** 报告  
**产品功能：** 监视和报告

从2019年10月1日开始，Power BI 将开始弃用所有内容包，包括 Azure AD Power BI 内容包。 作为此内容包的替代方法，你可以使用 Azure AD 工作簿来深入了解你 Azure AD 相关的服务。 还有其他工作簿，其中包括有关仅限报表模式下的条件性访问策略的工作簿、基于应用同意的见解，等等。

有关工作簿的详细信息，请参阅[如何将 Azure Monitor 工作簿用于 Azure Active Directory 报表](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。 有关内容包弃用的详细信息，请参阅[公告 Power BI 模板应用正式](https://powerbi.microsoft.com/blog/announcing-power-bi-template-apps-general-availability/)发布博客文章。

---

### <a name="my-profile-is-renaming-and-integrating-with-the-microsoft-office-account-page"></a>我的配置文件正在重命名并与 Microsoft Office 帐户页面集成

**类型：** 更改计划  
**服务类别：** 我的配置文件/帐户  
**产品功能：** 协作

从10月开始，"我的个人资料经验" 将成为我的帐户。 作为此更改的一部分，当前说， **"我的配置文件**" 将更改为 **"** 我的帐户"。 在命名更改和某些设计改进的基础上，更新的体验将提供与 Microsoft Office 帐户页面的其他集成。 具体而言，你将能够从 "**概述帐户**" 页访问 office 安装和订阅，还可以从 "**隐私**" 页访问与 Office 相关的联系人首选项。

有关 "我的个人资料（预览版）" 体验的详细信息，请参阅[我的个人资料（预览版）门户概述](https://docs.microsoft.com/azure/active-directory/user-help/myprofile-portal-overview)。

---

### <a name="bulk-manage-groups-and-members-using-csv-files-in-the-azure-ad-portal-public-preview"></a>在 Azure AD 门户中使用 CSV 文件批量管理组和成员（公共预览版）

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

我们非常高兴地宣布 Azure AD 门户中的批量组管理体验的公开预览版。 你现在可以使用 CSV 文件和 Azure AD 门户来管理组和成员列表，其中包括：

- 添加或删除组中的成员。

- 正在从目录下载组列表。

- 正在下载特定组的组成员的列表。

有关详细信息，请参阅[批量添加成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)、[批量删除成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)、[批量下载成员列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)和[批量下载组列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)。

---

### <a name="dynamic-consent-is-now-supported-through-a-new-admin-consent-endpoint"></a>现在，通过新的管理员许可终结点支持动态许可

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

我们已经创建了一个新的管理员许可终结点来支持动态同意，这对于想要在 Microsoft 标识平台上使用动态同意模式的应用很有用。

有关如何使用此新终结点的详细信息，请参阅[使用管理员许可终结点](https://docs.microsoft.com/azure/active-directory/develop/v2-admin-consent)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---september-2019"></a>Azure AD 应用库中提供了新的联合应用-2019 年9月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

2019年9月，我们已向应用库添加了以下29个新应用和联合支持：

[ScheduleLook](https://schedulelook.bbsonlineservices.net/)， [MS Azure SSO Access For Ethidex 合规性 Office™-单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/ms-azure-sso-access-for-ethidex-compliance-office-tutorial)， [iServer 门户](https://docs.microsoft.com/azure/active-directory/saas-apps/iserver-portal-tutorial)， [SKYSITE](https://docs.microsoft.com/azure/active-directory/saas-apps/skysite-tutorial)， [Concur 旅行和支出](https://docs.microsoft.com/azure/active-directory/saas-apps/concur-travel-and-expense-tutorial)， [WorkBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/workboard-tutorial)， [YeeFlow](https://apps.yeeflow.com/)， [ARC 设施](https://docs.microsoft.com/azure/active-directory/saas-apps/arc-facilities-tutorial)， [Luware Stratus 团队](https://stratus.emea.luware.cloud/login)，[广泛创意](https://wideideas.online/wideideas/)， [Prisma Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)， [JDLT 客户端中心](https://clients.jdlt.co.uk/login)， [RENRAKU，](https://docs.microsoft.com/azure/active-directory/saas-apps/renraku-tutorial) [SealPath 安全浏览器](https://protection.sealpath.com/SealPathInterceptorWopiSaas/Open/InstallSealPathEditorOneDrive)， [Prisma 云](https://docs.microsoft.com/azure/active-directory/saas-apps/prisma-cloud-tutorial)，Penneo [，Hiretual，](https://app.testhtm.com/settings/email-integration) [Cintoo 云](https://aec.cintoo.com/login)， [Penneo](https://app.penneo.com/)[Whitesource](https://docs.microsoft.com/azure/active-directory/saas-apps/whitesource-tutorial)，[托管的遗产在线 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-heritage-online-sso-tutorial)， [IDC](https://docs.microsoft.com/azure/active-directory/saas-apps/idc-tutorial)， [CakeHR](https://docs.microsoft.com/azure/active-directory/saas-apps/cakehr-tutorial)， [BIS](https://docs.microsoft.com/azure/active-directory/saas-apps/bis-tutorial)， [Coo Kai 团队构建](https://ms-contacts.coo-kai.jp/)， [Sonarqube](https://docs.microsoft.com/azure/active-directory/saas-apps/sonarqube-tutorial)， [Adobe Identity Management](https://docs.microsoft.com/azure/active-directory/saas-apps/adobe-identity-management-tutorial)，[发现权益 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/discovery-benefits-sso-tutorial)， [Amelio](https://app.amelio.co/)， [iTask](https://itask.yipinapp.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-global-reader-role"></a>新 Azure AD 全局读者角色

**类型：** 新功能  
**服务类别：** RBAC  
**产品功能：** 访问控制

从2019年9月24日开始，我们将开始推出称为全局读取器的新 Azure Active Directory （AD）角色。 此推出将从生产和全球云客户（GCC）开始，在10月的全球范围内完成。

全局读者角色是全局管理员的只读对应角色。 此角色中的用户可以跨 Microsoft 365 服务读取设置和管理信息，但不能执行管理操作。 我们已经创建了全局读者角色，以帮助减少组织中全局管理员的数量。 由于全局管理员帐户功能强大并且容易受到攻击，因此我们建议你的全局管理员少于五台。 建议使用全局读者角色进行计划、审核或调查。 我们还建议将全局读者角色与其他有限的管理员角色（如 Exchange 管理员）结合使用，以帮助完成工作而不需要全局管理员角色。

全局读者角色适用于新的 Microsoft 365 管理中心、Exchange 管理中心、团队管理中心、安全中心、合规性中心、Azure AD 管理中心和设备管理管理中心。

>[!NOTE]
> 公共预览版开始时，全局读者角色将无法使用： SharePoint、Privileged Access Management、客户密码箱、敏感度标签、团队生命周期、团队报告 & 呼叫分析、团队 IP 电话设备管理和团队应用分类. 所有这些服务都适用于将来的角色。

有关详细信息，请参阅[Azure Active Directory 中的管理员角色权限](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)。

---

### <a name="access-an-on-premises-report-server-from-your-power-bi-mobile-app-using-azure-active-directory-application-proxy"></a>使用 Azure Active Directory 应用程序代理从 Power BI 移动版应用访问本地报表服务器

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

通过 Power BI 移动应用与 Azure AD 应用程序代理之间的新集成，你可以安全地登录到 Power BI 移动应用，并查看本地 Power BI 报表服务器上托管的任何组织的报表。

有关 Power BI 移动版应用的信息，包括下载应用的位置，请参阅[Power BI 站点](https://powerbi.microsoft.com/mobile/)。 有关如何使用 Azure AD 应用程序代理设置 Power BI 移动应用的详细信息，请参阅[使用 Azure AD 应用程序代理启用对 Power BI 移动版的远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-power-bi)。

---

### <a name="new-version-of-the-azureadpreview-powershell-module-is-available"></a>新版本的 AzureADPreview PowerShell 模块可用

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 目录

新的 cmdlet 已添加到 AzureADPreview 模块中，可帮助定义和分配 Azure AD 中的自定义角色，其中包括：

- `Add-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Get-AzureADMSFeatureRolloutPolicy`
- `New-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicy`
- `Remove-AzureADMSFeatureRolloutPolicyDirectoryObject`
- `Set-AzureADMSFeatureRolloutPolicy`

---

### <a name="new-version-of-azure-ad-connect"></a>Azure AD Connect 的新版本

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** 目录

我们发布了自动升级客户的更新版本 Azure AD Connect。 此新版本包括几项新功能、改进和 bug 修复。 有关此新版本的详细信息，请参阅[Azure AD Connect：版本发布历史记录](https://docs.microsoft.com/azure/active-directory/hybrid/reference-connect-version-history#14250)。

---

### <a name="azure-multi-factor-authentication-mfa-server-version-802-is-now-available"></a>现在可以使用 Azure 多重身份验证（MFA）服务器版本8.0。2

**类型：** 已修复  
**服务类别：** MFA  
**产品功能：** 标识安全和保护

如果你是在2019年7月1日之前激活了 MFA 服务器的现有客户，则现在可以下载最新版本的 MFA 服务器（8.0.2 版）。 在这个新版本中，我们：

- 修复了这样的问题：当 Azure AD 同步将用户从禁用更改为启用时，会向用户发送电子邮件。

- 修复了一个问题，使客户能够成功升级，同时继续使用标记功能。

- 添加了科索沃（+ 383）国家/地区代码。

- 向 Multifactorauthsvc.log 添加了一次性跳过审核日志记录。

- 提高了 Web 服务 SDK 的性能。

- 修复了其他次要错误。

从2019年7月1日开始，Microsoft 停止为新部署提供 MFA 服务器。 需要多重身份验证的新客户应使用基于云的 Azure 多重身份验证。 有关详细信息，请参阅[规划基于云的 Azure 多重身份验证部署](https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted)。

---
