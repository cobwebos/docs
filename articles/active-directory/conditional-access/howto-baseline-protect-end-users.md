---
title: 最终用户保护（预览版）基线策略 - Azure Active Directory
description: 要求用户执行多重身份验证的条件访问策略
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
ms.openlocfilehash: 0f1a2e0bad39b54edc153416e4120bbc6912578c
ms.sourcegitcommit: 07700392dd52071f31f0571ec847925e467d6795
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70125448"
---
# <a name="baseline-policy-end-user-protection-preview"></a>基线策略：最终用户保护（预览版）

我们往往认为，只有管理员帐户才是需要使用多重身份验证 (MFA) 保护的帐户。 管理员对敏感信息拥有广泛的访问权限，并且可以更改订阅范围的设置。 但是，恶意行动者往往针对最终用户。 获取访问权限后，这类恶意行动者可以代表原始帐户持有者请求访问特权信息，或者下载整个目录以针对整个组织展开网络钓鱼攻击。 增强对所有用户的保护的一种常用方法是要求使用更强的帐户验证，例如多重身份验证 (MFA)。

为了在安全性与可用性之间实现合理的平衡，不应在用户每次登录时都向其显示提示信息。 反映正常用户行为（例如，在同一位置通过同一台设备登录）的身份验证请求不太可能会透露敏感信息。 应该只针对被视为有风险并展示了恶意行动者特征的登录提出 MFA 质询。

最终用户保护是一种基于风险的 MFA [基线策略](concept-baseline-protection.md)，可以保护目录中的所有用户，包括所有管理员角色。 启用此策略需要所有用户使用 Authenticator 应用注册 MFA。 用户可以忽略 MFA 注册提示 14天，此时限过后，除非注册 MFA，否则会阻止他们登录。 注册 MFA 后，只会在检测到有风险的登录企图时，才提示用户执行 MFA。 受损的用户帐户将被阻止, 直到其密码被重置并消除了风险检测。

> [!NOTE]
> 此策略适用于所有用户（包括来宾帐户）。登录所有应用程序时会评估此策略。

## <a name="recovering-compromised-accounts"></a>恢复遭到入侵的帐户

为帮助保护客户，Microsoft 的已泄漏凭据服务将查找公开提供的用户名/密码对。 如果这些凭据对与我们的某个用户相匹配，我们会立即帮助保护该帐户。 识别为凭据已泄漏的用户将确认为已遭到入侵。 在重置其密码之前，我们会阻止这些用户登录。

如果已在目录中启用功能, 则分配有 Azure AD Premium 许可证的用户可以通过自助服务密码重置 (SSPR) 还原访问权限。 如果用户不具有被阻止的高级许可证, 则必须与管理员联系以执行手动密码重置, 并解除已标记用户的风险检测。

### <a name="steps-to-unblock-a-user"></a>解除阻止用户的步骤

检查用户的登录日志，确认该用户是否已由策略阻止。

1. 管理员需要登录到 **Azure 门户**，导航到“Azure Active Directory” > “用户”，单击该用户名，然后导航到“登录”。
1. 若要针对已阻止的用户启动密码重置，管理员需要导航到“Azure Active Directory” > “已标记为存在风险的用户”
1. 单击帐户已阻止的用户，查看有关该用户最近登录活动的信息。
1. 单击“重置密码”，分配下次登录时必须更改的临时密码。
1. 单击“消除所有事件”以重置用户的风险评分。

现在，该用户可以登录、重置其密码和访问应用程序。

## <a name="deployment-considerations"></a>部署注意事项

由于“最终用户保护”策略适用于目录中的所有用户，因此需要考虑到多种因素才能确保顺利部署。 这些考虑事项包括：确定 Azure AD 中不能或不应该执行 MFA 的用户和服务主体，以及组织所使用的不支持新式身份验证的应用程序和客户端。

### <a name="legacy-protocols"></a>旧式协议

旧式身份验证协议（IMAP、SMTP、POP3 等）由邮件客户端用来进行身份验证请求。 这些协议不支持 MFA。  在 Microsoft 看来，大多数帐户入侵事件都是由攻击者对旧式协议进行攻击，尝试绕过 MFA。 为了确保在登录帐户时强制实施 MFA，同时确保攻击者无法绕过 MFA，此策略阻止从旧式协议进行的针对管理员帐户的所有身份验证请求。

> [!WARNING]
> 在启用此策略之前，请确保用户使用的不是旧式身份验证协议。 请参阅[如何：使用条件访问来阻止 Azure AD 上的旧式身份验证](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)一文了解详细信息。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略“基线策略:最终用户保护(预览版)”已预配置，当你在 Azure 门户中导航到“条件访问”边栏选项卡时，它会显示在顶部。

若要启用此策略并保护用户：

1. 以全局管理员、安全管理员或条件访问管理员身份登录到**Azure 门户**。
1. 浏览到“Azure Active Directory” > “条件访问”。
1. 在策略列表中选择“基线策略:最终用户保护(预览版)”。
1. 将“启用策略”设置为“立即使用策略”。
1. 单击“保存”。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
