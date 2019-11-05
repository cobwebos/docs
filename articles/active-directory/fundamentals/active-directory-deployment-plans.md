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
ms.date: 08/20/2019
ms.author: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 949676c622c87831d22dcfc7e1bc6d920b622738
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473279"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
正在寻找有关部署 Azure Active Directory （Azure AD）功能的端到端指南吗？ Azure AD 部署计划将引导你完成成功部署常见 Azure AD 功能所需的业务价值、规划注意事项和操作过程。

从任何计划页面，使用浏览器的打印到 PDF 功能创建文档的最新脱机版本。
## <a name="include-the-right-stakeholders"></a>包含正确的利益干系人

在开始规划新功能时，必须在组织中包含关键利益干系人。 建议你确定并记录满足以下各项角色的人员或人员，并与他们合作来确定他们在项目中的参与情况。  

角色可能包括以下 

|角色 |说明 |
|-|-|
|最终用户|将为其实施功能的代表用户组。 经常预览试点计划中的更改。
|IT 支持经理|IT 支持组织代表，他们可以从支持人员的角度提供此更改的可支持性的输入。  
|标识架构师或 Azure 全局管理员|标识管理团队代表定义此更改如何与组织中的核心标识管理基础结构保持一致。|
|应用程序企业所有者 |受影响的应用程序的总体业务所有者，其中可能包括管理访问权限。  还可以提供有关用户体验的输入，并从最终用户的角度来使用此更改。
|安全所有者|安全团队的代表，可以签署计划，以满足组织的安全要求。|
|合规性管理器|组织中负责确保符合公司、行业或政府要求的人员。|

**参与级别可能包括：**

- 用于实现项目计划和结果的**R**esponsible 

- 项目计划和结果**的 pproval** 

- **C**ontributor 到项目计划和结果 

- **我**nformed 项目计划和结果


## <a name="best-practices-for-a-pilot"></a>试验的最佳实践
通过试验，你可以在为每个人启用一种功能之前测试一个小组。 确保在测试过程中，组织内的每个用例都经过全面测试。 最好瞄准一组特定的试验用户，然后将其整体推广到你的组织。

在第一轮中，面向 IT、可用性和其他可测试和提供反馈的适当用户。 此反馈应该用于进一步开发您向用户发送的通信和说明，并深入了解支持人员可能会看到的问题类型。 

将部署扩展到更大的用户组应通过增加目标组的范围来执行。 这可以通过[动态组成员身份](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-dynamic-membership)来完成，或手动将用户添加到目标组。


## <a name="deploy-authentication"></a>部署身份验证

| 功能 | 说明|
| -| -|
| [多重身份验证](https://aka.ms/deploymentplans/mfa)| Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足用户对简单登录过程的需求。 |
| [条件性访问](https://aka.ms/deploymentplans/ca)| 使用条件性访问，你可以根据条件，实现可访问云应用的用户的自动访问控制决策。 |
| [自助密码重置](https://aka.ms/deploymentplans/sspr)| 自助密码重置功能可帮助用户在无需管理员干预的情况下重置密码。 |
| [无密码](https://aka.ms/deploymentplans/passwordless) | 使用组织中的 Microsoft Authenticator 应用或 FIDO2 安全密钥实现无密码 authentication |

## <a name="deploy-application-management"></a>部署应用程序管理

| 功能 | 说明|
| -| - |
| [单一登录](https://aka.ms/deploymentplans/sso)| 单一登录可帮助用户访问在只登录一次时需要执行业务的应用和资源。 登录后，他们可以从 Microsoft Office 到 SalesForce 到 Box，再到内部应用程序，而无需再次输入凭据。 |
| [访问面板](https://aka.ms/deploymentplans/accesspanel)| 为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 使用自助服务功能，例如请求访问应用和组，或者代表其他人管理对资源的访问权限，从而提高工作效率。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合方案

| 功能 | 说明|
| -| -|
| [使用 ADFS 进行密码哈希同步](https://aka.ms/deploymentplans/adfs2phs)| 通过密码哈希同步，用户密码的哈希将从本地 Active Directory 同步到 Azure AD，让 Azure AD 对不与本地的交互的用户进行身份验证 Active Directory |
| [使用 ADFS 进行直通身份验证](https://aka.ms/deploymentplans/adfs2pta)| Azure AD 传递身份验证可帮助用户使用相同的密码登录到本地和基于云的应用程序。 此功能为用户提供更好的体验-需要记住的密码越少，并且减少了 IT 支持人员的成本，因为用户不太可能忘记登录的方式。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。 |
| [Azure AD 应用程序代理](https://aka.ms/deploymentplans/appproxy)| 现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地公司应用。 Azure AD 应用程序代理可实现此强大的访问，而无需昂贵、复杂的虚拟专用网络（Vpn）或外围区域（Dmz）。 |
| [无缝 SSO](https://aka.ms/SeamlessSSODPDownload)| Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 利用此功能，用户无需键入密码即可登录 Azure AD，通常不需要输入用户名。 此功能为授权用户提供轻松访问基于云的应用程序的权限，而无需其他任何本地组件。 |

## <a name="deploy-user-provisioning"></a>部署用户预配

| 功能 | 说明|
| -| -|
| [用户预配](https://aka.ms/deploymentplans/userprovisioning)| 可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 |
| [Workday 驱动的入站用户预配](https://aka.ms/WorkdayDeploymentPlan)| Workday 驱动的到 Active Directory 的入站用户预配为正在进行的标识管理奠定了基础，并提高了依赖权威标识数据的业务流程的质量。 使用此功能，你可以通过配置将 Leaver 流程（如 "创建"、"终止"、"转移"）映射到 IT 预配操作（如 "创建"、"启用"、禁用 |

## <a name="deploy-governance-and-reporting"></a>部署管理和报告

| 功能 | 说明|
| -| -|
| [Privileged Identity Management](https://aka.ms/deploymentplans/pim)| Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。 |
| [报告和监视](https://aka.ms/deploymentplans/reporting)| Azure AD 报告和监视解决方案的设计取决于你的法律、安全和运营要求以及你的现有环境和过程。 本文介绍各种设计选项，并指导您正确部署策略。 |
