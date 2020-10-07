---
title: Azure Active Directory 的常见应用程序管理方案 | Microsoft Docs
description: 使用 Azure AD 集中应用程序管理
documentationcenter: ''
author: kenwith
manager: celestedg
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/02/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ce3819ff1f9b0c61f7738f90ff17c2798fe888b
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88642106"
---
# <a name="centralize-application-management-with-azure-ad"></a>使用 Azure AD 集中应用程序管理

密码对于 IT 人员和世界各地的员工而言都是一大难题。 正因如此，越来越多的公司转向使用 Azure Active Directory（Microsoft 的标识和访问管理解决方案）来处理云端和所有其他资源的作业。 无需输入每个应用程序的密码，即可从一个应用程序跳转到另一个应用程序。 从 Outlook 跳转到 Workday、再跳转到 ADP 的速度就像打开它们一样快，既迅速又安全。 然后，无需通过 IT 人员的协助，即可与合作伙伴甚至组织外部的其他人进行协作。 此外，Azure AD 可使用多重身份验证之类的机制来验证你的身份，以保护你所使用的应用，并使用持续自适应机器学习和安全智能来检测可疑的登录，让你随时随地都可安全地访问所需的应用，从而帮助你管理风险。 它不仅对用户有益，对 IT 人员也是如此。 借助实时访问评审和完整规模的治理套件，Azure AD 可帮助你保持合规性并强制执行策略。 借此，你甚至可以自动预配用户帐户，让访问管理变得轻而易举。 请查看客户使用 Azure Active Directory 应用程序管理功能的一些常见方案。

**常见方案**


> [!div class="checklist"]
> * 适用于所有应用程序的 SSO
> * 自动预配和取消预配 
> * 保护应用程序
> * 监管对应用程序的访问
> * 混合安全访问

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>应用场景 1：为所有应用程序设置 SSO

无需再管理密码。 使用公司凭据安全地访问所需的所有资源。 

|Feature  | 说明 | 建议 |
|---------|---------|---------|
|SSO|使用受信任的行业标准的基于标准的联合 SSO。|如果应用程序支持 SSO，请始终使用 [SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation) 来启用它。|
|我的应用|为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能提高他们的工作效率，例如请求访问应用和组，或代表其他人管理对资源的访问。| 将应用与 Azure AD 进行 SSO 集成后，请在组织中部署[我的应用](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>应用场景 2：自动预配和取消预配 


大多数应用程序都必须在用户部署到应用程序之后，才能让他们访问所需的资源。 使用 CSV 文件或复杂脚本可能成本高昂且难以管理。 此外，当有人不再拥有访问权限时，客户需要确保将帐户删除。 请利用以下工具自动预配和取消预配。 


|Feature  |说明|建议 |
|---------|---------|---------|
|SCIM 预配|[SCIM](https://aka.ms/SCIMOverview) 是实现用户预配自动化的行业最佳做法。 任何符合 SCIM 标准的应用程序都可以与 Azure AD 集成。 用户帐户可以自动创建、更新和删除，而无需维护 CSV 文件、自定义脚本或本地解决方案。|请查看 Azure AD 应用库中的[预先集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)应用列表（不断增加中）。|
|Microsoft Graph|利用 Azure AD 中的数据的广度和深度，为你的应用程序扩充其所需的数据。|利用 [Microsoft Graph](https://developer.microsoft.com/graph/) 从整个 Microsoft 生态系统中获取数据。 |


## <a name="scenario-3-secure-your-applications"></a>应用场景 3：保护应用程序
标识是安全性的关键。 标识一旦遭到泄露，就很难及时阻止后续的多米诺效应。 平均而言，组织要超过 100 天才会发现存在泄露。 请使用 Azure AD 提供的工具来改进应用程序的安全状况。 

|Feature  |说明| 建议 |
|---------|---------| ---------|
|Azure MFA|Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 Azure MFA 可使用管理员批准的身份验证方法，帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。| 为你的用户[启用 MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)。  |
|条件性访问|使用条件访问时，可以根据条件实施自动化的访问控制决策，决定谁能够访问云应用。| 请查看客户使用的[安全默认值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)和[常见策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|标识保护|标识保护利用 Microsoft 从 Azure AD 组织、Microsoft 帐户中的用户群以及 Xbox 游戏中获得的自身经验来保护用户。 Microsoft 每天分析 6.5 万亿条信号，以识别威胁并保护客户安全。|启用我们的服务所提供的[默认标识保护策略](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>应用场景 4：监管对应用程序的访问
Identity Governance 有助于组织在以下需求之间实现平衡：工作效率 - 用户可以多快地访问所需的应用程序（例如，在刚入职时）？ 安全性 - 用户的访问权限会不断发生怎样的变化（例如，由于该用户的雇佣状态发生变化）？ 

|Feature  |说明|建议 |
|---------|---------| ---------|
|ELM|Azure AD 权利管理可帮助组织内外的用户更高效地管理对应用程序的访问。| 非管理员可以使用[访问包](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)管理其应用程序。|
|访问评审|可以定期评审用户对应用的访问权限，确保相应人员持续拥有访问权限。| [查看对最明感应用程序的访问权限](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。 |
|Log Analytics|生成有关谁正在访问哪些应用程序的报告，并将其存储在你选择的 SIEM 工具中，以关联数据源之间和一段时间之间的数据。| 启用[日志分析](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)并设置与应用程序相关的关键事件警报。 |


## <a name="scenario-5-hybrid-secure-access"></a>应用场景 5：混合安全访问
标识必须能够跨云和本地应用程序连接所有内容，才能成为控制平面。 请利用 Azure AD 及其合作伙伴提供的工具，来保护对基于旧版身份验证的应用程序的访问。

|Feature  |说明|建议 |
|---------|---------|---------|
|应用程序代理|现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地的公司应用。 Azure AD 应用程序代理支持这种强大的访问，而无需昂贵和复杂的虚拟专用网络 (VPN) 或外围子网 (DMZ)。|为本地应用设置[远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5、Akamai、Zscaler|使用现有网络和交付控制器，可以轻松保护对业务流程仍然至关重要但以前无法使用 Azure AD 进行保护的旧版应用程序。 就像你已经有了开始保护这些应用程序所需的一切。| 应使用 Akamai、Citrix、F5 还是 Zscaler？ 请查看我们的[预建解决方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相关文章

- [应用程序管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [应用程序预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合安全访问](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [标识治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [标识安全性](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
