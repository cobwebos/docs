---
title: 功能评估 - 个性化
titleSuffix: Azure Cognitive Services
description: 当您从 Azure 门户在个性化程序资源中运行评估时，个性化程序会提供有关上下文和操作的功能影响模型的信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 31243b5e9da55aafbc376fa416c1b00a4499c116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "70242418"
---
# <a name="feature-evaluation"></a>功能评估

当您从[Azure 门户](https://portal.azure.com)在个性化程序资源中运行评估时，个性化程序会提供有关上下文和操作的功能影响模型的信息。 

这对于以下用途：

* 想象一下您可以使用的其他功能，从模型中更重要的功能中获得灵感。
* 查看哪些功能并不重要，并可能删除它们或进一步分析可能影响使用的内容。
* 就新内容或产品值得带入目录向编辑或整理团队提供指导。
* 解决向个性化程序发送功能时发生的常见问题和错误。

更重要的要素在模型中具有更强的权重。 由于这些功能具有更强的重量，因此当个人仪获得更高的奖励时，它们往往会存在。

## <a name="getting-feature-importance-evaluation"></a>获取功能重要性评估

要查看要素重要性结果，必须运行评估。 评估基于评估期间观察到的特征名称创建人可读特征标签。

有关功能重要性的结果信息表示当前个性化在线模型。 评价分析了在评估期结束时保存的模型在评估期间完成的所有培训后，采用当前在线学习政策所保存的模型的特征重要性。 

功能重要性结果不代表在评估期间测试或创建的其他策略和模型。  评估不包括在评估期结束后发送给个人服务器的功能。

## <a name="how-to-interpret-the-feature-importance-evaluation"></a>如何解释特征重要性评价

个性化程序通过创建具有类似重要性的功能的"组"来评估功能。 可以说，一个组比其他人具有整体上更强的重要性，但在组中，要素的顺序按字母顺序排列。

有关每个功能的信息包括：

* 功能是来自上下文还是操作。
* 功能键和值。

例如，冰淇淋店订购应用程序可能会看到"Context.Weather：Hot"作为一个非常重要的功能。

个性化显示功能的相关性，如果同时考虑，这些功能会产生更高的回报。

例如，您可能会看到"上下文.天气：热*与*行动.菜单项目：冰霜"以及"上下文.天气：冷*与*行动.菜单项目：暖茶：

## <a name="actions-you-can-take-based-on-feature-evaluation"></a>您可以根据功能评估执行的操作

### <a name="imagine-additional-features-you-could-use"></a>想象一下您可以使用的其他功能

从模型中更重要的功能获得灵感。 例如，如果您在视频移动应用中看到"Context.MobileBattery：低"，您可能会认为该连接类型也可能使客户选择在另一个视频剪辑上看到一个视频剪辑，然后将有关连接类型和带宽的功能添加到你的应用中。

### <a name="see-what-features-are-not-important"></a>查看哪些功能并不重要

可能会删除不重要的功能或进一步分析可能影响使用的内容。 由于多种原因，功能可能排名较低。 一种可能是，真正的功能不会影响用户的行为。 但它也可能意味着该功能对用户并不明显。 

例如，视频站点可以看到"Action.Video分辨率=4k"是一个低重要性功能，与用户研究相矛盾。 原因可能是应用程序甚至没有提及或显示视频分辨率，因此用户不会基于它更改其行为。

### <a name="provide-guidance-to-editorial-or-curation-teams"></a>为编辑或整理团队提供指导

提供有关新内容或产品的指导，值得带入目录中。 个性化工具被设计为增强人类洞察力和团队的工具。 这样做的一种方法是向编辑组提供有关推动行为的产品、文章或内容的信息。 例如，视频应用程序场景可能显示有一个重要功能称为"Action.VideoEntities.Cat：true"，这促使编辑团队引入更多猫视频。

### <a name="troubleshoot-common-problems-and-mistakes"></a>排除常见问题和错误

可以通过更改应用程序代码来修复常见问题和错误，这样它就不会向个性化程序发送不当或格式不正确的功能。 

发送功能时的常见错误包括：

* 发送个人身份信息 （PII）。 特定于一个人的 PII（如姓名、电话号码、信用卡号、IP 地址）不应与个人代卡器一起使用。 如果应用程序需要跟踪用户，请使用非标识 UUID 或其他一些 UserID 号码。 在大多数情况下，这也是有问题的。
* 对于大量用户，每个用户的交互不太可能比所有用户的交互量更重要，因此发送用户 IT（即使非 PII）可能会为模型增加比值更多的噪声。
* 将日期和时间字段作为精确时间戳而不是壮举时间值发送。 具有上下文.时间戳.Day_星期一或"上下文.时间戳.小时"="13"等功能更有用。 每个要素最多有 7 或 24 个要素值。 但"Context.timeStamp"："1985-04-12T23：20：50.52Z"是如此精确，以至于无法从中吸取教训，因为它永远不会再次发生。

## <a name="next-steps"></a>后续步骤

通过个性化程序了解[可扩展性和性能](concepts-scalability-performance.md)。

