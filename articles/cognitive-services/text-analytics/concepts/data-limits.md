---
title: 文本分析 API 的数据限制
titleSuffix: Azure Cognitive Services
description: Azure 认知服务中的文本分析 API 的数据限制。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 08/14/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: 068c2dc698e9f0b6d6f2f6486dff863c1343b178
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "88258284"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>文本分析 API 的数据和速率限制
<a name="data-limits"></a>

使用本文查找将数据发送到文本分析 API 的大小和速率限制。 

## <a name="data-limits"></a>数据限制

> [!NOTE]
> * 如果需要分析的文档的大小超出限制，则可将文本拆分成较小的文本块，然后再将其发送到 API。 
> * 文档是由文本字符构成的单个字符串。  

| 限制 | 值 |
|------------------------|---------------|
| 单个文档的最大大小 | 5,120 个字符，通过 [StringInfo.LengthInTextElements](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo.lengthintextelements) 进行度量。 也适用于运行状况文本分析容器。 |
| 整个请求的最大大小 | 1MB。 也适用于运行状况文本分析容器。 |

可以在单个请求中发送的最大文档数将取决于所使用的 API 版本和功能。

#### <a name="version-3"></a>[第 3 版](#tab/version-3)

v3 API 中已更改以下限制。 超过以下限制将生成 HTTP 400 错误代码。


| Feature | 每个请求的最大文档数 | 
|----------|-----------|
| 语言检测 | 1000 |
| 情绪分析 | 10 |
| 关键短语提取 | 10 |
| 命名实体识别 | 5 |
| 实体链接 | 5 |
| 运行状况文本分析容器 | 1000 |
#### <a name="version-2"></a>[第 2 版](#tab/version-2)

| Feature | 每个请求的最大文档数 | 
|----------|-----------|
| 语言检测 | 1000 |
| 情绪分析 | 1000 |
| 关键短语提取 | 1000 |
| 命名实体识别 | 1000 |
| 实体链接 | 1000 |

---

## <a name="rate-limits"></a>速率限制

速率限制将因[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)而异。 两个 API 版本的这些限制是相同的。 这些速率限制不适用于没有设置速率限制的运行状况文本分析容器。

| 层          | 每秒请求数 | 每分钟请求数 |
|---------------|---------------------|---------------------|
| S/多服务 | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

对每个文本分析功能的请求分别进行测量。 例如，可以同时向每个功能发送定价层的最大数量的请求。  


## <a name="see-also"></a>另请参阅

* [什么是文本分析 API](../overview.md)
* [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
