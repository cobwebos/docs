---
title: Azure Active Directory 安全默认值
description: 有助于保护组织免受 Azure AD 中常见攻击的安全默认值策略
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 05/13/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.custom: contperfq4
ms.openlocfilehash: 2c056bd4d5fa9037ce00588269c0da2937ff57ff
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90705327"
---
# <a name="what-are-security-defaults"></a>什么是安全默认值？

随着常见的与标识相关的攻击（如密码喷射、重放和网络钓鱼）越来越普遍，可能会使安全管理变得更困难。 利用安全默认值，可以更轻松地通过预配置的安全设置来保护组织免受这些攻击：

- 要求所有用户注册 Azure 多重身份验证。
- 要求管理员执行多重身份验证。
- 阻止旧身份验证协议。
- 要求用户在必要时执行多重身份验证。
- 保护特权活动，如访问 Azure 门户。

![带有启用安全默认设置切换的 Azure 门户的屏幕截图](./media/concept-fundamentals-security-defaults/security-defaults-azure-ad-portal.png)
 
有关为何要提供安全默认值的详细信息，请参阅 Alex Weinert 的博客文章[安全默认值简介](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/introducing-security-defaults/ba-p/1061414)。

## <a name="availability"></a>可用性

Microsoft 正在向所有用户提供安全默认值。 目标是确保所有组织能在不增加费用的情况下实现基本级别的安全防护。 你可以在 Azure 门户中打开安全默认值。 如果你的租户是在 2019 年 10 月 22 日或之后创建的，则可能已在租户中启用了安全默认值。 为了保护所有用户，将向所有新创建的租户推出安全默认值。

### <a name="whos-it-for"></a>目标用户

- 如果贵组织想要提高安全状况，但不知道如何或在何处着手，那么安全默认值就是理想之选。
- 如果贵组织使用的是 Azure Active Directory 许可的免费层，则安全默认值适合你。

### <a name="who-should-use-conditional-access"></a>谁应使用条件访问？

- 如果贵组织当前使用条件访问策略将信号组合在一起，以进行决策和实施组织策略，那么安全默认值可能不适用。 
- 如果贵组织具有 Azure Active Directory Premium 许可证，则安全默认值可能不适用。
- 如果贵组织有复杂的安全要求，则应考虑条件访问。

## <a name="policies-enforced"></a>执行策略

### <a name="unified-multi-factor-authentication-registration"></a>统一的多重身份验证注册

租户中的所有用户都必须以 Azure 多重身份验证的形式注册多重身份验证 (MFA)。 用户可以在 14 天内使用 Microsoft Authenticator 应用注册 Azure 多重身份验证。 14 天后，在完成注册之前用户将无法登录。 用户的 14 天周期从启用安全默认值后首次成功进行交互式登录算起。

### <a name="protecting-administrators"></a>保护管理员

具有特权访问权限的用户可以增加对你环境的访问权限。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 增强对特权帐户保护的一种常用方法是，要求在登录时进行更强的帐户验证。 在 Azure AD 中，可以通过要求进行多重身份验证来实现更强的帐户验证。

完成 Azure 多重身份验证注册后，以下 9 个 Azure AD 管理员角色在每次登录时都需要执行额外的身份验证：

- 全局管理员
- SharePoint 管理员
- Exchange 管理员
- 条件访问管理员
- 安全管理员
- 支持管理员
- 计费管理员
- 用户管理员
- 身份验证管理员

### <a name="protecting-all-users"></a>保护所有用户

我们通常认为管理员帐户是唯一需要额外身份验证层的帐户。 管理员对敏感信息具有广泛的访问权限，并且可以更改订阅范围的设置， 但攻击者经常以最终用户为目标。 

这些攻击者获得访问权限后，可以代表原始帐户持有者请求对特权信息的访问权限。 他们甚至可以下载整个目录，对整个组织进行网络钓鱼攻击。 

提高对所有用户保护的一种常用方法是，要求对所有用户进行更强形式的帐户验证，如多重身份验证。 用户完成多重身份验证注册后，将在必要时提示用户进行其他身份验证。 此功能可保护注册到 Azure AD 的所有应用程序，包括 SaaS 应用程序。

### <a name="blocking-legacy-authentication"></a>阻止旧式身份验证

为使用户轻松访问云应用程序，Azure AD 支持各种身份验证协议，包括旧身份验证。 术语“旧式身份验证”是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的客户端（例如 Office 2010 客户端）。
- 使用 IMAP、SMTP 或 POP3 等旧式邮件协议的任何客户端。

当今，大部分存在危害性的登录企图都来自旧式身份验证。 旧式身份验证不支持多重身份验证。 即使在目录中启用了多重身份验证策略，攻击者仍可使用旧协议进行身份验证，并绕过多重身份验证。 

在租户中启用安全默认值后，旧协议发出的所有身份验证请求都将被阻止。 安全默认值会阻止 Exchange Active Sync 基本身份验证。

> [!WARNING]
> 启用安全默认值之前，请确保管理员没有使用旧身份验证协议。 有关详细信息，请参阅[如何弃用旧身份验证](concept-fundamentals-block-legacy-authentication.md)。

- [如何使用 Microsoft 365 设置多功能设备或应用程序以发送电子邮件](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)

### <a name="protecting-privileged-actions"></a>保护特权操作

组织会使用各种通过 Azure 资源管理器 API 管理的 Azure 服务，包括：

- Azure 门户 
- Azure PowerShell 
- Azure CLI

使用 Azure 资源管理器来管理服务是一种高度特权操作。 Azure 资源管理器可以改变租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击，如网络钓鱼和密码喷射。 

务必要对希望访问 Azure 资源管理器和更新配置的用户的标识进行验证。 在允许访问之前，通过要求额外的身份验证来验证其标识。

启用租户中的安全默认值后，访问 Azure 门户、Azure PowerShell 或 Azure CLI 的任何用户都需要完成额外的身份验证。 此策略适用于访问 Azure 资源管理器的所有用户，无论他们是管理员还是用户。 

> [!NOTE]
> 默认情况下，2017 之前的 Exchange Online 租户已禁用新式身份验证。 为了避免在对这些租户进行身份验证时出现登录循环，必须[启用新式身份验证](/exchange/clients-and-mobile-in-exchange-online/enable-or-disable-modern-authentication-in-exchange-online)。

> [!NOTE]
> Azure AD Connect 同步帐户将从安全默认值中排除，系统不会提示该帐户注册或执行多重身份验证。 组织不应出于其他目的使用此帐户。

## <a name="deployment-considerations"></a>部署注意事项

下面是与安全默认值的部署相关的其他注意事项。

### <a name="authentication-methods"></a>身份验证方法

这些免费的安全默认值允许仅通过使用通知的 Microsoft Authenticator 应用注册和使用 Azure 多重身份验证。 条件访问允许使用管理员选择启用的任何身份验证方法。

| 方法 | 安全默认值 | 条件性访问 |
| --- | --- | --- |
| 通过移动应用发送通知 | X | X |
| 移动应用或硬件标志提供的验证码 | X** | X |
| 向手机发送短信 |   | X |
| 拨打电话 |   | X |
| 应用密码 |   | X*** |

- ** 用户可以使用来自 Microsoft Authenticator 应用的验证码，但只能使用通知选项进行注册。
- *** 仅当管理员启用了应用密码时，应用密码才可在旧身份验证场景中的每用户 MFA 中使用。

### <a name="disabled-mfa-status"></a>禁用的 MFA 状态

如果贵组织以前是基于每用户的 Azure 多重身份验证的用户，则你在查看多重身份验证状态页时，若没有看到处于“启用”或“已执行”状态的用户，请不必担心。 “禁用”是使用安全默认值或基于条件访问的 Azure 多重身份验证用户的适当状态。

### <a name="conditional-access"></a>条件性访问

你可以使用条件访问来配置类似于安全默认值的策略，但要具有更细的粒度，包括在安全默认值中不可用的用户排除。 如果使用的是条件访问，并且在环境中启用了条件访问策略，则不能使用安全默认值。 如果你的许可证提供条件访问，但在环境中未启用任何条件访问策略，则欢迎使用安全默认值，直至你启用条件访问策略。 有关 Azure AD 许可的更多信息，请参阅 [Azure AD 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

![不能同时具有安全默认值或条件访问的警告消息](./media/concept-fundamentals-security-defaults/security-defaults-conditional-access.png)

下面是分步指南，介绍如何使用条件访问来配置与安全默认值所启用的策略相同的策略：

- [要求对管理员执行 MFA](../conditional-access/howto-conditional-access-policy-admin-mfa.md)
- [要求将 MFA 用于 Azure 管理](../conditional-access/howto-conditional-access-policy-azure-management.md)
- [阻止旧式身份验证](../conditional-access/howto-conditional-access-policy-block-legacy.md)
- [要求对所有用户执行 MFA](../conditional-access/howto-conditional-access-policy-all-users-mfa.md)
- [要求 Azure MFA 注册](../identity-protection/howto-identity-protection-configure-mfa-policy.md) - 要求 Azure AD Premium P2 的 Azure AD 标识保护部分。

## <a name="enabling-security-defaults"></a>启用安全默认值

若要在目录中启用安全默认值：

1. 以安全管理员、条件访问管理员或全局管理员身份登录  [Azure 门户](https://portal.azure.com) 。
1. 浏览到“Azure Active Directory” ****  >“属性” **** 。
1. 选择“管理安全默认值”。
1. 将“启用安全默认值”切换键设置为“是”。
1. 选择“保存”。

## <a name="disabling-security-defaults"></a>禁用安全默认值

选择实施条件访问策略来取代安全默认值的组织必须禁用安全默认值。 

![警告消息禁用安全默认值以启用条件访问](./media/concept-fundamentals-security-defaults/security-defaults-disable-before-conditional-access.png)

若要在目录中禁用安全默认值：

1. 以安全管理员、条件访问管理员或全局管理员身份登录  [Azure 门户](https://portal.azure.com) 。
1. 浏览到“Azure Active Directory” ****  >“属性” **** 。
1. 选择“管理安全默认值”。
1. 将“启用安全默认值”切换键设置为“否”。
1. 选择“保存”。

## <a name="next-steps"></a>后续步骤

[常见条件访问策略](../conditional-access/concept-conditional-access-policy-common.md)