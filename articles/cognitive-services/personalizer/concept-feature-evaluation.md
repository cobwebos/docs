---
title: 功能评估-Personalizer
titleSuffix: Azure Cognitive Services
description: 在 Azure 门户中运行 Personalizer 资源的评估时, Personalizer 提供有关上下文和操作的哪些功能影响模型的信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: d03c5f66f760a2bea9f99501cec478831a347c5d
ms.sourcegitcommit: fecb6bae3f29633c222f0b2680475f8f7d7a8885
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/30/2019
ms.locfileid: "68668720"
---
# <a name="feature-evaluation"></a>功能评估

在[Azure 门户](https://portal.azure.com)中运行 Personalizer 资源的评估时, Personalizer 提供有关上下文和操作的哪些功能影响模型的信息。 

这可用于:

* 想象一下可以使用的其他功能, 从哪些功能在模型中更重要。
* 查看哪些功能并不重要, 并可能将其删除或进一步分析可能影响使用的内容。
* 向编辑或特选团队提供有关要引入到目录的新内容或产品的指导。
* 排查将功能发送到 Personalizer 时出现的常见问题和错误。

更重要的功能在模型中的权重更强。 由于这些功能的权重更强, 因此当 Personalizer 获得更高的回报时, 它们往往存在。

## <a name="getting-feature-importance-evaluation"></a>正在获取功能重要性评估

若要查看功能重要性结果, 必须运行评估。 有关功能重要性的结果信息表示当前的 Personalizer 联机模式。 评估会分析在评估期结束日期保存的模型的功能重要性。 

该评估基于在评估期间观察到的功能名称, 创建可读的特征标签。

功能重要性结果并不表示在评估过程中测试或创建的其他策略和模型。  评估期结束后, 评估将不包括发送到 Personalizer 的功能。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解释功能重要性评估

Personalizer 通过创建具有相似重要性的功能的 "组" 来计算功能。 可以说, 一个组的重要性高于其他组, 但在组中, 功能的排序按字母顺序排列。

有关每个功能的信息包括:

* 此功能是否来自上下文或操作。
* 功能键和值。

例如, 冰淇淋商店订购应用可能会看到 "Context.subname: 热", 这是一项非常重要的功能。

Personalizer 显示了功能的关联, 这些功能在共同共同产生了更高的回报。

例如, 你可能会看到 "Context.subname:*带有*操作的热: Menuitem: IceCream" 和 "Context.subname: WarmTea:

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>基于功能评估可以执行的操作

### <a name="imagine-additional-features-you-could-use"></a>假设你可以使用其他功能

从模型中的更重要功能获得灵感。 例如, 如果在视频移动应用中看到 "MobileBattery: Low", 则可能会认为连接类型可能还会使客户选择在另一个视频剪辑上显示一个视频剪辑, 然后将有关连接类型和带宽的功能添加到应用中。

### <a name="see-what-features-are-not-important"></a>查看哪些功能不重要

可能删除不重要的功能, 或进一步分析可能会影响使用的内容。 出于许多原因, 功能可能会排名低。 其中一项操作可能是真正, 而不会影响用户的行为。 但这也可能意味着该功能对用户并不明显。 

例如, 视频网站可能会看到 "VideoResolution = 4k" 是一项低重要性的功能, 相反用户研究。 原因在于, 应用程序甚至不会提及或显示视频分辨率, 因此用户不会基于它更改行为。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>向社论团队或特选团队提供指导

提供有关要引入到目录的新内容或产品的指导。 Personalizer 是一种用于扩大人见解和团队的工具。 实现此操作的一种方法是向编辑组提供有关驱动行为的产品、文章或内容的信息。 例如, 视频应用程序方案可能会显示一个名为 "VideoEntities: true" 的重要功能, 提示编辑团队引入更多的猫视频。

### <a name="troubleshoot-common-problems-and-mistakes"></a>排查常见问题和错误

可以通过更改应用程序代码来解决常见的问题和错误, 使其不会将不适当或格式不正确的功能发送给 Personalizer。 

发送功能时的常见错误包括:

* 发送个人身份信息 (PII)。 专用于一个个体 (如姓名、电话号码、信用卡号和 IP 地址) 的 PII 不应与 Personalizer 一起使用。 如果你的应用程序需要跟踪用户, 请使用非识别 UUID 或其他某个 UserID 号。 在大多数情况下, 这也是一个问题。
* 由于用户数量很大, 每个用户的交互不太可能与所有人口交互进行权衡, 因此, 如果发送用户 Id (即使非 PII), 则可能会添加比模型的值更多的干扰。
* 以精确时间戳而不是特征化时间值的形式发送日期-时间字段。 具有等功能 (如 Context.subname. Day = 星期一或 "Context.subname" = "13") 更有用。 每个的功能值最多为7或24。 但 "Context.subname": "1985 年-04-12T23:20: 50.52 Z" 很精确, 因为它永远不会再次出现。

## <a name="next-steps"></a>后续步骤

了解 Personalizer 的[可伸缩性和性能](concepts-scalability-performance.md)。

