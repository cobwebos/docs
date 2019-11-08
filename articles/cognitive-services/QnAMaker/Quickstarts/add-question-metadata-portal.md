---
title: 快速入门：在 QnA Maker 门户中添加问题和回答
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: 92735d8982fb1364d5ebfe0494f5ee51f4302469
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73794006"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>快速入门：使用 QnA Maker 门户添加问题和回答

创建知识库后，可以使用元数据添加问题和回答集，使用户能够找到问题的正确回答。

正确的回答是一种回答，但客户的多种提问方式都可能导致出现一种回答。

例如，下表中的问题涉及到 Azure 服务限制，但每个问题与不同的 Azure 服务相关。 

<a name="qna-table"></a>


|设置|问题|Answer|元数据|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

将元数据添加到问题/回答集之后，客户端应用程序可以：

* 请求仅与特定元数据匹配的回答。
* 接收所有回答，但根据每个回答的元数据对回答进行后处理。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="prerequisites"></a>先决条件

* 一个 QnA Maker 服务
* 在该 QnA Maker 服务中创建的一个知识库

[第一篇快速入门](../how-to/create-knowledge-base.md)中已创建该服务和知识库。

## <a name="sign-in-to-the-qna-maker-portal"></a>登录到 QnA Maker 门户

1. 登录到 [QnA Maker 门户](https://www.qnamaker.ai)。

1. 选择现有的知识库。 如果没有知识库，请返回[上一篇快速入门](../how-to/create-knowledge-base.md)并完成创建知识库的步骤。

## <a name="add-additional-alternatively-phrased-questions"></a>添加其他采用替代句式的问题 

[上一篇快速入门](../how-to/create-knowledge-base.md)中的当前知识库包含 QnA Maker 故障排除问题和回答集。 这些集是在创建过程中向知识库添加 URL 时创建的。 

导入此 URL 后，只创建了具有一种回答的一个问题。 

在此过程中添加其他问题。

1. 在“编辑”页上，使用问题和回答集上方的搜索文本框查找问题 `How large a knowledge base can I create?` 

1. 在“问题”列中，选择“+ 添加替代句式”，然后添加下表中提供的每种新句式。  

    |替代句式|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. 选择**保存并训练**以重新训练知识库。 

1. 选择“测试”，然后输入一个与新替代句式接近、但措辞不完全相同的问题： 

    `What GB size can a knowledge base be?`

    正确的回答将以 markdown 格式返回：`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    如果在返回的回答下选择“检查”，可以查看与问题相符、但置信度级别不一样高的其他回答。  

    不要添加替代句式的每种可能组合。 启用 QnA Maker 的[主动学习](../how-to/improve-knowledge-base.md)，以查找能够最好地帮助知识库符合用户需求的替代句式。

1. 再次选择“测试”关闭测试窗口。 

## <a name="add-metadata-to-filter-the-answers"></a>添加用于筛选回答的元数据

将元数据添加到问题和回答集可让客户端应用程序请求筛选的回答。 此筛选器将在应用[第一个和第二个排名器](../concepts/knowledge-base.md#ranker-process)之前应用。

1. 添加[此快速入门的第一个表格](#qna-table)中不包含元数据的第二个问题和回答集，然后继续执行以下步骤。 

1. 依次选择“视图选项”、“显示元数据”。   

1. 对于刚刚添加的问题和回答集，选择“添加元数据标记”，然后添加 `service` 的名称以及 `search` 和 `service:search` 的值。 

1. 添加包含 `link_in_answer` 的名称以及 `false` 和 `link_in_answer:false` 的值的元数据标记。

1. 在表 `How large a knowledge base can I create?` 中搜索第一个回答。 
1. 为相同的两个元数据标记添加元数据对：

    `link_in_answer` : `true`<br>
    `server`: `qna_maker`

    现在，有两个问题具有相同的元数据标记，但这些标记的值不同。 

1. 选择“保存并训练”以重新训练知识库。  

1. 选择顶部菜单中的“发布”转到“发布”页。  
1. 选择“发布”按钮，将当前知识库发布到可查询的终结点。  
1. 发布知识库后，选择“Curl”选项卡查看用于从知识库生成回答的示例 cURL 命令。 
1. 将命令复制到记事本或其他可编辑的环境，以便可以编辑命令。 编辑自己的资源名称、知识库 ID 和终结点密钥：

    |将|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    请注意，此问题只有一个单词 `size`，但可以返回任一问题和回答集。 `strictFilters` 数组告知响应将缩减为仅包含 `qna_maker` 回答。 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. 该响应仅包含符合筛选条件的回答。 

    为便于阅读，以下 cURL 响应已设置格式：

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    如果某个问题和回答集不符合搜索词但符合筛选器，将不会返回该集， 而是返回常规回答 `No good match found in KB.`。

    请确保将元数据名称和值对保持在所需的限制范围内。 

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [使用 Postman 获取回答](get-answer-from-kb-using-postman.md)