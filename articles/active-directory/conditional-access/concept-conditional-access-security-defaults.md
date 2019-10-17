---
title: Azure Active Directory 安全默认值
description: 安全默认策略，旨在保护组织免受常见攻击
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4921f0605f16a601f6e2ee9a15b71b50d253201
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72453038"
---
# <a name="what-are-security-defaults"></a>什么是安全默认值？

当常见的与身份相关的攻击（例如密码喷涂、重播和网络钓鱼）变得越来越流行时，管理安全性可能会很困难。 创建更简单的方法来使组织更安全地防御这些常见攻击，这是 Azure Active Directory （AD）中安全默认值的目标。 安全默认值使您可以更轻松地保护您的组织免受常见攻击。 安全默认值包含这些常见攻击的预配置安全设置。 Microsoft 正在使所有人都能使用安全默认值。 目标是确保所有组织都启用了基本级别的安全，无需额外付费。

![新安全性默认用户体验的屏幕截图](./media/concept-conditional-access-security-defaults/security-defaults-azure-ad-portal.png)
 
将在你的租户中启用以下安全配置。 

## <a name="unified-mfa-registration"></a>统一 MFA 注册

租户中的所有用户都需要注册 Azure 多重身份验证（MFA）。 用户需要14天才能使用 Microsoft Authenticator 应用注册 Azure MFA。 14天后，在完成 MFA 注册之前，用户将无法登录。

我们了解，在启用安全性默认设置的14天内，某些用户可能会外出并/或不会登录。 为了确保为每个用户提供充足的时间来注册 MFA，14天的期限对于每个用户都是唯一的。 一旦启用了安全默认设置，用户的14天时间就会在第一次成功交互式登录后开始。

## <a name="mfa-enforcement"></a>MFA 强制

### <a name="protecting-administrators"></a>保护管理员

具有特权帐户访问权限的用户可以更好地访问您的环境。 鉴于这些帐户具有的权利，应当特别小心地对待它们。 增强对特权帐户的保护的一种常用方法是要求在使用这些帐户登录时进行更强的帐户验证。 在 Azure Active Directory 中，你可以通过需要多重身份验证来获取更强的帐户验证。

完成 MFA 注册后，每次登录时都需要以下9个 Azure AD 管理员角色才能执行 MFA。

- 全局管理员
- SharePoint 管理员
- Exchange 管理员
- 条件访问管理员
- 安全管理员
- 支持人员管理员/密码管理员
- 计费管理员
- 用户管理员
- 身份验证管理员

### <a name="protecting-all-users"></a>保护所有用户

我们往往认为管理员帐户是唯一需要通过多重身份验证（MFA）进行保护的帐户。 管理员对敏感信息具有广泛的访问权限，并且可以更改订阅范围的设置。 但是，不良的执行组件往往会面向最终用户。 获取访问权限后，这类不良的执行组件可以代表原始帐户持有者请求对特权信息的访问权限，也可以下载整个目录以对整个组织执行网络钓鱼攻击。 为所有用户改进保护的一种常见方法是，需要一种更强的帐户验证形式，例如，多因素身份验证（MFA）。 完成 MFA 注册后，将在必要时提示用户输入 MFA。

### <a name="blocking-legacy-authentication"></a>阻止旧身份验证

为了让用户轻松访问云应用程序，Azure Active Directory (Azure AD) 支持各种身份验证协议，包括旧身份验证。 旧身份验证是指通过以下方式发出的身份验证请求：

- 不使用新式身份验证的旧版 Office 客户端（例如，Office 2010 客户端）
- 任何使用旧版邮件协议的客户端，例如 IMAP/SMTP/POP3

如今，所有泄密的登录尝试都来自传统身份验证。 旧身份验证不支持多重身份验证（MFA）。 即使在目录中启用了 MFA 策略，损坏的执行组件也可以使用旧版协议进行身份验证并绕过 MFA。 在租户中启用安全默认值后，由旧版协议发出的所有身份验证请求都将被阻止。 安全默认值不会阻止 Exchange ActiveSync。

### <a name="protecting-privileged-actions"></a>保护特权操作

组织使用通过 Azure 资源管理器 API 管理的各种 Azure 服务，包括：

- Azure 门户 
- Azure PowerShell 
- Azure CLI

使用 Azure 资源管理器来管理服务是一种高度特权的操作。 Azure 资源管理器可以改变租户范围的配置，例如服务设置和订阅计费。 单因素身份验证容易受到各种攻击，如仿冒和密码喷涂。 因此，在允许访问之前要求进行多重身份验证之前，请务必验证要访问 Azure 资源管理器和更新配置的用户的身份。

在租户中启用安全默认值后，将需要任何访问 Azure 门户、Azure PowerShell 或 Azure CLI 的用户完成多重身份验证。 此策略适用于访问 Azure 资源管理器的所有用户，无论他们是管理员还是用户。 如果用户未注册 MFA，用户将需要使用 Microsoft Authenticator 的应用进行注册，然后才能继续操作。 不会提供14天的 MFA 注册期限。

## <a name="deployment-considerations"></a>部署注意事项

下面是与为你的租户部署安全默认值相关的一些其他注意事项。

### <a name="legacy-protocols"></a>旧协议

电子邮件客户端使用旧的身份验证协议进行身份验证请求。 这些协议不支持 MFA。 Microsoft 所见到的大多数帐户损害都是由不良的执行组件对尝试绕过 MFA 的传统协议执行攻击导致的。 为了确保在登录到管理帐户时需要进行 MFA，而错误的执行组件不能绕过 MFA，安全默认值会阻止对旧协议中的管理员帐户发出的所有身份验证请求。

> [!WARNING]
> 在启用此设置之前，请确保你的管理员没有使用旧版身份验证协议。 有关详细信息，请参阅 [如何从旧身份验证中移出](concept-conditional-access-block-legacy-authentication.md)的文章。

### <a name="conditional-access"></a>条件访问

条件性访问可用于配置策略，这些策略提供的行为由安全默认值启用。 如果使用条件访问，并且在您的环境中启用了条件性访问策略，则将不能使用安全默认值。 如果你的许可证提供条件性访问，但在你的环境中未启用任何条件性访问策略，则欢迎使用安全默认设置，直至你启用 CA 策略为止。

![安全默认值或条件性访问](./media/concept-conditional-access-security-defaults/security-defaults-conditional-access.png)

下面是有关如何使用条件性访问来配置等效策略的分步指南：

- [需要对管理员的 MFA](howto-conditional-access-policy-admin-mfa.md)
- [需要 MFA 进行 Azure 管理](howto-conditional-access-policy-azure-management.md)
- [阻止旧身份验证](howto-conditional-access-policy-block-legacy.md)

## <a name="enabling-security-defaults"></a>启用安全默认值

若要在目录中启用安全默认值：

1. 登录到 [Azure 门户](https://portal.azure.com)@no__t 安全管理员、条件性访问管理员或全局管理员。
1. 浏览到 **Azure Active Directory**  @ no__t **属性**
1. 选择 "**管理安全性默认值**"
1. 设置 "**启用安全默认**设置" 切换为 **"是"** 。
1. 单击 "保存"。

## <a name="next-steps"></a>后续步骤

[常见的条件访问策略](concept-conditional-access-policy-common.md)

[什么是条件性访问？](overview.md)
