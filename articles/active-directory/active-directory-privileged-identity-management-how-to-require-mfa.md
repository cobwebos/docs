---
title: "如何请求多重身份验证 | Microsoft 文档"
description: "了解如何使用 Azure Active Directory Privileged Identity Management 扩展请求多重身份验证 (MFA) 用于特权标识。"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: 1e3dc4ad-3a6a-4a52-8417-3ca4f84ae05c
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d36fb97f22802b040d2d527ccfd7a48327fe0cb4


---
# <a name="how-to-require-mfa-in-azure-ad-privileged-identity-management"></a>如何在 Azure AD Privileged Identity Management 中请求 MFA
我们建议你为所有管理员请求多重身份验证 (MFA)。 这可降低由于受威胁的密码导致的攻击风险。

你可以请求用户在登录后完成 MFA 质询。 博客文章[用于 Office 365 的 MFA 和用于 Azure 的 MFA](https://blogs.technet.microsoft.com/ad/2014/02/11/mfa-for-office-365-and-mfa-for-azure/) 比较 Office 和 Azure 订阅中包含的内容，以及包含在 Microsoft Azure 多重身份验证产品/服务中的功能。

还可以请求用户在 Azure AD PIM 中激活角色后完成 MFA 质询。 这样一来，如果用户在登录后未完成 MFA 质询，PIM 会提示他们完成此操作。

## <a name="requiring-mfa-in-azure-ad-privileged-identity-management"></a>在 Azure AD Privileged Identity Management 中请求 MFA
当你以特权角色管理员身份在 PIM 中管理标识时，你可能会看到推荐 MFA 用于特权帐户的警报。 在 PIM 仪表板中单击安全警报，将打开一个包含应请求 MFA 的管理员帐户列表的新边栏选项卡。  可通过选择多个角色并随后单击“修复”按钮请求 MFA，也可以单击个别角色旁边的省略号，然后单击“修复”按钮。

> [!IMPORTANT]
> 目前，Azure MFA 仅适用于工作或学校帐户，不适用于 Microsoft 帐户（通常用于登录 Skype、Xbox、Outlook.com 等 Microsoft 服务的个人帐户）。 因此，使用 Microsoft 帐户的任何人都不是符合条件的管理员，因为他们无法使用 MFA 激活其角色。 如果这些用户需要继续使用 Microsoft 帐户管理工作负荷，请立即将其提升到永久管理员。
> 
> 

此外，你可以通过在 PIM 仪表板的“角色”部分中单击某个特定角色，更改该角色的 MFA 要求。 然后，单击角色边栏选项卡中的“设置”，并在多重身份验证下选择“启用”。

## <a name="how-azure-ad-pim-validates-mfa"></a>Azure AD PIM 如何验证 MFA
当用户激活角色时，有两个选项可用于验证 MFA。

最简单的选项是依赖于正在激活特权角色的用户的 Azure MFA。 若要执行此操作，首先检查这些用户是否已获得许可（如有必要），并且是否已注册 Azure MFA。 如何执行此操作的详细信息在[云中的 Azure 多重身份验证入门](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md)中提供。 建议将 Azure AD 配置为在用户登录后针对这些用户强制执行 MFA。 这是因为 MFA 检查将由 Azure AD PIM 本身执行。

或者，如果用户在本地进行身份验证，你可以让标识提供商负责执行 MFA。 例如，如果你将 AD 联合身份验证服务配置为需要基于智能卡的身份验证才能访问 Azure AD，[将 Azure 多重身份验证与 AD FS 配合使用来保护云资源](../multi-factor-authentication/multi-factor-authentication-get-started-adfs-cloud.md)包含有关配置 AD FS 以向 Azure AD 发送声明的说明。 当用户尝试激活角色时，Azure AD PIM 将接受 MFA 在收到相应声明后针对用户进行了验证。

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->
## <a name="next-steps"></a>后续步骤
[!INCLUDE [active-directory-privileged-identity-management-toc](../../includes/active-directory-privileged-identity-management-toc.md)]




<!--HONumber=Dec16_HO5-->


