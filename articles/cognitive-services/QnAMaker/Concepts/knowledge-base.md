---
title: 从数据源导入 - QnA 制造商
description: QnA Maker 知识库由一组问答 （QnA） 集和与每个 QnA 对关联的可选元数据组成。
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7772b2c76dfb92ced4d2c0d4b8dae97a59d1b83
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79457815"
---
# <a name="importing-from-data-sources"></a>从数据源导入

知识库由公共 URL 和文件带来的问答集组成。

## <a name="data-source-locations"></a>数据源位置

内容从数据源引入知识库。 数据源位置是**公共 URL 或文件**，不需要身份验证。

[共享点文件](../how-to/add-sharepoint-datasources.md)（通过身份验证保护）是例外。 SharePoint 资源必须是文件，而不是网页。 如果 URL 以 Web 扩展名（如 ） 结尾。ASPX，它将不会从SharePoint导入QnA制造商。

## <a name="chit-chat-content"></a>Chit 聊天内容

Chit 聊天 QnA 内容集以多种语言和会话样式作为完整的内容数据源提供。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。 [了解如何将](../how-to/chit-chat-knowledge-base.md)此内容集添加到知识库。

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要包含问题和`.tsv`答案的结构化文件。 此信息可帮助 QnA Maker 对问题答案集进行分组，并将其归为特定的数据源。

| 问题  | Answer  | 源| 元数据（1 个键：1 个值） |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>通过导入构建多转格式

您可以以`.tsv`文件格式创建多转对话。 该格式使您能够通过分析以前的聊天日志（与其他进程一起，不使用 QnA Maker）创建多圈对话，然后通过自动化创建`.tsv`文件。 导入文件以替换现有知识库。

> [!div class="mx-imgBorder"]
> ![3级多转问题的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

多转`.tsv`的列，特定于多转是 **"提示**"。 Excel`.tsv`中显示的示例显示要包括用于定义多转子级的信息：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**显示顺序**是数字，**显示文本**是不应包含标记的文本。

> [!div class="mx-imgBorder"]
> ![多转题示例，如 Excel 所示](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>导出为示例

如果您不确定如何在`.tsv`文件中表示 QnA 集：
* 使用[GitHub 的此可下载示例](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* 或者在 QnA Maker 门户中创建集，保存，然后导出知识库，以了解如何表示集的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>请参阅

使用 QnA 制造商[标记标记引用](../reference-markdown-format.md)可帮助您设置答案的格式。

[QnA Maker 概述](../Overview/overview.md)

创建和编辑知识库：：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

生成答案：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
