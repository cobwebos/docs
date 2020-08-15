---
title: Azure Active Directory 中的密码保护
description: 了解如何通过 Azure Active Directory 密码保护从环境动态地阻止弱密码
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: rogoya
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68419c33286457a770a9988f1f00cc0b5e1f91bc
ms.sourcegitcommit: 3bf69c5a5be48c2c7a979373895b4fae3f746757
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2020
ms.locfileid: "88235292"
---
# <a name="eliminate-bad-passwords-using-azure-active-directory-password-protection"></a>使用 Azure Active Directory 密码保护消除错误密码

许多安全指南建议你不要在多个位置使用同一密码，使其变得复杂，并避免使用简单的密码，例如 *Password123*。 你可以为用户提供有关 [如何选择密码的指导](https://www.microsoft.com/research/publication/password-guidance)，但通常仍然使用弱或不安全的密码。 Azure AD 密码保护检测并阻止已知弱密码及其变种，还可以阻止特定于你的组织的其他弱条款。

使用 Azure AD 密码保护时，默认的全局禁止密码列表会自动应用到 Azure AD 租户中的所有用户。 为了满足你自己的业务和安全需求，可以在自定义的禁止密码列表中定义条目。 当用户更改或重置其密码时，将检查这些禁止的密码列表以强制使用强密码。

你应该使用 [Azure 多重身份验证](concept-mfa-howitworks.md)等其他功能，而不只是依赖于强制实施 Azure AD 密码保护的强密码。 有关将多个安全层用于登录事件的详细信息，请参阅 [Pa $ $word 不重要](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Your-Pa-word-doesn-t-matter/ba-p/731984)。

> [!IMPORTANT]
> 此概念文章向管理员说明了 Azure AD 密码保护的工作方式。 如果你是已经注册自助密码重置的最终用户，并且需要返回到你的帐户，请转到 [https://aka.ms/sspr](https://aka.ms/sspr) 。
>
> 如果你的 IT 团队尚未启用重置自己密码的功能，请联系支持人员以获得更多帮助。

## <a name="global-banned-password-list"></a>全局禁止密码列表

Azure AD Identity Protection 团队不断分析 Azure AD 的安全遥测数据，以查找常用弱密码或泄露密码。 具体而言，分析将查找通常用作弱密码的基础的基本术语。 如果发现了薄弱术语，则会将其添加到 " *全局禁止密码" 列表*中。 全局禁止密码列表的内容不基于任何外部数据源，而是基于 Azure AD 安全遥测和分析的结果。

为 Azure AD 租户中的任何用户更改或重置密码时，将使用当前版本的全局禁止密码列表来验证密码的强度。 此验证检查为所有 Azure AD 客户带来了更强的密码。

"全局禁止密码" 列表将自动应用于 Azure AD 租户中的所有用户。 没有要启用或配置的内容，也不能禁用。 当用户通过 Azure AD 更改或重置其自己的密码时，此 "全局禁止密码" 列表将应用于用户。

> [!NOTE]
> 网络罪犯还在其攻击中使用类似的策略来识别常见弱密码和变体。 为了提高安全性，Microsoft 不会发布全局禁止密码列表的内容。

## <a name="custom-banned-password-list"></a>自定义禁止密码列表

某些组织希望提高安全性，并将其自己的自定义项添加到全局禁止密码列表的顶部。 若要添加自己的条目，可以使用 " *自定义禁止密码" 列表*。 添加到 "自定义禁止密码" 列表中的条款应集中于特定于组织的术语，例如以下示例：

- 品牌名称
- 产品名称
- 地点，例如公司总部
- 公司特定的内部字词
- 具有特定公司含义的缩写

将字词添加到 "自定义禁止密码" 列表中时，它们将与 "全局禁止密码" 列表中的词条结合在一起。 然后，将根据这些禁止密码列表的组合集验证密码更改或重置事件。

> [!NOTE]
> 自定义受禁密码列表限制为最多 1000 个字词。 它不用于阻止极大型的密码列表。
>
> 若要充分利用自定义禁止密码列表的优点，首先要了解在向自定义禁止列表添加字词之前 [如何计算密码](#how-are-passwords-evaluated) 。 此方法可让你有效地检测和阻止大量弱密码及其变种。

![修改 "身份验证方法" 下的 "自定义禁止密码" 列表](./media/tutorial-configure-custom-password-protection/enable-configure-custom-banned-passwords-cropped.png)

假设有一个名为 *Contoso*的客户。 公司基于伦敦，并生成一个名为 *小组件*的产品。 对于本示例客户而言，尝试阻止这些术语的特定变体（例如：

- "Contoso！ 1"
- "Contoso@London"
- "ContosoWidget"
- "!Contoso
- "LondonHQ"

相反，它比仅阻止关键基准术语更有效、更安全，例如以下示例：

- “Contoso”
- 伦敦
- 小组件

密码验证算法随后会自动阻止弱变体和组合。

若要开始使用自定义禁止密码列表，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：配置自定义禁止密码](tutorial-configure-custom-password-protection.md)

## <a name="password-spray-attacks-and-third-party-compromised-password-lists"></a>密码喷涂攻击和第三方泄露的密码列表

Azure AD 密码保护可帮助你防御密码喷涂攻击。 大多数密码喷涂攻击不会尝试多次攻击任何给定的帐户。 此行为会通过帐户锁定或其他方式增加检测的可能性。

相反，大多数密码喷涂攻击仅向企业中的每个帐户提交少量的已知最弱密码。 此方法可让攻击者快速搜索容易受到攻击的帐户，并避免潜在的检测阈值。

Azure AD 密码保护会有效地阻止可能在密码喷涂攻击中使用的所有已知弱密码。 此保护基于来自 Azure AD 的实际安全遥测数据，以生成全局禁止的密码列表。

有一些第三方网站列举了在以前的已知安全漏洞内受到破坏的数百万密码。 第三方密码验证产品通常基于对数百万密码进行的暴力式比较。 但是，根据密码喷涂攻击者使用的典型策略，这些技术不是改进整体密码强度的最佳方法。

> [!NOTE]
> 全局禁止密码列表不基于任何第三方数据源，包括已泄露的密码列表。

尽管全局禁止列表与一些第三方大容量列表相比很小，但其来源于实际密码喷涂攻击的实际安全遥测。 此方法提高了整体安全性和有效性，密码验证算法也使用了智能模糊匹配技术。 因此，Azure AD 密码保护会有效地检测和阻止在企业中使用数百万个最常见的弱密码。

## <a name="on-premises-hybrid-scenarios"></a>本地混合方案

许多组织都有混合标识模型，其中包括本地 Active Directory 域服务 (AD DS) 环境。 若要将 Azure AD 密码保护的安全性优点扩展到 AD DS 环境中，可以在本地服务器上安装组件。 这些代理需要本地 AD DS 环境中的密码更改事件，才能符合 Azure AD 中的相同密码策略。

有关详细信息，请参阅 [强制 Azure AD 密码保护 AD DS](concept-password-ban-bad-on-premises.md)。

## <a name="how-are-passwords-evaluated"></a>如何评估密码

当用户更改或重置其密码时，将通过对照全局和自定义禁止密码列表中的条款组合列表来验证新密码的强度和复杂性。

即使用户的密码包含禁止使用的密码，如果整个密码的安全性足够大，也可能会接受该密码。 新配置的密码通过以下步骤来评估其总体强度，以确定是否应接受或拒绝该密码：

### <a name="step-1-normalization"></a>步骤1：规范化

新密码首先经过一个规范化处理过程。 此方法允许将一小部分禁止的密码映射到一组更大的可能弱密码。

规范化包含以下两个部分：

* 所有大写字母都将更改为小写字母。
* 然后，执行常见字符替换，如以下示例中所示：

   | 原始字母 | 替换后的字母 |
   |-----------------|--------------------|
   | 0               | o                  |
   | 1               | l                  |
   | $               | 秒                  |
   | \@              | a                  |

请考虑以下示例：

* 禁用密码 "空白"。
* 用户尝试将其密码更改为 " Bl@nK "。
* 即使 " Bl@nk " 不是禁止的，规范化过程也会将此密码转换为 "空白"。
* 此密码将被拒绝。

### <a name="step-2-check-if-password-is-considered-banned"></a>步骤2：检查密码是否被视为禁止

然后，将检查密码以了解其他匹配行为，并生成分数。 最终评分确定是接受还是拒绝密码更改请求。

#### <a name="fuzzy-matching-behavior"></a>模糊匹配行为

模糊匹配用于经过规范化处理的密码，可以识别它是否包含在全局禁止列表或自定义禁止密码列表中找到的密码。 匹配过程根据编辑距离为一 (1) 来比较。

请考虑以下示例：

* 密码 "abcdef" 已禁用。
* 用户尝试将其密码更改为以下其中一项：

   * ' abcdeg '- *最后一个字符从 ' f ' 更改为 ' g '*
   * "abcdefg"- *"g" 追加到末尾*
   * "abcde...z"-已 *从末尾删除结尾 "f"*

* 上述每个密码与禁止密码 "abcdef" 不明确匹配。

    但是，由于每个示例都处于禁止字词 "abcdef" 的编辑距离1，因此它们都被视为与 "abcdef" 匹配。
* 这些密码将被拒绝。

#### <a name="substring-matching-on-specific-terms"></a>子字符串匹配（特定字词）

在规范化密码上使用子字符串匹配来检查用户的名字和姓氏以及租户名称。 为本地混合方案验证 AD DS 域控制器上的密码时，不会执行租户名称匹配。

> [!IMPORTANT]
> 仅对长度至少为四个字符的名称和其他字词强制执行子字符串匹配。

请考虑以下示例：

* 要将密码重置为 "p0LL23fb" 的名为 "投票" 的用户。
* 规范化后，此密码将变为 "poll23fb"。
* 子字符串匹配发现密码包含用户的名字 "轮询"。
* 尽管 "poll23fb" 不是专门针对禁止的密码列表，但在密码中找到的子字符串 "轮询"。
* 此密码将被拒绝。

#### <a name="score-calculation"></a>分数计算

下一步在用户经过规范化的新密码中识别禁止密码的所有实例。 根据以下条件分配点：

1. 在用户密码中找到的每个禁止密码都提供一个点。
1. 为每个剩余的唯一字符提供一个分数。
1. 密码必须至少为5个 (5) 点才能接受。

对于接下来的两个示例方案，Contoso 使用 Azure AD 密码保护，并在其自定义禁止密码列表中包含 "Contoso"。 同时，假设全局列表中有 "空白"。

在下面的示例方案中，用户将其密码更改为 "C0ntos0Blank12"：

* 规范化后，此密码将变为 "contosoblank12"。
* 匹配过程将发现此密码包含两个禁止的密码： "contoso" 和 "空白"。
* 然后，会向此密码提供以下评分：

    *[contoso] + [空白] + [1] + [2] = 4 点*

* 由于此密码低于5个 (5) 点，因此将被拒绝。

让我们看看略微不同的示例，演示密码中的额外复杂性如何构建所需的点数。 在下面的示例方案中，用户将其密码更改为 " ContoS0Bl@nkf9 ！"：

* 规范化后，此密码将变为 "contosoblankf9！"。
* 匹配过程将发现此密码包含两个禁止的密码： "contoso" 和 "空白"。
* 然后，会向此密码提供以下评分：

    *[contoso] + [空白] + [f] + [9] + [！] = 5 磅*

* 由于此密码至少有5个 (5) 点，因此将接受此密码。

> [!IMPORTANT]
> 在 Azure 中，禁止密码算法和全局禁止密码列表，可以根据持续的安全分析和研究随时进行更改。
>
> 对于混合方案中的本地 DC 代理服务，更新的算法仅在重新安装 DC 代理软件之后才会生效。

## <a name="what-do-users-see"></a>用户看到什么

当用户尝试将密码重置为禁止的内容时，将显示以下错误消息：

*"遗憾的是，您的密码中包含的词、短语或模式使您的密码猜出。请使用其他密码重试。 "*

## <a name="license-requirements"></a>许可要求

| 用户 | 用全局禁止密码列表 Azure AD 密码保护 | 通过自定义禁止密码列表 Azure AD 密码保护|
|-------------------------------------------|---------------------------|---------------------------|
| 仅限云的用户                          | Azure AD Free             | Azure AD Premium P1 或 P2 |
| 从本地 AD DS 同步的用户 | Azure AD Premium P1 或 P2 | Azure AD Premium P1 或 P2 |

> [!NOTE]
> 本地 AD DS 不同步到 Azure AD 的用户也可根据已同步用户的现有许可 Azure AD 密码保护。

可以在 [Azure Active Directory 定价站点](https://azure.microsoft.com/pricing/details/active-directory/)上找到包括成本在内的其他许可信息。

## <a name="next-steps"></a>后续步骤

若要开始使用自定义禁止密码列表，请完成以下教程：

> [!div class="nextstepaction"]
> [教程：配置自定义禁止密码](tutorial-configure-custom-password-protection.md)

然后，你还可以 [启用本地 Azure AD 密码保护](howto-password-ban-bad-on-premises-deploy.md)。
