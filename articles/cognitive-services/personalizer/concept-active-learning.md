---
title: 学习政策 - 个性化
description: 学习设置确定模型训练的*超参数*。 在不同学习设置上训练的两个相同数据的模型最终将有所不同。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219342"
---
# <a name="learning-policy-and-settings"></a>学习策略和设置

学习设置确定模型训练的*超参数*。 在不同学习设置上训练的两个相同数据的模型最终将有所不同。

[学习策略和设置](how-to-settings.md#configure-rewards-for-the-feedback-loop)设置在 Azure 门户中的个性化程序资源上。

## <a name="import-and-export-learning-policies"></a>进出口学习政策

可以从 Azure 门户导入和导出学习策略文件。 使用此方法可以保存现有策略、测试它们、替换它们，并将其存档到源代码管理中，作为项目以供将来参考和审核。

[了解如何](how-to-manage-model.md#import-a-new-learning-policy)在 Azure 门户中为个性化工具资源导入和导出学习策略。

## <a name="understand-learning-policy-settings"></a>了解学习策略设置

学习策略中的设置不打算更改。 仅当您了解设置如何影响个性化程序时，才更改这些设置。 如果没有这些知识，您可能会导致问题，包括使个性化模型无效。

个性化使用[誓言](https://github.com/VowpalWabbit)来训练和评分事件。 请参阅关于如何使用元音搜索编辑学习设置的[元音](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)文档。 获得正确的命令行参数后，将命令保存到具有以下格式的文件（用所需命令替换参数属性值），然后上载文件以导入个人化器资源的 Azure 门户中的 **"模型和学习设置"** 窗格中的学习设置。

下面是`.json`学习策略的示例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比较学习策略

您可以通过执行[脱机评估](concepts-offline-evaluation.md)来比较不同的学习策略与个人化日志中过去数据的性能。

[上传您自己的学习策略](how-to-manage-model.md)，将其与当前的学习策略进行比较。

## <a name="optimize-learning-policies"></a>优化学习策略

个性化器可以在[离线评估](how-to-offline-evaluation.md)中创建优化的学习策略。 优化的学习策略在离线评估中具有更好的回报，在个性化中在线使用时，将产生更好的效果。

优化学习策略后，可以直接将其应用于个人化程序，以便立即替换当前策略。 或者，您可以保存优化的策略以进行进一步评估，然后决定是否放弃、保存或应用它。

## <a name="next-steps"></a>后续步骤

* 学习[活动和非活动事件](concept-active-inactive-events.md)。
