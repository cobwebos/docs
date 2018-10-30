---
title: Azure Active Directory 中的条件访问是什么？ | Microsoft Docs
description: 了解如何通过 Azure Active Directory 中的条件访问来实现自动访问决策，此类决策不仅取决于尝试访问资源的人，也取决于访问资源的方式。
services: active-directory
keywords: 对应用的条件性访问, 使用 Azure AD 进行条件性访问, 保护对公司资源的访问, 条件性访问策略
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: ''
ms.assetid: 8c1d978f-e80b-420e-853a-8bbddc4bcdad
ms.service: active-directory
ms.component: conditional-access
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: 77bccaeec1ad3f0d2df4ab567d294c77fc2358cb
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/24/2018
ms.locfileid: "49954719"
---
# <a name="what-is-conditional-access-in-azure-active-directory"></a>Azure Active Directory 中的条件访问是什么？

安全是使用云的组织关心的头等大事。 云安全在管理云资源方面的一个重要方面是标识和访问。 在移动优先、云优先的世界，用户可以从任意位置使用各种设备和应用访问组织的资源。 因此，仅关注谁可以访问资源不再能满足需求。 为了掌握安全与效率之间的平衡，还需将资源的访问方式作为访问控制决策的考虑因素。 使用 Azure Active Directory (Azure AD) 条件访问便可处理该需求。 条件访问是 Azure Active Directory 的一项功能。 使用条件访问时，可以根据条件就云应用的访问实施自动化的访问控制决策。 

![控制](./media/overview/81.png)

本文从概念上概述了 Azure AD 中的条件访问。



## <a name="common-scenarios"></a>常见方案

在移动优先、云优先的世界中，使用 Azure Active Directory 可以实现从任意位置单一登录到设备、应用和服务。 随着包含 BYOD 在内的设备、脱离企业网络的办公和第三方 SaaS 应用的普及，你面临着两个对立的目标：

- 使用户能够随时随地保持高效的工作
- 随时保持企业资产

通过使用条件访问策略，用户可以根据所需的条件应用适当的访问控制。 Azure AD 条件访问可为你在需要时提供增强的安全性，并在不需要时毫不阻碍用户。 

以下是条件访问可帮助你的部分常见访问问题：



- [登录风险](conditions.md#sign-in-risk)：Azure AD Identity Protection 可检测登录风险。 如果检测到的登录风险指示可疑操作者，如何限制访问？ 如果想要获取更有力的证据，证明登录是由合法用户执行的，该怎么办？ 如果你十分怀疑特定的用户，以至于想要阻止其访问某个应用，该怎么办？  

- [网络位置](location-condition.md)：Azure AD 可从任意位置进行访问。 如果试图从不在 IT 部门控制范围内的网络位置访问，应怎么办？ 用户名和密码的组合可能足以作为从公司网络进行访问尝试的身份证据。 如果从其他意外的国家或地区发起的访问尝试要求更有力的身份证据，应怎么办？ 如果想要阻止从某些位置发起的访问尝试，应怎么办？  

- [设备管理](conditions.md#device-platforms)：在 Azure AD 中，用户可以从大量设备（包括移动设备和个人设备）访问云应用。 如果要求只能使用 IT 部门管理的设备执行访问尝试，应怎么办？ 如果要阻止特定设备类型访问环境中的云应用，应怎么办？ 

- [客户端应用程序](conditions.md#client-apps)：现在可以使用各种应用（例如基于 Web 的应用、移动应用或桌面应用）访问许多云应用。 如果使用导致已知问题的客户端应用类型执行访问尝试，应怎么办？ 如果需要由 IT 部门管理的特定应用类型的设备，应怎么办？ 

这些问题和相关答案表示 Azure AD 条件访问的常见访问方案。 条件访问是 Azure Active Directory 的一项功能，可使你使用基于策略的方法处理访问方案。


## <a name="conditional-access-policies"></a>条件访问策略

条件访问策略定义了使用以下模式的访问方案：

![控制](./media/overview/10.png)

**然后执行此操作**指定策略的响应。 请务必注意，条件访问策略的目标不是向云应用授予访问权限。 在 Azure AD 中，向云应用授予访问权限是用户指定的主题。 使用条件访问策略，你可以控制授权用户（已获得访问云应用权限的用户）在特定条件下访问云应用的方式。 在响应中，你将强制执行其他要求，如多重身份验证、托管设备等。 在 Azure AD 条件访问上下文中，策略强制执行的要求称为访问控制。 在限制性最强的窗体中，策略可阻止访问。 有关详细信息，请参阅 [Azure Active Directory 条件访问中的访问控制](controls.md)。
     

**发生这种情况时**定义触发策略的原因。 此原因以已满足的一组条件为特征。 在 Azure AD 条件访问中，这两个指定条件扮演了特殊的角色：

- [用户](conditions.md#users-and-groups)：执行访问尝试的用户（人物）。 

- [云应用](conditions.md#cloud-apps)：访问尝试的目标（事件）。    

这两个条件在条件访问策略中是必需的。 除这两个必需的条件外，还可加入介绍访问尝试执行方式的其他条件。 常见示例是使用公司网络外的移动设备或位置。 有关详细信息，请参阅 [Azure Active Directory 条件访问中的条件](conditions.md)。   

条件与访问控制的组合表示一种条件访问策略。 

![控制](./media/overview/51.png)

使用 Azure AD 条件访问，可以控制授权用户访问云应用的方式。 条件访问的目标是对某种云应用的访问尝试强制执行其他访问控制，该云应用基于访问尝试的执行方式。

使用基于策略的方法保护对云应用的访问时，可使用本文中概述的结构起草环境的策略要求，而无需担心技术实现。 


## <a name="azure-ad-conditional-access-and-federated-authentication"></a>Azure AD 条件访问和联合身份验证

条件访问策略可以与[联合身份验证](../../security/azure-ad-choose-authn.md#federated-authentication)无缝地配合使用。 此支持包括所有支持的条件和控制，可以用来通过 [Azure AD 报表](../reports-monitoring/concept-sign-ins.md)了解策略是如何应用到活动用户登录的。

*使用 Azure AD 进行的联合身份验证*是指由受信任的身份验证服务处理用户通过 Azure AD 进行的身份验证。 举例来说，受信任的身份验证服务是指 Active Directory 联合身份验证服务 (AD FS) 或任何其他的联合身份验证服务。 在此配置中，主用户身份验证会在服务中执行，然后会使用 Azure AD 登录到各个应用程序中。 在将访问权限授予用户访问的应用程序之前，会应用 Azure AD 条件访问。 

当配置的条件访问策略要求多重身份验证时，Azure AD 会默认设置为使用 Azure MFA。 如果使用联合身份验证服务进行 MFA，则可对 Azure AD 进行配置，以便在需要 MFA 时重定向到联合身份验证服务，方法是在 [PowerShell](https://docs.microsoft.com/powershell/module/msonline/set-msoldomainfederationsettings) 中将 `-SupportsMFA` 设置为 `$true`。 此设置适用的联合身份验证服务支持由 Azure AD 通过 `wauth= http://schemas.microsoft.com/claims/multipleauthn` 发出的 MFA 质询请求。

在用户登录到联合身份验证服务以后，Azure AD 会处理其他策略要求，例如设备符合性或批准的应用程序。

## <a name="license-requirements-for-using-conditional-access"></a>使用条件访问的许可证要求

使用条件访问需要 Azure AD Premium 许可证。 若要根据需要查找合适的许可证，请参阅[比较免费版、基本版和高级版的正式发布功能](https://azure.microsoft.com/pricing/details/active-directory/)。


## <a name="next-steps"></a>后续步骤

- 如需深入了解：
    - 条件，请参阅 [Azure Active Directory 条件访问中的条件](conditions.md)。

    - 访问控制，请参阅 [Azure Active Directory 条件访问中的访问控制](controls.md)。

- 若要体验一下如何配置条件访问策略，请参阅[通过 Azure Active Directory 条件访问要求特定应用进行多重身份验证](app-based-mfa.md)。

- 如果已准备好配置环境的条件访问策略，请参阅 [Azure Active Directory 中条件访问的最佳做法](best-practices.md)。 

- 如果想要使用推荐策略的分步部署计划，请参阅[条件访问部署计划](http://aka.ms/conditionalaccessdeploymentplan)
