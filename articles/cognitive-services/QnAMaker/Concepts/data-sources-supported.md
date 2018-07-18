---
title: 支持的数据源 - Microsoft 认知服务 | Microsoft Docs
titleSuffix: Azure
description: 支持的数据源
services: cognitive-services
author: nstulasi
manager: sangitap
ms.service: cognitive-services
ms.component: QnAMaker
ms.topic: article
ms.date: 04/21/2018
ms.author: saneppal
ms.openlocfilehash: b888846056fd60f37cdb1da85904fa14ffe79a39
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366301"
---
# <a name="data-sources"></a>数据源 
QnA Maker 可以从常见的半结构化内容格式（例如常见问题解答和产品手册）中自动提取问答对。 也可以从结构化文件将内容添加到知识库中。

## <a name="plain-faq-pages"></a>纯文本常见问题解答页
这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。 

![纯文本常见问题解答页](../media/qnamaker-concepts-datasources/plain-faq.png) 

 

## <a name="faq-pages-with-section-links"></a>带有部分链接的常见问题解答页 
在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页上不同部分中的答案。

 ![部分链接常见问题解答页](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


## <a name="faq-pages-with-links-to-different-pages"></a>包含指向不同页的链接的常见问题解答页 
这种类型的常见问题解答页类似于部分链接的常见问题解答页，不同的是，链接会重定向到不同页。 QnA Maker 会抓取所有链接的页以提取相应的答案。

 ![深层链接常见问题解答页](../media/qnamaker-concepts-datasources/deeplink-faq.png) 


## <a name="product-manuals"></a>产品手册

手册通常是产品附带的指导材料。 它可以帮助用户设置、使用、维护产品和排除产品故障。 当 QnA Maker 处理手册时，它会将标题和副标题作为问题提取，将后续内容作为答案提取。 请参阅[此处](http://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf)的示例。

> [!NOTE]
> 提取最适用于具有目录和/或索引页的手册，以及具有分层标题的清晰结构。


## <a name="structured-data-format-through-file-upload"></a>通过文件上传提供的结构化数据格式

在知识库创建期间，还可以将具有带格式列的结构化文件（如 .tsv、.xlsx）上传到 QnA Maker。 还可以从知识库的“设置”选项卡上传文件

| 问题  | Answer  | 元数据                |
|-----------|---------|-------------------------|
| 问题 1 | 答案 1 | `Key1:Value1\|Key2:Value2` |
| 问题 2 | 答案 2 |      `Key:Value`           |
将忽略源文件中的任何其他列。

## <a name="structured-data-format-through-import"></a>通过导入提供的结构化数据格式
导入知识库将替换现有知识库的内容。 导入需要包含数据源信息的结构化 .tsv 文件。 这些信息可帮助 QnA Maker 将问答对分组，并将它们归因于特定数据源。

| 问题  | Answer  | Source| 元数据                |
|-----------|---------|----|---------------------|
| 问题 1 | 答案 1 | Url1|`Key1:Value1\|Key2:Value2` |
| 问题 2 | 答案 2 | 编辑|    `Key:Value`       |

## <a name="editorial"></a>编辑
如果没有预先存在的内容来填充知识库，也可以在 QnA Maker 知识库中以编辑方式添加它们。 在[此处](../How-To/edit-knowledge-base.md)了解如何更新知识库。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [设置 QnA Maker 服务](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>另请参阅 

[QnA Maker 概述](../Overview/overview.md)