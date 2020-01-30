---
title: 内容类型-QnA Maker
description: 内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: ac94f51fbe20c2efc277c084f9c704b5bcfa3a86
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843431"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知识库的文档内容类型
内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>文件和 URL 数据类型

下表汇总了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|URL|常见问题<br> （平面，包含节或主题主页）<br>支持页面 <br> （单页操作指南文章、故障排除文章，等等）|[纯文本常见问题解答](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[包含链接的常见问题解答](https://www.microsoft.com/en-us/software-download/faq)、<br> [包含主题主页的常见问题解答](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支持文章](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|常见问题解答、<br> 产品手册、<br> 小册子、<br> 论文、<br> 传单策略、<br> 支持指南、<br> 结构化 QnA，<br> 等等|[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [示例白皮书 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)，<br>[示例 multi-turn](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)|
|\* Excel|结构化 QnA 文件<br> （包括 RTF、HTML 支持）|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|结构化 QnA 文件|[示例 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果需要对数据源进行身份验证，请考虑以下方法，以便将该内容导入 QnA Maker：

* 手动下载文件并导入 QnA Maker
* 从经过身份验证的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)添加文件

## <a name="url-content"></a>URL 内容

可以通过 QnA Maker 中的**URL**导入两种类型的文档：

* 常见问题解答 URL
* 支持 Url

每种类型都指示一个预期格式。

## <a name="file-based-content"></a>基于文件的内容

您可以在[QnA Maker 门户](https://www.qnamaker.ai)中从公共源或本地文件系统将文件添加到知识库中。

## <a name="content-format-guidelines"></a>内容格式准则

QnA Maker 添加文件和 URL 内容，并将内容转换为 QnA 集，存储为 markdown （`.md`）。 内容适用于一种知识库，其中内容以某种结构化形式进行组织，并在定义明确的部分中表示。 节可以进一步分解为子节或子主题。 提取最适用于结构清晰且具有分层标题的文档。

QnA Maker 根据视觉提示（如字体大小、字形、编号、颜色等）标识内容中的部分和子节和关系。半结构化的内容包括手册、常见问题解答、指导原则、策略、小册子、传单以及许多其他类型的文件。

## <a name="faq-urls"></a>常见问题解答 URL

QnA Maker 可以通过 3 种不同的形式支持常见问题解答网页：普通常见问题解答页、带链接的常见问题解答页、包含主题主页的常见问题解答页。


### <a name="plain-faq-pages"></a>纯文本常见问题解答页

这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。

下面是普通常见问题解答页的示例：

![知识库的纯文本常见问题解答页示例](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>带链接的常见问题解答页

在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页的不同部分或不同页中的答案。

下面的示例是一个常见问题解答页，其中的链接位于同一页上的不同部分：

 ![知识库的部分链接常见问题解答页示例](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>带主题主页的常见问题解答页

这种类型的常见问题解答有一个带主题的主页，其中的每个主题都是一个链接，链接到不同页中的相关 QnA。 在这里，QnA Maker 会抓取所有链接的页以提取相应的问题与解答。

下面的示例是一个常见问题解答页，其中的主题主页有一个链接，链接到不同页中的常见问题解答部分。

 ![知识库的深层链接常见问题解答页示例](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>支持 URL

QnA Maker 可以处理半结构化支持网页，例如，介绍如何执行给定任务、如何诊断和解决给定问题以及适用于给定流程的最佳做法的网文。 提取最适用于结构清晰且具有分层标题的文档。

> [!NOTE]
> 提取支持文章是一项新功能，并且处于早期阶段。 它最适用于结构良好且未包含复杂页眉/页脚的简单页面。

![QnA Maker 支持从在分层标题中提供了清晰结构的半结构化网页进行提取](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC 文件

QnA Maker 可以处理 PDF 或 DOC 文件中的半结构化内容，将其转换为 QnA。 若要让文件适合提取，应该将内容以结构化形式进行组织，并在定义好的节中进行呈现。 这些节可以进一步拆分成子节或子主题。 提取最适用于结构清晰且具有分层标题的文档。

QnA Maker 根据视觉提示（如字体大小、字形、编号、颜色等）标识文件中的节和子节和关系。半结构化的 PDF 或 DOC 文件可以是手册、常见问题、指导原则、策略、小册子、传单以及许多其他类型的文件。 下面是这些文件的一些示例类型。

### <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导材料。 它可以帮助用户设置、使用、维护产品和排除产品故障。 当 QnA Maker 处理手册时，它会将标题和副标题作为问题提取，将后续内容作为答案提取。 请参阅[此处](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

下面是一个手册示例，其中包含索引页和分层内容。

 ![知识库的产品手册示例](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册，以及具有分层标题的清晰结构。

### <a name="brochures-guidelines-papers-and-other-files"></a>小册子、指南、论文和其他文件

许多其他类型的文档也可进行处理，以便生成 QA 对，前提是它们有清晰的结构和布局。 其中包括：小册子、指导原则、报告、白皮书、科研论文、政策、书籍等。请参阅[此处](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的示例。

下面是一个半结构化文档的示例，没有索引：

 ![Azure Blob 存储半结构化文档](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>结构化 QnA 文档

DOC 文件中结构化问答的格式采用每行都有交替的问题和回答的形式，一个问题占据一行，后面的一行是其回答，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

下面是结构化 QnA Word 文档的示例：

 ![知识库的结构化常见问题解答文档示例](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>结构化 *TXT*、*TSV* 和 *XLS* 文件

采用结构化 *.txt*、 *.tsv* 或 *.xls* 文件形式的 QnA 也可上传到 QnA Maker，以便创建或增强知识库。  它们的内容可以是纯文本，也可以是 RTF 或 HTML 格式。

| 问题  | 答案  | 元数据（1键：1值） |
|-----------|---------|-------------------------|
| 问题 1 | 答案 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 |      `Key:Value`           |

将忽略源文件中的任何其他列。

### <a name="example-of-structured-excel-file"></a>结构化 Excel 文件的示例

下面是结构化 QnA *.xls* 文件的示例，包含 HTML 内容：

 ![知识库的结构化常见问题解答 Excel 示例](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 文件中单个答案的替代问题示例

下面是结构化*的 QnA 文件*的一个示例，其中有几个替代问题可用于单个答案：

 ![Excel 文件中单个答案的替代问题示例](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

导入文件后，问题和答案对在知识库中显示，如下所示：

 ![导入到知识库中的单个答案的替代问题的屏幕截图](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>格式设置注意事项

导入文件或 URL 后，QnA Maker 以[markdown 格式](https://en.wikipedia.org/wiki/Markdown)转换和存储内容。 转换过程会在文本中添加新行，如 `\n\n`。 Markdown 格式的知识有助于理解转换后的内容和管理知识库内容。

如果直接在知识库中添加或编辑内容，请使用**markdown 格式**创建丰富的文本内容，或者更改答案中已经存在的 markdown 格式内容。 QnA Maker 支持大部分 markdown 格式，以便为内容提供丰富的文本功能。 但是，客户端应用程序（如聊天机器人）可能不支持相同的一组 markdown 格式。 必须测试客户端应用程序的答案显示。

有关详细信息，请[参阅 QnA Maker markdown 参考文档](../reference-markdown-format.md)。

## <a name="testing-your-markdown"></a>测试 Markdown

使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** 教程验证 Markdown。 此教程具有用于快速复制/粘贴验证的**试一试**功能。

## <a name="next-steps"></a>后续步骤

* 了解如何设计和管理[问题与答案（QnA）集](question-answer-set.md)