---
title: 迁移知识库 - QnA Maker
description: 迁移知识库需要从一个知识库中导出然后导入另一个知识库。
ms.topic: how-to
ms.date: 03/25/2020
ms.openlocfilehash: 4148431ebb4de288ac517c7c006a925e36c5f460
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/18/2020
ms.locfileid: "88542915"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

迁移是从现有知识库创建新知识库的过程。 出于以下几个原因，你可以执行此操作：

* 备份和还原过程
* CI/CD 管道
* 移动区域

迁移知识库需要从现有知识库中导出，然后导入到另一个知识库中。

## <a name="prerequisites"></a>先决条件

* 在开始之前，创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。
* 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>从 QnA Maker 迁移知识库
1. 登录到 [QnA Maker 门户](https://qnamaker.ai)。
1. 选择要迁移的源知识库。

1. 在 " **设置** " 页上，选择 " **导出知识库** " 以下载一个 tsv 文件，其中包含源知识库的内容-问题、答案、元数据、后续提示以及从中提取它们的数据源名称。

1. 从顶部菜单中选择 " **创建知识库** "，然后创建一个 _空_ 的知识库。 它是空的，因为在创建它时，不会添加任何 Url 或文件。 在导入步骤中创建后会添加这些操作。

    配置知识库。 仅设置新的知识库名称。 支持重复的名称，也可以使用特殊字符。

    不要从步骤4中选择任何内容，因为在导入该文件时将覆盖这些值。

1. 在步骤5中，选择 " **创建**"。

1. 在此新的知识库中，打开“设置”选项卡，然后选择“导入知识库”********。 这会导入问题、答案、元数据、后续提示，并保留从中提取它们的数据源名称。

   > [!div class="mx-imgBorder"]
   > [![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用“测试”面板测试新知识库****。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

1. **发布** 知识库并创建聊天机器人。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以编程方式从 QnA Maker 迁移知识库

使用以下 REST Api 以编程方式提供迁移过程：

**导出**

* [下载知识库 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**导入**

* [将 API (重载替换为同一知识库 ID) ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [用新的知识库 ID 创建 API (负载) ](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天日志和变更内容
区分大小写的替换词（同义词）不会自动导入。 使用 [V4 api](https://go.microsoft.com/fwlink/?linkid=2092179) 移动新知识库中的变更。

无法迁移聊天日志，因为新知识库使用 Application Insights 存储聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
