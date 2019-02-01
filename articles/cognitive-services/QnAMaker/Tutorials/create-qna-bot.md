---
title: QnA 机器人 - Azure 机器人服务 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 本教程介绍在 Azure 门户中使用 Azure 机器人服务 v3 生成 QnA 机器人的完整过程。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: qna-maker`
ms.topic: article
ms.date: 01/24/2019
ms.author: tulasim
ms.openlocfilehash: 428aea5775c1777f38d7ea563cfe3af700c27f02
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55211825"
---
# <a name="tutorial-create-a-qna-bot-with-azure-bot-service-v3"></a>教程：使用 Azure 机器人服务 v3 创建 QnA 机器人

本教程介绍了在 [Azure 门户](https://portal.azure.com)中使用 Azure 机器人服务 v3 生成 QnA 机器人的完整过程，不需要编写任何代码。 将已发布的知识库 (KB) 连接到机器人就像更改机器人应用程序设置一样简单。 

> [!Note] 
> 本主题适用于机器人 SDK 版本 3。 可以在[此处](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs)找到版本 4。 

**本教程介绍如何执行下列操作：**

<!-- green checkmark -->
> [!div class="checklist"]
> * 使用 QnA Maker 模板创建 Azure 机器人服务
> * 与机器人聊天来验证代码是否正常工作 
> * 将已发布的 KB 连接到机器人
> * 使用问题对机器人进行测试

对于本文，你可以使用免费的 QnA Maker [服务](../how-to/set-up-qnamaker-service-azure.md)。

## <a name="prerequisites"></a>先决条件

本教程需要一个已发布的知识库。 如果没有知识库，请按照[创建知识库](../How-To/create-knowledge-base.md)中的步骤创建包含问题和回答的 QnA Maker 服务。

## <a name="create-a-qna-bot"></a>创建 QnA 机器人

1. 在 Azure 门户中，选择“创建资源”。

    ![创建机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 在搜索框中，搜索“Web 应用机器人”。

    ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. 在“机器人服务”中提供所需的信息：

    - 将“应用名称”设置为机器人名称。 将机器人部署到云（例如，mynotesbot.azurewebsites.net）时，该名称用作子域。
    - 选择订阅、资源组、应用服务计划和位置。

4. 若要使用 v3 模板，请选择 **SDK v3** 作为 SDK 版本，选择 **C#** 或 **Node.js** 作为 SDK 语言。

    ![机器人 sdk 设置](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. 对于机器人模板字段选择“问题和答案”模板，然后通过选择“选择”保存模板设置。

    ![保存机器人服务模板选择](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 查看设置，然后选择“创建”。 这将创建机器人服务并将其部署到 Azure。

    ![创建机器人](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. 确认已部署机器人服务。

    - 选择“通知”（Azure 门户顶部边缘的钟形图标）。 通知将从“部署已开始”更改为“部署已成功”。
    - 通知更改为“部署已成功”后，选择该通知中的“转到资源”。

## <a name="chat-with-the-bot"></a>与机器人聊天

选择“转到资源”，导航到机器人的资源。

选择“通过网上聊天执行测试”，打开“网上聊天”窗格。 在网上聊天中键入“hi”。

![QnA 机器人网页聊天](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

机器人将响应“请在‘应用设置’中设置 QnAKnowledgebaseId 和 QnASubscriptionKey。 此响应表明 QnA Bot 已收到消息，但尚无与其关联的 QnA Maker 知识库。 

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>将 QnA Maker 知识库连接到机器人

1. 打开“应用程序设置”，然后编辑“QnAKnowledgebaseId”、“QnAAuthKey”和“QnAEndpointHostName”字段，以包含 QnA Maker 知识库的值。

    ![应用程序设置](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

1. 在 QnA Maker 门户中从知识库的“设置”选项卡获取知识库 ID、主机 URL 和终结点密钥。

    - 登录 [QnA Maker](https://qnamaker.ai)
    - 转到你的知识库
    - 选择“设置”选项卡
    - “发布”知识库（如果尚未执行该操作）

    ![QnA Maker 值](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> 如果要将预览版知识库与 QnA 机器人连接，请将“Ocp-Apim-Subscription-Key”的值设置为“QnAAuthKey”。 将“QnAEndpointHostName”留空。

## <a name="test-the-bot"></a>测试机器人

在 Azure 门户中，选择“在 Web 聊天中测试”以测试机器人。 

![QnA Maker 机器人](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA 机器人通过知识库给出答案。

## <a name="clean-up-resources"></a>清理资源

完成本教程中的机器人后，在 Azure 门户中删除机器人。 机器人服务包括：

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
