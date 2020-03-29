---
title: 文本分析 API 中的文本偏移量
titleSuffix: Azure Cognitive Services
description: 了解多语言编码和表情符号编码造成的偏移量。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219232"
---
# <a name="text-offsets-in-the-text-analytics-api-output"></a>文本分析 API 输出中的文本偏移量

多语言和表情符号支持已导致 Unicode 编码使用多个[代码点](https://wikipedia.org/wiki/Code_point)来表示单个显示的字符，称为图形学。 例如，🌷等表情符号，👍可以使用多个字符来组成具有视觉属性的其他字符（如肤色）的形状。 同样，印地语单词`अनुच्छेद`被编码为五个字母和三个组合标记。

由于可能的多语言编码和表情符号编码长度不同，文本分析 API 可能会在响应中返回偏移量。

## <a name="offsets-in-the-api-response"></a>API 响应中的偏移量。 

每当返回偏移时，API 响应（如[命名实体识别](../how-tos/text-analytics-how-to-entity-linking.md)或[情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)）请记住以下内容：

* 响应中的元素可能特定于调用的终结点。 
* HTTP POST/GET 有效负载在[UTF-8](https://www.w3schools.com/charsets/ref_html_utf8.asp)中编码，这可能是客户端编译器或操作系统上的默认字符编码。
* 偏移量是指基于[Unicode 8.0.0](https://unicode.org/versions/Unicode8.0.0)标准而不是字符计数的图目数。

## <a name="extracting-substrings-from-text-with-offsets"></a>从具有偏移量的文本中提取子字符串

使用基于字符的子字符串方法（例如 .NET[子字符串）](https://docs.microsoft.com/dotnet/api/system.string.substring?view=netframework-4.8)方法时，偏移可能会导致问题。 一个问题是，偏移量可能会导致子字符串方法以多字符图目数编码而不是结束结束结束。

在 .NET 中，请考虑使用[StringInfo](https://docs.microsoft.com/dotnet/api/system.globalization.stringinfo?view=netframework-4.8)类，该类使您能够将字符串用作一系列文本元素，而不是单个字符对象。 您还可以在首选软件环境中查找石墨分分器库。 

为了方便起见，文本分析 API 也返回这些文本元素。

## <a name="see-also"></a>请参阅

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
* [检测语言](../how-tos/text-analytics-how-to-keyword-extraction.md)
* [语言识别](../how-tos/text-analytics-how-to-language-detection.md)
