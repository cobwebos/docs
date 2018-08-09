---
title: 使用批处理测试来改进 LUIS 预测 | Microsoft Docs
titleSuffix: Azure
description: 负载批处理测试、查看结果、并通过更改提高 LUIS 预测。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 08/02/2018
ms.author: diberry
ms.openlocfilehash: e907a1f5ad8e75e018149e53ae0c2f9faff10a89
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493099"
---
# <a name="improve-app-with-batch-test"></a>使用批处理测试改进应用

本教程演示如何使用批处理测试发现陈述预测问题。  

本教程介绍如何执行下列操作：

<!-- green checkmark -->
> [!div class="checklist"]
* 创建批处理测试文件 
* 运行批处理测试
* 查看测试结果
* 修复错误 
* 重新测试批处理

[!include[LUIS Free account](../../../includes/cognitive-services-luis-free-key-short.md)]

## <a name="before-you-begin"></a>开始之前

如果尚未获得[查看终结点话语](luis-tutorial-review-endpoint-utterances.md)教程中所述的人力资源应用，请将 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。 要导入的应用位于 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-review-HumanResources.json) Github 存储库中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `batchtest`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

将应用定型。

## <a name="purpose-of-batch-testing"></a>批处理测试的目的

批测试允许使用一组已知的已标记话语和实体来验证活动的定型模型的状态。 在 JSON 格式的批处理文件中，添加话语并设置要在话语中预测的所需实体标签。 

<!--The recommended test strategy for LUIS uses three separate sets of data: example utterances provided to the model, batch test utterances, and endpoint utterances. --> 使用本教程以外的应用时，请确保不使用已经添加到意向的示例话语。 若要验证针对示例话语的批处理测试话语，请[导出](luis-how-to-start-new-app.md#export-app)应用。 比较应用示例话语和批处理测试话语。 

批处理测试的要求：

* 每个测试的最大话语量为 1000 个。 
* 没有重复项。 
* 允许的实体类型：仅简单、层次（仅父级）和复合的机器学习实体。 批处理测试仅适用于机器学习意向和实体。

## <a name="create-a-batch-file-with-utterances"></a>使用话语创建批处理文件

1. 在文本编辑器（如 [VSCode](https://code.visualstudio.com/)）中创建 `HumanResources-jobs-batch.json`。 

2. 在 JSON 格式的批处理文件中，使用想要在测试中预测的意向添加话语。 

   [!code-json[Add the intents to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-jobs-batch.json "Add the intents to the batch test file")]

## <a name="run-the-batch"></a>运行批处理

1. 选择顶部导航栏的“测试”。 

2. 选择右侧面板中的“批处理测试面板”。 

    [![LUIS 应用的屏幕截图，其中突出显示了“批处理测试面板”](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png)](./media/luis-tutorial-batch-testing/hr-batch-testing-panel-link.png#lightbox)

3. 选择“导入数据集”。

    [![LUIS 应用的屏幕截图，其中突出显示了“导入数据集”](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png)](./media/luis-tutorial-batch-testing/hr-import-dataset-button.png#lightbox)

4. 选择 `HumanResources-jobs-batch.json` 文件的文件系统位置。

5. 命名数据集 `intents only`，然后选择“完成”。

    ![选择文件](./media/luis-tutorial-batch-testing/hr-import-new-dataset-ddl.png)

6. 选择“运行”按钮。 请等待测试完成。

7. 选择“查看结果”。

8. 查看图和图例中的结果。

    [![LUIS 应用的批处理测试结果的屏幕截图](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png)](./media/luis-tutorial-batch-testing/hr-intents-only-results-1.png#lightbox)

## <a name="review-batch-results"></a>查看批处理结果

批处理图表将结果显示在四个象限中。 在图表右侧是一个筛选器。 默认情况下，筛选器设置为列表中的第一个意向。 筛选器包含所有意向，并且仅包含简单、分层（仅父级）和复合实体。 选择[图表的一个部分](luis-concept-batch-test.md#batch-test-results)或图表中的一个点时，关联的话语显示在图表下方。 

鼠标悬停在图表上时，鼠标滚轮可以放大或缩小图表中的显示。 当图表上有许多点紧密地聚集在一起时，这是非常有用的。 

图表分为四个象限，其中两个部分以红色显示。 这些是要关注的部分。 

### <a name="getjobinformation-test-results"></a>GetJobInformation 测试结果

显示在筛选器中的 GetJobInformation 测试结果显示四种预测中有 2 种是成功的。 选择右上象限上方的名称“误报”，查看图表下方的话语。 

![LUIS 批处理测试话语](./media/luis-tutorial-batch-testing/hr-applyforjobs-false-positive-results.png)

为什么两个话语被预测为 ApplyForJob，而不是正确的意向 GetJobInformation？ 两个意向在字词的选择和排列方式方面密切相关。 在复合实体中包装此外，ApplyForJob 的示例几乎是 GetJobInformation 的三倍。 示例话语的这种不平衡对 ApplyForJob 意向有利。 

请注意，这两个意向都有相同的错误计数。 一个意向中的错误预测也会影响另一个意向。 由于错误地预测了一个意向的话语，也错误地未预测另一个意向，因此二者都有错误。 

![LUIS 批处理测试筛选器错误](./media/luis-tutorial-batch-testing/hr-intent-error-count.png)

对应于“误报”部分中顶点的话语为 `Can I apply for any database jobs with this resume?` 和 `Can I apply for any database jobs with this resume?`。 对于第一个话语，单词 `resume` 仅在 ApplyForJob 中使用过。 对于第二个话语，单词 `apply` 仅在 ApplyForJob 意向中使用过。

## <a name="fix-the-app-based-on-batch-results"></a>基于批处理结果修复应用

本部分的目标是通过修复应用，正确预测 GetJobInformation 的所有话语。 

一个看似快速的解决方法是将这些批处理文件话语添加到正确的意向。 但这不是你想要做的。 你想让 LUIS 正确地预测这些话语，而无需将其添加为示例。 

可能还想知道如何从 ApplyForJob 中删除话语，直到话语数量与 GetJobInformation 中相同。 这可能会修复测试结果，但会阻碍 LUIS 下一次准确地预测该意向。 

第一个解决方法是向 GetJobInformation 添加更多话语。 第二个解决方法是减少针对 ApplyForJob 意向的单词（如 `resume` 和 `apply`）的权重。 

### <a name="add-more-utterances-to-getjobinformation"></a>向 GetJobInformation 添加更多话语

1. 选择顶部导航面板中的“测试”按钮，关闭批处理测试面板。 

2. 从意向列表中选择“GetJobInformation”。 

3. 添加更多长度、字词选择和排列方式不同的话语，确保包含术语 `resume`、`c.v.` 和 `apply`：

    |GetJobInformation 意向的示例话语|
    |--|
    |仓库中新的存货管理员工作是否要求通过简历申请？|
    |目前哪里有屋顶工作？|
    |我听说有一份医疗编码工作需要简历。|
    |我想找份工作帮助大学生编写简历。 |
    |这是我的简历，我正在使用计算机在社区大学找一份新的工作。|
    |有哪些儿童和家庭护理方面的职位可供选择？|
    |报社是否需要实习生？|
    |我的简历 显示了我擅长分析采购、预算和亏损。 是否有此类工作？|
    |现在是否有钻地工作？|
    |我当了 8 年 EMS 司机。 是否有新工作？|
    |有新的食物处理工作需要申请吗？|
    |有多少新的庭院工作岗位？|
    |是否有新的劳资关系和谈判 HR 职位？|
    |我有图书馆和档案管理硕士学位。 是否有任何新职位？|
    |今天城市中有照顾 13 岁儿童的保姆工作吗？|

    不要在话语中标记“工作”实体。 本教程的本部分仅侧重于意向预测。

4. 通过选择右上角导航中的“定型”来定型应用。

## <a name="verify-the-fix-worked"></a>验证解决方法是否起作用

为了验证批处理测试中的话语是否被正确地预测，请再次运行批处理测试。

1. 选择顶部导航栏的“测试”。 如果批处理结果仍处于打开状态，请选择“返回到列表”。  

2. 选择批处理名称右侧的省略号 (...) 按钮，然后选择“运行数据集”。 请等待批处理测试完成。 请注意，“查看结果”按钮现在为绿色。 这意味着整个批处理已成功运行。

3. 选择“查看结果”。 所有意向的名称左侧都应有绿色图标。 

    ![LUIS 的屏幕截图，其中已突出显示“批处理结果”按钮](./media/luis-tutorial-batch-testing/hr-batch-test-intents-no-errors.png)

## <a name="create-batch-file-with-entities"></a>使用实体创建批处理文件 

若要验证批处理测试中的实体，需要在批处理 JSON 文件中标记实体。 仅使用机器学习实体：简单、分层（仅父级）和复合实体。 不要添加非机器学习实体，因为它们总是通过正则表达式或显式文本匹配找到的。

总字（[令牌](luis-glossary.md#token)）计数的实体的变化会影响预测质量。 请确保提供给具有标记话语的意向的定型数据包括各种长度的实体。 

首次编写和测试批处理文件时，最好从知道有用的一些话语和实体以及认为可能错误预测的一些话语和实体开始。 这有助于快速专注于问题区域。 使用未预测的几个不同的“工作”名称测试 GetJobInformation 和 ApplyForJob 意向之后，开发了此批处理测试文件，用于查看“工作”实体的某些值是否存在预测问题。 

测试话语中提供的“工作”实体的值通常是一个或两个词，其中有几个示例有更多词。 如果自己的人力资源应用通常有多个词的工作名称，该应用中带有“工作”实体标记的示例话语将无法正常工作。

1. 在文本编辑器（如 [VSCode](https://code.visualstudio.com/)）中创建 `HumanResources-entities-batch.json`。 或从 LUIS-Samples Github 存储库中下载[此文件](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json)。


2. 在 JSON 格式的批处理文件中，添加一个对象数组，其中包含具有想要在测试中预测的意向的话语以及话语中任何实体的位置。 由于实体是基于令牌的，因此请确保启动和停止字符上的每个实体。 不要以空格开始或结束话语。 这会导致批处理文件导入过程中出现错误。  

   [!code-json[Add the intents and entities to the batch test file](~/samples-luis/documentation-samples/tutorial-batch-testing/HumanResources-entities-batch.json "Add the intents and entities to the batch test file")]


## <a name="run-the-batch-with-entities"></a>使用实体运行批处理

1. 选择顶部导航栏的“测试”。 

2. 选择右侧面板中的“批处理测试面板”。 

3. 选择“导入数据集”。

4. 选择 `HumanResources-entities-batch.json` 文件的文件系统位置。

5. 命名数据集 `entities`，然后选择“完成”。

6. 选择“运行”按钮。 请等待测试完成。

7. 选择“查看结果”。

## <a name="review-entity-batch-results"></a>查看实体批处理结果

图表打开，所有意向都已正确预测。 在右侧筛选器中向下滚动，查找错误的实体预测。 

1. 在筛选器中选择“工作”实体。

    ![筛选器中的错误实体预测](./media/luis-tutorial-batch-testing/hr-entities-filter-errors.png)

    图表更改以显示实体预测。 

2. 选择图表左下象限的“漏报”。 然后使用组合键 control + E 切换到令牌视图。 

    [![实体预测的令牌视图](./media/luis-tutorial-batch-testing/token-view-entities.png)](./media/luis-tutorial-batch-testing/token-view-entities.png#lightbox)
    
    查看图表下方的话语会发现，当“工作”名称包括 `SQL` 时，会出现一致性错误。 查看示例话语和“工作”短语列表，SQL 仅使用一次，并且仅作为较大工作名称的一部分，`sql/oracle database administrator`。

## <a name="fix-the-app-based-on-entity-batch-results"></a>基于实体批处理结果修复应用

修复应用需要 LUIS 正确地确定 SQL 作业的变体。 对于该修补程序有多个选项。 

* 显式添加更多示例话语，使用 SQL 并将这些词标记为“工作”实体。 
* 将更多 SQL 作业显式添加到短语列表

这些任务会保留以供执行。

在正确预测实体之前添加[模式](luis-concept-patterns.md)不会解决该问题。 这是因为在检测到模式中的所有实体之前，该模式将不会匹配。 

## <a name="what-has-this-tutorial-accomplished"></a>本教程实现了哪些目的？

通过查找批处理中的错误并更正模型，增加了应用预测准确性。 

## <a name="clean-up-resources"></a>清理资源

[!include[LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解模式](luis-tutorial-pattern.md)

