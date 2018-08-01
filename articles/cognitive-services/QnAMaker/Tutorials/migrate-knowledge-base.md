---
title: 如何导入知识库 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 如何导入知识库
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: ce8f98f9bdb37d5f326e942fe5b5e815e5272c56
ms.sourcegitcommit: 680964b75f7fff2f0517b7a0d43e01a9ee3da445
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "35366913"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库
QnA Maker 于 2018 年 5 月 7 日的 \\\build\ 大会上推出了正式版。 QnA Maker 正式版在 Azure 上构建了新的体系结构。 使用 QnA Maker 免费预览版创建的知识库需要迁移到 QnA Maker 正式版。 QnA Maker 预览版将于 2018 年 11 月弃用。 有关 QnA Maker 正式版中的更改的详细信息，请参阅 QnA Maker 正式版公告[博客文章](https://aka.ms/qnamakerga-blog)。

QnA Maker 现在具有[定价模型](https://azure.microsoft.com/en-us/pricing/details/cognitive-services/qna-maker/)。

先决条件
> [!div class="checklist"]
> * 如果你还没有 Azure 订阅，可以在开始前创建一个 [免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
> * 设置新的 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker-preview-portal"></a>从 QnA Maker 预览版门户迁移知识库
1. 导航到 [QnA Maker 预览版门户](https://aka.ms/qnamaker-old-portal
)，单击 **我的服务** 。
2. 单击“编辑”图标，选择要迁移的知识库。

    ![编辑知识库](../media/qnamaker-how-to-migrate-kb/preview-editkb.png)

3. 单击“下载知识库”，下载包含知识库内容（问题、答案、元数据和从中提取这些内容的数据源的名称）的 .tsv 文件。

    ![下载知识库](../media/qnamaker-how-to-migrate-kb/preview-download.png)

4. 使用 Azure 凭据登录 [QnA Maker 门户](https://qnamaker.ai)，单击“新建服务”。

    ![创建知识库 ](../media/qnamaker-how-to-create-kb/create-new-service.png)
    
5. 如果尚未创建 QnA Maker 服务，请选择“创建 QnA Maker 服务”。 否则，从步骤 2 中的下拉列表选择一个 QnA Maker 服务。 选择将托管知识库的 QnA Maker 服务。

    ![设置 QnA 服务](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

6. 创建空知识库 

    ![设置数据源](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - 为服务提供一个“名称”。 支持重复的名称，也可以使用特殊字符。
    - 如果想要使用预览版知识库中的数据，请跳过上传文件或 URL。 接下来将创建一个空知识库。

7. 选择**创建**。

    ![创建知识库](../media/qnamaker-how-to-create-kb/create-kb.png)

8. 在此新的知识库中，打开“设置”选项卡，然后单击“导入知识库”。 此操作可导入问题、答案和元数据，同时保留从中提取这些内容的数据源的名称。

   ![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)

9. 使用“测试”面板测试新知识库。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。
10. 发布知识库。 了解如何[发布知识库](../How-To/publish-knowledge-base.md)。
11. 在应用程序或机器人代码中使用下面的终结点。 参阅此处，了解如何[创建 QnA 机器人](../Tutorials/create-qna-bot.md)。

    ![QnA Maker 值](../media/qnamaker-tutorials-create-bot/qnamaker-settings-kbid-key.PNG)

此时，所有知识库内容（问题、答案和元数据，以及源文件的名称和 URL）都已导入新知识库。 

## <a name="chatlogs-and-alterations"></a>聊天日志和替换词
替换词（同义词）不会自动导入。 使用 [V2 API](https://aka.ms/qnamaker-v2-apis) 导出预览版堆栈中的替换词，然后使用 [V4 API](https://aka.ms/qnamaker-v4-apis) 在新堆栈中替换。

无法迁移聊天日志，因为新堆栈使用 Application Insights 存储聊天日志。 但可从[预览版门户](https://aka.ms/qnamaker-old-portal)下载聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
