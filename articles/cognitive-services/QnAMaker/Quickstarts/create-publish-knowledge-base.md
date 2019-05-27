---
title: 创建、训练、发布知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 此示例中的 QnA Maker 知识库根据一个简单的 FAQ 网页创建，该网页解答有关 BitLocker 密钥恢复的问题。
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/10/2019
ms.author: diberry
ms.openlocfilehash: 1411576d078115921cb04b41347264bccdc390f4
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65593973"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>创建、训练和发布 QnA Maker 知识库

可以根据自己的内容（例如常见问题解答或产品手册）创建一个 QnA Maker 知识库 (KB)。 此示例中的 QnA Maker 知识库根据一个简单的 FAQ 网页创建，该网页解答有关 BitLocker 密钥恢复的问题。

## <a name="prerequisite"></a>先决条件

> [!div class="checklist"]
> * 如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。

## <a name="create-a-qna-maker-knowledge-base"></a>创建 QnA Maker 知识库

1. 使用 Azure 凭据登录到 [QnAMaker.ai](https://QnAMaker.ai) 门户。

1. 在 QnA Maker 门户上，选择“创建知识库”。

   ![创建新的知识库](../media/qna-maker-create-kb.png)

1. 在步骤 1 的“创建”页上，选择“创建 QnA 服务”。 此时会将你定向到 [Azure 门户](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)，让你在订阅中设置 QnA Maker 服务。 如果 Azure 门户超时，请在站点上选择“重试”。 连接后，Azure 仪表板会显示。

1. 在 Azure 中成功创建新的 QnA Maker 服务以后，返回到 qnamaker.ai/create。 在步骤 2 中，从下拉列表选择 QnA 服务。 如果已创建新的 QnA 服务，请确保刷新页面。

   ![选择 QnA 服务知识库](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. 在步骤 3 中，将知识库命名为“我的示例 QnA KB”。

1. 若要向知识库添加内容，请选择三种类型的数据源。 在步骤 4 中，在“填充 KB”下的“URL”框中添加 [BitLocker 恢复常见问题解答](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL。

   ![选择 QnA 服务知识库](../media/qnamaker-quickstart-kb/add-datasources.png)

1. 在步骤 5 中，选择“创建 KB”。

1. 在创建知识库时，会显示一个弹出窗口。 提取过程需要数分钟来读取 HTML 页面并确定问题和解答。

1. 成功创建知识库后，“知识库”页面会打开。 可在此页面上编辑知识库的内容。

## <a name="edit-the-knowledge-base"></a>编辑知识库

1. 在 QnA Maker 门户中的“编辑”部分，单击“添加 QnA 对”以将一个新行添加到知识库。 在“问题”下输入“嗨”。 在“解答”下输入“你好，请向我提出 BitLocker 问题。”

    ![添加 QnA 对](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. 在右上角选择“保存并训练”，以便保存所做的编辑并训练 QnA Maker 模型。 如果不保存，编辑的内容不会保留。

## <a name="test-the-knowledge-base"></a>测试知识库

1. 在 QnA Maker 门户的右上角，选择“测试”以测试所做的更改是否已生效。 在框中输入“`hi there`”，然后选择 Enter。 此时会看到已创建的作为响应的解答。

1. 选择“检查”，更详细地检查响应。 测试窗口用于测试对知识库所做的更改，然后会发布这些更改。

    ![测试面板](../media/qnamaker-quickstart-kb/inspect.png)

1. 再次选择“测试”，关闭“测试”弹出窗口。

## <a name="publish-the-knowledge-base"></a>发布知识库

发布知识库时，知识库的问答内容将从测试索引转移到 Azure 搜索中的生产索引。

![发布生产测试索引](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. 在 QnA Maker 门户中，在“编辑”旁边的菜单中选择“发布”。 然后，若要进行确认，请在页面上选择“发布”。

1. QnA Maker 服务现在已成功发布。 可以在应用程序或机器人代码中使用此终结点。

    ![发布](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>创建机器人

通过“发布”页发布后，始终可以创建机器人。 

* 可以快速创建多个机器人，它们全部可以指向不同区域中的同一个知识库，或者指向单个机器人的定价计划。 
* 如果只想为知识库创建一个机器人，请使用“在 Azure 门户中查看所有机器人”链接查看当前机器人的列表。 
* 对知识库进行更改并重新发布时，无需对机器人采取进一步的措施。 该机器人已配置为使用该知识库，并可以处理将来对该知识库所做的全部更改。 每当发布某个知识库时，连接到该知识库的所有机器人将自动更新

1. 在 QnA Maker 门户中的“发布”页上，选择“创建机器人”。 只有在发布知识库后，才显示此按钮。

    ![在 QnA Maker 门户中转到“发布”页，然后发布知识库。 选择“创建机器人”。](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. 此时会打开 Azure 门户的新浏览器标签页，其中显示了 Azure 机器人服务的创建页。 配置 Azure 机器人服务。 有关这些配置设置的详细信息，请参阅[使用 Azure 机器人服务 v4 创建 QnA 机器人](../tutorials/create-qna-bot.md)教程。
    
    * 创建机器人时，请不要更改 Azure 门户中的以下设置。 这些值是为现有的知识库预先填充的： 
        * QnA 身份验证密钥
        * 应用服务计划/位置
        * Azure 存储
    * 机器人和 QnA Maker 可以共享 Web 应用服务计划，但不能共享 Web 应用。 这意味着，该**应用名称**必须不同于创建 QnA Maker 服务时所用的应用名称。 


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [创建知识库](../How-To/create-knowledge-base.md)
