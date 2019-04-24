---
title: 管理紧急访问管理员帐户的 Azure Active Directory |Microsoft Docs
description: 本文介绍如何借助紧急访问帐户来防止意外地被锁在 Azure Active Directory (Azure AD) 租户之外的情况。
services: active-directory
author: markwahl-msft
ms.author: curtand
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: active-directory
ms.subservice: users-groups-roles
ms.workload: identity
ms.custom: it-pro
ms.reviewer: markwahl-msft
ms.collection: M365-identity-device-management
ms.openlocfilehash: f430a832ffb35b95d0bf4eff2d82be5ecc3d865c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60472343"
---
# <a name="manage-emergency-access-accounts-in-azure-ad"></a>在 Azure AD 中管理紧急访问帐户

必须防止意外地被锁在 Azure Active Directory (Azure AD) 租户之外，因为在这种情况下，无法以管理员的身份登录或激活现有的个人用户帐户。 可在租户中创建两个或更多紧急访问帐户，缓解不经意丧失管理访问权限造成的影响。

紧急访问帐户拥有较高的特权，因此请不要将其分配给特定的个人。 紧急访问帐户只能用于“破窗”紧急情况，即不能使用正常管理帐户的情况。 组织必须始终以将紧急帐户的使用限于绝对必要情况为目标。

本文提供有关在 Azure AD 中管理紧急访问帐户的指导。

## <a name="when-would-you-use-an-emergency-access-account"></a>何时使用紧急访问帐户？

在以下情况下，组织可能需要使用紧急访问帐户：

- 用户帐户进行了联合，且由于手机网络中断或标识提供程序服务中断，联合身份验证当前不可用。 例如，如果在你的环境中的标识提供者主机停运，则当 Azure AD 重定向到其标识提供者时，用户可能无法登录。
- 管理员通过 Azure 多重身份验证注册，而其每个设备或服务都不可用。 用户可能无法完成多重身份验证以激活角色。 例如，手机网络中断让用户无法应答电话呼叫或接收短信，而这是他们为其设备注册的仅有的两种身份验证机制。
- 具有最新全局管理访问权限的人员离开了组织。 Azure AD 将阻止删除最后一个全局管理员帐户，但它不会阻止从本地删除或禁用该帐户。 这两种情况都可能使组织无法恢复帐户。
- 出现自然灾害等不可预见的紧急情况，导致手机或其他网络不可用。 

## <a name="create-two-cloud-based-emergency-access-accounts"></a>创建两个基于云的紧急访问帐户

创建两个或多个紧急访问帐户。 这些帐户应是仅限云帐户，使用 \*.onmicrosoft.com 域且未与本地环境未联合或同步。

配置这些帐户时，必须满足以下要求：

- 紧急访问帐户不应与组织中的任何单个用户相关联。 确保帐户未关联到任何员工提供的移动电话、会随单个员工流动的硬件令牌或其他特定于员工的凭据。 此预防措施介绍需要凭据而无法找到某个拥有凭据的员工时的情况。 请务必确保将任何已注册设备保存在与 Azure AD 有多种通信方式的已知安全位置。
- 紧急访问帐户使用的身份验证机制应该不同于其他管理帐户（包括其他紧急访问帐户）使用的机制。  例如，如果管理员通过本地 MFA 正常登录，则 Azure MFA 是不同的机制。  但是，如果 Azure MFA 是管理帐户的主要身份验证部分，请考虑对这些帐户使用不同的方法，例如，结合第三方 MFA 提供程序使用条件访问。
- 设备或凭据不得过期，或者由于使用次数不多而划归到自动清理的范围内。  
- 应将全局管理员角色分配设为紧急访问帐户的永久角色。 


### <a name="exclude-at-least-one-account-from-phone-based-multi-factor-authentication"></a>从基于电话的多重身份验证中排除至少一个帐户

为降低泄露密码所致攻击的风险，Azure AD 建议要求所有用户使用多重身份验证。 此组包括管理员和被盗帐户将产生重大影响的其他所有用户（例如财务）。

但是，至少应有一个紧急访问帐户的多重身份验证机制与其他非紧急帐户不同。 这包括第三方多重身份验证解决方案。 如果条件访问策略要求每个管理员针对 Azure AD 及连接的其他软件即服务 (SaaS) 应用执行[多重身份验证](../authentication/howto-mfa-userstates.md)，则应从此要求中排除紧急访问帐户，并改而配置其他机制。 此外，应确保这些帐户不使用按用户的多重身份验证策略。

### <a name="exclude-at-least-one-account-from-conditional-access-policies"></a>从条件访问策略中排除至少一个帐户

在紧急情况下，你不希望某个策略阻止你进行访问以解决问题。 应从所有条件访问策略中排除至少一个紧急访问帐户。 如果已启用[基准策略](../conditional-access/baseline-protection.md)，应排除紧急访问帐户。

## <a name="additional-guidance-for-hybrid-customers"></a>面向混合客户的更多指导

对于使用 AD 域服务和 ADFS 或类似标识提供者联合到 Azure AD 的组织，另一种做法是配置一个可由该标识提供者提供 MFA 声明的紧急访问帐户。  例如，紧急访问帐户可由证书和密钥对（例如，存储在智能卡上）提供安全保障。  当该用户在 AD 中进行身份验证时，ADFS 可向 Azure AD 提供声明，指示该用户满足 MFA 要求。  即使使用此方法，组织也仍需要提供基于云的紧急访问帐户，否则无法建立联合。 

## <a name="store-devices-and-credentials-in-a-safe-location"></a>将设备和凭据存储在安全位置

组织需要确保紧急访问帐户的凭据始终安全且仅为有权使用它们的用户所知。 有些客户使用智能卡，有些客户使用密码。 紧急访问帐户的密码通常分为两到三个部分，分开写在纸上，存储在安全独立位置中的防火保险柜中。

如果使用密码，请确保为帐户使用不会过期的强密码。 密码最好是至少包含 16 个字符，且随机生成。


## <a name="monitor-sign-in-and-audit-logs"></a>监视登录和审核日志

监视 [Azure AD 登录和审核日志](../reports-monitoring/concept-sign-ins.md)中紧急访问帐户的任何登录和审核活动。 正常情况下，这些帐户不应有登录和更改行为，因此其使用很可能是异常且需要进行安全调查。

## <a name="validate-accounts-at-regular-intervals"></a>定期验证帐户

若要为员工培训紧急访问帐户的用法和验证紧急访问帐户，至少应定期执行以下步骤：

- 确保安全监视人员了解正在进行帐户检查活动。
- 确保使用这些帐户的紧急破窗流程有文档记录，且是最新的流程。
- 确保在紧急情况下可能需要执行这些步骤的管理员和保安员接受了该流程的培训。
- 更新紧急访问帐户的帐户凭据，尤其是所有密码，然后验证紧急访问帐户是否可以登录并执行管理任务。
- 确保用户未对任何个人用户设备或个人详细信息注册多重身份验证或自助服务密码重置 (SSPR)。 
- 如果帐户已注册为通过多重身份验证登录到设备，以便在登录或角色激活期间使用，请确保需在紧急情况下使用该设备的所有管理员都可访问该设备。 另请验证设备是否可以通过不存在共同故障模式的至少两条网络路径进行通信。 例如，设备可通过公司无线网络和移动网络提供商网络与 Internet 通信。

应定期执行以下步骤，进行重大更改后也应执行这些步骤：

- 每隔 90 天至少执行一次
- IT 员工中最近有变动时（如工作变动、离职或入职）
- 组织中的 Azure AD 订阅发生更改时

## <a name="next-steps"></a>后续步骤

- [确保 Azure AD 中混合部署和云部署的特权访问安全性](directory-admin-roles-secure.md)
- [使用 Azure AD 添加用户](../fundamentals/add-users-azure-active-directory.md)并[将新用户分配到全局管理员角色](../fundamentals/active-directory-users-assign-role-azure-portal.md)
- [注册 Azure AD Premium](../fundamentals/active-directory-get-started-premium.md)（如果尚未注册）
- [如何要求用户进行双重验证](../authentication/howto-mfa-userstates.md)
- [在 Office 365 中为全局管理员配置额外的保护](https://docs.microsoft.com/office365/enterprise/protect-your-global-administrator-accounts)（如果使用 Office 365）
- [启动全局管理员访问评审](../privileged-identity-management/pim-how-to-start-security-review.md)并[将现有全局管理员转换为更具体的管理员角色](directory-assign-admin-roles.md)
