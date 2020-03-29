---
title: 语言概念 - QnA 制造商
titleSuffix: Azure Cognitive Services
description: QnA Maker 支持多种语言版本的知识库内容。 但是，每个 QnA Maker 服务应保留一种语言。 创建的第一个知识库针对特定的 QnA Maker 服务，设置该服务的语言。
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/24/2019
ms.author: diberry
ms.openlocfilehash: 38701e8bbef1c5d78eca2242105e81fe7261c0f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220629"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>QnA Maker 的知识库内容的语言支持

在创建资源中的第一个知识库时，将选择服务的语言。 资源中的所有附加知识库必须使用同一语言。

语言确定 QnA Maker 为响应用户查询而提供的结果的相关性。

## <a name="one-language-for-all-knowledge-bases-in-resource"></a>资源中所有知识库的一种语言

QnA Maker 允许您为 QnA 服务选择语言，同时创建第一个知识库。 对于 QnA Maker 资源中的所有知识库，它们都必须使用同一语言。 无法更改此语言。

在一个资源中以不同语言创建知识库会对 QnA Maker 响应用户查询的结果的相关性产生负面影响。

查看[支持的语言](../overview/language-support.md#languages-supported)列表以及语言如何影响[匹配和相关性](#query-matching-and-relevance)。

## <a name="select-language-when-creating-first-knowledge-base"></a>创建第一个知识库时选择语言

语言选择是创建资源中第一个知识库的步骤的一部分。

![为第一知识库选择语言的 QnA Maker 门户屏幕截图](../media/language-support/select-language-when-creating-knowledge-base.png)

## <a name="query-matching-and-relevance"></a>查询匹配和相关性
QnA Maker 依赖于[Azure 认知搜索语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support)来提供结果。

虽然 Azure 认知搜索功能与支持的语言不相上下，但 QnA Maker 具有位于 Azure 搜索结果上方的附加排名。 在此排名模型中，我们在以下语言中使用一些特殊的语义和基于单词的功能。

|具有附加排名语言的语言|
|--|
|中文|
|捷克语|
|荷兰语|
|英语|
|法语|
|德语|
|匈牙利语|
|意大利语|
|日语|
|韩语|
|波兰语|
|葡萄牙语|
|西班牙语|
|瑞典语|

此附加排名是 QnA 制造商的记号的内部工作。

## <a name="verify-language"></a>验证语言

您可以在 QnA Maker 中的服务设置页面验证 QnA Maker 资源的语言。

![服务设置页面的 QnA Maker 门户屏幕截图](../media/language-support/language-knowledge-base.png)


## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [迁移知识库](../Tutorials/migrate-knowledge-base.md)
