---
title: 将 LUIS 应用发布到预测终结点
titleSuffix: Azure Cognitive Services
description: 当完成生成和测试活动 LUIS 应用时，可通过将其发布到终结点使其可用于客户端应用程序。
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: conceptual
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6eb48fd0f3290fbc3a249bc3880c809ace9f9ddb
ms.sourcegitcommit: 55952b90dc3935a8ea8baeaae9692dbb9bedb47f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2018
ms.locfileid: "48886456"
---
# <a name="publish-your-trained-app"></a>发布已训练的应用

当完成生成和测试活动 LUIS 应用时，可通过将其发布到终结点使其可用于客户端应用程序。 

<a name="publish-your-trained-app-to-an-http-endpoint"></a>

## <a name="publishing"></a>发布

若要发布到终结点，请在右上方的面板中选择“发布”。 

![右上方的导航栏](./media/luis-how-to-publish-app/publish-top-nav-bar.png)

当显示弹出窗口时选择正确的槽：过渡或生产。 使用两个发布槽，可以对已发布的终结点使用两个不同的版本，或者在两个不同的终结点上使用同一版本。 

该应用将发布到与 LUIS 门户中添加的 LUIS 资源关联的所有区域。 例如，对于在 [www.luis.ai](https://www.luis.ai) 上创建的应用，如果你在 **westus** 中创建 LUIS 资源并将其作为资源添加到该应用，则该应用将发布到此区域中。 有关 LUIS 区域的详细信息，请参阅[区域](luis-reference-regions.md)。
 
![“发布”弹出窗口](./media/luis-how-to-publish-app/publish-pop-up.png)

成功发布应用后，浏览器顶部会显示一个表示成功的绿色通知。 绿色通知栏还包含指向终结点的链接。 

![“发布”弹出窗口](./media/luis-how-to-publish-app/publish-success.png)

如果需要终结点 URL，请选择该链接。 还可以通过在顶部菜单中选择“管理”，然后在左侧菜单中选择“密钥和终结点”来访问终结点 URL。 

## <a name="configuring-publish-settings"></a>配置发布设置

通过在右上方导航栏中选择“管理”，然后选择“发布设置”来配置发布设置。 

![发布设置](./media/luis-how-to-publish-app/publish-settings.png)

### <a name="publish-after-enabling-sentiment-analysis"></a>在启用情绪分析后发布

<a name="enable-sentiment-analysis"></a>

情绪分析使 LUIS 可以与[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)集成，以便提供情绪和关键短语分析。 

不需提供文本分析密钥，而且系统不会因为此服务向你的 Azure 帐户收费。 检查此设置后，它就会变成持久性的。 

情绪数据是一个 0 到 1 的分数，指示数据的正面情绪（分数接近 1）或负面情绪（分数接近 0）。

若要详细了解使用情绪分析时的 JSON 终结点响应，请参阅[情绪分析](luis-concept-data-extraction.md#sentiment-analysis)



## <a name="next-steps"></a>后续步骤

* 请参阅[管理密钥](./luis-how-to-manage-keys.md)以了解如何将密钥添加到针对 LUIS 的 Azure 订阅密钥、如何设置必应拼写检查密钥以及如何将所有意向包含在结果中。
* 有关如何在测试控制台中测试已发布应用的说明，请参阅[训练和测试应用](luis-interactive-test.md)。

