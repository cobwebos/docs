---
title: 活动和非活动事件-Personalizer
titleSuffix: Azure Cognitive Services
description: 本文介绍如何在 Personalizer 服务中使用活动事件和非活动事件、学习设置和学习策略。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: 1641a1020193395d7d2ddb9c4893bd7bc89cdcd0
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2019
ms.locfileid: "73681861"
---
# <a name="active-and-inactive-events"></a>活动和非活动事件

当应用程序调用排名 API 时，会收到应用程序应在**rewardActionId**字段中显示的操作。  从那时起，Personalizer 需要一个具有相同 eventId 的奖励调用。 奖励分数将用于为将来的排名调用定型模型。 如果没有收到对 eventId 的奖励调用，则应用默认的奖励。 在 Azure 门户中设置默认回报。

在某些情况下，应用程序可能需要先调用排名，才能知道是使用还是向用户显示结果。 例如，在某些情况下可能会发生这种情况，例如，升级内容的页面呈现被市场营销活动覆盖。 如果未使用排名调用的结果，并且用户从未看到过，则不发送相应的奖励呼叫。

通常情况下，在以下情况下会发生这些情况：

* 预呈现 UI，用户可能会或可能无法查看。 
* 您的应用程序正在进行预测性的个性化，其中的排名调用使用很少的实时上下文进行，并且应用程序不能使用该输出。 

在这些情况下，请使用 Personalizer 调用 Rank，请求事件处于_非活动状态_。 Personalizer 不会对此活动带来回报，也不会应用默认奖励。 稍后在业务逻辑中，如果应用程序使用排名调用中的信息，只需_激活_事件。 一旦活动处于活动状态，Personalizer 就需要一个事件回报。 如果对奖励 API 没有显式调用，则 Personalizer 将应用默认奖励。

## <a name="inactive-events"></a>非活动事件

若要禁用事件培训，请使用 `learningEnabled = False`调用排名。 对于非活动事件，如果向 eventId 发送奖励或为该 eventId 调用 `activate` API，则会隐式激活学习。

## <a name="learning-settings"></a>学习设置

学习设置确定模型定型的*超参数*。 对于在不同学习设置上定型的相同数据，两个模型将最终不同。

### <a name="import-and-export-learning-policies"></a>导入和导出学习策略

你可以从 Azure 门户导入和导出学习策略文件。 使用此方法可以保存现有策略，对其进行测试，替换它们，并将它们作为项目存档在源代码控制中，以供将来参考和审核。

### <a name="understand-learning-policy-settings"></a>了解学习策略设置

学习策略中的设置不应更改。 仅在了解其对 Personalizer 的影响时才更改设置。 如果没有此信息，则可能会导致问题，包括使 Personalizer 模型失效。

### <a name="compare-learning-policies"></a>比较学习策略

您可以通过执行[脱机评估](concepts-offline-evaluation.md)来比较不同的学习策略如何对 Personalizer 日志中的过去的数据执行。

[上传你自己的学习策略](how-to-offline-evaluation.md)，以将其与当前学习策略进行比较。

### <a name="optimize-learning-policies"></a>优化学习策略

Personalizer 可以在[脱机评估版](how-to-offline-evaluation.md)中创建优化的学习策略。 在离线评估中获得更好回报的优化学习策略将在 Personalizer 中联机使用时产生更好的结果。

优化学习策略后，可以将其直接应用到 Personalizer，以便立即替换当前策略。 或者，你可以保存优化后的策略，以便进一步评估，并决定是要放弃、保存还是应用它。
