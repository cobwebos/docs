---
title: 文本分析 API 概述 - Azure 认知服务 | Microsoft Docs
description: 用于情绪分析、关键短语提取和语言检测的 Azure 认知服务中的文本分析 API。
services: cognitive-services
author: ashmaka
manager: cgronlun
ms.service: cognitive-services
ms.technology: text-analytics
ms.topic: article
ms.date: 5/02/2018
ms.author: ashmaka
ms.openlocfilehash: db5ea943f270aa512afb508668aec90cc4c90df4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366664"
---
# <a name="what-is-text-analytics-api-version-20"></a>文本分析 API 版本 2.0 是什么？

文本分析 API 是一种基于云的服务，它对原始文本提供高级自然语言处理，并且包含四项主要功能：情绪分析、关键短语提取、语言检测和实体链接。

该 API 以 [Microsoft 认知服务](https://docs.microsoft.com/azure/cognitive-services/)中的资源、云中机器学习和 AI 算法的集合为后盾，随时可在开发项目中使用。

## <a name="capabilities-in-text-analytics"></a>文本分析的功能

文本分析可能有不同的含义，但在认知服务中，文本分析 API 提供下表所述的四种分析。

| 操作| 说明 | API |
|-----------|-------------|------|
|[**情绪分析**](how-tos/text-analytics-how-to-sentiment-analysis.md) | 通过在原始文本中分析有关积极和消极情绪的线索，确定客户如何看待你的品牌或主题。 此 API 针对每个文档返回介于 0 和 1 之间的情绪评分，1 是最积极的评分。<br /> 分析模型已使用 Microsoft 提供的大量文本正文和自然语言技术进行预先训练。 对于[选定的语言](text-analytics-supported-languages.md)，该 API 可以分析和评分提供的任何原始文本，并直接将结果返回给调用方应用程序。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c9) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package)  |
|[**关键短语提取**](how-tos/text-analytics-how-to-keyword-extraction.md) | 自动提取关键短语，以快速识别要点。 例如，针对输入文本“The food was delicious and there were wonderful staff”，该 API 会返回谈话要点：“food”和“wonderful staff”。  | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) <br /> [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) |
|[**语言检测**](how-tos/text-analytics-how-to-language-detection.md) | 针对多达 120 种语言，该 API 将检测输入文本是使用哪种语言编写的，并报告请求中提交的每个文档的单个语言代码。 语言代码与表示评分强度的评分相搭配。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c7) <br />  [.NET](https://docs.microsoft.com/en-us/azure/cognitive-services/text-analytics/quickstarts/csharp#install-the-nuget-sdk-package) | 
|[**实体链接（预览版）**](how-tos/text-analytics-how-to-entity-linking.md) | 识别文本中已知的实体，并链接到 Web 上的其他信息。 将某个字词用作一个单独辨别的实体、谓词和其他单词形式时，实体链接能够予以识别和区分。 | [REST](https://westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/5ac4251d5b4ccd1554da7634) | 

## <a name="typical-workflow"></a>典型工作流

工作流非常简单：在代码中提交分析数据和处理输出。 分析器按原样使用，无需额外的配置或自定义。

1. [注册](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account)[访问密钥](how-tos/text-analytics-how-to-access-key.md)。 必须在每个请求中传递密钥。

2. [规划请求](how-tos/text-analytics-how-to-call-api.md#json-schema)，其中包含原始非结构化文本形式的 JSON 数据。

3. 将此请求发布到注册期间建立的终结点，并追加所需的资源：情绪分析、关键短语提取、语言检测或实体识别。

4. 在本地流式处理或存储响应。 根据具体的请求，结果将是情绪评分、提取的关键短语集合或语言代码。

输出将会根据 ID 以单个 JSON 文档的形式返回，其中包含发布的每个文本文档的结果。 然后，可以分析、可视化结果，或将其分类成可行的见解。

数据不会存储在你的帐户中。 文本分析 API 执行的操作是无状态的，这意味着，将会处理所提供的文本，并立即返回结果。

<a name="supported-languages"></a>

## <a name="supported-languages"></a>支持的语言

为方便查找，本部分已转移到单独的文章。 请参阅[文本分析 API 支持的语言](text-analytics-supported-languages.md)中的相关内容。

<a name="data-limits"></a>

## <a name="data-limits"></a>数据限制

所有的文本分析 API 终结点都接受原始文本数据。 当前限制为每个文档最多包含 5,000 个字符；如果需要分析更大的文档，可将它们分解成较小的区块。 如果仍然需要提高限制，请[联系我们](https://azure.microsoft.com/overview/sales-number/)讨论具体要求。

| 限制 | 值 |
|------------------------|---------------|
| 单个文档的最大大小 | 5,000 个字符，由 `String.Length` 度量。 |
| 整个请求的最大大小 | 1 MB |
| 一个请求中的文档数上限 | 1,000 个文档 |

速率限制为每分钟 100 次调用。 请注意，可在单次调用中提交大量的文档（最多 1,000 个文档）。

## <a name="unicode-encoding"></a>Unicode 编码

文本分析 API 使用 Unicode 编码来呈现文本和计算字符数。 可以 UTF-8 和 UTF-16 编码提交请求，这在字符计数方面没有可度量的差别。 Unicode 码位用作字符长度的启发因子，对文本分析数据限制的影响被视为等效。 如果你使用 `String.Length` 获取字符计数，则使用的方法也是我们用来度量数据大小的方法。

## <a name="next-steps"></a>后续步骤

首先请尝试[交互式演示](https://azure.microsoft.com/services/cognitive-services/text-analytics/)。 可以粘贴一段文本输入（最多 5,000 个字符）来检测语言（最多 120 种）、计算情绪评分，或提取关键短语。 不需要注册。

准备好直接调用 API 时：

+ [注册](how-tos/text-analytics-how-to-signup.md)访问密钥，并查看[调用 API](how-tos/text-analytics-how-to-call-api.md) 的步骤。

+ [快速入门](quickstarts/csharp.md)演练了以 C# 编写的 REST API 调用。 了解如何以少量的代码提交文本、选择分析，并查看结果。

+ [API 参考文档](//go.microsoft.com/fwlink/?LinkID=759346)提供了 API 的技术文档。 该文档支持嵌入式调用，使你能够从每个文档页调用 API。

+ [外部和社区内容](text-analytics-resource-external-community.md)提供了演示如何结合其他工具和技术使用文本分析的博客文章与视频列表。

## <a name="see-also"></a>另请参阅

 [认知服务文档页](https://docs.microsoft.com/azure/cognitive-services/)
