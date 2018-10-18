---
title: Web 语言模型 API 概述
titleSuffix: Azure Cognitive Services
description: Microsoft 认知服务中的 Web 语言模型 API 提供了用于自然语言处理的最先进工具。
services: cognitive-services
author: piyushbehre
manager: cgronlun
ms.service: cognitive-services
ms.component: web-language-model
ms.topic: overview
ms.date: 08/12/2016
ms.author: pibehre
ROBOTS: NOINDEX
ms.openlocfilehash: 79b126fc33175b7cd6df96ab07cd7b726d6065a7
ms.sourcegitcommit: f20e43e436bfeafd333da75754cd32d405903b07
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2018
ms.locfileid: "49389932"
---
# <a name="what-is-the-web-language-model-api-preview"></a>什么是 Web 语言模型 API？ （预览版）

> [!IMPORTANT]
> Web 语言模型预览版于 2018 年 8 月 9 日停止使用。 我们建议使用 [Azure 机器学习文本分析模块](https://docs.microsoft.com/azure/machine-learning/studio-module-reference/text-analytics)进行文本处理和分析。

Microsoft Web 语言模型 API 是一种基于 REST 的云服务，它提供了用于自然语言处理的最先进工具。 使用此 API，通过基于必应在美国市场收集的网络级语料库训练的语言模型，你的应用程序可以利用大数据的力量。

这些平滑回退 N-gram 语言模型最多支持 5 阶马尔可夫链，并且是基于以下语料库训练的：

- 网页正文文本
- 网页标题文本
- 网页定位标记文本
- Web 搜索查询文本

Web 语言模型 API 支持四个查找操作：

1. 单词序列的联合 (log10) 概率。
2. 根据给定的先前单词的序列，确定某个单词的联合 (log10) 概率。
3. 最可能接在给定词语序列后的词语列表（结束词）。
4. 未含空格的字符串的分词。

## <a name="getting-started"></a>入门

1. 订阅服务。
2. 下载 [SDK](https://www.github.com/microsoft/cognitive-weblm-windows)。
3. 运行 SDK 示例代码。
4. 有关终结点的完整详细信息，包括采用各种语言的代码片段，请参阅 [API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/55de9ca4e597ed1fd4e2f104)。

## <a name="underlying-technology"></a>基础技术

以下文章提供了有关这些语言模型的开发的详细信息，并且应当在使用此服务的研究出版物中引用：

- [Microsoft Web N-gram 语料库和应用程序概述](http://research.microsoft.com/apps/pubs/default.aspx?id=130762)，NAACL-HLT 2010

有关引用此作品的文章的当前列表，请单击[此处](https://academic.microsoft.com/#/search?iq=And%28Ty%3D'0'%2CRId%3D2145833060%29&q=papers%20citing%20an%20overview%20of%20microsoft%20web%20n%20gram%20corpus%20and%20applications&filters=&from=0&sort=0)。
