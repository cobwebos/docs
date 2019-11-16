---
title: 快速入门：创建、训练和发布知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 此示例中的 QnA Maker 知识库根据一个简单的 FAQ 网页创建，该网页解答有关 BitLocker 密钥恢复的问题。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: bc64196969b23f0aad77ff4d4495e4bb3e569c32
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73888242"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>快速入门：创建、训练和发布 QnA Maker 知识库

可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 本文包含一个从简单的 FAQ 网页创建 QnA Maker 知识库以回答 BitLocker 密钥恢复相关问题的示例。

包括聊天个性化内容，让你的知识更吸引用户。

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>先决条件

> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-new-qna-maker-knowledge-base"></a>创建新的 QnA Maker 知识库

1. 使用 Azure 凭据登录到 [QnAMaker.ai](https://QnAMaker.ai) 门户。

1. 在 QnA Maker 门户上，选择“创建知识库”。 

1. 在“创建”页上，选择“创建 QnA 服务”。   此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 

1. 在 QnA Maker 门户中，从下拉列表选择 QnA Maker 服务。 如果已创建新的 QnA Maker 服务，请确保刷新页面。

   ![选择 QnA Maker 服务知识库的屏幕截图](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 将知识库命名为“我的示例 QnA KB”。 

1. 将示例 Word 文档添加为 URL： 

    `https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`

1. 选择 `+ Add URL`。

1. 将专业聊天  添加到你的 KB。 

1. 选择“创建知识库”  。

    提取过程需要数分钟来读取文档并确定问题和解答。

    QnA Maker 成功创建知识库后，“知识库”页会打开。  可在此页面上编辑知识库的内容。

## <a name="add-a-new-question-and-answer-set"></a>添加新的问答集

1. 在 QnA Maker 门户中的“编辑”页上，选择“添加 QnA 对”。  
1. 添加以下问题： 

    `How many Azure services are used by a knowledge base?`

1. 添加 _markdown_ 格式的答案：

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ 添加文本格式的问题和 markdown 格式的答案。](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    markdown 符号 `*` 用于项目符号点。 `\n` 用于新行。  

    “编辑”页显示 markdown。  稍后使用“测试”面板时，会看到 markdown 显示正确。  

## <a name="save-and-train"></a>保存并训练

在右上角选择“保存并训练”，以便保存所做的编辑并训练  QnA Maker 模型。 如果不保存，编辑的内容不会保留。

## <a name="test-the-knowledge-base"></a>测试知识库

1. 在 QnA Maker 门户的右上角，选择“测试”以测试所做的更改是否已生效。  
1. 在文本框中输入示例用户查询。 

    `How many Azure services are used by a knowledge base?`  

    ![ 在文本框中输入示例用户查询。 ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. 选择“检查”，更详细地检查响应。  测试窗口用于测试对知识库所做的更改，然后会发布知识库。

1. 再次选择“测试”  ，关闭“测试”面板。 

## <a name="publish-the-knowledge-base"></a>发布知识库

发布知识库时，知识库的内容将从 `test` 索引转移到 Azure 搜索中的 `prod` 索引。

![移动知识库内容的屏幕截图](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 门户中，选择“发布”。  然后，若要进行确认，请在页面上选择“发布”  。

    QnA Maker 服务现在已成功发布。 可以在应用程序或机器人代码中使用此终结点。

    ![成功发布的屏幕截图](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>创建机器人

发布后，可以从“发布”  页创建机器人： 

* 可以快速创建多个机器人，它们全部可以指向不同区域中的同一个知识库，或者指向单个机器人的定价计划。 
* 如果只想为知识库创建一个机器人，请使用“在 Azure 门户中查看所有机器人”链接查看当前机器人的列表。  

对知识库进行更改并重新发布时，不需要对机器人采取进一步的操作。 机器人已配置为使用该知识库，并会处理将来对该知识库所做的所有更改。 每当发布某个知识库时，连接到该知识库的所有机器人都会自动更新。

1. 在 QnA Maker 门户中的“发布”页上，选择“创建机器人”。   只有在发布知识库后，才会显示此按钮。

    ![创建机器人的屏幕截图](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 此时会打开 Azure 门户的新浏览器标签页，其中显示了 Azure 机器人服务的创建页。 配置 Azure 机器人服务。 
    
    * 创建机器人时，请不要更改 Azure 门户中的以下设置。 这些值是为现有的知识库预先填充的： 
        * QnA 身份验证密钥
        * 应用服务计划和位置
    * 机器人和 QnA Maker 可以共享 Web 应用服务计划，但不能共享 Web 应用。 这意味着，机器人的**应用名称**必须不同于 QnA Maker 服务的应用名称。 

1. 创建机器人后，打开“机器人服务”资源。  
1. 在“机器人管理”  下，选择“通过网上聊天执行测试”  。
1. 出现聊天提示“键入你的消息”时，输入： 

    `Azure services?`

    聊天机器人使用知识库中的一个答案进行响应。 

    ![将用户查询输入测试性 Web 聊天中。](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>清理资源

清理 Azure 门户中的 QnA Maker 和 Bot Framework 资源。 

## <a name="next-steps"></a>后续步骤

更多相关信息：

* [答案中的 Markdown 格式](../concepts/data-sources-supported.md)
* [测试 Markdown](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [数据源](../Concepts/data-sources-supported.md)。 
* [机器人资源配置设置](../tutorials/create-qna-bot.md)。

> [!div class="nextstepaction"]
> [添加带有元数据的问题](add-question-metadata-portal.md)

