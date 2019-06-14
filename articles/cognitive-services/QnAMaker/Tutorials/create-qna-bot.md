---
title: QnA 机器人 - Azure 机器人服务 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 从现有知识库的发布页创建 QnA 聊天机器人。 此智能机器人应用程序使用 Bot Framework SDK v4。 不需要编写任何代码即可生成智能机器人应用程序，将为您提供的所有代码。
services: cognitive-services
author: tulasim88
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 06/11/2019
ms.author: tulasim
ms.openlocfilehash: b3bae01d65685aa9ea7bfc95d1f1454741d37b5e
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67053228"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>教程：创建与 Azure 的 QnA 机器人智能机器人应用程序服务 v4

创建从的 QnA 聊天机器人**发布**现有知识库的页。 此智能机器人应用程序使用 Bot Framework SDK v4。 不需要编写任何代码即可生成智能机器人应用程序，将为您提供的所有代码。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 从现有知识库创建 Azure 机器人服务
> * 与机器人聊天来验证代码是否正常工作 

## <a name="prerequisites"></a>必备组件

本教程需要一个已发布的知识库。 如果您确实有一个，请按照中的步骤[创建和从 KB 答案](create-publish-query-in-portal.md)教程，以创建与问题和答案的 QnA Maker 知识库。

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>创建 QnA 机器人

创建智能机器人应用程序作为客户端应用程序的知识库。 

1. 在 QnA Maker 门户中，转到**发布**页，然后发布您的知识库。 选择**创建机器人**。 

    ![在 QnA Maker 门户中转到“发布”页，然后发布知识库。 选择“创建机器人”。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    在 Azure 门户将打开与智能机器人应用程序创建配置。

1.  输入要创建智能机器人应用程序的设置：

    |设置|值|目的|
    |--|--|--|
    |机器人名称|`my-tutorial-kb-bot`|这是智能机器人应用程序的 Azure 资源名称。|
    |订阅|请参阅用途。|选择作为您用来创建 QnA Maker 资源相同的订阅。|
    |资源组|`my-tutorial-rg`|使用所有的智能机器人应用程序相关 Azure 资源的资源组。|
    |Location|`west us`|智能机器人应用程序的 Azure 资源位置。|
    |定价层|`F0`|Azure 机器人服务免费层。|
    |应用程序名称|`my-tutorial-kb-bot-app`|这是一个 web 应用以支持仅智能机器人。 这不应是相同的应用名称，因为已在使用 QnA Maker 服务。 不支持与任何其他资源共享 QnA Maker 提供的 web 应用。|
    |SDK 语言|C#|这是使用 bot framework SDK 的基础编程语言。 您可以选择[ C# ](https://github.com/Microsoft/botbuilder-dotnet)或[Node.js](https://github.com/Microsoft/botbuilder-js)。|
    |QnA 身份验证密钥|**不会更改**|此值为您填充。|
    |应用服务计划/位置|**不会更改**|本教程中，位置并不重要。|
    |Azure 存储|**不会更改**|会话数据存储在 Azure 存储表。|
    |Application Insights|**不会更改**|日志记录发送到 Application Insights。|
    |Microsoft 应用 ID|**不会更改**|Active directory 用户和密码是必需的。|

    ![使用这些设置创建知识库智能机器人应用程序。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    等待几分钟，直到智能机器人应用程序创建进程通知报告成功。

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>与机器人聊天

1. 在 Azure 门户中，从通知中打开新的智能机器人应用程序资源。 

    ![在 Azure 门户中，从通知中打开新的智能机器人应用程序资源。](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. 从**智能机器人应用程序管理**，选择**测试中 Web 聊天**并输入： `How large can my KB be?`。 智能机器人应用程序将使用进行响应： 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![测试新知识库智能机器人应用程序。](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    有关 Azure 机器人的详细信息，请参阅[使用 QnA Maker 回答问题](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="related-to-qna-maker-bots"></a>与相关的 QnA Maker 智能机器人

* QnA Maker 帮助机器人，QnA Maker 门户中使用是可用作[智能机器人应用程序示例](https://github.com/Microsoft/BotBuilder-Samples/tree/master/experimental/csharp_dotnetcore/qnamaker-support-bot)。
    ![QnA Maker 帮助智能机器人应用程序图标是红色的机器人](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [医疗保健机器人](https://docs.microsoft.com/HealthBot/qna_model_howto)QnA Maker 使用作为其中一个其[语言模型](https://docs.microsoft.com/HealthBot/qna_model_howto)。

## <a name="clean-up-resources"></a>清理资源

完成本教程中的机器人后，在 Azure 门户中删除机器人。 

如果创建新的资源组的智能机器人应用程序的资源，删除资源组。 

如果未创建新的资源组，您需要查找与智能机器人应用程序关联的资源。 最简单方法是要搜索的智能机器人应用程序和智能机器人应用程序的名称。 智能机器人应用程序资源包括：

* 应用服务计划
* 搜索服务
* 认知服务
* 应用服务
* 另外，它还可以包括 Application Insights 服务和 Application Insights 数据的存储

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概念：知识库](../concepts/knowledge-base.md)

## <a name="see-also"></a>另请参阅

- [管理知识库](https://qnamaker.ai)
- [在不同频道启用机器人](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
