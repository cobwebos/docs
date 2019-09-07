---
title: 仪表板 - 语言理解 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用分析仪表板（一个可视化报告工具）修复意向。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: diberry
ms.openlocfilehash: 22e187bba3782e485685354c203a6273d5bcc618
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735055"
---
# <a name="how-to-use-the-dashboard-to-improve-your-app"></a>如何使用仪表板来改善应用

使用示例言语时查找并修复已训练应用的意向问题。 仪表板显示整体性的应用信息，并突出显示应予以修复的意向。 

查看仪表板分析是一个迭代过程，请在更改和改进模型时重复此过程。

对于在意向中不包含任何示例言语的应用（称为仅限模式的应用），此页不会提供相关的分析。 

## <a name="what-issues-can-be-fixed-from-dashboard"></a>在仪表板中可以修复哪些问题？

在仪表板中可以解决以下三种问题：

|问题|图表颜色|说明|
|--|--|--|
|数据不平衡|-|当示例言语的数量存在显著的差异时，将出现此问题。 所有意向需要有大致相同的示例言语数量 - None 意向除外。 None 意向的数量应该只占应用中言语总数的 10%-15%。<br><br> 如果数据不平衡但意向准确度超过特定的阈值，则不会将这种不平衡报告为一种问题。<br><br>**请从此问题着手 - 它可能是其他问题的根本原因。**|
|不明确的预测|橙色|如果由于使用[负采样](luis-how-to-train.md#train-with-all-data)或者将更多示例言语添加到意向，而导致最前面的意向与下一个意向的评分足够接近，以致下一次训练时评分将会掉转，则就会出现此问题。 |
|错误的预测|红色|未针对标记的意向（示例言语所在的意向）预测示例言语时，会出现此问题。|

正确的预测以蓝色表示。

仪表板将显示这些问题，告知哪些意向受到影响，并建议采取哪些措施来改进应用。 

## <a name="before-app-is-trained"></a>训练应用之前 

训练应用之前，仪表板不包含任何修复建议。 训练应用即可看到这些建议。  

## <a name="check-your-publishing-status"></a>检查发布状态

“发布状态”卡片包含有关活动版本的上次发布情况的信息。 

请检查活动的版本是否为要修复的版本。 

![仪表板将显示应用的外部服务、发布区域，以及聚合的终结点访问次数。](./media/luis-how-to-use-dashboard/analytics-card-1-shows-app-summary-and-endpoint-hits.png)

其中还会显示任何外部服务、发布区域，以及聚合的终结点访问次数。 

## <a name="review-training-evaluation"></a>检查训练评估

“训练评估”卡片按区域显示应用总体准确度的聚合摘要。 评分指示意向的质量。 

![“训练评估”卡片包含有关应用总体准确度的信息的第一个区域。](./media/luis-how-to-use-dashboard/analytics-card-2-shows-app-overall-accuracy.png)

图表以不同的颜色指示正确预测的意向和问题区域。 根据建议改善应用时，此评分会提高。 

建议的修复按问题类型进行区分，对于你的应用而言最为重要。 若要按意向检查和修复问题，请使用页面底部的 **[有错误的意向](#intents-with-errors)** 卡片。 

每个问题区域包含需要修复的意向。 选择意向名称时，“意向”页将会打开，其中包含一个应用于言语的筛选器。 使用此筛选器可以专注于处理导致问题的言语。

### <a name="compare-changes-across-versions"></a>比较不同版本中的更改

对应用进行更改之前创建新版本。 在新版本中，对意向的示例言语做出建议的更改，然后重新训练。 在仪表板页的“训练评估”卡片上，使用“显示已训练版本中的更改”来比较更改。 

![比较不同版本中的更改](./media/luis-how-to-use-dashboard/compare-improvement-across-versions.png)

### <a name="fix-version-by-adding-or-editing-example-utterances-and-retraining"></a>通过添加或编辑示例言语并重新训练来修复版本

修复应用的主要方法是添加或编辑示例言语，然后重新训练。 新的或更改的言语需要遵循[不同言语](luis-concept-utterance.md)的准则。

添加示例言语的操作应由具备以下经验的人员来执行：

* 高度了解了不同意向中的最谈话。
* 知道最谈话在一个目的中的方式可能与其他意图混淆。
* 可以决定是否应将两个经常相互混淆的方法折叠为一个意向。 如果是这种情况，则必须与实体一起提取不同的数据。

### <a name="patterns-and-phrase-lists"></a>模式和短语列表

分析页面并不指示何时使用[模式](luis-concept-patterns.md)或[短语列表](luis-concept-feature.md)。 如果添加了这些数据，则可以帮助进行不正确或不明确的预测，但不会帮助数据不平衡。 

### <a name="review-data-imbalance"></a>检查数据不平衡问题

请从此问题着手 - 它可能是其他问题的根本原因。

**数据不平衡**意向列表显示需要添加更多言语才能纠正数据不平衡问题的意向。 

**若要解决此问题**：

* 请将更多言语添加到意向，然后重新训练。 

除非仪表板上有相关建议，否则不要将言语添加到 None 意向。

> [!Tip]
> 使用该页上的第三个部分 - 包含“言语(数目)”设置的“每个意向的言语”可以快速直观地了解哪些意向需要更多的言语。  
    ![使用“言语(数目)”查找存在数据不平衡的意向。](./media/luis-how-to-use-dashboard/predictions-per-intent-number-of-utterances.png)

### <a name="review-incorrect-predictions"></a>检查错误的预测

**错误的预测**意向列表显示包含言语的意向，这些言语用作特定意向的示例，但已针对不同的意向做了预测。 

**若要解决此问题**：

* 编辑言语，使之与该意向更为相关，然后重新训练。
* 如果言语过于接近，请合并意向，然后重新训练。

### <a name="review-unclear-predictions"></a>检查不明确的预测

**不明确的预测**意向列表显示包含言语的意向，这些言语的预测评分与它们最靠近的其他言语没有足够大的差异，以致于在使用[负采样](luis-how-to-train.md#train-with-all-data)的情况下，下一次训练时这些言语的第一个意向可能会发生变化。

**若要修复此问题**：

* 编辑言语，使之与该意向更为相关，然后重新训练。
* 如果言语过于接近，请合并意向，然后重新训练。

## <a name="utterances-per-intent"></a>每个意向的言语

此卡片显示不同意向的总体应用运行状况。 在修复意向并重新训练的过程中，请不断地概览一下此卡片中的问题。

以下图表显示了一个适当平衡的应用，其中几乎不存在任何要修复的问题。

![以下图表显示了一个适当平衡的应用，其中几乎不存在任何要修复的问题。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-balance.png)

以下图表显示了一个很不平衡的应用，其中包含许多要修复的问题。

![以下图表显示了一个适当平衡的应用，其中几乎不存在任何要修复的问题。](./media/luis-how-to-use-dashboard/utterance-per-intent-shows-data-imbalance.png)

将鼠标悬停在每个意向的条块上可以获取有关该意向的信息。 

![以下图表显示了一个适当平衡的应用，其中几乎不存在任何要修复的问题。](./media/luis-how-to-use-dashboard/utterances-per-intent-with-details-of-errors.png)

使用“排序依据”功能可按问题类型排列意向，以便可以专注于处理存在该问题的最严重意向。 

## <a name="intents-with-errors"></a>有错误的意向

使用此卡片可以检查特定意向的问题。 此卡片的默认视图是问题最严重的意图，让你知道要将工作重心放在何处。

![使用“有错误的意向”卡片可以检查特定意向的问题。 该卡片默认已根据问题最严重的意向进行筛选，让你知道要将工作重心放在何处。](./media/luis-how-to-use-dashboard/most-problematic-intents-with-errors.png)

顶部的圆环图表以三种问题类型显示意向的问题。 如果出现了这三种类型的问题，每种问题类型会在下面绘制其自身的图表，以及另外两种问题类型的图表。 

### <a name="filter-intents-by-issue-and-percentage"></a>按问题和百分比筛选意向

在该卡片的此部分，可以查找超出错误阈值的示例言语。 理想情况下，正确的预测应该占有很高的比例。 该百分比由业务和客户驱动。 

请确定最符合业务需求的阈值百分比。 

使用筛选器可以查找存在特定问题的意向：

|筛选|建议的百分比|用途|
|--|--|--|
|问题最严重的意向|-|**从此处着手** - 修复此意向中的言语比其他修复方法更能改善应用。|
|正确的预测低于|60%|这是选定意向中正确的、但置信度评分低于阈值的言语的百分比。 |
|不明确的预测高于|15%|这是选定意向中与最靠近的其他意向相混淆的言语的百分比。|
|错误的预测高于|15%|这是选定意向中未正确预测的言语的百分比。 |

### <a name="correct-prediction-threshold"></a>正确的预测阈值

你的有把握预测的置信度评分是什么？ 在应用开发的初期，目标可能是 60%。 使用“正确的预测低于”和百分比 60% 查找选定意向中需要修复的所有言语。

### <a name="unclear-or-incorrect-prediction-threshold"></a>不明确或错误的预测阈值

使用这两个筛选器可以查找选定意向中超出阈值的言语。 可将这两个百分比视为错误百分比。 如果 10-15% 的预测错误率对你而较为合适，将筛选器阈值设置为 15% 即可找到高于此值的所有言语。 

## <a name="next-steps"></a>后续步骤

* [管理 Azure 资源](luis-how-to-azure-subscription.md)
