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
ms.openlocfilehash: d7a596454a48a1d6fcee77634363dd38f34a4d58
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2020
ms.locfileid: "81603353"
---
# <a name="azure-active-directory-deployment-plans"></a>Azure Active Directory 部署计划
是否寻找有关部署 Azure 活动目录 （Azure AD） 功能的端到端指导？ Azure AD 部署计划将引导您完成成功部署常见 Azure AD 功能所需的业务价值、规划注意事项和操作过程。

从任何计划页面，使用浏览器的"打印到 PDF"功能创建文档的最新脱机版本。
## <a name="include-the-right-stakeholders"></a>包括合适的利益相关者

开始部署规划新功能时，必须在整个组织中包括关键利益干系人。 我们建议您识别并记录履行以下每个角色的人员，并与他们合作确定他们参与项目。  

角色可能包括以下内容 

|角色 |说明 |
|-|-|
|最终用户|将为其实施该功能的代表性用户组。 通常预览试验程序中的更改。
|IT 支持经理|IT 支持组织代表，他可以从帮助台的角度为此更改的支持性提供意见。  
|身份架构师或 Azure 全局管理员|负责定义此更改如何与组织中的核心身份管理基础结构保持一致的身份管理团队代表。|
|应用程序业务所有者 |受影响的应用程序的总体业务所有者，其中可能包括管理访问。还可以从最终用户的角度提供有关此更改的用户体验和有用性的意见。
|安全所有者|安全团队的代表，可以签署该计划将满足组织的安全要求。|
|合规性管理器|组织中负责确保符合公司、行业或政府要求的人员。|

**参与程度可能包括：**

- 执行项目计划和成果的**R** 

- **A**项目计划和成果的一个预测 

- **C**项目计划和结果的三元分项 

- **我**形成项目计划和结果


## <a name="best-practices-for-a-pilot"></a>飞行员的最佳做法
Pilot 允许您在为所有人打开功能之前，先使用一小群进行测试。 确保作为测试的一部分，对组织中的每个用例都进行了全面测试。 最好在将特定试点用户组推出给整个组织之前，先将其定位为特定一组试点用户。

在第一波中，针对 IT、可用性和其他可以测试和提供反馈的适当用户。 此反馈应用于进一步开发您发送给用户的通信和说明，并深入了解您的支持人员可能看到的问题类型。 

扩大向较大的用户群体应通过增加目标组的范围来进行。 这可以通过[动态组成员身份](../users-groups-roles/groups-dynamic-membership.md)或手动将用户添加到目标组来实现。


## <a name="deploy-authentication"></a>部署身份验证

| 功能 | 说明|
| -| -|
| [多重身份验证](../authentication/howto-mfa-getstarted.md)| Azure 多重身份验证 (MFA) 是 Microsoft 的双重验证解决方案。 使用管理员批准的身份验证方法，Azure MFA 可帮助保护对数据和应用程序的访问，同时满足对简单登录过程的需求。 |
| [条件访问](../conditional-access/plan-conditional-access.md)| 借助条件访问，您可以根据条件为谁可以访问云应用实施自动访问控制决策。 |
| [自助式密码重置](../authentication/howto-sspr-deployment.md)| 自助服务密码重置可帮助用户重置密码，而无需管理员干预，何时何地需要。 |
| [无密码](../authentication/howto-authentication-passwordless-deployment.md) | 使用组织中的 Microsoft 身份验证器应用或 FIDO2 安全密钥实现无密码身份验证 |

## <a name="deploy-application-management"></a>部署应用程序管理

| 功能 | 说明|
| -| - |
| [单一登录](../manage-apps/plan-sso-deployment.md)| 单一登录可帮助用户访问开展业务所需的应用和资源，而只需登录一次。 登录后，他们可以从 Microsoft Office 转到 SalesForce 到 Box 到内部应用程序，而无需再次输入凭据。 |
| [访问面板](../manage-apps/access-panel-deployment-plan.md)| 为你的用户提供一个简单的中心来发现和访问他们的所有应用程序。 通过自助服务功能（如请求访问应用和组或代表其他人管理对资源的访问权限）使他们能够提高工作效率。 |


## <a name="deploy-hybrid-scenarios"></a>部署混合方案

| 功能 | 说明|
| -| -|
| [使用 ADFS 进行密码哈希同步](../hybrid/plan-migrate-adfs-password-hash-sync.md)| 使用密码哈希同步，用户密码哈希从本地活动目录同步到 Azure AD，允许 Azure AD 对用户进行身份验证，而无需与本地活动目录交互 |
| [使用 ADFS 进行直通身份验证](../hybrid/plan-migrate-adfs-pass-through-authentication.md)| Azure AD 直通身份验证可帮助用户使用相同的密码登录到本地和基于云的应用程序。 此功能为用户提供了更好的体验 （少记住一个密码）并降低了 IT 帮助台成本，因为用户不太可能忘记如何登录。 当人们使用 Azure AD 登录时，此功能可直接通过本地 Active Directory 验证用户的密码。 |
| [Azure AD 应用程序代理](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-deployment-plan) |现今的员工想要随时随地都能在任何设备上高效工作。 他们需要访问云中的 SaaS 应用和本地的企业应用。 Azure AD 应用程序代理支持这种强大的访问，而无需昂贵和复杂的虚拟专用网络 （VPN） 或非军事区域 （DMZ）。 |
| [无缝 SSO](../hybrid/how-to-connect-sso-quick-start.md)| Azure Active Directory 无缝单一登录（Azure AD 无缝 SSO）可使连接到企业网络的企业设备上的用户自动登录。 使用此功能，用户无需键入密码才能登录到 Azure AD，也通常需要输入其用户名。 此功能使授权用户能够轻松访问基于云的应用程序，而无需任何其他本地组件。 |

## <a name="deploy-user-provisioning"></a>部署用户预配

| 功能 | 说明|
| -| -|
| [用户预配](../app-provisioning/plan-auto-user-provisioning.md)| 可以通过 Azure AD 自动创建、维护和删除云 (SaaS) 应用程序（如 Dropbox、Salesforce、ServiceNow 等）中的用户标识。 |
| [云 HR 用户预配](../app-provisioning/plan-cloud-hr-provision.md)| 云 HR 用户预配到 Active Directory 为持续的身份治理奠定了基础，并提高了依赖于权威标识数据的业务流程的质量。 将此功能与云 HR 产品（如工作日或成功因素）一起使用，您可以通过配置将 Joiner-Mover-Leaver 流程（如新员工、终止、转移）映射到 IT 预配操作（如创建、启用、禁用）的规则无缝管理员工和临时工作人员的身份生命周期 |

## <a name="deploy-governance-and-reporting"></a>部署治理和报告

| 功能 | 说明|
| -| -|
| [Privileged Identity Management](../privileged-identity-management/pim-deployment-plan.md)| Azure AD Privileged Identity Management (PIM) 可帮助跨 Azure AD、Azure 资源和其他 Microsoft 联机服务管理特权管理角色。 PIM 提供实时访问、请求审批工作流和完全集成的访问评审等解决方案，让用户可以实时识别、发现并防止特权角色的恶意活动。 |
| [报告和监测](../reports-monitoring/plan-monitoring-and-reporting.md)| Azure AD 报告和监视解决方案的设计取决于法律、安全性和操作要求以及现有环境和流程。 本文介绍了各种设计选项，并指导您制定正确的部署策略。 |
