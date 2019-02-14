---
title: 改进知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: ''
author: diberry
manager: cgronlun
displayName: active learning, suggestion, dialog prompt, train api, feedback loop, autolearn, auto-learn, user setting, service setting, services setting
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/29/2019
ms.author: diberry
ms.openlocfilehash: 639b665926f54387dfdc6e837c15c8d6d28df925
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55755758"
---
# <a name="use-active-learning-to-improve-knowledge-base"></a>使用主动学习改进知识库

通过主动学习，可以根据用户提交的内容为问题和答案对提供替代问题建议，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。 

知识库不会自动更改。 必须先接受建议才能使更改生效。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="active-learning"></a>主动学习

QnA Maker 通过隐式和显式反馈学习新的问题变体。
 
* 隐式反馈 - 排名程序了解到用户问题具有多个答案且分数非常接近时，将其视为反馈。 
* 显式反馈 - 从知识库返回分数变化很小的多个答案时，客户端应用程序会询问用户哪个问题是正确的问题。 使用训练 API 将用户的显式反馈发送给 QnA Maker。 

这两种方法都为排名程序提供了类似的集中查询。

集中类似的查询时，QnA Maker 会向知识库设计者建议基于用户的问题用于接受或拒绝。

## <a name="how-active-learning-works"></a>主动学习的工作原理

根据 QnA Maker 针对任何给定查询返回的数量最少的答案分数来触发主动学习。 如果分数差范围很小，则对于每个可能的答案，将查询视为可能的建议。 

所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。 在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。

## <a name="upgrade-version-to-use-active-learning"></a>升级版本以使用主动学习

运行时版本 4.4.0 及更高版本支持主动学习。 如果你的知识库是在早期版本上创建的，请[升级运行时](troubleshooting-runtime.md#how-to-get-latest-qnamaker-runtime-updates)以使用此功能。 

## <a name="best-practices"></a>最佳做法

有关使用主动学习的最佳做法，请参阅[最佳做法](../Concepts/best-practices.md#active-learning)。

## <a name="score-proximity-between-knowledge-base-questions"></a>知识库问题之间的分数接近

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。 

用于确定置信度的算法并不是一个简单的计算。 前面示例中的范围并不是固定的，仅用作了解算法影响的参考。

## <a name="turn-on-active-learning"></a>启用主动学习

默认情况下，主动学习处于关闭状态。 启用它可以查看建议的问题。 

1. 若要打开主动学习，请单击“名称”，转到 QnA Maker 门户右上角的“[服务设置](https://www.qnamaker.ai/UserSettings)”。  

    ![在“服务设置”页上，启用“主动学习”](../media/improve-knowledge-base/Endpoint-Keys.png)


1. 找到 QnA Maker 服务，然后切换到“主动学习”。 

    [![在“服务设置”页上，启用“主动学习”](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)

    启用“主动学习”后，知识库会根据用户提交的问题定期建议新的问题。 可以通过再次切换设置来禁用“主动学习”。

## <a name="add-active-learning-suggestion-to-knowledge-base"></a>向知识库添加主动学习建议

1. 要查看建议的问题，请在“编辑”知识库页上，选择“显示建议”。 

    [![在“服务设置”页上，切换“显示建议”按钮](../media/improve-knowledge-base/show-suggestions-button.png)](../media/improve-knowledge-base/show-suggestions-button.png#lightbox)

1. 通过选择“按建议筛选”，将包含问题和答案对的知识库筛选为仅显示建议。

    [![在“服务设置”页上，按建议筛选以仅查看这些问题/答案对](../media/improve-knowledge-base/filter-by-suggestions.png)](../media/improve-knowledge-base/filter-by-suggestions.png#lightbox)

1.  每个包含建议的问题部分都会显示带有复选标记的新问题，`✔` 表示接受问题，`x` 表示拒绝建议。 选中复选标记可以添加问题。 

    [![在“服务设置”页上，启用“主动学习”](../media/improve-knowledge-base/accept-active-learning-suggestions.png)](../media/improve-knowledge-base/accept-active-learning-suggestions.png#lightbox)

    可以通过选择“全部添加”或“全部拒绝”来添加或删除“所有建议”。

1. 选择“保存并训练”，将所做的更改保存到知识库。


## <a name="determine-best-choice-when-several-questions-have-similar-scores"></a>当几个问题具有相似的分数时，确定最佳选择

当某个问题的分数与其他问题的分数过于接近时，客户端应用程序开发人员可以选择要求进行说明。

### <a name="use-the-top-property-in-the-generateanswer-request"></a>在 GenerateAnswer 请求中使用 top 属性

在向 QnA Maker 提交问题以获得答案时，JSON 主体的一部分允许返回多个最佳答案：

```json
{
    "question": "wi-fi",
    "isTest": false,
    "top": 3
}
```

当客户端应用程序（例如聊天机器人）收到响应时，返回排名靠前的 3 个问题：

```json
{
    "answers": [
        {
            "questions": [
                "Wi-Fi Direct Status Indicator"
            ],
            "answer": "**Wi-Fi Direct Status Indicator**\n\nStatus bar icons indicate your current Wi-Fi Direct connection status:  \n\nWhen your device is connected to another device using Wi-Fi Direct, '$  \n\n+ •+ ' Wi-Fi Direct is displayed in the Status bar.",
            "score": 74.21,
            "id": 607,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Wi-Fi - Connections"
            ],
            "answer": "**Wi-Fi**\n\nWi-Fi is a term used for certain types of Wireless Local Area Networks (WLAN). Wi-Fi communication requires access to a wireless Access Point (AP).",
            "score": 74.15,
            "id": 599,
            "source": "Bugbash KB.pdf",
            "metadata": []
        },
        {
            "questions": [
                "Turn Wi-Fi On or Off"
            ],
            "answer": "**Turn Wi-Fi On or Off**\n\nTurning Wi-Fi on makes your device able to discover and connect to compatible in-range wireless APs.  \n\n1.  From a Home screen, tap ::: Apps > e Settings .\n2.  Tap Connections > Wi-Fi , and then tap On/Off to turn Wi-Fi on or off.",
            "score": 69.99,
            "id": 600,
            "source": "Bugbash KB.pdf",
            "metadata": []
        }
    ]
}
```

### <a name="client-application-follow-up-when-questions-have-similar-scores"></a>在问题具有相似的分数时的客户应用程序跟进

客户端应用程序显示所有问题，并且可以让用户选择最能代表其意图的问题。 

用户选择一个现有问题后， 用户反馈会发送到 QnA Maker 的[训练](http://www.aka.ms/activelearningsamplebot) API，用于继续执行主动学习反馈循环。 

```http
POST https://<QnA-Maker-resource-name>.azurewebsites.net/qnamaker/knowledgebases/<knowledge-base-ID>/train
Authorization: EndpointKey <endpoint-key>
Content-Type: application/json
{"feedbackRecords": [{"userId": "1","userQuestion": "<question-text>","qnaId": 1}]}
```

通过 [Azure 机器人 C# 示例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-activelearning-bot)详细了解如何使用主动学习

## <a name="next-steps"></a>后续步骤
 
> [!div class="nextstepaction"]
> [使用 QnA Maker API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff)
