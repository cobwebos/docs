---
title: Azure AD Connect：传递身份验证 - 当前限制 | Microsoft Docs
description: 本文介绍 Azure Active Directory (Azure AD) 直通身份验证的当前限制
services: active-directory
keywords: Azure AD Connect 传递身份验证, 安装 Active Directory, Azure AD 所需的组件, SSO, 单一登录
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/04/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: de42312c580b6bd5b4a5148fd877d55aa00e8804
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2019
ms.locfileid: "56204587"
---
# <a name="azure-active-directory-pass-through-authentication-current-limitations"></a>Azure Active Directory 传递身份验证：当前限制

>[!IMPORTANT]
>Azure Active Directory (Azure AD) 直通身份验证是一项免费功能，不需要拥有任何付费版本的 Azure AD 即可使用。 直通身份验证仅适用于 Azure AD 全球实例，[Microsoft Azure 德国云](https://www.microsoft.de/cloud-deutschland)或 [Microsoft Azure 政府云](https://azure.microsoft.com/features/gov/)中不可用。

## <a name="supported-scenarios"></a>支持的方案

支持以下方案：

- 用户登录到基于 Web 浏览器的应用程序。
- 用户使用旧版协议（例如 Exchange ActiveSync、EAS、SMTP、POP 和 IMAP）登录 Outlook 客户端。
- 用户登录到旧版 Office 客户端应用程序和支持[新式身份验证](https://aka.ms/modernauthga)的 Office 应用程序：Office 2010、2013 和 2016 版。
- 用户登录到旧版协议的应用程序，例如 PowerShell 1.0 版本等。
- 适用于 Windows 10 设备的 Azure AD 联接。
- 进行多重身份验证的应用密码。

## <a name="unsupported-scenarios"></a>不支持的方案

_不_支持以下方案：

- 检测[凭据泄露](../reports-monitoring/concept-risk-events.md#leaked-credentials)的用户。
- Azure AD 域服务需要在租户上启用密码哈希同步。 因此，_仅_使用传递身份验证的租户不适用于需要 Azure AD 域服务的方案。
- 直通身份验证未与 [Azure AD Connect Health](whatis-hybrid-identity-health.md) 集成。

>[!IMPORTANT]
>仅作为不支持方案的变通方法（除 Azure AD Connect Health 集成），可在 Azure AD Connect 向导中的[可选功能](how-to-connect-install-custom.md#optional-features)页上启用“密码哈希同步”。

>[!NOTE]
此外，启用“密码哈希同步”还可以选择在本地基础结构被破坏的情况下故障转移身份验证。 这种从传递身份验证到密码哈希同步的故障转移不是自动进行的。 需要使用 Azure AD Connect 手动切换登录方法。 如果运行 Azure AD Connect 的服务器出现故障，则需要 Microsoft 支持部门的帮助以关闭传递身份验证。

## <a name="next-steps"></a>后续步骤
- [快速入门](how-to-connect-pta-quick-start.md)：快速启动并运行 Azure AD 传递身份验证。
- [从 AD FS 迁移到传递身份验证](https://aka.ms/ADFSTOPTADPDownload) - 从 AD FS（或其他联合技术）迁移到传递身份验证的详细指南。
- [智能锁定](../authentication/howto-password-smart-lockout.md)：了解如何在租户中配置智能锁定功能以保护用户帐户。
- [技术深入了解](how-to-connect-pta-how-it-works.md)：了解传递身份验证功能的工作原理。
- [常见问题](how-to-connect-pta-faq.md)：查找有关传递身份验证功能的常见问题的解答。
- [故障排除](tshoot-connect-pass-through-authentication.md)：了解如何解决传递身份验证功能的常见问题。
- [深入了解安全性](how-to-connect-pta-security-deep-dive.md)：深入了解有关直通身份验证功能的技术信息。
- [Azure AD 无缝 SSO](how-to-connect-sso.md)：详细了解此补充功能。
- [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160611-directory-synchronization-aad-connect)：使用 Azure Active Directory 论坛来提交新的功能请求。
