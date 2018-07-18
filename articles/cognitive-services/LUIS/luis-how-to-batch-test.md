---
title: 批处理测试 LUIS 应用 - Azure | Microsoft Docs
description: 使用语言理解 (LUIS) 批处理测试来查找意向和实体不正确的陈述。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/14/2018
ms.author: v-geberr
ms.openlocfilehash: 822fb1e2d5b13941527d242e8501b423bd6b81cb
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265507"
---
# <a name="batch-testing"></a>批处理测试
 批处理测试是对当前已训练的模型进行的全面测试，以衡量其 LUIS 性能。 

<a name="batch-testing"></a>
## <a name="import-a-dataset-file-for-batch-testing"></a>导入数据集文件以进行批处理测试

1. 在顶栏上选择“测试”，然后选择“批处理测试面板”。

    ![批处理测试链接](./media/luis-how-to-batch-test/batch-testing-link.png)

2. 选择“导入数据集”。 此时会显示“导入新数据集”对话框。 选择“选择文件”并找到 [JSON](luis-concept-batch-test.md#batch-file-format) 文件，该文件包含的待测试陈述数量不超过 1000。

    ![导入数据集文件](./media/luis-how-to-batch-test/batchtest-importset.png)

    浏览器顶部的红色通知栏中将报告导入错误。 导入出现错误时，不会创建任何数据集。 有关详细信息，请参阅[常见错误](luis-concept-batch-test.md#common-errors-importing-a-batch)。

3. 在“数据集名称”字段中，输入数据集文件的名称。 数据集文件包括一个“陈述数组”，其中包括已标记的意向和实体。 请查看[示例批处理文件](luis-concept-batch-test.md#batch-file-format)，了解相应语法。 

4. 选择“完成”。 数据集文件随即完成添加。

## <a name="run-rename-export-or-delete-dataset"></a>运行、重命名、导出或删除数据集
若要运行、重命名、导出或删除数据集，请点击数据集行末尾的三个点图标 (...)。

![数据集操作](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>对已训练的应用运行批处理测试

若要运行测试，请选择数据集名称。 测试完成后，此行将显示数据集的测试结果。

![批处理测试结果](./media/luis-how-to-batch-test/run-test.png)

此数据集可以下载，与之前上传用于批处理测试的文件相同。

|State|含义|
|--|--|
|![测试成功绿色圆圈图标](./media/luis-how-to-batch-test/batch-test-result-green.png)|所有陈述都成功完成。|
|![测试未通过红色 x 图标](./media/luis-how-to-batch-test/batch-test-result-red.png)|至少一个陈述意向与预测不匹配。|
|![测试准备就绪图标](./media/luis-how-to-batch-test/batch-test-result-blue.png)|测试已可运行。|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>
## <a name="view-batch-test-results"></a>查看批处理测试结果 
若要查看批处理测试结果，请选择“查看结果”。

![批处理测试结果](./media/luis-how-to-batch-test/run-test-results.png)

<!--
 Select the **See results** link that appears after you run the test. A scatter graph known as an error matrix displays. The data points represent the utterances in the dataset. 

Green points indicate correct prediction, and red ones indicate incorrect prediction.

The filtering panel on the right side of the screen displays a list of all intents and entities in the app, with a green point for intents/entities that were predicted correctly in all dataset utterances, and a red point for those items with errors. Also, for each intent/entity, you can see the number of correct predictions out of the total utterances.

-->


<a name="filter-chart-results-by-intent-or-entity">









</a>## 筛选图表结果

若要按特定意向或实体筛选图表，请在右侧筛选面板中选择意向或实体。 图中的数据点及其分布会根据不同的选择而相应更新。 
 
![可视化的批处理测试结果](./media/luis-how-to-batch-test/filter-by-entity.png) 

<!--
## Investigate false sections
Data points on the **[False Positive][false-positive]** and **[False Negative][false-negative]** sections indicate errors, which should be investigated. If all data points are on the **[True Positive][true-positive]** and **[True Negative][true-negative]** sections, then your application's performance is perfect on this dataset.


The graph indicates [F-measure][f-measure], [recall][recall], and [precision][precision].  
-->
## <a name="view-single-point-utterance-data"></a>查看单点陈述数据
在图表中，将鼠标悬停在某个数据点上可查看其预测结果的确定性分数。 选择数据点可在页面底部的陈述列表中检索出相应的陈述。 

![所选的陈述](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>
## <a name="view-section-data"></a>查看分区数据
在此四分区图表中，选择分区名称，例如在图表右上角的“误报”。 该分区中的所有陈述都会显示在该图表下方的列表中。 

![按照分区所选的陈述](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

在上图中，陈述 `switch on` 被标记为 TurnAllOn 意向，但接收的预测意向为 None。 这表示 TurnAllOn 意向需要更多示例陈述才能做出预期预测。 

该图表中红色的两个分区表示与预期预测不匹配的陈述。 它们表示需要更多 LUIS 训练的陈述。 

该图表中绿色的两个分区与预期预测相匹配。

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的性能。 

* [使用 LUIS 标记建议的陈述](Label-Suggested-Utterances.md) 
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md) 
* [通过本教程了解批处理测试](luis-tutorial-batch-testing.md)
* [了解批处理测试概念](luis-concept-batch-test.md)。

[true-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-positive
[true-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#true-negative
[false-positive]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-positive
[false-negative]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#false-negative
[f-measure]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#f-measure
[recall]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#recall
[precision]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-glossary#precision

