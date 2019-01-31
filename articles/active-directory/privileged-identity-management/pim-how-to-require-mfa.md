---
title: 多重身份验证 (MFA) 和 PIM - Azure | Microsoft Docs
description: 了解 Azure AD Privileged Identity Management (PIM) 如何验证多重身份验证 (MFA)。
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.service: active-directory
ms.topic: conceptual
ms.workload: identity
ms.subservice: pim
ms.date: 08/31/2018
ms.author: rolyon
ms.custom: pim
ms.openlocfilehash: c26050eb8844368554726d0e642b7b7384649763
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195575"
---
# <a name="multi-factor-authentication-mfa-and-pim"></a>多重身份验证 (MFA) 和 PIM

我们建议要求所有管理员使用多重身份验证 (MFA)。 这可降低因密码泄露而受到攻击的风险。

可以请求用户在登录后完成 MFA 质询。 还可以要求用户在 Azure AD Privileged Identity Management (PIM) 中激活角色后完成 MFA 质询。 这样一来，如果用户在登录后未完成 MFA 质询，PIM 会提示他们完成此操作。

> [!IMPORTANT]
> 目前，Azure MFA 仅适用于工作或学校帐户，不适用于 Microsoft 帐户（通常用于登录 Skype、Xbox、Outlook.com 等 Microsoft 服务的个人帐户）。 因此，使用 Microsoft 帐户的任何人都不是符合条件的管理员，因为他们无法使用 MFA 激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。

## <a name="how-pim-validates-mfa"></a>PIM 如何验证 MFA

当用户激活角色时，有两个选项可用于验证 MFA。

最简单的选项是依赖于正在激活特权角色的用户的 Azure MFA。 若要执行此操作，首先检查这些用户是否已获得许可（如有必要），并且是否已注册 Azure MFA。 有关如何部署 Azure MFA 的详细信息，请参阅[部署基于云的 Azure 多重身份验证](../authentication/howto-mfa-getstarted.md)。 建议将 Azure AD 配置为在用户登录后针对这些用户强制执行 MFA。 这是因为 MFA 检查由 PIM 本身执行。

或者，如果用户在本地进行身份验证，可以让标识提供商负责执行 MFA。 例如，如果将 AD 联合身份验证服务配置为需要基于智能卡的身份验证才能访问 Azure AD，[将 Azure 多重身份验证与 AD FS 配合使用来保护云资源](../authentication/howto-mfa-adfs.md)包含有关配置 AD FS 以向 Azure AD 发送声明的说明。 当用户尝试激活角色时，PIM 将接受 MFA 在收到相应声明后针对用户进行了验证。

## <a name="next-steps"></a>后续步骤

- [在 PIM 中配置 Azure AD 目录角色设置](pim-how-to-change-default-settings.md)
- [在 PIM 中配置 Azure 资源角色设置](pim-resource-roles-configure-role-settings.md)
