---
title: Azure 活动目录的常见应用程序管理方案 |微软文档
description: 使用 Azure AD 集中应用程序管理
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/02/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: d21ff820470765b82e397e56a2458603b8e5a7c7
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80657944"
---
# <a name="centralize-application-management-with-azure-ad"></a>使用 Azure AD 集中应用程序管理

密码，既是 IT 噩梦，也是全球员工的痛苦。 这就是为什么越来越多的公司转向 Azure 活动目录，这是 Microsoft 云和所有其他资源的标识和访问管理解决方案。 从应用程序跳转到应用程序，而无需为每个应用程序输入密码。 快速、快速、安全地从 Outlook 跳到工作日到 ADP。 然后，无需致电 IT，即可与合作伙伴甚至组织外部的其他人协作。 此外，Azure AD 还通过保护您使用的应用（如多重身份验证）来验证您是谁，使用连续自适应机器学习和安全智能来检测可疑登录，从而帮助您安全地访问所需的应用，从而管理风险，无论您身在何处。 它不仅对用户都很好，对 IT 也大有用。 借助及时的访问审核和全面的治理套件，Azure AD 还可以帮助您保持合规性并强制实施策略。 并且，您甚至可以自动预配用户帐户，使访问管理变得轻松。 查看客户使用 Azure 活动目录的应用程序管理功能的一些常见方案。

**常见方案**


> [!div class="checklist"]
> * 适用于所有应用程序的 SSO
> * 自动预配和取消预配 
> * 保护您的应用程序
> * 管理对应用程序的访问
> * 混合安全访问

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>方案 1：为所有应用程序设置 SSO

无需再管理密码。 使用公司凭据安全地访问您需要的所有资源。 

|Feature  | 说明 | 建议 |
|---------|---------|---------|
|SSO|使用受信任的行业标准，基于标准的联合 SSO。|始终使用[SAML / OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation)在应用程序支持 SSO 时启用 SSO。|
|访问面板|为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能（如请求访问应用和组或代表其他人管理对资源的访问权限）使他们能够提高工作效率。| 将应用与 SSO 的 Azure AD 集成后，在组织中部署[访问面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>方案 2：自动预配和取消预配 


大多数应用程序要求用户在访问所需的资源之前预配到应用程序中。 使用 CSV 文件或复杂的脚本可能成本高昂且难以管理。 此外，客户需要确保在某人不应再具有访问权限时删除帐户。 利用以下工具自动调配和取消预配。 


|Feature  |说明|建议 |
|---------|---------|---------|
|SCIM 预配|[SCIM](https://aka.ms/SICMOverview)是自动化用户预配的行业最佳实践。 任何符合 SCIM 的应用程序都可以与 Azure AD 集成。 自动创建、更新和删除用户帐户，而无需维护 CSV 文件、自定义脚本或预处理解决方案。|查看 Azure AD 应用库中不断增长的[预集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)应用列表|
|Microsoft Graph|利用 Azure AD 所需的数据呼吸和深度，使用所需的数据丰富应用程序。|利用[Microsoft 图形](https://developer.microsoft.com/graph/)从整个 Microsoft 生态系统获取数据。 |


## <a name="scenario-3-secure-your-applications"></a>方案 3：保护您的应用程序
身份是安全的关键。 如果身份遭到破坏，在为时已晚之前阻止多米诺骨牌效应是非常困难的。 平均而言超过100天，组织才发现存在妥协。 使用 Azure AD 提供的工具改善应用程序的安全状态。 

|Feature  |说明| 建议 |
|---------|---------| ---------|
|Azure MFA|Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。| 为用户[启用 MFA。](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124)  |
|条件性访问|借助条件访问，您可以根据条件为谁可以访问云应用实施自动访问控制决策。| 查看客户正在使用[的安全默认值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)[和常见策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|标识保护|标识保护利用 Microsoft 从 Azure AD 组织、Microsoft 帐户中的用户群以及 Xbox 游戏中获得的自身经验来保护用户。 Microsoft 每天分析 6.5 万亿条信号，以识别威胁并保护客户安全。|启用我们的服务提供的[默认身份保护策略](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>方案 4：控制对应用程序的访问
身份治理可帮助组织在工作效率之间取得平衡 - 一个人访问他们需要的应用程序的速度有多快，例如当他们加入我的组织时？ 安全性 - 用户的访问权限会不断发生怎样的变化（例如，由于该用户的雇佣状态发生变化）？ 

|Feature  |说明|建议 |
|---------|---------| ---------|
|榆树|Azure AD 授权管理可帮助组织内外的用户更有效地管理对其应用程序的访问。| 允许非管理员使用[访问包](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)管理访问其应用程序。|
|访问评审|可以定期审查用户对应用的访问权限，以确保只有合适的人员才能继续访问。| [查看](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)对最敏感应用程序的访问权限。 |
|Log Analytics|生成有关谁正在访问哪些应用程序的报告，并将其存储在您选择的 SIEM 工具中，以关联数据源之间的数据并随时间而关联。| 启用[日志分析](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics)，并为与应用程序相关的关键事件设置警报。 |


## <a name="scenario-5-hybrid-secure-access"></a>方案 5：混合安全访问
仅当标识能够跨云和本地应用程序连接所有内容时，身份才能成为您的控制平面。 利用 Azure AD 及其合作伙伴提供的工具，确保对基于旧版的应用程序的访问。

|Feature  |说明|建议 |
|---------|---------|---------|
|应用程序代理|现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地的企业应用。 Azure AD 应用程序代理支持这种强大的访问，而无需昂贵和复杂的虚拟专用网络 （VPN） 或非军事区域 （DMZ）。|为预安装应用设置[远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5， Akamai， 兹卡尔|使用现有网络和交付控制器，可以轻松保护对业务流程仍然至关重要但以前无法使用 Azure AD 进行保护的旧版应用程序。 就像你已经有了开始保护这些应用程序所需的一切。| 使用 Akamai、Citrix、F5 还是 Zscaler？ 查看我们的[预构建解决方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相关文章

- [应用程序管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [应用程序预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合安全访问]()
- [标识治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [身份安全](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
