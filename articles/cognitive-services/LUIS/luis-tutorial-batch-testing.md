---
title: 教程：用于查找问题的批处理测试 - LUIS
description: 本教程演示如何使用批处理测试来验证语言理解 (LUIS) 应用的质量。
ms.topic: tutorial
ms.date: 05/07/2020
ms.openlocfilehash: c823cbd854c28ab356dd9968e6f8f1a12b6421be
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592587"
---
# <a name="tutorial-batch-test-data-sets"></a>教程：成批测试数据集

本教程演示如何使用批处理测试来验证语言理解 (LUIS) 应用的质量。

批测试允许使用一组已知的已标记话语和实体来验证活动的定型模型的状态。 在 JSON 格式的批处理文件中，添加话语并设置要在话语中预测的所需实体标签。

批处理测试的要求：

* 每个测试的最大话语量为 1000 个。
* 没有重复项。
* 允许的实体类型：仅限机器习得的实体。

使用本教程以外的应用时，请不要使用已添加到应用的示例言语。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 导入示例应用
> * 创建批处理测试文件
> * 运行批处理测试
> * 查看测试结果

[!INCLUDE [LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="import-example-app"></a>导入示例应用

导入提取披萨订单的应用，例如 `1 pepperoni pizza on thin crust`。

1.  下载并保存[应用 JSON 文件](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/apps/pizza-with-machine-learned-entity.json?raw=true)。

1. 登录到 [LUIS 门户](https://www.luis.ai)，选择“订阅”和“创作资源”以查看分配给该创作资源的应用。
1. 将 JSON 导入到新应用中，并将应用命名为 `Pizza app`。


1. 选择导航栏右上角的“训练”以训练该应用。

## <a name="what-should-the-batch-file-utterances-include"></a>批处理文件言语应包含的内容

批处理文件中的言语应包含机器习得的带有标签的顶级实体（包括开始和结束位置）。 言语不应是已包含在应用中的示例的一部分。 它们应该是你要在其中积极预测意向和实体的言语。

可以按意向和/或实体划分测试，或者将所有测试（最多 1000 个言语）包含在同一文件中。

## <a name="batch-file"></a>批处理文件

示例 JSON 包含一个言语（该言语包含一个带标签的实体）用于演示测试文件的外观。 你自己的测试中应该包含多个言语，这些言语包含正确的意向以及机器习得的带标签实体。

1. 在文本编辑器中创建 `pizza-with-machine-learned-entity-test.json` 或[下载](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/luis/batch-tests/pizza-with-machine-learned-entity-test.json?raw=true)它。

2. 在 JSON 格式的批处理文件中，添加要在测试中预测的言语和**意向**。

   [!code-json[Add the intents to the batch test file](~/samples-cognitive-services-data-files/luis/batch-tests/pizza-with-machine-learned-entity-test.json "Add the intent to the batch test file")]

## <a name="run-the-batch"></a>运行批处理

1. 选择顶部导航栏的“测试”。

2. 选择右侧面板中的“批处理测试面板”。

3. 选择“导入数据集”。

    > [!div class="mx-imgBorder"]
    > ![LUIS 应用的屏幕截图，其中突出显示了“导入数据集”](./media/luis-tutorial-batch-testing/import-dataset-button.png)

4. 选择 `pizza-with-machine-learned-entity-test.json` 文件的文件位置。

5. 命名数据集 `pizza test`，然后选择“完成”。

    > [!div class="mx-imgBorder"]
    > ![选择文件](./media/luis-tutorial-batch-testing/import-dataset-modal.png)

6. 选择“运行”按钮。

7. 选择“查看结果”。

8. 查看图和图例中的结果。

## <a name="review-batch-results-for-intents"></a>查看意向的批处理结果

测试结果以图形显示如何针对活动版本预测测试言语。

批处理图表将结果显示在四个象限中。 在图表右侧是一个筛选器。 筛选器包含意向和实体。 选择[图表的一个部分](luis-concept-batch-test.md#batch-test-results)或图表中的一个点时，关联的话语显示在图表下方。

鼠标悬停在图表上时，鼠标滚轮可以放大或缩小图表中的显示。 当图表上有许多点紧密地聚集在一起时，这是非常有用的。

图表分为四个象限，其中两个部分以红色显示。

1. 在筛选器列表中选择“ModifyOrder”意向。

    > [!div class="mx-imgBorder"]
    > ![在筛选器列表中选择 ModifyOrder 意向](./media/luis-tutorial-batch-testing/select-intent-from-filter-list.png)

    言语预测为“漏报”，这意味着，该言语已成功匹配其在批处理文件中列出的正面预测结果。

    > [!div class="mx-imgBorder"]
    > ![言语已成功匹配其正面预测结果](./media/luis-tutorial-batch-testing/intent-predicted-true-positive.png)

    筛选器列表中的绿色勾选标记也指示每个意向的测试成功。 所有其他意向列出了 1/1 正面评分，因为言语是针对每个意向测试的，而任何意向的负面测试不会列在批处理测试中。

1. 选择“Confirmation”意向。 此意向未在批处理测试中列出，因此，这是批处理测试中列出的言语的负面测试。

    > [!div class="mx-imgBorder"]
    > ![针对批处理文件中未列出的意向成功负面预测了言语](./media/luis-tutorial-batch-testing/true-negative-intent.png)

    根据筛选器和网格中的绿色文本所示，负面测试成功。

## <a name="review-batch-test-results-for-entities"></a>查看实体的批处理测试结果

ModifyOrder 实体（包含子实体的机器实体）显示是否匹配了顶级实体，并显示如何预测子实体。

1. 在筛选器列表中选择“ModifyOrder”实体，然后选择网格中的圆圈。

1. 实体预测结果显示在图表下方。 显示的内容包括符合预期的预测对应的实线，以及不符合预期的预测对应的虚线。

    > [!div class="mx-imgBorder"]
    > ![已成功预测批处理文件中的实体父级](./media/luis-tutorial-batch-testing/labeled-entity-prediction.png)

## <a name="finding-errors-with-a-batch-test"></a>使用批处理测试查找错误

本教程演示了如何运行测试并解释结果。 其中不会介绍测试理念或者如何对失败的测试做出响应。

* 请确保在测试中同时涵盖正面和负面言语，包括可在其中预测不同但相关意向的言语。
* 对于失败的言语，请执行以下任务，然后再次运行测试：
    * 查看当前示例中的意向和实体，并验证活动版本的示例言语是否正确，可用于标记意向和实体。
    * 添加可帮助应用预测意向和实体的特征
    * 添加更多正面示例言语
    * 查看剩余各个意向的示例言语

## <a name="clean-up-resources"></a>清理资源

[!INCLUDE [LUIS How to clean up resources](./includes/cleanup-resources-preview-portal.md)]

## <a name="next-step"></a>后续步骤

本教程使用了批处理测试来验证当前模型。

> [!div class="nextstepaction"]
> [了解模式](luis-tutorial-pattern.md)

