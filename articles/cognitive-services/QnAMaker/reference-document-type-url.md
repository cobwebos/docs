---
title: 导入支持的 URL 类型 - QnA 制造商
description: 了解如何使用 URL 类型导入和创建 QnA 对。
ms.topic: reference
ms.date: 01/02/2020
ms.openlocfilehash: c6c6c7ba19fe7be54733f45997134fddb56a6ba0
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804310"
---
# <a name="urls-supported-for-importing-documents"></a>导入文档支持的 URL

了解如何使用 URL 类型导入和创建 QnA 对。

## <a name="faq-urls"></a>常见问题解答 URL

QnA Maker 可以支持 3 种不同形式的常见问题解答网页：

* 纯文本常见问题解答页
* 带链接的常见问题解答页
* 包含主题主页的常见问题页面

### <a name="plain-faq-pages"></a>纯文本常见问题解答页

这是最常见的常见问题解答页类型，其中答案会紧跟在同一页中的问题后面。

下面是普通常见问题解答页的示例：

![知识库的纯文本常见问题解答页示例](./media/qnamaker-concepts-datasources/plain-faq.png)


### <a name="faq-pages-with-links"></a>带链接的常见问题解答页

在这种类型的常见问题解答页中，问题聚合在一起，并链接到同一页的不同部分或不同页中的答案。

下面的示例是一个常见问题解答页，其中的链接位于同一页上的不同部分：

 ![知识库的部分链接常见问题解答页示例](./media/qnamaker-concepts-datasources/sectionlink-faq.png)


### <a name="parent-topics-page-links-to-child-answers-pages"></a>父主题页面链接到子答案页

这种类型的常见问题解答有一个主题页面，其中每个主题链接到不同页面上的一组相应的问题和答案。 QnA Maker 会抓取所有链接页面，以提取相应的问题&答案。

下面是主题页面的示例，该页面包含指向不同页面中常见问题解答部分的链接。

 ![知识库的深层链接常见问题解答页示例](./media/qnamaker-concepts-datasources/topics-faq.png)

## <a name="support-urls"></a>支持 URL

QnA Maker 可以处理半结构化支持网页，例如，介绍如何执行给定任务、如何诊断和解决给定问题以及适用于给定流程的最佳做法的网文。 提取最适用于结构清晰且具有分层标题的文档。

> [!NOTE]
> 提取支持文章是一项新功能，并且处于早期阶段。 它最适用于结构良好且未包含复杂页眉/页脚的简单页面。

![QnA Maker 支持从在分层标题中提供了清晰结构的半结构化网页进行提取](./media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)