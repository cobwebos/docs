---
title: 内容类型-QnA Maker
description: 内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e2569832f7f87ed7b8fccbc44e431fa45495ba67
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996102"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>可以添加到知识库的文档内容类型
内容类型包括许多标准结构化文档，如 PDF、DOC 和 TXT。

## <a name="file-and-url-data-types"></a>文件和 URL 数据类型

下表汇总了 QnA Maker 支持的内容类型和文件格式。

|源类型|内容类型| 示例|
|--|--|--|
|代码|常见问题解答<br> （平面，包含节或主题主页）<br>支持页面 <br> （单页操作指南文章、故障排除文章，等等）|[纯文本常见问题解答](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs)、 <br>[包含链接的常见问题解答](https://www.microsoft.com/en-us/software-download/faq)、<br> [包含主题主页的常见问题解答](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[支持文章](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF / DOC|常见问题解答、<br> 产品手册、<br> 小册子、<br> 论文、<br> 传单策略、<br> 支持指南、<br> 结构化 QnA，<br> 等等|**无多轮**<br>[Structured QnA.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)、<br> [Sample Product Manual.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)、<br> [Sample semi-structured.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)、<br> [示例白皮书 .pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)，<br><br>**多项转换**：<br>[Surface Pro （.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Contoso 权益（.docx）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Contoso 权益（pdf）](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|* Excel|结构化 QnA 文件<br> （包括 RTF、HTML 支持）|[Sample QnA FAQ.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|* TXT/TSV|结构化 QnA 文件|[示例 chit-chat.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

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

## <a name="content-format-guidelines"></a>内容格式指南

了解有关不同文件的[格式准则](../reference-document-format-guidelines.md)的详细信息。

## <a name="next-steps"></a>后续步骤

了解[问题和答案（QnA）对](question-answer-set.md)中存储的信息。