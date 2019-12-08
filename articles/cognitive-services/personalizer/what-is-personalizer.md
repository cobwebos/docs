---
title: 什么是个性化体验创建服务？
titleSuffix: Azure Cognitive Services
description: 个性化体验创建服务是基于云的 API 服务，可让你选择要显示给用户的最佳体验，并从其实时行为中学习信息。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: overview
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: b5d38ffeda3600fd90c4ee84acdd29ed599886ae
ms.sourcegitcommit: c69c8c5c783db26c19e885f10b94d77ad625d8b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/03/2019
ms.locfileid: "74707953"
---
# <a name="what-is-personalizer"></a>什么是个性化体验创建服务？

Azure 个性化体验创建服务是基于云的 API 服务，可让应用程序选择要显示给用户的最佳体验，并从其集体实时行为中进行学习。

* 提供有关用户和内容的信息，并接收要显示给用户的排名最高的操作。 
* 无需清理和标记数据即可使用个性化体验创建服务。
* 在方便的时候向个性化体验创建服务提供反馈。 
* 查看实时分析。 

观看[个性化体验创建服务工作方式](https://personalizercontentdemo.azurewebsites.net/)的演示

## <a name="how-does-personalizer-work"></a>个性化体验创建服务的工作原理

个性化体验创建服务使用机器学习模型来发现上下文中排名最高的操作。 客户端应用程序会提供可能操作的列表，其中包含有关这些操作的信息、有关上下文的信息，并且可能包含有关用户、设备等的信息。个性化体验创建服务会确定要执行的操作。 一旦客户端应用程序使用所选的操作，就会向个性化体验创建服务提供奖励评分形式的反馈。 收到反馈后，个性化体验创建服务会自动更新自身用于将来排名的模型。 随着时间的推移，个性化体验创建服务将训练一个模型，该模型可以根据其功能推荐要在每个上下文中选择的最佳操作。

## <a name="how-do-i-use-the-personalizer"></a>如何使用个性化体验创建服务？

![使用个性化体验创建服务来选择要向用户显示的视频](media/what-is-personalizer/personalizer-example-highlevel.png)

1. 在应用中选择要个性化的体验。
1. 在 Azure 门户中创建并配置个性化服务的实例。 每个实例都是一个个性化体验创建服务循环。
1. 结合有关用户的信息（特征）以及内容（操作）使用 [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) 调用个性化体验创建服务。   使用个性化体验创建服务之前，无需提供清理的带标签的数据。 可以直接调用 API，也可以使用适用于不同编程语言的 SDK。
1. 在客户端应用程序中，向用户显示个性化体验创建服务选择的操作。
1. 使用 [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) 向个性化体验创建服务提供反馈，指示用户是否选择了个性化体验创建服务的操作。 这是 _[奖励评分](concept-rewards.md)_ 。
1. 在 Azure 门户中查看分析，以评估系统的工作状态以及数据为个性化提供的帮助大小。

## <a name="where-can-i-use-personalizer"></a>可在哪种场合下使用个性化体验创建服务？

例如，客户端应用程序可以添加个性化体验创建服务来执行以下操作：

* 个性化要在新闻网站上突出显示的文章。    
* 优化网站上的广告位置。
* 在购物网站上显示个性化的“推荐商品”。
* 建议要应用到特定照片的用户界面元素（例如筛选器）。
* 选择用于澄清用户意向或建议操作的聊天机器人响应。
* 确定用户要在业务流程的下一步骤中优先处理的操作建议。

个性化体验创建服务不是用于保存和管理用户配置文件信息的服务，也不是记录单个用户的首选项或历史记录的服务。 个性化体验创建服务在单个模型的上下文操作中从每个交互的特征学习，从而在出现类似的特征时获得最大的奖励。 

## <a name="personalization-for-developers"></a>面向开发人员的个性化

个性化体验创建服务有两个 API：

* 排名  ：使用排名 API 来确定在当前上下文中要显示的具体操作。   操作以 JSON 对象数组的形式发送，包含每个对象的 ID 和信息（功能  ）；上下文以另一 JSON 对象的形式发送。 API 返回应该由应用程序呈现给用户的 actionId。
* *回馈*：在用户与应用程序交互以后，即可以 0 到 1 之间的某个数字来度量个性化的效果，并将其作为[奖励评分](concept-rewards.md)发送。 

![个性化事件的基本顺序](media/what-is-personalizer/personalization-intro.png)

## <a name="next-steps"></a>后续步骤

* [个性化体验创建服务的新增功能](whats-new.md)
* [个性化体验创建服务工作原理](how-personalizer-works.md)
* [什么是强化学习？](concepts-reinforcement-learning.md)
* [了解排名请求的功能和操作](concepts-features.md)
* [了解如何确定奖励请求的分数](concept-rewards.md)
* [使用交互式演示](https://personalizationdemo.azurewebsites.net/)
