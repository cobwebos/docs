---
title: 教程：创建返回关键短语的 LUIS 应用 - Azure | Microsoft Docs
description: 本教程介绍如何在 LUIS 应用中添加和返回 keyPhrase 实体，以分析有关关键主题的陈述。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: 12c306b5199da5862302c28d1690b81c6e1edb0e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36264609"
---
# <a name="tutorial-create-app-that-returns-keyphrases-entity-data-found-in-utterances"></a>教程：创建可返回陈述中找到的 keyPhrases 实体数据的应用
在本教程中，我们将创建一个应用，用于演示如何从陈述中提取关键主题。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解 keyPhrase 实体 
> * 创建适用于人力资源领域的新 LUIS 应用
> * 添加 _None_ 意向并添加示例陈述
> * 添加用于从陈述中提取内容的 keyPhrase 实体
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

在本文中，可以使用免费的 [LUIS][LUIS] 帐户来创作 LUIS 应用程序。

## <a name="keyphrase-entity-extraction"></a>keyPhrase 实体提取
关键主题由预生成实体 **keyPhrase** 提供。 此实体返回陈述中的关键主题

以下陈述显示了关键短语的示例：

|陈述|keyPhrase 实体值|
|--|--|
|Is there a new medical plan with a lower deductible offered next year?|"lower deductible"<br>"new medical plan"<br>"year"|
|Is vision therapy covered in the high deductible medical plan?|"high deductible medical plan"<br>"vision therapy"|

在确定对话中的下一步骤时，聊天机器人可以考虑这些值，以及提取的其他所有实体。

## <a name="download-sample-app"></a>下载示例应用
下载[人力资源](https://github.com/Microsoft/LUIS-Samples/blob/master/documentation-samples/quickstarts/HumanResources.json)应用，并使用扩展名 *.json 将其保存到某个文件。 此示例应用识别与员工福利、组织结构图和实物资产相关的陈述。

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS][LUIS] 网站。 确保登录到需要发布 LUIS 终结点的[区域][LUIS-regions]。

2. 在 [LUIS][LUIS] 网站上选择“导入新应用”，导入在上一部分下载的人力资源应用。 

    [![](media/luis-quickstart-intent-and-key-phrase/app-list.png "“应用列表”页的屏幕截图")](media/luis-quickstart-intent-and-key-phrase/app-list.png#lightbox)

3. 在“导入新应用”对话框中，将应用命名为 `Human Resources with Key Phrase entity`。 

    ![“创建新应用”对话框的图像](./media/luis-quickstart-intent-and-key-phrase/import-new-app-inline.png)

    应用创建过程完成后，LUIS 会显示意向列表。

    [![](media/luis-quickstart-intent-and-key-phrase/intents-list.png "“意向列表”页的屏幕截图")](media/luis-quickstart-intent-and-key-phrase/intents-list.png#lightbox)

## <a name="add-keyphrase-entity"></a>添加 keyPhrase 实体 
添加 keyPhrase 预生成实体，以从陈述中提取主题。

1. 在左侧菜单中选择“实体”。

    [![“生成”部分左侧导航栏中突出显示的“实体”的屏幕截图](./media/luis-quickstart-intent-and-key-phrase/select-entities.png)](./media/luis-quickstart-intent-and-key-phrase/select-entities.png#lightbox)

2. 选择“管理预生成实体”。

    [![“实体列表”弹出对话框的屏幕截图](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/manage-prebuilt-entities.png#lightbox)

3. 在弹出对话框中选择“keyPhrase”，然后选择“完成”。 

    [![“实体列表”弹出对话框的屏幕截图](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png)](./media/luis-quickstart-intent-and-key-phrase/add-or-remove-prebuilt-entities.png#lightbox)

    <!-- TBD: asking Carol
    You won't see these entities labeled in utterances on the intents pages. 
    -->

## <a name="train-the-luis-app"></a>训练 LUIS 应用
在训练模型之前，LUIS 并不知道对模型做了此项更改。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![突出显示的“训练”按钮屏幕截图](./media/luis-quickstart-intent-and-key-phrase/train-button-expanded.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功通知栏的屏幕截图 ](./media/luis-quickstart-intent-and-key-phrase/trained-inline.png)

## <a name="publish-app-to-endpoint"></a>将应用发布到终结点

1. 在右上方的导航栏中选择“发布”。

    ![“实体”页的屏幕截图，其中已展开“发布”按钮 ](./media/luis-quickstart-intent-and-key-phrase/publish-expanded.png)

2. 选择“生产”槽和“发布”按钮。

    [![](media/luis-quickstart-intent-and-key-phrase/publish-to-production-inline.png "“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮")](media/luis-quickstart-intent-and-key-phrase/publish-to-production-expanded.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-an-utterance"></a>使用陈述查询终结点

1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    ![“发布”页的屏幕截图，其中已突出显示终结点 URL](media/luis-quickstart-intent-and-key-phrase/endpoint-url-inline.png )

2. 将光标定位到地址中 URL 的末尾，并输入 `Is there a new medical plan with a lower deductible offered next year?`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 

```
{
  "query": "Is there a new medical plan with a lower deductible offered next year?",
  "topScoringIntent": {
    "intent": "FindForm",
    "score": 0.216838628
  },
  "entities": [
    {
      "entity": "lower deductible",
      "type": "builtin.keyPhrase",
      "startIndex": 35,
      "endIndex": 50
    },
    {
      "entity": "new medical plan",
      "type": "builtin.keyPhrase",
      "startIndex": 11,
      "endIndex": 26
    },
    {
      "entity": "year",
      "type": "builtin.keyPhrase",
      "startIndex": 65,
      "endIndex": 68
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用具有 keyPhrase 实体检测功能，识别了自然语言查询意向，并返回了包含主要主题的提取数据。 

现在，聊天机器人已获得足够的信息，可以确定对话中的下一步。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和陈述中的 keyPhrase 数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建可返回情绪和意向预测结果的应用](luis-quickstart-intent-and-sentiment-analysis.md)

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
