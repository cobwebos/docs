---
title: 语言 QnA Maker 的设计
description: 此资源中的 QnA Maker 资源和所有知识库都支持一种语言。 需要使用一种语言来提供查询的最佳答案。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843416"
---
# <a name="design-knowledge-base-for-content-language"></a>内容语言的设计知识库

此资源中的 QnA Maker 资源和所有知识库都支持一种语言。 需要使用一种语言来提供查询的最佳答案。

## <a name="single-language-per-resource"></a>每个资源一种语言

语言支持 QnA Maker 注意事项：

* QnA Maker 服务及其所有知识库仅支持一种语言。
* 当创建服务的第一个知识库时，将显式设置语言
* 语言由创建知识库时添加的文件和 Url 确定。
* 不能为服务中的任何其他知识库更改此语言
* 语言由认知搜索服务（ranker #1）和 QnA Maker 服务（ranker #2）用来生成查询的最佳答案

## <a name="supporting-multiple-languages"></a>支持多种语言

如果需要支持包含多种语言的知识库系统，可选择以下方法之一：

* 在向知识库发送问题之前，请使用[翻译文本服务](../../translator/translator-info-overview.md)将问题转换为一种语言。 这使您可以专注于一种语言的质量以及备用问题和答案的质量。
* 为每种语言创建一个 QnA Maker 资源和该资源中的知识库。 这使你可以管理单独的替代问题和答案文本，这些问题对于每种语言更加微妙。 这为你提供了更大的灵活性，但在所有语言中的问题或答案发生变化时需要更高的维护成本。

查看 QnA Maker[支持的语言](../overview/language-support.md)。

### <a name="support-each-language-with-a-qna-maker-resource"></a>使用 QnA Maker 资源支持每种语言

* 为每种语言创建 QnA Maker 资源
* 仅添加该语言的文件和 Url
* 为资源使用命名约定来标识语言。 例如，`qna-maker-fr` 适用于法语文档的所有知识库。

## <a name="next-steps"></a>后续步骤

了解有关如何查询知识库以获得答案的[概念](query-knowledge-base.md)。