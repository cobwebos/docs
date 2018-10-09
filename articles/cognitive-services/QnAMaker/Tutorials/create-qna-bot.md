---
title: 使用 Azure 机器人服务创建 QnA 机器人 - QnA Maker
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 74c7bc5c601cd36a8dd2454506745406bc00dac0
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031282"
---
# <a name="create-a-qna-bot-with-azure-bot-service-v3"></a>使用 Azure 机器人服务 v3 创建 QnA 机器人
本教程介绍在 Azure 门户中使用 Azure 机器人服务 v3 生成 QnA 机器人的完整过程。

## <a name="prerequisite"></a>先决条件
生成之前，请按照[创建知识库](../How-To/create-knowledge-base.md)中的步骤创建包含问题和回答的 QnA Maker 服务。

该机器人通过 QnAMakerDialog 响应所创建的知识库中的问题。

## <a name="create-a-qna-bot"></a>创建 QnA 机器人
1. 在 [Azure 门户](https://portal.azure.com)的菜单边栏选项卡中，选择“创建”以创建新资源，然后选择“查看全部”。

    ![创建机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-creation.png)

2. 在搜索框中，搜索“Web 应用机器人”。

    ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-service-selection.png)

3. 在“机器人服务”边栏选项卡中提供所需的信息：

    - 将“应用名称”设置为机器人名称。 将机器人部署到云（例如，mynotesbot.azurewebsites.net）时，该名称用作子域。
    - 选择订阅、资源组、应用服务计划和位置。

4. 若要查看使用 SDK v4 创建 QnA 机器人的说明，请参阅 [QnA v4 机器人模板](https://aka.ms/qna-bot-v4)。 若要使用 v3 模板，请选择 **SDK v3** 作为 SDK 版本，选择 **C#** 或 **Node.js** 作为 SDK 语言。

    ![机器人 sdk 设置](../media/qnamaker-tutorials-create-bot/bot-v3.png)

5. 对于机器人模板字段选择“问题和答案”模板，然后通过选择“选择”保存模板设置。

    ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-v3-template.png)

6. 查看设置，然后选择“创建”。 此操作可创建具有 QnAMakerDialog 的机器人服务并将其部署到 Azure。

    ![选择机器人服务](../media/qnamaker-tutorials-create-bot/bot-blade-settings-v3.png)

7. 确认已部署机器人服务。

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
