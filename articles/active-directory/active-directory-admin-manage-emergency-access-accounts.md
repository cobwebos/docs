---
title: 在 Azure AD 中管理紧急访问管理帐户 |Microsoft Docs
description: 本文介绍如何使用紧急访问帐户来帮助组织限制现有 Azure Active Directory 环境中的特权访问。
services: active-directory
author: markwahl-msft
ms.author: billmath
ms.date: 12/13/2017
ms.topic: article-type-from-white-list
ms.service: active-directory
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.openlocfilehash: 5c256befba4c73527b529c953f7b9cd4d266450c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
ms.locfileid: "30901099"
---
# <a name="manage-emergency-access-administrative-accounts-in-azure-ad"></a>在 Azure AD 中管理紧急访问管理帐户 

对于大多数日常活动，用户不需要全局管理员权限。 不应向用户永久分配该角色，因为他们可能无意中执行需要更高权限的任务。 当用户不需要以全局管理员身份进行操作时，应使用 Azure Active Directory (Azure AD) Privileged Identity Management (PIM) 在其自己的帐户或备用管理帐户上激活角色分配。

除了防止用户为自己授予管理访问权限，还需要防止无意中被阻止管理 Azure AD 租户，因为作为管理员既不能登录也不能激活现有个人用户帐户。 可在租户中存储两个或更多紧急访问帐户，缓解不经意丧失管理访问权限造成的影响。

使用紧急访问帐户来帮助组织限制现有 Azure Active Directory 环境中的特权访问。 此类帐户拥有极高的特权，不要将其分配给特定的个人。 紧急访问帐户仅限于紧急情况，即不能使用正常管理帐户的情况。 组织必须始终以将紧急帐户的使用限于必要情况为目标。

在以下情况下，组织可能需要使用紧急访问帐户：

 - 用户帐户进行了联合，且由于手机网络中断或标识提供程序服务中断，联合身份验证当前不可用。 例如，如果在你的环境中的标识提供者主机停运，则当 Azure AD 重定向到其标识提供者时，用户可能无法登录。 
 - 管理员通过 Azure 多重身份验证注册，而其每个设备都不可用。 用户可能无法完成多重身份验证以激活角色。 例如，手机网络中断让用户无法应答电话呼叫或接收短信，而这是他们为其设备注册的仅有的两种身份验证机制。 
 - 具有最新全局管理访问权限的人员离开了组织。 Azure AD 将阻止删除最后一个全局管理员帐户，但它不会阻止从本地删除或禁用该帐户。 这两种情况都可能使组织无法恢复帐户。

## <a name="initial-configuration"></a>初始配置

创建两个或多个紧急访问帐户。 这些帐户应是仅限云帐户，使用 \*.onmicrosoft.com 域且未与本地环境未联合或同步。 

帐户不应与组织中任何单个用户关联。 组织需要确保这些帐户的凭据始终安全且仅为有权使用它们的用户所知。 

> [!NOTE]
> 紧急访问帐户的帐户密码通常分为两三部分，分开写在两三张纸上，存储在安全的独立位置的安全、防火的保险柜中。 
>
> 请确保紧急访问帐户未连接到任何员工提供的移动电话、会随单个员工流动的硬件令牌或其他特定于员工的凭据。 此预防措施介绍需要凭据而无法找到某个拥有凭据的员工时的情况。 

### <a name="initial-configuration-with-permanent-assignments"></a>初始配置和永久分配

可以选择使用户成为拥有全局管理员角色的永久成员。 此选项适用于不适用 Azure AD Premium P2 订阅的组织。

为降低泄露密码所致攻击的风险，Azure AD 建议要求所有用户使用多重身份验证。 此组应包括管理员和被盗帐户将产生重大影响的其他所有用户（例如财务）。 

但是，如果组织没有共享设备，这些紧急访问帐户可能无法使用多重身份验证。 如果要配置条件访问策略，以要求[每个管理员针对 Azure AD 及连接的其他软件即服务 (SaaS) 应用进行多重身份验证注册](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)，可能需要配置策略例外，从此要求中排除紧急访问帐户。

### <a name="initial-configuration-with-approvals"></a>初始配置与批准

还可选择将用户配置为符合条件的审批者，使其可以激活全局管理员角色。 此选项要求组织使用 Azure AD Premium P2 订阅。 它还将要求使用适合在多名用户和网络环境之间共享使用的多重身份验证选项。 这些要求是因为用户执行过多重身份验证才能激活全局管理员角色。 有关详细信息，请参阅[如何在 Azure AD Privileged Identity Management 中要求使用多重身份验证](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-how-to-require-mfa)。

建议不使用与用于紧急访问帐户的个人设备关联的多重身份验证。 在实际紧急情况下，需要访问注册了多重身份验证的设备的人员可能不是该个人设备的所有者。 

另请考虑威胁态势。 例如，可能出现自然灾害等不可预见的紧急情况，导致手机或其他网络不可用。 请务必确保将任何已注册设备保存在与 Azure AD 有多种通信方式的已知安全位置。

## <a name="ongoing-monitoring"></a>持续监视

监视 [Azure AD 登录和审核日志](https://docs.microsoft.com/azure/active-directory/active-directory-reporting-activity-sign-ins)中紧急访问帐户的任何登录和审核活动。 正常情况下，这些帐户不应有登录和更改行为，因此其使用很可能是异常且需要进行安全调查。

## <a name="account-check-validation-must-occur-at-regular-intervals"></a>必须以固定的时间间隔进行帐户检查验证

若要验证该帐户，请至少以此时间间隔执行以下步骤：
- 每 90 天。
- IT 员工中最近有变动时（如工作变动、离职或入职）。
- 组织中的 Azure AD 订阅发生更改时。

若要训练员工使用紧急访问帐户，请执行以下操作：

* 确保安全监视人员了解正在进行帐户检查活动。
* 验证云用户帐户可以登录并激活其角色，需在紧急情况下执行这些步骤的用户经过流程培训。
* 确保这些用户未对任何个人用户设备或个人详细信息注册多重身份验证或自助服务密码重置 (SSPR)。 
* 如果帐户已注册为通过多重身份验证注册到设备，为在角色激活期间使用，请确保需在紧急情况下使用该设备的所有管理员都可访问该设备。 另请验证通过不共享通用失败模式的至少两种机制注册了该设备。 例如，设备可通过公司无线网络和移动网络提供商网络与 Internet 通信。
* 更新帐户凭据。

## <a name="next-steps"></a>后续步骤
- [添加基于云的用户](add-users-azure-active-directory.md)并[将新用户分配给全局管理员角色](active-directory-users-assign-role-azure-portal.md)。
- [注册 Azure Active Directory Premium](active-directory-get-started-premium.md)（如果尚未已注册）。
- [要求分配到管理员角色的各个用户使用 Azure 多重身份验证](https://docs.microsoft.com/azure/multi-factor-authentication/multi-factor-authentication-get-started-user-states)。
- 如果使用 Office 365，请[在 Office 365 中配置全局管理员的额外保护](https://support.office.com/article/Protect-your-Office-365-global-administrator-accounts-6b4ded77-ac8d-42ed-8606-c014fd947560)。
- [执行全局管理员访问评审](active-directory-privileged-identity-management-how-to-start-security-review.md)并[将现有全局管理员转换为更具体的管理员角色](active-directory-assign-admin-roles-azure-portal.md)。


