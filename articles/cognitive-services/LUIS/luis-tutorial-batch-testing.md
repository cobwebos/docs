---
title: 使用批处理测试来改进 LUIS 预测 | Microsoft Docs
titleSuffix: Azure
description: 负载批处理测试、查看结果、并通过更改提高 LUIS 预测。
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/19/2018
ms.author: v-geberr
ms.openlocfilehash: 5788f17f2724a0354a1db506971c2343c1800f01
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266390"
---
# <a name="use-batch-testing-to-find-prediction-accuracy-issues"></a>使用批处理测试发现预测准确性问题

本教程演示如何使用批处理测试发现陈述预测问题。  

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 创建批处理测试文件 
* 运行批处理测试
* 查看测试结果
* 修复意向错误
* 重新测试批处理

## <a name="prerequisites"></a>先决条件

> [!div class="checklist"]
> * 本文还需要一个 [LUIS][LUIS] 帐户，以便创作 LUIS 应用程序。

> [!Tip]
> 如果没有订阅，可以注册一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-new-app"></a>创建新应用
本文将使用预生成的域 HomeAutomation。 预生成的域具有用于控制 HomeAutomation 设备（例如灯）的意向、实体和陈述。 创建应用、添加域、训练并发布。

1. 在 [LUIS] 网站，通过在“MyApps”页上选择“新建应用”来创建新应用。 

    ![创建新应用](./media/luis-tutorial-batch-testing/create-app-1.png)

2. 在对话框中输入名称 `Batchtest-HomeAutomation`。

    ![输入应用名称](./media/luis-tutorial-batch-testing/create-app-2.png)

3. 在左下角选择“预生成的域”。 

    ![选择“预生成的域”](./media/luis-tutorial-batch-testing/prebuilt-domain-1.png)

4. 为 HomeAutomation 选择“添加域”。

    ![添加 HomeAutomation 域](./media/luis-tutorial-batch-testing/prebuilt-domain-2.png)

5. 在右上方导航栏中选择“训练”。

    ![选择“训练”按钮](./media/luis-tutorial-batch-testing/train-button.png)

## <a name="batch-test-criteria"></a>批处理测试条件
批处理测试一次最多可以测试 1000 个陈述。 批处理不应具有重复项。 [导出](create-new-app.md#export-app)应用以查看当前陈述列表。  

LUIS 测试策略使用三个单独的数据集：模型陈述、批处理测试陈述和终结点陈述。 在本教程中，请确保没有使用来自模型陈述（添加到意向）或终结点陈述的陈述。 

请勿在批处理测试中使用应用中已有的任何陈述：

```
'breezeway on please',
'change temperature to seventy two degrees',
'coffee bar on please',
'decrease temperature for me please',
'dim kitchen lights to 25 .',
'fish pond off please',
'fish pond on please',
'illuminate please',
'living room lamp on please',
'living room lamps off please',
'lock the doors for me please',
'lower your volume',
'make camera 1 off please',
'make some coffee',
'play dvd',
'set lights bright',
'set lights concentrate',
'set lights out bedroom',
'shut down my work computer',
'silence the phone',
'snap switch fan fifty percent',
'start master bedroom light .',
'theater on please',
'turn dimmer off',
'turn off ac please',
'turn off foyer lights',
'turn off living room light',
'turn off staircase',
'turn off venice lamp',
'turn on bathroom heater',
'turn on external speaker',
'turn on my bedroom lights .',
'turn on the furnace room lights',
'turn on the internet in my bedroom please',
'turn on thermostat please',
'turn the fan to high',
'turn thermostat on 70 .' 
```

## <a name="create-a-batch-to-test-intent-prediction-accuracy"></a>创建批处理以测试意向预测准确性
1. 在文本编辑器（如 [VSCode](https://code.visualstudio.com/)）中创建 `homeauto-batch-1.json`。 

2. 使用想要在测试中预测的意向添加陈述。 在本教程中，出于简化目的，请采用 `HomeAutomation.TurnOn` 和 `HomeAutomation.TurnOff` 中的陈述，并在陈述中切换 `on` 和 `off` 文本。 对于 `None` 意向，添加一些不属于[域](luis-glossary.md#domain)（主题）区域的陈述。 

    若要了解批处理测试结果与批处理 JSON 如何关联，只需添加六个意向。

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": []
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": []
        }
    ]
    ```

## <a name="run-the-batch"></a>运行批处理
1. 选择顶部导航栏的“测试”。 

    ![选择导航栏的“测试”](./media/luis-tutorial-batch-testing/test-1.png)

2. 选择右侧面板中的“批处理测试面板”。 

    ![选择“批处理测试面板”](./media/luis-tutorial-batch-testing/test-2.png)

3. 选择“导入数据集”。

    ![选择“导入数据集”](./media/luis-tutorial-batch-testing/test-3.png)

4. 选择 `homeauto-batch-1.json` 文件的文件系统位置。

5. 为数据集 `set 1` 命名。

    ![选择文件](./media/luis-tutorial-batch-testing/test-4.png)

6. 选择“运行”按钮。 请等待测试完成。

    ![选择“运行”](./media/luis-tutorial-batch-testing/test-5.png)

7. 选择“查看结果”。

    ![查看结果](./media/luis-tutorial-batch-testing/test-6.png)

8. 查看图和图例中的结果。

    ![批处理结果](./media/luis-tutorial-batch-testing/batch-result-1.png)

## <a name="review-batch-results"></a>查看批处理结果
批处理结果分为两个部分。 上面部分包含图和图例。 选择图的区域名称时，下面部分将显示陈述。

所有错误都用红色指示。 图分为四个部分，其中两个部分以红色显示。 这些是要关注的部分。 

右上角部分表示测试错误预测了意向或实体的存在。 左下角部分表示测试错误预测了意向或实体的缺失。

### <a name="homeautomationturnoff-test-results"></a>HomeAutomation.TurnOff 测试结果
在图例中，选择 `HomeAutomation.TurnOff` 意向。 在图例中，名称左侧有一个绿色的成功图标。 此意向没有错误。 

![批处理结果](./media/luis-tutorial-batch-testing/batch-result-1.png)

### <a name="homeautomationturnon-and-none-intents-have-errors"></a>HomeAutomation.TurnOn 和 None 意向有错误
另外两个意向都有错误，这意味着测试预测不匹配批处理文件预期。 选择图例中的 `None` 意向可查看第一个错误。 

![None 意向](./media/luis-tutorial-batch-testing/none-intent-failures.png)

红色部分中的图表上显示故障：“误报”和“漏报”。 选择图表中的“漏报”部分名称可查看图表下方的故障陈述。 

![漏报故障](./media/luis-tutorial-batch-testing/none-intent-false-negative.png)

故障陈述，`help` 预期为 `None` 意向，但测试预测为 `HomeAutomation.TurnOn` 意向。  

有两个故障，一个在 HomeAutomation.TurnOn 中，一个在 None 中。 这两个故障都是由陈述 `help` 引起的，因为它没有达到 None 中的预期要求，而且是 HomeAutomation.TurnOn 意向的意外匹配项。 

若要确定 `None` 陈述发生故障的原因，请查看当前 `None` 中的陈述。 

## <a name="review-none-intents-utterances"></a>查看 None 意向的陈述

1. 选择顶部导航栏上的“测试”按钮，关闭“测试”面板。 

2. 在顶部导航面板中，选择“生成”。 

3. 从意向列表中选择 None 意向。

4. 选择 Control+E，查看陈述的令牌视图 
    
    |None 意向的陈述|预测分数|
    |--|--|
    |“请降低温度”|0.44|
    |“将厨房灯光调暗至 25。”|0.43|
    |“降低音量”|0.46|
    |“请打开卧室的 Internet”|0.28|

## <a name="fix-none-intents-utterances"></a>修复 None 意向的陈述
    
`None` 中的任何意向都应该在应用域之外。 这些陈述均相对于 HomeAutomation，所以它们处于错误的意向中。 

LUIS 给出的陈述预测分数小于 50% (<.50)。 如果查看另外两个意向中的陈述，会看到更高的预测分数。 如果 LUIS 的示例陈述分数低，则表明陈述使 LUIS 混淆了当前意向和其他意向。 

若要修复应用，当前处于 `None` 意向中的陈述需要移动到正确的意向，`None` 意向需要新的适当意向。 

`None` 意向中的三个陈述是为了降低自动化设备设置。 它们使用诸如 `dim`、`lower` 或 `decrease` 的单词。 第四个陈述要求在 Internet 上打开。 由于所有四个陈述都是关于打开或更改设备的功率程度，所以应将它们移动到 `HomeAutomation.TurnOn` 意向。 

这是其中一个解决方案。 还可以创建新的 `ChangeSetting` 意向并将包含“dim”、“lower”和“decrease” 的陈述移动到新意向。 

## <a name="fix-the-app-based-on-batch-results"></a>基于批处理结果修复应用
将四个陈述移动到 `HomeAutomation.TurnOn` 意向。 

1. 选择陈述列表上方的复选框，选中所有陈述。 

2. 在“重新分配意向”下拉列表中，选择 `HomeAutomation.TurnOn`。 

    ![移动陈述](./media/luis-tutorial-batch-testing/move-utterances.png)

    重新分配四个陈述后，`None` 意向的陈述列表为空。

3. 为 None 意向添加四个新意向：

    ```
    "fish"
    "dogs"
    "beer"
    "pizza"
    ```

    这些陈述肯定在 HomeAutomation 域的外部。 输入每个陈述时，监视其分数。 分数可能较低，甚至非常低（分数在红框中显示）。 训练应用后，在步骤 8 中分数将高得多。 

7. 在陈述中选择蓝色标签，然后选择“删除标签”，删除任何标签。

8. 在右上方导航栏中选择“训练”。 每个陈述的分数要高得多。 现在 `None` 意向的所有分数应该超过 80。 

## <a name="verify-the-fix-worked"></a>验证解决方法是否起作用
为了验证批处理测试中的陈述是否被正确地预测为 None 意向，请再次运行批处理测试。

1. 选择顶部导航栏的“测试”。 

2. 选择右侧面板中的“批处理测试面板”。 

3. 选择批处理名称右侧的三个点 (...)，然后选择“运行数据集”。 请等待批处理测试完成。

    ![运行数据集](./media/luis-tutorial-batch-testing/run-dataset.png)

4. 选择“查看结果”。 所有意向的名称左侧都应有绿色图标。 将右侧的筛选器设置为 `HomeAutomation.Turnoff` 意向，选择最接近图表中部的右上角面板中的绿色点。 陈述的名称出现在图表下方的表格中。 `breezeway off please` 的分数非常低。 可选的活动是向意向添加更多陈述，以增加此分数。 

    ![运行数据集](./media/luis-tutorial-batch-testing/turnoff-low-score.png)

<!--
    The Entities section of the legend may have errors. That is the next thing to fix.

## Create a batch to test entity detection
1. Create `homeauto-batch-2.json` in a text editor such as [VSCode](https://code.visualstudio.com/). 

2. Utterances have entities identified with `startPos` and `endPost`. These two elements identify the entity before [tokenization](luis-glossary.md#token), which happens in some [cultures](luis-supported-languages.md#tokenization) in LUIS. If you plan to batch test in a tokenized culture, learn how to [extract](luis-concept-data-extraction.md#tokenized-entity-returned) the non-tokenized entities.

    Copy the following JSON into the file:

    ```JSON
    [
        {
          "text": "lobby on please",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 4
            }
          ]
        },
        {
          "text": "change temperature to seventy one degrees",
          "intent": "HomeAutomation.TurnOn",
          "entities": [
            {
              "entity": "HomeAutomation.Operation",
              "startPos": 7,
              "endPos": 17
            }
          ]
        },
        {
          "text": "where is my pizza",
          "intent": "None",
          "entities": []
        },
        {
          "text": "help",
          "intent": "None",
          "entities": []
        },
        {
          "text": "breezeway off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Room",
              "startPos": 0,
              "endPos": 9
            }
          ]
        },
        {
          "text": "coffee bar off please",
          "intent": "HomeAutomation.TurnOff",
          "entities": [
            {
              "entity": "HomeAutomation.Device",
              "startPos": 0,
              "endPos": 10
            }
          ]
        }
      ]
    ```

3. Import the batch file, following the [same instructions](#run-the-batch) as the first import, and name the dataset `set 2`. Run the test.

## Possible entity errors
Since the intents in the right-side filter of the test panel still pass the test, this section focuses on correct entity identification. 

Entity testing is diferrent than intents. An utterance will have only one top scoring intent, but it may have several entities. An utterance's entity may be correctly identified, may be incorrectly identified as an entity other than the one in the batch test, may overlap with other entities, or not identified at all. 

## Review entity errors
1. Select `HomeAutomation.Device` in the filter panel. The chart changes to show a single false positive and several true negatives. 

2. Select the False positive section name. The utterance for this chart point is displayed below the chart. The labeled intent and the predicted intent are the same, which is consistent with the test -- the intent prediction is correct. 

    The issue is that the HomeAutomation.Device was detected but the batch expected HomeAutomation.Room for the utterance "coffee bar off please". `Coffee bar` could be a room or a device, depending on the environment and context. As the model designer, you can either enforce the selection as `HomeAutomation.Room` or change the batch file to use `HomeAutomation.Device`. 

    If you want to reinforce that coffee bar is a room, you nee to add an utterances to LUIS that help LUIS decide a coffee bar is a room. 

    The most direct route is to add the utterance to the intent but that to add the utterance for every entity detection error is not the machine-learned solution. Another fix would be to add an utterance with `coffee bar`.

## Add utterance to help extract entity
1. Select the **Test** button on the top navigation to close the batch test panel.

2. On the `HomeAutomation.TurnOn` intent, add the utterance, `turn coffee bar on please`. The uttterance should have all three entities detected after you select enter. 

3. Select **Train** on the top navigation panel. Wait until training completes successfully.

3. Select **Test** on the top navigation panel to open the Batch testing pane again. 

4. If the list of datasets is not visible, select **Back to list**. Select the three dots (...) at the end of `Set 2` and select `Run Dataset`. Wait for the test to complete.

5. Select **See results** to review the test results.

6. 
-->
## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [详细了解示例陈述](luis-how-to-add-example-utterances.md)

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions