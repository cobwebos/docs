---
title: 条件性访问基线保护策略的 Azure Active Directory
description: 基线条件性访问策略以保护组织免受常见攻击
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 970fdaba1870097e253b51c70e523e399bc88dfc
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67440773"
---
# <a name="what-are-baseline-policies"></a>什么是基线策略？

基线策略是一组预定义策略来帮助保护组织针对许多常见的攻击。 这些常见的攻击可以包含密码喷射、 重播和网页仿冒欺诈。 所有版本的 Azure AD 中提供了基线策略。 Microsoft 向这些基线保护策略提供每个人都因为基于标识的攻击已过去几年的上升。 以下四个策略的目标是确保所有组织都都基线级别的安全性启用，无需额外付费。  

管理自定义的条件性访问策略需要将 Azure AD Premium 许可证。

## <a name="baseline-policies"></a>基线策略

![在 Azure 门户中的条件性访问基线策略](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

有四个可以让组织的基线策略：

* [有关管理员 （预览版） 要求进行 MFA](howto-baseline-protect-administrators.md)
* [最终用户保护 （预览版）](howto-baseline-protect-end-users.md)
* [块旧式身份验证 （预览版）](howto-baseline-protect-legacy-auth.md)
* [服务管理 （预览版） 要求进行 MFA](howto-baseline-protect-azure.md)

这些策略的所有四个会影响 POP、 IMAP 和较旧的 Office 桌面客户端等传统的身份验证流。

### <a name="require-mfa-for-admins-preview"></a>有关管理员 （预览版） 要求进行 MFA

由于的功能和管理员帐户的访问权限，应将它们视为特殊谨慎。 改进的保护特权帐户的一种常见方法是使用登录时需要更强的帐户验证形式。 在 Azure Active Directory 中，可以通过要求管理员可以注册并使用 Azure 多重身份验证来获取更强的帐户验证。

[有关管理员 （预览版） 要求使用 MFA](howto-baseline-protect-administrators.md) 要求多重身份验证 (MFA) 的基准策略为以下目录角色，被视为最高特权的 Azure AD 角色：

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员
* 支持管理员 / 密码管理员
* 计费管理员
* 用户管理员

如果你的组织中的脚本或代码中使用这些帐户，请考虑将它们替换为 [托管标识](../managed-identities-azure-resources/overview.md)。

### <a name="end-user-protection-preview"></a>最终用户保护 （预览版）

高特权的管理员不是针对的攻击是唯一的。 不良参与方可能会针对普通用户。 获得访问权限之后, 这些不良参与方可以请求对代表原始帐户持有者的特权信息的访问或下载整个目录并执行在整个组织的网络钓鱼攻击。 改善的所有用户保护的一种常见方法是检测有风险登录中时，需要更强的帐户验证形式。

**最终用户保护 （预览版）** 是保护目录中的所有用户的基线策略。 如果启用此策略要求所有用户在 14 天内注册 Azure 多重身份验证。 注册后，用户将会提示进行 MFA 仅在有风险的登录尝试。 密码重置和风险上诉之前，将阻止用户帐户泄露。

### <a name="block-legacy-authentication-preview"></a>块旧式身份验证 （预览版）

旧式身份验证协议 (例如：IMAP、 SMTP、 POP3） 是通常使用较旧的邮件客户端进行身份验证的协议。 旧协议不支持多重身份验证。 即使为你的目录要求多重身份验证的策略，恶意参与者可以使用其中一种旧协议进行身份验证，并绕过多重身份验证。

保护你的帐户免受恶意身份验证请求所做的旧协议的最佳方法是阻止它们。

**块旧式身份验证 （预览版）** 基准策略阻止使用旧版协议进行的身份验证请求。 若要成功登录的所有用户，必须使用新式身份验证。 与其他基线策略结合使用，将阻止旧版协议从传入请求。 此外，所有用户都都需要 MFA 时需要。 此策略不会阻止 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>服务管理 （预览版） 要求进行 MFA

组织使用各种 Azure 服务和管理它们的基于 Azure 资源管理器之类的工具：

* Azure 门户
* Azure PowerShell
* Azure CLI

使用任何一种工具来执行资源管理是高特权的操作。 这些工具可以更改订阅范围的配置，例如服务设置和订阅计费。

若要保护的特权的操作，这**服务管理 （预览版） 要求使用 MFA**策略需要多重身份验证访问 Azure 门户、 Azure PowerShell 或 Azure CLI 的任何用户。

## <a name="enable-a-baseline-policy"></a>启用基线策略

若要启用基线策略：

1. 登录到 **Azure 门户** 作为全局管理员、 安全管理员或条件性访问管理员。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中，选择你想要启用的基线策略。
1. 设置**启用策略**到**上**。
1. 单击保存。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [保护标识基础结构的五个步骤](../../security/azure-ad-secure-steps.md)
* [什么是 Azure Active Directory 中条件性访问？](overview.md)
* [有关管理员 （预览版） 要求进行 MFA](howto-baseline-protect-administrators.md)
* [最终用户保护 （预览版）](howto-baseline-protect-end-users.md)
* [块旧式身份验证 （预览版）](howto-baseline-protect-legacy-auth.md)
* [服务管理 （预览版） 要求进行 MFA](howto-baseline-protect-azure.md)
