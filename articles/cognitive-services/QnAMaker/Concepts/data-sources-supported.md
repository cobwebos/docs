---
title: 受支持的数据源 - QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker 从半结构化内容（例如常见问题解答、产品手册、指南、支持文档，以及以网页、PDF 文件或 MS Word 文档文件形式存储的策略）中自动提取问答对。 也可以将内容从结构化 QnA 内容文件添加到知识库中。
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/25/2018
ms.author: tulasim
ms.openlocfilehash: 36d74c7fba472956d57344977de79a4cbfd2cf02
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49648507"
---
# <a name="data-sources-for-qna-maker-content"></a>QnA Maker 内容的数据源

QnA Maker 从半结构化内容（例如常见问题解答、产品手册、指南、支持文档，以及以网页、PDF 文件或 MS Word 文档文件形式存储的策略）中自动提取问答对。 也可以将内容从结构化 QnA 内容文件添加到知识库中。 

下表汇总了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|代码|常见问题解答（平面，包含节或主题主页）|[普通的常见问题解答](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、[带链接的常见问题解答](https://www.microsoft.com/software-download/faq)、[带主题主页的常见问题解答](https://support.microsoft.com/products/windows?os=windows-10)|
|PDF / DOC|常见问题解答、产品手册、小册子、论文、海报策略、支持指南、结构化 QnA，等等。|[Structured QnA.doc](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Bot%20Service%20Sample%20FAQ.docx)、[Sample Product Manual.pdf](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)、[Sample semi-structured.doc](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)、[Sample white paper.pdf](https://azure.microsoft.com/mediahandler/files/resourcefiles/azure-stack-wortmann-bring-the-power-of-the-public-cloud-into-your-data-center/Azure_Stack_Wortmann_Bring_the_Power_of_the_Public_Cloud_into_Your_Data_Center.pdf)|
|Excel|结构化 QnA 文件（包括 RTF、 HTML 支持）|[Sample QnA FAQ.xls](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|结构化 QnA 文件|[示例 chit-chat.tsv](https://raw.githubusercontent.com/Microsoft/BotBuilder-PersonalityChat/master/CSharp/Datasets/Queries_Responses_Friendly_QnAMaker.tsv)|

## <a name="faq-urls"></a>常见问题解答 URL

QnA Maker 可以通过 3 种不同的形式支持常见问题解答网页：普通常见问题解答页、带链接的常见问题解答页、包含主题主页的常见问题解答页。

### <a name="plain-faq-pages"></a>纯文本常见问题解答页

这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。 

下面是普通常见问题解答页的示例：

![纯文本常见问题解答页](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>带链接的常见问题解答页 

在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页的不同部分或不同页中的答案。

下面的示例是一个常见问题解答页，其中的链接位于同一页上的不同部分：

 ![部分链接常见问题解答页](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>带主题主页的常见问题解答页

这种类型的常见问题解答有一个带主题的主页，其中的每个主题都是一个链接，链接到不同页中的相关 QnA。 在这里，QnA Maker 会抓取所有链接的页以提取相应的问题与解答。

下面的示例是一个常见问题解答页，其中的主题主页有一个链接，链接到不同页中的常见问题解答部分。 

 ![深层链接常见问题解答页](../media/qnamaker-concepts-datasources/topics-faq.png) 


## <a name="pdf-doc-files"></a>PDF/DOC 文件

QnA Maker 可以处理 PDF 或 DOC 文件中的半结构化内容，将其转换为 QnA。 若要让文件适合提取，应该将内容以结构化形式进行组织，并在定义好的节中进行呈现。 这些节可以进一步拆分成子节或子主题。 提取最适用于结构清晰且具有分层标题的文档。

QnA Maker 根据视觉线索（例如字体大小、字体样式、编号、颜色等）来确定文件中的节、子节和关系。半结构化 PDF 或 DOC 文件可以是手册、常见问题解答、指南、策略、小册子、传单以及许多其他类型的文件。 下面是这些文件的一些示例类型。

### <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导材料。 它可以帮助用户设置、使用、维护产品和排除产品故障。 当 QnA Maker 处理手册时，它会将标题和副标题作为问题提取，将后续内容作为答案提取。 请参阅[此处](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

下面是一个手册示例，其中包含索引页和分层内容。

 ![产品手册示例](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册，以及具有分层标题的清晰结构。

### <a name="brochures-guidelines-papers-and-other-files"></a>小册子、指南、论文和其他文件

许多其他类型的文档也可进行处理，以便生成 QA 对，前提是它们有清晰的结构和布局。 其中包括：小册子，指南、报告、白皮书、科学论文、策略、书籍等。请参阅[此处](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx)的示例。

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

 ![结构化 QnA 文档](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>结构化 *TXT*、*TSV* 和 *XLS* 文件

采用结构化 *.txt*、*.tsv* 或 *.xls* 文件形式的 QnA 也可上传到 QnA Maker，以便创建或增强知识库。  它们的内容可以是纯文本，也可以是 RTF 或 HTML 格式。 

| 问题  | Answer  | 元数据                |
|-----------|---------|-------------------------|
| 问题 1 | 答案 1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 |      `Key:Value`           |

将忽略源文件中的任何其他列。

下面是结构化 QnA *.xls* 文件的示例，包含 HTML 内容：

 ![结构化 QnA Excel](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式

导入知识库将替换现有知识库的内容。 导入需要包含数据源信息的结构化 .tsv 文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | 源| 元数据                |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>以编辑身份添加到知识库

如果没有预先存在的内容来填充知识库，可以在 QnA Maker 知识库中以编辑方式添加 QnA。 在[此处](../How-To/edit-knowledge-base.md)了解如何更新知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>另请参阅 

[QnA Maker 概述](../Overview/overview.md)
