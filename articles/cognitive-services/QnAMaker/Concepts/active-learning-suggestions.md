---
title: 主动学习建议 - QnA 制造商
description: 主动学习建议允许您根据用户提交向问答对推荐其他问题，从而提高知识库的质量。
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: edbe06b12fbb97473b28ccca968fd3e7d8366152
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804203"
---
# <a name="active-learning-suggestions"></a>主动学习建议

_"主动学习建议"_ 功能允许您根据用户提交向问答对推荐其他问题，从而提高知识库的质量。 查看这些建议后，可以将其添加到现有问题，也可以拒绝它们。

知识库不会自动更改。 为了使任何更改生效，您必须接受这些建议。 这些建议会添加问题，但不会更改或删除现有问题。

## <a name="what-is-active-learning"></a>什么是主动学习？

QnA Maker 通过隐式和显式反馈学习新的问题变体。

* [隐式反馈](#how-qna-makers-implicit-feedback-works)– 当用户问题有多个答案且分数非常接近，并将其视为反馈时，排名者会理解。 你不需要做任何事情来做到这一点。
* [明确反馈](#how-you-give-explicit-feedback-with-the-train-api)– 当从知识库中返回分数变化不大的多个答案时，客户端应用程序会询问用户哪个问题是正确的问题。 用户的显式反馈将随[训练 API](../How-to/improve-knowledge-base.md#train-api)发送给 QnA 制造商。

这两种方法都为排名者提供群集的类似查询。

## <a name="how-active-learning-works"></a>主动学习的工作原理

活动学习根据 QnA Maker 返回的前几个答案的分数触发。 如果匹配查询的 QnA 对之间的分数差异位于一个小范围内，则查询将被视为每个可能的 QnA 对的可能建议（作为备用问题）。 接受特定 QnA 对的建议问题后，其他对将拒绝该问题。 在接受建议后，您需要记住保存和训练。

在终结点获得合理数量和类型的使用查询的情况下，主动学习可提供最佳建议。 当 5 个或更多类似的查询被群集时，每 30 分钟一次，QnA Maker 会向知识库设计人员建议基于用户的问题接受或拒绝。 所有建议通过相似度聚集在一起，并且根据最终用户的特定查询频率显示替代问题排名靠前的建议。

在 QnA Maker 门户中提出问题后，您需要查看并接受或拒绝这些建议。 没有用于管理建议的 API。

## <a name="turn-on-active-learning"></a>启用主动学习

默认情况下，活动学习**处于关闭状态**。
要使用主动学习：
* 您需要[打开主动学习](../How-To/use-active-learning.md#turn-on-active-learning-for-alternate-questions)，以便 QnA Maker 为您的知识库收集备用问题。
* 要查看建议的备用问题，请使用"编辑"页上[的"查看"选项](../How-To/improve-knowledge-base.md#view-suggested-questions)。

## <a name="how-qna-makers-implicit-feedback-works"></a>QnA 制造商的隐式反馈的工作原理

QnA Maker 的隐式反馈使用算法来确定分数接近程度，然后提出主动学习建议。 用于确定置信度的算法并不是一个简单的计算。 以下示例中的范围不是要固定的，但应用作仅了解算法影响的指南。

当问题的分数置信度很高（例如 80%）时，考虑进行主动学习的分数范围较广，大约在 10% 以内。 随着置信度分数降低（例如 40%），分数范围也会降低，大约在 4% 以内。

在以下 JSON 响应中，从查询到 QnA Maker 的生成应答，A、B 和 C 的分数接近，将被视为建议。

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

QnA 制造商不知道哪个答案是最好的答案。 使用 QnA Maker 门户的建议列表选择最佳答案并再次训练。


## <a name="how-you-give-explicit-feedback-with-the-train-api"></a>如何使用培训 API 提供显式反馈

QnA Maker 需要明确反馈，了解哪些答案是最佳答案。 如何确定最佳答案取决于您，可能包括：

* 用户反馈，选择其中一个答案。
* 业务逻辑，例如确定可接受的分数范围。
* 用户反馈和业务逻辑的组合。

使用[训练 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/train)在用户选择后向 QnA 制造商发送正确的答案。

## <a name="next-step"></a>后续步骤

> [!div class="nextstepaction"]
> [查询知识库](query-knowledge-base.md)