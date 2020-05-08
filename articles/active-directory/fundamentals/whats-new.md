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
ms.date: 04/30/2020
ms.author: mimart
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c58c99faa83d6f04ac37d03789202e111f1b06d
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2020
ms.locfileid: "82611154"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>通过复制并粘贴此 URL，获取有关何时通过复制并粘贴此 URL 来`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`重新访问![此页面的通知](./media/whats-new/feed-icon-16x16.png) ：到 rss 源读者图标源读者。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="april-2020"></a>2020 年 4 月

### <a name="combined-security-info-registration-experience-is-now-generally-available"></a>组合的安全信息注册体验现已正式发布

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

多重身份验证（MFA）和自助服务密码重置（SSPR）的组合注册体验现已正式发布。 这项新的注册体验使用户能够在一个循序渐进的过程中注册 MFA 和 SSPR。 当你为你的组织部署新的体验时，用户可以在更短的时间内注册，并且减少了麻烦。 请查看[此处](https://bit.ly/3etiRyQ)的博客文章。

---

### <a name="continuous-access-evaluation"></a>持续访问评估

**类型：** 新功能

**服务类别：** 身份验证（登录）

**产品功能：** 标识安全和保护

持续访问评估是一项新的安全功能，在 Azure AD 中发生事件时（例如，用户帐户删除），可以在使用 Azure AD 访问令牌的信赖方上几乎实时地强制执行策略。 我们首先将此功能扩展到团队和 Outlook 客户端。 有关更多详细信息，请阅读我们的[博客](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/moving-towards-real-time-policy-and-security-enforcement/ba-p/1276933)和[文档](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-continuous-access-evaluation)。

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

[SincroPool Apps](https://www.sincropool.com/)， [SmartDB](https://hibiki.dreamarts.co.jp/smartdb/trial/)， [Float](https://docs.microsoft.com/azure/active-directory/saas-apps/float-tutorial)， [LMS365](https://lms.365.systems/)， [iwt-9j-u8d 采购套件](https://docs.microsoft.com/azure/active-directory/saas-apps/iwt-procurement-suite-tutorial)， [Lunni](https://lunni.fi/)， [EasySSO For Jira](https://docs.microsoft.com/azure/active-directory/saas-apps/easysso-for-jira-tutorial)， [Virtual 定型学院](https://vta.c3p.ca/app/en/openid?authenticate_with=microsoft)， [Meraki 仪表板](https://docs.microsoft.com/azure/active-directory/saas-apps/meraki-dashboard-tutorial)， [Office 365 搬运工](https://app.mover.io/login)，[发言人参与](https://speakerengage.com/login.php)，[诚实](https://docs.microsoft.com/azure/active-directory/saas-apps/honestly-tutorial)， [Ally](https://docs.microsoft.com/azure/active-directory/saas-apps/ally-tutorial) [，DutyFlow](https://app.dutyflow.nl/) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/highground-tutorial) [AlertMedia](https://docs.microsoft.com/azure/active-directory/saas-apps/alertmedia-tutorial)，gr8 人员[，Pendo，HighGround，](https://docs.microsoft.com/azure/active-directory/saas-apps/pendo-tutorial)[协调](https://docs.microsoft.com/azure/active-directory/saas-apps/harmony-tutorial)，Timetabling[解决方案](https://docs.microsoft.com/azure/active-directory/saas-apps/timetabling-solutions-tutorial)， [SynchroNet 单击](https://docs.microsoft.com/azure/active-directory/saas-apps/synchronet-click-tutorial) [，，](https://www.made-in-office.com/en/)Fortes， [Litmus](https://docs.microsoft.com/azure/active-directory/saas-apps/fortes-change-cloud-tutorial)， [GroupTalk](https://docs.microsoft.com/azure/active-directory/saas-apps/tickitlms-learn-tutorial)， [Frontify，TickitLMS](https://recorder.grouptalk.com/) [，COCO](https://hexaware.com/partnerships-and-alliances/digital-transformation-using-microsoft-azure/) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/nitro-productivity-suite-tutorial) [MongoDB cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/mongodb-cloud-tutorial) [，Nitro，TMWS](https://docs.microsoft.com/azure/active-directory/saas-apps/litmus-tutorial) [，，](https://docs.microsoft.com/azure/active-directory/saas-apps/frontify-tutorial) [gr8 People](https://docs.microsoft.com/azure/active-directory/saas-apps/gr8-people-tutorial) [Trend Micro Web Security(TMWS)](https://review.docs.microsoft.com/azure/active-directory/saas-apps/trend-micro-tutorial)

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

这些 Api 是用于管理用户身份验证方法的重要工具。 现在，你可以以编程方式预注册和管理用于 MFA 和自助密码重置的验证器（SSPR）。 这是 Azure MFA、SSPR 和 Microsoft Graph 空间中最常请求的功能之一。 此浪潮中发布的新 Api 使您能够：

- 读取、添加、更新和删除用户的身份验证电话
- 重置用户的密码
- 打开和关闭 SMS-登录

有关详细信息，请参阅[Azure AD authentication 方法 API 概述](https://docs.microsoft.com/graph/api/resources/authenticationmethods-overview?view=graph-rest-beta)。

---

### <a name="administrative-units-public-preview"></a>管理单元公共预览版

**类型：** 新功能

**服务类别：** RBAC

**产品功能：** 访问控制

使用管理单元，你可以授予限制为你定义的部门、区域或其他部门的管理员权限。 可以使用管理单元将权限委派给区域管理员或以粒度级别设置策略。 例如，用户帐户管理员可以更新配置文件信息、重置密码以及仅在其管理单元中为用户分配许可证。

使用管理单元，中心管理员可以：

- 创建管理单元以分散管理资源
- 仅为管理单元中的 Azure AD 用户分配具有管理权限的角色
- 根据需要向用户和组填充管理单元

有关详细信息，请参阅[Azure Active Directory （预览版）中的管理单元管理](https://aka.ms/AdminUnitsDocs)。

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

充当此角色的用户可以启用、配置和管理与在 Azure AD 中启用混合标识相关的服务及设置。 此角色允许将 Azure AD 配置为三种受支持的身份验证方法之一&#8212;密码哈希同步（PHS）、传递身份验证（PTA）或联合身份验证（AD FS 或第三方联合身份验证提供程序） &#8212;并部署相关的本地基础结构以启用它们。 本地基础结构包括预配和 PTA 代理。 此角色授予在非 Windows 10 设备或非 Windows Server 2016 计算机上启用无缝单一登录 (SSO) 以实现无缝身份验证的能力。 此外，此角色授予了查看登录日志以及访问运行状况和分析以进行监视和故障排除的功能。 [了解详细信息。](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles#hybrid-identity-administrator)

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
- 为组[添加（导入）成员](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-import-members)或[删除](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-remove-members)成员或[下载成员列表](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-bulk-download-members)

---

### <a name="my-staff-delegated-user-management"></a>我的员工委托用户管理

**类型：** 新功能

**服务类别：** 用户管理

**产品功能：**

我的员工可以使用 Firstline 经理（如商店经理）来确保其员工能够访问其 Azure AD 帐户。 组织可将常见任务（如重置密码或更改电话号码）委托给 Firstline Manager，而不是依赖于中心支持人员。 使用我的员工，不能访问帐户的用户只需点击几下鼠标就能重新获得访问权限，而无需任何支持人员或 IT 人员。 有关详细信息，请参阅 "[通过我的员工管理用户（预览版）](https://aka.ms/MyStaffAdminDocs) " 和 "[通过我的人员委托用户管理" （预览版）](https://aka.ms/MyStaffUserDocs)。

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

根据客户反馈，我们现已更新企业应用库中的 Workday 入站用户预配和写回应用，以支持最新版本的 Workday Web Services （WWS） API。 通过此更改，客户可以指定要在连接字符串中使用的 WWS API 版本。 这使客户能够检索 Workday 版本中可用的更多 HR 属性。 Workday 写回应用现在使用推荐的 Change_Work_Contact_Info Workday web 服务来克服 Maintain_Contact_Info 的限制。

如果在连接字符串中未指定任何版本，则默认情况下，Workday 入站预配应用将继续使用 WWS v 21.1 切换到最新的 Workday Api 进行入站用户预配，客户需要更新连接字符串（如[本教程中](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#which-workday-apis-does-the-solution-use-to-query-and-update-workday-worker-profiles)所述），并更新用于 workday 属性的 xpath，如[workday 属性参考指南](https://docs.microsoft.com/azure/active-directory/app-provisioning/workday-attribute-reference#xpath-values-for-workday-web-services-wws-api-v30)中所述。 

若要使用新 API 进行写回，Workday 写回预配应用程序中不需要进行任何更改。 在 Workday 端，确保 Workday Integration System 用户（ISU）帐户有权调用 Change_Work_Contact 业务流程，如 "教程" 部分 "[配置业务流程安全策略" 权限](https://docs.microsoft.com/azure/active-directory/saas-apps/workday-inbound-tutorial#configuring-business-process-security-policy-permissions)中所述。 

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

Azure Active Directory （Azure AD）现在提供了验证动态组规则的方法。 在 "**验证规则**" 选项卡上，可以对照组成员验证动态规则，以确认规则按预期方式工作。 创建或更新动态组规则时，管理员希望知道用户或设备是否将成为该组的成员。 这有助于评估用户或设备是否满足规则条件，并有助于在不期望成员身份时进行故障排除。

有关详细信息，请参阅[验证动态组成员身份规则（预览版）](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-rule-validation)。

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

这些新的改进操作要求在目录中注册用户或管理员进行多重身份验证（MFA），并建立符合组织需求的正确策略集。 主要目标是具有灵活性，同时确保所有用户和管理员都可以使用多个因素或基于风险的标识验证提示进行身份验证。 这种形式可以采用具有多个策略的形式，这些策略应用作用域决定，或设置安全默认值（从3月16日起），以使 Microsoft 决定何时为 MFA 质询用户。 [阅读有关 Microsoft 安全分数中的新增功能的详细信息](https://docs.microsoft.com/microsoft-365/security/mtp/microsoft-secure-score?view=o365-worldwide#whats-new)。

---

## <a name="march-2020"></a>2020 年 3 月

### <a name="unmanaged-azure-active-directory-accounts-in-b2b-update-for-march-2021"></a>B2B 更新中的非托管 Azure Active Directory 帐户，2021年3月

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
**从2021年3月31日开始**，Microsoft 将不再支持通过创建用于 B2B 协作方案的非托管 Azure Active Directory （Azure AD）帐户和租户兑换邀请。 为此，我们建议你选择加入[电子邮件一次性密码身份验证](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)。

---

### <a name="users-with-the-default-access-role-will-be-in-scope-for-provisioning"></a>具有默认访问角色的用户将处于预配范围内

**类型：** 更改计划  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
在过去，具有默认访问角色的用户已超出预配的范围。 我们已经注意到，客户希望具有此角色的用户在预配范围内提供反馈。 我们正在努力部署更改，以便所有新的预配配置都允许设置具有默认访问角色的用户。 我们将逐步更改现有预配配置的行为，以支持设置具有此角色的用户。 无需任何客户操作。 完成此更改后，我们会将更新发布到[文档](https://docs.microsoft.com/azure/active-directory/app-provisioning/application-provisioning-config-problem-no-users-provisioned)。

---

### <a name="azure-ad-b2b-collaboration-will-be-available-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet-tenants"></a>Azure AD B2B 协作将在世纪互联运营的 Microsoft Azure （Azure 中国世纪互联）租户中提供

**类型：** 更改计划  
**服务类别：** B2B  
**产品功能：** B2B/B2C
 
Azure AD B2B 协作功能将在世纪互联运营的 Microsoft Azure （Azure 中国世纪互联）租户中提供，使 Azure 中国世纪互联租户中的用户无缝与其他 Azure 中国世纪互联租户中的用户进行协作。 [详细了解 AZURE AD B2B 协作](https://docs.microsoft.com/azure/active-directory/b2b/)。

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
 
现在 Azure 政府版中提供了与 Azure AD 日志 Azure Monitor 集成。 可以将 Azure AD 日志（审核和登录日志）路由到存储帐户、事件中心和 Log Analytics。 请查看详细的[文档](https://aka.ms/aadlogsinamd)以及用于[报告和监视](https://docs.microsoft.com/azure/active-directory/reports-monitoring/plan-monitoring-and-reporting)Azure AD 方案的部署计划。

---

### <a name="identity-protection-refresh-in-azure-government"></a>Azure 政府版中的 Identity Protection 刷新

**类型：** 新功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全和保护

我们很高兴地分享，我们现在已在[Microsoft Azure 政府门户](https://portal.azure.us/)中推出刷新后的[Azure AD Identity Protection](https://aka.ms/IdentityProtectionDocs) 体验。 有关详细信息，请参阅我们的[公告博客文章](https://techcommunity.microsoft.com/t5/public-sector-blog/identity-protection-refresh-in-microsoft-azure-government/ba-p/1223667)。

---

### <a name="disaster-recovery-download-and-store-your-provisioning-configuration"></a>灾难恢复：下载并存储预配配置

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 标识生命周期管理
 
Azure AD 预配服务提供了一组丰富的配置功能。 客户需要能够保存其配置，以便以后可以引用或回滚到已知的良好版本。 我们已添加了将预配配置下载为 JSON 文件的功能，并在需要时将其上传。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/app-provisioning/export-import-provisioning-configuration)。

---
 
### <a name="sspr-self-service-password-reset-now-requires-two-gates-for-admins-in-microsoft-azure-operated-by-21vianet-azure-china-21vianet"></a>SSPR （自助服务密码重置）现在要求在世纪互联（Azure 中国世纪互联）的 Microsoft Azure 中为管理员提供两个入口 

**类型：** 已更改的功能  
**服务类别：** 自助服务密码重置  
**产品功能：** 标识安全和保护
 
以前在世纪互联运营的 Microsoft Azure 中，管理员使用自助服务密码重置（SSPR）来重置其自己的密码，只需要一个 "门" （质询）来证明其身份。 在公共和其他国家云中，管理员通常必须在使用 SSPR 时使用两个入口来证明其身份。 但是，因为我们不支持 Azure 中国世纪互联中的短信或电话呼叫，所以管理员允许通过一入口的密码重置。

我们正在创建 Azure 中国世纪互联和公有云之间的 SSPR 功能奇偶校验。 今后，管理员必须在使用 SSPR 时使用两个入口。 将支持 SMS、电话呼叫和验证器应用通知和代码。 [了解详细信息](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-policy#administrator-reset-policy-differences)。

---

### <a name="password-length-is-limited-to-256-characters"></a>密码长度限制为256个字符

**类型：** 已更改的功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证
 
为了确保 Azure AD 服务的可靠性，用户密码现在的长度限制为256个字符。 密码长度超过此长度的用户将被要求在后续登录时更改其密码，方法是联系管理员或使用自助服务密码重置功能。

此更改已于2020年3月13日（，18:00 UTC）启用，错误为 AADSTS 50052，InvalidPasswordExceedsMaxLength。 有关更多详细信息，请参阅[重大更改通知](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#user-passwords-will-be-restricted-to-256-characters)。

---

### <a name="azure-ad-sign-in-logs-are-now-available-for-all-free-tenants-through-the-azure-portal"></a>Azure AD 登录日志现在可通过 Azure 门户的所有免费租户使用

**类型：** 已更改的功能  
**服务类别：** 报表  
**产品功能：** 监视和报告
 
从现在开始，具有免费租户的客户可以从 Azure 门户到最多7天的[Azure AD 登录日志](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-sign-ins)。 以前，登录日志仅适用于具有 Azure Active Directory Premium 许可证的客户。 进行此更改后，所有租户都可以通过门户访问这些日志。

> [!NOTE]
> 客户仍需要高级许可证（Azure Active Directory Premium P1 或 P2），才能通过 Microsoft Graph API 和 Azure Monitor 访问登录日志。

---

### <a name="deprecation-of-directory-wide-groups-option-from-groups-general-settings-on-azure-portal"></a>弃用了目录范围内的组选项 Azure 门户上的组常规设置

**类型：** 已弃用  
**服务类别：** 组管理  
**产品功能：** 协作

若要为客户提供一种更灵活的方式来创建最符合需要的目录范围内的组，我们已将 "Azure 门户中的"**组** > ""**常规**设置 "中的"**目录范围组**"选项替换为[动态组文档](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)的链接。 我们已改进文档以提供更多说明，以便管理员可以创建包括或排除来宾用户在内的所有用户组。

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

[IamIP 专利平台](https://docs.microsoft.com/azure/active-directory/saas-apps/iamip-patent-platform-tutorial)，[体验云](https://docs.microsoft.com/azure/active-directory/saas-apps/experience-cloud-tutorial)，[适用于 Azure 的 NS1 SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/ns1-sso-azure-tutorial)， [Barracuda 电子邮件安全服务](https://ess.barracudanetworks.com/sso/azure)， [In Case of Crisis - Online Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/in-case-of-crisis-online-portal-tutorial) [ABa 报告](https://myaba.co.uk/client-access/signin/auth/msad) [Beekeeper Azure AD 数据连接器](https://docs.microsoft.com/azure/active-directory/saas-apps/beekeeper-azure-ad-data-connector-tutorial)， [Korn 运送评估](https://www.kornferry.com/solutions/kf-digital/kf-assess)， [Verkada 命令](https://docs.microsoft.com/azure/active-directory/saas-apps/verkada-command-tutorial)， [Splashtop](https://docs.microsoft.com/azure/active-directory/saas-apps/splashtop-tutorial)， [TeamViewer](https://docs.microsoft.com/azure/active-directory/saas-apps/teamviewer-tutorial) Syxsense， [Reprints Desk - Article Galaxy](https://docs.microsoft.com/azure/active-directory/saas-apps/reprints-desk-article-galaxy-tutorial) [EAB](https://docs.microsoft.com/azure/active-directory/saas-apps/eab-navigate-tutorial)，Relic [，Thulium，](https://docs.microsoft.com/azure/active-directory/saas-apps/new-relic-limited-release-tutorial) [Beesy](https://docs.microsoft.com/azure/active-directory/saas-apps/syxsense-tutorial)， [WEDO](https://docs.microsoft.com/azure/active-directory/saas-apps/wedo-tutorial) [MURAL，](https://admin.thulium.com/login/instance)[票证管理器](https://docs.microsoft.com/azure/active-directory/saas-apps/ticketmanager-tutorial)，[团队模板选择器](https://links.officeatwork.com/templatechooser-download-teams) [，LavaDo，](https://www.beesy.me/index.php/site/login)[健康支持系统](https://docs.microsoft.com/azure/active-directory/saas-apps/health-support-system-tutorial)， [Wakelet](https://app.mural.co/signup)， [Hive](https://docs.microsoft.com/azure/active-directory/saas-apps/hive-tutorial) [，](https://docs.microsoft.com/azure/active-directory/saas-apps/coda-tutorial) [Firmex](https://appsource.microsoft.com/product/web-apps/lavaloon.lavado_standard?tab=Overview) [，vdr-fp-xzo，ThingLink](https://wakelet.com/login) [，NearpodApp](https://nearpod.com/signup/?oc=Microsoft&utm_campaign=Microsoft&utm_medium=site&utm_source=product) [，WEDO，](https://www.thinglink.com/) [InvitePeople，](https://invitepeople.com/login) [Reprints，](https://docs.microsoft.com/azure/active-directory/saas-apps/firmex-vdr-tutorial) [BIC Cloud Design](https://docs.microsoft.com/azure/active-directory/saas-apps/bic-cloud-design-tutorial)

 
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
 
新的我的帐户最终用户体验将在下个月将其`https://myaccount.microsoft.com` URL 更新到。 可在[我的帐户门户帮助](https://docs.microsoft.com/azure/active-directory/user-help/my-account-portal-overview)中找到有关其提供给最终用户的体验和所有帐户自助服务功能的详细信息。

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
 
工作区，管理员可以配置筛选器来组织其用户的应用，现在称为集合。 若要详细了解如何在["我的应用" 门户上创建集合](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-collections)，请参阅。

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

[JOSA](https://docs.microsoft.com/azure/active-directory/saas-apps/josa-tutorial)、 [Fastly Edge Cloud](https://docs.microsoft.com/azure/active-directory/saas-apps/fastly-edge-cloud-tutorial)、 [Terraform Enterprise](https://docs.microsoft.com/azure/active-directory/saas-apps/terraform-enterprise-tutorial)、 [Spintr SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/spintr-sso-tutorial)、 [Abibot Netlogistik](https://azuremarketplace.microsoft.com/marketplace/apps/aad.abibotnetlogistik)、 [SkyKick](https://login.skykick.com/login?state=g6Fo2SBTd3M5Q0xBT0JMd3luS2JUTGlYN3pYTE1remJQZnR1c6N0aWTZIDhCSkwzYVQxX2ZMZjNUaWxNUHhCSXg2OHJzbllTcmYto2NpZNkgM0h6czk3ZlF6aFNJV1VNVWQzMmpHeFFDbDRIMkx5VEc&client=3Hzs97fQzhSIWUMUd32jGxQCl4H2LyTG&protocol=oauth2&audience=https://papi.skykick.com&response_type=code&redirect_uri=https://portal.skykick.com/callback&scope=openid%20profile%20offline_access)、 [Upshotly](https://docs.microsoft.com/azure/active-directory/saas-apps/upshotly-tutorial)、 [LeaveBot](https://leavebot.io/#home)、 [DataCamp](https://docs.microsoft.com/azure/active-directory/saas-apps/datacamp-tutorial)、 [TripActions](https://docs.microsoft.com/azure/active-directory/saas-apps/tripactions-tutorial)、 [SMARTWORK](https://www.intumit.com/english/SmartWork.html)、 [DOTCOM](https://docs.microsoft.com/azure/active-directory/saas-apps/dotcom-monitor-tutorial)、SSOGEN、 [Azure AD SSO 网关（适用于 Oracle 电子商务套件） PeopleSoft 和 JDE](https://docs.microsoft.com/azure/active-directory/saas-apps/ssogen-tutorial)，[托管 MyCirqa SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/hosted-mycirqa-sso-tutorial)， [Yuhu 属性管理平台](https://docs.microsoft.com/azure/active-directory/saas-apps/yuhu-property-management-platform-tutorial)， [LumApps](https://sites.lumapps.com/login)， [Upwork](https://www.portaltalk.com/) [Enterprise，](https://docs.microsoft.com/azure/active-directory/saas-apps/upwork-enterprise-tutorial)Talentsoft [，](https://docs.microsoft.com/azure/active-directory/saas-apps/talentsoft-tutorial)SmartDB [for Microsoft 团队，](http://teams.smartdb.jp/login/)PressPage [，](https://docs.microsoft.com/azure/active-directory/saas-apps/presspage-tutorial)ContractSafe [Saml2，](https://docs.microsoft.com/azure/active-directory/saas-apps/contractsafe-saml2-sso-tutorial)Maxient，Helpshift [Maxient Conduct Manager Software](https://docs.microsoft.com/azure/active-directory/saas-apps/maxient-conduct-manager-software-tutorial)，PortalTalk，CoreView， [Helpshift](https://docs.microsoft.com/azure/active-directory/saas-apps/helpshift-tutorial)Squelch，Office365，PingFlow [CoreView](https://portal.coreview.com/)，PrinterLogic [Squelch Cloud Office365 Connector](https://laxmi.squelch.io/login)，Taskize，Sandwai， [PingFlow Authentication](https://app-staging.pingview.io/)EZRentOut，AssetSonar [ PrinterLogic SaaS](https://docs.microsoft.com/azure/active-directory/saas-apps/printerlogic-saas-tutorial)，Akari， [Taskize Connect](https://docs.microsoft.com/azure/active-directory/saas-apps/taskize-connect-tutorial)虚拟[助手 365](https://akari.io/akari-virtual-assistant/) [Sandwai](https://app.sandwai.com/) [EZRentOut](https://docs.microsoft.com/azure/active-directory/saas-apps/ezrentout-tutorial) [AssetSonar](https://docs.microsoft.com/azure/active-directory/saas-apps/assetsonar-tutorial)

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

作为 cookie 的默认默认模型的一部分，Chrome 80 浏览器正在改变它在不使用属性的`SameSite`情况下如何处理 cookie。 不指定属性的`SameSite`任何 cookie 都将被视为已设置为`SameSite=Lax`，这将导致 Chrome 阻止你的应用程序可能依赖的某些跨域 cookie 共享方案。 若要维护较旧的 Chrome 行为，你可以`SameSite=None`使用属性并添加其他`Secure`属性，因此只能通过 HTTPS 连接访问跨站点 cookie。 Chrome 计划在2020年2月4日完成此更改。

我们建议所有开发人员使用本指南来测试其应用程序：

- 将 "**使用安全 Cookie** " 设置的默认值设置为 **"是"**。

- 将**SameSite**属性的默认值设置为 "**无**"。

- 添加一个`SameSite` **Secure**的附加属性。

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

[Airtable](https://docs.microsoft.com/azure/active-directory/saas-apps/airtable-tutorial)， [Hootsuite](https://docs.microsoft.com/azure/active-directory/saas-apps/hootsuite-tutorial)，[成员（BAM）](https://docs.microsoft.com/azure/active-directory/saas-apps/blue-access-for-members-tutorial)、 [Bitly](https://docs.microsoft.com/azure/active-directory/saas-apps/bitly-tutorial)、 [RIVA](https://docs.microsoft.com/azure/active-directory/saas-apps/riva-tutorial)、 [ResLife 门户](https://app.reslifecloud.com/hub5_signin/microsoft_azuread/?g=44BBB1F90915236A97502FF4BE2952CB&c=5&uid=0&ht=2&ref=)、 [NegometrixPortal 单一登录（SSO）](https://docs.microsoft.com/azure/active-directory/saas-apps/negometrixportal-tutorial)、 [TeamsChamp](https://login.microsoftonline.com/551f45da-b68e-4498-a7f5-a6e1efaeb41c/adminconsent?client_id=ca9bbfa4-1316-4c0f-a9ee-1248ac27f8ab&redirect_uri=https://admin.teamschamp.com/api/adminconsent&state=6883c143-cb59-42ee-a53a-bdb5faabf279)、 [Motus](https://docs.microsoft.com/azure/active-directory/saas-apps/motus-tutorial) [、MyAryaka](https://docs.microsoft.com/azure/active-directory/saas-apps/myaryaka-tutorial) [、BlueMail、Beedle](https://loginself1.bluemail.me/)、 [Visma](https://docs.microsoft.com/azure/active-directory/saas-apps/visma-tutorial)、 [OneDesk](https://docs.microsoft.com/azure/active-directory/saas-apps/onedesk-tutorial) [&](https://docs.microsoft.com/azure/active-directory/saas-apps/qmarkets-idea-innovation-management-tutorial)、 [Foko、Qmarkets](https://teams-web.beedle.co/#/)、Netskope、uniFLOW [、Claromentis、](https://docs.microsoft.com/azure/active-directory/saas-apps/foko-retail-tutorial) [Jisc、](https://docs.microsoft.com/azure/active-directory/saas-apps/netskope-user-authentication-tutorial)选民、e4enable、 [、、](https://docs.microsoft.com/azure/active-directory/saas-apps/uniflow-online-tutorial)、、、 [Jisc Student Voter Registration](https://docs.microsoft.com/azure/active-directory/saas-apps/jisc-student-voter-registration-tutorial)、 [、](https://portal.e4enable.com/) [、、](https://docs.microsoft.com/azure/active-directory/saas-apps/claromentis-tutorial)、、、

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
