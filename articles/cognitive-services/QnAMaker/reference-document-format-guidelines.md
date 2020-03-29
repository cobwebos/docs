---
title: 导入文档格式指南 - QnA 制造商
description: 了解如何使用 URL 类型导入和创建 QnA 集。
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: 6a954f2fd607b70c6db256ab6dcc1dbcd7a5a473
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651835"
---
# <a name="format-guidelines-for-imported-documents-and-urls"></a>导入文档和 URL 的格式指南

查看这些格式设置指南，以获得内容的最佳结果。

## <a name="formatting-considerations"></a>格式设置注意事项

导入文件或 URL 后，QnA Maker 会以[标记格式](https://en.wikipedia.org/wiki/Markdown)转换和存储您的内容。 转换过程在文本中添加新行，如`\n\n`。 了解标记格式可帮助您了解转换后的内容并管理知识库内容。

如果直接在知识库中添加或编辑内容，请使用**标记格式**创建丰富的文本内容或更改答案中已有的标记格式内容。 QnA Maker 支持大部分标记格式，为您的内容带来丰富的文本功能。 但是，客户端应用程序（如聊天机器人）可能不支持相同的标记格式集。 测试客户端应用程序的显示答案非常重要。

## <a name="basic-document-formatting"></a>基本文档格式

QnA Maker 根据视觉线索（如：

* 字体大小
* 字体样式
* 编号
* 颜色

|文档示例|
|--|
||



## <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导材料。 它可以帮助用户设置、使用、维护产品和排除产品故障。 当 QnA Maker 处理手册时，它会将标题和副标题作为问题提取，将后续内容作为答案提取。 请参阅[此处](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

下面是一个手册示例，其中包含索引页和分层内容。

 ![知识库的产品手册示例](./media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册，以及具有分层标题的清晰结构。

## <a name="brochures-guidelines-papers-and-other-files"></a>小册子、指南、论文和其他文件

许多其他类型的文档也可进行处理，以便生成 QA 对，前提是它们有清晰的结构和布局。 其中包括：小册子、指南、报告、白皮书、科学论文、政策、书籍等。[在此处](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)查看示例。

下面是一个半结构化文档的示例，没有索引：

 ![Azure Blob 存储半结构化文档](./media/qnamaker-concepts-datasources/semi-structured-doc.png)

## <a name="structured-qna-document"></a>结构化 QnA 文档

DOC 文件中结构化问答的格式采用每行都有交替的问题和回答的形式，一个问题占据一行，后面的一行是其回答，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

下面是结构化 QnA Word 文档的示例：

 ![知识库的结构化常见问题解答文档示例](./media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>结构化 *TXT*、*TSV* 和 *XLS* 文件

采用结构化 *.txt*、*.tsv* 或 *.xls* 文件形式的 QnA 也可上传到 QnA Maker，以便创建或增强知识库。  它们的内容可以是纯文本，也可以是 RTF 或 HTML 格式。

| 问题  | Answer  | 元数据（1 个键：1 个值） |
|-----------|---------|-------------------------|
| 问题 1 | 答案 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 |      `Key:Value`           |

将忽略源文件中的任何其他列。

### <a name="example-of-structured-excel-file"></a>结构化 Excel 文件的示例

下面是结构化 QnA *.xls* 文件的示例，包含 HTML 内容：

 ![知识库的结构化常见问题解答 Excel 示例](./media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 文件中单一答案的备用问题示例

下面是结构化 QnA *.xls*文件的示例，其中有几个备用问题可供单个答案回答：

 ![Excel 文件中单一答案的备用问题示例](./media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

导入文件后，问答对位于知识库中，如下所示：

 ![导入知识库的单一答案的备用问题的屏幕截图](./media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要包含数据源信息的结构化 .tsv 文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | 源| 元数据（1 个键：1 个值） |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

<a href="#formatting-considerations"></a>

## <a name="multi-turn-document-formatting"></a>多转文档格式

* 使用标题和子标题表示层次结构。 例如，您可以 h1 表示父 QnA 和 h2 以表示应作为提示符的 QnA。 使用小标题大小表示后续层次结构。 不要使用样式、颜色或其他机制来暗示文档中的结构，QnA Maker 不会提取多转提示。
* 标题的第一个字符必须大写。
* 不要用问号结束标题。 `?`


|文档示例|
|--|
||