---
title: 内容类型-QnA Maker
description: 内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 3020c0623cff73e5a907f4d63f08f5866afd52dc
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2020
ms.locfileid: "76902087"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知识库的文档内容类型
内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>文件和 URL 数据类型

下表总结了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|URL|常见问题<br> （单层，含部分或包含主题主页）<br>支持页面 <br> （单页操作方法文章、疑难解答文章等）|[普通常见问题](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)， <br>[链接常见问题解答](https://www.microsoft.com/en-us/software-download/faq)，<br> [主题主页常见问题解答](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支持文章](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|常见问题<br> 产品手册、<br> 手册<br> 曲别针<br> 传单政策，<br> 支持指南，<br> 结构化 QnA，<br> 等.|**无多轮**<br>[结构化 QnA](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [示例产品手册 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)，<br> [示例 semi-structured](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)，<br> [示例白皮书 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)，<br><br>**多项转换**：<br>[Surface Pro （.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 权益（.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 权益（pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|\* Excel|结构化 QnA 文件<br> （包括 RTF、HTML 支持）|[示例 QnA 的 FAQ](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|\* TXT/TSV|结构化 QnA 文件|[示例 chit-chat](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

如果需要对数据源进行身份验证，请考虑以下方法，以便将该内容导入 QnA Maker：

* 手动下载文件并导入 QnA Maker
* 从经过身份验证的[Sharepoint 位置](../how-to/add-sharepoint-datasources.md)添加文件

## <a name="url-content"></a>URL 内容

可以通过 QnA Maker 中的**URL**导入两种类型的文档：

* 常见问题 Url
* 支持 Url

每种类型都指示一个预期格式。

## <a name="file-based-content"></a>基于文件的内容

您可以在[QnA Maker 门户](https://www.qnamaker.ai)中从公共源或本地文件系统将文件添加到知识库中。

## <a name="content-format-guidelines"></a>内容格式准则

QnA Maker 添加文件和 URL 内容，并将内容转换为 QnA 集，存储为 markdown （`.md`）。 内容适用于一种知识库，其中内容以某种结构化形式进行组织，并在定义明确的部分中表示。 节可以进一步分解为子节或子主题。 提取最适用于具有分层标题的清晰结构的内容。

QnA Maker 根据视觉提示（如字体大小、字形、编号、颜色等）标识内容中的部分和子节和关系。半结构化的内容包括手册、常见问题解答、指导原则、策略、小册子、传单以及许多其他类型的文件。

## <a name="faq-urls"></a>常见问题 Url

QnA Maker 可以通过3种不同形式支持 FAQ 网页：普通的常见问题页面、包含链接的 FAQ 页面、带有主题主页的 FAQ 页面。


### <a name="plain-faq-pages"></a>普通常见问题页面

这是最常见的 "常见问题" 页，其中的答案会立即按照同一页中的问题进行。

下面是普通常见问题页面的示例：

![知识库的普通常见问题页面示例](../media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>带链接的常见问题页面

在此类常见问题页面中，问题聚合在一起，并链接到在同一页面的不同部分或不同页面中的答案。

下面是一个 FAQ 页面示例，其中包含位于同一页面的部分中的链接：

 ![章节链接知识库的 "常见问题" 页示例](../media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="faq-pages-with-a-topics-homepage"></a>主题主页的常见问题页面

这种类型的 FAQ 有一个主页，其中每个主题都是指向其相关 Qna 在不同页面中的链接。 此处 QnA Maker 对所有链接页面进行爬网，以 & 答案来提取相应的问题。

下面是 "常见问题" 页的一个示例，其中的主题主页链接到不同页面中的 FAQ 部分。

 ![知识库的深层链接常见问题页面示例](../media/qnamaker-concepts-datasources/topics-faq.png)


### <a name="support-urls"></a>支持 Url

QnA Maker 可以处理半结构化的支持网页，如介绍如何执行给定任务的 web 文章、如何诊断和解决给定的问题，以及给定进程的最佳实践。 提取最适用于具有分层标题的清晰结构的内容。

> [!NOTE]
> 支持项目的提取是一项新功能，并处于早期阶段。 它最适合用于结构良好的简单页面，并且不包含复杂的页眉/页脚。

![QnA Maker 支持从使用分层标题显示清晰结构的半结构化网页中提取](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF/DOC 文件

QnA Maker 可以在 PDF 或 DOC 文件中处理半结构化内容，并将其转换为 Qna。 可以很好地提取的好文件是以某种结构化形式对内容进行组织，并在定义明确的部分中表示。 节可以进一步分解为子节或子主题。 提取最适用于具有分层标题的清晰结构的文档。

QnA Maker 根据视觉提示（如字体大小、字形、编号、颜色等）标识文件中的节和子节和关系。半结构化的 PDF 或 DOC 文件可以是手册、常见问题、指导原则、策略、小册子、传单以及许多其他类型的文件。 下面是这些文件的一些示例类型。

### <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导原则。 它可帮助用户设置、使用、维护和解决产品问题。 当 QnA Maker 处理手动时，它会将标题和副标题作为问题和后续内容提取为答案。 请参阅[此处](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

下面是一个带有索引页和分层内容的手册的示例

 ![知识库产品手册示例](../media/qnamaker-concepts-datasources/product-manual.png)

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册和具有分层标题的清晰结构。

### <a name="brochures-guidelines-papers-and-other-files"></a>小册子、指南、白皮书和其他文件

还可以处理许多其他类型的文档以生成 QA 对，前提是它们具有清晰的结构和布局。 其中包括：小册子、指导原则、报告、白皮书、科研论文、政策、书籍等。请参阅[此处](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的示例。

下面是不含索引的半结构化文档的一个示例：

 ![Azure Blob 存储半结构化文档](../media/qnamaker-concepts-datasources/semi-structured-doc.png)

### <a name="structured-qna-document"></a>结构化 QnA 文档

DOC 文件中的结构化问题答案的格式以每行的问题和答案的形式提供，每行有一个问题，其后是以下行的答案，如下所示：

```text
Question1

Answer1

Question2

Answer2
```

下面是结构化 QnA word 文档的一个示例：

 ![知识库的结构化 QnA 文档示例](../media/qnamaker-concepts-datasources/structured-qna-doc.png)

## <a name="structured-txt-tsv-and-xls-files"></a>结构化*TXT*、 *TSV*和*XLS*文件

Qna、 *tsv*或 *.xls*文件格式的也可以上载到 QnA Maker 来创建或增加知识库。  这些内容可以是纯文本，也可以包含 RTF 或 HTML 格式的内容。

| 问  | 答案  | 元数据（1键：1值） |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

源文件中的任何其他列都将被忽略。

### <a name="example-of-structured-excel-file"></a>结构化 Excel 文件的示例

下面是结构化*的 QnA 文件*的示例，其中包含 HTML 内容：

 ![知识库的结构化 QnA excel 示例](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Excel 文件中单个答案的替代问题示例

下面是结构化*的 QnA 文件*的一个示例，其中有几个替代问题可用于单个答案：

 ![Excel 文件中单个答案的替代问题示例](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

导入文件后，问题和答案对在知识库中显示，如下所示：

 ![导入到知识库中的单个答案的替代问题的屏幕截图](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="formatting-considerations"></a>格式注意事项

导入文件或 URL 后，QnA Maker 以[markdown 格式](https://en.wikipedia.org/wiki/Markdown)转换和存储内容。 转换过程会在文本中添加新行，如 `\n\n`。 Markdown 格式的知识有助于理解转换后的内容和管理知识库内容。

如果直接在知识库中添加或编辑内容，请使用**markdown 格式**创建丰富的文本内容，或者更改答案中已经存在的 markdown 格式内容。 QnA Maker 支持大部分 markdown 格式，以便为内容提供丰富的文本功能。 但是，客户端应用程序（如聊天机器人）可能不支持相同的一组 markdown 格式。 必须测试客户端应用程序的答案显示。

有关详细信息，请[参阅 QnA Maker markdown 参考文档](../reference-markdown-format.md)。

## <a name="testing-your-markdown"></a>测试 Markdown

使用 **[CommonMark](https://commonmark.org/help/tutorial/index.html)** 教程验证您的 Markdown。 本教程针对快速复制/粘贴验证提供了一项**试用**功能。

## <a name="next-steps"></a>后续步骤

* 了解如何设计和管理[问题与答案（QnA）集](question-answer-set.md)