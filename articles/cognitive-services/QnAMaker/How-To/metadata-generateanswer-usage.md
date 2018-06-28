---
title: 配合使用知识库中的元数据与 GenerateAnswer API | Microsoft Docs
description: 将元数据与 GenerateAnswer API 配合使用
services: cognitive-services
author: pchoudhari
manager: rsrikan
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/18/2018
ms.author: pchoudh
ms.openlocfilehash: 94e3632884d7033971ff1c45b455afb9a09ee798
ms.sourcegitcommit: c722760331294bc8532f8ddc01ed5aa8b9778dec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/04/2018
ms.locfileid: "35366938"
---
# <a name="using-metadata-and-the-generateanswer-api"></a>配合使用元数据与 GenerateAnswer API

通过 QnA Maker，可将键值对形式的元数据添加到问/答集。 可通过各种方式使用此信息，例如筛选用户查询的结果、增强特定结果以及存储可用于后续对话的其他信息。 有关详细信息，请参阅[知识库](../Concepts/knowledge-base.md)。

## <a name="qna-entity"></a>QnA 实体

首先，理解 QnA Maker 存储问/答数据的方式是非常重要的。 下图显示了一个 QnA 实体：

![QnA 实体](../media/qnamaker-how-to-metadata-usage/qna-entity.png)

每个 QnA 实体都有一个唯一的永久 ID。 ID 可用于对特定 QnA 实体进行更新。

## <a name="generateanswer-api"></a>GenerateAnswer API

使用聊天机器人或应用程序中的 GenerateAnswer API，能以某个用户问题对知识库进行查询，从问/答集中获取匹配度最高的结果。

### <a name="generateanswer-endpoint"></a>GenerateAnswer 终结点

发布知识库后，从 [QnA Maker 门户](https://www.qnamaker.ai)或使用 [API](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) 都可以获取 GenerateAnswer 终结点的详细信息。

获取终结点详细信息：
1. 登录至 [https://www.qnamaker.ai](https://www.qnamaker.ai)。
2. 在“我的知识库”中，单击知识库的“查看代码”。
![我的知识库](../media/qnamaker-how-to-metadata-usage/my-knowledge-bases.png)
3. 获取 GenerateAnswer 终结点的详细信息。

![终结点详细信息](../media/qnamaker-how-to-metadata-usage/view-code.png)

还可以在知识库的“设置”选项卡中获取终结点详细信息。

### <a name="generateanswer-request"></a>GenerateAnswer 请求

通过 HTTP POST 请求调用 GenerateAnswer。 有关演示如何调用 GenerateAnswer 的示例代码，请参阅[快速入门](../quickstarts/csharp.md)。

- **请求 URL**：https://{QnA Maker endpoint}/knowledgebases/{knowledge base ID}/generateAnswer

- **请求参数**： 
    - **Knowledge base ID**（字符串）：知识库的 GUID。
    - **QnAMaker endpoint**（字符串）：部署在 Azure 订阅中的终结点的主机名。
- **请求标头**
    - **Content-Type**（字符串）：发送到 API 的正文媒体类型。
    - **Authorization**（字符串）：终结点密钥。
- **请求正文**
    - **question**（字符串）：要针对知识库查询的用户问题。
    - **top**（可选，整数）：要包含在输出中的排序结果数。 默认值为 1。
    - **userId**（可选，字符串）：用于标识用户的唯一 ID。 此 ID 将记录在聊天日志中。
    - **strictFilters**（可选，字符串）：若指定此参数，将指示 QnA Maker 仅返回含有指定元数据的答案。 有关详细信息，请参阅以下内容。
    ```json
    {
        "question": "qna maker and luis",
        "top": 6,
        "strictFilters": [
        {
            "name": "category",
            "value": "api"
        }],
        "userId": "sd53lsY="
    }
    ```

### <a name="generateanswer-response"></a>GenerateAnswer 响应

- **响应 200** - 成功调用返回问题结果。 响应包含以下字段：
    - **answers** - 用户查询的答案列表，按分数排名的递减顺序排列。
        - **score**：排名分数，介于 0 到 100 之间。
        - **questions**：用户提供的问题。
        - **source**：从中提取答案或将其存储在知识库中的源名称。
        - **metadata**：与答案关联的元数据。
            - name：元数据名称。 （字符串，最大长度：100，必填）
            - value：元数据值。 （字符串，最大长度：100，必填）
        - **Id**：分配给答案的唯一 ID。
    ```json
    {
        "answers": [
            {
                "score": 28.54820341616869,
                "Id": 20,
                "answer": "There is no direct integration of LUIS with QnA Maker. But, in your bot code, you can use LUIS and QnA Maker together. [View a sample bot](https://github.com/Microsoft/BotBuilder-CognitiveServices/tree/master/Node/samples/QnAMaker/QnAWithLUIS)",
                "source": "Custom Editorial",
                "questions": [
                    "How can I integrate LUIS with QnA Maker?"
                ],
                "metadata": [
                    {
                        "name": "category",
                        "value": "api"
                    }
                ]
            }
        ]
    }
    ```

## <a name="metadata-example"></a>元数据示例

请思考以下位于 Hyderabad 的餐馆常见问题解答数据。 单击齿轮图标，将元数据添加到知识库。

![添加元数据](../media/qnamaker-how-to-metadata-usage/add-metadata.png)

### <a name="filter-results-with-strictfilters"></a>使用 strictFilters 来筛选结果

请思考此用户问题 -“此酒店什么时候打烊？” 这里隐含的意向是“Paradise”餐馆。

由于只需要关于餐馆“Paradise”的结果，因此可以在 GenerateAnswer 调用中对元数据“餐馆名称”设置一个筛选器，如下所示。

```json
{
    "question": "When does this hotel close?",
    "top": 1,
    "strictFilters": [
      {
        "name": "restaurant",
        "value": "paradise"
      }]
}
```

### <a name="keep-context"></a>保留上下文
对 GenerateAnswer 的响应包含匹配的问/答集相应的元数据信息，如下所示。

```json
{
    "answers": [
        {
            "questions": [
                "What is the closing time?"
            ],
            "answer": "10.30 PM",
            "score": 100,
            "id": 1,
            "source": "Editorial",
            "metadata": [
                {
                    "name": "restaurant",
                    "value": "paradise"
                },
                {
                    "name": "location",
                    "value": "secunderabad"
                }
            ]
        }
    ]
}
```

此信息可用于记录之前对话的上下文，供后续对话使用。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](./create-knowledge-base.md)
