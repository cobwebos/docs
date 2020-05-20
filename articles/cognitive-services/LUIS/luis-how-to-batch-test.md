---
title: 如何执行批处理测试 - LUIS
titleSuffix: Azure Cognitive Services
description: 使用语言理解 (LUIS) 批处理测试集来查找意向和实体不正确的话语。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/17/2020
ms.author: diberry
ms.openlocfilehash: 4d3535d419b0f99491795b3920fdd295e3128299
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654022"
---
# <a name="batch-testing-with-a-set-of-example-utterances"></a>使用一组示例话语进行批处理测试

 批处理测试是对当前已训练的模型进行的全面测试，以衡量其 LUIS 性能。 用于批处理测试的数据集不应包括意向中的示例话语或从预测运行时终结点接收的话语。

<a name="batch-testing"></a>

## <a name="import-a-dataset-file-for-batch-testing"></a>导入数据集文件以进行批处理测试

1. 在顶栏上选择“测试”，然后选择“批处理测试面板”   。

    ![批处理测试链接](./media/luis-how-to-batch-test/batch-testing-link.png)

2. 选择“导入数据集”  。 此时会显示“导入新数据集”对话框  。 选择“选择文件”并找到具有正确 [JSON 格式](luis-concept-batch-test.md#batch-file-format)的 JSON 文件，该文件包含的待测试话语数量不超过 1,000   。

    浏览器顶部的红色通知栏中将报告导入错误。 导入出现错误时，不会创建任何数据集。 有关详细信息，请参阅[常见错误](luis-concept-batch-test.md#common-errors-importing-a-batch)。

3. 在“数据集名称”字段中，输入数据集文件的名称  。 数据集文件包括一个“陈述数组”，其中包括已标记的意向和实体    。 请查看[示例批处理文件](luis-concept-batch-test.md#batch-file-format)，了解相应语法。

4. 选择“完成”  。 数据集文件随即完成添加。

## <a name="run-rename-export-or-delete-dataset"></a>运行、重命名、导出或删除数据集

若要运行、重命名、导出或删除数据集，请点击数据集行末尾的省略号 (...) 按钮。

> [!div class="mx-imgBorder"]
> ![带有选项的批处理测试列表的屏幕截图](./media/luis-how-to-batch-test/batch-testing-options.png)

## <a name="run-a-batch-test-on-your-trained-app"></a>对已训练的应用运行批处理测试

若要运行测试，请选择数据集名称，然后在上下文工具栏中选择 "**运行**"。 测试完成后，此行将显示数据集的测试结果。

此数据集可以下载，与之前上传用于批处理测试的文件相同。

|州省/自治区/直辖市|含义|
|--|--|
|![测试成功绿色圆圈图标](./media/luis-how-to-batch-test/batch-test-result-green.png)|所有陈述都成功完成。|
|![测试未通过红色 x 图标](./media/luis-how-to-batch-test/batch-test-result-red.png)|至少一个陈述意向与预测不匹配。|
|![测试准备就绪图标](./media/luis-how-to-batch-test/batch-test-result-blue.png)|测试已可运行。|

<a name="access-batch-test-result-details-in-a-visualized-view"></a>

## <a name="view-batch-test-results"></a>查看批处理测试结果

若要查看批处理测试结果，请选择“查看结果”****。

<a name="filter-chart-results-by-intent-or-entity"></a>

## <a name="filter-chart-results"></a>筛选图表结果

若要按特定意向或实体筛选图表，请在右侧筛选面板中选择意向或实体。 图中的数据点及其分布会根据不同的选择而相应更新。

![可视化的批处理测试结果](./media/luis-how-to-batch-test/filter-by-entity.png)

## <a name="view-single-point-utterance-data"></a>查看单点陈述数据

在图表中，将鼠标悬停在某个数据点上可查看其预测结果的确定性分数。 选择数据点可在页面底部的陈述列表中检索出相应的陈述。

![所选的陈述](./media/luis-how-to-batch-test/selected-utterance.png)


<a name="relabel-utterances-and-retrain"></a>
<a name="false-test-results"></a>

## <a name="view-section-data"></a>查看分区数据

在此四分区图表中，选择分区名称，例如在图表右上角的“误报”****。 该分区中的所有陈述都会显示在该图表下方的列表中。

![按照分区所选的陈述](./media/luis-how-to-batch-test/selected-utterances-by-section.png)

在上图中，陈述 `switch on` 被标记为 TurnAllOn 意向，但接收的预测意向为 None。 这表示 TurnAllOn 意向需要更多示例陈述才能做出预期预测。

该图表中红色的两个分区表示与预期预测不匹配的陈述。 它们表示需要更多 LUIS 训练的陈述。

该图表中绿色的两个分区与预期预测相匹配。

[!INCLUDE [Entity roles in batch testing - currently not supported](../../../includes/cognitive-services-luis-roles-not-supported-in-batch-testing.md)]

## <a name="next-steps"></a>后续步骤

如果测试表明 LUIS 应用未正确识别意向和实体，则可以通过标记更多陈述或添加功能来提高 LUIS 应用的性能。

* [使用 LUIS 标记建议的陈述](luis-how-to-review-endpoint-utterances.md)
* [使用相关功能来改进 LUIS 应用的性能](luis-how-to-add-features.md)
* [通过本教程了解批处理测试](luis-tutorial-batch-testing.md)
* [了解批处理测试概念](luis-concept-batch-test.md)。
