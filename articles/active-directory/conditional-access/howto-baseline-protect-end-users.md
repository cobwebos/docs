---
title: 基准策略最终用户保护 (预览版)-Azure Active Directory
description: 要求用户进行多重身份验证的条件性访问策略
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
ms.openlocfilehash: afcd9c9d3191caeabe182f499b5fd80cd8e1d8dd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608148"
---
# <a name="baseline-policy-end-user-protection-preview"></a>基准策略:最终用户保护 (预览版)

我们往往认为管理员帐户是唯一需要通过多重身份验证 (MFA) 进行保护的帐户。 管理员对敏感信息具有广泛的访问权限, 并且可以更改订阅范围的设置。 但是, 不良的执行组件往往会面向最终用户。 获取访问权限后, 这类不良的执行组件可以代表原始帐户持有者请求对特权信息的访问权限, 也可以下载整个目录以对整个组织执行网络钓鱼攻击。 为所有用户改善保护的一种常见方法是, 需要一种更强的帐户验证形式, 例如多重身份验证 (MFA)。

为实现合理的安全性和可用性平衡, 用户在每次登录时都不应收到提示。 反映正常用户行为的身份验证请求 (如从同一位置从同一台设备登录) 的可能性较低。 只有被视为有风险且显示错误参与者特征的登录时, 才会收到 MFA 质询的提示。

最终用户保护是一种基于风险的 MFA[基线策略](concept-baseline-protection.md), 可保护目录中的所有用户, 包括所有管理员角色。 如果启用此策略, 则要求所有用户注册使用验证器应用的 MFA。 用户可以忽略14天的 MFA 注册提示, 超过此时间后, 它们将被阻止登录, 直到他们注册 MFA。 为 MFA 注册后, 用户仅在有风险的登录尝试期间才会提示用户进行 MFA。 受损的用户帐户会被阻止, 直到其密码重置和消除了风险事件。

> [!NOTE]
> 此策略适用于包括来宾帐户的所有用户, 并且将在登录到所有应用程序时进行评估。

## <a name="recovering-compromised-accounts"></a>恢复受损帐户

为了帮助保护我们的客户, Microsoft 泄漏的凭据服务将查找公开提供的用户名/密码对。 如果这些用户与我们的用户之一匹配, 我们将帮助你立即保护该帐户。 标识为具有泄漏的凭据的用户将被确认为已泄露。 这些用户在重置其密码之前将被阻止登录。

如果已在目录中启用功能, 则分配有 Azure AD Premium 许可证的用户可以通过自助服务密码重置 (SSPR) 还原访问权限。 如果用户不具有被阻止的高级许可证, 则必须与管理员联系以执行手动密码重置, 并关闭标记用户风险事件。

### <a name="steps-to-unblock-a-user"></a>取消阻止用户的步骤

通过检查用户的登录日志来确认该用户已被该策略阻止。

1. 管理员需要登录到**Azure 门户**并导航到**Azure Active Directory** > **用户**> 单击用户的名称并导航到 "登录"。
1. 若要在被阻止的用户上启动密码重置, 管理员需要 > 导航到已**标记为有风险 Azure Active Directory 用户**
1. 单击其帐户被阻止的用户, 查看有关用户最近登录活动的信息。
1. 单击 "重置密码", 分配在下一次登录时必须更改的临时密码。
1. 单击 "消除所有事件" 以重置用户的风险评分。

用户现在可以登录、重置其密码并访问应用程序。

## <a name="deployment-considerations"></a>部署注意事项

由于**最终用户保护**策略适用于目录中的所有用户, 因此需要考虑几个注意事项才能确保顺利部署。 这些注意事项包括: 在 Azure AD 中标识用户和服务原则, 这些用户和服务原则不能或不应执行 MFA, 以及不支持新式身份验证的组织使用的应用程序和客户端。

### <a name="legacy-protocols"></a>旧协议

电子邮件客户端使用旧的身份验证协议进行身份验证请求。 这些协议不支持 MFA。  Microsoft 所见到的大多数帐户损害都是由不良的执行组件对尝试绕过 MFA 的传统协议执行攻击导致的。 若要确保在登录帐户时需要 MFA, 而不良执行组件无法绕过 MFA, 此策略将阻止从旧协议对管理员帐户发出的所有身份验证请求。

> [!WARNING]
> 启用此策略之前, 请确保用户未使用旧身份验证协议。 请参阅文章[如何:有关详细信息, 请阻止使用条件](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)性访问 Azure AD 的旧式身份验证。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基线策略:最终用户保护 (预览版)** 是预配置的, 当你导航到 Azure 门户中的 "条件性访问" 边栏选项卡时, 将显示在顶部。

若要启用此策略并保护用户:

1. 以全局管理员、安全管理员或条件访问管理员身份登录到 **Azure 门户** 。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中, 选择 **"基准策略":最终用户保护 (预览版)** 。
1. 将 "**启用策略**" 设置为 "**立即使用策略**"。
1. 单击 " **保存**"。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/fundamentals/steps-secure-identity.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
