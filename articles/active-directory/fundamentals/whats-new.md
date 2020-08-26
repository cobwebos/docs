---
title: 新增功能 发行说明 - Azure Active Directory | Microsoft Docs
description: 了解 Azure Active Directory 中的新增功能，例如最新的发行说明、已知问题、bug 修复、已弃用的功能和即将应用的更改。
services: active-directory
author: ajburnle
manager: daveba
featureFlags:
- clicktale
ms.assetid: 06a149f7-4aa1-4fb9-a8ec-ac2633b031fb
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 06/30/2020
ms.author: ajburnle
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6694043f083de30f3bca10051fc7012ef1471c37
ms.sourcegitcommit: fbb66a827e67440b9d05049decfb434257e56d2d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/05/2020
ms.locfileid: "87798811"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制此 URL (`https://docs.microsoft.com/api/search/rss?search=%22Release+notes+-+Azure+Active+Directory%22&locale=en-us`) 并粘贴到 ![RSS 源阅读器图标](./media/whats-new/feed-icon-16x16.png) 订阅源阅读器中，获取有关何时重新访问此页以获得更新的通知。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="july-2020"></a>2020 年 7 月

### <a name="as-an-it-admin-i-want-to-target-client-apps-using-conditional-access"></a>作为 IT 管理员，我想要使用条件性访问来面向客户端应用

**类型：** 更改计划   
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
由于条件访问中的客户端应用条件的 GA 版本，默认情况下，新策略将默认应用于所有客户端应用程序，包括旧身份验证客户端。 现有策略将保持不变，但会从现有策略中删除 *"配置是/否*" 切换，以便轻松查看策略所应用到的客户端应用。 

创建新策略时，请确保排除仍使用旧身份验证的用户和服务帐户;否则，它们将被阻止。 [了解详细信息](https://aka.ms/caclientapps)。
 
---

### <a name="upcoming-scim-compliance-fixes"></a>即将推出的 SCIM 合规性修复

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务利用 SCIM 标准来与应用程序集成。 我们 SCIM 标准的实现正在不断发展，我们希望对我们如何执行修补程序操作以及如何在资源上设置属性 "活动" 的行为进行更改。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-scim-compatibility)。
 
---

### <a name="group-owner-setting-on-azure-admin-portal-will-be-changed"></a>Azure 管理门户上的组所有者设置将更改

**类型：** 更改计划  
**服务类别：** 组管理  
**产品功能：** 协作

"组常规设置" 页上的 "所有者设置" 可以配置为在 Azure 管理门户和访问面板中将所有者分配权限限制为一组有限的用户。 我们很快就可以在这两个 UX 门户上分配组所有者特权，但也可以在后端强制执行策略，以跨终结点（例如 PowerShell 和 Microsoft Graph）提供一致的行为。 

我们将开始为不使用它的客户禁用当前设置，并将在接下来的几个月中提供一个选项来向用户提供组所有者权限。 有关更新组设置的指导，请参阅使用[Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-settings-azure-portal?context=azure/active-directory/users-groups-roles/context/ugr-context)编辑组信息。

---

### <a name="azure-active-directory-registration-service-is-ending-support-for-tls-10-and-11"></a>Azure Active Directory 注册服务正在结束 TLS 1.0 和1.1 的支持

**类型：** 更改计划  
**服务类别：** 设备注册和管理  
**产品功能：** 平台

传输层安全性 (TLS) 1.2 和更新服务器和客户端不久将与 Azure Active Directory 设备注册服务通信。 支持 TLS 1.0 和1.1 以便与 Azure AD 设备注册 service 进行通信：
- 在2020年8月31日，在所有主权云中 (GCC 高、DoD 等 ) 
- 所有商业云中的2020年10月30日

[详细了解](https://docs.microsoft.com/azure/active-directory/devices/reference-device-registration-tls-1-2)Azure AD 注册服务的 TLS 1.2。

---

### <a name="windows-hello-for-business-sign-ins-visible-in-azure-ad-sign-in-logs"></a>Azure AD 登录日志中显示 Windows Hello 企业版登录

**类型：** 已修复  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
Windows Hello 企业版允许最终用户使用手势 (（例如 PIN 或生物识别) ）登录到 Windows 计算机。 Azure AD 管理员可能希望将 Windows Hello 企业版登录从其他 Windows 登录区，作为组织的无密码身份验证过程的一部分。 

管理员现在可以通过在 Azure 门户中的 Azure AD 登录 "边栏选项卡中检查 Windows 登录事件的" 身份验证详细信息 "选项卡，来查看 Windows 身份验证是否使用了 windows Hello 企业版。 Windows Hello 企业版身份验证将在 "身份验证方法" 字段中包括 "WindowsHelloForBusiness"。 有关解释登录日志的详细信息，请参阅[登录日志文档](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。
 
---

### <a name="fixes-to-group-deletion-behavior-and-performance-improvements"></a>修复了组删除行为和性能改进

**类型：** 已修复  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
以前，当某个组从 "范围中" 更改为 "超出作用域"，并且管理员在完成更改之前单击了 "重新启动" 时，不会删除组对象。 现在，当组对象超出范围 (禁用、删除、未分配或未通过范围筛选器) 时，会从目标应用程序中删除。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)。
 
---

### <a name="public-preview-admins-can-now-add-custom-content-in-the-email-to-reviewers-when-creating-an-access-review"></a>公共预览版：管理员现在可以将电子邮件中的自定义内容添加到审阅者创建访问评审

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识调控
 
创建新的访问评审后，审阅者将收到一封电子邮件，要求他们完成访问评审。 许多客户要求向电子邮件添加自定义内容（例如联系信息）或其他其他支持内容，以指导审阅者。 

管理员可以在公共预览版中提供，通过在 Azure AD 访问评审的 "高级" 部分添加内容来指定发送给审阅者的电子邮件中的自定义内容。 有关创建访问评审的指南，请参阅[在 Azure AD 访问评审中创建组和应用程序的访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。
 
---

### <a name="authorization-code-flow-for-single-page-apps-available"></a>提供单页面应用的授权代码流

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 开发人员体验
 
由于新的浏览器第三方 cookie 限制（如 Safari ITP），Spa 将必须使用授权代码流，而不是隐式流来维护 SSO，MSAL.js 版本 2. x 现在支持授权代码流。 

Azure 门户有相应的更新，以便你可以将你的 SPA 更新为 "SPA" 类型并使用身份验证代码流。 若要进一步指导，请参阅[使用身份验证代码流登录用户和获取 JAVASCRIPT SPA 中的访问令牌](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)。
 
---

### <a name="azure-ad-application-proxy-now-supports-the-remote-desktop-services-web-client"></a>Azure AD 应用程序代理现在支持远程桌面服务 Web 客户端

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** 访问控制

Azure AD 应用程序代理现在支持 (RDS) Web 客户端的远程桌面服务。 RDS web 客户端允许用户通过任何支持 HTLM5 的浏览器（如 Microsoft Edge、Internet Explorer 11、Google Chrome 等）访问远程桌面基础结构。用户可以与远程应用或桌面进行交互，就像在任何位置使用本地设备一样。 通过使用 Azure AD 应用程序代理，你可以通过为所有类型的丰富客户端应用强制执行预身份验证和条件性访问策略，从而提高 RDS 部署的安全性。 有关指南，请参阅将[远程桌面与 Azure AD 应用程序代理一起发布](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-integrate-with-remote-desktop-services)。
 
---

### <a name="next-generation-azure-ad-b2c-user-flows-in-public-preview"></a>公共预览版中的下一代 Azure AD B2C 用户流

**类型：** 新功能  
**服务类别：** B2C - 用户标识管理  
**产品功能：** B2B/B2C
 
简化的用户流体验提供具有预览功能的功能奇偶校验，并是所有新功能的主页。 用户能够在同一用户流中启用新功能，从而减少了使用每个新功能版本创建多个版本的需要。 最后，新的、用户友好的 UX 可简化用户流的选择和创建。 立即通过[创建用户流](https://docs.microsoft.com/azure/active-directory-b2c/tutorial-create-user-flows)来试用。 

有关用户流的详细信息，请参阅[Azure Active Directory B2C 中的用户流版本](https://docs.microsoft.com/azure/active-directory-b2c/user-flow-versions#:~:text=%20%20%20%20User%20flow%20%20%2caccount.%20Usi%20...%20%201%20more%20rows%20)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---july-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年7月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年7月，我们已在应用程序库中添加了以下55新应用程序，并提供联合身份验证支持：

[击掌](http://www.rmit.com.ar/)， [Appreiz](https://microsoftteams.appreiz.com/)， [Inextor 保管库](https://inexto.com/inexto-suite/inextor)， [Beekast](https://my.beekast.com/)， [Templafy](https://app.templafy.com/)， [Control 塔](https://bpm.tnxcorp.com/sso/microsoft)， [PeterConnects，](https://msteams.peterconnects.com/)[硬币构造云](https://sso.coinsconstructioncloud.com/#login/) [，](https://appfusions.alohacloud.com/auth) [AlohaCloud MT](https://task.teamsmain.medx.im/authorization) [，Cocoom，](https://start.cocoom.com/) [Medxnote，Reflekt](https://reflekt.konsolute.com/login) [，Rever](https://app.reverscore.net/access) [，MyCompanyArchive](https://login.mycompanyarchive.com/) [，GReminders](https://docs.microsoft.com/azure/active-directory/saas-apps/titanfile-tutorial) [，Titanfile](https://docs.microsoft.com/azure/active-directory/saas-apps/wootric-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/kpifire-tutorial) [Wootric，](https://docs.microsoft.com/azure/active-directory/saas-apps/intsights-tutorial) [SolarWinds，Orion](https://app.greminders.com/o365-oauth) [，OpenText](https://docs.microsoft.com/azure/active-directory/saas-apps/datasite-tutorial) [，Datasite，](https://support.solarwinds.com/SuccessCenter/s/orion-platform?language=en_US) [BlogIn、](https://docs.microsoft.com/azure/active-directory/saas-apps/opentext-directory-services-tutorial) [IntSights、](https://docs.microsoft.com/azure/active-directory/saas-apps/textline-tutorial)kpifire [、system.windows.media.textformatting.textline>](https://docs.microsoft.com/azure/active-directory/saas-apps/blogin-tutorial)、[社区 Spark](https://docs.microsoft.com/azure/active-directory/saas-apps/community-spark-tutorial)、 [Chatwork、](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-academy-sso-tutorial) [CloudSign](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudsign-tutorial)、 [C3M Cloud Control](https://docs.microsoft.com/azure/active-directory/saas-apps/c3m-cloud-control-tutorial) [，SmartHR ](https://smart-schoolapp.com/frmLoginForm)， [Chatwork](https://docs.microsoft.com/azure/active-directory/saas-apps/chatwork-tutorial) [NumlyEngage](https://smarthr.jp/)™ [，密歇根](https://docs.microsoft.com/azure/active-directory/saas-apps/numlyengage-tutorial)Data [Hub 单一登录，出口](https://docs.microsoft.com/azure/active-directory/saas-apps/michigan-data-hub-single-sign-on-tutorial)， [SendSafely](https://docs.microsoft.com/azure/active-directory/saas-apps/egress-tutorial)， [Eletive](https://docs.microsoft.com/azure/active-directory/saas-apps/sendsafely-tutorial)，[右手](https://app.eletive.com/)网络安全[ADI，Fyde](https://right-hand.ai/)Enterprise [Authentication，Verme](https://enterprise.fyde.com/)， [Lenses.io](https://docs.microsoft.com/azure/active-directory/saas-apps/verme-tutorial) [，Momenta，Uprise](https://docs.microsoft.com/azure/active-directory/saas-apps/lensesio-tutorial) [，CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/momenta-tutorial) [，](https://app.inspiresoftware.com/)TellMe Identity [Orchestrator SAML 连接器，Maverics](https://www.strata.io/identity-fabric/)，Smartschool， [Zepto](https://app.uprise.co/sign-in)， [Q](https://q.moduleq.com/login)timekeeping， [CloudCords](https://docs.microsoft.com/azure/active-directory/saas-apps/cloudcords-tutorial)Studi.ly， [TellMe Bot](https://tellme365liteweb.azurewebsites.net/) [Trackplan，Skedda](https://studi.ly/) [，WhosOnLocation，](https://user.zepto-ai.com/signin) [Coggle，](https://docs.microsoft.com/azure/active-directory/saas-apps/skedda-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/whos-on-location-tutorial)) [ (，](https://docs.microsoft.com/azure/active-directory/saas-apps/coggle-tutorial) [Kemp LoadMaster](https://kemptechnologies.com/cloud-load-balancer/)， [BrowserStack 单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/browserstack-single-sign-on-tutorial) [Trackplan](http://www.trackplanfm.com/)

你还可以从此处查找所有应用程序的文档https://aka.ms/AppsTutorial

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息https://aka.ms/AzureADAppRequest

---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---july-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年7月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

你现在可以自动创建、更新和删除新集成的应用[LinkedIn 学习](https://docs.microsoft.com/azure/active-directory/saas-apps/linkedin-learning-provisioning-tutorial)的用户帐户。

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="view-role-assignments-across-all-scopes-and-ability-to-download-them-to-a-csv-file"></a>查看所有范围内的角色分配，并将其下载到 csv 文件

**类型：** 已更改的功能  
**服务类别：** RBAC  
**产品功能：** 访问控制
 
你现在可以在 Azure AD 门户的 "角色和管理员" 选项卡中查看角色的所有作用域中的角色分配。 你还可以将每个角色的角色分配下载到 CSV 文件中。 有关查看和添加角色分配的指导，请参阅[在 Azure Active Directory 中查看和分配管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-manage-roles-portal)。
 
---

### <a name="azure-multi-factor-authentication-software-development-azure-mfa-sdk-deprecation"></a>Azure 多重身份验证软件开发 (Azure MFA SDK) 弃用

**类型：** 已弃用  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
Azure 多重身份验证软件开发 (Azure MFA SDK) 已于2018年11月14日结束，如11月 2017 11 日公布。 Microsoft 将关闭 SDK 服务，在2020年9月30日生效。 对 SDK 的任何调用都将失败。

如果你的组织使用的是 Azure MFA SDK，则需要在2020年9月30日之前迁移：
- 用于 MIM 的 Azure MFA SDK：如果将 SDK 与 MIM 一起使用，则应迁移到 Azure MFA 服务器，并按照这些[说明](https://docs.microsoft.com/microsoft-identity-manager/working-with-mfaserver-for-mim)激活 Privileged Access Management () PAM。   
- 适用于自定义应用的 Azure MFA SDK：考虑将你的应用集成到 Azure AD 并使用条件性访问来强制执行 MFA。 若要开始，请查看此[页](https://docs.microsoft.com/azure/active-directory/manage-apps/plan-an-application-integration)。 

---

## <a name="june-2020"></a>2020 年 6 月 

### <a name="user-risk-condition-in-conditional-access-policy"></a>条件访问策略中的用户风险条件

**类型：** 更改计划  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 

Azure AD 条件性访问策略中的用户风险支持，可创建多个基于风险的用户策略。 不同的用户和应用可能需要不同的最低用户风险级别。 根据用户风险，你可以创建策略来阻止访问、需要多重身份验证、安全密码更改或重定向到 Microsoft Cloud App Security 来强制执行会话策略，如其他审核。

用户风险条件需要 Azure AD Premium P2，因为它使用的是 P2 产品/服务。 有关条件性访问的详细信息，请参阅[Azure AD 条件访问文档](https://docs.microsoft.com/azure/active-directory/conditional-access/)。

---

### <a name="saml-sso-now-supports-apps-that-require-spnamequalifier-to-be-set-when-requested"></a>SAML SSO 现在支持需要在请求时设置 SPNameQualifier 的应用

**类型：** 已修复  
**服务类别：** 企业应用  
**产品功能：** SSO
 
某些 SAML 应用程序需要在请求时在断言主题中返回 SPNameQualifier。 现在 Azure AD 在请求 NameID 策略中请求 SPNameQualifier 时正确响应。 这也适用于 SP 发起的登录，并将在 IdP 发起的登录。  若要详细了解 Azure Active Directory 中的 SAML 协议，请参阅[单一登录 SAML 协议](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol)。

---

### <a name="azure-ad-b2b-collaboration-supports-inviting-msa-and-google-users-in-azure-government-tenants"></a>Azure AD B2B 协作支持邀请 MSA 和 Azure 政府租户中的 Google 用户

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

使用 B2B 协作功能的 Azure 政府租户现在可以邀请具有 Microsoft 或 Google 帐户的用户。 若要查明你的租户是否可以使用这些功能，请按照[如何判断我的 AZURE 美国政府租户中是否提供了 B2B 协作中](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)的说明进行操作？

 
---
 
### <a name="user-object-in-ms-graph-v1-now-includes-externaluserstate-and-externaluserstatechangeddatetime-properties"></a>MS Graph v1 中的用户对象现在包含 externalUserState 和 externalUserStateChangedDateTime 属性

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 

"ExternalUserState" 和 "externalUserStateChangedDateTime" 属性可用于查找未接受其邀请的受邀 B2B 来宾，还可用于生成自动化，如删除在数天后未接受其邀请的用户。 现在，MS Graph v1 中提供了这些属性。 有关使用这些属性的指南，请参阅[用户资源类型](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0)。
 
---

### <a name="manage-authentication-sessions-in-azure-ad-conditional-access-is-now-generally-available"></a>管理身份验证会话 Azure AD 的条件访问现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护
 
通过身份验证会话管理功能，你可以配置用户需要提供登录凭据的频率，以及在关闭和重新打开浏览器后是否需要提供凭据，以在你的环境中提供更高的安全性和灵活性。
 
此外，身份验证会话管理仅适用于 Azure AD 联接、混合 Azure AD 联接和 Azure AD 注册设备上的第一因素身份验证。 现在，身份验证会话管理也适用于 MFA。 有关详细信息，请参阅[使用条件访问配置身份验证会话管理](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-session-lifetime)。

---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---june-2020"></a>Azure AD 应用程序库中提供了新的联合应用程序-2020 年6月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年6月，我们已在应用程序库中添加了以下29个新应用程序，并提供联合身份验证支持：

[Shopify Plus](https://docs.microsoft.com/azure/active-directory/saas-apps/shopify-plus-tutorial)， [Ekarda](https://docs.microsoft.com/azure/active-directory/saas-apps/ekarda-tutorial)， [MailGates](https://docs.microsoft.com/azure/active-directory/saas-apps/mailgates-tutorial)， [BullseyeTDP](https://docs.microsoft.com/azure/active-directory/saas-apps/bullseyetdp-tutorial)， [Raketa](https://docs.microsoft.com/azure/active-directory/saas-apps/raketa-tutorial)， [Segment](https://docs.microsoft.com/azure/active-directory/saas-apps/segment-tutorial)， [Ai 审计员](https://www.mindbridge.ai/products/ai-auditor/)， [Pobuca Connect](https://app.pobu.ca/)， [Proto.io](https://docs.microsoft.com/azure/active-directory/saas-apps/proto.io-tutorial)，[网关守卫](https://www.gatekeeperhq.com/)，[中心规划](https://docs.microsoft.com/azure/active-directory/saas-apps/hub-planner-tutorial)人员， [Ansira-合作伙伴走向市场工具箱](https://ansira.com/technology/channel-engagement)， [IBM 数字业务自动化，适用于云](https://docs.microsoft.com/azure/active-directory/saas-apps/ibm-digital-business-automation-on-cloud-tutorial)， [Kisi 物理安全性](https://docs.microsoft.com/azure/active-directory/saas-apps/kisi-physical-security-tutorial)， [ViewpointOne](https://team.viewpoint.com/)， [IntelligenceBank](https://docs.microsoft.com/azure/active-directory/saas-apps/intelligencebank-tutorial)， [pymetrics](https://docs.microsoft.com/azure/active-directory/saas-apps/pymetrics-tutorial)，[零](https://www.teamzero.com/)， [InStation](https://instation.invillia.com/)， [edX for Business SAML 2.0 集成](https://docs.microsoft.com/azure/active-directory/saas-apps/edx-for-business-saml-integration-tutorial)， [MOOC Office 365](https://mooc.office365-training.com/en/)， [SmartKargo](https://docs.microsoft.com/azure/active-directory/saas-apps/smartkargo-tutorial)， [PKIsigning 平台](https://platform.pkisigning.nl/)， [SiteIntel](https://docs.microsoft.com/azure/active-directory/saas-apps/siteintel-tutorial)，[字段 iD](https://docs.microsoft.com/azure/active-directory/saas-apps/field-id-tutorial)，[课程 saml](https://docs.microsoft.com/azure/active-directory/saas-apps/curricula-saml-tutorial)，Perforce [Helix](https://docs.microsoft.com/azure/active-directory/saas-apps/perforce-helix-core-tutorial) [，Helix](https://cloud.metacompliance.com/) [SSH](https://smallstep.com/sso-ssh/)  

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial 。 若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息： https://aka.ms/AzureADAppRequest 。

---

### <a name="api-connectors-for-external-identities-self-service-sign-up-are-now-in-public-preview"></a>用于外部标识的 API 连接器现在提供公共预览版

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
使用外部标识 API 连接器，可以利用 web Api 将自助注册与外部云系统集成。 这意味着，现在可以将 web Api 作为注册流中的特定步骤调用，以触发基于云的自定义工作流。 例如，可以使用 API 连接器执行以下操作：

- 与自定义批准工作流集成。
- 执行标识校对
- 验证用户输入数据
- 覆盖用户属性
- 运行自定义业务逻辑

有关 API 连接器的所有可能的体验的详细信息，请参阅[使用 api 连接器自定义和扩展自助注册](https://docs.microsoft.com/azure/active-directory/b2b/api-connectors-overview)，或[自定义使用 web API 集成的外部标识自助服务注册](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/customize-external-identities-self-service-sign-up-with-web-api/ba-p/1257364#.XvNz2fImuQg.linkedin)。
 
---

### <a name="provision-on-demand-and-get-users-into-your-apps-in-seconds"></a>按需预配，并在几秒钟内使用户进入你的应用

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务当前以循环方式进行操作。 此服务每40分钟运行一次。 按[需预配功能](https://aka.ms/provisionondemand)允许您选择用户并在数秒内进行设置。 此功能可让你快速排查预配问题，无需重新启动即可强制开始预配周期。 
 
---

### <a name="new-permission-for-using-azure-ad-entitlement-management-in-graph"></a>在 Graph 中使用 Azure AD 权限管理的新权限

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 权利管理
 
新的委托权限 EntitlementManagement。现在，可以在 Microsoft Graph beta 版中与授权管理 API 一起使用。 若要了解有关可用 Api 的详细信息，请参阅使用[Azure AD 的权利管理 API](https://docs.microsoft.com/graph/api/resources/entitlementmanagement-root?view=graph-rest-beta)。

---

### <a name="identity-protection-apis-available-in-v10"></a>在1.0 版中提供 Identity Protection Api

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护
 
RiskyUsers 和 riskDetections Microsoft Graph Api 现已正式发布。 现在，在 v1.0 终结点上提供了它们，我们邀请你在生产环境中使用它们。 有关详细信息，请查看[Microsoft Graph 文档](https://docs.microsoft.com/graph/api/resources/identityprotectionroot?view=graph-rest-1.0)。
 
---

### <a name="sensitivity-labels-to-apply-policies-to-microsoft-365-groups-is-now-generally-available"></a>将策略应用到 Microsoft 365 组的敏感度标签现已正式发布

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作
 

你现在可以创建敏感度标签，并使用标签设置将策略应用到 Microsoft 365 组，包括隐私 (公用或专用) 和外部用户访问策略。 你可以创建一个包含隐私策略的标签，并使用外部用户访问策略不允许添加来宾用户。 当用户将此标签应用于组时，该组将为 "专用"，并且不允许向该组添加来宾用户。 

敏感度标签非常重要，可保护你的关键业务数据，并使你能够以相容且安全的方式管理规模较高的组。 有关使用敏感度标签的指南，请参阅[Azure Active Directory (preview) 中的将敏感度标签分配给 Office 365 组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-assign-sensitivity-labels)。
 
---

### <a name="updates-to-support-for-microsoft-identity-manager-for-azure-ad-premium-customers"></a>更新以支持 Azure AD Premium 客户 Microsoft Identity Manager

**类型：** 已更改的功能  
**服务类别：** Microsoft Identity Manager  
**产品功能：** 标识生命周期管理
 
Azure 支持现可用于 Microsoft Identity Manager 2016 的 Azure AD 集成组件，Microsoft Identity Manager 2016 的扩展支持结束。 [使用 Microsoft Identity Manager 为 Azure AD Premium 客户提供更多有关支持更新的](https://docs.microsoft.com/microsoft-identity-manager/support-update-for-azure-active-directory-premium-customers)信息。

---

### <a name="the-use-of-group-membership-conditions-in-sso-claims-configuration-is-increased"></a>增加 SSO 声明配置中组成员条件的使用

**类型：** 已更改的功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
以前，根据任何单个应用程序配置中的组成员条件更改声明时，可以使用的组数限制为10个。 SSO 声明配置中的组成员条件的使用现在已增加到最多50组。 有关如何配置声明的详细信息，请参阅[企业应用程序 SSO 声明配置](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#emitting-claims-based-on-conditions)。 

---

### <a name="enabling-basic-formatting-on-the-sign-in-page-text-component-in-company-branding"></a>在公司品牌的登录页文本组件上启用基本格式设置。

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
已更新 Azure AD/Microsoft 365 登录体验的公司品牌功能，以允许客户添加超链接和简单格式设置，包括粗体、下划线和斜体。 有关使用此功能的指南，请参阅[将品牌添加到组织的 Azure Active Directory 登录页](https://docs.microsoft.com/azure/active-directory/fundamentals/customize-branding)。

---

### <a name="provisioning-performance-improvements"></a>预配性能改进

**类型：** 已更改的功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
已更新预配服务，以缩短[增量周期](https://docs.microsoft.com/azure/active-directory/app-provisioning/how-provisioning-works#incremental-cycles)的完成时间。 这意味着将用户和组预配到其应用程序中的速度比以前更快。 6/10/2020 之后创建的所有新预配作业都将自动受益于性能改进。 在6/10/2020 之前配置为预配的任何应用程序都需要在6/10/2020 后重启一次，以便充分利用性能改进。 

---

### <a name="announcing-the-deprecation-of-adal-and-ms-graph-parity"></a>宣布弃用 ADAL 和 MS Graph 奇偶校验

**类型：** 已弃用  
**服务类别：** N/A  
**产品功能：** 设备生命周期管理

现在，Microsoft 身份验证库 (MSAL) 提供，我们将不再向 Azure Active Directory 身份验证库添加新功能 (ADAL) 并将于2022年6月30日结束安全修补程序。 有关如何迁移到 MSAL 的详细信息，请参阅将[应用程序迁移到 Microsoft 身份验证库 (MSAL) ](https://docs.microsoft.com/azure/active-directory/develop/msal-migration)。

此外，我们还完成了通过 MS Graph 提供所有 Azure AD Graph 功能的工作。 因此，Azure AD Graph Api 在2022年6月30日之前仅接收到 bug 修复和安全修补程序。 有关详细信息，请参阅[更新应用程序以使用 Microsoft 身份验证库和 MICROSOFT GRAPH API](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/update-your-applications-to-use-microsoft-authentication-library/ba-p/1257363)
 
---
 
## <a name="may-2020"></a>2020 年 5 月

### <a name="retirement-of-properties-in-signins-riskyusers-and-riskdetections-apis"></a>停用找、riskyUsers 和 riskDetections Api 中的属性

**类型：** 更改计划  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

目前，枚举类型用于表示预览版) 中 riskDetections API 和 riskyUserHistoryItem (中的 riskType 属性。 枚举类型还用于找 API 中的 riskEventTypes 属性。 接下来，我们将这些属性表示为字符串。 

客户应转换为 beta riskDetections 和 riskyUserHistoryItem API 中的 riskEventType 属性，并将 beta 找 API 中的属性 riskEventTypes_v2 为2020年9月9日。 此时，我们将停用当前的 riskType 和 riskEventTypes 属性。 有关详细信息，请参阅[Microsoft Graph 上的风险事件属性和 Identity Protection api 的更改](https://developer.microsoft.com/graph/blogs/changes-to-risk-event-properties-and-identity-protection-apis-on-microsoft-graph/)。

--- 

### <a name="deprecation-of-riskeventtypes-property-in-signins-v10-api-on-microsoft-graph"></a>Microsoft Graph 上的找 v1.0 API 中弃用了 riskEventTypes 属性

**类型：** 更改计划  
**服务类别：** 报表  
**产品功能：** 标识安全和保护

枚举类型将在2020年9月 Microsoft Graph 表示风险事件属性时转换为字符串类型。 除了影响预览版 Api 以外，此更改还会影响生产中的找 API。

我们引入了新的 riskEventsTypes_v2 (string) 属性设置为找 1.0 API。 在2022年6月11日，我们将根据我们的 Microsoft Graph 弃用政策，停用当前 riskEventTypes (枚举) 属性。 客户应在2022年6月11日之前过渡到 v1.0 找 API 中的 riskEventTypes_v2 属性。 有关详细信息，请参阅[Microsoft Graph 上找 V1.0 API 中的弃用 riskEventTypes 属性](https://developer.microsoft.com/graph/blogs/deprecation-of-riskeventtypes-property-in-signins-v1-0-api-on-microsoft-graph//)。

--- 

### <a name="upcoming-changes-to-mfa-email-notifications"></a>MFA 电子邮件通知即将更改

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 

我们将对云 MFA 的电子邮件通知进行以下更改：

电子邮件通知将从以下地址发送： azure-noreply@microsoft.com 和 msonlineservicesteam@microsoftonline.com 。 我们正在更新欺诈警报电子邮件的内容，以更好地指示取消阻止使用所需的步骤。

---

### <a name="new-self-service-sign-up-for-users-in-federated-domains-who-cant-access-microsoft-teams-because-they-arent-synced-to-azure-active-directory"></a>新的自助服务注册无法访问 Microsoft 团队的联合域中的用户，因为这些用户未同步到 Azure Active Directory。

**类型：** 更改计划  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 

目前，位于 Azure AD、但未同步到租户中的域中的用户无法访问团队。 从6月结束，这项新功能将通过扩展现有电子邮件验证的注册功能来实现此目的。 这将允许可登录到联合 IdP 但尚未在 Azure ID 中使用用户对象的用户，以自动创建用户对象并对团队进行身份验证。 用户对象将被标记为 "自助注册"。 这是现有功能的扩展，用于对托管域中的用户进行电子邮件验证自行注册，并可使用同一标志进行控制。 此更改将在以下两个月内完成推出。 [在此处](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-self-service-signup)观看文档更新。
 
---

### <a name="upcoming-fix-the-oidc-discovery-document-for-the-azure-government-cloud-is-being-updated-to-reference-the-correct-graph-endpoints"></a>即将解决： Azure 政府版云的 OIDC 发现文档将更新，以引用正确的关系图终结点。

**类型：** 更改计划  
**服务类别：** 主权云  
**产品功能：** 用户身份验证
 
从6月开始， [Azure 政府云](https://docs.microsoft.com/azure/active-directory/develop/authentication-national-cloud)终结点上的 OIDC 发现文档[Microsoft 标识平台和 OpenID connect 协议](https://docs.microsoft.com/azure/active-directory/develop/v2-protocols-oidc) (login.microsoftonline.us) ，将开始根据提供的租户返回正确的[国家/地区云图形](https://docs.microsoft.com/graph/deployments)终结点 (https://graph.microsoft.us 或 https://dod-graph.microsoft.us) 。  它目前 (graph.microsoft.com) "msgraph_host" 字段提供了不正确的图形端点。  

此 bug 修复将在大约2个月内逐步推出。  

---

### <a name="azure-government-users-will-no-longer-be-able-to-sign-in-on-loginmicrosoftonlinecom"></a>Azure 政府版用户将无法再在 login.microsoftonline.com 上登录。

**键入：** 规划更改  
**服务类别：** 主权云  
**产品功能：** 用户身份验证
 
2018年6月1日，Azure 政府 (AAD) 机构的官方 Azure Active Directory 从更改 https://login-us.microsoftonline.com 为 https://login.microsoftonline.us 。 如果你拥有 Azure 政府租户中的应用程序，则必须更新应用程序以在美国终结点上登录用户。

从5月5日开始，Azure AD 将开始强制终结点更改，阻止 Azure 政府用户使用公共终结点 (microsoftonline.com) 登录到托管在 Azure 政府租户中的应用。 受影响的应用将开始显示错误 "AADSTS900439-USGClientNotSupportedOnPublicEndpoint"。 

在6月2020的所有应用中，将对此更改进行逐步推出。 有关更多详细信息，请参阅[Azure 政府博客文章](https://devblogs.microsoft.com/azuregov/azure-government-aad-authority-endpoint-update/)。

---

### <a name="saml-single-logout-request-now-sends-nameid-in-the-correct-format"></a>SAML 单一注销请求现在发送正确格式的 NameID

**类型：** 已修复  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
当用户单击 "注销" (例如，在 MyApps 门户) 中，Azure AD 会将 SAML 单一注销消息发送到用户会话中处于活动状态的每个应用，并配置一个注销 URL。 这些消息包含持久格式的 NameID。

如果原始 SAML 登录令牌使用不同格式的 NameID (例如，电子邮件/UPN) ，则 SAML 应用程序无法将注销消息中的 NameID 关联到现有会话 (因为这两条消息中使用的 NameIDs 是不同的) ，这会导致 SAML 应用程序和用户保留登录消息。 此修补程序使注销消息与为应用程序配置的 NameID 一致。

---

### <a name="hybrid-identity-administrator-role-is-now-available-with-cloud-provisioning"></a>混合标识管理员角色现可用于云预配

**类型：** 新功能  
**服务类别：** Azure AD 云预配  
**产品功能：** 标识生命周期管理
 
IT 管理员可以开始使用新的 "混合管理员" 角色作为设置 Azure ADConnect 云预配的最小特权角色。 使用此新角色，你不再需要使用全局管理员角色来设置和配置云设置。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/users-groups-roles/roles-delegate-by-task#connect)。
 
---

### <a name="new-federated-apps-available-in-azure-ad-application-gallery---may-2020"></a>Azure AD 应用程序库中提供了新的联合应用-可能为2020

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
在5月2020，我们已在应用程序库中添加了以下36新应用程序，并提供联合身份验证支持：

[Moula](https://moula.com.au/pay/merchants)、 [Surveypal](https://www.surveypal.com/app)、 [Kbot365](https://www.konverso.ai/virtual-assistant-digital-workplace/)、 [TackleBox](http://www.tacklebox.app/)、 [Powell 团队](https://powell-software.com/en/powell-teams-en/)、 [Talentsoft 助手](https://msteams.talent-soft.com/)、 [ASC 记录见解](https://teams.asc-recording.app/product)、 [GO1](https://www.go1.com/)、 [B-已接洽](https://b-engaged.se/)、 [Competella 联系人中心工作组](http://www.competella.com/)、 [Asite](http://www.asite.com/)， [ImageSoft 标识](https://identity.imagesoftinc.com/)， [My IBISWorld](https://identity.imagesoftinc.com/)， [insuite](https://docs.microsoft.com/azure/active-directory/saas-apps/insuite-tutorial)，[更改流程管理](https://docs.microsoft.com/azure/active-directory/saas-apps/change-process-management-tutorial)， [Cyara CX 保障平台](https://docs.microsoft.com/azure/active-directory/saas-apps/cyara-cx-assurance-platform-tutorial)，[智能全局调控](https://docs.microsoft.com/azure/active-directory/saas-apps/smart-global-governance-tutorial) [，Prezi，MAPBOX](https://docs.microsoft.com/azure/active-directory/saas-apps/prezi-tutorial) [，Datava](https://docs.microsoft.com/azure/active-directory/saas-apps/mapbox-tutorial)[企业服务平台](https://docs.microsoft.com/azure/active-directory/saas-apps/datava-enterprise-service-platform-tutorial) [，古怪，](https://docs.microsoft.com/azure/active-directory/saas-apps/whimsical-tutorial)Trelica [，EasySSO for](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-confluence-tutorial)Confluence [，EasySSO](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bitbucket-tutorial)，BitBucket [，](https://docs.microsoft.com/azure/active-directory/saas-apps/sharevault-tutorial) [EasySSO，](https://docs.microsoft.com/azure/active-directory/saas-apps/humanage-tutorial) [Bamboo，](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-bamboo-tutorial)Torii [，Axiad，Humanage](https://docs.microsoft.com/azure/active-directory/saas-apps/axiad-cloud-tutorial)， [ColorTokens ZTNA](https://docs.microsoft.com/azure/active-directory/saas-apps/colortokens-ztna-tutorial)ColorTokens，ZTNA [，](https://docs.microsoft.com/azure/active-directory/saas-apps/cch-tagetik-tutorial)[任何家庭 CRM](https://docs.microsoft.com/azure/active-directory/saas-apps/anyone-home-crm-tutorial)， [CCH](https://docs.microsoft.com/azure/active-directory/saas-apps/vyond-tutorial)， [TextExpander](https://docs.microsoft.com/azure/active-directory/saas-apps/textexpander-tutorial) [ice 联系人中心](https://docs.microsoft.com/azure/active-directory/saas-apps/ice-contact-center-tutorial) [Trelica](https://docs.microsoft.com/azure/active-directory/saas-apps/trelica-tutorial) [Torii](https://docs.microsoft.com/azure/active-directory/saas-apps/torii-tutorial) [askSpoke](https://docs.microsoft.com/azure/active-directory/saas-apps/askspoke-tutorial)

你还可以从此处查找所有应用程序的文档 https://aka.ms/AppsTutorial 。

若要在 Azure AD 应用库中列出你的应用程序，请阅读此处的详细信息 https://aka.ms/AzureADAppRequest 。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件性访问的仅限报表模式现已正式发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

[Azure AD 条件性访问的报表模式](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)允许您在不强制实施访问控制的情况下评估策略的结果。 你可以在组织范围内测试仅限报告的策略并了解其影响，使部署更安全且更容易。 在过去的几个月里，我们已了解到仅限报表模式-通过26M 用户已在仅限报告的策略范围内使用。 现在，通过公告，默认情况下，将在仅报告模式下创建新的 Azure AD 条件访问策略。 这意味着你可以监视策略在创建后的影响。 对于使用 MS Graph Api 的用户来说，你也可以[通过编程方式管理仅报告策略](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)。 

---

### <a name="self-service-sign-up-for-guest-users"></a>自助服务注册来宾用户

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
使用 Azure AD 中的外部标识，你可以允许组织外部的人员访问应用和资源，同时让他们使用所需的任何标识进行登录。 与外部用户共享应用时，你可能并不总是提前知道谁需要访问应用。 使用[自助注册](https://docs.microsoft.com/azure/active-directory/b2b/self-service-sign-up-overview)，你可以让来宾用户为你的业务线 (LOB) 应用注册和获取来宾帐户。 可以创建和自定义注册流以支持 Azure AD 和社交标识。 你还可以在注册过程中收集有关用户的其他信息。

---

 ### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件性访问见解和报告工作簿已公开发布

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

[Insights 和报表工作簿](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)为管理员提供了其租户中 Azure AD 条件访问的摘要视图。 通过选择单个策略的功能，管理员可以更好地了解每个策略的作用，并实时监视任何更改。 工作簿流式传输 Azure Monitor 中存储的数据，可在[这些说明](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)后的几分钟内进行设置。 为了使仪表板更容易发现，我们已将它移动到 Azure AD 条件访问菜单内的 "新建见解和报告" 选项卡中。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>用于条件访问的 "策略详细信息" 边栏选项卡提供公共预览

**类型：** 新功能  
**服务类别：** 条件访问  
**产品功能：** 标识安全和保护

新的 "[策略详细信息" 边栏选项卡](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)显示条件访问策略评估过程中满足的分配、条件和控件。 您可以通过在登录详细信息的 "条件访问" 或 "仅报告" 选项卡中选择行来访问该边栏选项卡。

---

### <a name="new-query-capabilities-for-directory-objects-in-microsoft-graph-are-in-public-preview"></a>Microsoft Graph 中的目录对象的新查询功能处于公共预览

**类型：** 新功能  
**服务类别：** MS Graph**产品功能：** 开发人员体验

正在为 Microsoft Graph Directory 对象 Api 引入新功能，从而启用计数、搜索、筛选和排序操作。 这将使开发人员能够快速查询目录对象，而无需解决方法，例如内存中筛选和排序。 在此[博客文章](https://aka.ms/CountFilterMSGraphAAD)中了解详细信息。

我们目前正在公开预览版，查找反馈。 请通过此[brief 调查](https://aka.ms/MsGraphAADSurveyDocs)发送评论。

---

### <a name="configure-saml-based-single-sign-on-using-microsoft-graph-api-beta"></a>使用 Microsoft Graph API (Beta) 配置基于 SAML 的单一登录

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
现在提供了在 Beta 版本中使用 MS Graph Api 创建和配置 Azure AD 库中的应用程序的支持。 如果需要为应用程序的多个实例设置基于 SAML 的单一登录，请使用 Microsoft Graph Api 来[自动配置基于 saml 的单一登录](https://docs.microsoft.com/azure/active-directory/manage-apps/application-saml-sso-configure-api)，从而节省时间。
 
---

### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---may-2020"></a>Azure AD 应用程序库中的新预配连接器-可能为2020

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

* [8x8](https://docs.microsoft.com/azure/active-directory/saas-apps/8x8-provisioning-tutorial)
* [Juno Journey](https://docs.microsoft.com/azure/active-directory/saas-apps/juno-journey-provisioning-tutorial)
* [MediusFlow](https://docs.microsoft.com/azure/active-directory/saas-apps/mediusflow-provisioning-tutorial)
* [New Relic（按组织）](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-by-organization-provisioning-tutorial)
* [Oracle Cloud Infrastructure Console](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-cloud-infratstructure-console-provisioning-tutorial)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

---

### <a name="saml-token-encryption-is-generally-available"></a>SAML 令牌加密已公开发布

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
[SAML 令牌加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)允许将应用程序配置为接收加密的 SAML 断言。 此功能现已在所有云中公开发布。
 
---

### <a name="group-name-claims-in-application-tokens-is-generally-available"></a>应用程序令牌中的组名声明已公开发布

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO
 
令牌中颁发的组声明现在可以仅限于分配给应用程序的组。  当用户是大量组的成员，并且存在超出令牌大小限制的风险时，这一点尤其重要。 利用这项新功能，可[将组名称添加到令牌](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims)中。
 
---

### <a name="workday-writeback-now-supports-setting-work-phone-number-attributes"></a>Workday 写回现在支持设置工作电话号码属性

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
我们增强了 Workday 写回预配应用程序，现在支持工作电话号码和移动电话号码属性的写回。 除了电子邮件和用户名，你现在可以配置 Workday 写回预配应用，以将电话号码值从 Azure AD 传递到 Workday。 有关如何配置电话号码写回的详细信息，请参阅[Workday 写回](https://aka.ms/WorkdayWriteback)应用教程。 

---

### <a name="publisher-verification-preview"></a>出版商验证 (预览) 

**类型：** 新功能  
**服务类别：** 其他  
**产品功能：** 开发人员体验
 
发布者验证 (预览) 可帮助管理员和最终用户了解与 Microsoft 标识平台集成的应用程序开发人员的真实性。 有关详细信息，请参阅[发行者验证 (预览) ](https://docs.microsoft.com/azure/active-directory/develop/publisher-verification-overview)。
 
---

### <a name="authorization-code-flow-for-single-page-apps"></a>单页应用的授权代码流

**键入：** 已更改的功能**服务类别：** 身份验证**产品功能：** 开发人员体验

由于 Safari ITP 等新式浏览器[第三方 cookie 限制](https://docs.microsoft.com/azure/active-directory/develop/reference-third-party-cookies-spas)，spa 将必须使用授权代码流，而不是隐式流来维护 SSO;MSAL.js 版本 2. x 现在支持授权代码流。 作为 Azure 门户的相应更新，以便你可以将你的 SPA 更新为 "spa" 类型并使用身份验证代码流。 有关指南，请参阅[快速入门：使用身份验证代码流在 JAVASCRIPT SPA 中登录用户并获取访问令牌](https://docs.microsoft.com/azure/active-directory/develop/quickstart-v2-javascript-auth-code)。

---

### <a name="improved-filtering-for-devices-is-in-public-preview"></a>改进的设备筛选功能处于公共预览阶段

**键入：** 更改的功能   
**服务类别：** 设备管理**产品功能：** 设备生命周期管理
 
以前，只能使用 "启用" 和 "活动日期" 的筛选器。 现在，你可以[在更多属性上筛选设备列表](https://docs.microsoft.com/azure/active-directory/devices/device-management-azure-portal#device-list-filtering-preview)，包括操作系统类型、联接类型和符合性等。 这些新增功能应简化查找特定设备的工作。

---

### <a name="the-new-app-registrations-experience-for-azure-ad-b2c-is-now-generally-available"></a>Azure AD B2C 的新应用注册体验现已正式发布

**键入：** 更改的功能   
**服务类别：** B2C - 用户标识管理  
**产品功能：** 标识生命周期管理
 
Azure AD B2C 的新应用注册体验现已正式发布。 

以前，您必须使用旧的 "应用程序" 体验从您的应用程序的其余部分分别管理 B2C 使用者的应用程序。 这意味着在 Azure 中，不同的位置可能有不同的应用创建体验。

新体验显示了所有 B2C 应用注册并在同一位置 Azure AD 应用注册，并为管理这些注册提供了一致的方法。 无论你是否需要管理面向客户的应用或有权访问 Microsoft Graph 的应用以编程方式管理 Azure AD B2C 资源，你只需了解一种执行操作的方法。

可以通过导航 Azure AD B2C 服务并选择应用注册边栏选项卡来达到新的体验。 此体验也可从 Azure Active Directory 服务进行访问。

Azure AD B2C 应用注册体验基于适用于 Azure AD 租户的常规[应用注册体验](https://developer.microsoft.com/identity/blogs/new-app-registrations-experience-is-now-generally-available/)，但针对 Azure AD B2C 进行了定制。 旧的 "应用程序" 体验将在将来不再推荐使用。

有关详细信息，请访问[Azure AD B2C 的新应用注册体验](https://aka.ms/b2cappregtraining)。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>组合的安全信息注册体验现已正式发布

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

多重身份验证 (MFA) 和自助密码重置 (SSPR) 的合成注册体验现已正式发布。 这项新的注册体验使用户能够在一个循序渐进的过程中注册 MFA 和 SSPR。 当你为你的组织部署新的体验时，用户可以在更短的时间内注册，并且减少了麻烦。 请查看[此处](https://bit.ly/3etiRyQ)的博客文章。

---

### <a name="continuous-access-evaluation"></a>持续访问评估

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

持续访问评估是一项新的安全功能，可用于在 Azure AD (（如用户帐户删除) ）中发生事件时，在依赖 Azure AD 方上以接近实时的方式执行策略。 我们首先将此功能扩展到团队和 Outlook 客户端。 有关更多详细信息，请阅读我们的[博客](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)和[文档](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)。

---

### <a name="sms-sign-in-firstline-workers-can-sign-in-to-azure-ad-backed-applications-with-their-phone-number-and-no-password"></a>SMS 登录： Firstline 工作人员可通过其电话号码和无密码登录到 Azure AD 支持的应用程序

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 用户身份验证

Office 正在启动一系列适用于非传统组织的移动优先业务应用，以及不使用电子邮件作为其主要通信方法的大型组织中的员工。 这些应用面向前端员工、deskless 工作人员、现场代理或零售员工，这些员工可能无法从其雇主获取电子邮件地址、访问计算机或访问计算机。 此项目将允许这些员工通过输入电话号码并往返代码来登录到业务应用程序。 有关更多详细信息，请参阅我们的[管理员文档](https://docs.microsoft.com/azure/active-directory/authentication/howto-authentication-sms-signin)和[最终用户文档](https://docs.microsoft.com/azure/active-directory/user-help/sms-sign-in-explainer)。

---

### <a name="invite-internal-users-to-use-b2b-collaboration"></a>邀请内部用户使用 B2B 协作

**类型：** 新功能

**服务类别：** B2B

**产品功能：**

我们正在扩展 B2B 邀请功能，以允许邀请现有内部帐户使用 B2B 协作凭据。 此操作可通过将用户对象传递到邀请 API，并将典型参数（如受邀电子邮件地址）传递给。 用户的对象 ID、UPN、组成员身份、应用分配等仍保持不变，但我们将使用 B2B 来使用其主租户凭据进行身份验证，而不是在邀请之前使用的内部凭据进行身份验证。 有关详细信息，请参阅[文档](https://docs.microsoft.com/azure/active-directory/b2b/invite-internal-users)。

---

### <a name="report-only-mode-for-conditional-access-is-now-generally-available"></a>条件性访问的仅限报表模式现已正式发布

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

[Azure AD 条件性访问的报表模式](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-report-only)允许您在不强制实施访问控制的情况下评估策略的结果。 你可以在组织范围内测试仅限报告的策略并了解其影响，使部署更安全且更容易。 在过去的几个月里，我们已了解到仅限报表模式，26M 用户已在仅限报告的策略范围内。 此次公告中，默认情况下，将在 "仅报告" 模式下创建新的 Azure AD 条件访问策略。 这意味着你可以监视策略在创建后的影响。 对于使用 MS Graph Api 的用户，你还可以[通过编程方式管理仅报告策略](https://docs.microsoft.com/graph/api/resources/conditionalaccesspolicy?view=graph-rest-beta)。 

---

### <a name="conditional-access-insights-and-reporting-workbook-is-generally-available"></a>条件性访问见解和报告工作簿已公开发布

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

条件性访问[见解和报表工作簿](https://docs.microsoft.com/azure/active-directory/conditional-access/howto-conditional-access-insights-reporting)为管理员提供了其租户中 Azure AD 条件性访问的摘要视图。 通过选择单个策略的功能，管理员可以更好地了解每个策略的作用，并实时监视任何更改。 工作簿流式传输 Azure Monitor 中存储的数据，可在[这些说明](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics)后的几分钟内进行设置。 为了使仪表板更容易发现，我们已将它移动到 Azure AD 条件访问菜单内的 "新建见解和报告" 选项卡中。

---

### <a name="policy-details-blade-for-conditional-access-is-in-public-preview"></a>用于条件访问的 "策略详细信息" 边栏选项卡提供公共预览

**类型：** 新功能

**服务类别：** 条件访问

**产品功能：** 标识安全和保护

新的 "[策略详细信息" 边栏选项卡](https://docs.microsoft.com/azure/active-directory/conditional-access/troubleshoot-conditional-access)显示条件访问策略评估过程中满足的分配、条件和控件。 您可以通过在登录详细信息的 "**条件访问**" 或 "**仅报告**" 选项卡中选择行来访问该边栏选项卡。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2020"></a>Azure AD 应用库中提供了新的联合应用程序-2020 年4月

**类型：** 新功能

**服务类别：** 企业应用

**产品功能：** 第三方集成

2020年4月，我们已向应用库添加了这31个新应用和联合支持： 

[SincroPool Apps](https://www.sincropool.com/)， [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)， [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)， [LMS365](https://lms.365.systems/)， [iwt-9j-u8d 采购套件](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)， [Lunni](https://lunni.fi/)， [EasySSO For Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)， [Virtual 定型学院](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)， [Meraki 仪表板](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)， [Office 365 搬运工](https://app.mover.io/login)，[发言人参与](https://speakerengage.com/login.php)，[诚实](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)，Ally [，DutyFlow](https://app.dutyflow.nl/) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial) [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)，gr8 人员[，Pendo，HighGround，](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)[协调](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)，Timetabling[解决方案](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)， [SynchroNet 单击](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial) [，，](https://www.made-in-office.com/en/)Fortes， [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) GroupTalk， [GroupTalk](https://recorder.grouptalk.com/)Frontify， [TickitLMS，](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial) [COCO，](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial)MongoDB [TickitLMS Learn](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial)cloud [，Nitro](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial)， [TMWS，](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [，) ](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) ([Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="microsoft-graph-delta-query-support-for-oauth2permissiongrant-available-for-public-preview"></a>针对公共预览版的 oAuth2PermissionGrant Microsoft Graph 增量查询支持

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

OAuth2PermissionGrant 的增量查询适用于公共预览版！ 你现在可以跟踪更改，而不必持续轮询 Microsoft Graph。 [了解详细信息。](https://docs.microsoft.com/graph/api/oAuth2PermissionGrant-delta?view=graph-rest-beta&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-organizational-contact-generally-available"></a>组织联系人的 Microsoft Graph 增量查询支持已正式发布

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

组织联系人的增量查询已正式发布！ 你现在可以跟踪生产应用中的更改，而不必持续轮询 Microsoft Graph。 替换任何通过增量查询持续轮询 orgContact 数据的现有代码，以显著提高性能。 [了解详细信息。](https://docs.microsoft.com/graph/api/orgcontact-delta?view=graph-rest-1.0&tabs=http)

---

### <a name="microsoft-graph-delta-query-support-for-application-generally-available"></a>对应用程序的 Microsoft Graph 增量查询支持已公开发布

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

应用程序的增量查询已公开发布！ 你现在可以跟踪生产应用中的更改，而不必持续轮询 Microsoft Graph。 替换任何现有代码，该代码通过增量查询不断轮询应用程序数据，以显著提高性能。 [了解详细信息。](https://docs.microsoft.com/graph/api/application-delta?view=graph-rest-1.0)

---

### <a name="microsoft-graph-delta-query-support-for-administrative-units-available-for-public-preview"></a>适用于公共预览版的管理单元 Microsoft Graph 增量查询支持

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 管理单元的开发人员体验增量查询可用于公共预览版！ 你现在可以跟踪更改，而不必持续轮询 Microsoft Graph。 [了解详细信息。](https://docs.microsoft.com/graph/api/administrativeunit-delta?view=graph-rest-beta&tabs=http)

---

### <a name="manage-authentication-phone-numbers-and-more-in-new-microsoft-graph-beta-apis"></a>在新的 Microsoft Graph beta Api 中管理身份验证电话号码和其他信息

**类型：** 新功能

**服务类别：** MS Graph

**产品功能：** 开发人员体验

这些 Api 是用于管理用户身份验证方法的重要工具。 现在，你可以以编程方式预先注册和管理用于 MFA 和自助服务密码重置 (SSPR) 的验证器。 这是 Azure MFA、SSPR 和 Microsoft Graph 空间中最常请求的功能之一。 此浪潮中发布的新 Api 使您能够：

- 读取、添加、更新和删除用户的身份验证电话
- 重置用户的密码
- 打开和关闭 SMS-登录

有关详细信息，请参阅[Azure AD authentication 方法 API 概述](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

---

### <a name="administrative-units-public-preview"></a>管理单元公共预览版

**类型：** 新功能

**服务类别：** RBAC

**产品功能：** 访问控制

管理单元允许你授予仅限于你定义的部门、区域或组织的其他部门的管理权限。 你能够使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以更新配置文件信息、重置密码，并仅为其管理单元中的用户分配许可证。

使用管理单元，中心管理员可以：

- 创建管理单元以分散管理资源
- 仅为管理单元中的 Azure AD 用户分配具有管理权限的角色
- 根据需要向用户和组填充管理单元

有关详细信息，请参阅[Azure Active Directory (preview) 中的管理单元管理](https://aka.ms/AdminUnitsDocs)。

---

### <a name="printer-administrator-and-printer-technician-built-in-roles"></a>打印机管理员和打印机技术人员内置角色

**类型：** 新功能

**服务类别：** RBAC

**产品功能：** 访问控制

**打印机管理员**：具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机并管理所有打印机配置的所有方面，包括通用打印连接器设置。 他们可以同意所有委托的打印权限请求。 打印机管理员还有权访问打印报告。 

**打印机技术人员**：具有此角色的用户可以在 Microsoft 通用打印解决方案中注册打印机和管理打印机状态。 他们还可以读取所有连接器信息。 打印机技术人员无法完成的关键任务是在打印机和共享打印机上设置用户权限。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#printer-administrator)

---

### <a name="hybrid-identity-admin-built-in-role"></a>混合标识管理员内置角色

**类型：** 新功能

**服务类别：** RBAC

**产品功能：** 访问控制

充当此角色的用户可以启用、配置和管理与在 Azure AD 中启用混合标识相关的服务及设置。 此角色允许将 Azure AD 配置为三种受支持的身份验证方法之一&#8212;密码哈希同步 (PHS) ，传递身份验证 (PTA) 或联合 (AD FS 或第三方联合身份验证提供程序) # B1，并部署相关的本地基础结构以启用它们。 本地基础结构包括预配和 PTA 代理。 此角色授予在非 Windows 10 设备或非 Windows Server 2016 计算机上启用无缝单一登录 (SSO) 以实现无缝身份验证的能力。 此外，此角色授予了查看登录日志以及访问运行状况和分析以进行监视和故障排除的功能。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

---

### <a name="network-administrator-built-in-role"></a>网络管理员内置角色

**类型：** 新功能

**服务类别：** RBAC

**产品功能：** 访问控制

具有此角色的用户可以查看 Microsoft 提供的网络外围架构建议，这些建议基于用户位置的网络遥测。 Office 365 的网络性能依赖于谨慎的企业客户网络外围架构，这通常是特定于用户的位置。 此角色允许编辑已发现的用户位置以及配置这些位置的网络参数，以方便改善遥测措施和设计建议。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#network-administrator)

---

### <a name="bulk-activity-and-downloads-in-the-azure-ad-admin-portal-experience"></a>Azure AD 管理门户体验中的批量活动和下载

**类型：** 新功能

**服务类别：** 用户管理

**产品功能：** 文件夹

现在，你可以在 Azure AD 中通过上传 Azure AD 管理门户体验中的 CSV 文件来对用户和组执行批量活动。 你可以创建用户、删除用户和邀请来宾用户。 您可以在组中添加和删除成员。

还可以从 Azure AD 管理门户体验中下载 Azure AD 资源的列表。 您可以下载目录中的用户列表、目录中的组列表和特定组的成员。

有关详细信息，请参阅以下内容：

- [创建用户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-add)或[邀请来宾用户](https://docs.microsoft.com/azure/active-directory/b2b/tutorial-bulk-invite)
- [删除用户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-delete)或[还原已删除的用户](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-restore)
- [下载用户列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/users-bulk-download)或[下载组列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download)
- [添加 (导入) 成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)或[删除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)成员或[下载组的成员列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>我的员工委托用户管理

**类型：** 新功能

**服务类别：** 用户管理

**产品功能：**

我的员工可以使用 Firstline 经理（如商店经理）来确保其员工能够访问其 Azure AD 帐户。 组织可将常见任务（如重置密码或更改电话号码）委托给 Firstline Manager，而不是依赖于中心支持人员。 使用我的员工，不能访问帐户的用户只需点击几下鼠标就能重新获得访问权限，而无需任何支持人员或 IT 人员。 有关详细信息，请参阅 "[通过我的员工管理用户" (预览版) ](https://aka.ms/MyStaffAdminDocs)并[向我的员工 (预览) 委托用户管理](https://aka.ms/MyStaffUserDocs)。

---

### <a name="an-upgraded-end-user-experience-in-access-reviews"></a>访问评审中升级的最终用户体验

**类型：** 已更改的功能

**服务类别：** 访问评审

**产品功能：** 标识调控

我们更新了 "我的应用" 门户中 Azure AD 访问评审的审阅者体验。 在4月结束时，登录到 Azure AD 访问评审审阅者体验的审阅者会看到一个横幅，让他们尝试访问我的 Access 中的更新体验。 请注意，更新的访问评审体验提供与当前体验相同的功能，但在新功能的基础上改进了用户界面，使用户能够提高工作效率。 [可在此处了解有关更新的体验的详细信息](https://docs.microsoft.com/azure/active-directory/governance/perform-access-review)。 此公共预览版将持续到2020年7月结束。 7月结束时，未选择预览体验的审阅者会自动定向到 "我的访问权限" 以执行访问评审。 如果希望在现在的访问中将审阅者永久切换到预览体验，[请在此处提出请求](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR5dv-S62099HtxdeKIcgO-NUOFJaRDFDWUpHRk8zQ1BWVU1MMTcyQ1FFUi4u)。

---

### <a name="workday-inbound-user-provisioning-and-writeback-apps-now-support-the-latest-versions-of-workday-web-services-api"></a>Workday 入站用户预配和写回应用现在支持最新版本的 Workday Web Services API

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 

根据客户反馈，我们现已更新企业应用库中的 Workday 入站用户预配和写回应用程序，以支持 (WWS) API 的最新版本的 Workday Web Services。 通过此更改，客户可以指定要在连接字符串中使用的 WWS API 版本。 这使客户能够检索 Workday 版本中可用的更多 HR 属性。 Workday 写回应用现在使用推荐的 Change_Work_Contact_Info Workday web 服务来克服 Maintain_Contact_Info 的限制。

如果在连接字符串中未指定任何版本，则默认情况下，Workday 入站预配应用将继续使用 WWS v 21.1 切换到最新的 Workday Api 进行入站用户预配，客户需要更新连接字符串（如[本教程中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)所述），并更新用于 workday 属性的 xpath，如[workday 属性参考指南](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)中所述。 

若要使用新 API 进行写回，Workday 写回预配应用程序中不需要进行任何更改。 在 Workday 端，确保 Workday Integration System User (ISU) 帐户有权调用 Change_Work_Contact 业务流程，如 "教程" 部分 "[配置业务流程安全策略" 权限](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)中所述。 

我们已经更新了[本教程指南](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial)，以反映新的 API 版本支持。

---

### <a name="users-with-default-access-role-are-now-in-scope-for-provisioning"></a>具有默认访问角色的用户现已在预配范围内

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 标识生命周期管理

在过去，具有默认访问角色的用户已超出预配的范围。 我们已经注意到，客户希望具有此角色的用户在预配范围内提供反馈。 从2020年4月16日起，所有新的预配配置都允许设置具有默认访问角色的用户。 我们将逐步更改现有预配配置的行为，以支持设置具有此角色的用户。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)

---

### <a name="updated-provisioning-ui"></a>已更新的预配 UI

**类型：** 已更改的功能

**服务类别：** 应用预配

**产品功能：** 标识生命周期管理

我们刷新了预配体验，以创建更集中的管理视图。 当你导航到已配置的企业应用程序的 "预配" 边栏选项卡时，你将能够轻松监视预配和管理操作的进度，如启动、停止和重新启动设置。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/app-provisioning/configure-automatic-user-provisioning-portal)

---

### <a name="dynamic-group-rule-validation-is-now-available-for-public-preview"></a>动态组规则验证现可用于公共预览

**类型：** 已更改的功能

**服务类别：** 组管理

**产品功能：** 协作

Azure Active Directory (Azure AD) 现在提供了验证动态组规则的方法。 在 "**验证规则**" 选项卡上，可以对照组成员验证动态规则，以确认规则按预期方式工作。 创建或更新动态组规则时，管理员希望知道用户或设备是否将成为该组的成员。 这有助于评估用户或设备是否满足规则条件，并有助于在不期望成员身份时进行故障排除。

有关详细信息，请参阅[ (预览版) 验证动态组成员身份规则](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)。

---

### <a name="identity-secure-score---security-defaults-and-mfa-improvement-action-updates"></a>标识安全分数-安全性默认值和 MFA 改善操作更新

**类型：** 已更改的功能

**服务类别：** N/A

**产品功能：** 标识安全和保护

**支持 Azure AD 改进操作的安全默认值：** Microsoft 安全分数将更新改进操作以支持[Azure AD 中的安全默认](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)设置，这使你的组织能够更轻松地通过预配置的安全设置来处理常见攻击。 这将影响以下改进操作：

- 确保所有用户都可以完成多重身份验证以实现安全访问
- 需要对管理角色进行 MFA
- 启用策略以阻止旧身份验证
 
**MFA 改进操作更新：** 为了反映企业在应用与业务相关的策略时是否需要确保过的话很安全性，Microsoft 安全分数已消除了三个围绕多重身份验证的改进操作并添加了两个操作。

删除了改进操作：

- 注册多重身份验证的所有用户
- 要求所有用户执行 MFA
- Azure AD 特权角色需要 MFA

添加了改进操作：

- 确保所有用户都可以完成多重身份验证以实现安全访问
- 需要对管理角色进行 MFA

这些新的改进操作要求向你的用户或管理员注册多重身份验证 (MFA) 在你的目录中，并建立适合你的组织需求的正确策略集。 主要目标是具有灵活性，同时确保所有用户和管理员都可以使用多个因素或基于风险的标识验证提示进行身份验证。 这可以采用具有多个策略的形式，这些策略应用作用域内的决策，或设置安全默认值 (于3月16日) ，让 Microsoft 决定何时为 MFA 质询用户。 [阅读有关 Microsoft 安全分数中的新增功能的详细信息](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>B2B 更新中的非托管 Azure Active Directory 帐户，2021年3月

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
**从2021年3月31日开始**，Microsoft 将不再支持通过创建非托管 Azure Active Directory (AZURE AD 用于 B2B 协作方案的) 帐户和租户来兑换邀请。 为此，我们建议你选择加入[电子邮件一次性密码身份验证](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有默认访问角色的用户将处于预配范围内

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
在过去，具有默认访问角色的用户已超出预配的范围。 我们已经注意到，客户希望具有此角色的用户在预配范围内提供反馈。 我们正在努力部署更改，以便所有新的预配配置都允许设置具有默认访问角色的用户。 我们将逐步更改现有预配配置的行为，以支持设置具有此角色的用户。 无需任何客户操作。 完成此更改后，我们会将更新发布到[文档](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 协作将在由世纪互联 (Azure 中国世纪互联) 租户 Microsoft Azure 运营

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能将在由世纪互联 (Azure 中国世纪互联的 Microsoft Azure 运营) 租户中提供，使 Azure 中国世纪互联租户中的用户无缝与其他 Azure 中国世纪互联租户中的用户进行协作。 [详细了解 AZURE AD B2B 协作](https://docs.microsoft.com/azure/active-directory/b2b/)。

---
 
### <a name="azure-ad-b2b-collaboration-invitation-email-redesign"></a>Azure AD B2B 协作邀请电子邮件重新设计

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作邀请服务发送的[电子邮件](https://docs.microsoft.com/azure/active-directory/b2b/invitation-email-elements)将用户邀请到目录，以使邀请信息和用户的后续步骤更清晰。

---

### <a name="homerealmdiscovery-policy-changes-will-appear-in-the-audit-logs"></a>Homerealmdiscovery.aspx 策略更改将显示在审核日志中

**类型：** 已修复  
**服务类别：** 审核  
**产品功能：** 监视和报告
 
我们修复了一个 bug，即审核日志中未包含对[homerealmdiscovery.aspx 策略](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-authentication-for-federated-users-portal)所做的更改。 现在，你将能够查看策略更改的时间和方式，以及更改者。 

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2020"></a>Azure AD 应用库中提供了新的联合应用程序-2020 年3月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年3月向应用程序库添加了这些支持联合身份验证的51新应用： 

[Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)， [Zoho 一](https://docs.microsoft.com/azure/active-directory/saas-apps/zoho-one-china-tutorial)， [PlusPlus](https://test.plusplus.app/auth/login/azuread-outlook/)， [Profit.co SAML 应用](https://docs.microsoft.com/azure/active-directory/saas-apps/profitco-saml-app-tutorial)程序， [IPoint 服务提供商](https://docs.microsoft.com/azure/active-directory/saas-apps/ipoint-service-provider-tutorial)， [contexxt.ai 球](https://contexxt-sphere.com/login)，[按 Invictus](https://docs.microsoft.com/azure/active-directory/saas-apps/wisdom-by-invictus-tutorial)， [Flare 数字告示](https://spark-dev.pixelnebula.com/login)， [Logz.io-云可观察性，适用于工程师](https://docs.microsoft.com/azure/active-directory/saas-apps/logzio-cloud-observability-for-engineers-tutorial)， [SpectrumU](https://docs.microsoft.com/azure/active-directory/saas-apps/spectrumu-tutorial)， [BizzContact](https://bizzcontact.app/)，Elqano [SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/elqano-sso-tutorial) [Campus Café](https://docs.microsoft.com/azure/active-directory/saas-apps/campus-cafe-tutorial)，MarketSignShare， [CrossKnowledge Learning Suite](https://docs.microsoft.com/azure/active-directory/saas-apps/crossknowledge-learning-suite-tutorial)， [Netvision Compas](https://docs.microsoft.com/azure/active-directory/saas-apps/netvision-compas-tutorial)， [FCM](https://docs.microsoft.com/azure/active-directory/saas-apps/fcm-hub-tutorial)，Byggeweb [MarketSignShare](http://www.signshare.com/) [，GoLinks](https://docs.microsoft.com/azure/active-directory/saas-apps/datadog-tutorial) [B2B 用户门户](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-b2b-user-portal-tutorial) [，](https://apps.apple.com/us/app/docia/id529058757)[升降](https://docs.microsoft.com/azure/active-directory/saas-apps/lift-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/golinks-tutorial) [Planview Enterprise One](https://docs.microsoft.com/azure/active-directory/saas-apps/planview-enterprise-one-tutorial) [，Datadog，](https://www.devfinition.com/)Zscaler [，](https://demo.asterapp.io/login)技能[工作流，](https://docs.microsoft.com/azure/active-directory/saas-apps/skills-workflow-tutorial)节点[见解，](https://admin.nodeinsight.com/AADLogin.aspx)IP[平台，](https://docs.microsoft.com/azure/active-directory/saas-apps/ip-platform-tutorial)InVision [，](https://docs.microsoft.com/azure/active-directory/saas-apps/invision-tutorial)Pipedrive [，](https://docs.microsoft.com/azure/active-directory/saas-apps/pipedrive-tutorial)展示[研讨会，](https://app.showcaseworkshop.com/)Greenlight[集成平台，](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-integration-platform-tutorial)Greenlight[相容访问管理，](https://docs.microsoft.com/azure/active-directory/saas-apps/greenlight-compliant-access-management-tutorial)Grok[学习，](https://docs.microsoft.com/azure/active-directory/saas-apps/grok-learning-tutorial)Miradore[联机，](https://login.online.miradore.com/)Khoros[护理，](https://docs.microsoft.com/azure/active-directory/saas-apps/khoros-care-tutorial)AskYourTeam [，](https://docs.microsoft.com/azure/active-directory/saas-apps/askyourteam-tutorial)TruNarrative [，](https://docs.microsoft.com/azure/active-directory/saas-apps/trunarrative-tutorial)Smartwaiver [，](https://www.smartwaiver.com/m/user/sw_login.php?wms_login)Bizagi [Studio for 数字流程自动化，](https://docs.microsoft.com/azure/active-directory/saas-apps/bizagi-studio-for-digital-process-automation-tutorial)insuiteX [，](https://www.insuite.jp/)sybo，Britive， [WhosOffice，](https://docs.microsoft.com/azure/active-directory/saas-apps/whosoffice-tutorial)Kollective [，](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-360-tutorial)Witivio [，](https://portal.kollective.app/login) [Playvox，Korn](https://www.systexsoftware.com.tw/) [，](https://app.witivio.com/)[运送，捕获点](https://docs.microsoft.com/azure/active-directory/saas-apps/e-days-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/code42-tutorial) [Code42，](https://docs.microsoft.com/azure/active-directory/saas-apps/britive-tutorial)， [Playvox](https://my.playvox.com/login) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/catchpoint-tutorial)360

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-b2b-collaboration-available-in-azure-government-tenants"></a>Azure 政府租户中提供 Azure AD B2B 协作

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能现已在一些 Azure 政府租户之间可用。  若要查明你的租户是否能够使用这些功能，请按照[如何判断我的 AZURE 美国政府租户中是否提供了 B2B 协作中](https://docs.microsoft.com/azure/active-directory/b2b/current-limitations#how-can-i-tell-if-b2b-collaboration-is-available-in-my-azure-us-government-tenant)的说明进行操作。

---

### <a name="azure-monitor-integration-for-azure-logs-is-now-available-in-azure-government"></a>Azure 的 Azure Monitor 集成现已在 Azure 政府版中提供

**类型：** 新功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
现在 Azure 政府版中提供了与 Azure AD 日志 Azure Monitor 集成。 可以将 Azure AD 日志 (审核和登录日志路由) 到存储帐户、事件中心和 Log Analytics。 请查看详细的[文档](https://aka.ms/aadlogsinamd)以及用于[报告和监视](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 方案的部署计划。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 政府版中的 Identity Protection 刷新

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

我们很高兴地分享，我们现在已[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs)   在[Microsoft Azure 政府门户](https://portal.azure.us/)中推出刷新后的 Azure AD Identity Protection 体验。 有关详细信息，请参阅我们的[公告博客文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>灾难恢复：下载并存储预配配置

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务提供了一组丰富的配置功能。 客户需要能够保存其配置，以便以后可以引用或回滚到已知的良好版本。 我们已添加了将预配配置下载为 JSON 文件的功能，并在需要时将其上传。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR (自助服务密码重置) 现在需要在由世纪互联 (Azure 中国世纪互联的 Microsoft Azure 中为管理员提供两个入口)  

**类型：** 已更改的功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 标识安全和保护
 
以前的 Microsoft Azure 由世纪互联 (Azure 中国世纪互联) ，使用自助服务密码重置 (SSPR) 来重置其自己的密码，只需要一个 "入口" (质询) 来证明其身份。 在公共和其他国家云中，管理员通常必须在使用 SSPR 时使用两个入口来证明其身份。 但是，因为我们不支持 Azure 中国世纪互联中的短信或电话呼叫，所以管理员允许通过一入口的密码重置。

我们正在创建 Azure 中国世纪互联和公有云之间的 SSPR 功能奇偶校验。 今后，管理员必须在使用 SSPR 时使用两个入口。 将支持 SMS、电话呼叫和验证器应用通知和代码。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密码长度限制为256个字符

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
为了确保 Azure AD 服务的可靠性，用户密码现在的长度限制为256个字符。 密码长度超过此长度的用户将被要求在后续登录时更改其密码，方法是联系管理员或使用自助服务密码重置功能。

此更改已于2020年3月13日在 10AM PST (18:00 UTC) 启用，错误为 AADSTS 50052，InvalidPasswordExceedsMaxLength。 有关更多详细信息，请参阅[重大更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD 登录日志现在可通过 Azure 门户的所有免费租户使用

**类型：** 已更改的功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
从现在开始，具有免费租户的客户可以从 Azure 门户到最多7天的[Azure AD 登录日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。 以前，登录日志仅适用于具有 Azure Active Directory Premium 许可证的客户。 进行此更改后，所有租户都可以通过门户访问这些日志。

> [!NOTE]
> 客户仍需要高级许可证 (Azure Active Directory Premium P1 或 P2) 通过 Microsoft Graph API 和 Azure Monitor 访问登录日志。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>弃用了目录范围内的组选项 Azure 门户上的组常规设置

**类型：** 已弃用  
**服务类别：** 组管理  
**产品功能：** 协作

若要为客户提供一种更灵活的方式来创建最符合需要的目录范围内的组，我们已将 "Azure 门户中的"**组**"" 常规设置 "中的"**目录范围组**"选项替换  >  **General**为[动态组文档](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的链接。 我们已改进文档以提供更多说明，以便管理员可以创建包括或排除来宾用户在内的所有用户组。

---

## <a name="february-2020"></a>2020 年 2 月

### <a name="upcoming-changes-to-custom-controls"></a>自定义控件即将发生的更改

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
我们计划将当前自定义控件预览替换为允许合作伙伴提供的身份验证功能与 Azure Active Directory 管理员和最终用户体验无缝配合使用的方法。 目前，合作伙伴 MFA 解决方案面临以下限制：仅在输入密码后才能工作：在其他关键情况下，这些客户端不充当执行单步身份验证的 MFA;它们不与最终用户或管理凭据管理功能集成。 新实现将允许合作伙伴提供的身份验证因素与内置因素结合使用，以实现关键方案（包括注册、使用、MFA 声明、单步身份验证、报告和日志记录）。 

自定义控件将在预览版中继续支持，直到其公开上市。 此时，我们将向客户的时间迁移到新设计。 由于当前方法的限制，我们不会在新的设计中提供新的设计器。 我们与客户和提供商密切合作，并将在我们更近的时间里传达时间线。 [了解详细信息](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/upcoming-changes-to-custom-controls/ba-p/1144696#)。

---

### <a name="identity-secure-score---mfa-improvement-action-updates"></a>标识安全分数-MFA 改善操作更新

**类型：** 更改计划  
**服务类别：** MFA  
**产品功能：** 标识安全和保护
 
为了反映企业在应用与业务相关的策略时，是否需要确保过的话很安全性，Microsoft 安全分数会删除三个围绕多重身份验证的改进操作， (MFA) ，并添加两个。

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
 
我们听说 Azure AD 域服务客户需要更灵活地选择其实例的性能级别。 从2020年2月1日开始，我们从动态模型转换 (，其中 Azure AD 根据对象计数) 到自选择模型来确定性能和定价层。 现在，客户可以选择与其环境匹配的性能层。 此更改还允许我们启用新方案，如资源林和高级功能，如每日备份。 对象计数现在对所有 Sku 均无限制，但我们将继续为每个层提供对象计数建议。

**无需立即进行客户操作。** 对于现有客户，在2020年2月1日使用的动态层确定了新的默认层。 此更改的结果不会影响定价或性能。 今后，Azure AD DS 客户需要评估性能要求，因为它们的目录大小和工作负荷特征发生变化。 在服务层之间切换将继续进行无中断操作，并且我们将不再自动根据其目录的增长将客户移动到新的层。 此外，将不会有任何价格增加，新定价将与当前计费模型保持一致。 有关详细信息，请参阅[AZURE AD DS sku 文档](https://docs.microsoft.com/azure/active-directory-domain-services/administration-concepts#azure-ad-ds-skus)和[Azure AD 域服务定价页](https://azure.microsoft.com/pricing/details/active-directory-ds/)。

---
 
### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2020"></a>Azure AD 应用库中提供了新的联合应用程序-2020 年2月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
2020年2月，我们已向应用库添加了这31个新应用和联合支持： 

[IamIP 专利平台](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[体验云](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)，[适用于 Azure 的 NS1 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)， [Barracuda 电子邮件安全服务](https://ess.barracudanetworks.com/sso/azure)， [In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [ABa 报告](https://myaba.co.uk/client-access/signin/auth/msad) [Beekeeper Azure AD 数据连接器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)， [Korn 运送评估](https://www.kornferry.com/solutions/kf-digital/kf-assess)， [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)， [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)， [Syxsense](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)， [EAB 导航](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial) [，Relic ](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial)，Thulium，Beesy，MURAL，LavaDo，Ticket Manager [，模板](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)选择[器用于团队，Wakelet](https://links.officeatwork.com/templatechooser-download-teams)， [) ](https://www.beesy.me/index.php/site/login) ([健康支持系统](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)， [Firmex](https://app.mural.co/signup) [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)， [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial)，vdr-fp-xzo，ThingLink，NearpodApp， [WEDO，InvitePeople](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [Thulium](https://admin.thulium.com/login/instance)，Reprints， [Firmex VDR](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial)，，， [Coda](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial)， [NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [InvitePeople](https://invitepeople.com/login) [Wakelet](https://wakelet.com/login) [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial)， [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [ThingLink for Teachers and Schools](https://www.thinglink.com/) [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial)

 
有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---
 
### <a name="new-provisioning-connectors-in-the-azure-ad-application-gallery---february-2020"></a>Azure AD 应用程序库中的新预配连接器-2020 年2月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
现在，可以为这些新集成的应用自动创建、更新和删除用户帐户：

- [Mixpanel](https://docs.microsoft.com/azure/active-directory/saas-apps/mixpanel-provisioning-tutorial)
- [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-provisioning-tutorial)
- [Azure Databricks](https://docs.microsoft.com/azure/active-directory/saas-apps/azure-databricks-scim-connector-provisioning-tutorial)
- [PureCloud by Genesys](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-provisioning-tutorial)
- [Zapier](https://docs.microsoft.com/azure/active-directory/saas-apps/zapier-provisioning-tutorial)

有关如何使用自动化用户帐户预配更好地保护组织的详细信息，请参阅[使用 Azure AD 自动将用户预配到 SaaS 应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning)。

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
 
新的我的帐户最终用户体验将 `https://myaccount.microsoft.com` 在下个月将其 URL 更新到。 可在[我的帐户门户帮助](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中找到有关其提供给最终用户的体验和所有帐户自助服务功能的详细信息。

