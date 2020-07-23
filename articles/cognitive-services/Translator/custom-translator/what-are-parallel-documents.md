---
title: 什么是并行文档？ - 自定义翻译
titleSuffix: Azure Cognitive Services
description: 并行文档是配对的文档，其中的一个文档是另一个文档的翻译。 该对中的一个文档包含采用源语言的句子，另一个文档包含这些句子的目标语言翻译。
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.date: 05/26/2020
ms.author: swmachan
ms.topic: conceptual
ms.openlocfilehash: d397e481fcd11e534e4bb5cf98f8c402435e890e
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83997071"
---
# <a name="what-are-parallel-documents"></a>什么是并行文档？

并行文档是配对的文档，其中的一个文档是另一个文档的翻译。 该对中的一个文档包含采用源语言的句子，另一个文档包含这些句子的目标语言翻译。
不重要的是哪种语言被标记为 "source"，哪种语言被标记为 "目标" –一个并行文档可用于在任一方向训练翻译系统。

## <a name="requirements"></a>要求

你将需要至少10000个唯一对齐的并行句子来训练系统。 此限制是一种安全网络，确保你的并行句子包含足够的独特词汇来成功地训练翻译模型。 最佳做法是，持续添加更多的并行内容和重新训练以提高翻译系统的质量。 请参阅[句子对齐](https://docs.microsoft.com/azure/cognitive-services/translator/custom-translator/sentence-alignment)。

Microsoft 要求上载到自定义转换器的文档不违反第三方的版权或知识产权。 有关详细信息，请参阅[使用条款](https://azure.microsoft.com/support/legal/cognitive-services-terms/)。
使用门户上传文档不会改变文档本身的知识产权所有权。

## <a name="use-of-parallel-documents"></a>使用并行文档

系统使用并行文档来实现以下目的：

1.  了解单词、短语和句子在两种语言之间的一般映射方式。

2.  了解如何根据周围短语处理相应的上下文。 某个单词的翻译成不一定总与另一种语言的单词完全相同。

最佳做法是，确保在文档的源与目标语言版本之间建立 1 对 1 的句子对应关系。

如果项目特定于领域（类别），则文档应该与该类别中的术语相一致。 生成的翻译系统的质量取决于文档集中的句子数以及句子的质量。 文档中包含特定于类别的单词的不同用法示例越多，翻译过程中系统的表现就越好。

上传的文档专用于每个工作区，可在任意数量的项目或训练中使用。 从文档中提取的句子作为 Unicode 纯文本文件单独存储在存储库中，并可删除。 不要使用自定义翻译作为文档存储库，否则无法以上传文档时所用的格式下载这些文档。



## <a name="next-steps"></a>后续步骤

- 了解如何在自定义翻译中使用[字典](what-is-dictionary.md)。
