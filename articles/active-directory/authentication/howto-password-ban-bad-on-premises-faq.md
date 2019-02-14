---
title: 本地 Azure AD 密码保护常见问题解答
description: 本地 Azure AD 密码保护常见问题解答
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.openlocfilehash: 62bda3a1c9cb9d53578c2d471b9e63d1f0873234
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/02/2019
ms.locfileid: "55663292"
---
# <a name="preview-azure-ad-password-protection-on-premises---frequently-asked-questions"></a>预览版：本地 Azure AD 密码保护 - 常见问题解答

|     |
| --- |
| Azure AD 密码保护是 Azure Active Directory 的一项公共预览功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

## <a name="general-questions"></a>一般问题

**问：Azure AD 密码保护何时推出正式版 (GA)？**

GA 计划在 CY2019 第一季度推出（2019 年 3 月末之前）。 感谢目前为止提供该功能相关反馈的所有人 - 我们非常感激！

**问：非公有云是否支持本地 Azure AD 密码保护？**

不支持 - 只有公有云才支持本地 Azure AD 密码保护。

**问：如何将 Azure AD 密码保护权益应用到我的一部分本地用户？**

不支持。 部署并启用后，Azure AD 密码保护不会区分对待 - 所有用户都会获得均等的安全权益。

**问：是否支持与其他基于密码筛选器的产品一同安装 Azure AD 密码保护？**

是的。 支持多个已注册的密码筛选器 dll 是一项 Windows 核心功能，并不特定于 Azure AD 密码保护。 在接受密码之前，所有已注册的密码筛选器 dll 必须同意。

**问：为何需要使用 DFSR 进行 sysvol 复制？**

FRS（DFSR 以前的技术）存在很多已知问题，在较新版本的 Windows Server Active Directory 中完全不受支持。 Azure AD 密码保护的零测试将在配置了 FRS 的域上完成。

有关详细信息，请参阅以下文章：

[将 sysvol 复制迁移到 DFSR 的用例](https://blogs.technet.microsoft.com/askds/2010/04/22/the-case-for-migrating-sysvol-to-dfsr)

[FRS 即将终结](https://blogs.technet.microsoft.com/filecab/2014/06/25/the-end-is-nigh-for-frs)

**问：安装或升级 DC 代理软件后为何需要重新启动？**

此项要求是核心 Windows 行为造成的。

**问：是否有任何方法可将 DC 代理配置为使用特定的代理服务器？**

不是。

## <a name="next-steps"></a>后续步骤

如果本文未解答你遇到的本地 Azure AD 密码保护问题，请在下方提交反馈 - 谢谢！

[部署 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)
