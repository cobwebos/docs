---
title: 迁移知识库 - QnA Maker
description: 迁移知识库需要从一个知识库中导出然后导入另一个知识库。
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: 13e5e79bf4eaf6ec59e41b3e12aa1bb23f2c1578
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80258084"
---
# <a name="migrate-a-knowledge-base-using-export-import"></a>使用导出-导入迁移知识库

迁移是从现有知识库创建新知识库的过程。 这样做可能有几个原因：

* 备份和恢复过程
* CI/CD 管道
* 移动区域

迁移知识库需要从现有知识库导出，然后导入到另一个知识库。

## <a name="prerequisites"></a>先决条件

* 在开始之前，创建一个[免费帐户](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)。
* 设置新 [QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="migrate-a-knowledge-base-from-qna-maker"></a>从 QnA Maker 迁移知识库
1. 登录到[QnA 制造商门户](https://qnamaker.ai)。
1. 选择要迁移的原点知识库。

1. 在 **"设置"** 页上，选择 **"导出知识库**"以下载包含源知识库内容的 .tsv 文件 - 问题、答案、元数据、后续提示以及从中提取它们的数据源名称。

1. 选择从顶部菜单**创建知识库**，然后创建_空_知识库。 它是空的，因为当您创建它时，您不打算添加任何 URL 或文件。 这些在导入步骤期间在创建后添加。

    配置知识库。 仅设置新的知识库名称。 支持重复的名称，也可以使用特殊字符。

    不要从步骤 4 中选择任何内容，因为导入文件时这些值将被覆盖。

1. 在步骤 5 中，选择 **"创建**"。

1. 在此新的知识库中，打开“设置”选项卡，然后选择“导入知识库”********。 这将导入问题、答案、元数据、后续提示，并保留从中提取这些问题的数据源名称。

   > [!div class="mx-imgBorder"]
   > [![导入知识库](../media/qnamaker-how-to-migrate-kb/Import.png)](../media/qnamaker-how-to-migrate-kb/Import.png#lightbox)

1. 使用“测试”面板测试新知识库****。 了解如何[测试知识库](../How-To/test-knowledge-base.md)。

1. **发布**知识库并创建聊天机器人。 了解如何[发布知识库](../Quickstarts/create-publish-knowledge-base.md#publish-the-knowledge-base)。

## <a name="programmatically-migrate-a-knowledge-base-from-qna-maker"></a>以编程方式从 QnA 制造商迁移知识库

迁移过程使用以下 REST API 以编程方式可用：

**导出**

* [下载知识库 API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/download)

**导入**

* [替换 API（使用相同的知识库 ID 重新加载）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/replace)
* [创建 API（使用新的知识库 ID 加载）](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create)


## <a name="chat-logs-and-alterations"></a>聊天日志和变更内容
区分大小写的替换词（同义词）不会自动导入。 使用[V4 API](https://go.microsoft.com/fwlink/?linkid=2092179)在新知识库中移动更改。

无法迁移聊天日志，因为新知识库使用 Application Insights 存储聊天日志。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [编辑知识库](../How-To/edit-knowledge-base.md)
