---
title: Azure AD 中的动态禁止密码
description: 使用 Azure AD 动态禁止密码在你的环境中禁止弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: rogoya
ms.openlocfilehash: 916ef921bf2ad183e3fb74c640ccfa7049559a72
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/30/2019
ms.locfileid: "55295859"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>消除你的组织中的错误密码

|     |
| --- |
| Azure AD 密码保护和自定义禁止密码列表是 Azure Active Directory 的公共预览版功能。 有关预览版的详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。|
|     |

行业领导者告诉你不要在多个位置使用同一密码，请使用复杂密码，不要使用诸如 Password123 的简单密码。 组织如何可以保证其用户遵守指导？ 它们如何可以确保用户不使用已知在最近的数据外泄中常见的密码？

## <a name="global-banned-password-list"></a>全局禁止密码列表

Microsoft 一直在努力，力求比网络罪犯先行一步。 因此，Azure AD Identity Protection 团队不断在查明常用的已泄露密码。 然后，他们在所谓的全局禁止密码列表中阻止被认为太常用的密码。 网络罪犯在其攻击中也使用类似的策略，因此 Microsoft 未公开发布此列表的内容。 这些易受攻击的密码已被阻止，因为它们已对 Microsoft 的客户造成了实际威胁。 有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="preview-custom-banned-password-list"></a>预览版：自定义禁止密码列表

某些组织可能希望进一步采取措施，在全局禁止的 密码列表基础上添加自己的自定义项，Microsoft 称之为自定义的禁止密码列表。 然后，企业客户（例如 Contoso）可以阻止其品牌名称、公司特定项或其他项的变体。

自定义的禁止密码列表和启用本地 Active Directory 集成的能力是使用 Azure 门户管理的。

![在 Azure 门户中的“身份验证方法”下修改自定义禁止密码列表](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

保护纯云帐户很有帮助，但是许多组织维护着混合方案，包括本地 Windows Server Active Directory。 可以为本地 Windows Server Active Directory（预览版）代理安装 Azure AD 密码保护来将禁止密码列表扩展到你的现有基础结构。 现在，更改、设置或重置本地密码的用户或管理员需要与纯云用户遵循相同的密码策略。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

每当用户更改或重置其密码时，都会通过针对全局和自定义禁止密码列表（如果配置了后者）对其进行验证来检查新密码的强度和复杂程度。

即使用户的密码包含禁止密码，如果整体密码足够强，那么仍可以接受该密码。 将通过以下步骤评估新配置密码的整体强度，从而确定应接受还是拒绝该密码。

### <a name="step-1-normalization"></a>步骤 1：规范化

新密码首先经过一个规范化处理过程。 此过程可以将一小组禁止的密码映射到更大范围的一组潜在弱密码。

规范化分为两个部分。  首先，将所有大写字母都改为小写字母。  其次，执行常见的字符替换，例如：  

| 原始字母  | 替换后的字母 |
| --- | --- |
| “0”  | “o” |
| '1'  | “l” |
| “$”  | “s” |
| “@”  | “a” |

示例：假定已禁止密码“blank”，并且用户尝试将密码更改为“Bl@nK”。 即使未专门禁止“Bl@nk”，规范化过程也会将此密码转换为“blank”，这是一个禁止的密码。

### <a name="step-2-check-if-password-is-considered-banned"></a>步骤 2：检查密码是否被视为禁止

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。  

示例：假定已禁止密码“abcdef”，并且用户尝试将密码更改为以下密码之一：

“abcdeg”（最后一个字符由“f”变为“g”）    **；“abcdefg”（末尾附加了“g”）   **；“abcde”（删除了末尾的尾随“f”）     **

上述每个密码都不与禁止的密码“abcdef”精确匹配。 但是，由于每个示例与禁止令牌“abcdef”的编辑距离都为 1，因此它们都被视为与“abcdef”匹配。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

在经过规范化处理的密码上使用子字符串匹配，以检查用户的名字和姓氏以及租户名称（请注意，在 Active Directory 域控制器上验证密码时，不会执行租户名称匹配）。

示例：假定用户 John Doe 要将其密码重置为“J0hn123fb”。 经过规范化处理后，此密码将变为“john123fb”。 子字符串匹配发现密码包含用户的名字“John”。 即使任何禁止的密码列表中都没有专门列出“J0hn123fb”，子字符串匹配也能在密码中找到“John”。 因此，该密码将被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 然后：

1. 为在用户密码中找到的每个禁止密码提供一个分数。
2. 为每个剩余的唯一字符提供一个分数。
3. 密码必须至少为 5 分才能被接受。

接下来的两个示例假定 Contoso 正在使用 Azure AD 密码保护，并且在其自定义列表中包含“contoso”。 我们还假定全局列表中包含“blank”。

示例：用户将其密码更改为“C0ntos0Blank12”

经过规范化处理后，此密码变为“contosoblank12”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [blank] = [1] + [2] = 4 分，由于此密码低于 5 分，因此将被拒绝。

示例：用户将其密码更改为“ContoS0Bl@nkf9!”。

经过规范化处理后，此密码变为“contosoblankf9!”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [blank] + [f] + [9] + [!] = 5 分，由于此密码至少为 5 分，因此被接受。

   > [!IMPORTANT]
   > 请注意，根据持续的安全性分析和研究，禁止密码算法以及全局列表可以并且会随时在 Azure 中进行更改。 对于本地 DC 代理服务，只有在重新安装 DC 代理软件后，更新的算法才能生效。

## <a name="license-requirements"></a>许可要求

|   | 带有全局禁止密码列表的 Azure AD 密码保护 | 带有自定义禁止密码列表的 Azure AD 密码保护|
| --- | --- | --- |
| 仅限云的用户 | Azure AD Free | Azure AD Basic |
| 从本地 Windows Server Active Directory 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为某些会被禁止的内容时，他会看到以下错误消息：

很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

* [配置自定义的禁止密码列表](howto-password-ban-bad.md)
* [启用本地 Azure AD 密码保护代理](howto-password-ban-bad-on-premises-deploy.md)
