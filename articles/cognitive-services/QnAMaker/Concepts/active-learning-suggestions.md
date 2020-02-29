---
title: 主动学习建议-QnA Maker
description: 利用活动的学习建议，你可以通过基于用户提交的问题和答案对来建议替代问题，从而提高知识库的质量。
ms.topic: conceptual
ms.date: 02/27/2020
ms.openlocfilehash: 56f3ab870e148c39912d4f1f5e6e7133a5df4a98
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921659"
---
# <a name="active-learning-suggestions"></a>活动学习建议

利用_活动的学习建议_功能，你可以通过基于用户提交的问题和答案对来建议替代问题，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。

知识库不会自动更改。 要使更改生效，必须接受建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="what-is-active-learning"></a>什么是活动学习？

QnA Maker 通过隐式和显式反馈学习新的问题变体。

* [隐式反馈](#how-qna-makers-implicit-feedback-works)–当用户问题有多个答案，其中包含非常接近的分数并将此视为反馈时，ranker 可以理解。 无需执行任何操作即可执行此操作。
* [明确反馈](#how-you-give-explicit-feedback-with-the-train-api)–当从知识库返回分数变小的多个答案时，客户端应用程序会询问用户哪个问题是正确的问题。 将用户的显式反馈发送到带有[训练 API](../How-to/improve-knowledge-base.md#train-api)QnA Maker。

这两种方法都为 ranker 提供了聚集的类似查询。

## <a name="how-active-learning-works"></a>主动学习的工作原理

活动学习是根据 QnA Maker 返回的前几个答案的分数来触发的。 如果与查询匹配的 QnA 集之间的分数差异在一个较小的范围内，则查询将被视为可能的每个 QnA 对的建议（作为替代问题）。 接受特定 QnA 对的建议问题后，其他对将拒绝该问题。 在接受建议后，需要记住保存和训练。

在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。 如果有5个或更多个类似查询被聚集，每30分钟 QnA Maker 建议向知识库设计器提供基于用户的问题，以接受或拒绝。 所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。

在 QnA Maker 门户中建议问题后，需要查看并接受或拒绝这些建议。 没有用于管理建议的 API。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA Maker 的隐式反馈如何工作

QnA Maker 的隐式反馈使用算法来确定接近的分数，并做出积极的学习建议。 用于确定置信度的算法并不是一个简单的计算。 以下示例中的范围并不是固定的，而应作为指导来了解算法的影响。

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。

在以下从查询到 QnA Maker generateAnswer 的 JSON 响应中，A、B 和 C 的分数接近，将被视为建议。

```json
{
  "activeLearningEnabled": true,
  "answers": [
    {
      "questions": [
        "Q1"
      ],
      "answer": "A1",
      "score": 80,
      "id": 15,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q2"
      ],
      "answer": "A2",
      "score": 78,
      "id": 16,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q3"
      ],
      "answer": "A3",
      "score": 75,
      "id": 17,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    },
    {
      "questions": [
        "Q4"
      ],
      "answer": "A4",
      "score": 50,
      "id": 18,
      "source": "Editorial",
      "metadata": [
        {
          "name": "topic",
          "value": "value"
        }
      ]
    }
  ]
}
```

QnA Maker 不知道哪个答案是最佳答案。 使用 QnA Maker 门户的建议列表选择最佳回答并再次训练。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何向训练 API 提供显式反馈

QnA Maker 需要有关哪种答案是最佳答案的明确反馈。 如何确定最好的答案取决于你，并可包括：

* 用户反馈，选择其中一个答案。
* 业务逻辑，例如确定可接受的分数范围。
* 用户反馈和业务逻辑的组合。

使用[训练 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train)将正确答案发送到 QnA Maker，并在用户选择它后发送。

## <a name="next-step"></a>后续步骤

> [!div class="nextstepaction"]
> [查询知识库](query-knowledge-base.md)