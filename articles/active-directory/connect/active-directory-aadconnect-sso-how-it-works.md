---
title: "Azure AD Connect：无缝单一登录 - 工作原理 | Microsoft Docs"
description: "本文介绍 Azure Active Directory 无缝单一登录功能的工作原理。"
services: active-directory
keywords: "什么是 Azure AD Connect, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录"
documentationcenter: 
author: swkrish
manager: femila
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: billmath
ms.translationtype: HT
ms.sourcegitcommit: bfd49ea68c597b109a2c6823b7a8115608fa26c3
ms.openlocfilehash: 1f12a8e3e11bc7eee1fc536e231f71c1d17c3fe4
ms.contentlocale: zh-cn
ms.lasthandoff: 07/25/2017

---

# <a name="azure-active-directory-seamless-single-sign-on-technical-deep-dive"></a>Azure Active Directory 无缝单一登录：深入技术探究

本文从技术层面详细介绍了 Azure Active Directory 无缝单一登录（无缝 SSO）功能的工作原理。

## <a name="how-does-seamless-sso-work"></a>无缝 SSO 的工作原理

本节包含两个部分：
1. 无缝 SSO 功能的设置。
2. 单一用户登录事务与无缝 SSO 一起使用的方式。

### <a name="how-does-set-up-work"></a>如何进行设置？

可使用 Azure AD Connect 启用无缝 SSO，如[此处](active-directory-aadconnect-sso-quick-start.md)所示。 启用该功能时，请执行以下步骤：
- 在本地 Active Directory (AD) 中创建一个名为 `AZUREADSSOACCT` 的计算机帐户（表示 Azure AD）。
- 与 Azure AD 安全共享计算机帐户的 Kerberos 解密密钥。
- 此外，创建两个 Kerberos 服务主体名称 (SPN) 来表示 Azure AD 登录期间使用的两个 URL。

>[!NOTE]
> 在与 Azure AD 同步（使用 Azure AD Connect）且要为其用户启用无缝 SSO 的每个 AD 林中创建计算机帐户和 Kerberos SPN。 将 `AZUREADSSOACCT` 计算机帐户移动到在其中存储其他计算机帐户的组织单位 (OU)，确保以相同的方式对该帐户进行管理，并确保它不会被删除。

>[!IMPORTANT]
>我们强烈建议至少每隔 30 天滚动更新一次 `AZUREADSSOACCT` 计算机帐户的 [Kerberos 解密密钥](active-directory-aadconnect-sso-faq.md#how-can-i-roll-over-the-kerberos-decryption-key-of-the-azureadssoacct-computer-account)。

### <a name="how-does-sign-in-with-seamless-sso-work"></a>如何使用无缝 SSO 进行登录？

完成此设置后，无缝 SSO 的工作方式与使用集成 Windows 身份验证 (IWA) 的任何其他登录方式相同。 流程如下所示：

1. 用户尝试从企业网络内部已加入域的企业设备访问应用程序（例如，Outlook Web App - https://outlook.office365.com/owa/）。
2. 如果用户尚未登录，该用户将被重定向到 Azure AD 登录页面。

  >[!NOTE]
  >如果 Azure AD 登录请求包含 `domain_hint`（标识你的租户 - 例如 contoso.onmicrosoft.com）或 `login_hint`（标识用户 - 例如 user@contoso.onmicrosoft.com 或 user@contoso.com）参数，则跳过步骤 2。

3. 用户在 Azure AD 登录页键入其用户名。
4. Azure AD 在后台使用 JavaScript，通过“401 未授权”响应质询浏览器，以提供 Kerberos 票证。
5. 然后，浏览器请求从 Active Directory 获取用于 `AZUREADSSOACCT` 计算机帐户（表示 Azure AD）的票证。
6. Active Directory 查找计算机帐户，然后将使用计算机帐户机密加密的 Kerberos 票证返回给浏览器。
7. 浏览器将它从 Active Directory 获取的 Kerberos 票证转发给 Azure AD（在其中一个[之前添加到浏览器 Intranet 区域设置的 Azure AD URL](active-directory-aadconnect-sso-quick-start.md#step-3-roll-out-the-feature) 上）。
8. Azure AD 使用以前共享的密钥对 Kerberos 票证进行解密，其中包括登录到企业设备的用户的标识。
9. 在评估之后，Azure AD 会将一个令牌返回给应用程序，或者要求用户执行其他验证，例如多重身份验证。
10. 如果用户登录成功，则该用户可以访问应用程序。

下图说明了所涉及的所有组件和步骤。

![无缝单一登录](./media/active-directory-aadconnect-sso/sso2.png)

无缝 SSO 是一项机会型功能，这意味着，如果失败，登录体验将回退到其常规行为 - 即，用户需要输入密码才能登录。

## <a name="next-steps"></a>后续步骤

- [快速入门](active-directory-aadconnect-sso-quick-start.md) - 启动并运行 Azure AD 无缝 SSO。
- [**常见问题**](active-directory-aadconnect-sso-faq.md) - 常见问题解答。
- [故障排除](active-directory-aadconnect-troubleshoot-sso.md) - 了解如何解决使用此功能时遇到的常见问题。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect) - 用于填写新功能请求。

