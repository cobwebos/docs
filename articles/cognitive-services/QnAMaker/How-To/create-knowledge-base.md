---
title: 创建知识库-QnA Maker
titleSuffix: Azure Cognitive Services
description: 使用 QnA Maker API 服务门户, 通过 chit 添加创建知识库。 这使得应用程序具有吸引力。 将预先填充的顶部聊天集添加到知识库中作为你的机器人的聊天内容的起点，并节省从头开始编写它们的时间和成本。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/30/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 322a1d25ed434d8be674288b1b13d6ecf961590b
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2019
ms.locfileid: "70193560"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>快速入门：使用 QnA Maker API 服务门户创建知识库

使用 QnA Maker API 服务门户, 可以在创建知识库时轻松添加现有数据源。 可以基于以下文档类型创建新的 QnA Maker 知识库：

<!-- added for scanability -->
* 常见问题解答页面
* 产品手册
* 结构化文档

包括聊天个性化内容，让你的知识更吸引用户。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。 

## <a name="create-a-new-knowledge-base"></a>创建新的知识库

1. 使用 Azure 凭据登录到 [QnA Maker 门户](https://qnamaker.ai)，选择“创建知识库”。

1. 如果尚未创建 QnA Maker 服务，请选择“创建 QnA Maker 服务”。 

1. 在 QnA Maker 门户中，从**步骤 2** 中的列表中选择你的 Azure 租户、Azure 订阅名称以及与 QnA Maker 服务关联的 Azure 资源名称。 选择将托管知识库的 Azure QnA Maker 服务。

    ![设置 QnA 服务](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. 输入你的知识库的名称和新知识库的数据源。

    ![设置数据源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

1. 为服务指定一个**名称**, 例如`my first kb`。 支持重复的名称，也支持特殊字符。

1. 添加 "QnA Maker 故障排除" 页作为 URL `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`:, 然后`+ Add URL`选择。 若要详细了解支持的源类型，请查看[此处](../Concepts/data-sources-supported.md)。 对于本快速入门, 请勿为要提取的数据**上传文件**。 若要了解能添加的文档数量，请参阅[定价信息](https://aka.ms/qnamaker-pricing)。

1. 添加 **_专业_Chit-聊天**到你的 KB。 

1. 选择“创建知识库”。

    ![创建知识库](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 提取数据可能需要几分钟时间。

    ![提取](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. 成功创建知识库后, 会重定向到**知识库**页面。

## <a name="clean-up-resources"></a>清理资源

使用完知识库后，在 QnA Maker 门户中将其删除。

## <a name="next-steps"></a>后续步骤

为了节省成本, 可以[共享](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker)某些但不是所有为 QnA Maker 创建的 Azure 资源。

> [!div class="nextstepaction"]
> [添加带有元数据的问题](../quickstarts/add-question-metadata-portal.md)
