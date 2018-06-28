---
title: 了解 QnA Maker - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 使用 QnA Maker，可以根据常见问题解答文档或者 URL 和产品手册等半结构化内容打造一项问题与解答服务。
services: cognitive-services
author: noellelacharite
manager: nolachar
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: overview
ms.date: 06/15/2018
ms.author: nolachar
ms.openlocfilehash: f34cec047c18a6db10b5adda82800c51d44c1155
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295139"
---
# <a name="what-is-qna-maker"></a>什么是 QnA Maker？

使用 [QnA Maker](https://qnamaker.ai)，可以根据 FAQ（常见问题解答）文档或者 URL 和产品手册等半结构化内容打造一项问题与解答服务。 可以生成一个问题与解答模型，以便灵活地应对用户查询，提供可供以自然的对话方式受训的机器人使用的响应。

图形用户界面简单易用，即使没有任何开发经验，也可以创建、管理、训练、启动并运行服务。

![概述](../media/qnamaker-overview-learnabout/overview.png)

## <a name="highlights"></a>亮点

- 完全**不需要代码编写**经验即可[创建常见问题解答机器人](https://aka.ms/qnamaker-docs-create-faqbot)。
- **再也不会有网络限制**。 只需支付服务托管费用，不需支付事务费用。 如需更多详细信息，请参阅[定价页](https://aka.ms/qnamaker-docs-pricing)。
- **按需缩放**。 选择与方案匹配的单个组件的适当 SKU。 了解如何为 QnA Maker 服务[选择容量](https://aka.ms/qnamaker-docs-capacity)。
- **完全符合数据要求**。 数据和运行时组件部署在用户的 Azure 订阅中，处于符合性边界内。 请阅读下面的内容，了解更多详细信息。

## <a name="key-qna-maker-processes"></a>重要的 QnA Maker 过程

QnA Maker 提供两项针对数据的重要服务：

* **提取**：结构化问题-解答数据从常见问题解答和产品手册等半结构化数据源提取。 此提取操作在创建知识库时完成。 在[此处](https://aka.ms/qnamaker-docs-createkb)创建知识库。

* **匹配**：[训练并测试](https://aka.ms/qnamaker-docs-trainkb)知识库以后，即可将其[发布](https://aka.ms/qnamaker-docs-publishkb)。 这样就会为 QnA Maker 知识库启用一个终结点，然后即可将其用在机器人或应用中。 此终结点接受用户问题，然后使用知识库中的最佳问题/解答匹配进行响应，并提供该匹配的置信度分数。

## <a name="qna-maker-architecture"></a>QnA Maker 体系结构

QnA Maker 堆栈包含以下部件：

1. **QnA Maker 管理服务（控制平面）**：QnA Maker 知识库的管理体验，包括初始创建、更新、训练和发布。 这些活动可以通过[门户](https://qnamaker.ai)或[管理 API](https://aka.ms/qnamaker-v4-apis) 来完成。 这些管理服务与下面的运行时组件通信。

2. **QnA Maker 运行时（数据平面）**：数据和运行时部署在用户所选区域的 Azure 订阅中。 客户的问题/解答内容存储在 [Azure 搜索](https://azure.microsoft.com/services/search/)中，运行时则部署为[应用服务](https://azure.microsoft.com/services/app-service/)。 也可选择部署用于分析的 [Application Insights](https://azure.microsoft.com/services/application-insights/) 资源。

![体系结构](../media/qnamaker-overview-learnabout/architecture.png)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建 QnA Maker 服务](../how-to/set-up-qnamaker-service-azure.md)
