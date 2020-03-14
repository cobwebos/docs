---
title: 学习策略-Personalizer
description: 学习设置确定模型定型的*超参数*。 对于在不同学习设置上定型的相同数据，两个模型将最终不同。
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: abe6a2a2ec9b9978230d894c69193469f6e932e6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219342"
---
# <a name="learning-policy-and-settings"></a>了解策略和设置

学习设置确定模型定型的*超参数*。 对于在不同学习设置上定型的相同数据，两个模型将最终不同。

在 Azure 门户中的 Personalizer 资源上设置[学习策略和设置](how-to-settings.md#configure-rewards-for-the-feedback-loop)。

## <a name="import-and-export-learning-policies"></a>导入和导出学习策略

你可以从 Azure 门户导入和导出学习策略文件。 使用此方法可以保存现有策略，对其进行测试，替换它们，并将它们作为项目存档在源代码控制中，以供将来参考和审核。

了解[如何](how-to-manage-model.md#import-a-new-learning-policy)在 Personalizer 资源的 Azure 门户中导入和导出学习策略。

## <a name="understand-learning-policy-settings"></a>了解学习策略设置

学习策略中的设置不应更改。 仅在了解其对 Personalizer 的影响时才更改设置。 如果没有此信息，则可能会导致问题，包括使 Personalizer 模型失效。

Personalizer 使用[vowpalwabbit](https://github.com/VowpalWabbit)来定型和评分事件。 请参阅[vowpalwabbit 文档](https://github.com/VowpalWabbit/vowpal_wabbit/wiki/Command-line-arguments)，了解如何使用 vowpalwabbit 编辑学习设置。 使用正确的命令行参数后，请使用以下格式将命令保存到文件（使用所需的命令替换 arguments 属性值），并上传该文件，以便在 Personalizer 资源的 Azure 门户中的 "**模型和学习设置**" 窗格中导入学习设置。

以下 `.json` 是学习策略的一个示例。

```json
{
  "name": "new learning settings",
  "arguments": " --cb_explore_adf --epsilon 0.2 --power_t 0 -l 0.001 --cb_type mtr -q ::"
}
```

## <a name="compare-learning-policies"></a>比较学习策略

您可以通过执行[脱机评估](concepts-offline-evaluation.md)来比较不同的学习策略如何对 Personalizer 日志中的过去的数据执行。

[上传你自己的学习策略](how-to-manage-model.md)，以将其与当前学习策略进行比较。

## <a name="optimize-learning-policies"></a>优化学习策略

Personalizer 可以在[脱机评估版](how-to-offline-evaluation.md)中创建优化的学习策略。 在离线评估中获得更好回报的优化学习策略将在 Personalizer 中联机使用时产生更好的结果。

优化学习策略后，可以将其直接应用到 Personalizer，以便立即替换当前策略。 或者，你可以保存优化后的策略，以便进一步评估，并决定是要放弃、保存还是应用它。

## <a name="next-steps"></a>后续步骤

* 了解[活动和非活动事件](concept-active-inactive-events.md)。
