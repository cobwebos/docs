---
title: Azure Active Directory 的常见应用程序管理方案 |Microsoft Docs
description: 利用 Azure AD 集中管理应用程序
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
ms.openlocfilehash: 1874a2f2cf96aaa905616bddcc6cb83c60c1d279
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115602"
---
# <a name="centralize-application-management-with-azure-ad"></a>利用 Azure AD 集中管理应用程序

密码，这两个人都非常头痛，并为世界各地的员工提供难点。 这就是越来越多的公司转向 Azure Active Directory、Microsoft 的标识和访问管理解决方案以及所有其他资源的原因。 从应用程序跳转到应用程序，而无需为每个应用程序输入一个密码。 从 Outlook 立即跳转到 Workday，并快速、安全地打开它们。 然后，与合作伙伴甚至组织外的其他人协作，无需调用它。 此外，Azure AD 可通过保护你使用的应用程序（例如多重身份验证）来验证你的身份，从而帮助管理风险，这种情况下，使用持续自适应机器学习和安全智能来检测可疑登录，使你无论身在何处，都能安全访问所需的应用。 这不仅适用于用户，而且还适用于用户。 使用实时访问评审和完整的规模调控套件，Azure AD 可帮助你保持合规性并强制实施策略。 为此，您甚至可以自动预配用户帐户，使访问管理变得轻而易举。 查看客户使用 Azure Active Directory 的应用程序管理功能的一些常见方案。

**常见方案**


> [!div class="checklist"]
> * 适用于所有应用程序的 SSO
> * 自动预配和取消预配 
> * 保护应用程序
> * 控制对应用程序的访问
> * 混合安全访问

## <a name="scenario-1-set-up-sso-for-all-your-applications"></a>方案1：为所有应用程序设置 SSO

无更多管理密码。 使用公司凭据安全地访问所需的所有资源。 

|功能  | 说明 | 建议 |
|---------|---------|---------|
|SSO|使用受信任行业标准的基于标准的联合 SSO。|当应用程序支持 SSO 时，请始终使用[SAML/OIDC](https://docs.microsoft.com/azure/active-directory/manage-apps/isv-choose-multi-tenant-federation)启用 SSO。|
|访问面板|为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 使用自助服务功能，例如请求访问应用和组，或者代表其他人管理对资源的访问权限，从而提高工作效率。| 在将应用集成到 SSO 的 Azure AD 后，在你的组织中部署[访问面板](https://docs.microsoft.com/azure/active-directory/manage-apps/access-panel-deployment-plan)。|

## <a name="scenario-2-automate-provisioning-and-deprovisioning"></a>方案2：自动预配和取消预配 


大多数应用程序需要将用户预配到应用程序中，然后才能访问所需的资源。 使用 CSV 文件或复杂脚本可能会成本高昂且难以管理。 而且，客户需要确保在有人不应访问帐户时删除帐户。 利用以下工具自动进行预配和取消预配。 


|功能  |说明|建议 |
|---------|---------|---------|
|SCIM 预配|[SCIM](https://aka.ms/SCIMOverview)是自动化用户预配的业界最佳方案。 任何符合 SCIM 的应用程序都可以与 Azure AD 集成。 自动创建、更新和删除用户帐户，而无需维护 CSV 文件、自定义脚本或本地解决方案。|查看 Azure AD 应用库中不断增长的[预先集成](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)的应用列表|
|Microsoft Graph|利用 Azure AD 的数据的深呼吸和深度，使应用程序能够使用它所需的数据来丰富应用程序。|利用[microsoft graph](https://developer.microsoft.com/graph/)获取跨 Microsoft 生态系统的数据。 |


## <a name="scenario-3-secure-your-applications"></a>方案3：保护应用程序
标识是用于安全的作为。 如果某个标识被泄露，则很难在过多 domino 效果太晚时停止它。 超过100天后，组织会发现有一个折衷。 使用 Azure AD 提供的工具来改善应用程序的安全状况。 

|功能  |说明| 建议 |
|---------|---------| ---------|
|Azure MFA|Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。| 为用户[启用 MFA](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/all-your-creds-are-belong-to-us/ba-p/855124) 。  |
|条件性访问|使用条件性访问，你可以根据条件，实现可访问云应用的用户的自动访问控制决策。| 查看[安全默认值](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults)和客户使用的[常见策略](https://docs.microsoft.com/azure/active-directory/conditional-access/concept-conditional-access-policy-common)。 | 
|标识保护|标识保护利用 Microsoft 从 Azure AD 组织、Microsoft 帐户中的用户群以及 Xbox 游戏中获得的自身经验来保护用户。 Microsoft 每天分析 6.5 万亿条信号，以识别威胁并保护客户安全。|启用服务提供的[默认 identity protection 策略](https://docs.microsoft.com/azure/active-directory/identity-protection/concept-identity-protection-policies)。 | 

## <a name="scenario-4-govern-access-to-your-applications"></a>方案4：控制对应用程序的访问
标识调控可帮助组织实现工作效率之间的平衡-用户有权访问所需的应用程序（例如，在加入组织时）？ 安全性 - 用户的访问权限会不断发生怎样的变化（例如，由于该用户的雇佣状态发生变化）？ 

|功能  |说明|建议 |
|---------|---------| ---------|
|ELM|Azure AD 的权利管理可帮助组织内部和外部的用户更有效地管理其应用程序的访问权限。| 允许非管理员管理使用[access 包](https://docs.microsoft.com/azure/active-directory/governance/entitlement-management-access-package-first)访问其应用程序。|
|访问评审|可以定期查看用户对应用的访问权限，以确保只有正确的人员才能继续访问。| 查看对最敏感应用程序的[访问权限](https://docs.microsoft.com/azure/active-directory/governance/access-reviews-overview)。 |
|Log Analytics|生成有关谁正在访问哪些应用程序并将其存储在所选的 SIEM 工具中的报告，以便在数据源和一段时间之间关联数据。| 启用[log analytics](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) ，并为与应用程序相关的关键事件设置警报。 |


## <a name="scenario-5-hybrid-secure-access"></a>方案5：混合安全访问
标识只能是控制平面，因为它可以跨云和本地应用程序连接所有内容。 利用 Azure AD 及其合作伙伴提供的工具来保护对基于旧身份验证的应用程序的访问。

|功能  |说明|建议 |
|---------|---------|---------|
|应用程序代理|现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地公司应用。 Azure AD 应用程序代理可实现此强大的访问，而无需昂贵、复杂的虚拟专用网络（Vpn）或外围区域（Dmz）。|为本地应用设置[远程访问](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)。 |
|F5、Akamai、Zscaler|使用现有网络和交付控制器，可以轻松保护对业务流程仍然至关重要但以前无法使用 Azure AD 进行保护的旧版应用程序。 就像你已经有了开始保护这些应用程序所需的一切。| 使用 Akamai、Citrix、F5 或 Zscaler？ 请查看我们[的预建解决方案](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)。 | 

## <a name="related-articles"></a>相关文章

- [应用程序管理](https://docs.microsoft.com/azure/active-directory/manage-apps/index)
- [应用程序预配](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning)
- [混合安全访问](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
- [标识治理](https://docs.microsoft.com/azure/active-directory/governance/identity-governance-overview)
- [Microsoft 标识平台](https://docs.microsoft.com/azure/active-directory/develop/v2-overview)
- [标识安全](https://docs.microsoft.com/azure/active-directory/conditional-access/index)
