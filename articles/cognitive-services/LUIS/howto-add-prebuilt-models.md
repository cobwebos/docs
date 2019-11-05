---
title: 为语言理解预生成的模型
titleSuffix: Azure Cognitive Services
description: LUIS 包含一组预构建的模型，用于快速添加常见的对话用户方案。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 2e90a5f3f7cd2cfde2a2ead26674d2348a24ae6f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73507777"
---
# <a name="add-prebuilt-models-for-common-usage-scenarios"></a>为常见使用方案添加预构建的模型 

LUIS 通过预生成域提供了一组预生成意向，用以快速添加常用的意向和话语。 这是一种快速简单地向对话式客户端应用添加功能的方法，不需要为这些功能设计模型。 

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="add-a-prebuilt-domain"></a>添加预生成域

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. 在“意向”页面上，从左下角的工具栏中选择“添加预生成域”。 

1. 选择“日历”意向并选择“添加域”按钮。

    ![添加“日历”预生成域](./media/luis-prebuilt-domains/add-prebuilt-domain.png)

1. 在左侧导航栏中选择“意向”以查看“日历”意向。 此域中每个意向都具有前缀 `Calendar.`。 与话语一起，此域的以下两个实体也添加到应用：`Calendar.Location` 和 `Calendar.Subject`。 

### <a name="train-and-publish"></a>训练并发布

1. 添加域后，通过在右上角的工具栏中选择“训练”来训练应用。 

1. 在顶部工具栏中，选择“发布”。 发布到“生产”。 

1. 当出现绿色的成功通知后，选择“参阅终结点列表”链接来查看终结点。

1. 选择一个终结点。 将会在新的浏览器选项卡中打开该终结点。 使浏览器选项卡保持打开状态，并继续执行“测试”部分。

### <a name="test"></a>测试

通过为 **q** 参数添加值在终结点上测试新意向：`Schedule a meeting with John Smith in Seattle next week`。

LUIS 返回正确的意向和会议主题：

```json
{
  "query": "Schedule a meeting with John Smith in Seattle next week",
  "topScoringIntent": {
    "intent": "Calendar.Add",
    "score": 0.824783146
  },
  "entities": [
    {
      "entity": "a meeting with john smith",
      "type": "Calendar.Subject",
      "startIndex": 9,
      "endIndex": 33,
      "score": 0.484055847
    }
  ]
}
```

## <a name="add-a-prebuilt-intent"></a>添加预生成意向

1. 在“我的应用”页面上，选择你的应用。 这将打开你的应用并显示该应用的“生成”部分。 

1. 在“意向”页面上，从意向列表上方的工具栏中选择“添加预生成意向”。 

1. 从弹出对话框中选择“Utilities.Cancel”意向。 

    ![添加预生成意向](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. 选择“完成”按钮。

### <a name="train-and-test"></a>训练和测试

1. 添加意向后，通过在右上角的工具栏中选择“训练”来训练应用。 

1. 通过在右侧的工具栏中选择“测试”来测试新意向。 

1. 在文本框中，输入用于取消的话语：

    |测试话语|预测分数|
    |--|:--|
    |我想取消我的航班。|0.67|
    |取消购买。|0.52|
    |取消会议。|0.56|

    ![测试预生成意向](./media/luis-prebuilt-intents/test.png)

## <a name="add-a-prebuilt-entity"></a>添加预构建实体

1. 单击“我的应用”页上的应用名称打开应用，然后单击左侧的“实体”。 

1. 在“实体”页上，单击“添加预构建实体”。

1. 在“添加预构建实体”对话框中，选择 datetimeV2 预构建实体。 

    ![“添加预构建实体”对话框](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. 选择“完成”。 添加实体后，你不需要训练应用。 

## <a name="publish-to-view-prebuilt-model-from-prediction-endpoint"></a>发布以查看从预测终结点预生成的模型

查看预先生成的模型的值的最简单方法是从已发布的终结点进行查询。 

## <a name="marking-entities-containing-a-prebuilt-entity-token"></a>标记包含预生成实体令牌的实体
 如果要将 `HH-1234` 等文本标记为自定义实体并将[预生成数字](luis-reference-prebuilt-number.md)添加到模型，则无法在 LUIS 门户中标记自定义实体。 可以使用 API 进行标记。 

 若要标记此类型的令牌（已使用预生成实体标记其中一部分），请从 LUIS 应用中删除预生成实体。 无需训练该应用。 然后，使用你自己的自定义实体标记令牌。 再将预生成实体添加回 LUIS 应用。

 对于另一个示例，请考虑将查询文本作为类首选项的列表： `I want first year spanish, second year calculus, and fourth year english lit.` 如果 LUIS 应用已添加称之为 fakes.prebuild 序号，`first`，`second`，并且 `fourth` 将已标记为序数。 如果要捕获序号和类，则可以创建复合实体并围绕预生成序号和类名称的自定义实体进行包装。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"]
> [通过 REST Api 从 .csv 生成模型](./luis-tutorial-node-import-utterances-csv.md)
