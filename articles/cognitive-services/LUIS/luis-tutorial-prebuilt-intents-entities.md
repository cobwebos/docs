---
title: 添加预生成的意向和实体以提取语言理解中的常见数据 - Azure | Microsoft Docs
description: 了解如何使用预生成的意向和实体以提取不同类型的实体数据。
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 06/29/2018
ms.author: diberry
ms.openlocfilehash: 3fc2040e66f6fc649448d3241b01678b7bb7f214
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239029"
---
# <a name="tutorial-2-add-prebuilt-intents-and-entities"></a>教程：2。 添加预构建的意向和实体
将预生成的意向和实体添加到人力资源教程应用，以便快速了解意向预测和数据提取。 

本教程介绍如何执行下列操作：

> [!div class="checklist"]
* 添加预生成意向 
* 添加预生成实体 datetimeV2 和数量
* 训练并发布
* 查询 LUIS 并接收预测响应

## <a name="before-you-begin"></a>开始之前
如果尚未获得前一教程中所述的[人力资源](luis-quickstart-intents-only.md)应用，请将 [LUIS-Samples](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/custom-domain-intent-only-HumanResources.json) Github 存储库中的 JSON [导入](luis-how-to-start-new-app.md#import-new-app)到 [LUIS](luis-reference-regions.md#luis-website) 网站上的一个新应用中。

若要保留原始人力资源应用，请在[设置](luis-how-to-manage-versions.md#clone-a-version)页上克隆版本，并将其命名为 `prebuilts`。 克隆非常适合用于演练各种 LUIS 功能，且不会影响原始版本。 

## <a name="add-prebuilt-intents"></a>添加预生成意向
LUIS 提供几个预生成意向，以便提供常见用户意向。  

1. 确保应用在 LUIS 的“生成”部分。 在右上方的菜单栏中选择“生成”可切换到此部分。 

    [ ![LUIS 应用的屏幕截图，其中已突出显示右上方导航栏中的“生成”](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png)](./media/luis-tutorial-prebuilt-intents-and-entities/first-image.png#lightbox)

2. 选择“添加预生成域意向”。 

    [![“意向”页的屏幕截图，其中已突出显示“添加预生成域意向”按钮](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/add-prebuilt-domain-button.png#lightbox)

3. 搜索 `Utilities`。 

    [![“预生成意向”对话框的屏幕截图，其中的搜索框中包含“实用工具”](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png)](./media/luis-tutorial-prebuilt-intents-and-entities/prebuilt-intent-utilities.png#lightbox)

4. 选择以下意向，然后选择“完成”： 

    * Utilities.Cancel
    * Utilities.Confirm
    * Utilities.Help
    * Utilities.StartOver
    * Utilities.Stop


## <a name="add-prebuilt-entities"></a>添加预生成的实体
LUIS 为常见数据提取提供多个预生成的实体。 

1. 在左侧导航菜单中选择“实体”。

    [ ![“意向”列表的屏幕截图，在左侧导航中突出显示了“实体”](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png)](./media/luis-tutorial-prebuilt-intents-and-entities/entities-navigation.png#lightbox)

2. 选择“管理预生成的实体”按钮。

    [ ![“实体”列表的屏幕截图，已突出显示“管理预生成的实体”](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png)](./media/luis-tutorial-prebuilt-intents-and-entities/manage-prebuilt-entities-button.png#lightbox)

3. 从预生成的实体的列表中选择“数字”和“datetimeV2”，然后选择“完成”。

    ![在“预生成的实体”对话框中选择的数字的屏幕截图](./media/luis-tutorial-prebuilt-intents-and-entities/select-prebuilt-entities.png)

## <a name="train-and-publish-the-app"></a>训练并发布应用
1. 在 LUIS 网站的右上方，选择“训练”按钮。 

    ![“训练”按钮](./media/luis-quickstart-intents-only/train-button.png)

    当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![已训练状态栏](./media/luis-quickstart-intents-only/trained.png)

2. 在 LUIS 网站的右上方，选择“发布”按钮打开“发布”页。 

3. 默认已选择生产槽。 选择生产槽选项旁边的“发布”按钮。 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

    在发布或者测试终结点 URL 之前，无需在 Azure 门户中创建 LUIS 终结点密钥。 每个 LUIS 应用具有一个用于创作的免费初学者密钥。 使用该密钥可以不受限制地进行创作，并获得一些[终结点点击量](luis-boundaries.md#key-limits)。 

## <a name="query-endpoint-with-an-utterance"></a>使用陈述查询终结点
在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 将光标定位到地址中 URL 的末尾，并输入 `I want to cancel on March 3`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 

结果预测了 Utilities.Cancel 意向并提取了 3 月 3 日的日期和数字 3。 

    ```
    {
      "query": "I want to cancel on March 3",
      "topScoringIntent": {
        "intent": "Utilities.Cancel",
        "score": 0.7818295
      },
      "intents": [
        {
          "intent": "Utilities.Cancel",
          "score": 0.7818295
        },
        {
          "intent": "ApplyForJob",
          "score": 0.0237864349
        },
        {
          "intent": "GetJobInformation",
          "score": 0.017576348
        },
        {
          "intent": "Utilities.StartOver",
          "score": 0.0130122062
        },
        {
          "intent": "Utilities.Help",
          "score": 0.006731322
        },
        {
          "intent": "None",
          "score": 0.00524190161
        },
        {
          "intent": "Utilities.Stop",
          "score": 0.004912514
        },
        {
          "intent": "Utilities.Confirm",
          "score": 0.00092950504
        }
      ],
      "entities": [
        {
          "entity": "march 3",
          "type": "builtin.datetimeV2.date",
          "startIndex": 20,
          "endIndex": 26,
          "resolution": {
            "values": [
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2018-03-03"
              },
              {
                "timex": "XXXX-03-03",
                "type": "date",
                "value": "2019-03-03"
              }
            ]
          }
        },
        {
          "entity": "3",
          "type": "builtin.number",
          "startIndex": 26,
          "endIndex": 26,
          "resolution": {
            "value": "3"
          }
        }
      ]
    }
    ```

“3 月 3 日”有两个值，因为表述没有指出“3 月 3 日”是过去的还是未来的。 将由 LUIS 调用应用程序自行决定是做一个假设，还是通过提问进行澄清（如果需要）。 

通过轻松快速地添加预生成的意向和实体，客户端应用程序可以添加会话管理和提取常见数据类型。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在左上角的菜单中选择“我的应用”。 在应用列表中选择应用名称右侧的省略号 (***...***)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [向应用添加正则表达式实体](luis-quickstart-intents-regex-entity.md)

