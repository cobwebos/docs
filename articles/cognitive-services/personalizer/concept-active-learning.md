---
title: 活动和非活动事件-Personalizer
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 05/30/2019
ms.author: diberry
ms.openlocfilehash: aa6f53901f21dcb0726454d641a4a2a66007f9e0
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429048"
---
# <a name="active-and-inactive-events"></a>活动和非活动事件

当应用程序调用排名 API 时，会收到应用程序应在 rewardActionId 字段中显示的操作。  从那时起，Personalizer 将需要具有相同 eventId 的奖励。 奖励分数将用于为将来的排名调用定型模型。 如果没有收到对 eventId 的奖励调用，将应用默认的奖励。 在 Azure 门户中建立默认回报。

在某些情况下，应用程序可能需要调用排名孔，甚至知道是否会对用户使用或 displayedn 结果。 例如，在某些情况下可能会发生这种情况，例如，使用市场营销活动覆盖升级内容的页面呈现方式。 如果未使用排名调用的结果，并且用户看不到该结果，则不能对其进行任何奖励（零或其他）训练。
通常情况下，出现这种情况：

* 你可能正在预先呈现不一定可让用户看到的某个 UI。 
* 应用程序可能正在执行预测个性化，其中的“排名”调用是使用实时性不够高的上下文发出的，其输出不一定会由应用程序使用。 

在这些情况下，使用 Personalizer 的正确方法是调用请求事件处于_非活动状态_的级别。 Personalizer 不会对此事件提供奖励，也不会应用默认奖励。 Letr 在业务逻辑中，如果应用程序使用排名调用中的信息，只需_激活_事件。 在事件处于活动状态时，Personalizer 将需要对该事件的奖励，或在没有对奖励 API 作出显式调用的情况下应用默认奖励。

## <a name="get-inactive-events"></a>获取非活动事件

若要禁用事件培训，请调用排名 `learningEnabled = False`。

如果为 eventId 发送奖励，或为该 eventId 调用 `activate` API，则会隐式激活对非活动事件的学习。

## <a name="learning-settings"></a>学习设置

学习设置确定模型定型的特定*超参数*。 同一数据的两个模型在不同的学习设置上定型，最终会有所不同。

### <a name="import-and-export-learning-policies"></a>导入和导出学习策略

可以通过 Azure 门户导入和导出学习策略文件。 在门户中可以保存、测试、替换现有策略，并在源代码管理中将其存档为项目，以供将来参考和审核。

### <a name="learning-policy-settings"></a>学习策略设置

不应更改**学习策略**中的设置。 仅当你了解所做的更改会对个性化体验创建服务造成何种影响时，才能更改设置。 在不了解这种后果的情况下更改设置会造成负面影响，包括使个性化体验创建服务模型失效。

### <a name="comparing-effectiveness-of-learning-policies"></a>比较学习策略的有效性

可以通过执行[脱机评估](concepts-offline-evaluation.md)，来比较不同的学习策略针对个性化体验创建服务日志中以往数据的有效性。

[上传自己的学习策略](how-to-offline-evaluation.md)以便与当前学习策略进行比较。

### <a name="discovery-of-optimized-learning-policies"></a>发现优化的学习策略

执行[脱机评估](how-to-offline-evaluation.md)时，个性化体验创建服务可以创建更优化的学习策略。 更优化的学习策略会在脱机评估中生成更好的奖励评分，在个性化体验创建服务中联机使用时会产生更好的结果。

创建优化的学习策略后，可将其直接应用到个性化体验创建服务，使之立即取代当前策略，或者可将其保存供进一步的评估，将来可以决定是要放弃、保存还是应用它。
