---
title: 教程：创建 LUIS 应用以提取数据 - Azure | Microsoft Docs
description: 本教程介绍如何使用意向和简单实体创建一个简单的 LUIS 应用，以提取机器学习的数据。
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: luis
ms.topic: tutorial
ms.date: 03/29/2018
ms.author: v-geberr
ms.openlocfilehash: 1e8647e34da3d34946a4f6ac298017f6d4c99de6
ms.sourcegitcommit: 301855e018cfa1984198e045872539f04ce0e707
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2018
ms.locfileid: "36265354"
---
# <a name="tutorial-create-app-that-uses-simple-entity"></a>教程：创建使用简单实体的应用
在本教程中，我们将使用**简单**实体创建一个应用，用于演示如何从陈述中提取机器学习的数据。

<!-- green checkmark -->
> [!div class="checklist"]
> * 了解简单实体 
> * 使用 SendMessage 意向创建适用于通信领域的新 LUIS 应用
> * 添加 _None_ 意向并添加示例陈述
> * 添加简单实体以从陈述中提取消息内容
> * 训练并发布应用
> * 查询应用终结点以查看 LUIS JSON 响应

本文需要一个免费的 [LUIS][LUIS] 帐户，以便能够创作 LUIS 应用程序。

## <a name="purpose-of-the-app"></a>应用的用途
此应用演示如何从陈述中提取数据。 假设某个聊天机器人包含以下陈述：

```JSON
Send a message telling them to stop
```

意向是发送消息。 陈述的重要数据是消息本身，即 `telling them to stop`。  

## <a name="purpose-of-the-simple-entity"></a>简单实体的用途
简单实体的用途是让 LUIS 知道消息是什么，以及在陈述中的哪个位置可以找到该消息。 根据选择的单词和陈述的长度，消息在陈述中的位置根据陈述的不同而不同。 LUIS 需要跨所有意向获取任一陈述的消息示例。  

对于此简单应用，消息位于陈述的末尾。 

## <a name="create-a-new-app"></a>创建新应用
1. 登录到 [LUIS][LUIS] 网站。 确保登录到需要发布 LUIS 终结点的区域。

2. 在 [LUIS][LUIS] 网站上，选择“创建新应用”。  

    ![LUIS 应用列表](./media/luis-quickstart-primary-and-secondary-data/app-list.png)

3. 在弹出的对话框中，输入名称 `MyCommunicator`。 

    ![LUIS 应用列表](./media/luis-quickstart-primary-and-secondary-data/create-new-app-dialog.png)

4. 过程完成后，应用会显示具有 **None** 意向的“意向”页。 

    [![](media/luis-quickstart-primary-and-secondary-data/intents-list.png "包含 None 意向的 LUIS“意向”页屏幕截图")](media/luis-quickstart-primary-and-secondary-data/intents-list.png#lightbox)

## <a name="create-a-new-intent"></a>创建新意向

1. 在“意向”页上，选择“创建新意向”。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png "LUIS 的屏幕截图，其中已突出显示“创建新意向”按钮")](media/luis-quickstart-primary-and-secondary-data/create-new-intent-button.png#lightbox)

2. 输入新意向名称 `SendMessage`。 每当用户想要发送消息时，都应该选择此意向。

    创建意向会创建想要识别的主要信息类别。 为类别命名可让使用 LUIS 查询结果的其他任何应用程序使用该类别名称来查找相应的答案或采取相应的措施。 LUIS 不会回答这些问题，而只会识别以自然语言请求的信息类型。 

    ![输入意向名称 SendMessage](./media/luis-quickstart-primary-and-secondary-data/create-new-intent-popup-dialog.png)

3. 将 7 个陈述添加到用户预期会请求的 `SendMessage` 意向，例如：

    | 示例陈述|
    |--|
    |Reply with I got your message, I will have the answer tomorrow|
    |Send message of When will you be home?|
    |Text that I am busy|
    |Tell them that it needs to be done today|
    |IM that I am driving and will respond later|
    |Compose message to David that says When was that?|
    |say greg hello|

    [![](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png "已在其中输入陈述的 LUIS 屏幕截图")](media/luis-quickstart-primary-and-secondary-data/enter-utterances-on-intent-page.png#lightbox)

## <a name="add-utterances-to-none-intent"></a>将陈述添加到 None 意向

LUIS 应用当前没有 **None** 意向的陈述。 它需要包含你不希望应用回答的陈述，因此，它必须在 **None** 意向中包含陈述。 请不要将此意向留空。 
    
1. 在左侧面板中选择“意向”。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png "LUIS 的屏幕截图，其中已突出显示“意向”按钮")](media/luis-quickstart-primary-and-secondary-data/select-intent-link.png#lightbox)

2. 选择“None”意向。 

    [![](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png "选择“None”意向的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/select-none-intent.png#lightbox)

3. 添加用户可能会输入的、但与应用无关的 3 个陈述。 下面是一些合理的 **None** 陈述：

    | 示例陈述|
    |--|
    |Cancel!|
    |Good bye|
    |What is going on?|
    
    在 LUIS 调用方应用程序（例如聊天机器人）中，如果 LUIS 返回陈述的 **None** 意向，机器人可以询问用户是否要结束对话。 如果用户不想要结束对话，机器人还能提供有关继续对话的更多指示。 

    [![](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png "包含 None 意向的陈述的 LUIS 屏幕截图")](media/luis-quickstart-primary-and-secondary-data/utterances-for-none-intent.png#lightbox)

## <a name="create-a-simple-entity-to-extract-message"></a>创建简单实体用于提取消息 
1. 在左侧菜单中选择“意向”。

    ![选择“意向”链接](./media/luis-quickstart-primary-and-secondary-data/select-intents-from-none-intent.png)

2. 在意向列表中选择 `SendMessage`。

    ![选择 SendMessage 意向](./media/luis-quickstart-primary-and-secondary-data/select-sendmessage-intent.png)

3. 在陈述 `Reply with I got your message, I will have the answer tomorrow` 中，选择消息正文的第一个单词 `I`，以及消息正文的最后一个单词 `tomorrow`。 系统会选择该消息的所有这些单词，并在顶部显示一个包含文本框的下拉菜单。

    [![](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png "在消息陈述中选择单词的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/select-words-in-utterance.png#lightbox)

4. 在文本框中输入实体名称 `Message`。

    [![](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png "在框中输入实体名称的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/enter-entity-name-in-box.png#lightbox)

5. 在下拉菜单中选择“创建新实体”。 该实体的用途是提取文本（消息的正文）。 在此 LUIS 应用中，文本消息位于陈述的末尾，但陈述和消息可以是任意长度。 

    [![](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png "从陈述创建新实体的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/create-message-entity.png#lightbox)

6. 在弹出窗口中，默认实体类型为“简单”，实体名称为 `Message`。 请保留这些设置，并选择“完成”。

    ![验证实体类型](./media/luis-quickstart-primary-and-secondary-data/entity-type.png)

7. 创建实体并标记一个陈述后，请标记包含该实体的其他陈述。 选择一个陈述，然后选择消息的第一个和最后一个单词。 在下拉菜单中，选择实体 `Message`。 现在，已在实体中标记该消息。 继续标记剩余陈述中的所有消息短语。

    [![](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png "已标记所有消息陈述的屏幕截图")](media/luis-quickstart-primary-and-secondary-data/all-labeled-utterances.png#lightbox)

    陈述的默认视图是“实体”视图。 选择陈述上方的“实体”视图控件。 “标记”视图显示陈述文本。 

    [![](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png "“标记”视图中的陈述屏幕截图")](media/luis-quickstart-primary-and-secondary-data/tokens-view-of-utterances.png#lightbox)

## <a name="train-the-luis-app"></a>训练 LUIS 应用
LUIS 在训练之前，并不知道意向和实体（模型）发生的变化。 

1. 在 LUIS 网站的右上方，选择“训练”按钮。

    ![选择训练按钮](./media/luis-quickstart-primary-and-secondary-data/train-button.png)

2. 当网站顶部出现确认成功的绿色状态栏时，表示训练已完成。

    ![训练成功通知](./media/luis-quickstart-primary-and-secondary-data/trained.png)

## <a name="publish-the-app-to-get-the-endpoint-url"></a>发布应用以获取终结点 URL
若要获取聊天机器人或其他应用程序中的 LUIS 预测，需要发布应用。 

1. 在 LUIS 网站的右上方，选择“发布”按钮。 

2. 选择“生产”槽和“发布”按钮。

    [![](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png "“发布”页的屏幕截图，其中已突出显示“发布到生产槽”按钮")](media/luis-quickstart-primary-and-secondary-data/publish-to-production.png#lightbox)

3. 当网站顶部出现确认成功的绿色状态栏时，表示发布已完成。

## <a name="query-the-endpoint-with-a-different-utterance"></a>使用不同的陈述查询终结点
在“发布”页的底部，选择“终结点”链接。 

[![](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png "“发布”页的屏幕截图，其中已突出显示终结点")](media/luis-quickstart-primary-and-secondary-data/publish-select-endpoint.png#lightbox)

此操作会打开另一个浏览器窗口，其地址栏中包含终结点 URL。 将光标定位到地址中 URL 的末尾，并输入 `text I'm driving and will be 30 minutes late to the meeting`。 最后一个查询字符串参数为 `q`，表示陈述查询 (**q**uery)。 此陈述不同于标记的任何陈述，因此，它非常适合用于测试，测试结果应返回 `SendMessage` 陈述。

```
{
  "query": "text I'm driving and will be 30 minutes late to the meeting",
  "topScoringIntent": {
    "intent": "SendMessage",
    "score": 0.987501
  },
  "intents": [
    {
      "intent": "SendMessage",
      "score": 0.987501
    },
    {
      "intent": "None",
      "score": 0.111048922
    }
  ],
  "entities": [
    {
      "entity": "i ' m driving and will be 30 minutes late to the meeting",
      "type": "Message",
      "startIndex": 5,
      "endIndex": 58,
      "score": 0.162995353
    }
  ]
}
```

## <a name="what-has-this-luis-app-accomplished"></a>此 LUIS 应用实现了哪些目的？
此应用只包含两个意向和一个实体，识别了自然语言查询意向，并返回了消息数据。 

JSON 结果中标识了评分最高的意向 `SendMessage`，其评分为 0.987501。 所有评分介于 1 和 0 之间，评分越接近 1 越好。 `None` 意向的评分为 0.111048922，非常接近 0。 

消息数据具有类型 `Message` 和值 `i ' m driving and will be 30 minutes late to the meeting`。 

现在，聊天机器人已获得足够的信息，可以确定主要操作 `SendMessage`、该操作的参数以及消息的文本。 

## <a name="where-is-this-luis-data-used"></a>在何处使用此 LUIS 数据？ 
LUIS 已完成此请求。 调用方应用程序（例如聊天机器人）可以提取 topScoringIntent 结果和实体中的数据，以通过第三方 API 发送消息。 如果机器人或调用方应用程序有其他编程选项，LUIS 不会执行相关的工作。 LUIS 只确定用户的意向是什么。 

## <a name="clean-up-resources"></a>清理资源
不再需要 LUIS 应用时，请将其删除。 为此，请在应用列表中选择应用名称右侧的省略号图标 (...)，然后选择“删除”。 在弹出的“删除应用?”对话框中，选择“确定”。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [了解如何添加分层实体](luis-quickstart-intent-and-hier-entity.md)


<!--References-->
[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
