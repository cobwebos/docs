---
title: 创建知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 向机器人中添加聊天内容可使其更健谈而有趣。 使用 QnA Maker，可以轻松将预填充的一组最常用的聊天内容添加到知识库中。 这可以用作你的机器人的聊天内容的起点，并节省从头开始编写它们的时间和成本。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: 274c2289c75f44c5a1c8dd3799612a23f46a6d67
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "47037688"
---
# <a name="create-a-knowledge-base"></a>创建知识库

使用 QnA Maker，在创建知识库时可以轻松添加现有数据源。 可以基于以下文档类型创建新的 QnA Maker 知识库：

<!-- added for scanability -->
* 常见问题解答页面
* 产品手册
* 结构化文档

## <a name="steps"></a>步骤

1. 使用 Azure 凭据登录到 [QnA Maker 门户](https://qnamaker.ai)，选择“新建服务”。

    ![创建知识库 ](../media/qnamaker-how-to-create-kb/create-new-service.png)

2. 如果尚未创建 QnA Maker 服务，请选择“创建 QnA Maker 服务”。 

3. 在 QnA Maker 门户中，从**步骤 2** 中的列表中选择你的 Azure 租户、Azure 订阅名称以及与 QnA Maker 服务关联的 Azure 资源名称。 选择将托管知识库的 Azure QnA Maker 服务。

    ![设置 QnA 服务](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. 输入你的知识库的名称和新知识库的数据源。

    ![设置数据源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 为服务提供一个“名称”。 支持重复的名称，也支持特殊字符。
    - 添加你希望提取的数据的 URL。 若要详细了解支持的源类型，请查看[此处](../Concepts/data-sources-supported.md)。
    - 上传你希望提取的数据的文件。 若要了解能添加的文档数量，请参阅[定价信息](https://aka.ms/qnamaker-pricing)。
    - 如果希望手动添加 QnA，则可以跳过上图中显示的**步骤 4**。

5. 向知识库中添加**聊天内容**。 通过选择 3 个预定义的性格之一，选择为你的机器人添加聊天内容支持。 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. 选择“创建知识库”。

    ![创建知识库](../media/qnamaker-how-to-create-kb/create-kb.png)

7. 提取数据需要几分钟时间。

    ![提取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. 成功创建知识库后，将重定向至“知识库”页面。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [添加个人聊天内容](./chit-chat-knowledge-base.md)
