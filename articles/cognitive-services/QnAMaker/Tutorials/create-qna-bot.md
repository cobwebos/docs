---
title: QnA 机器人 - Azure 机器人服务 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 通过现有知识库的“发布”页创建 QnA 聊天机器人。 此机器人使用 Bot Framework SDK v4。 无需编写任何代码即可生成该机器人，系统会自动提供所有代码。
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 07/31/2019
ms.author: diberry
ms.openlocfilehash: 4bb987a5a091871bec2c0cc8cec6d9ab804bb244
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697990"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v4"></a>教程：创建使用 Azure 机器人服务 v4 的 QnA 机器人

通过现有知识库的“发布”页创建 QnA 聊天机器人。  此机器人使用 Bot Framework SDK v4。 无需编写任何代码即可生成该机器人，系统会自动提供所有代码。

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 基于现有的知识库创建 Azure 机器人服务
> * 与机器人聊天来验证代码是否正常工作 

## <a name="prerequisites"></a>先决条件

本教程需要一个已发布的知识库。 如果没有知识库，请遵循[创建问答知识库](create-publish-query-in-portal.md)中的步骤创建包含问题和回答的 QnA Maker 知识库。

<a name="create-a-knowledge-base-bot"></a>

## <a name="create-a-qna-bot"></a>创建 QnA 机器人

创建一个机器人，作为知识库的客户端应用程序。 

1. 在 QnA Maker 门户中转到“发布”页，然后发布知识库。  选择“创建机器人”。  

    ![在 QnA Maker 门户中转到“发布”页，然后发布知识库。 选择“创建机器人”。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base-page.png)

    Azure 门户将会打开，其中提供了用于创建机器人的配置。

1.  输入用于创建机器人的设置：

    |设置|值|目的|
    |--|--|--|
    |机器人名称|`my-tutorial-kb-bot`|这是机器人的 Azure 资源名称。|
    |Subscription|查看用途。|请选择创建 QnA Maker 资源时所用的同一个订阅。|
    |Resource group|`my-tutorial-rg`|与机器人相关的所有 Azure 资源使用的资源组。|
    |位置|`west us`|机器人的 Azure 资源位置。|
    |定价层|`F0`|Azure 机器人服务的免费层。|
    |应用程序名称|`my-tutorial-kb-bot-app`|这是一个 Web 应用，仅用于为机器人提供支持。 此应用名称不能与 QnA Maker 服务已使用的名称相同。 不支持与任何其他资源共享 QnA Maker 的 Web 应用。|
    |SDK 语言|C#|这是 Bot Framework SDK 使用的基础编程语言。 可以选择 [C#](https://github.com/Microsoft/botbuilder-dotnet) 或 [Node.js](https://github.com/Microsoft/botbuilder-js)。|
    |QnA 身份验证密钥|**不要更改**|系统会自动填充此值。|
    |应用服务计划/位置|**不要更改**|在本教程中，位置并不重要。|
    |Azure 存储|**不要更改**|聊天数据将存储在 Azure 存储表中。|
    |Application Insights|**不要更改**|日志将发送到 Application Insights。|
    |Microsoft 应用 ID|**不要更改**|必须填写 Active Directory 用户和密码。|

    ![将使用这些设置创建知识库机器人。](../media/qnamaker-tutorials-create-bot/create-bot-from-published-knowledge-base.png)

    请等待几分钟，直到机器人创建过程通知报告成功消息。

<a name="test-the-bot"></a>

## <a name="chat-with-the-bot"></a>与机器人聊天

1. 在 Azure 门户中，通过通知打开新的机器人资源。 

    ![在 Azure 门户中，通过通知打开新的机器人资源。](../media/qnamaker-tutorials-create-bot/azure-portal-notifications.png)

1. 在“机器人管理”中，选择“通过网上聊天执行测试”并输入：`How large can my KB be?`。   机器人将会响应： 


    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.`


    ![创建新的知识库机器人。](../media/qnamaker-tutorial-create-publish-query-in-portal/test-bot-in-web-chat-in-azure-portal.png)

    有关 Azure 机器人的详细信息，请参阅[使用 QnA Maker 回答问题](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)

## <a name="clean-up-resources"></a>清理资源

完成本教程中的机器人后，在 Azure 门户中删除机器人。 

如果为机器人资源创建了新的资源组，请删除该资源组。 

如果未创建新资源组，需要查找与机器人关联的资源。 最简单的方法是按机器人和机器人应用的名称进行搜索。 机器人资源包括：

* 应用服务计划
* 搜索服务
* 认知服务
* 应用服务
* 另外，它还可以包括 Application Insights 服务和 Application Insights 数据的存储


## <a name="related-to-qna-maker-bots"></a>有关 QnA Maker 机器人

* QnA Maker 门户中使用的 QnA Maker 帮助机器人可用作[机器人示例](https://github.com/microsoft/BotBuilder-Samples/tree/master/experimental/qnamaker-support)。
    ![QnA Maker 帮助机器人图标是红色的机器人](../media/qnamaker-tutorials-create-bot/answer-bot-icon.PNG)
* [医疗保健机器人](https://docs.microsoft.com/HealthBot/qna_model_howto)使用 QnA Maker 作为其[语言模型](https://docs.microsoft.com/HealthBot/qna_model_howto)之一。


[!INCLUDE [Bot Information](../../../../includes/cognitive-services-qnamaker-luis-bot-info.md)]

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [概念：知识库](../concepts/knowledge-base.md)

## <a name="see-also"></a>另请参阅

- [管理知识库](https://qnamaker.ai)
- [在不同频道启用机器人](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
