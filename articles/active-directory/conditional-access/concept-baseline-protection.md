---
title: 条件性访问基线策略-Azure Active Directory
description: 用于防范组织遭到常见攻击的基线条件访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: calebb, rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a9bb384045c8b2e0a5743fdc301a829792639b7e
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/23/2019
ms.locfileid: "74420549"
---
# <a name="what-are-baseline-policies"></a>什么是基线策略？

基准策略是一组预定义策略，可帮助组织防范多种常见攻击。 这些常见攻击包括密码喷洒、重放和网络钓鱼。 基线策略可在所有版本的 Azure AD 中使用。 Microsoft 之所以向所有用户提供这些基线保护策略，是因为在过去多年以来，基于身份的攻击不断增多。 这四个策略旨在确保所有组织在无需额外增加成本的情况下实现基线级别的安全性。  

管理自定义的条件访问策略需要 Azure AD Premium 许可证。

## <a name="baseline-policies"></a>基线策略

![Azure 门户中的条件访问基线策略](./media/concept-baseline-protection/conditional-access-policies.png)

有四个基准策略：

* 要求对管理员进行 MFA (预览)
* 最终用户保护（预览版）
* 阻止旧式身份验证（预览版）
* 要求对服务管理进行 MFA（预览版）

所有这四个基线策略会影响旧式身份验证流，例如 POP、IMAP 和早期的 Office 桌面客户端。

### <a name="require-mfa-for-admins-preview"></a>要求对管理员进行 MFA (预览)

由于管理员帐户拥有的超级权力和访问权限，应十分谨慎地对待这些帐户。 增强对特权帐户的保护的一种常用方法是要求在使用这些帐户登录时进行更强的帐户验证。 在 Azure Active Directory 中，可以通过要求管理员注册并使用 Azure 多重身份验证来实施更强的帐户验证。

"需要对管理员的 MFA （预览版）" 是需要对以下目录角色进行多重身份验证（MFA）的基准策略，被认为是最特权的 Azure AD 角色：

* 全局管理员
* SharePoint 管理员
* Exchange 管理员
* 条件访问管理员
* 安全管理员
* 支持管理员/密码管理员
* 计费管理员
* 用户管理员

如果组织在脚本或代码中使用这些帐户，请考虑将其替换为[托管标识](../managed-identities-azure-resources/overview.md)。

### <a name="end-user-protection-preview"></a>最终用户保护（预览版）

高特权管理员并不是攻击者的唯一目标。 恶意行动者往往针对普通用户。 获取访问权限后，这类恶意行动者可以代表原始帐户持有者请求访问特权信息，或者下载整个目录并针对整个组织展开网络钓鱼攻击。 增强对所有用户的保护的一种常用方法是在检测到有风险的登录时要求使用更强的帐户验证。

**最终用户保护（预览版）** 是保护目录中所有用户的基线策略。 启用此策略需要所有用户在 14 天内注册 Azure 多重身份验证。 注册后，只会在检测到有风险的登录企图时，才提示用户执行 MFA。 在重置密码并解除风险之前，会封锁已遭到入侵的用户帐户。 

[!NOTE]
任何之前标记为有风险的用户都将被阻止，直到密码重置和策略激活后风险解除。

### <a name="block-legacy-authentication-preview"></a>阻止旧式身份验证（预览版）

旧的身份验证协议（例如： IMAP、SMTP、POP3）通常由较旧的邮件客户端用来进行身份验证。 旧式协议不支持多重身份验证。 即使你创建了一个要求对目录执行多重身份验证的策略，恶意行动者也仍可以使用其中一种旧式协议进行身份验证，并绕过多重身份验证。

防范旧式协议发出的恶意身份验证请求入侵帐户的最佳方法是阻止这些请求。

“阻止旧式身份验证（预览版）”基线策略可以阻止使用旧式协议发出的身份验证请求。 所有用户必须使用新式身份验证才能成功登录。 与其他基线策略结合使用时，将会阻止来自旧式协议的请求。 此外，所有用户必须根据要求执行 MFA。 此策略不会阻止 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>要求对服务管理进行 MFA（预览版）

组织会使用各种 Azure 服务，并通过如下所述的基于 Azure 资源管理器的工具对其进行管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

使用上述任何工具执行资源管理是一种高特权操作。 这些工具可以更改订阅范围的配置，例如服务设置和订阅计费。

为了保护特权操作，“要求对服务管理执行 MFA（预览版）”策略要求针对访问 Azure 门户、Azure PowerShell 或 Azure CLI 的任何用户执行多重身份验证。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [常见的条件访问策略](concept-conditional-access-policy-common.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
