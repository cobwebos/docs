---
title: 条件性访问基线策略-Azure Active Directory
description: 用于保护组织免受常见攻击的基准条件性访问策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 12/18/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: a15b55aa3d8cc8f16a35c858d11e3d20c260bff8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425016"
---
# <a name="what-are-baseline-policies"></a>什么是基准策略？

基准策略是一组预定义策略，可帮助组织防范多种常见攻击。 这些常见攻击可能包括密码喷射、重播和钓鱼。 所有版本的 Azure AD 都提供基准策略。 Microsoft 会将这些基线保护策略提供给所有人，因为基于身份的攻击在过去几年呈上升趋势。 这四个策略的目标是确保所有组织都启用了基线级别 "安全"，无需额外付费。

管理自定义条件访问策略需要 Azure AD Premium 许可证。

> [!IMPORTANT]
> 正在弃用基准策略。 有关详细信息，请参阅[Azure Active Directory 中的新增功能？](../fundamentals/whats-new.md#replacement-of-baseline-policies-with-security-defaults)

## <a name="baseline-policies"></a>基线策略

![Azure 门户中的条件性访问基线策略](./media/concept-baseline-protection/conditional-access-baseline-policies.png)

有四个基准策略：

* 要求对管理员进行 MFA （预览）
* 最终用户保护（预览版）
* 阻止旧身份验证（预览）
* 需要 MFA 进行服务管理（预览版）

这四个策略都将影响旧版身份验证流，如 POP、IMAP 和旧版 Office 桌面客户端。

### <a name="exclusions"></a>排除在外

当基线策略进入最初的公共预览版时，可以选择从策略中排除用户。 此功能经过预览，并在2019年7月内被删除。 已创建排除项的组织可以继续保持他们的新用户无法将排除添加到策略中。

### <a name="require-mfa-for-admins-preview"></a>要求对管理员进行 MFA （预览）

由于管理员帐户具有的强大功能和访问权限，你应特别小心对待它们。 改善特权帐户保护的一种常见方法是在使用登录时需要更强大的帐户验证形式。 在 Azure Active Directory 中，你可以通过要求管理员注册并使用 Azure 多重身份验证来获取更强的帐户验证。

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

高特权管理员并不是攻击者的唯一目标。 不良的执行组件往往面向普通用户。 获取访问权限后，这类不良的执行组件可以代表原始帐户持有者请求对特权信息的访问权限，也可以下载整个目录并对整个组织执行网络钓鱼攻击。 提高所有用户保护的一种常见方法是在检测到风险登录时需要更强大的帐户验证形式。

**最终用户保护（预览版）** 是保护目录中所有用户的基准策略。 如果启用此策略，则需要所有用户在14天内注册 Azure 多重身份验证。 注册以后，系统仅在检测到风险登录尝试时提示用户进行 MFA。 系统会阻止被盗用的用户帐户，直至用户重置密码并消除风险为止。 

> [!NOTE]
> 之前标记为风险的任何用户都将被阻止，直到重置密码，并在策略激活时消除。

### <a name="block-legacy-authentication-preview"></a>阻止旧身份验证（预览）

旧的身份验证协议（例如： IMAP、SMTP、POP3）通常由较旧的邮件客户端用来进行身份验证。 旧版协议不支持多重身份验证。 即使你具有需要对目录进行多重身份验证的策略，糟糕的执行组件也可以使用其中一种旧协议进行身份验证，并绕过多重身份验证。

保护你的帐户不受传统协议发出的恶意身份验证请求的最佳方式是阻止它们。

"**阻止旧身份验证（预览版）** " 基准策略阻止使用旧版协议发出的身份验证请求。 必须使用新式身份验证才能成功登录所有用户。 与其他基准策略结合使用时，将阻止来自旧协议的请求。 此外，在需要时，所有用户都需要进行 MFA。 此策略不会阻止 Exchange ActiveSync。

### <a name="require-mfa-for-service-management-preview"></a>需要 MFA 进行服务管理（预览版）

组织使用各种 Azure 服务，并通过 Azure 资源管理器工具（如下所示）对其进行管理：

* Azure 门户
* Azure PowerShell
* Azure CLI

使用这些工具中的任何一种执行资源管理是一种高度特权的操作。 这些工具可能会改变订阅范围的配置，例如服务设置和订阅计费。

若要保护特权操作，此操作需要针对任何用户访问 Azure 门户、Azure PowerShell 或 Azure CLI 的**服务管理（预览版）** 策略进行多重身份验证。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [启用安全默认值](../fundamentals/concept-fundamentals-security-defaults.md)
* [常见的条件访问策略](concept-conditional-access-policy-common.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
