---
title: Azure MFA 服务器或服务在本地还是在云中？
description: 作为 Azure AD 管理员，我是否需要了解应部署哪个版本的 MFA？
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: michmcla
ms.openlocfilehash: 0d68c88bdad63bb022babcc4a6ee4ee7c59ce58a
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39158441"
---
# <a name="which-version-of-azure-mfa-is-right-for-my-organization"></a>哪个版本的 Azure MFA 适合我的组织？

在决定部署多重身份验证 (MFA) 的位置和方式之前，需要回答三个基本问题。

* [我要保护什么](#what-am-i-trying-to-secure)
* [用户位于何处](#where-are-the-users-located)
* [需要哪些功能？](#what-features-do-i-need)

以下各节均提供了有助于回答上述问题的详细信息。

## <a name="what-am-i-trying-to-secure"></a>我要保护什么？

为了确定适当的双重验证解决方案，首先你必须回答一个问题：试图使用附加身份验证因素来保护什么？ 它是 Azure 中的应用程序， 还是远程访问系统？ 确定你尝试保护的项目后，就可以回答“需要在何处启用多重身份验证”这个问题。

| 想要保护什么 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| 第一方 Microsoft 应用 |● |● |
| 应用库中的 SaaS 应用 |● |  |
| 通过 Azure AD 应用代理发布的 Web 应用程序 |● |  |
| 不是通过 Azure AD 应用代理发布的 IIS 应用程序 | |● |
| VPN、RDG 等远程访问系统 | ● | ● |

## <a name="where-are-the-users-located"></a>用户位于何处

接下来，确定组织的用户位于何处有助于确定要使用的正确解决方案，不论是在云中还是在本地使用 MFA 服务器。

| 用户位置 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| Azure Active Directory |● | |
| Azure AD 和使用 AD FS 联合身份验证的本地 AD |● |● |
| 使用 Azure AD Connect 的 Azure AD 和本地 AD - 没有密码哈希同步或直通身份验证 |● |● |
| 使用 Azure AD Connect 的 Azure AD 和本地 AD - 使用密码哈希同步或直通身份验证 |● | |
| 本地 Active Directory | |● |

## <a name="what-features-do-i-need"></a>需要哪些功能？

下表是云中多重身份验证和多重身份验证服务器的功能比较。

| 功能 | 云中的 MFA | MFA 服务器 |
| --- |:---:|:---:|
| 将移动应用通知用作第二个因素 | ● | ● |
| 将移动应用验证码用作第二个因素 | ● | ● |
| 将电话呼叫用作第二个因素 | ● | ● |
| 将单向短信用作第二个因素 | ● | ● |
| 将硬件令牌用作第二个因素 | | ● |
| 不支持 MFA 的 Office 365 客户端的应用密码 | ● | |
| 管理员控制身份验证方法 | ● | ● |
| PIN 模式 | | ● |
| 欺诈警报 | ● | ● |
| MFA 报告 | ● | ● |
| 一次性跳过 | | ● |
| 通话的自定义问候语 | ● | ● |
| 可自定义的来电者 ID | ● | ● |
| 受信任的 IP | ● | ● |
| 记住受信任的设备的 MFA | ● | |
| 条件性访问 | ● | ● |
| 缓存 |  | ● |

## <a name="next-steps"></a>后续步骤

了解云中 Azure 多重身份验证和本地 MFA 服务器的差异以后，即可设置和使用 Azure 多重身份验证。 选择代表方案的图标

<center>

[![云中的 MFA](./media/concept-mfa-whichversion/cloud2.png)](howto-mfa-getstarted.md)  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; [![MFA 服务器 ](./media/concept-mfa-whichversion/server2.png)](howto-mfaserver-deploy.md) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp; </center>
