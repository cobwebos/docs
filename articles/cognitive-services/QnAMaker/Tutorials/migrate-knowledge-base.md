---
title: 迁移知识库 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 迁移知识库需要从一个知识库中导出然后导入另一个知识库。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 04/08/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 04ee592122d7c76396f091f8d249518976682004
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446600"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

迁移知识库需要从一个知识库中导出然后导入另一个知识库。 

## <a name="prerequisites"></a>必备组件

* 在开始之前，创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>从 QnA Maker 迁移知识库
1. 登录 [QnA Maker 门户](https://qnamaker.ai)。
1. 选择要迁移的知识库。

1. 在“设置”  页上，选择“导出知识库”  ，下载包含知识库内容（问题、答案、元数据和从中提取这些内容的数据源的名称）的 .tsv 文件。

1. 从顶部菜单选择“创建知识库”  ，然后创建一个空知识库。 

    ![设置数据源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 为服务提供一个“名称”  。 支持重复的名称，也可以使用特殊字符。

1. 选择“创建”  。

    ![创建知识库](../media/qnamaker-how-to-create-kb/create-kb.png)

1. 在此新的知识库中，打开“设置”选项卡，然后选择“导入知识库”   。 此操作可导入问题、答案和元数据，同时保留从中提取这些内容的数据源的名称。

   ![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)

1. 使用“测试”面板测试新知识库  。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。
1. 发布知识库  。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。
1. 在应用程序或机器人代码中使用此终结点。 参阅此处，了解如何[创建 QnA 机器人](../Tutorials/create-qna-bot.md)。

    ![QnA Maker 值](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    此时，所有知识库内容（问题、答案和元数据，以及源文件的名称和 URL）都已导入新知识库。 

## <a name="chat-logs-and-alterations"></a>聊天日志和变更内容
区分大小写的替换词（同义词）不会自动导入。 使用[V4 Api](https://go.microsoft.com/fwlink/?linkid=2092179)移动新知识库中的更改。

无法迁移聊天日志，因为新知识库使用 Application Insights 存储聊天日志。 

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
