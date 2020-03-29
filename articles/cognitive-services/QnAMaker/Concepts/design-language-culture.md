---
title: 语言设计 - QnA 制造商
description: QnA Maker 资源和该资源中的所有知识库都支持单一语言。 单一语言是为查询提供最佳答案结果所必需的。
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 5cb1dcd35649debbafd2e234606ad4c9d6906ea6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843416"
---
# <a name="design-knowledge-base-for-content-language"></a>设计内容语言知识库

QnA Maker 资源以及该资源中的所有知识库都支持单一语言。 单一语言是为查询提供最佳答案结果所必需的。

## <a name="single-language-per-resource"></a>每个资源的单一语言

QnA 制造商对语言支持的注意事项：

* QnA Maker 服务及其所有知识库仅支持一种语言。
* 创建服务的第一个知识库时，将显式设置语言
* 创建知识库时从添加的文件和 URL 确定语言
* 无法为服务中的任何其他知识库更改语言
* 认知搜索服务（ranker #1）和 QnA Maker 服务（ranker #2）使用该语言来生成查询的最佳答案

## <a name="supporting-multiple-languages"></a>支持多种语言

如果需要支持包含多种语言的知识库系统，可以选择以下方法之一：

* 在将问题发送到知识库之前，使用[翻译文本服务](../../translator/translator-info-overview.md)将问题翻译成单一语言。 这使您可以专注于单一语言的质量以及备用问题和答案的质量。
* 为每个语言创建 QnA Maker 资源以及该资源内的知识库。 这允许您管理单独的备用问题和回答每种语言更细微的文本。 这为您提供了更大的灵活性，但当问题或答案在所有语言之间发生变化时，需要更高的维护成本。

查看 QnA 制造商[支持的语言](../overview/language-support.md)。

### <a name="support-each-language-with-a-qna-maker-resource"></a>使用 QnA Maker 资源支持每种语言

* 为每种语言创建 QnA Maker 资源
* 仅添加该语言的文件和 URL
* 使用资源的命名约定来标识语言。 法语文档的所有`qna-maker-fr`知识库都有一个示例

## <a name="next-steps"></a>后续步骤

了解如何[查询](query-knowledge-base.md)知识库以寻找答案。