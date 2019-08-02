---
title: 部署计划 - Azure Active Directory | Microsoft Docs
description: 有关如何部署多个 Azure Active Directory 功能的端到端指南。
services: active-directory
author: msaburnley
manager: daveba
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: conceptual
ms.date: 02/08/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: a19c78efb7d81dcdcd3c221f58d4e4cc7be5ccd1
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68722195"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
正在查找有关如何部署某些 Azure Active Directory (Azure AD) 功能的端到端指南？ 以下部署计划详述了成功推出部分更常用 Azure AD 功能所需的业务价值、规划注意事项、设计和运营过程。 

在文档中，可以找到电子邮件模板、系统体系结构关系图、常见测试案例，等等。 

欢迎对文档提供反馈。 请参与这个简短的[调查](https://aka.ms/deploymentplanfeedback)，说出你对文档的看法。 

## <a name="include-the-right-stakeholders"></a>包含正确的利益干系人

在开始规划新功能时, 必须在组织中包含关键利益干系人。 建议你确定并记录满足以下各项角色的人员或人员, 并与他们合作来确定他们在项目中的参与情况。  

角色可能包括以下 

|Role |描述 |
|-|-|
|最终用户|将为其实施功能的代表用户组。 经常预览试点计划中的更改。
|IT 支持经理|IT 支持组织代表, 他们可以从支持人员的角度提供此更改的可支持性的输入。  
|标识架构师或 Azure 全局管理员|标识管理团队代表定义此更改如何与组织中的核心标识管理基础结构保持一致。|
|应用程序企业所有者 |受影响的应用程序的总体业务所有者, 其中可能包括管理访问权限。  还可以提供有关用户体验的输入, 并从最终用户的角度来使用此更改。
|安全所有者|安全团队的代表, 可以签署计划, 以满足组织的安全要求。|
|合规性管理器|组织中负责确保符合公司、行业或政府要求的人员。|

**参与级别可能包括:**

- 用于实现项目计划和结果的**R**esponsible 

- 项目计划和结果的 pproval 

- **C**ontributor 到项目计划和结果 

- **我**nformed 项目计划和结果
 
## <a name="deployment-plans"></a>部署计划



|应用场景 |描述 |
|-|-|
|[多重身份验证](../authentication/howto-mfa-getstarted.md)|Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。|
|[条件性访问](https://aka.ms/deploymentplans/ca)|使用条件性访问, 你可以根据条件, 实现可访问云应用的用户的自动访问控制决策。|
|[自助密码重置](https://aka.ms/deploymentplans/sspr)|自助密码重置允许用户在需要时重置其密码，不需要管理员干预。|
|[Privileged Identity Management](https://aka.ms/deploymentplans/pim)|Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。|
|[单一登录](https://aka.ms/deploymentplans/sso)|进行单一登录时，只需使用单个用户帐户登录一次，就能访问开展业务所需的全部应用和资源。 登录之后，可以从 Microsoft Office 转到 SalesForce，再转到 Box，无需再次进行身份验证（例如键入密码）。|
|[无缝 SSO](https://aka.ms/SeamlessSSODPDownload)|Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 启用此功能后，用户无需键入其密码即可登录到 Azure AD；通常情况下，甚至无需键入其用户名。 此功能可让用户轻松访问基于云的应用程序，而无需使用其他任何本地组件。|
|[访问面板](https://aka.ms/AccessPanelDPDownload)|为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能提高他们的工作效率，例如请求访问新应用和组的能力，或代表其他人管理对这些资源的访问。|
|[使用 ADFS 进行密码哈希同步](https://aka.ms/deploymentplans/adfs2phs)|有了密码哈希同步，就可以将用户密码的哈希从本地 Active Directory 同步到 Azure AD，让 Azure AD 对用户进行身份验证，不与本地 Active Directory 交互|
|[使用 ADFS 进行直通身份验证](https://aka.ms/deploymentplans/adfs2pta)|借助 Azure AD 直通身份验证证，你的用户可使用相同的密码登录到本地应用程序和基于云的应用程序。 此功能为用户提供更好的体验 - 少记一个密码，并且能减少 IT 支持人员成本，因为用户不太可能忘记如何登录。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。|
|[Azure AD 应用程序代理](https://aka.ms/deploymentplans/appproxy)|现今的员工想要随时随地都能在任何设备上高效工作。 他们想要在自己的设备上工作，无论设备是平板电脑、手机还是笔记本电脑。 员工期望能够访问其所有的应用程序：云中的 SaaS 应用和本地的公司应用。 传统上，提供本地应用程序访问权限会涉及虚拟专用网络 (VPN) 或外围网络 (DMZ)。 这些解决方案不仅复杂且难以确保安全性，而且设置和管理成本也很高。 还有更好的办法！ - Azure AD 应用程序代理|
|[用户预配](https://aka.ms/UserProvisioningDPDownload)|可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。|
|[Workday 驱动的入站用户预配](https://aka.ms/WorkdayDeploymentPlan)|Workday 驱动的到 Active Directory 的入站用户预配为正在进行的标识管理奠定了基础，并提高了依赖权威标识数据的业务流程的质量。 使用此功能，可以通过配置将 Joiner-Mover-Leaver 流程（例如新雇佣、终止、转移）映射到 IT 预配操作（例如创建、启用、禁用、删除帐户等）的规则，无缝地管理员工和临时工的标识生命周期。|
|[报告和监视](https://aka.ms/deploymentplans/reporting)| Azure AD 报告和监视解决方案的设计取决于你的法律、安全和运营要求以及你的现有环境和过程。 本文介绍各种设计选项, 并指导您正确部署策略。|
