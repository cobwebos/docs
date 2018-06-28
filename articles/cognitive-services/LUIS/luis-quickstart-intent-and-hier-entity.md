---
title: 教程：创建 LUIS 应用以获取位置数据 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和分层实体创建一个简单的 LUIS 应用，以提取数据。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/27/2018
ms.author: v-geberr
ms.openlocfilehash: 2547407126943161ba604fa2f5e80b9186cae57e
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36266492"
---
# <a name="tutorial-create-app-that-uses-hierarchical-entity"></a>教程：创建使用分层实体的应用
在本教程中，我们将创建一个应用，用于演示如何根据上下文查找相关的数据片段。 

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解分层实体和根据上下文学习到的子级 
> * 使用 Bookflight 意向创建适用于旅行领域的新 LUIS 应用
> * 添加 _None_ 意向并添加示例陈述
> * 添加包含出发地和目的地子级的位置分层实体
> * 训练并发布应用
> * 查询应用的终结点以查看包含分层子级的 LUIS JSON 响应 

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="purpose-of-the-app-with-this-entity"></a>包含此实体的应用的用途
此应用确定用户是否想要预订航班。 它使用分层实体在用户文本中确定位置、出发地城市和目的地城市。 

分层实体非常适合此类数据，因为这两个数据片段：

* 都是位置，通常以城市或机场代码表示。
* 通常围绕单词提供独特的单词选项，能够确定哪个是出发地，哪个是目的地。 这些单词包括：to（到）、headed toward（前往）、from（从）、leaving（离开）
* 两个位置往往在同一个陈述中。 

**分层**实体的用途是根据上下文查找陈述中的相关数据。 考虑以下陈述：

```JSON
1 ticket from Seattle to Cairo`
```

该陈述中指定了两个位置。 一个位置是出发地城市 Seattle，另一个位置是目的地城市 Cairo。 在预订航班时，这些城市非常重要。 尽管可以使用简单实体找到这些位置，但它们彼此相关，往往可以在同一个陈述中找到。 因此，将它们分组为分层实体“Location”的子级会很有帮助。 

与使用机器学习的实体时一样，应用需要获取包含标记的出发地城市和目的地城市的示例陈述。 这样，LUIS 便知道实体在陈述中的位置、实体的长度及其相关单词。 

## <a name="app-intents"></a>应用意向
意向是用户需求的类别。 此应用有两个意向：BookFlight 和 None。 [None](luis-concept-intent.md#none-intent-is-fallback-for-app) 意向是有针对性的，表示应用外部的任何属性。  

## <a name="hierarchical-entity-is-contextually-learned"></a>分层实体是根据上下文学习到的 
实体的用途是查找陈述中的文本部分并将其分类。 [分层](luis-concept-entity-types.md)实体是基于用法上下文的父-子实体。 用户可以根据 `to` 和 `from` 的用法在陈述中确定出发地和目的地城市。 这些都是上下文用法的示例。  

对于此旅行应用，LUIS 会适当地提取出发地和目的地位置，以便能够创建和填充标准预订。 LUIS 允许陈述包含变体、缩写和俚语。 

用户提供的简单陈述示例包括：

```
Book a flight to London for next Monday
2 tickets from Dallas to Dublin this weekend
Researve a seat from New York to Paris on the first of April
```

陈述的缩写或俚语版本包括：

```
LHR tomorrow
SEA to NYC next Monday
LA to MCO spring break
```
 
分层实体与出发地和目的地位置相匹配。 如果只存在分层实体的一个子级（出发地或目的地），仍会提取该子级。 不需要找到所有子级即可提取一个或一些子级。 

## <a name="what-luis-does"></a>LUIS 的作用
在[终结点](https://aka.ms/luis-endpoint-apis)中识别、[提取](luis-concept-data-extraction.md#list-entity-data)并以 JSON 格式返回陈述的意向和实体后，LUIS 即告完成。 调用方应用程序或聊天机器人按照设计的任何方式提取该 JSON 响应并履行请求。 

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS][LUIS] 网站。 确保登录到需要发布 LUIS 终结点的[区域][LUIS-regions]。

2. 在 [LUIS][LUIS] 网站上，选择“创建新应用”。  

    [![](media/luis-quickstart-intent-and-hier-entity/app-list.png "应用列表页的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/app-list.png#lightbox)

3. 在弹出的对话框中，输入名称 `MyTravelApp`。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-app.png "“创建新应用”弹出对话框的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/create-new-app.png#lightbox)

4. 过程完成后，应用会显示具有 **None** 意向的“意向”页。 

    [![](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png "仅包含 None 意向的“意向”列表屏幕截图")](media/luis-quickstart-intent-and-hier-entity/intents-page-none-only.png#lightbox)

## <a name="create-a-new-intent"></a>创建新意向

1. 在“意向”页上，选择“创建新意向”。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png "“意向”列表的屏幕截图，其中已突出显示“创建新意向”按钮")](media/luis-quickstart-intent-and-hier-entity/create-new-intent-button.png#lightbox)

2. 输入新意向名称 `BookFlight`。 每当用户想要预订航班时，都应该选择此意向。

    创建意向会创建想要识别的主要信息类别。 为类别命名可让使用 LUIS 查询结果的其他任何应用程序使用该类别名称来查找相应的答案或采取相应的措施。 LUIS 不会回答这些问题，而只会识别以自然语言请求的信息类型。 

    [![](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png "“创建新意向”弹出对话框的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/create-new-intent.png#lightbox)

3. 将多个陈述添加到用户预期会请求的 `BookFlight` 意向，例如：

    | 示例陈述|
    |--|
    |Book 2 flights from Seattle to Cairo next Monday|
    |Reserve a ticket to London tomorrow|
    |Schedule 4 seats from Paris to London for April 1|

    [![](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png "在 BookFlight 意向页上输入陈述的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/enter-utterances-on-intent.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>将陈述添加到 None 意向

LUIS 应用当前没有 **None** 意向的陈述。 它需要包含你不希望应用回答的陈述，因此，它必须在 **None** 意向中包含陈述。 请不要将此意向留空。 

1. 在左侧面板中选择“意向”。 

    [![](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png "BookFlight 意向页的屏幕截图，其中已突出显示“意向”按钮")](media/luis-quickstart-intent-and-hier-entity/select-intents-from-bookflight-intent.png#lightbox)

2. 选择“None”意向。 添加用户可能会输入的、但与应用无关的三条陈述：

    | 示例陈述|
    |--|
    |Cancel!|
    |Good bye|
    |What is going on?|

## <a name="when-the-utterance-is-predicted-for-the-none-intent"></a>何时预测 None 意向的陈述
在 LUIS 调用方应用程序（例如聊天机器人）中，当 LUIS 返回陈述的 **None** 意向时，机器人可以询问用户是否要结束对话。 如果用户不想要结束对话，机器人还能提供有关继续对话的更多指示。 

实体在 **None** 意向中工作。 如果评分最高的意向是 **None**，但提取的实体对聊天机器人而言有意义，则聊天机器人可以跟进以客户意向为重点的问题。 

## <a name="create-a-location-entity-from-the-intent-page"></a>从“意向”页创建位置实体
在两个意向中包含陈述后，LUIS 需要了解位置是什么。 导航回到 `BookFlight` 意向，并遵循以下步骤标示（标记）陈述中的城市名称：

1. 在左侧面板中选择“意向”，返回到 `BookFlight` 意向。

2. 在意向列表中选择 `BookFlight`。

3. 在陈述 `Book 2 flights from Seattle to Cairo next Monday` 中，选择单词 `Seattle`。 此时会出现一个下拉菜单，其顶部显示了用于创建新实体的文本框。 在文本框中输入实体名称 `Location`，然后在下拉菜单中选择“创建新实体”。 

    [![](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png "BookFlight 意向页 - 从选定的文本创建新实体的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/label-seattle-in-utterance.png#lightbox)

4. 在弹出窗口中，选择包含 `Origin` 和 `Destination` 子实体的“分层”实体类型。 选择“完成”。

    [![](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png "用于创建新位置实体的弹出对话框的屏幕截图")](media/luis-quickstart-intent-and-hier-entity/hier-entity-ddl.png#lightbox)

    `Seattle` 的标签已标记为 `Location`，因为 LUIS 不知道名词是出发地还是目的地，或者两者都不是。 依次选择 `Seattle`、“位置”，遵循菜单操作，然后选择 `Origin`。

5. 创建实体并标记一个陈述后，依次选择城市名称、“位置”，然后遵循右侧的菜单选择 `Origin` 或 `Destination`，以标记其他城市。

    [![](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png "Bookflight 实体的屏幕截图，其中包含针对实体选项选择的陈述文本")](media/luis-quickstart-intent-and-hier-entity/label-destination-in-utterance.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![训练应用](./media/luis-quickstart-intent-and-hier-entity/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功](./media/luis-quickstart-intent-and-hier-entity/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish.png "Bookflight 意向的屏幕截图，其中已突出显示“发布”按钮")](media/luis-quickstart-intent-and-hier-entity/publish.png#lightbox)

2. 选择“生产”槽和“发布”按钮。

    [![](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png "“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮")](media/luis-quickstart-intent-and-hier-entity/publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的陈述查询终结点
1. 在“发布”页的底部，选择“终结点”链接。 此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 

    [![](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png "“发布”页的屏幕截图，其中已突出显示终结点 URL")](media/luis-quickstart-intent-and-hier-entity/publish-select-endpoint.png#lightbox)

2. 将光标定位到地址中 URL 的末尾，并输入 `1 ticket to Portland on Friday`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回包含所提取的分层实体的 `BookFlight` 意向。

```
{
  "query": "1 ticket to Portland on Friday",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.9998226
  },
  "intents": [
    {
      "intent": "BookFlight",
      "score": 0.9998226
    },
    {
      "intent": "None",
      "score": 0.221926212
    }
  ],
  "entities": [
    {
      "entity": "portland",
      "type": "Location::Destination",
      "startIndex": 12,
      "endIndex": 19,
      "score": 0.564448953
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含两个意向和一个分层实体，识别了自然语言查询意向，并返回了提取的数据。 

现在，聊天机器人已获得足够的信息，可以确定确定主要操作 `BookFlight`，以及在陈述中找到的位置信息。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以执行下一步骤。 LUIS 不会针对机器人或调用方应用程序执行编程工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤
> [!div class="nextstepaction"] 
> [了解如何添加列表实体](luis-quickstart-intent-and-list-entity.md) 

添加**数字**[预生成实体](luis-how-to-add-entities.md#add-prebuilt-entity)以提取数字。 

添加 **datetimeV2** [预生成实体](luis-how-to-add-entities.md#add-prebuilt-entity)以提取日期信息。


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
[LUIS-regions]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#publishing-regions
