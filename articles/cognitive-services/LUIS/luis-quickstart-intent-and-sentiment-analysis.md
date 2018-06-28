---
title: 教程：创建返回情绪分析的 LUIS 应用 - Azure | Microsoft Docs
description: 本教程介绍如何将情绪分析添加到 LUIS 应用，以便在陈述中分析积极、消极和中性的情感。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: d000637312619fc493e2f7bad8e8edf0d8d0d94b
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265328"
---
# <a name="tutorial-create-app-that-returns-sentiment-along-with-intent-prediction"></a>教程：创建可返回情绪和意向预测结果的应用
在本教程中，我们将创建一个应用，用于演示如何从陈述中提取积极、消极和中性的情绪。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解分层实体和根据上下文学习到的子级 
> * 使用 Bookflight 意向创建适用于旅行领域的新 LUIS 应用
> * 添加 _None_ 意向并添加示例陈述
> * 添加包含出发地和目的地子级的位置分层实体
> * 训练并发布应用
> * 查询应用的终结点以查看包含分层子级的 LUIS JSON 响应 

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="sentiment-analysis"></a>情绪分析
情绪分析是确定用户陈述是积极、消极还是中性的功能。 

以下陈述显示了情绪的示例：

|情绪和评分|陈述|
|:--|--|
|积极 - 0.89 |浓汤和沙拉组合非常美味。|
|消极 - 0.07 |我不喜欢晚宴中的开胃菜。|

情绪分析是应用到每个陈述的应用设置。 不需要在陈述中查找指示情绪的单词并对其进行标记。 LUIS 会自动完成此操作。

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS][LUIS] 网站。 确保登录到需要发布 LUIS 终结点的[区域][LUIS-regions]。

2. 在 [LUIS][LUIS] 网站上，选择“创建新应用”。 

    [![](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png "“应用列表”页的屏幕截图")](media/luis-quickstart-intent-and-sentiment-analysis/app-list.png#lightbox)

3. 在“创建新应用”对话框中，将应用命名为 `Restaurant Reservations With Sentiment`，然后选择“完成”。 

    ![“创建新应用”对话框的图像](./media/luis-quickstart-intent-and-sentiment-analysis/create-app-ddl.png)

    应用创建过程完成后，LUIS 会显示包含 None 意向的意向列表。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png "“意向列表”页的屏幕截图")](media/luis-quickstart-intent-and-sentiment-analysis/intents-list.png#lightbox)

## <a name="add-a-prebuilt-domain"></a>添加预生成域
添加预生成域，以快速添加意向、实体和带有标签的陈述。

1. 在左侧菜单中选择“预生成域”。

    [![“预生成域”按钮屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-button-expanded.png#lightbox)

2. 选择 **RestaurantReservation** 预生成域对应的“添加域”。 等待添加完该域。

    [![“预生成域”列表的屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-expanded.png#lightbox)

3. 在左侧导航栏中选择“意向”。 此预生成域具有一个意向。

    [![“预生成域”的屏幕截图，其中已突出显示左侧导航栏中的“意向”](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png)](./media/luis-quickstart-intent-and-sentiment-analysis/prebuilt-domains-list-domain-added-expanded.png#lightbox)

4.  选择“RestaurantReservation.Reserve”意向。 

    [![“意向”列表的屏幕截图，其中已突出显示“RestaurantReservation.Reserve”](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png)](./media/luis-quickstart-intent-and-sentiment-analysis/select-intent.png#lightbox)

5. 切换“实体”视图，查看标记了域特定实体的许多陈述。

    [![“RestaurantReservation.Reserve”意向的屏幕截图，其中突出显示了从“实体”视图切换到“标记”视图后的结果](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-inline.png)](./media/luis-quickstart-intent-and-sentiment-analysis/utterance-list-expanded.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![突出显示的“训练”按钮屏幕截图](./media/luis-quickstart-intent-and-sentiment-analysis/train-button-expanded.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功通知栏的屏幕截图 ](./media/luis-quickstart-intent-and-sentiment-analysis/trained-expanded.png)

## <a name="configure-app-to-include-sentiment-analysis"></a>将应用配置为包含情绪分析
在“发布”页上启用情绪分析。 

1. 在右上方的导航栏中选择“发布”。

    ![“意向”页的屏幕截图，其中已展开“发布”按钮 ](./media/luis-quickstart-intent-and-sentiment-analysis/publish-expanded.png)

2. 选择“启用情绪分析”。

    ![“发布”页的屏幕截图，其中已突出显示“启用情绪分析” ](./media/luis-quickstart-intent-and-sentiment-analysis/enable-sentiment-expanded.png)

3. 选择“生产”槽和“发布”按钮。

    [![](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-inline.png "“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮")](media/luis-quickstart-intent-and-sentiment-analysis/publish-to-production-expanded.png#lightbox)

4. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-an-utterance"></a>使用陈述查询终结点

1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    ![“发布”页的屏幕截图，其中已突出显示终结点 URL](media/luis-quickstart-intent-and-sentiment-analysis/endpoint-url-inline.png)

2. 将光标定位到地址中 URL 的末尾，并输入 `Reserve table for  10 on upper level away from kitchen`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的情绪分析的 `RestaurantReservation.Reserve` 意向。

```
{
  "query": "Reserve table for 10 on upper level away from kitchen",
  "topScoringIntent": {
    "intent": "RestaurantReservation.Reserve",
    "score": 0.9926384
  },
  "intents": [
    {
      "intent": "RestaurantReservation.Reserve",
      "score": 0.9926384
    },
    {
      "intent": "None",
      "score": 0.00961109251
    }
  ],
  "entities": [],
  "sentimentAnalysis": {
    "label": "neutral",
    "score": 0.5
  }
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用已启用情绪分析，识别了自然语言查询意向，并返回了包含总体情绪（评分形式）的提取数据。 

现在，聊天机器人已获得足够的信息，可以确定对话中的下一步。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和陈述中的情绪数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"] 
> [使用 C# 调用 LUIS 终结点 API](luis-get-started-cs-get-intent.md) 

<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
