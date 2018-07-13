---
title: 使用 Azure 机器人服务创建 QnA 机器人 - Azure 认知服务 | Microsoft Docs
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 05/07/2018
ms.author: saneppal
ms.openlocfilehash: fc430bf3aa7cad279d7a93bb6892aa19abee3378
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109263"
---
# <a name="create-a-qna-bot-with-azure-bot-service"></a>使用 Azure 机器人服务创建 QnA 机器人
本教程介绍在 Azure 门户中使用 Azure 机器人服务生成 QnA 机器人的完整过程。

## <a name="prerequisite"></a>先决条件
生成之前，请按照[创建知识库](../How-To/create-knowledge-base.md)中的步骤创建包含问题和回答的 QnA Maker 服务。

该机器人通过 QnAMakerDialog 响应所创建的知识库中的问题。

## <a name="create-a-qna-bot"></a>创建 QnA 机器人
1. 在 [Azure 门户](https://portal.azure.com)的菜单边栏选项卡中，选择“创建”以创建新资源，然后选择“查看全部”。

    ![创建机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 在搜索框中，搜索“Web 应用机器人”。

    ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. 在“机器人服务”边栏选项卡中，提供所需信息，然后选择“创建”。 此操作可创建具有 QnAMakerDialog 的机器人服务并将其部署到 Azure。

    - 将“应用名称”设置为你的机器人名称。 将机器人部署到云（例如，mynotesbot.azurewebsites.net）时，该名称用作子域。
    - 选择订阅、资源组、应用服务计划和位置。
    - 在机器人模板字段中选择“问题和答案”（Node.js 或 C#）模板。
    - 勾选法律声明的确认复选框。 法律声明条款在该复选框下方。

        ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-qna-template.PNG)

4. 确认已部署机器人服务。

    - 选择“通知”（Azure 门户顶部边缘的钟形图标）。 通知将从“部署已开始”更改为“部署已成功”。
    - 通知更改为“部署已成功”后，选择该通知中的“转到资源”。

## <a name="chat-with-the-bot"></a>与机器人聊天
选择“转到资源”，导航到机器人的资源边栏选项卡。

注册机器人后，单击“通过网页聊天执行测试”，打开“网页聊天”窗格。 在网页聊天中键入“你好”。

![QnA 机器人网页聊天](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat.PNG)

机器人将响应“请在‘应用设置’中设置 QnAKnowledgebaseId 和 QnASubscriptionKey。 请前往 https://aka.ms/qnaabssetup 了解获取方法”。 此响应表明 QnA Bot 已收到消息，但尚无与其关联的 QnA Maker 知识库。 此关联操作将在下一步中执行。

## <a name="connect-your-qna-maker-knowledge-base-to-the-bot"></a>将 QnA Maker 知识库连接到机器人

1. 打开“应用程序设置”，然后编辑“QnAKnowledgebaseId”、“QnAAuthKey”和“QnAEndpointHostName”字段，以包含 QnA Maker 知识库的值。

    ![应用程序设置](../media/qnamaker-tutorials-create-bot/application-settings.PNG)

2. 从 https://qnamaker.ai 中知识库的“设置”选项卡获取知识库 ID、主机 URL 和终结点密钥。
    - 登录 [QnA Maker](https://qnamaker.ai)
    - 转到你的知识库
    - 单击“设置”选项卡
    - “发布”知识库（如果尚未执行该操作）

    ![QnA Maker 值](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

> [!NOTE]
> 如果要将预览版知识库与 QnA 机器人连接，请将“Ocp-Apim-Subscription-Key”的值设置为“QnAAuthKey”。 将“QnAEndpointHostName”留空。

## <a name="test-the-bot"></a>测试机器人
在 Azure 门户中，选择“通过网页聊天执行测试”以测试机器人。 

![QnA Maker 机器人](../media/qnamaker-tutorials-create-bot/qna-bot-web-chat-response.PNG)

QnA 机器人现在通过知识库给出答案。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [集成 QnA Maker 和 LUIS](./integrate-qnamaker-luis.md)

## <a name="see-also"></a>另请参阅

- [管理知识库](https://qnamaker.ai)
- [在不同频道启用机器人](https://docs.microsoft.com/azure/bot-service/bot-service-manage-channels)
