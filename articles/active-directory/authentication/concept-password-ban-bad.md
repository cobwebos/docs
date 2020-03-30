---
title: 动态禁用的密码 - Azure 活动目录
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263993"
---
# <a name="eliminate-bad-passwords-in-your-organization"></a>消除你的组织中的错误密码

行业领导者告诉您不要在多个位置使用相同的密码，使其变得复杂，并且不要像"密码123"那样简单。 组织如何保证其用户遵循最佳实践指南？ 他们如何确保用户不使用弱密码，甚至不使用弱密码的变体？

拥有更强的密码的第一步是为用户提供指导。 在以下链接中可以找到 Microsoft 当前为此主题提供的指导：

[Microsoft 密码指导](https://www.microsoft.com/research/publication/password-guidance)

拥有良好的指导很重要，但即使这样，我们知道许多用户最终还是会选择弱密码。 Azure AD 密码保护通过检测和阻止已知的弱密码及其变体，以及选择性地阻止特定于组织的其他弱术语来保护组织。

有关我们目前在安全方面所做努力的详细信息，请参阅 [Microsoft 安全智能报告](https://www.microsoft.com/security/operations/security-intelligence-report)。

## <a name="global-banned-password-list"></a>全局禁止密码列表

Azure AD 标识保护团队不断分析 Azure AD 安全遥测数据，查找常用的弱密码或泄露的密码，或者更具体地说，通常用作弱密码基础的弱基本术语。 当发现此类弱项时，它们将添加到全局禁止密码列表中。 全局禁止密码列表的内容不基于任何外部数据源。 全局禁用密码列表完全基于 Azure AD 安全遥测和分析的持续结果。

每当 Azure AD 中任何租户中的任何用户更改或重置新密码时，在验证密码强度时，全局禁止密码列表的当前版本将用作密钥输入。 此验证为所有 Azure AD 客户生成更强的密码。

> [!NOTE]
> 网络犯罪分子在攻击中也使用类似的策略。 因此，Microsoft 不会公开发布此列表的内容。

## <a name="custom-banned-password-list"></a>自定义禁止密码列表

一些组织可能希望通过在 Microsoft 所称的自定义禁止密码列表中添加自己的自定义项来进一步提高安全性。 Microsoft 建议添加到此列表的术语主要侧重于特定于组织的术语，例如：

- 品牌
- 产品名称
- 地点（例如，如公司总部）
- 公司特定的内部条款
- 具有特定公司含义的缩写。

将术语添加到自定义禁止密码列表中后，在验证密码时，这些术语将与全局禁止密码列表中的术语结合使用。

> [!NOTE]
> 自定义禁止密码列表限制最多包含 1000 个术语。 它不是用于阻止非常大的密码列表。 为了充分利用自定义禁止密码列表的好处，Microsoft 建议您在向自定义禁止列表添加新术语之前，首先查看并了解密码评估算法（请参阅[如何计算密码](concept-password-ban-bad.md#how-are-passwords-evaluated)）。 了解该算法的工作原理将使您的企业能够有效地检测和阻止大量弱密码及其变体。

例如：考虑一个名为"Contoso"的客户，该客户位于伦敦，生产名为"Widget"的产品。 对于这样的客户来说，试图阻止这些术语的特定变体（例如：

- "康托索！1"
- "Contoso@London"
- "ContosoWidget"
- "!孔托索"
- "伦敦总部"
- ...诸如此类

相反，仅阻止关键基本术语会更加高效和安全：

- “Contoso”
- "伦敦"
- "小工具"

然后，密码验证算法将自动阻止上述弱变体和组合。

自定义的禁止密码列表和启用本地 Active Directory 集成的能力是使用 Azure 门户管理的。

![修改身份验证方法下的自定义禁止密码列表](./media/concept-password-ban-bad/authentication-methods-password-protection.png)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密码喷雾攻击和第三方泄露的密码列表

Azure AD 密码保护的一个关键好处是可帮助您防御密码喷射攻击。 大多数密码喷雾攻击不会尝试攻击任何特定的个人帐户多次，因为这种行为大大增加了通过帐户锁定或其他方式检测的可能性。 因此，大多数密码喷射攻击都依赖于仅针对企业中的每个帐户提交少量已知最弱的密码。 此技术允许攻击者快速搜索容易泄露的帐户，同时避免潜在的检测阈值。

Azure AD 密码保护旨在根据 Azure AD 所示的实际安全遥测数据，有效地阻止所有可能用于密码喷射攻击的已知弱密码。  Microsoft 知道第三方网站枚举了以前公开已知的安全漏洞中泄露的数百万个密码。 第三方密码验证产品通常基于与数百万个密码的强力比较。 Microsoft 认为，鉴于密码喷雾攻击者使用的典型策略，此类技术不是提高整体密码强度的最佳方式。

> [!NOTE]
> Microsoft 全球禁止密码列表不基于任何第三方数据源，包括泄露的密码列表。

尽管与某些第三方批量列表相比，Microsoft 全球禁止列表很小，但其安全影响因来自实际密码喷射攻击的实际安全遥测数据以及 Microsoft 的事实而放大了密码验证算法使用智能模糊匹配技术。 最终结果是，它将有效地检测和阻止企业中数百万个最常见的弱密码。 选择将特定于组织的术语添加到自定义禁止密码列表中的客户也受益于同一算法。

有关基于密码的安全问题的其他信息可能会在[您的 Pa$$word](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)上查看。这并不重要。

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

保护纯云帐户很有帮助，但是许多组织维护着混合方案，包括本地 Windows Server Active Directory。 Azure AD 密码保护的安全优势也可能通过安装本地代理扩展到 Windows 服务器活动目录环境。 现在，在 Active Directory 中更改或重置密码的用户和管理员必须遵守与仅云用户相同的密码策略。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

每当用户更改或重置其密码时，都会根据全局和自定义禁止密码列表中的术语列表（如果配置了后者）中的术语列表对其进行验证，以检查新密码的强度和复杂性。

即使用户的密码包含禁止密码，如果整体密码足够强，那么仍可以接受该密码。 将通过以下步骤评估新配置密码的整体强度，从而确定应接受还是拒绝该密码。

### <a name="step-1-normalization"></a>第 1 步：规范化

新密码首先经过一个规范化处理过程。 此技术允许将一小组被禁止的密码映射到一组可能较弱的密码。

规范化分为两个部分。  首先，将所有大写字母都改为小写字母。  其次，执行常见的字符替换，例如：  

| 原始字母  | 替换后的字母 |
| --- | --- |
| “0”  | “o” |
| '1'  | “l” |
| '$'  | “s” |
| '\@'  | “a” |

示例：假定已禁止密码“blank”，并且用户尝试将密码更改为“Bl@nK”。 即使未专门禁止“Bl@nk”，规范化过程也会将此密码转换为“blank”，这是一个禁止的密码。

### <a name="step-2-check-if-password-is-considered-banned"></a>第 2 步：检查密码是否被视为禁止

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。  

示例：假定已禁止密码“abcdef”，并且用户尝试将密码更改为以下密码之一：

"abcdeg"（*最后一个字符从"f"更改为"g"）"abcdefg""（g"**追加到结束*）"abcde"（*尾随'f'从末尾删除）*

上述每个密码都不与禁止的密码“abcdef”精确匹配。 但是，由于每个示例在禁用术语"abcdef"的编辑距离 1 内，因此它们都被视为与"abcdef"的匹配。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

在经过规范化处理的密码上使用子字符串匹配，以检查用户的名字和姓氏以及租户名称（请注意，在 Active Directory 域控制器上验证密码时，不会执行租户名称匹配）。

示例：假设我们有一个用户 Pol，他想要将其密码重置为"P0l123fb"。 规范化后，此密码将成为"pol123fb"。 子字符串匹配发现密码包含用户的名字"Pol"。 尽管"P0l123fb"没有具体出现在禁止的密码列表中，但密码中找到了"Pol"的子字符串匹配。 因此，该密码将被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 然后：

1. 为在用户密码中找到的每个禁止密码提供一个分数。
2. 为每个剩余的唯一字符提供一个分数。
3. 密码必须至少为五 （5） 个点才能接受。

接下来的两个示例假定 Contoso 正在使用 Azure AD 密码保护，并且在其自定义列表中包含“contoso”。 我们还假定全局列表中包含“blank”。

示例：用户将其密码更改为“C0ntos0Blank12”

经过规范化处理后，此密码变为“contosoblank12”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] = [空白] [ 1] [ 2] = 4 点 由于此密码低于 5 （5） 点，它将被拒绝。

示例：用户将其密码更改为“ContoS0Bl@nkf9!”。

经过规范化处理后，此密码变为“contosoblankf9!”。 匹配过程发现此密码包含两个禁止密码：contoso 和 blank。 然后为此密码提供一个分数：

[contoso] = [空白] [ f] [ 9] [ ！] = 5 点 由于此密码至少为五 （5） 点，因此接受。

   > [!IMPORTANT]
   > 请注意，根据持续的安全性分析和研究，禁止密码算法以及全局列表可以并且会随时在 Azure 中进行更改。 对于本地 DC 代理服务，更新后的算法仅在重新安装 DC 代理软件后生效。

## <a name="license-requirements"></a>许可要求

|   | 带有全局禁止密码列表的 Azure AD 密码保护 | 带有自定义禁止密码列表的 Azure AD 密码保护|
| --- | --- | --- |
| 仅限云的用户 | Azure AD Free | Azure AD Premium P1 或 P2 |
| 从本地 Windows Server Active Directory 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 未同步到 Azure 活动目录的本地 Windows 服务器活动目录用户还受益于基于同步用户的现有许可的 Azure AD 密码保护。

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为某些会被禁止的内容时，他会看到以下错误消息：

很遗憾，你的密码包含单词、短语或模式，这些使你的密码容易被猜出。 请使用其他密码重试。

## <a name="next-steps"></a>后续步骤

- [配置自定义的禁止密码列表](howto-password-ban-bad.md)
- [启用本地 Azure AD 密码保护代理](howto-password-ban-bad-on-premises-deploy.md)
