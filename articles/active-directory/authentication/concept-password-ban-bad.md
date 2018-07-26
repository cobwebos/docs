---
title: Azure AD 中的动态禁止密码
description: 使用 Azure AD 动态禁止密码在你的环境中禁止弱密码
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: rogoya
ms.openlocfilehash: dfeacb266d6aa6a43e49a39bd19c9699ef65ce82
ms.sourcegitcommit: 1478591671a0d5f73e75aa3fb1143e59f4b04e6a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2018
ms.locfileid: "39162008"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>消除你的组织中的错误密码

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

行业领导者告诉你不要在多个位置使用同一密码，请使用复杂密码，不要使用诸如 Password123 的简单密码。 组织如何可以保证其用户遵守指导？ 它们如何可以确保用户不使用已知在最近的数据外泄中常见的密码？

## <a name="global-banned-password-list"></a>全局禁止密码列表

Microsoft 一直在努力，力求比网络罪犯先行一步。 因此，Azure AD Identity Protection 团队不断在查明常用的已泄露密码。 然后，他们在所谓的全局禁止密码列表中阻止被认为太常用的密码。 网络罪犯在其攻击中也使用类似的策略，因此 Microsoft 未公开发布此列表的内容。 这些易受攻击的密码已被阻止，因为它们已对 Microsoft 的客户造成了实际威胁。 有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/intelligence-report)。

## <a name="preview-custom-banned-password-list"></a>预览版：自定义的禁止密码列表

某些组织可能希望进一步采取措施，在全局禁止的 密码列表基础上添加自己的自定义项，Microsoft 称之为自定义的禁止密码列表。 然后，企业客户（例如 Contoso）可以阻止其品牌名称、公司特定项或其他项的变体。

自定义的禁止密码列表和启用本地 Active Directory 集成的能力是使用 Azure 门户管理的。

![在 Azure 门户中的“身份验证方法”下修改自定义禁止密码列表](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

保护纯云帐户很有帮助，但是许多组织维护着混合方案，包括本地 Windows Server Active Directory。 可以为本地 Windows Server Active Directory（预览版）代理安装 Azure AD 密码保护来将禁止密码列表扩展到你的现有基础结构。 现在，更改、设置或重置本地密码的用户或管理员需要与纯云用户遵循相同的密码策略。

## <a name="how-does-the-banned-password-list-work"></a>禁止密码列表的工作原理

禁止密码列表通过将字符串转换为小写并通过模糊匹配与编辑实例 1 中已知禁止的密码进行比较来匹配列表中的密码。

示例：为组织阻止单词 password
   - 用户尝试将其密码设置为“P@ssword”，它转换为“password”，因为它是 password 的变体，因此将被阻止。
   - 管理员尝试将用户密码设置为“Password123!”， 它转换为“password123!”， 因为它是 password 的变体，因此将被阻止。

用户每次重置或更改 Azure AD 密码时，该密码都会经历此流程来确认它不在禁止密码列表中。 使用自助服务密码重置、密码哈希同步和直通身份验证的混合方案中都会进行此检查。

## <a name="license-requirements"></a>许可要求

全局禁止密码列表会使 Azure Active Directory (Azure AD) 的所有用户受益。

自定义的禁止密码列表需要 Azure AD Basic 许可证。

Windows Server Active Directory 的 Azure AD 密码保护需要 Azure AD Premium 许可证。 

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为某些会被禁止的内容时，他会看到以下错误消息：

很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

* [配置自定义的禁止密码列表](howto-password-ban-bad.md)
* [启用本地 Azure AD 密码保护代理](howto-password-ban-bad-on-premises.md)
