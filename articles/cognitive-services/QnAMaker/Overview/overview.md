---
title: 什么是 QnA Maker？
titleSuffix: Azure Cognitive Services
description: QnA Maker 是一种基于云的 API 服务，它针对用户的自然语言问题应用自定义的机器学习智能来提供最佳解答。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: overview
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 1fa15c4420cb1e42fcaa34c56a1b0aef9c4102b1
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55872452"
---
# <a name="what-is-qna-maker"></a>什么是 QnA Maker？

QnA Maker 是一种基于云的 API 服务，它创建基于数据的聊天式问答层。 

使用 QnA Maker，你可以借助半结构化内容（例如常见问题解答 (FAQ)）URL、产品手册、支持文档以及自定义的问题和解答来创建知识库 (KB)。 QnA Maker 服务可以回答用户的自然语言问题，其方法是将问题与知识库的 QnA 中的最可能答案进行匹配。

通过易于使用的 [Web 门户](https://qnamaker.ai)，没有开发者经验的人也可以创建、管理、训练和发布其服务。 将服务发布到终结点后，诸如聊天机器人之类的客户端应用程序可以管理与用户的对话，从而获取问题并以答案进行响应。 

![概述](../media/qnamaker-overview-learnabout/overview.png)

## <a name="key-qna-maker-processes"></a>重要的 QnA Maker 过程

QnA Maker 提供两项针对数据的重要服务：

* **提取**：结构化问题和解答数据是从常见问题解答和产品手册等结构化和半结构化[数据源](../Concepts/data-sources-supported.md)提取的。 此提取可以在知识库[创建](https://aka.ms/qnamaker-docs-createkb)过程中进行，也可以在以后的编辑过程中进行。

* **匹配**：[训练并测试](https://aka.ms/qnamaker-docs-trainkb)知识库以后，即可将其[发布](https://aka.ms/qnamaker-docs-publishkb)。 这样就会为 QnA Maker 知识库启用一个终结点，然后可以将此终结点用于机器人或客户端应用中。 此终结点接受用户问题，然后使用知识库中的最佳解答进行响应，并提供该匹配的置信度分数。

```JSON
{
    "answers": [
        {
            "questions": [
                "How do I share a knowledge base with other?"
            ],
            "answer": "Sharing works at the level of a QnA Maker service, i.e. all knowledge bases in the services will be shared. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/how-to/collaborate-knowledge-base)how to collaborate on a knowledge base.",
            "score": 70.95,
            "id": 4,
            "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs",
            "metadata": []
        }
    ]
}

```

## <a name="qna-maker-architecture"></a>QnA Maker 体系结构

QnA Maker 体系结构包括下述两个组件：

1. **QnA Maker 管理服务**：QnA Maker 知识库的管理体验，包括初始创建、更新、训练和发布。 这些活动可以通过[门户](https://qnamaker.ai)或[管理 API](https://aka.ms/qnamaker-v4-apis) 来完成。 

2. **QnA Maker 数据和运行时**：这是在指定区域的 Azure 订阅中部署的。 KB 内容存储在 [Azure 搜索](https://azure.microsoft.com/services/search/)中，终结点则部署为[应用服务](https://azure.microsoft.com/services/app-service/)。 还可以选择部署用于分析的 [Application Insights](https://azure.microsoft.com/services/application-insights/) 资源。

![体系结构](../media/qnamaker-overview-learnabout/architecture.png)


## <a name="service-highlights"></a>服务亮点

- 完全**不需要代码编写**经验即可[创建常见问题解答机器人](https://aka.ms/qnamaker-docs-create-faqbot)。
- **对于预测没有网络限制**。 只需支付服务托管费用，不需支付事务费用。 如需更多详细信息，请参阅[定价页](https://aka.ms/qnamaker-docs-pricing)。
- **可以根据需要进行缩放**。 选择与方案匹配的单个组件的适当 SKU。 了解如何为 QnA Maker 服务[选择容量](https://aka.ms/qnamaker-docs-capacity)。
- **完全符合数据要求**。 预测服务组件部署在 Azure 订阅中，处于符合性边界内。


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建 QnA Maker 服务](../how-to/set-up-qnamaker-service-azure.md)
