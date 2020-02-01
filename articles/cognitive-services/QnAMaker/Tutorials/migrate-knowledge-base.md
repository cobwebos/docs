---
title: 迁移知识库-QnA Maker
titleSuffix: Azure Cognitive Services
description: 迁移知识库需要从一个知识库中导出，然后导入到另一个知识库。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/28/2020
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: b441eb1e6531030a998fe628ae833b29a5d9fe5a
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902052"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

迁移知识库需要从一个知识库中导出，然后导入到另一个知识库。

## <a name="prerequisites"></a>Prerequisites

* 在开始之前，请创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 设置新的[QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>从 QnA Maker 迁移知识库
1. 登录到[QnA Maker 门户](https://qnamaker.ai)。
1. 选择要迁移的源知识库。

1. 在 "**设置**" 页上，选择 "**导出知识库**" 以下载一个 tsv 文件，其中包含源知识库的内容-问题、答案、元数据、后续提示以及从中提取它们的数据源名称。

1. 从顶部菜单中选择 "**创建知识库**"，然后创建一个_空_的知识库。 它是空的，因为在创建它时，不会添加任何 Url 或文件。 在导入步骤中创建后会添加这些操作。

    配置知识库。 仅设置新的知识库名称。 支持重复的名称，并且还支持特殊字符。

    不要从步骤4中选择任何内容，因为在导入该文件时将覆盖这些值。

1. 在步骤5中，选择 "**创建**"。

1. 在这个新知识库中，打开 "**设置**" 选项卡，然后选择 "**导入知识库**"。 这会导入问题、答案、元数据、后续提示，并保留从中提取它们的数据源名称。

   > [!div class="mx-imgBorder"]
   > [![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用 "测试" 面板**测试**新的知识库。 了解如何[测试您的知识库](../How-To/test-knowledge-base.md)。
1. **发布**知识库。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。
1. 在应用程序或机器人代码中使用终结点。 请参阅此处，了解如何[创建 QnA 机器人](../Tutorials/create-qna-bot.md)。

    ![QnA Maker 值](../media/qnamaker-how-to-migrate-kb/qnamaker-settings-kbid-key.png)

    此时，所有知识库内容问题、解答和元数据以及源文件和 Url 的名称将导入到新的知识库。

## <a name="chat-logs-and-alterations"></a>聊天日志和变更
不会自动导入不区分大小写的变更（同义词）。 使用[V4 api](https://go.microsoft.com/fwlink/?linkid=2092179)移动新知识库中的变更。

由于新的知识库使用 Application Insights 来存储聊天日志，因此无法迁移聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
