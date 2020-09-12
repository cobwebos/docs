---
title: 语言支持 - QnA Maker
titleSuffix: Azure Cognitive Services
description: 区域性列表（QnA Maker 支持的知识库的自然语言）。 请勿将多种语言混合在同一个知识库中。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: reference
ms.date: 09/24/2019
ms.openlocfilehash: c990b6980dea871679b0b301e293e4fb94748db7
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650896"
---
# <a name="language-support-for-a-qna-maker-resource-and-knowledge-bases"></a>QnA Maker 资源和知识库的语言支持

在资源中创建第一个知识库时，将选择服务语言。 资源中的所有其他知识库必须采用相同的语言。 

该语言确定 QnA Maker 提供的结果与用户查询的相关关系。 此资源中的 QnA Maker 资源和所有知识库都支持一种语言。 需要使用一种语言来提供查询的最佳答案。

## <a name="single-language-per-resource"></a>每个资源一种语言

考虑以下情况：

* QnA Maker 服务及其所有知识库仅支持一种语言。
* 当创建服务的第一个知识库时，将显式设置语言
* 语言由创建知识库时添加的文件和 Url 确定。
* 不能为服务中的任何其他知识库更改此语言
* 语言由认知搜索服务使用 (ranker #1) 和 QnA Maker 服务 (ranker #2) 来生成查询的最佳答案

## <a name="supporting-multiple-languages"></a>支持多种语言

如果需要支持包含多种语言的知识库系统，可以执行以下操作：

* 使用 [转换器服务](../../translator/translator-info-overview.md) 将问题转换为一种语言，然后将问题发送到知识库。 这使您可以专注于一种语言的质量以及备用问题和答案的质量。
* 为每种语言创建一个 QnA Maker 资源和该资源中的知识库。 这使你可以管理单独的替代问题和答案文本，这些问题对于每种语言更加微妙。 这为你提供了更大的灵活性，但在所有语言中的问题或答案发生变化时需要更高的维护成本。

查看 QnA Maker [支持的语言](../overview/language-support.md) 。

### <a name="support-each-language-with-a-qna-maker-resource"></a>使用 QnA Maker 资源支持每种语言

* 为每种语言创建 QnA Maker 资源
* 仅添加该语言的文件和 Url
* 为资源使用命名约定来标识语言。 示例适用 `qna-maker-fr` 于法语文档的所有知识库


## <a name="languages-supported"></a>支持的语言

以下列表包含 QnA Maker 资源支持的语言。 

|语言|
|--|
|阿拉伯语|
|亚美尼亚语|
|Bangla|
|巴斯克语|
|保加利亚语|
|加泰罗尼亚语|
|中文_简体|
|中文_繁体|
|克罗地亚语|
|捷克语|
|丹麦语|
|荷兰语|
|英语|
|爱沙尼亚语|
|芬兰语|
|法语|
|加利西亚语|
|德语|
|希腊语|
|古吉拉特语|
|希伯来语|
|Hindi|
|匈牙利语|
|冰岛语|
|印度尼西亚语|
|爱尔兰语|
|意大利语|
|日语|
|卡纳达语|
|韩语|
|拉脱维亚语|
|立陶宛语|
|马拉雅拉姆语|
|马来语|
|挪威语|
|波兰语|
|葡萄牙语|
|旁遮普语|
|罗马尼亚语|
|俄语|
|塞尔维亚语_西里尔文|
|塞尔维亚语_拉丁语|
|斯洛伐克语|
|斯洛文尼亚语|
|西班牙语|
|瑞典语|
|泰米尔语|
|泰卢固语|
|泰语|
|土耳其语|
|乌克兰语|
|乌尔都语|
|越南语|

## <a name="query-matching-and-relevance"></a>查询匹配和相关性
QnA Maker 依赖于 [Azure 认知搜索语言分析器](https://docs.microsoft.com/rest/api/searchservice/language-support) 来提供结果。

尽管 Azure 认知搜索功能对于支持的语言是相同的，但 QnA Maker 有一个位于 Azure 搜索结果之上的其他 ranker。 在此 ranker 模型中，使用以下语言的一些特殊语义和基于 word 的功能。

|具有其他 ranker 的语言|
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

此附加排名是 QnA Maker 的 ranker 的内部工作。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [语言选择](../how-to/language-knowledge-base.md)
