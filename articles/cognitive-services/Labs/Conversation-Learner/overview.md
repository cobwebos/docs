---
title: 什么是对话学习器？ - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 了解对话学习器及其工作原理。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 9cbf0e60382ef17d68aab47cf5f24ea9b8434f13
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366264"
---
# <a name="what-is-conversation-learner"></a>什么是对话学习器？

对话学习器使你能够构建和教授从示例交互中学习的对话接口。 

与传统方法不同，对话学习器会考虑对话的端到端上下文来改进响应并提供更具吸引力的用户体验。 对话学习器涉及一组广泛的面向任务的用例，在后台应用机器学习，使机器人和智能代理不太可能阻挠用户、增加客户服务成本，并更直观地与之交互。

首先，开发人员输入他们想要模拟的原型对话。 随着输入更多的对话，模型不断进行更新，开发人员可以看到模型通用化到什么程度。 模型运行良好后，就可以将机器人部署到最终用户。 对话学习器记录与用户的对话，开发人员可以查看这些对话。 如果发现错误，开发人员可以进行现场更正，并对模型进行重新训练，模型立即可供使用。

这种方法会减少对话控制逻辑的手动编码，并使企业所有者或领域专家能够在没有先前机器学习知识的情况下促成对话接口。 无论是作为机器人、智能设备还是智能代理的一部分进行部署，对话学习器都可以快速迭代新技能、行为或能力，并快速提高其质量。 

对话学习器使开发人员能够加快产品上市速度，并通过 Microsoft Bot Framework 或仅仅使用其自己的基础结构跨多个对话渠道推动成功对话。

摘要和要点：

- 对话学习器是构建面向任务型机器人的一种 AI 优先方式。

- 它依赖于端到端循环神经网络 (LSTM)，并直接从多轮次的对话示例中学习。 

- 使设计人员、开发人员、业务用户和呼叫中心工作人员能够构建和维护机器人。 

- 提供在代码中表达业务规则和常识的能力。

- 在教学过程中，神经网络模型用于在对话中对下一组预期操作进行评分。 然后，机器人开发人员可以选择正确的操作，并训练网络以提供正确的响应。
 
- 训练完成后，开发人员可以使用用户交互中的记录对话来修正机器人响应并重新训练模型。 

- 可以调用领域特定的 API 和第三方 API 来完成任务。

