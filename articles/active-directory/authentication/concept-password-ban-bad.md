---
title: 动态禁止的密码-Azure Active Directory
description: 使用 Azure AD 动态禁止密码在你的环境中禁止弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef127d120b32f5344bce0f68d79f48401087f0ce
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79263993"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>消除你的组织中的错误密码

业内领导人告诉您不要在多个位置使用同一密码，使其复杂化，并使其变得简单，如 "Password123"。 组织如何保证其用户遵循最佳做法指南？ 他们怎样才能确保用户不会使用弱密码，甚至不能使用弱密码。

具有更强密码的初始步骤是向用户提供指导。 在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

很重要的指导很重要，但在这种情况下，我们知道许多用户仍会选择弱密码。 Azure AD 密码保护通过检测和阻止已知弱密码及其变种，并有选择性地阻止特定于你的组织的附加弱术语，来保护你的组织。

有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全局禁止密码列表

Azure AD Identity Protection 团队不断分析 Azure AD 的安全遥测数据，以查找常用弱密码或泄露的密码，或更具体地说，这是通常用作弱密码的基础的弱基准术语。 如果找到此类薄弱术语，则会将其添加到 "全局禁止密码" 列表中。 全局禁止密码列表的内容不基于任何外部数据源。 全局禁止密码列表完全取决于当前 Azure AD 安全遥测和分析的结果。

每当更改或重置 Azure AD 中任何租户中的任何用户的新密码时，全局禁止密码列表的当前版本在验证密码强度时用作密钥输入。 此验证为所有 Azure AD 客户带来了更强的密码。

> [!NOTE]
> 网络罪犯还在其攻击中使用类似的策略。 因此，Microsoft 不会公开发布此列表的内容。

## <a name="custom-banned-password-list"></a>自定义禁止密码列表

某些组织可能希望通过在 Microsoft 调用 "自定义禁止密码" 列表中的 "全局禁止密码" 列表的顶部添加自己的自定义，进一步提高安全性。 Microsoft 建议添加到此列表中的条款主要用于特定于组织的术语，例如：

- 品牌名称
- 产品名称
- 位置（例如，公司总部）
- 公司特定的内部术语
- 具有特定公司含义的缩写。

将字词添加到 "自定义禁止密码" 列表后，在验证密码时，它们将与 "全局禁止密码" 列表中的词条结合。

> [!NOTE]
> 自定义禁止密码列表限制为最多包含1000个术语。 它不用于阻止极大型的密码列表。 为了充分利用自定义禁止密码列表的优点，Microsoft 建议你先查看并了解密码评估算法（请参阅[如何计算密码](concept-password-ban-bad.md#how-are-passwords-evaluated)），然后再将新术语添加到自定义禁止列表。 了解算法的工作原理可使企业有效地检测和阻止大量弱密码及其变种。

例如：假设有一个名为 "Contoso" 的客户，该客户基于伦敦，这会生成一个名为 "小组件" 的产品。 对于这类客户而言，尝试阻止这些术语的特定变体（例如：

- "Contoso！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso
- "LondonHQ"
- ...等

相反，它比仅阻止关键基准术语更有效、更安全：

- Contoso
- 伦敦
- 小组件

密码验证算法随后会自动阻止上述的弱变体和组合。

自定义的禁止密码列表和启用本地 Active Directory 集成的能力是使用 Azure 门户管理的。

![修改 "身份验证方法" 下的 "自定义禁止密码" 列表](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密码喷涂攻击和第三方泄露的密码列表

密码保护权益 Azure AD 一项关键，旨在帮助你防御密码喷涂攻击。 大多数密码喷涂攻击不会尝试多次攻击任何给定的帐户，因为这种行为会通过帐户锁定或其他方式大幅增加检测的可能性。 因此，大多数密码喷涂攻击依赖于只针对企业中的每个帐户提交少量的已知最弱密码。 此方法可让攻击者快速搜索容易受到攻击的帐户，同时避免出现可能的检测阈值。

Azure AD 密码保护旨在有效地阻止可能在密码喷涂攻击中使用的所有已知弱密码，具体取决于 Azure AD 所查看的实际安全遥测数据。  Microsoft 对第三方网站进行了了解，这些网站列举了在以前的公开已知安全漏洞内受到破坏的数百万密码。 第三方密码验证产品通常基于对数百万密码进行的暴力式比较。 Microsoft 认为此类技术不是根据密码喷涂攻击者使用的典型策略来提高密码强度的最佳方法。

> [!NOTE]
> Microsoft 全局禁止密码列表不基于任何第三方数据源，包括已泄露的密码列表。

尽管 Microsoft 全局禁止列表与一些第三方大容量列表相比很小，但其安全效果却是从实际的密码喷涂攻击的真实安全遥测中获得的，这也是 Microsoft密码验证算法使用智能模糊匹配技术。 最终的结果是，它将有效检测和阻止在企业中使用数百万个最常见的弱密码。 选择将组织特定的条款添加到自定义禁止密码列表中的客户也将受益于相同的算法。

可以在[Pa $ $word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)上查看有关基于密码的安全问题的其他信息。

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

保护纯云帐户很有帮助，但是许多组织维护着混合方案，包括本地 Windows Server Active Directory。 通过安装本地代理，还可以将 Azure AD 密码保护的安全性优势扩展到 Windows Server Active Directory 环境中。 现在，在 Active Directory 中更改或重置密码的用户和管理员需要遵从与仅限云的用户相同的密码策略。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

每当用户更改或重置其密码时，将通过对照全局和自定义禁止密码列表（如果已配置）中的条款组合列表来验证新密码的强度和复杂性。

即使用户的密码包含禁止密码，如果整体密码足够强，那么仍可以接受该密码。 将通过以下步骤评估新配置密码的整体强度，从而确定应接受还是拒绝该密码。

### <a name="step-1-normalization"></a>步骤1：规范化

新密码首先经过一个规范化处理过程。 此方法允许将一小部分禁止的密码映射到一组更大的可能弱密码。

规范化分为两个部分。  首先，将所有大写字母都改为小写字母。  其次，执行常见的字符替换，例如：  

| 原始字母  | 替换后的字母 |
| --- | --- |
| “0”  | “o” |
| '1'  | “l” |
| “$”  | “s” |
| '\@'  | “a” |

示例：假定已禁止密码“blank”，并且用户尝试将密码更改为“Bl@nK”。 即使未专门禁止“Bl@nk”，规范化过程也会将此密码转换为“blank”，这是一个禁止的密码。

### <a name="step-2-check-if-password-is-considered-banned"></a>步骤2：检查密码是否被视为禁止

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。  

示例：假定已禁止密码“abcdef”，并且用户尝试将密码更改为以下密码之一：

"abcdeg" *（最后一个字符从 "f" 更改为 "g"）* "abcdefg" *"（g 附加到末尾）* " abcde...z " *（末尾的" f "已从末尾删除）*

上述每个密码都不与禁止的密码“abcdef”精确匹配。 但是，由于每个示例都处于禁止字词 "abcdef" 的编辑距离1，因此它们被视为与 "abcdef" 匹配。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

在经过规范化处理的密码上使用子字符串匹配，以检查用户的名字和姓氏以及租户名称（请注意，在 Active Directory 域控制器上验证密码时，不会执行租户名称匹配）。

示例：假设我们有一个用户 Registry.pol，该用户要将其密码重置为 "P0l123fb"。 规范化后，此密码将变为 "pol123fb"。 子字符串匹配发现密码包含用户的名字 "Registry.pol"。 即使 "P0l123fb" 不是专门针对禁止的密码列表，也在密码中找到了匹配的 "Registry.pol" 的子字符串。 因此，该密码将被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 然后：

1. 为在用户密码中找到的每个禁止密码提供一个分数。
2. 为每个剩余的唯一字符提供一个分数。
3. 密码必须至少为五（5）点才能被接受。

接下来的两个示例假定 Contoso 正在使用 Azure AD 密码保护，并且在其自定义列表中包含“contoso”。 我们还假定全局列表中包含“blank”。

示例：用户将其密码更改为“C0ntos0Blank12”

经过规范化处理后，此密码变为“contosoblank12”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [空白] + [1] + [2] = 4 点，因为此密码低于五（5）点，将被拒绝。

示例：用户将其密码更改为“ContoS0Bl@nkf9!”。

经过规范化处理后，此密码变为“contosoblankf9!”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] + [空白] + [f] + [9] + [！] = 5 点，因为此密码至少为五（5）点，因此接受此密码。

   > [!IMPORTANT]
   > 请注意，根据持续的安全性分析和研究，禁止密码算法以及全局列表可以并且会随时在 Azure 中进行更改。 对于本地 DC 代理服务，更新的算法只有在重新安装 DC 代理软件之后才会生效。

## <a name="license-requirements"></a>许可要求

|   | 带有全局禁止密码列表的 Azure AD 密码保护 | 带有自定义禁止密码列表的 Azure AD 密码保护|
| --- | --- | --- |
| 仅限云的用户 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 从本地 Windows Server Active Directory 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 本地 Windows Server Active Directory 未同步到 Azure Active Directory 的用户也可根据已同步用户的现有许可 Azure AD 密码保护。

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为某些会被禁止的内容时，他会看到以下错误消息：

很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

- [配置自定义的禁止密码列表](howto-password-ban-bad.md)
- [启用本地 Azure AD 密码保护代理](howto-password-ban-bad-on-premises-deploy.md)
