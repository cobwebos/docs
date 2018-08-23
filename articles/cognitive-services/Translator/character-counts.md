---
title: Microsoft 文本翻译 API 字符计数 | Microsoft Docs
description: Microsoft 文本翻译 API 如何计算字符数。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: 1b4987509c17e4064d7c54608395e272efa8de3b
ms.sourcegitcommit: 744747d828e1ab937b0d6df358127fcf6965f8c8
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/16/2018
ms.locfileid: "41929952"
---
# <a name="how-the-microsoft-translator-text-api-counts-characters"></a>Microsoft 文本翻译 API 如何计算字符数

Microsoft Translator 对输入的每个字符进行计数。 Unicode 意义上的字符，而非字节。 Unicode 代理项计为两个字符。 空格和标记计为字符。 响应的长度无关紧要。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗。 但是，我们希望 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 Microsoft 保留开始对 Detect 和 BreakSentence 计数的权利。 

有关字符计数的详细信息，请参阅 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)。
