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
ms.date: 05/23/2019
ms.author: lizross
ms.reviewer: dhanyahk
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 387f1e0bf6461b5e87da1de35e74c5e49c5dc632
ms.sourcegitcommit: 009334a842d08b1c83ee183b5830092e067f4374
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/29/2019
ms.locfileid: "66305531"
---
# <a name="whats-new-in-azure-active-directory"></a>Azure Active Directory 中的新增功能

>获取有关何时通过复制并粘贴此 URL 重新访问此页，进行更新的通知：`https://docs.microsoft.com/api/search/rss?search=%22release+notes+for+azure+AD%22&locale=en-us`到你![RSS 馈送读取器图标](./media/whats-new/feed-icon-16x16.png)馈送读取器。

Azure AD 会不断改进。 为了让大家随时了解最新的开发成果，本文将提供以下方面的信息：

- 最新版本
- 已知问题
- Bug 修复
- 已弃用的功能
- 更改计划

本页面每月更新，请不时回来查看。 如果要查找超过 6 个月的项目，可以在 [Azure Active Directory 新增功能存档](whats-new-archive.md)中找到它们。

---

## <a name="april-2019"></a>2019 年 4 月

### <a name="new-azure-ad-threat-intelligence-detection-is-now-available-in-refreshed-azure-ad-identity-protection"></a>新 Azure AD 威胁智能检测现已推出刷新 Azure AD Identity Protection

**类型：** 新功能  
**服务类别：** Azure AD Identity Protection  
**产品功能：** 标识安全性和保护

Azure AD 威胁智能检测现已推出刷新的 Azure AD Identity Protection。 这一新功能可帮助以指示特定用户的不寻常的或者是与基于 Microsoft 的内部和外部威胁智能的已知的攻击模式保持一致的用户活动。

有关刷新版本的 Azure AD Identity Protection 的详细信息，请参阅[四个主要的 Azure AD Identity Protection 增强功能现在处于公共预览版](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Four-major-Azure-AD-Identity-Protection-enhancements-are-now-in/ba-p/326935)博客和[什么是 Azure Active Directory（刷新） 标识保护？](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-v2) 一文。 有关 Azure AD 威胁智能检测的详细信息，请参阅[Azure Active Directory Identity Protection 风险事件](https://docs.microsoft.com/azure/active-directory/identity-protection/risk-events-reference#azure-ad-threat-intelligence)一文。

---

### <a name="azure-active-directory-azure-ad-entitlement-management-is-now-available-public-preview"></a>Azure Active Directory (Azure AD) 授权管理现已发布的 （公共预览版）

**类型：** 新功能  
**服务类别：** 标识监管  
**产品功能：** 标识监管

Azure AD 授权管理现在在公共预览版中，可帮助客户定义的员工和业务合作伙伴如何请求访问权限、 谁必须批准和他们有权访问多长时间的访问包的管理委托。 访问包可以管理 Azure AD 中的成员资格和 Office 365 组、 企业应用程序中的角色分配和 SharePoint Online 网站的角色分配。 阅读有关在授权管理的详细信息[的 Azure AD 授权管理概述](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-overview)。 若要了解更多有关 Azure AD 标识管理功能，包括 Privileged Identity Management、 访问评审和使用条款，请参阅[什么是 Azure AD 标识管理？](../governance/identity-governance-overview.md)。

---

### <a name="configure-a-naming-policy-for-office-365-groups-in-azure-ad-portal-public-preview"></a>在 Azure AD 门户 （公共预览版） 中配置为 Office 365 组命名策略

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

管理员现在可以配置为使用 Azure AD 门户中的 Office 365 组命名策略。 此更改有助于强制执行一致的命名约定为 Office 365 组由组织中用户创建或编辑。 

两个不同的方式，可以配置为 Office 365 组命名策略：

- 定义前缀或后缀，将自动添加到组名称。

- 上传一组自定义阻止字词为你的组织，这不允许在组名称 (例如，"CEO、 工资单、 HR") 中。

有关详细信息，请参阅[强制实施为 Office 365 组命名策略](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-naming-policy)。

---

### <a name="azure-ad-activity-logs-are-now-available-in-azure-monitor-general-availability"></a>Azure AD 活动日志现可在 Azure Monitor （正式发布）

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

为了帮助解决有关使用 Azure AD 活动日志的可视化效果的反馈，我们将引入 Log Analytics 中的新见解功能。 此功能可帮助你深入了解你的 Azure AD 资源通过使用我们的交互式模板，名为工作簿。 这些预先构建的工作簿可以为应用程序或用户，提供详细信息，并且包括：

- **登录名。** 提供应用和用户，包括登录位置、 正在使用操作系统或浏览器客户端和版本和成功或失败的登录名数的详细信息。

- **传统的身份验证和条件性访问。** 提供有关应用和用户使用传统的身份验证，包括条件性访问策略，使用条件性访问策略的应用触发的多重身份验证使用情况详细信息，等等。

- **登录失败分析。** 可帮助你确定是否由于用户操作、 策略问题或你的基础结构发生登录错误。

- **自定义报表。** 您可以创建新的或编辑现有的工作簿，以帮助自定义见解功能为你的组织。

有关详细信息，请参阅[如何使用 Azure Monitor 的工作簿的报表的 Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---april-2019"></a>新的联合应用可在 Azure AD 应用库-2019 年 4 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

在年 4 月 2019，我们添加到应用程序库支持使用联合身份验证这些 21 新应用程序：

[SAP Fiori](https://docs.microsoft.com/azure/active-directory/saas-apps/sap-fiori-tutorial)， [HRworks 单一登录](https://docs.microsoft.com/azure/active-directory/saas-apps/hrworks-single-sign-on-tutorial)，[渗透](https://docs.microsoft.com/azure/active-directory/saas-apps/percolate-tutorial)， [MobiControl](https://docs.microsoft.com/azure/active-directory/saas-apps/mobicontrol-tutorial)， [Citrix NetScaler](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)， [Shibumi](https://docs.microsoft.com/azure/active-directory/saas-apps/shibumi-tutorial)， [Benchling](https://docs.microsoft.com/azure/active-directory/saas-apps/benchling-tutorial)， [MileIQ](https://mileiq.onelink.me/991934284/7e980085)， [PageDNA](https://docs.microsoft.com/azure/active-directory/saas-apps/pagedna-tutorial)， [EduBrite LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/edubrite-lms-tutorial)， [RStudio连接](https://docs.microsoft.com/azure/active-directory/saas-apps/rstudio-connect-tutorial)， [AMM](https://docs.microsoft.com/azure/active-directory/saas-apps/amms-tutorial)， [Mitel 连接](https://docs.microsoft.com/azure/active-directory/saas-apps/mitel-connect-tutorial)，[阿里巴巴云 (基于角色的 SSO)](https://docs.microsoft.com/azure/active-directory/saas-apps/alibaba-cloud-service-role-based-sso-tutorial)， [Certent 权益管理](https://docs.microsoft.com/azure/active-directory/saas-apps/certent-equity-management-tutorial)， [Sectigo 证书管理器](https://docs.microsoft.com/azure/active-directory/saas-apps/sectigo-certificate-manager-tutorial)， [GreenOrbit](https://docs.microsoft.com/azure/active-directory/saas-apps/greenorbit-tutorial)， [Workgrid](https://docs.microsoft.com/azure/active-directory/saas-apps/workgrid-tutorial)， [monday.com](https://docs.microsoft.com/azure/active-directory/saas-apps/mondaycom-tutorial)， [SurveyMonkey 企业](https://docs.microsoft.com/azure/active-directory/saas-apps/surveymonkey-enterprise-tutorial)， [Indiggo](https://indiggolead.com/)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-access-reviews-frequency-option-and-multiple-role-selection"></a>频率选项和多个角色选择的新访问评审

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 标识监管

Azure AD 中的新更新访问评审允许你：

- 更改到您的访问权限的频率评审**半年一次**，除了每周、 每月、 每季度和每年一次的先前存在的选项。

- 选择多个 Azure AD 和 Azure 资源角色创建单一的访问权限时查看。 在此情况下，所有角色都设置有相同的设置和所有审阅者会在同一时间收到通知。

有关如何创建访问评审的详细信息，请参阅[创建访问评审的组或 Azure AD 中的应用程序访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)。

---

### <a name="azure-ad-connect-email-alert-systems-are-transitioning-sending-new-email-sender-information-for-some-customers"></a>正在转换为 azure AD Connect 电子邮件警报系统，发送某些客户的新电子邮件发件人信息

**类型：** 已更改的功能  
**服务类别：** AD Sync  
**产品功能：** 平台

Azure AD Connect 是在进行转换我们电子邮件警报系统，其中某些客户可能显示新的电子邮件发件人。 若要解决此问题，必须添加`azure-noreply@microsoft.com`到组织的允许列表或你将无法继续从 Office 365、 Azure 或你的同步服务接收重要的警报。

---

### <a name="upn-suffix-changes-are-now-successful-between-federated-domains-in-azure-ad-connect"></a>UPN 后缀更改现已成功在 Azure AD Connect 中的联合域之间

**类型：** 已修复  
**服务类别：** AD Sync  
**产品功能：** 平台

你现在可以成功更改用户的 UPN 后缀从一个联合域到 Azure AD Connect 中的另一个联合域。 此修补程序意味着，您应不再同步周期过程中遇到 FederatedDomainChangeError 错误消息或接收通知电子邮件，指出"无法更新此对象在 Azure Active Directory，因为属性 [FederatedUser.UserPrincipalName]，不是有效的。 更新本地目录服务中的值"。

有关详细信息，请参阅[在同步过程中进行故障排除错误](https://docs.microsoft.com/azure/active-directory/hybrid/tshoot-connect-sync-errors#federateddomainchangeerror)。

---

### <a name="increased-security-using-the-app-protection-based-conditional-access-policy-in-azure-ad-public-preview"></a>在 Azure AD （公共预览版） 中使用应用保护基于条件性访问策略增强的安全性

**类型：** 新功能  
**服务类别：** 条件性访问  
**产品功能：** 标识安全性和保护

应用保护基于条件性访问现可通过使用**需要应用保护**策略。 此新策略有助于提高可帮助防止在组织的安全性：

- 获取对 Microsoft Intune 许可证的情况下应用访问权限的用户。

- 无法获取 Microsoft Intune 应用保护策略的用户。

- 获取访问权限而无需配置的 Microsoft Intune 应用保护策略的应用程序的用户。

有关详细信息，请参阅[如何使用条件性访问的云应用访问要求应用保护策略](https://docs.microsoft.com/azure/active-directory/conditional-access/app-protection-based-conditional-access)。

---

### <a name="new-support-for-azure-ad-single-sign-on-and-conditional-access-in-microsoft-edge-public-preview"></a>Microsoft Edge （公共预览版） 中 Azure AD 单一登录和条件性访问新的支持

**类型：** 新功能  
**服务类别：** 条件性访问  
**产品功能：** 标识安全性和保护

我们已改进我们的 Azure AD 支持适用于 Microsoft Edge，其中包括 Azure AD 单一登录和条件性访问提供新的支持。 如果以前已使用 Microsoft Intune 托管浏览器，您现在可以改为使用 Microsoft Edge。

有关设置和管理设备和应用程序使用条件性访问的详细信息，请参阅[需要托管使用条件性访问的云应用访问权限的设备](https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices)和[需要批准的云的客户端应用使用条件访问应用访问权限](https://docs.microsoft.com/azure/active-directory/conditional-access/app-based-conditional-access)。 有关如何使用 Microsoft Edge 使用 Microsoft Intune 策略管理访问权限的详细信息，请参阅[使用 Microsoft Intune 策略保护浏览器管理 Internet 访问](https://docs.microsoft.com/intune/app-configuration-managed-browser)。

---

## <a name="march-2019"></a>2019 年 3 月

### <a name="identity-experience-framework-and-custom-policy-support-in-azure-active-directory-b2c-is-now-available-ga"></a>标识体验框架和自定义策略中 Azure Active Directory B2C 的支持现已推出 (GA）)

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

现在可以创建自定义策略在 Azure AD B2C 中，包括以下任务，这是支持大规模在我们的 Azure SLA:

- 创建并上载自定义身份验证的用户旅程使用自定义策略。

- 将用户旅程逐步描述为声明提供程序之间的交换。

- 在用户旅程中定义条件分支。

- 转换并将在实时决策和通信中使用声明映射。

- 在自定义身份验证用户旅程中使用已启用 REST API 的服务。 例如，对于电子邮件提供商、 Crm 和专有授权系统。

- 与标识提供商符合 OpenIDConnect 协议的联合。 例如，对于多租户 Azure AD、 社交帐户提供程序或两个身份验证提供程序。

有关创建自定义策略的详细信息，请参阅[Azure Active Directory B2C 中的自定义策略的开发人员注意事项](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-developer-notes-custom)并阅读[Alex Simon 博客文章，包括案例研究](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-custom-policies-to-build-your-own-identity-journeys/ba-p/382791)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---march-2019"></a>新的联合应用可在 Azure AD 应用库-2019 年 3 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成

在 3 月 2019，我们添加到应用程序库支持使用联合身份验证这些 14 新应用程序：

[ISEC7 移动 Exchange 委托](https://www.isec7.com/english/)， [MediusFlow](https://office365.cloudapp.mediusflow.com/)， [ePlatform](https://docs.microsoft.com/azure/active-directory/saas-apps/eplatform-tutorial)， [Fulcrum](https://docs.microsoft.com/azure/active-directory/saas-apps/fulcrum-tutorial)， [ExcelityGlobal](https://docs.microsoft.com/azure/active-directory/saas-apps/excelityglobal-tutorial)， [说明基于审核系统](https://docs.microsoft.com/azure/active-directory/saas-apps/explanation-based-auditing-system-tutorial)，[精益](https://docs.microsoft.com/azure/active-directory/saas-apps/lean-tutorial)， [Powerschool 性能相关问题](https://docs.microsoft.com/azure/active-directory/saas-apps/powerschool-performance-matters-tutorial)， [Cinode](https://cinode.com/)，[鸢尾花Intranet](https://docs.microsoft.com/azure/active-directory/saas-apps/iris-intranet-tutorial)， [Empactis](https://docs.microsoft.com/azure/active-directory/saas-apps/empactis-tutorial)， [SmartDraw](https://docs.microsoft.com/azure/active-directory/saas-apps/smartdraw-tutorial)， [Confirmit 视野](https://docs.microsoft.com/azure/active-directory/saas-apps/confirmit-horizons-tutorial)，[任务](https://docs.microsoft.com/azure/active-directory/saas-apps/tas-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-zscaler-and-atlassian-provisioning-connectors-in-the-azure-ad-gallery---march-2019"></a>新的 Zscaler 和 Atlassian 预配连接器的 Azure AD 库中-2019 年 3 月

**类型：** 新功能  
**服务类别：** 应用预配  
**产品功能：** 第三方集成

自动执行创建、 更新和删除用户帐户以执行以下应用：

[Zscaler](https://aka.ms/ZscalerProvisioning)， [Zscaler Beta](https://aka.ms/ZscalerBetaProvisioning)， [Zscaler One](https://aka.ms/ZscalerOneProvisioning)， [Zscaler Two](https://aka.ms/ZscalerTwoProvisioning)， [Zscaler 三](https://aka.ms/ZscalerThreeProvisioning)， [Zscaler ZSCloud](https://aka.ms/ZscalerZSCloudProvisioning)， [Atlassian Cloud](https://aka.ms/atlassianCloudProvisioning)

有关如何更好地保护你的组织通过自动的用户帐户预配的详细信息，请参阅[自动用户预配到 SaaS 应用程序与 Azure AD](https://aka.ms/ProvisioningDocumentation)。

---

### <a name="restore-and-manage-your-deleted-office-365-groups-in-the-azure-ad-portal"></a>还原和管理 Azure AD 门户中的您已删除的 Office 365 组

**类型：** 新功能  
**服务类别：** 组管理  
**产品功能：** 协作

你现在可以查看和管理从 Azure AD 门户的已删除的 Office 365 组。 此更改可帮助你了解哪些组是可用于还原，以及让你永久删除你的组织不需要任何组。

有关详细信息，请参阅[还原已过期或已删除的组](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-restore-deleted#view-and-manage-the-deleted-office-365-groups-that-are-available-to-restore)。

---

### <a name="single-sign-on-is-now-available-for-azure-ad-saml-secured-on-premises-apps-through-application-proxy-public-preview"></a>单一登录现已可供 Azure AD SAML 保护对本地应用程序通过应用程序代理 （公共预览版）

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** Access Control

现在可以为本地，SAML 身份验证的应用以及对这些应用的整个应用程序代理远程访问提供单一登录 (SSO) 体验。 有关如何使用你的本地应用程序设置 SAML SSO 的详细信息，请参阅[SAML 单一登录对应用程序代理 （预览版） 的本地应用程序](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-on-premises-apps)。

---

### <a name="client-apps-in-request-loops-will-be-interrupted-to-improve-reliability-and-user-experience"></a>在循环中请求的客户端应用程序将被中断，以提高可靠性和用户体验

**类型：** 新功能  
**服务类别：** 身份验证（登录）  
**产品功能：** 用户身份验证

客户端应用程序不正确可以短的一段时间内发出数百个相同的登录请求。 这些请求，无论他们是成功与否，都会导致糟糕的用户体验和更高的工作负荷的 IDP，增加的所有用户的延迟时间并减少，IDP 的可用性。

此更新将发送`invalid_grant`错误：`AADSTS50196: The server terminated an operation because it encountered a loop while processing a request`短时间内，超出了正常操作范围内多次发出重复请求的客户端应用程序。 遇到此问题的客户端应用程序应显示交互式提示，要求用户重新登录。 有关此更改以及如何修复您的应用程序，如果遇到此错误的详细信息，请参阅[什么是身份验证的新增功能？](https://docs.microsoft.com/azure/active-directory/develop/reference-breaking-changes#looping-clients-will-be-interrupted)。

---

### <a name="new-audit-logs-user-experience-now-available"></a>现已推出新审核日志的用户体验

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们已经创建了新的 Azure AD**审核日志**页后，可以帮助改善可读性和搜索您的信息的方式。 若要查看新**审核日志**页上，选择**审核日志**中**活动**部分中的 Azure AD。

![新的审核日志页上，使用示例信息](media/whats-new/audit-logs-page.png)

有关新的详细信息**审核日志**页上，请参阅[审核 Azure Active Directory 门户中的活动报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/concept-audit-logs#audit-logs)。

---

### <a name="new-warnings-and-guidance-to-help-prevent-accidental-administrator-lockout-from-misconfigured-conditional-access-policies"></a>新的警告和指南来帮助防止意外的管理员锁定从配置错误的条件性访问策略

**类型：** 已更改的功能  
**服务类别：** 条件性访问  
**产品功能：** 标识安全性和保护

为了帮助防止意外自身锁定配置错误的条件性访问策略通过他们自己的租户管理员，我们创建新的警告和更新后的指导在 Azure 门户中。 有关新指南的详细信息，请参阅[什么是 Azure Active Directory 条件访问中的服务依赖项](https://docs.microsoft.com/azure/active-directory/conditional-access/service-dependencies)。

---

### <a name="improved-end-user-terms-of-use-experiences-on-mobile-devices"></a>改进了最终用户使用条款的移动设备上的使用体验

**类型：** 已更改的功能  
**服务类别：** 使用条款  
**产品功能：** 调控

我们已更新的使用体验，以帮助改进如何查看并同意条款的移动设备上使用我们现有的条款。 你现在可以放大和缩小，返回、 下载信息，并选择超链接。 有关更新的使用条款详细信息，请参阅[Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#what-terms-of-use-looks-like-for-users)。

---

### <a name="new-azure-ad-activity-logs-download-experience-available"></a>下载可用的体验，新的 Azure AD 活动日志

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

现在可以直接从 Azure 门户下载大量的活动日志。 此更新可让你：

- 下载到 250,000 个行。

- 在下载完成后，收到通知。

- 自定义你的文件名称。

- 确定输出格式，JSON 或 CSV。

有关此功能的更多详细信息，请参阅[快速入门：下载一份使用 Azure 门户的审核报告](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-download-audit-report)

---

### <a name="breaking-change-updates-to-condition-evaluation-by-exchange-activesync-eas"></a>重大更改：条件评估由 Exchange ActiveSync (EAS) 的更新

**类型：** 更改计划  
**服务类别：** 条件性访问  
**产品功能：** Access Control

我们正在更新 Exchange ActiveSync (EAS) 如何评估下列条件：

- 基于国家/地区、 区域或 IP 地址的用户位置

- 登录风险

- 设备平台

如果以前已在你的条件性访问策略中使用这些条件，请注意，可能会更改的条件行为。 例如，如果你以前在策略中使用用户位置条件，可能会发现策略现在跳过根据你的用户的位置。

---

## <a name="february-2019"></a>2019 年 2 月

### <a name="configurable-azure-ad-saml-token-encryption-public-preview"></a>可配置 Azure AD SAML 令牌加密(公共预览版) 

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** SSO

现在可以配置任何受支持的 SAML 应用以接收加密的 SAML 令牌。 配置并与应用一起使用，Azure AD 进行加密的发出的 SAML 断言，使用从证书存储在 Azure AD 获取的公共密钥。

有关配置 SAML 令牌加密的详细信息，请参阅[配置 Azure AD SAML 令牌加密](https://docs.microsoft.com/azure/active-directory/manage-apps/howto-saml-token-encryption)。

---

### <a name="create-an-access-review-for-groups-or-apps-using-azure-ad-access-reviews"></a>使用 Azure AD 访问评审创建组或应用的访问评审

**类型：** 新功能  
**服务类别：** 访问评审  
**产品功能：** 调控

现在可以包含多个组或单个 Azure AD 中的应用访问评审为组成员身份或应用分配。 访问评审具有多个组或应用程序设置使用相同的设置和所有包含审阅者会在同一时间收到通知。

详细了解如何创建使用 Azure AD 访问评审访问评审，请参阅[在 Azure AD 访问评审中创建的组或应用程序访问评审](https://docs.microsoft.com/azure/active-directory/governance/create-access-review)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---february-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 2 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
在 2019 年 2 月，我们已添加到应用程序库支持使用联合身份验证这些 27 新应用程序：

[Euromonitor Passport](https://docs.microsoft.com/azure/active-directory/saas-apps/euromonitor-passport-tutorial)， [MindTickle](https://docs.microsoft.com/azure/active-directory/saas-apps/mindtickle-tutorial)，[意外](https://seeforgetest-exxon.azurewebsites.net/Account/create?Length=7)， [AirStack](https://docs.microsoft.com/azure/active-directory/saas-apps/airstack-tutorial)， [Oracle Fusion ERP](https://docs.microsoft.com/azure/active-directory/saas-apps/oracle-fusion-erp-tutorial)， [IDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/idrive-tutorial)， [Skyward Qmlativ](https://docs.microsoft.com/azure/active-directory/saas-apps/skyward-qmlativ-tutorial)， [Brightidea](https://docs.microsoft.com/azure/active-directory/saas-apps/brightidea-tutorial)， [AlertOps](https://docs.microsoft.com/azure/active-directory/saas-apps/alertops-tutorial)， [Soloinsight CloudGate SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/soloinsight-cloudgate-sso-tutorial)，单击权限， [Brandfolder](https://docs.microsoft.com/azure/active-directory/saas-apps/brandfolder-tutorial)， [StoregateSmartFile](https://docs.microsoft.com/azure/active-directory/saas-apps/smartfile-tutorial)， [Pexip](https://docs.microsoft.com/azure/active-directory/saas-apps/pexip-tutorial)， [Stormboard](https://docs.microsoft.com/azure/active-directory/saas-apps/stormboard-tutorial)，[发生颠覆性](https://docs.microsoft.com/azure/active-directory/saas-apps/seismic-tutorial)，[共享梦想](https://www.shareadream.org/how-it-works)， [Bugsnag](https://docs.microsoft.com/azure/active-directory/saas-apps/bugsnag-tutorial)， [webMethods 集成云](https://docs.microsoft.com/azure/active-directory/saas-apps/webmethods-integration-cloud-tutorial)，[知识随处 LMS](https://docs.microsoft.com/azure/active-directory/saas-apps/knowledge-anywhere-lms-tutorial)，[OU 校园](https://docs.microsoft.com/azure/active-directory/saas-apps/ou-campus-tutorial)， [Periscope 数据](https://docs.microsoft.com/azure/active-directory/saas-apps/periscope-data-tutorial)， [Netop 门户](https://docs.microsoft.com/azure/active-directory/saas-apps/netop-portal-tutorial)， [smartvid.io](https://docs.microsoft.com/azure/active-directory/saas-apps/smartvid.io-tutorial)，[通过 GenesysPureCloud](https://docs.microsoft.com/azure/active-directory/saas-apps/purecloud-by-genesys-tutorial)， [ClickUp 生产力平台](https://docs.microsoft.com/azure/active-directory/saas-apps/clickup-productivity-platform-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="enhanced-combined-mfasspr-registration"></a>增强了组合 MFA/SSPR 注册

**类型：** 已更改的功能  
**服务类别：** 自助密码重置  
**产品功能：** 用户身份验证
 
在响应客户反馈，我们已改进合并的 MFA/SSPR 注册预览体验，帮助用户更快地针对 MFA 和 SSPR 注册其安全信息。 

**若要立即启用增强的体验为您的用户，请按照下列步骤：**

1. 作为全局管理员或用户管理员，登录到 Azure 门户并转到**Azure Active Directory > 用户设置 > 管理的访问面板中预览功能设置**。 

2. 在**用户可以使用预览功能来注册和管理的安全信息 – 刷新**选项中，选择要启用的功能**的用户所选组**; 二是**所有用户**.

在下一步的几周内，我们将删除旧的组合 MFA/SSPR 注册预览体验，还没有打开该功能的租户上启用的功能。

**若要查看是否将为你的租户中删除控件，请按照下列步骤：**

1. 作为全局管理员或用户管理员，登录到 Azure 门户并转到**Azure Active Directory > 用户设置 > 管理的访问面板中预览功能设置**。  

2. 如果**可以使用预览功能来注册和管理的安全信息的用户**选项设置为**None**，将从你的租户中删除的选项。

无论是否以前打开了旧的组合 MFA/SSPR 注册预览版的用户体验或不，在将来的日期将关闭旧体验。 正因为如此，我们强烈建议尽快迁移到新的增强型体验。

有关增强的注册体验的详细信息，请参阅[到 Azure AD 的强大增强功能结合使用 MFA 和密码重置注册体验](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Cool-enhancements-to-the-Azure-AD-combined-MFA-and-password/ba-p/354271)。

---

### <a name="updated-policy-management-experience-for-user-flows"></a>更新了用户流的策略管理体验

**类型：** 已更改的功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

我们已更新用户流 （以前称为、 内置策略） 更轻松的策略创建和管理过程。 此新体验现在是 Azure AD 租户的所有默认值。

可以提供其他反馈和建议通过笑脸或哭脸中的图标**向我们发送反馈**门户的屏幕顶部区域。

有关新的策略管理体验的详细信息，请参阅[JavaScript 自定义和更多新功能现在具有 Azure AD B2C](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="choose-specific-page-element-versions-provided-by-azure-ad-b2c"></a>选择由 Azure AD B2C 提供的特定页元素版本

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

现在可以选择特定版本的 Azure AD b2c 提供的页面元素。 通过选择特定版本，您可以测试更新后，它们显示在页面上，并可以获取可预知的行为。 此外，您可以立即选择中以强制实施特定的页面版本，以允许 JavaScript 自定义项。 若要启用此功能，请转到**属性**在用户流中的页。

有关选择特定版本的页面元素的详细信息，请参阅[JavaScript 自定义和更多新功能现在具有 Azure AD B2C](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)博客。

---

### <a name="configurable-end-user-password-requirements-for-b2c-ga"></a>可配置的 B2C (GA) 最终用户密码要求

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

你现在可以设置组织的密码复杂性为最终用户，而无需使用本机 Azure AD 密码策略。 从**属性**边栏选项卡中的用户 （以前称为内置策略） 的流，你可以选择的密码复杂性**简单**或**强**，也可以创建**自定义**组的要求。

有关密码复杂性要求配置的详细信息，请参阅[在 Azure Active Directory B2C 中配置密码复杂性要求](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-password-complexity)。

---

### <a name="new-default-templates-for-custom-branded-authentication-experiences"></a>自定义品牌的身份验证体验的新的默认模板

**类型：** 新功能  
**服务类别：** B2C - 使用者标识管理  
**产品功能：** B2B/B2C

可以使用我们新的默认模板，位于**页上的布局**边栏选项卡中的用户流 （以前称为内置策略），若要创建自定义品牌的用户的身份验证体验。

有关使用模板的详细信息，请参阅[JavaScript 自定义和更多新功能现在具有 Azure AD B2C](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-B2C-now-has-JavaScript-customization-and-many-more-new/ba-p/353595)。

---

## <a name="january-2019"></a>2019 年 1 月

### <a name="active-directory-b2b-collaboration-using-one-time-passcode-authentication-public-preview"></a>使用一次性密码身份验证（公共预览版）的 Active Directory B2B 协作

**类型：** 新功能  
**服务类别：** B2B  
**产品功能：** B2B/B2C

已为无法通过 Azure AD、Microsoft 帐户 (MSA) 或 Google 联合身份验证等其他方式进行身份验证的 B2B 来宾用户引入了一次性密码认证 (OTP)。 这种新的身份验证方法意味着来宾用户无需创建新的 Microsoft 帐户。 相反，在兑换邀请或访问共享资源时，来宾用户可以请求将临时代码发送到电子邮件地址。 使用此临时代码，访客便可以继续登录。

有关详细信息，请参阅[电子邮件一次性密码身份验证（预览）](https://docs.microsoft.com/azure/active-directory/b2b/one-time-passcode)和博客 [Azure AD makes sharing and collaboration seamless for any user with any account](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-AD-makes-sharing-and-collaboration-seamless-for-any-user/ba-p/325949)（Azure AD 使拥有任意帐户的任何用户都能无缝地共享和协作）。

### <a name="new-azure-ad-application-proxy-cookie-settings"></a>新的 Azure AD 应用程序代理 Cookie 设置

**类型：** 新功能  
**服务类别：** 应用代理  
**产品功能：** Access Control

我们引入了三种新的 Cookie 设置，这些设置可以供通过应用程序代理发布的应用使用：

- **使用仅限 HTTP 的 Cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **HTTPOnly** 标志。 启用此设置会带来更多的安全好处，例如有助于防止用户通过客户端脚本复制或修改 Cookie。 建议启用此标志（选择“是”  ）以享受增加的安全好处。

- **使用安全 Cookie。** 在应用程序代理访问权限和会话 Cookie 上设置 **Secure** 标志。 启用此设置可确保 Cookie 仅通过 TLS 安全通道（例如 HTTPS）传输，因此会带来更多的安全好处，。 建议启用此标志（选择“是”  ）以享受增加的安全好处。

- **使用永久性 Cookie。** 防止访问 Cookie 在关闭 Web 浏览器时过期。 这些 Cookie 可以持续到访问令牌的生存期结束。 不过，如果过期时间已到，或者用户手动删除了 Cookie，则 Cookie 会重置。 建议保持默认设置“否”，只有在应用较旧且不需在进程之间共享 Cookie 时，才启用该设置。 

有关新 Cookie 的详细信息，请参阅[用于在 Azure Active Directory 中访问本地应用程序的 Cookie 设置](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---january-2019"></a>Azure AD 应用库中已推出新的联合应用 - 2019 年 1 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已于 2019 年 1 月将这 35 款支持联合的新应用添加到了应用库中：

[Firstbird](https://docs.microsoft.com/azure/active-directory/saas-apps/firstbird-tutorial)， [Folloze](https://docs.microsoft.com/azure/active-directory/saas-apps/folloze-tutorial)，[人才调色板](https://docs.microsoft.com/azure/active-directory/saas-apps/talent-palette-tutorial)， [Infor CloudSuite](https://docs.microsoft.com/azure/active-directory/saas-apps/infor-cloud-suite-tutorial)， [Cisco 涵盖性](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-umbrella-tutorial)， [ZscalerInternet 访问管理员](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-internet-access-administrator-tutorial)，[过期提醒](https://docs.microsoft.com/azure/active-directory/saas-apps/expiration-reminder-tutorial)， [InstaVR 查看器](https://docs.microsoft.com/azure/active-directory/saas-apps/instavr-viewer-tutorial)， [CorpTax](https://docs.microsoft.com/azure/active-directory/saas-apps/corptax-tutorial)，[谓词](https://app.verb.net/login)， [OpenLattice](https://openlattice.com/agora)， [TheOrgWiki](https://www.theorgwiki.com/signup)，[关闭 Pavaso 数字](https://docs.microsoft.com/azure/active-directory/saas-apps/pavaso-digital-close-tutorial)， [GoodPractice 工具包](https://docs.microsoft.com/azure/active-directory/saas-apps/goodpractice-toolkit-tutorial)， [云服务 PICCO](https://docs.microsoft.com/azure/active-directory/saas-apps/cloud-service-picco-tutorial)， [AuditBoard](https://docs.microsoft.com/azure/active-directory/saas-apps/auditboard-tutorial)， [iProva](https://docs.microsoft.com/azure/active-directory/saas-apps/iprova-tutorial)， [Workable](https://docs.microsoft.com/azure/active-directory/saas-apps/workable-tutorial)， [CallPlease](https://webapp.callplease.com/create-account/create-account.html)， [GTNexus SSO 系统](https://docs.microsoft.com/azure/active-directory/saas-apps/gtnexus-sso-module-tutorial)， [CBRE ServiceInsight](https://docs.microsoft.com/azure/active-directory/saas-apps/cbre-serviceinsight-tutorial)， [Deskradar](https://docs.microsoft.com/azure/active-directory/saas-apps/deskradar-tutorial)， [Coralogixv](https://docs.microsoft.com/azure/active-directory/saas-apps/coralogix-tutorial)， [Signagelive](https://docs.microsoft.com/azure/active-directory/saas-apps/signagelive-tutorial)， [ARES 企业](https://docs.microsoft.com/azure/active-directory/saas-apps/ares-for-enterprise-tutorial)，[适用于 Office 365 K2](https://www.k2.com/O365)， [Xledger](https://www.xledger.net/)， [iDiD Manager](https://docs.microsoft.com/azure/active-directory/saas-apps/idid-manager-tutorial)， [HighGear](https://docs.microsoft.com/azure/active-directory/saas-apps/highgear-tutorial)， [Visitly](https://docs.microsoft.com/azure/active-directory/saas-apps/visitly-tutorial)， [Korn Ferry ALP](https://docs.microsoft.com/azure/active-directory/saas-apps/korn-ferry-alp-tutorial)， [Acadia](https://docs.microsoft.com/azure/active-directory/saas-apps/acadia-tutorial)， [Adoddle cSaas 平台](https://docs.microsoft.com/azure/active-directory/saas-apps/adoddle-csaas-platform-tutorial)<!-- , [CaféX Portal (Meetings)](https://docs.microsoft.com/azure/active-directory/saas-apps/cafexportal-meetings-tutorial), [MazeMap Link](https://docs.microsoft.com/azure/active-directory/saas-apps/mazemaplink-tutorial)-->  

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="new-azure-ad-identity-protection-enhancements-public-preview"></a>新的 New Azure AD Identity Protection 增强功能（公共预览版）

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 标识安全性和保护

我们很高兴地宣布，我们已为 Azure AD Identity Protection 公开预览版套餐添加以下增强功能，其中包括：

- 经过更新后集成度更高的用户界面

- 其他 API

- 通过机器学习改进风险评估

- 针对风险用户和风险登录实现产品范围的符合性

有关增强功能的详细信息，请参阅[什么是 Azure Active Directory Identity Protection（已刷新）？](https://aka.ms/IdentityProtectionDocs) 以进行详细的了解并通过产品内提示共享自己的想法。

---

### <a name="new-app-lock-feature-for-the-microsoft-authenticator-app-on-ios-and-android-devices"></a>iOS 和 Android 设备上的 Microsoft Authenticator 应用的新应用锁定功能

**类型：** 新功能  
**服务类别：** Microsoft Authenticator 应用  
**产品功能：** 标识安全性和保护

若要使你的一次性密码、应用信息和应用设置更加安全，可以在 Microsoft Authenticator 应用中开启应用锁定功能。 开启应用锁定意味着你每次打开 Microsoft Authenticator 应用时都会要求你使用 PIN 或生物识别进行身份验证。

有关详细信息，请参阅 [Microsoft Authenticator app FAQ](https://docs.microsoft.com/azure/active-directory/user-help/microsoft-authenticator-app-faq)（Microsoft Authenticator 应用常见问题解答）。

---

### <a name="enhanced-azure-ad-privileged-identity-management-pim-export-capabilities"></a>增强的 Azure AD Privileged Identity Management (PIM) 导出功能

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management

Privileged Identity Management (PIM) 管理员现在可以为特定资源导出所有活动的和符合资格的角色分配，其中包括针对所有子资源的角色分配。 以前，管理员很难获取某个订阅的角色分配完整列表，他们必须导出每个特定资源的角色分配。

有关详细信息，请参阅[在 PIM 中查看 Azure 资源角色的活动和审核历史记录](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/azure-pim-resource-rbac)。

---

## <a name="novemberdecember-2018"></a>2018 年 11 月/12 月

### <a name="users-removed-from-synchronization-scope-no-longer-switch-to-cloud-only-accounts"></a>从同步范围删除的用户不再切换到仅限云的帐户

**类型：** 已修复  
**服务类别：** 用户管理  
**产品功能：** Directory

>[!Important]
>我们已听说并理解你因为此修复而遭受到的挫折感。 因此，我们已将此更改还原，直到我们可以让你更轻松地在组织中实施此修复。

我们修复了一个 Bug。该 Bug 表现为：将 Active Directory 域服务 (AD DS) 对象从同步范围中排除后，如果接着在下一个同步周期中将其移到 Azure AD 中的回收站，则用户的 DirSyncEnabled 标志会被错误地切换为 **False**。 进行此修复以后，如果将用户从同步范围中排除，然后又将其从 Azure AD 回收站还原，则用户帐户会保持预期的从本地 AD 同步的状态，不能在云中托管，因为其授权源 (SoA) 仍旧为本地 AD。

在进行此修复之前，将 DirSyncEnabled 标志切换为 False 会出现问题。 它给人一个错误印象：这些帐户已转换为仅限云的对象，并且可以在云中托管。 但是，这些帐户仍将其 SoA 作为本地项保留，并保留所有来自本地 AD 的已同步属性（影子属性）。 这种情况在 Azure AD 和其他特定的云工作负荷（例如 Exchange Online）中导致多个问题，这些工作负荷预期将这些帐户作为从 AD 同步的帐户处理，但现在这些帐户的表现就像是仅限云的帐户一样。

目前，若要将从 AD 同步的帐户真正地转换为仅限云的帐户，唯一的方法是在租户级别禁用 DirSync，以便触发一项传输 SoA 的后端操作。 此类 SoA 更改要求（但不限于）清除所有本地的相关属性（例如 LastDirSyncTime 属性和影子属性）并将一个信号发送到其他云工作负荷，以便将相应的对象也转换为仅限云的帐户。

因此，此修复可以防止对从 AD 同步的用户的 ImmutableID 属性进行直接更新，这种更新在过去的某些情况下是必需的。 顾名思义，根据设计，Azure AD 中对象的 ImmutableID 是不可更改的。 在 Azure AD Connect Health 和 Azure AD Connect Synchronization 客户端中实现的新功能用于解决以下方案的问题：

- **分阶段对多个用户进行大规模 ImmutableID 更新**
  
  例如，需要进行耗时很长的 AD DS 林间迁移。 解决方案：使用 Azure AD Connect **配置源定位点**，并在用户迁移时将现有的 ImmutableID 值从 Azure AD 复制到本地 AD DS 用户的新林的 ms-DS-Consistency-Guid 属性中。 有关详细信息，请参阅[将 msDS-ConsistencyGuid 用作 sourceAnchor](/azure/active-directory/hybrid/plan-connect-design-concepts#using-ms-ds-consistencyguid-as-sourceanchor)。

- **一次性对多个用户进行大规模 ImmutableID 更新**

  例如，在实现 Azure AD Connect 时犯错，现在需要更改 SourceAnchor 属性。 解决方案：在租户级别禁用 DirSync，并清除所有无效的 ImmutableID 值。 有关详细信息，请参阅[禁用 Office 365 的目录同步](/office365/enterprise/turn-off-directory-synchronization)。

- **将本地用户与 Azure AD 中的现有用户重新匹配** 例如，在 AD DS 中重新创建的用户会在 Azure AD 帐户中生成一个重复的用户，系统不会将其与现有的 Azure AD 帐户（孤立对象）重新匹配。 解决方案：在 Azure 门户中使用 Azure AD Connect Health 重新映射源定位点/ImmutableID。 有关详细信息，请参阅[孤立对象场景](/azure/active-directory/hybrid/how-to-connect-health-diagnose-sync-errors#orphaned-object-scenario)。

### <a name="breaking-change-updates-to-the-audit-and-sign-in-logs-schema-through-azure-monitor"></a>重大更改：更新了通过 Azure Monitor 提供的审核和登录日志的架构

**类型：** 已更改的功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

目前，我们正在通过 Azure Monitor 发布审核和登录日志流，使你能够将日志文件与 SIEM 工具或 Log Analytics 无缝集成。 根据客户的反馈，同时为此功能的正式版通告做好准备，我们正在对架构进行以下更改。 在 1 月份的第一周，我们将完成这些架构更改并更新其相关的文档。

#### <a name="new-fields-in-the-audit-schema"></a>审核架构中的新字段
我们将添加一个新的“操作类型”字段，以提供针对资源执行的操作类型。  例如“添加”、“更新”或“删除”。   

#### <a name="changed-fields-in-the-audit-schema"></a>审核架构中已更改的字段
审核架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|类别|以前它是“服务名称”字段，  现在是“审核类别”字段。  “服务名称”已重命名为“loggedByService”字段。  |<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|<ul><li>用户管理</li><li>组管理</li><li>应用管理</li></ul>|
|targetResources|包括顶层的 **TargetResourceType**。|&nbsp;|<ul><li>策略</li><li>应用</li><li>用户</li><li>组</li></ul>|
|loggedByService|提供生成审核日志的服务的名称。|Null|<ul><li>帐户预配</li><li>核心目录</li><li>自助密码重置</li></ul>|
|结果|提供审核日志的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li></ul>|<ul><li>Success</li><li>失败</li></ul>|

#### <a name="changed-fields-in-the-sign-in-schema"></a>登录架构中已更改的字段
登录架构中的以下字段即将发生变化：

|字段名|更改内容|旧值|新值|
|----------|------------|----------|----------|
|appliedConditionalAccessPolicies|以前它是“conditionalaccessPolicies”字段，  现在是“appliedConditionalAccessPolicies”字段。 |无变化|无变化|
|conditionalAccessStatus|在登录时提供条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|
|appliedConditionalAccessPolicies: result|在登录时提供单个条件访问策略状态的结果。 以前会显示枚举值，但现在会显示实际值。|<ul><li>0</li><li>1</li><li>2</li><li>3</li></ul>|<ul><li>Success</li><li>失败</li><li>未应用</li><li>已禁用</li></ul>|

有关架构的详细信息，请参阅[解释 Azure Monitor（预览版）中的 Azure AD 审核日志架构](https://docs.microsoft.com/azure/active-directory/reports-monitoring/reference-azure-monitor-audit-log-schema)

---

### <a name="identity-protection-improvements-to-the-supervised-machine-learning-model-and-the-risk-score-engine"></a>对监督式机器学习模型和风险评分引擎做出的 Identity Protection 改进

**类型：** 已更改的功能  
**服务类别：** 标识保护  
**产品功能：** 风险评分

对 Identity Protection 相关的用户和登录风险评估引擎所做的改进有助于提高用户风险评估的准确度和覆盖度。 管理员可能会注意到，用户风险级别不再与特定检测的风险级别直接相关，并且有风险登录事件的数量和级别都已增加。

风险检测现在由监督式机器学习模型评估。该模型使用附加的用户登录和检测模式功能来计算用户风险。 基于此模型，管理员可以发现风险评分较高的用户，即使与该用户关联的检测具有中低型的风险。 

---

### <a name="administrators-can-reset-their-own-password-using-the-microsoft-authenticator-app-public-preview"></a>管理员可以使用 Microsoft Authenticator 应用（公共预览版）重置自己的密码

**类型：** 已更改的功能  
**服务类别：** 自助密码重置  
**产品功能：** 用户身份验证

现在，Azure AD 管理员可以使用 Microsoft Authenticator 应用通知或者任何移动 Authenticator 应用或硬件令牌提供的代码重置自己的密码。 若要重置自己的密码，管理员现在可以使用以下两种方法：

- Microsoft Authenticator 应用通知

- 其他移动 Authenticator 应用/硬件令牌代码

- 电子邮件

- 电话呼叫

- 短信

有关使用 Microsoft Authenticator 应用重置密码的详细信息，请参阅 [Azure AD 自助密码重置 - 移动应用和 SSPR（预览版）](https://docs.microsoft.com/azure/active-directory/authentication/concept-sspr-howitworks#mobile-app-and-sspr-preview)

---

### <a name="new-azure-ad-cloud-device-administrator-role-public-preview"></a>新的 Azure AD 云设备管理员角色（公共预览版）

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 访问控制

管理员可将用户分配到新的云设备管理员角色，以执行云设备管理员任务。 分配有“云设备管理员”角色的用户可以在 Azure AD 中启用、禁用和删除设备，并可以在 Azure 门户中读取 Windows 10 BitLocker 密钥（如果有）。

有关角色和权限的详细信息，请参阅[在 Azure Active Directory 中分配管理员角色](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)

---

### <a name="manage-your-devices-using-the-new-activity-timestamp-in-azure-ad-public-preview"></a>在 Azure AD 中使用新的活动时间戳管理设备（公共预览版）

**类型：** 新功能  
**服务类别：** 设备注册和管理  
**产品功能：** 设备生命周期管理

我们意识到，随着时间的推移必须刷新，并在 Azure AD，以避免过时设备在你的环境中停用您所在组织的设备。 为了帮助完成此过程，Azure AD 现在会使用新的活动时间戳更新设备，以帮助管理设备生命周期。

有关如何获取和使用此时间戳的详细信息，请参阅[如何：在 Azure AD 中管理陈旧的设备](https://docs.microsoft.com/azure/active-directory/devices/manage-stale-devices)

---

### <a name="administrators-can-require-users-to-accept-a-terms-of-use-on-each-device"></a>管理员可以要求用户接受了使用条款，每台设备上

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控
 
管理员现在可以开启**要求用户在每个设备上的同意**要求用户接受你的使用条款它们在你的租户使用每个设备上的选项。

有关详细信息，请参阅[的 Azure Active Directory 使用条款功能的使用部分的每个设备条款](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#per-device-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-a-recurring-schedule"></a>管理员可以配置过期时间按定期计划取决于使用条款

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控
 

管理员现在可以开启**过期的许可：** 选项，以便作为使用条款的过期的所有用户基于你指定的定期计划。 可以实施每年、半年、每季或每月计划。 使用条款到期后，用户必须接受。

有关详细信息，请参阅[添加条款使用部分中的 Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。

---

### <a name="administrators-can-configure-a-terms-of-use-to-expire-based-on-each-users-schedule"></a>管理员可以配置过期根据每个用户的计划的使用条款

**类型：** 新功能  
**服务类别：** 使用条款  
**产品功能：** 调控

管理员现在可以指定一个持续时间该用户必须接受的使用条款。 例如，管理员可以指定用户必须接受使用条款每隔 90 天。

有关详细信息，请参阅[添加条款使用部分中的 Azure Active Directory 使用条款功能](https://docs.microsoft.com/azure/active-directory/conditional-access/terms-of-use#add-terms-of-use)。
 
---

### <a name="new-azure-ad-privileged-identity-management-pim-emails-for-azure-active-directory-roles"></a>Azure Active Directory 角色的新 Azure AD Privileged Identity Management (PIM) 电子邮件

**类型：** 新功能  
**服务类别：** Privileged Identity Management  
**产品功能：** Privileged Identity Management
 
使用 Azure AD Privileged Identity Management (PIM) 的客户现在可以接收每周摘要电子邮件，其中包括过去七天的以下信息：

- 最符合条件的和永久性的角色分配概述

- 激活角色的用户数

- 已分配到 PIM 中的角色的用户数

- 已分配到 PIM 外部的角色的用户数

- PIM 中“永久”的用户数

有关 PIM 和可用电子邮件通知的详细信息，请参阅 [PIM 中的电子邮件通知](https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-email-notifications)。

---

### <a name="group-based-licensing-is-now-generally-available"></a>基于组的许可现已推出正式版

**类型：** 已更改的功能  
**服务类别：** 其他  
**产品功能：** Directory

基于组的许可已过公共预览期，现已推出正式版。 正式版提高了此功能的可伸缩性，并添加了为单个用户重新处理基于组的许可分配的功能，以及结合 Office 365 E3/A3 许可证使用基于组的许可的功能。

有关基于组的许可的详细信息，请参阅 [Azure Active Directory 中基于组的许可是什么？](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-licensing-whatis-azure-portal)

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---november-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 11 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 11 月将这 26 款支持联合的新应用添加到了应用库：

[CoreStack](https://cloud.corestack.io/site/login)、[HubSpot](https://docs.microsoft.com/azure/active-directory/saas-apps/HubSpot-tutorial)、[GetThere](https://docs.microsoft.com/azure/active-directory/saas-apps/getthere-tutorial)、[Gra-Pe](https://docs.microsoft.com/azure/active-directory/saas-apps/grape-tutorial)、[eHour](https://getehour.com/try-now)、[Consent2Go](https://docs.microsoft.com/azure/active-directory/saas-apps/Consent2Go-tutorial)、[Appinux](https://docs.microsoft.com/azure/active-directory/saas-apps/appinux-tutorial)、[DriveDollar](https://azuremarketplace.microsoft.com/marketplace/apps/savitas.drivedollar-azuread?tab=Overview)、[Useall](https://docs.microsoft.com/azure/active-directory/saas-apps/useall-tutorial)、[Infinite Campus](https://docs.microsoft.com/azure/active-directory/saas-apps/infinitecampus-tutorial)、[Alaya](https://alayagood.com/en/demo/)、[HeyBuddy](https://docs.microsoft.com/azure/active-directory/saas-apps/heybuddy-tutorial)、[Wrike SAML](https://docs.microsoft.com/azure/active-directory/saas-apps/wrike-tutorial)、[Drift](https://docs.microsoft.com/azure/active-directory/saas-apps/drift-tutorial)、[Zenegy for Business Central 365](https://accounting.zenegy.com/)、[Everbridge Member Portal](https://docs.microsoft.com/azure/active-directory/saas-apps/everbridge-tutorial)、[IDEO](https://profile.ideo.com/users/sign_up)、[Ivanti Service Manager (ISM)](https://docs.microsoft.com/azure/active-directory/saas-apps/ivanti-service-manager-tutorial)、[Peakon](https://docs.microsoft.com/azure/active-directory/saas-apps/peakon-tutorial)、[Allbound SSO](https://docs.microsoft.com/azure/active-directory/saas-apps/allbound-sso-tutorial)、[Plex Apps - Classic Test](https://test.plexonline.com/signon)、[Plex Apps – Classic](https://www.plexonline.com/signon)、[Plex Apps - UX Test](https://test.cloud.plex.com/sso)、[Plex Apps – UX](https://cloud.plex.com/sso)、[Plex Apps – IAM](https://accounts.plex.com/)、[CRAFTS - Childcare Records, Attendance, & Financial Tracking System](https://getcrafts.ca/craftsregistration) 

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

## <a name="october-2018"></a>2018 年 10 月

### <a name="azure-ad-logs-now-work-with-azure-log-analytics-public-preview"></a>Azure AD 日志现在可与 Azure Log Analytics（公共预览版）配合使用

**类型：** 新功能  
**服务类别：** 报告  
**产品功能：** 监视和报告

我们很高兴地宣布，现在可将 Azure AD 日志转发到 Azure Log Analytics！ 这项呼声最高的功能有助于更好地访问业务、运营和安全分析数据，以及监视基础结构。 有关详细信息，请参阅博客文章 [Azure Active Directory Activity logs in Azure Log Analytics now available](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-Activity-logs-in-Azure-Log-Analytics-now/ba-p/274843)（Azure Log Analytics 中的 Azure Active Directory 活动日志现已提供）。

---

### <a name="new-federated-apps-available-in-azure-ad-app-gallery---october-2018"></a>Azure AD 应用库中推出了新的联合应用 - 2018 年 10 月

**类型：** 新功能  
**服务类别：** 企业应用  
**产品功能：** 第三方集成
 
我们已在 2018 年 10 月将这 14 款支持联合的新应用添加到了应用库：

[My Award Points](https://docs.microsoft.com/azure/active-directory/saas-apps/myawardpoints-tutorial)、[Vibe HCM](https://docs.microsoft.com/azure/active-directory/saas-apps/vibehcm-tutorial)、ambyint、[MyWorkDrive](https://docs.microsoft.com/azure/active-directory/saas-apps/myworkdrive-tutorial)、[BorrowBox](https://docs.microsoft.com/azure/active-directory/saas-apps/borrowbox-tutorial)、Dialpad、[ON24 Virtual Environment](https://docs.microsoft.com/azure/active-directory/saas-apps/on24-tutorial)、[RingCentral](https://docs.microsoft.com/azure/active-directory/saas-apps/ringcentral-tutorial)、[Zscaler Three](https://docs.microsoft.com/azure/active-directory/saas-apps/zscaler-three-tutorial)、[Phraseanet](https://docs.microsoft.com/azure/active-directory/saas-apps/phraseanet-tutorial)、[Appraisd](https://docs.microsoft.com/azure/active-directory/saas-apps/appraisd-tutorial)、[Workspot Control](https://docs.microsoft.com/azure/active-directory/saas-apps/workspotcontrol-tutorial)、[Shuccho Navi](https://docs.microsoft.com/azure/active-directory/saas-apps/shucchonavi-tutorial)、[Glassfrog](https://docs.microsoft.com/azure/active-directory/saas-apps/glassfrog-tutorial)

有关这些应用的详细信息，请参阅 [SaaS 应用程序与 Azure Active Directory 集成](https://aka.ms/appstutorial)。 要详细了解如何在 Azure AD 应用库中列出应用程序，请参阅[在 Azure Active Directory 应用程序库中列出应用程序](https://aka.ms/azureadapprequest)。

---

### <a name="azure-ad-domain-services-email-notifications"></a>Azure AD 域服务电子邮件通知

**类型：** 新功能  
**服务类别：** Azure AD 域服务  
**产品功能：** Azure AD 域服务

Azure AD 域服务在 Azure 门户中提供有关托管域配置错误或问题的警报。 这些警报包含分步引导，使你能够在不联系支持人员的情况下尝试解决问题。

从 10 月份开始，可以自定义托管域的通知设置，以便在发生新警报时，向指定的一组人员发送电子邮件，而无需频繁地在门户中检查最新信息。

有关详细信息，请参阅 [Azure AD 域服务中的通知设置](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-notifications)。

---

### <a name="azure-ad-portal-supports-using-the-forcedelete-domain-api-to-delete-custom-domains"></a>Azure AD 门户支持使用 ForceDelete 域 API 删除自定义域 

**类型：** 已更改的功能  
**服务类别：** 目录管理  
**产品功能：** Directory

我们很高兴地宣布，现在可以使用 ForceDelete 域 API，通过将用户、组和应用等引用从自定义域名 (contoso.com) 异步重命名回到初始默认域名 (contoso.onmicrosoft.com)，来删除自定义域名。

如果组织不再使用自定义域名，或者你需要使用其他 Azure AD 的域名，此项更改可帮助你更快地删除自定义域名。

有关详细信息，请参阅[删除自定义域名](https://docs.microsoft.com/azure/active-directory/users-groups-roles/domains-manage#delete-a-custom-domain-name)。

---
