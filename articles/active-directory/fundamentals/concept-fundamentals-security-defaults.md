---
title: Azure 活动目录安全默认值
description: 安全默认策略，可帮助保护组织免受常见攻击
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 02/11/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3139d39797c3bc576bb39f1438b7e6d3f37e3c5c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "78248843"
---
# <a name="what-are-security-defaults"></a>什么是安全默认值？

当常见的与身份相关的攻击越来越流行时，管理安全性可能很困难。 这些攻击包括密码喷射、重播和网络钓鱼。

Azure 活动目录 （Azure AD） 中的安全默认值使安全性更加容易，并有助于保护组织。 安全默认值包含常见攻击的预配置安全设置。 

微软正在使安全默认值可供所有人使用。 目标是确保所有组织都启用基本安全级别，无需额外费用。 在 Azure 门户中打开安全默认值。

![启用安全默认值的 Azure 门户的屏幕截图，](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
> [!TIP]
> 如果您的租户是在 2019 年 10 月 22 日或之后创建的，则可能是您遇到新的按默认安全行为，并且已在租户中启用了安全默认值。 为了保护所有用户，安全默认值正在推广到创建的所有新租户。

有关安全默认值为何可用的更多详细信息，请参阅 Alex Weinert 的博客文章"[介绍安全默认值](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)"。

## <a name="unified-multi-factor-authentication-registration"></a>统一多重身份验证注册

租户中的所有用户都必须以 Azure 多重身份验证服务的形式注册多重身份验证 （MFA）。 用户有 14 天的时间使用 Microsoft 身份验证器应用注册多重身份验证。 14 天后，在多因素身份验证注册完成之前，用户将无法登录。

我们理解，某些用户可能在启用安全默认值后的 14 天内外出或无法登录。 为了确保每个用户都有充足的时间注册多重身份验证，14 天的期限对于每个用户都是唯一的。 用户 14 天的时间从启用安全默认值后首次成功交互登录后开始。

## <a name="multi-factor-authentication-enforcement"></a>多重身份验证实施

### <a name="protecting-administrators"></a>保护管理员

有权访问特权帐户的用户增加了对您环境的访问。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 改进特权帐户保护的一个常见方法是要求对登录进行更强有力的帐户验证。 在 Azure AD 中，可以通过要求多重身份验证来获得更强的帐户验证。

完成对多重身份验证的注册后，每次登录时都需要以下九个 Azure AD 管理员角色执行其他身份验证：

- 全局管理员
- SharePoint 管理员
- Exchange 管理员
- 条件访问管理员
- 安全管理员
- 帮助台管理员或密码管理员
- 计费管理员
- 用户管理员
- 身份验证管理员

### <a name="protecting-all-users"></a>保护所有用户

我们倾向于认为管理员帐户是唯一需要额外身份验证层的帐户。 管理员对敏感信息拥有广泛的访问权限，并且可以更改订阅范围的设置。 但是攻击者往往以最终用户为目标。 

这些攻击者获得访问权限后，他们可以代表原始帐户持有人请求访问特权信息。 他们甚至可以下载整个目录，对整个组织执行网络钓鱼攻击。 

改善对所有用户保护的一个常见方法是要求每个人进行更强有力的帐户验证，例如多重身份验证。 用户完成多重身份验证注册后，将在必要时提示他们进行其他身份验证。

### <a name="blocking-legacy-authentication"></a>阻止旧版身份验证

为了方便用户访问云应用，Azure AD 支持各种身份验证协议，包括旧版身份验证。 *旧身份验证*是一个术语，它是指由：

- 不使用现代身份验证的客户端（例如，Office 2010 客户端）。
- 使用较旧的邮件协议（如 IMAP、SMTP 或 POP3）的任何客户端。

如今，大多数妥协登录尝试都来自旧版身份验证。 旧身份验证不支持多重身份验证。 即使您在目录中启用了多重身份验证策略，攻击者也可以使用较旧的协议进行身份验证并绕过多重身份验证。 

在租户中启用安全默认值后，旧协议发出的所有身份验证请求都将被阻止。 安全默认值阻止交换活动同步基本身份验证。

> [!WARNING]
> 在启用安全默认值之前，请确保管理员不使用较旧的身份验证协议。 有关详细信息，请参阅[如何远离旧版身份验证](concept-fundamentals-block-legacy-authentication.md)。

### <a name="protecting-privileged-actions"></a>保护特权操作

组织使用通过 Azure 资源管理器 API 管理的各种 Azure 服务，包括：

- Azure 门户 
- Azure PowerShell 
- Azure CLI

使用 Azure 资源管理器来管理服务是需要很高特权的操作。 Azure 资源管理器可以更改租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到网络钓鱼和密码喷淋等多种攻击的影响。 

请务必验证想要访问 Azure 资源管理器并更新配置的用户的身份。 在允许访问之前，通过要求其他身份验证来验证其身份。

在租户中启用安全默认值后，任何访问 Azure 门户、Azure PowerShell 或 Azure CLI 的用户都需要完成其他身份验证。 此策略适用于访问 Azure 资源管理器的所有用户，无论他们是管理员还是用户。 

如果用户未注册多重身份验证，则需要使用 Microsoft 身份验证器应用进行注册才能继续。 不会提供 14 天多重身份验证注册期。

> [!NOTE]
> 2017 年前 Exchange Online 租户默认禁用了现代身份验证。 为了避免通过这些租户进行身份验证时出现登录循环的可能性，必须[启用现代身份验证](https://docs.microsoft.com/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

> [!NOTE]
> Azure AD Connect 同步帐户从安全默认值中排除，不会提示注册或执行多重身份验证。 组织不应出于其他目的使用此帐户。

## <a name="deployment-considerations"></a>部署注意事项

以下其他注意事项与租户的安全默认值的部署有关。

### <a name="authentication-methods"></a>身份验证方法

安全默认值允许**仅使用使用通知的 Microsoft 身份验证器应用**注册和使用 Azure 多重身份验证。 条件访问允许使用管理员选择启用的任何身份验证方法。

|   | 安全默认值 | 条件性访问 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 |   | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |
| 应用密码 |   | X* |

• 应用密码仅在每个用户 MFA 中可用，并且只有在管理员启用的情况下才具有旧版身份验证方案。

### <a name="conditional-access"></a>条件性访问

可以使用条件访问配置类似于安全默认值的策略，但具有更粒度的策略，包括用户排除，这在安全默认值中不可用。 如果您正在使用条件访问并在环境中启用条件访问策略，则安全默认值将不可用。 如果您拥有提供条件访问但未在环境中启用任何条件访问策略的许可证，则欢迎在启用条件访问策略之前使用安全默认值。 有关 Azure AD 许可的详细信息，请参阅[Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

![警告消息，您可以具有安全默认值或条件访问，而不是两者](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

以下是有关如何使用条件访问配置等效策略的分步指南：

- [要求对管理员执行 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [要求将 MFA 用于 Azure 管理](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [阻止传统身份验证](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [要求所有用户执行 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [需要 Azure MFA 注册](../identity-protection/howto-identity-protection-configure-mfa-policy.md)- 需要 Azure AD 标识保护

## <a name="enabling-security-defaults"></a>启用安全默认值

要在目录中启用安全默认值，请进行：

1. 以安全管理员、条件访问管理员或全局管理员的身份登录到 [Azure 门户](https://portal.azure.com) 。
1. 浏览到 **Azure 活动目录** > **属性**。
1. 选择 **"管理安全默认值**"。
1. 将**启用安全默认值**切换为 **"是**"。
1. 选择“保存”。****

## <a name="disabling-security-defaults"></a>禁用安全默认值

选择实施替换安全默认值的条件访问策略的组织必须禁用安全默认值。 

![警告消息禁用安全默认值以启用条件访问](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

要禁用目录中的安全默认值，请进行：

1. 以安全管理员、条件访问管理员或全局管理员的身份登录到 [Azure 门户](https://portal.azure.com) 。
1. 浏览到 **Azure 活动目录** > **属性**。
1. 选择 **"管理安全默认值**"。
1. 将**启用安全默认值**设置为 **"否**"。
1. 选择“保存”。****

## <a name="next-steps"></a>后续步骤

[常用条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)
