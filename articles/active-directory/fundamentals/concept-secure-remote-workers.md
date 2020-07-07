---
title: 使用 Azure Active Directory 快速响应以保护标识
description: 使用基于 Azure AD 云的标识快速响应威胁
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 713afb7b277fba65dc4c860e8bdd6b62b4e0147d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "82204944"
---
# <a name="rapidly-respond-to-secure-identities-with-azure-ad"></a>使用 Azure AD 快速响应以保护标识

在当今世界，尝试保护员工可能是件令人颇感头痛的事情，尤其是在需要快速响应并迅速提供对多种服务的访问时。 本文旨在提供一份简明列表，列出所有要采取的措施，从而帮助你根据所拥有许可证的类型来识别要部署的 Azure AD 功能的顺序并确定其优先级。 Azure AD 具备许多功能并为标识提供多层安全性，所以要找出相关功能有时会令人不知所措。 许多组织已在云中或正在快速向云中迁移，本文档旨在说明如何在优先考虑保护标识的情况下快速部署服务。 

每张表格都提供了一致的安全建议，从而保护管理员标识和用户标识都免受主要安全攻击（安全漏洞重播、网络钓鱼和密码喷射）的危害，同时尽量减小用户受到的影响并改善用户体验。 

本指南还将允许管理员以安全且受保护的方式配置对 SaaS 和本地应用程序的访问权限，并且适用于云或混合（已同步）标识，适用于远程或在办公室工作的用户。

此查检表有助于快速部署关键的建议措施，立即为组织提供保护。具体措施包括：

- 增强凭据。
- 缩小受攻击面。
- 自动化威胁响应。
- 利用云智能。
- 启用最终用户自助服务。

## <a name="prerequisites"></a>先决条件

本指南假定已在 Azure AD 中建立了云专用标识或混合标识。 有关选择标识类型的帮助，请参阅文章为[Azure Active Directory 混合标识解决方案选择正确的身份验证方法](../hybrid/choose-ad-authn.md) 

## <a name="summary"></a>摘要

安全的标识基础结构涉及许多方面，但此核对清单侧重于安全可靠的、能够让用户以远程方式工作的标识基础结构。 保护标识只是安全工作范畴中的一部分，还要考虑保护数据、应用程序和设备。

### <a name="guidance-for-azure-ad-free-or-office-365-customers"></a>用于 Azure AD Free 或 Office 365 客户的指南。

有很多建议可供 Azure AD Free 或 Office 365 应用的客户采纳以保护其用户标识，下表旨在重点介绍有关以下许可证订阅的关键措施：

- Office 365（O365 E1、E3、E5、F1、A1、A3、A5）
- Office 365 商业版（商业协作版、商业版、商业高级版）
- Microsoft 365（M365 商业版、A1）
- Azure AD Free（随 Azure、Dynamics 365、Intune 和 Power Platform 提供）

| 建议的操作 | 详细信息 |
| --- | --- |
| [启用安全默认值](concept-fundamentals-security-defaults.md) | 通过启用 MFA 和阻止旧身份验证来保护所有用户身份和应用程序 |
| [启用密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)（如果使用混合标识） | 为身份验证提供冗余并提高安全性（包括智能锁定、IP 锁定以及发现凭据泄露的功能） |
| [启用 ADFS 智能锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果适用） | 保护用户免受恶意活动导致的 Extranet 帐户锁定。 |
| [启用 Azure Active Directory 智能锁定](../authentication/howto-password-smart-lockout.md)（如果使用托管标识） | 智能锁定有助于锁定尝试猜测用户密码或使用强制方法获取的不良执行组件。 |
| [禁用应用程序的最终用户许可](../manage-apps/configure-user-consent.md) | 管理员许可工作流为管理员提供了一种安全的方法来向需要管理员批准的应用程序授予访问权限，因此最终用户不会公开公司数据。 Microsoft 建议禁用将来的所有用户许可操作有助于减小受攻击面并缓解此风险。 |
| [将支持的 SaaS 应用程序从库集成到 Azure AD 并启用单一登录](../manage-apps/add-application-portal.md) | Azure AD 的某个库包含数千个预先集成的应用程序。 组织使用的某些应用程序可能就在该库中。可通过 Azure 门户直接访问该库。 通过改进的用户体验（SSO）提供对企业 SaaS 应用程序的远程访问安全 |
| [从 SaaS 应用程序自动化用户预配和取消设置](../app-provisioning/user-provisioning.md)（如果适用） | 自动创建用户需要访问的云（SaaS）应用程序中的用户标识和角色。 除了创建用户身份外，自动预配还包括将用户标识作为状态或角色的更改进行维护和删除，从而提高组织的安全性。 |
| [启用安全混合访问：使用现有的应用传递控制器和网络保护旧应用](../manage-apps/secure-hybrid-access.md)（如果适用） | 通过将本地和云旧式身份验证应用程序连接到现有的应用程序传送控制器或网络 Azure AD 来发布和保护这些应用程序。 |
| [启用自助式密码重置](../authentication/tutorial-enable-sspr.md)（适用于云专用帐户） | 此功能可减少用户无法登录其设备或应用程序时呼叫支持人员的次数以及生产效率的损失。 |
| [尽可能使用非全局管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 只为管理员分配他们必须访问的区域的访问权限。 并非所有管理员都需要是全局管理员。 |
| [启用 Microsoft 的密码指导](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求用户按照设置的计划更改其密码，禁用复杂性要求，用户更倾向于记住他们习惯的密码，并妥善保管其密码。 |


### <a name="guidance-for-azure-ad-premium-plan-1-customers"></a>用于 Azure AD Premium 计划 1 客户的指南。

下表旨在重点介绍用于以下许可证订阅的关键措施：

- Azure Active Directory Premium P1 (Azure AD P1)
- 企业移动性 + 安全性 (EMS E3)
- Microsoft 365（M365 E3、A3、F1、F3）

| 建议的操作 | 详细信息 |
| --- | --- |
| [启用 Azure MFA 和 SSPR 的组合注册体验，简化用户注册体验](../authentication/howto-registration-mfa-sspr-combined.md) | 允许用户从 Azure 多重身份验证和自助式密码重置的一个常用体验进行注册。 |
| [为组织配置 MFA 设置](../authentication/howto-mfa-getstarted.md) | 使用多重身份验证确保帐户免受危害 |
| [启用自助服务密码重置](../authentication/tutorial-enable-sspr.md) | 此功能可减少用户无法登录其设备或应用程序时呼叫支持人员的次数以及生产效率的损失 |
| [实现密码写回](../authentication/tutorial-enable-sspr-writeback.md)（如果使用混合标识） | 允许将云中的密码更改写回到本地 Windows Server Active Directory 环境。 |
| 创建和启用条件访问策略 | [为管理员配置 MFA，以保护分配了管理员权限的帐户。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [阻止旧式身份验证协议，因为与旧式身份验证协议相关的风险日益增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [为所有用户和应用程序配置 MFA，以便为环境创建平衡的 MFA 策略，从而保护用户和应用程序。](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) <br><br> [要求为 Azure 管理配置 MFA，从而通过对任何访问 Azure 资源的用户都要求多重身份验证来保护特权资源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [启用密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)（如果使用混合标识） | 为身份验证提供冗余并提高安全性（包括智能锁定、IP 锁定以及发现凭据泄露的功能） |
| [启用 ADFS 智能锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果适用） | 保护用户免受恶意活动导致的 Extranet 帐户锁定。 |
| [启用 Azure Active Directory 智能锁定](../authentication/howto-password-smart-lockout.md)（如果使用托管标识） | 智能锁定有助于锁定尝试猜测用户密码或使用强制方法获取的不良执行组件。 |
| [禁用应用程序的最终用户许可](../manage-apps/configure-user-consent.md) | 管理员许可工作流为管理员提供了一种安全的方法来向需要管理员批准的应用程序授予访问权限，因此最终用户不会公开公司数据。 Microsoft 建议禁用将来的所有用户许可操作有助于减小受攻击面并缓解此风险。 |
| [允许使用应用程序代理远程访问本地旧版应用程序](../manage-apps/application-proxy-add-on-premises-application.md) | 启用 Azure AD 应用程序代理，并与旧应用集成，使用户能够通过使用 Azure AD 帐户登录来安全地访问本地应用程序。 |
| [启用安全混合访问：使用现有的应用传递控制器和网络保护旧应用](../manage-apps/secure-hybrid-access.md)（如果适用）。 | 通过将本地和云旧式身份验证应用程序连接到现有的应用程序传送控制器或网络 Azure AD 来发布和保护这些应用程序。 |
| [将支持的 SaaS 应用程序从库集成到 Azure AD 并启用单一登录](../manage-apps/add-application-portal.md) | Azure AD 的某个库包含数千个预先集成的应用程序。 组织使用的某些应用程序可能就在该库中。可通过 Azure 门户直接访问该库。 通过改进的用户体验（SSO），以远程和安全的方式提供对企业 SaaS 应用程序的访问权限。 |
| [从 SaaS 应用程序自动化用户预配和取消设置](../app-provisioning/user-provisioning.md)（如果适用） | 自动创建用户需要访问的云（SaaS）应用程序中的用户标识和角色。 除了创建用户身份外，自动预配还包括将用户标识作为状态或角色的更改进行维护和删除，从而提高组织的安全性。 |
| [启用条件性访问–基于设备](../conditional-access/require-managed-devices.md) | 使用基于设备的条件访问来改进安全性和用户体验。 此步骤可确保用户只能从满足安全和合规性标准的设备进行访问。 这些设备也称为受管理设备。 托管设备可以是与 Intune 兼容的设备，也可以是混合 Azure AD 加入的设备。 |
| [启用密码保护](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止用户使用弱密码和轻松猜测密码。 |
| [指定多个全局管理员](../users-groups-roles/directory-emergency-access.md) | 至少分配两个仅限云的永久性全局管理员帐户，以便在紧急情况下使用。 这些帐户不是每日使用，应该具有复杂的长密码。 Break Glass 帐户确保你可在紧急情况下访问该服务。 |
| [尽可能使用非全局管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 只为管理员分配他们必须访问的区域的访问权限。 并非所有管理员都需要是全局管理员。 |
| [启用 Microsoft 的密码指导](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求用户按照设置的计划更改其密码，禁用复杂性要求，用户更倾向于记住他们习惯的密码，并妥善保管其密码。 |
| [针对来宾用户访问权限创建计划](../b2b/what-is-b2b.md) | 通过让来宾用户使用其自己的工作、学校或社交标识登录应用和服务，实现与来宾用户的协作。 |

### <a name="guidance-for-azure-ad-premium-plan-2-customers"></a>用于 Azure AD Premium 计划 2 客户的指南。

下表旨在重点介绍用于以下许可证订阅的关键措施：

- Azure Active Directory Premium P2 (Azure AD P2)
- 企业移动性 + 安全性 (EMS E5)
- Microsoft 365（M365 E5、A5）

| 建议的操作 | 详细信息 |
| --- | --- |
| [启用 Azure MFA 和 SSPR 的组合注册体验，简化用户注册体验](../authentication/howto-registration-mfa-sspr-combined.md) | 允许用户从 Azure 多重身份验证和自助式密码重置的一个常用体验进行注册。 |
| [为组织配置 MFA 设置](../authentication/howto-mfa-getstarted.md) | 使用多重身份验证确保帐户免受危害 |
| [启用自助服务密码重置](../authentication/tutorial-enable-sspr.md) | 此功能可减少用户无法登录其设备或应用程序时呼叫支持人员的次数以及生产效率的损失 |
| [实现密码写回](../authentication/tutorial-enable-sspr-writeback.md)（如果使用混合标识） | 允许将云中的密码更改写回到本地 Windows Server Active Directory 环境。 |
| [启用 Identity Protection 策略以强制执行 MFA 注册](../identity-protection/howto-identity-protection-configure-mfa-policy.md) | 管理 Azure 多重身份验证（MFA）的推出。 |
| [启用标识保护用户和登录风险策略](../identity-protection/howto-identity-protection-configure-risk-policies.md) | 启用标识保护用户和登录策略。 建议的登录策略是针对中型风险登录，并要求进行 MFA。 对于用户策略，它应以要求密码更改操作的高风险用户为目标。 |
| 创建和启用条件访问策略 | [为管理员配置 MFA，以保护分配了管理员权限的帐户。](../conditional-access/howto-conditional-access-policy-admin-mfa.md) <br><br> [阻止旧式身份验证协议，因为与旧式身份验证协议相关的风险日益增加。](../conditional-access/howto-conditional-access-policy-block-legacy.md) <br><br> [要求为 Azure 管理配置 MFA，从而通过对任何访问 Azure 资源的用户都要求多重身份验证来保护特权资源。](../conditional-access/howto-conditional-access-policy-azure-management.md) |
| [启用密码哈希同步](../hybrid/how-to-connect-password-hash-synchronization.md)（如果使用混合标识） | 为身份验证提供冗余并提高安全性（包括智能锁定、IP 锁定以及发现凭据泄露的功能） |
| [启用 ADFS 智能锁定](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-smart-lockout-protection)（如果适用） | 保护用户免受恶意活动导致的 Extranet 帐户锁定。 |
| [启用 Azure Active Directory 智能锁定](../authentication/howto-password-smart-lockout.md)（如果使用托管标识） | 智能锁定有助于锁定尝试猜测用户密码或使用强制方法获取的不良执行组件。 |
| [禁用应用程序的最终用户许可](../manage-apps/configure-user-consent.md) | 管理员许可工作流为管理员提供了一种安全的方法来向需要管理员批准的应用程序授予访问权限，因此最终用户不会公开公司数据。 Microsoft 建议禁用将来的所有用户许可操作有助于减小受攻击面并缓解此风险。 |
| [允许使用应用程序代理远程访问本地旧版应用程序](../manage-apps/application-proxy-add-on-premises-application.md) | 启用 Azure AD 应用程序代理，并与旧应用集成，使用户能够通过使用 Azure AD 帐户登录来安全地访问本地应用程序。 |
| [启用安全混合访问：使用现有的应用传递控制器和网络保护旧应用](../manage-apps/secure-hybrid-access.md)（如果适用）。 | 通过将本地和云旧式身份验证应用程序连接到现有的应用程序传送控制器或网络 Azure AD 来发布和保护这些应用程序。 |
| [将支持的 SaaS 应用程序从库集成到 Azure AD 并启用单一登录](../manage-apps/add-application-portal.md) | Azure AD 的某个库包含数千个预先集成的应用程序。 组织使用的某些应用程序可能就在该库中。可通过 Azure 门户直接访问该库。 通过改进的用户体验（SSO），以远程和安全的方式提供对企业 SaaS 应用程序的访问权限。 |
| [从 SaaS 应用程序自动化用户预配和取消设置](../app-provisioning/user-provisioning.md)（如果适用） | 自动创建用户需要访问的云（SaaS）应用程序中的用户标识和角色。 除了创建用户身份外，自动预配还包括将用户标识作为状态或角色的更改进行维护和删除，从而提高组织的安全性。 |
| [启用条件性访问–基于设备](../conditional-access/require-managed-devices.md) | 使用基于设备的条件访问来改进安全性和用户体验。 此步骤可确保用户只能从满足安全和合规性标准的设备进行访问。 这些设备也称为受管理设备。 托管设备可以是与 Intune 兼容的设备，也可以是混合 Azure AD 加入的设备。 |
| [启用密码保护](../authentication/howto-password-ban-bad-on-premises-deploy.md) | 防止用户使用弱密码和轻松猜测密码。 |
| [指定多个全局管理员](../users-groups-roles/directory-emergency-access.md) | 至少分配两个仅限云的永久性全局管理员帐户，以便在紧急情况下使用。 这些帐户不是每日使用，应该具有复杂的长密码。 Break Glass 帐户确保你可在紧急情况下访问该服务。 |
| [尽可能使用非全局管理角色](../users-groups-roles/directory-assign-admin-roles.md) | 只为管理员分配他们必须访问的区域的访问权限。 并非所有管理员都需要是全局管理员。 |
| [启用 Microsoft 的密码指导](https://www.microsoft.com/research/publication/password-guidance/) | 停止要求用户按照设置的计划更改其密码，禁用复杂性要求，用户更倾向于记住他们习惯的密码，并妥善保管其密码。 |
| [针对来宾用户访问权限创建计划](../b2b/what-is-b2b.md) | 通过让来宾用户使用其自己的工作、学校或社交标识登录应用和服务，实现与来宾用户的协作。 |
| [启用 Privileged Identity Management](../privileged-identity-management/pim-configure.md) | 让你能够管理、控制和监视对组织中重要资源的访问，从而确保管理员只在需要且得到批准时才有访问权限 |

## <a name="next-steps"></a>后续步骤

- 若要详细了解 Azure AD 的各个功能，请查看[Azure AD 项目部署计划](active-directory-deployment-plans.md)。

- 有关端到端 Azure AD 部署核对清单，请参阅 [Azure Active Directory 功能部署指南](active-directory-deployment-checklist-p2.md)一文