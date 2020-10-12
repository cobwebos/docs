---
title: 字符计数 - 翻译器
titleSuffix: Azure Cognitive Services
description: 本文介绍了 Azure 认知服务翻译器如何计算字符数，以便你了解它如何引入内容。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 7fad90cf47591b5ffab4232c2be3180138738f01
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "83992822"
---
# <a name="how-the-translator-counts-characters"></a>翻译器如何计算字符数

翻译器将输入文本的每个 Unicode 码位计为一个字符。 文本到某种语言的每个翻译都计为单独的翻译，即使在单个 API 调用中发出翻译为多种语言的请求时也是如此。 响应的长度无关紧要。

计数对象为：

* 在请求正文中传递到翻译器的文本
   * `Text`（如果使用 Translate、Transliterate 和 Dictionary Lookup 方法）
   * `Text` 和 `Translation`（如果使用 Dictionary Examples 方法）
* 所有标记：请求正文文本字段内的 HTML、XML 标记等。 用于生成请求的 JSON 表示法（例如，“Text:”）不计入。
* 单个字母
* 标点
* 空格、制表符、标记和任何类型的空格字符
* Unicode 中定义的每个码位
* 重复的翻译（即使之前已翻译相同的文本）

对于基于表意文字（例如中文汉字和日文汉字）的脚本，翻译器服务仍会对 Unicode 码位计数，每个表意文字计为一个字符。 异常：Unicode 代理项计为两个字符。

请求、单词、字节或句子的数量在字符计数中不相关。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗。 但是，我们希望 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 如果发出的 Detect 或 BreakSentence 调用的数量是其他计数方法数量的 100 倍，Microsoft 保留限制使用 Detect 和 BreakSentence 方法的权利。

有关字符计数的详细信息，请参阅[翻译器常见问题解答](https://www.microsoft.com/en-us/translator/faq.aspx)。
