---
title: 从数据源导入-QnA Maker
description: QnA Maker 知识库由一组问题和答案（QnA）集以及与每个 QnA 对关联的可选元数据组成。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: d47d994366a8057521c1cc2ab1ab8a7ec3393965
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219683"
---
# <a name="importing-from-data-sources"></a>从数据源导入

知识库由公共 Url 和文件中引入的问题和答案集组成。

## <a name="data-source-locations"></a>数据源位置

内容将从数据源引入知识库。 数据源位置是不需要身份验证的**公共 url 或文件**。

[SharePoint 文件](../how-to/add-sharepoint-datasources.md)是由身份验证保护的，这是个例外。 SharePoint 资源必须是文件，而不是网页。 如果 URL 以 web 扩展结束，例如。ASPX，它不会从 SharePoint 导入 QnA Maker。

## <a name="chit-chat-content"></a>Chit 聊天内容

Chit chat QnA 内容集以多种语言和会话样式提供的完整内容数据源。 这可以用作你的机器人的个性化内容的起点，并节省从头开始编写它们的时间和成本。 了解[如何将](../how-to/chit-chat-knowledge-base.md)此内容集添加到知识库中。

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要包含问题和答案的结构化 `.tsv` 文件。 此信息有助于 QnA Maker 将问题答案集分组，并将其属性设置为特定的数据源。

| 问题  | Answer  | 源| 元数据（1键：1值） |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>通过导入的结构化多转换格式

可以采用 `.tsv` 文件格式创建多轮会话。 此格式提供了通过分析以前的聊天日志（使用其他进程，而不是使用 QnA Maker）来创建多轮会话的功能，然后通过自动化创建 `.tsv` 文件。 导入文件以替换现有知识库。

> [!div class="mx-imgBorder"]
> ![3 级多轮问题的概念模型](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

多轮 `.tsv`的列是**提示**的。 例如，在 Excel 中显示的一个示例 `.tsv`显示用于定义多轮子项的信息：

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder**为数值，而 "文本" 是不应包含 " **markdown" 的**文本。

> [!div class="mx-imgBorder"]
> ![多转换问题示例，如 Excel 中所示](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>导出为示例

如果不确定如何在 `.tsv` 文件中表示 QnA 集，请在 QnA Maker 门户中创建该集，保存，然后导出该知识库，以获取有关如何表示该集的示例。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [知识库的开发生命周期](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>另请参阅

使用 QnA Maker [Markdown 参考](../reference-markdown-format.md)来帮助你设置答案的格式。

[QnA Maker 概述](../Overview/overview.md)

使用创建和编辑知识库：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

使用以下内容生成答案：
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [.NET SDK](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
