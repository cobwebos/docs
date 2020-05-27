---
title: 快速入门：在 QnA Maker 门户中添加问题和回答
description: 本快速入门介绍如何使用元数据添加问题和答案集，以便用户可以找到其问题的正确答案。
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: fc8bac425723e9194c447f1cf6ee13547c09d772
ms.sourcegitcommit: 64fc70f6c145e14d605db0c2a0f407b72401f5eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83873690"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>快速入门：使用 QnA Maker 门户添加问题和回答

创建知识库后，添加问题和回答 (QnA) 集以及元数据，以便筛选回答。 下表中的问题涉及到 Azure 服务限制，但每个问题与不同的 Azure 服务相关。

<a name="qna-table"></a>

|设置|问题|Answer|元数据|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

将元数据添加到 QnA 对之后，客户端应用程序可以：

* 请求仅与特定元数据匹配的回答。
* 接收所有回答，但根据每个回答的元数据对回答进行后处理。


## <a name="prerequisites"></a>先决条件

* 完成[前一个快速入门](./create-publish-knowledge-base.md)

## <a name="sign-in-to-the-qna-maker-portal"></a>登录到 QnA Maker 门户

1. 登录到 [QnA Maker 门户](https://www.qnamaker.ai)。

1. 选择来自[上一个快速入门](../how-to/create-knowledge-base.md)的现有知识库。

## <a name="add-additional-alternatively-phrased-questions"></a>添加其他采用替代句式的问题

当前知识库具有 QnA Maker 故障排除 QnA 对。 这些集是在创建过程中向知识库添加 URL 时创建的。

导入此 URL 后，只创建了具有一种回答的一个问题。 在此过程中添加其他问题。

1. 在“编辑”页上，使用问题和回答集上方的搜索文本框查找问题 `How large a knowledge base can I create?`

1. 在“问题”列中，选择“+ 添加替代句式”，然后添加下表中提供的每种新句式。  

    |替代句式|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. 选择**保存并训练**以重新训练知识库。

1. 选择“测试”，然后输入一个与新替代句式接近、但措辞不完全相同的问题： 

    `What GB size can a knowledge base be?`

    正确的回答将以 markdown 格式返回：

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    如果在返回的回答下选择“检查”，可以查看与问题相符、但置信度级别不一样高的其他回答。 

    不要添加替代句式的每种可能组合。 当启用 QnA Maker 的[主动学习](../how-to/improve-knowledge-base.md)后，可以查找能够最好地帮助知识库符合用户需求的替代句式。

1. 再次选择“测试”关闭测试窗口。 

## <a name="add-metadata-to-filter-the-answers"></a>添加用于筛选回答的元数据

将元数据添加到问题和回答集可让客户端应用程序请求筛选的回答。 此筛选器将在应用[第一个和第二个排名器](../concepts/query-knowledge-base.md#ranker-process)之前应用。

1. 添加[此快速入门的第一个表格](#qna-table)中不包含元数据的第二个问题和回答集，然后继续执行以下步骤。

1. 依次选择“视图选项”、“显示元数据”。  

1. 对于刚刚添加的 QnA 对，选择“添加元数据标记”，然后添加名称 `service` 和值 `search`。  它将如下所示：`service:search`。

1. 添加名为 `link_in_answer` 且值为 `false` 的另一个元数据标记。 它将如下所示：`link_in_answer:false`。

1. 在表 `How large a knowledge base can I create?` 中搜索第一个回答。

1. 为相同的两个元数据标记添加元数据对：

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    现在，有两个问题具有相同的元数据标记，但这些标记的值不同。

1. 选择**保存并训练**以重新训练知识库。

1. 选择顶部菜单中的“发布”转到“发布”页。 
1. 选择“发布”按钮，将当前知识库发布到终结点。 
1. 发布知识库后，继续学习下一个快速入门，了解如何从知识库生成回答。

## <a name="what-did-you-accomplish"></a>你完成了哪些操作？

你已编辑了你的知识库来支持更多问题，并提供了名称/值对以在搜索最佳回答时或者在返回回答后进行后处理时支持筛选。

## <a name="clean-up-resources"></a>清理资源

如果你不继续学习下一个快速入门，请在 Azure 门户中删除 QnA Maker 和 Bot 框架资源。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Postman 或 cURL 获取回答](get-answer-from-knowledge-base-using-url-tool.md)