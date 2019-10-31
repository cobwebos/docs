---
title: 多重身份验证（MFA）和 Privileged Identity Management Azure Active Directory |Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何验证多重身份验证 (MFA)。
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2aa58c354198e648227b9b0b43d1f60546f87d32
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063066"
---
# <a name="multi-factor-authentication-and-privileged-identity-management"></a>多重身份验证和 Privileged Identity Management

我们建议要求所有管理员使用多重身份验证 (MFA)。 这可降低因密码泄露而受到攻击的风险。

你可以要求用户在登录时完成多重身份验证质询。 你还可以要求用户在激活 Azure Active Directory （Azure AD） Privileged Identity Management （PIM）中的角色时完成多重身份验证质询。 这样一来，如果用户在登录时未完成多重身份验证质询，则 Privileged Identity Management 会提示他们执行此操作。

> [!IMPORTANT]
> 目前，Azure 多重身份验证仅适用于工作或学校帐户，而不能使用 Microsoft 个人帐户（通常是用于登录 Microsoft 服务（如 Skype、Xbox 或 Outlook.com）的个人帐户。 因此，使用个人帐户的任何人都不能是符合条件的管理员，因为他们不能使用多重身份验证来激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。

## <a name="how-pim-validates-mfa"></a>PIM 如何验证 MFA

用户激活角色时，有两个选项可用于验证多重身份验证。

最简单的选项是针对正在激活特权角色的用户依赖 Azure 多重身份验证。 为此，请首先检查这些用户是否已获许可，如有必要，是否已注册 Azure 多重身份验证。 有关如何部署 Azure 多重身份验证的详细信息，请参阅[部署基于云的 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)。 建议（但不要求）你将 Azure AD 配置为在用户登录时强制执行多因素身份验证。 这是因为多重身份验证检查将 Privileged Identity Management 本身进行。

或者，如果用户在本地进行身份验证，你可以让标识提供者负责多重身份验证。 例如，如果将 AD 联合身份验证服务配置为需要基于智能卡的身份验证才能访问 Azure AD，[将 Azure 多重身份验证与 AD FS 配合使用来保护云资源](../authentication/howto-mfa-adfs.md)包含有关配置 AD FS 以向 Azure AD 发送声明的说明。 当用户尝试激活某个角色时，Privileged Identity Management 将在收到相应声明后，接受已为该用户验证的多因素身份验证。

## <a name="next-steps"></a>后续步骤

- [在 Privileged Identity Management 中配置 Azure AD 角色设置](pim-how-to-change-default-settings.md)
- [在 Privileged Identity Management 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
