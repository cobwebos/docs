---
title: Azure Active Directory 安全默认值
description: 有助于保护组织免受常见攻击的安全默认策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 806447f788b7a394b29ee08e8b562662c2cc3e1b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74208397"
---
# <a name="what-are-security-defaults"></a>什么是安全默认值？

当常见标识相关的攻击越来越普及时，管理安全性可能会很困难。 这些攻击包括密码喷涂、重播和网络钓鱼。

Azure Active Directory （Azure AD）中的安全性默认值使其更易于安全，并帮助保护你的组织。 安全默认值包含常见攻击的预配置安全设置。 

Microsoft 正在使所有人都能使用安全默认值。 目标是确保所有组织都启用了基本级别的安全，无需额外付费。 在 Azure 门户中打开安全性默认值。

![使用切换启用安全默认设置的 Azure 门户屏幕截图](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
将在你的租户中启用以下安全配置。 

## <a name="unified-multi-factor-authentication-registration"></a>统一的多重身份验证注册

租户中的所有用户都必须以 Azure 多重身份验证服务的形式注册多重身份验证（MFA）。 用户有14天的时间，可以使用 Microsoft Authenticator 应用注册多重身份验证。 14天后，用户将无法登录，直到多重身份验证注册完成。

我们知道，某些用户可能会外出，或在启用安全默认值后14天内不会登录。 若要确保每个用户都有充足的时间来注册多重身份验证，14天的期限对于每个用户都是唯一的。 启用安全默认设置后，用户14天的第一次成功交互式登录后开始。

## <a name="multi-factor-authentication-enforcement"></a>强制执行多重身份验证

### <a name="protecting-administrators"></a>保护管理员

具有特权帐户访问权限的用户可以更好地访问您的环境。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 改善特权帐户保护的一种常用方法是，要求进行更强大的帐户验证形式用于登录。 在 Azure AD 中，你可以通过需要多重身份验证来获取更强的帐户验证。

完成多重身份验证注册后，每次登录时都需要以下9个 Azure AD 管理员角色才能执行其他身份验证：

- 全局管理员
- SharePoint 管理员
- Exchange 管理员
- 条件访问管理员
- 安全管理员
- 支持人员管理员或密码管理员
- 计费管理员
- 用户管理员
- 身份验证管理员

### <a name="protecting-all-users"></a>保护所有用户

我们往往认为管理员帐户是唯一需要额外身份验证层的帐户。 管理员对敏感信息拥有广泛的访问权限，并且可以更改订阅范围的设置。 但攻击者往往会以最终用户为目标。 

这些攻击者获得访问权限后，可以代表原始帐户持有者请求对特权信息的访问权限。 它们甚至可以下载整个目录，对整个组织执行网络钓鱼攻击。 

提高所有用户保护的一种常用方法是，要求对所有人都使用一种更强的帐户验证形式，例如多重身份验证。 用户完成多重身份验证注册后，将在必要时提示用户进行其他身份验证。

### <a name="blocking-legacy-authentication"></a>阻止旧身份验证

为了让用户轻松访问你的云应用，Azure AD 支持多种身份验证协议，包括旧身份验证。 *旧身份验证*是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的旧版 Office 客户端（例如，Office 2010 客户端）。
- 使用 IMAP、SMTP 或 POP3 等旧邮件协议的任何客户端。

如今，大多数泄密的登录尝试都来自传统身份验证。 旧身份验证不支持多重身份验证。 即使在目录中启用了多重身份验证策略，攻击者仍可使用较旧的协议进行身份验证，并绕过多重身份验证。 

在租户中启用安全默认设置后，较旧协议发出的所有身份验证请求都将被阻止。 安全默认值不会阻止 Exchange ActiveSync。

### <a name="protecting-privileged-actions"></a>保护特权操作

组织使用通过 Azure 资源管理器 API 管理的各种 Azure 服务，包括：

- Azure 门户 
- Azure PowerShell 
- Azure CLI

使用 Azure 资源管理器来管理服务是需要很高特权的操作。 Azure 资源管理器可以更改租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击，如仿冒和密码喷涂。 

验证要访问 Azure 资源管理器和更新配置的用户的标识很重要。 在允许访问之前，通过要求额外的身份验证来验证其身份。

启用租户中的安全性默认值后，访问 Azure 门户、Azure PowerShell 或 Azure CLI 的任何用户将需要完成其他身份验证。 此策略适用于访问 Azure 资源管理器的所有用户，无论这些用户是管理员还是用户。 

如果用户未注册多重身份验证，则用户将需要使用 Microsoft Authenticator 应用进行注册，然后才能继续操作。 不会提供14天的多重身份验证注册期限。

## <a name="deployment-considerations"></a>部署注意事项

以下其他注意事项与为租户部署安全默认值相关。

### <a name="older-protocols"></a>旧协议

邮件客户端使用较旧的身份验证协议（如 IMAP、SMTP 和 POP3）发出身份验证请求。 这些协议不支持多重身份验证。 Microsoft 看到的大多数帐户损害都受到对尝试绕过多重身份验证的旧版协议的攻击。 

为了确保登录到管理帐户时需要多重身份验证，并且攻击者无法跳过它，默认情况下，安全默认情况下会阻止从较旧的协议向管理员帐户发出的所有身份验证请求。

> [!WARNING]
> 在启用此设置之前，请确保你的管理员没有使用较旧的身份验证协议。 有关详细信息，请参阅[如何离开旧身份验证](concept-conditional-access-block-legacy-authentication.md)。

### <a name="conditional-access"></a>条件访问

可以使用条件性访问来配置策略，这些策略提供的行为由安全默认值启用。 如果使用条件访问，并且在您的环境中启用了条件性访问策略，则不能使用安全默认值。 如果你的许可证提供条件性访问，但你的环境中未启用任何条件性访问策略，则欢迎使用安全默认设置，直至你启用条件性访问策略。

![不能同时具有安全默认值或条件访问的警告消息](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

下面是有关如何使用条件性访问来配置等效策略的分步指南：

- [要求对管理员执行 MFA](howto-conditional-access-policy-admin-mfa.md)
- [要求将 MFA 用于 Azure 管理](howto-conditional-access-policy-azure-management.md)
- [阻止旧式身份验证](howto-conditional-access-policy-block-legacy.md)
- [要求对所有用户进行 MFA](howto-conditional-access-policy-all-users-mfa.md)
- [需要 AZURE MFA 注册](../identity-protection/howto-identity-protection-configure-mfa-policy.md)-需要 Azure AD Identity Protection

## <a name="enabling-security-defaults"></a>启用安全默认值

若要在目录中启用安全默认值：

1. 以安全管理员、条件性访问管理员或全局管理员身份登录到 [Azure 门户](https://portal.azure.com) 。
1. 浏览到 **Azure Active Directory** >" **属性**"。
1. 选择 "**管理安全性默认值**"。
1. 设置 "**启用安全默认**设置" 切换为 **"是"** 。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[常见的条件访问策略](concept-conditional-access-policy-common.md)

[什么是条件访问？](overview.md)
