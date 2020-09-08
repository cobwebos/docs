---
title: 什么是个性化体验创建服务？
description: 个性化体验创建服务是基于云的服务，可让你选择要显示给用户的最佳体验，并从其实时行为中学习信息。
ms.topic: overview
ms.date: 08/27/2020
ms.custom: cog-serv-seo-aug-2020
keywords: personalizer, Azure personalizer, machine learning
ms.openlocfilehash: 93b1f0487818cf783cc4a19f79618cfe2285cbd1
ms.sourcegitcommit: 420c30c760caf5742ba2e71f18cfd7649d1ead8a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2020
ms.locfileid: "89055611"
---
# <a name="what-is-personalizer"></a>什么是个性化体验创建服务？

Azure 个性化体验创建服务是基于云的服务，有助于应用程序选择最佳内容项来向用户显示。 可以使用个性化体验创建服务来确定向购物者推荐的产品或了解广告的最佳位置。 向用户显示内容后，系统会监视实时用户行为，并将奖励评分报告回个性化体验创建服务。 这确保了机器学习模型的持续改进，个性化体验创建服务能够根据收到的上下文信息选择最佳内容项。

> [!TIP]
> 内容是任何信息单元，如文本、图像、URL、电子邮件或你希望从中选择并向用户显示的任何其他内容。

在开始之前，请随意试用[个性化体验创建服务交互式演示](https://personalizationdemo.azurewebsites.net/)。

<!--
![What is personalizer animation](./media/what-is-personalizer.gif)
-->

## <a name="how-does-personalizer-select-the-best-content-item"></a>个性化体验创建服务如何选择最佳内容项目？

个性化体验创建服务使用**强化学习**根据所有用户的集体行为和奖励评分来选择最佳项目（操作）。 操作是内容项，例如新闻文章、特定电影或产品。

**排名**调用可采用操作项以及操作的特征和上下文特征来选择顶级操作项：

* **包含特征的操作** - 其特征特定于每个项的内容项
* **上下文特征** - 使用应用时用户的特征、其上下文或环境

排名调用在“奖励操作 ID”字段中返回要向用户显示的具体内容项（操作）的 ID。

向用户显示的操作与机器学习模型一起选择，目的是尝试将一段时间内的总奖励量最大化。

### <a name="sample-scenarios"></a>示例方案

让我们看一下可使用个性化体验创建服务来选择要为用户呈现的最佳内容的几种方案。

|内容类型|操作（包含特征）|上下文特征|返回的奖励操作 ID<br>（显示此内容）|
|--|--|--|--|
|新闻列表|a. `The president...`（国家/地区、政治、[文本]）<br>b. `Premier League ...`（全球、体育、[文本、图像、视频]）<br> c. `Hurricane in the ...`（区域、天气、[文本、图像]|从中读取设备新闻<br>月或季<br>|a `The president...`|
|电影列表|1.`Star Wars`（1977，[动作、冒险、奇幻]，George Lucas）<br>2.`Hoop Dreams`（1994，[记录、体育]，Steve James<br>3.`Casablanca`（1942，[浪漫、剧情、战争]，Michael Curtiz）|从中观看设备电影<br>屏幕大小<br>用户类型<br>|3. `Casablanca`|
|产品列表|i. `Product A`（3 公斤，$$$$，在 24 小时内交付）<br>ii. `Product B`（20 公斤，$$，通过海关运输，运输时间为 2 周）<br>iii. `Product C`（3 公斤，$$$，在 48 小时内交付）|从中读取设备购买信息<br>用户的支出层<br>月或季|ii. `Product B`|

个性化体验创建服务使用了强化学习选择单个最佳操作（称为“奖励操作 ID”）。 机器学习模型使用： 

* 已训练的模型 - 之前从个性化服务接收的用于改进机器学习模型的信息
* 当前数据 - 包含特征和上下文特征的特定操作

## <a name="when-to-use-personalizer"></a>使用个性化体验创建服务的时间

每当应用程序显示内容时，都会调用个性化体验创建服务的排名 [API](https://go.microsoft.com/fwlink/?linkid=2092082)。 这称为“事件”，使用“事件 ID”来标注。

个性化体验创建服务的**奖励** [API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) 可以实时调用，也可以延迟，以便更好地适应基础结构。 根据业务需要确定奖励分数。 奖励分介于 0 到 1 之间。 该分数可以是单个值（例如，1 表示“好”，0 表示“差”），也可以是你根据业务目标和指标创建的算法所生成的某个数字。

## <a name="content-requirements"></a>内容要求

当内容符合以下条件时，请使用个性化体验创建服务：

* 可供选择的项数有限（最多为 50 左右）。 如果列表较大，请[使用建议引擎](where-can-you-use-personalizer.md#how-to-use-personalizer-with-a-recommendation-solution)将列表缩小到 50 项。
* 提供的信息描述了要排名的内容：包含特征的操作和上下文特征 。
* 至少每天有大约 1000 个与内容相关的事件，否则个性化体验创建服务无法正常运行。 如果个性化体验创建服务未收到所需的最小流量，则该服务需要较长时间来确定单个最佳内容项。

由于个性化体验创建服务以近实时方式根据集合信息来返回单个最佳内容项目，因此该服务不会执行以下操作：
* 保留和管理用户配置文件信息
* 记录单个用户的首选项或历史记录
* 要求已清理并标记的内容

## <a name="how-to-design-for-and-implement-personalizer"></a>如何针对个性化体验创建服务进行设计和实现

1. [设计](concepts-features.md)和计划内容、操作和上下文 。 确定奖励分数的奖励算法。
1. 系统将你创建的每个[个性化体验创建服务资源](how-to-settings.md)视为一个学习循环。 该循环会收到针对该内容或用户体验的排名和奖励调用。

    |资源类型| 目的|
    |--|--|
    |[学徒模式](concept-apprentice-mode.md) `E0`|在不影响现有应用程序的情况下训练个性化体验创建服务模型，然后将在线学习行为部署到生产环境|
    |标准，`S0`|生产环境中的联机学习行为|
    |免费，`F0`| 在非生产环境中尝试联机学习行为|

1. 将个性化体验创建服务添加到应用程序、网站或系统：
    1. 在应用程序、网站或系统中添加对个性化体验创建服务的**排名**调用，这样就可以在向用户显示内容之前确定最佳的单一内容项。
    1. 显示最佳的单一内容项，它是为用户返回的奖励操作 ID。
    1. 对收集的有关用户行为的信息应用业务逻辑，确定奖励分数，例如：

    |行为|计得的奖励分数|
    |--|--|
    |用户选择了最佳单一内容项（奖励操作 ID）|**1**|
    |用户选择了其他内容|**0**|
    |用户在选择最佳单一内容项（奖励操作 ID）之前犹豫不决|**0.5**|

    1. 添加一个**奖励**调用，发送的奖励分数在 0 到 1 之间
        * 在显示内容后立即这样做
        * 或者稍后在脱机系统中这样做
    1. 使用一段时间后，使用脱机评估来[评估循环](concepts-offline-evaluation.md)。 使用脱机评估，可以在不更改代码或影响用户体验的情况下测试和评估个性化体验创建服务的有效性。

## <a name="complete-a-quickstart"></a>完成快速入门

我们提供了适用于 C#、JavaScript 和 Python 的快速入门。 每个快速入门旨在让你了解基本设计模式并帮助你在 10 分钟以内运行代码。 

* [快速入门：如何使用个性化体验创建服务客户端库](sdk-learning-loop.md)

有机会开始使用个性化体验创建服务后，请尝试我们的教程，了解如何在 Web 应用程序、聊天机器人或 Azure Notebook 中使用个性化体验创建服务。

* [教程：在 .NET Web 应用中使用个性化体验创建服务](tutorial-use-personalizer-web-app.md)
* [教程：在 .NET 聊天机器人中使用个性化体验创建服务](tutorial-use-personalizer-chat-bot.md)
* [教程：在 Azure Notebook 中使用个性化体验创建服务](tutorial-use-azure-notebook-generate-loop-data.md)

## <a name="reference"></a>参考 

* [个性化体验创建服务 C#/.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/personalizer?view=azure-dotnet)
* [个性化体验创建服务 Go SDK](https://github.com/Azure/azure-sdk-for-go/tree/master/services/preview/personalizer/v1.0/personalizer)
* [个性化体验创建服务 JavaScript SDK](https://docs.microsoft.com/javascript/api/@azure/cognitiveservices-personalizer/?view=azure-node-latest)
* [个性化体验创建服务 Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/personalizer?view=azure-python)
* [REST API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [个性化体验创建服务的工作原理](how-personalizer-works.md)
> [什么是强化学习？](concepts-reinforcement-learning.md)
