---
title: 基准策略最终用户保护 （预览版）-Azure Active Directory
description: 条件性访问策略以要求多重身份验证的用户
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
ms.openlocfilehash: 104665774eee885cc2f562e9813cffcf23aa943e
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66235509"
---
# <a name="baseline-policy-end-user-protection-preview"></a>基准策略：最终用户保护 （预览版）

我们往往认为管理员帐户都需要使用多重身份验证 (MFA) 保护的唯一帐户。 管理员具有对敏感信息的广泛访问，并且可以对订阅范围的设置进行更改。 但是，不良参与方倾向于目标的最终用户。 后获得的访问权限，这些不良参与方可以请求对代表原始帐户持有者的特权信息的访问或下载整个目录，以执行在整个组织的网络钓鱼攻击。 改善的所有用户保护的一种常见方法是要求更强的帐户验证，如多重身份验证 (MFA) 形式。

若要实现合理的平衡的安全性和可用性，不应提示用户每次登录。 身份验证请求，以反映普通用户的行为，如从同一位置，从同一个设备登录有低遭到破坏的机会。 应该使用 MFA 质询提示仅登录被认为是有风险和显示恶意参与者的特征。

![要求对用户进行 MFA](./media/howto-baseline-protect-end-users/baseline-policy-end-user-protection.png)

最终用户保护是基于风险的 MFA[基准策略](concept-baseline-protection.md)，它保护的目录，包括所有管理员角色中的所有用户。 如果启用此策略要求所有用户注册 MFA 使用身份验证器应用。 14 天，此后它们将无法登录，直到在注册 MFA，用户可以忽略 MFA 注册提示。 一旦注册 MFA，用户将会提示进行 MFA 仅在有风险的登录尝试。 重置其密码和已关闭风险事件之前，将阻止用户帐户泄露。

> [!NOTE]
> 此策略适用于所有用户包括来宾帐户和登录所有应用程序时将计算。

## <a name="recovering-compromised-accounts"></a>恢复帐户被盗问题

为了帮助保护我们的客户，Microsoft 提供的已泄漏的凭据服务查找公开提供的用户名/密码对。 如果它们匹配有个用户，我们帮助立即保护该帐户。 确认用户标识为具有已泄漏的凭据泄露。 将阻止这些用户在登录之前重置其密码。

如果在其目录中启用了此功能，分配的 Azure AD Premium 许可证的用户可以还原通过自助服务密码重置 (SSPR) 的访问权限。 高级版许可证的情况下会受到阻止的用户必须联系管理员，以执行手动密码重置和关闭已标记的用户风险事件。

### <a name="steps-to-unblock-a-user"></a>取消阻止用户的步骤

确认，用户已被策略阻止通过检查用户的登录日志。

1. 管理员需要登录到**Azure 门户**并导航到**Azure Active Directory** > **用户**> 单击用户的名称，导航为登录名。
1. 若要启动密码重置上被阻止的用户，管理员需要导航到**Azure Active Directory** > **已标记为风险用户**
1. 单击的用户的帐户将被阻止，若要查看有关用户的最近登录活动的信息。
1. 单击要分配的下一步的登录名时必须更改临时密码重置密码。
1. 单击清除所有事件重置用户的风险评分。

用户现在可以登录、 重置其密码，并访问该应用程序。

## <a name="deployment-considerations"></a>部署注意事项

因为**最终用户保护**策略适用于你的目录中的所有用户，需要进行，以确保顺利完成部署几个注意事项。 这些注意事项包括识别用户并不能或不应执行 MFA，以及应用程序和由你的组织不支持新式身份验证的客户端的 Azure AD 中的服务主体。

### <a name="legacy-protocols"></a>旧协议

邮件客户端使用旧式身份验证协议 （IMAP、 SMTP、 POP3、 等） 进行身份验证请求。 这些协议不支持 MFA。  大部分看到由 Microsoft 帐户破坏而引起不良参与方执行攻击试图绕过 MFA 的旧协议。 为了确保登录到帐户时需要 MFA 和不良参与方不能绕过 MFA，此策略将阻止旧版协议通过对管理员帐户所做的所有身份验证请求。

> [!WARNING]
> 启用此策略之前，请确保你的用户不使用传统的身份验证协议。 请参阅文章[如何：阻止到 Azure AD 条件性访问与传统的身份验证](howto-baseline-protect-legacy-auth.md#identify-legacy-authentication-use)有关详细信息。

### <a name="user-exclusions"></a>用户排除项

此基线策略提供选项来排除用户。 在启用之前该策略为你的租户，我们建议排除以下帐户：

* **紧急访问**或**break glass**以防止租户范围内帐户锁定的帐户。 在所有管理员被都锁定在租户外部不太可能方案中，可以使用紧急访问管理帐户能够登录到租户采取步骤来恢复访问权限。
   * 可在本文中，找到更多信息[在 Azure AD 中管理紧急访问帐户](../users-groups-roles/directory-emergency-access.md)。
* **服务帐户**并**服务原则**，例如 Azure AD Connect 同步帐户。 服务帐户是不受限于任何特定用户的非交互式帐户。 它们通常由后端服务，并且允许以编程方式访问应用程序。 应排除服务帐户，因为不能以编程方式完成 MFA。
   * 如果你的组织中的脚本或代码中使用这些帐户，请考虑将它们替换为 [托管标识](../managed-identities-azure-resources/overview.md)。 作为临时的解决方法，可以从基准策略中排除这些特定的帐户。
* 不具有或不能使用智能手机的用户。
   * 此策略要求用户使用 Microsoft Authenticator 应用的 mfa 注册。

## <a name="enable-the-baseline-policy"></a>启用基线策略

策略**基准策略：最终用户保护 （预览版）** 预配置，此时将显示在顶部导航到在 Azure 门户中的条件性访问边栏选项卡时。

若要启用此策略并保护你的用户：

1. 登录到 **Azure 门户** 作为全局管理员、 安全管理员或条件性访问管理员。
1. 浏览到**Azure Active Directory** > **条件性访问**。
1. 在策略列表中，选择**基准策略：最终用户保护 （预览版）** 。
1. 设置**启用策略**到**立即使用策略**。
1. 通过单击添加任何用户排除项**用户** > **选择排除的用户**，然后选择需要排除的用户。 单击**选择**然后**完成**。
1. 单击 **保存**。

## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅：

* [条件性访问基线保护策略](concept-baseline-protection.md)
* [保护标识基础结构的五个步骤](../../security/azure-ad-secure-steps.md)
* [什么是 Azure Active Directory 中的条件访问？](overview.md)
