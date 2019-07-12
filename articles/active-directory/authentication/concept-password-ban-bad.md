---
title: 动态阻止的密码-Azure Active Directory
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: c043b2ed1a626e362d7edd1a83429aa14046f8ac
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2019
ms.locfileid: "67703065"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>消除你的组织中的错误密码

行业领导者告诉您不应在多个位置使用相同的密码，可使复杂，并不使其如"Password123"一样简单。 组织如何保证他们的用户是遵循最佳实践指导原则？ 如何在确保用户未使用弱密码或甚至变体对弱密码？

在具有更强的密码的第一步是向用户提供指导。 在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

具有正确的指导是重要的是，但即便使用了，我们知道，许多用户仍将最终会选择弱密码。 通过检测和阻止已知弱密码和变体，以及根据需要阻止特定于你的组织的其他弱条款，azure AD 密码保护来保护你的组织。

有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全局禁止密码列表

Azure AD Identity Protection 团队不断地分析 Azure AD 安全遥测寻找常用弱或已泄露的密码，或更具体地说，弱数据库中使用的术语通常作为基础的弱密码。 找不到此类弱条款，这些都添加到全局受禁的密码列表。 全局受禁的密码列表的内容不基于任何外部数据源。 全局受禁的密码列表完全基于正在进行的 Azure AD 安全遥测和分析结果。

每当更改或在 Azure AD 中的任何租户中的任何用户重置新密码时，当前版本的全局受禁的密码列表用作验证密码强度时输入的密钥。 此验证会导致较强密码的所有 Azure AD 客户。

> [!NOTE]
> 网络罪犯也在攻击中使用类似的策略。 因此 Microsoft 不公开发布此列表的内容。

## <a name="custom-banned-password-list"></a>自定义禁止密码列表

某些组织可能想要通过在 Microsoft 所调用的自定义受禁的密码列表中添加其自己的自定义全局阻止的密码列表顶部提高安全性更进一步。 Microsoft 建议，添加到此列表中的条款主要致力于研究组织特定的术语包括：

- 品牌名称
- 产品名称
- 位置 （例如，例如公司总部）
- 特定于公司的内部条款
- 这意味着特定公司的缩写形式。

一旦条款添加到自定义受禁的密码列表，它们将添加到全局受禁的密码列表这是验证密码时。

> [!NOTE]
> 自定义受禁的密码列表被限制为 1000年条款最多可以拥有。 它不适用于阻止的密码极大的列表。 若要完全利用自定义受禁的密码列表的优势，Microsoft 建议您首先阅读和了解密码评估算法 (请参阅[如何评估密码](concept-password-ban-bad.md#how-are-passwords-evaluated)) 之前添加到新的条款自定义阻止的列表。 了解如何算法可将使您的企业能够有效地检测和阻止的弱密码及其变体的较大数字。

例如： 请考虑名为"Contoso"，该费用基于位于伦敦，，可将名为"小组件"的产品的客户。 对于此类客户，它将是浪费，也不太安全，以尝试阻止这些术语的变体，如：

- "Contoso ！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso"
- "LondonHQ"
- ...etcetera

相反，它是更高效、 安全地阻止仅关键基本术语：

- "Contoso"
- "London"
- "Widget"

然后会自动阻止密码验证算法，弱变体，上述的组合。

自定义的禁止密码列表和启用本地 Active Directory 集成的能力是使用 Azure 门户管理的。

![修改自定义受禁的密码列表下的身份验证方法](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密码喷射攻击和第三方已泄露的密码列表

一个关键 Azure AD 密码保护好处是帮助你防范密码喷射的攻击。 大多数密码喷射攻击不尝试攻击任何给定的单个帐户超过几次，因为这种行为会大大提高检测，通过帐户锁定或其他方式的可能性。 大多数密码喷射攻击因此依赖于提交只有少量的对每个企业中的帐户的已知最弱密码。 该技术允许攻击者可以快速搜索同时避免潜在的检测阈值在易被破坏帐户。

Azure AD 密码保护旨在有效地阻止可能会使用在密码喷射攻击中，根据实际安全遥测数据由 Azure AD 所示的所有已知弱密码。  Microsoft 可察觉的枚举数以百万计的密码已泄露以前公开已知的安全漏洞中的第三方网站。 它是常见的第三方密码验证产品，基于对这些数以百万计的密码的暴力破解比较。 Microsoft 认为这种技术不是提高总体密码强度密码喷射攻击者使用的典型策略的最佳方法。

> [!NOTE]
> 全局受禁的密码列表不是的 Microsoft 基于自己执行的任何第三方数据源，包括已泄露的密码列表。

尽管 Microsoft 的全局禁止的列表很小的某些第三方大容量列表相比，其安全影响因为事实源自实际密码喷射攻击，再加上这一事实的现实环境安全遥测数据，Microsoft密码验证算法使用智能模糊匹配的技术。 最终结果是它将有效地检测和阻止数以百万计的被用在企业中最常见的弱密码。 选择要将特定于组织的字词添加到自定义受禁的密码列表的客户还受益于相同的算法。

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

保护纯云帐户很有帮助，但是许多组织维护着混合方案，包括本地 Windows Server Active Directory。 它是可以在安装 Azure AD 密码保护的 Windows Server Active Directory 代理在本地扩展到现有基础结构阻止的密码列表。 现在，更改、设置或重置本地密码的用户或管理员需要与纯云用户遵循相同的密码策略。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

每当用户更改或重置其密码时，都会通过针对全局和自定义禁止密码列表（如果配置了后者）对其进行验证来检查新密码的强度和复杂程度。

即使用户的密码包含禁止密码，如果整体密码足够强，那么仍可以接受该密码。 将通过以下步骤评估新配置密码的整体强度，从而确定应接受还是拒绝该密码。

### <a name="step-1-normalization"></a>步骤 1：规范化

新密码首先经过一个规范化处理过程。 此方法允许一小组已禁止的密码将映射到更广的潜在弱密码。

规范化分为两个部分。  首先，将所有大写字母都改为小写字母。  其次，执行常见的字符替换，例如：  

| 原始字母  | 替换后的字母 |
| --- | --- |
| “0”  | “o” |
| '1'  | “l” |
| “$”  | “s” |
| '\@'  | “a” |

示例：假定已禁止密码“blank”，并且用户尝试将密码更改为“Bl@nK”。 即使未专门禁止“Bl@nk”，规范化过程也会将此密码转换为“blank”，这是一个禁止的密码。

### <a name="step-2-check-if-password-is-considered-banned"></a>步骤 2：检查密码是否被视为禁止

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。  

示例：假定已禁止密码“abcdef”，并且用户尝试将密码更改为以下密码之一：

“abcdeg”（最后一个字符由“f”变为“g”）    ** ；“abcdefg”（末尾附加了“g”）   ** ；“abcde”（删除了末尾的尾随“f”）     **

上述每个密码都不与禁止的密码“abcdef”精确匹配。 但是，由于每个示例是 1 被阻止的字词 abcdef 的编辑距离内，它们均被视为作为"abcdef"的匹配项。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

在经过规范化处理的密码上使用子字符串匹配，以检查用户的名字和姓氏以及租户名称（请注意，在 Active Directory 域控制器上验证密码时，不会执行租户名称匹配）。

示例： 假设我们有一个用户，Pol，想要重置其密码为"P0l123fb"。 标准化后，此密码将变为"pol123fb"。 匹配的子字符串将查找该密码包含用户的名字"Pol"。 即使"P0l123fb"不是特定于任一受禁的密码列表，匹配的子字符串在密码中找到"Pol"。 因此，该密码将被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 然后：

1. 为在用户密码中找到的每个禁止密码提供一个分数。
2. 为每个剩余的唯一字符提供一个分数。
3. 密码必须至少五 （5），才能被接受的点。

接下来的两个示例假定 Contoso 正在使用 Azure AD 密码保护，并且在其自定义列表中包含“contoso”。 我们还假定全局列表中包含“blank”。

示例：用户将其密码更改为“C0ntos0Blank12”

经过规范化处理后，此密码变为“contosoblank12”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [空白] + [1] + [2] = 4 个点由于此密码是在五 （5） 点，将被拒绝。

示例：用户将其密码更改为“ContoS0Bl@nkf9!”。

经过规范化处理后，此密码变为“contosoblankf9!”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [空白] + [f] + [9] + [！] = 5 点由于此密码是至少五 （5） 点，接受它。

   > [!IMPORTANT]
   > 请注意，根据持续的安全性分析和研究，禁止密码算法以及全局列表可以并且会随时在 Azure 中进行更改。 对于本地 DC 代理服务，更新的算法才会生效后的 DC 代理软件重新安装。

## <a name="license-requirements"></a>许可要求

|   | 带有全局禁止密码列表的 Azure AD 密码保护 | 带有自定义禁止密码列表的 Azure AD 密码保护|
| --- | --- | --- |
| 仅限云的用户 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 从本地 Windows Server Active Directory 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 不会同步到 Azure Active Directory 的本地 Windows Server Active Directory 用户还利用基于现有的许可为同步的用户的 Azure AD 密码保护的优点。

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为某些会被禁止的内容时，他会看到以下错误消息：

很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

- [配置自定义的禁止密码列表](howto-password-ban-bad.md)
- [启用本地 Azure AD 密码保护代理](howto-password-ban-bad-on-premises-deploy.md)
