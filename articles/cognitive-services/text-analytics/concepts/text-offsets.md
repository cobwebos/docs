---
title: 文本分析 API 中的文本偏移量
titleSuffix: Azure Cognitive Services
description: 了解多语言和表情符号编码导致的偏移量。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 03/09/2020
ms.author: aahi
ms.reviewer: jdesousa
ms.openlocfilehash: 6e404c710a244f06676edf50c3f5c95a7d681e35
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79219232"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>文本分析 API 输出中的文本偏移量

多语言和表情符号支持将使用多个[码位](https://wikipedia.org/wiki/Code_point)来表示单个显示字符（称为字形）的 Unicode 编码。 例如，表情符号（如🌷和 👍）可能会使用多个字符来为视觉对象属性（如肤色）的其他字符撰写形状。 同样，印地语文字 `अनुच्छेद` 编码为五个字母和三个组合标记。

由于可能的多语言和表情符号编码的长度不同，文本分析 API 可能会在响应中返回偏移量。

## <a name="offsets-in-the-api-response"></a>API 响应中的偏移量。 

每当返回偏移量时，API 响应（如[命名实体识别](../how-tos/text-analytics-how-to-entity-linking.md)或[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)）都请记住以下事项：

* 响应中的元素可能特定于所调用的终结点。 
* HTTP POST/GET 负载以[utf-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)编码，这可能是客户端编译器或操作系统的默认字符编码，也可能不是。
* 偏移量是指基于[Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)标准的字形计数，而不是字符计数。

## <a name="extracting-substrings-from-text-with-offsets"></a>从带有偏移量的文本中提取子字符串

当使用基于字符的子字符串方法（例如 .NET [substring （）方法）](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8)时，偏移可能会导致问题。 一个问题是，偏移可能导致 substring 方法在多字符字形编码的中间结束，而不是结束。

在 .NET 中，应考虑使用[system.globalization.stringinfo>](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)类，这使你可以将字符串用作一系列文本元素，而不是单个字符对象。 你还可以在首选的软件环境中查找字形拆分器库。 

为方便起见，文本分析 API 还会返回这些文本元素。

## <a name="see-also"></a>另请参阅

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)
