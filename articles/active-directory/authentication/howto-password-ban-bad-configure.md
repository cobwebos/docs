---
title: 如何在 Azure AD-Azure Active Directory 中禁止弱密码
description: 在 Azure AD 动态禁止密码的环境中禁止弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: f531174c889948308e27109ab4fd80a481ec6bdc
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67798189"
---
# <a name="configuring-the-custom-banned-password-list"></a>配置自定义禁止密码列表

许多组织发现其用户使用常见的当地词汇（比如学校、运动队或名人）来创建密码，这让这些密码很容易猜到。 当用户和管理员尝试更改或重置密码时，除了全局禁止密码列表之外，Microsoft 自定义禁止密码列表允许组织添加要评估和阻止的字符串。

## <a name="add-to-the-custom-list"></a>添加到自定义列表

配置自定义禁止密码列表需要 Active Directory Premium P1 或 P2 许可证。 详细了解 Azure Active Directory 许可的详细信息，请参阅[Azure Active Directory 定价页](https://azure.microsoft.com/pricing/details/active-directory/)。

1. 登录到[Azure 门户](https://portal.azure.com)并浏览到**Azure Active Directory**，**身份验证方法**，然后**密码保护**。
1. 将选项“强制实施自定义列表”  设为“是”  。
1. 将字符串添加到“自定义禁止密码列表”  （每行一个字符串）
   * 自定义受禁的密码列表可以包含最多 1000 个字词。
   * 自定义禁止密码列表不区分大小写。
   * 自定义禁止密码列表会考虑常用字符替换。
      * 示例：“o”和“0”或“a”和“\@”
   * 最小字符串长度为四个字符，最大字符串长度为 16 个字符。
1. 添加所有字符串后，请单击“保存”  。

> [!NOTE]
> 自定义禁止密码列表更新可能需要几个小时才能应用。

> [!NOTE]
> 自定义受禁的密码列表被限制为 1000年条款最多可以拥有。 它不适用于阻止的密码极大的列表。 若要完全利用自定义受禁的密码列表的优势，Microsoft 建议您首先阅读和理解的预期的设计和自定义阻止的密码列表的使用情况 (请参阅[自定义受禁密码列表](concept-password-ban-bad.md#custom-banned-password-list))，和也密码评估算法 (请参阅[如何评估密码](concept-password-ban-bad.md#how-are-passwords-evaluated))。

![在 Azure 门户中的“身份验证方法”下修改自定义禁止密码列表](./media/howto-password-ban-bad/authentication-methods-password-protection.png)

## <a name="how-it-works"></a>工作原理

每次用户或管理员重置或更改 Azure AD 密码时，该密码都会流经禁止密码列表来确认它不在该列表中。 此检查包含在使用 Azure AD 设置或更改的任何密码中。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试密码重置为会被禁止的内容时，他们将看到以下错误消息之一：

* 很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。
* 遗憾的是，不能使用该密码，因为它包含已被管理员阻止的单词或字符。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

[禁止密码列表的概念性概述](concept-password-ban-bad.md)

[Azure AD 密码保护的概念性概述](concept-password-ban-bad-on-premises.md)

[启用与禁止密码列表的本地集成](howto-password-ban-bad-on-premises.md)
