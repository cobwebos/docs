---
title: 文本翻译 API 字符计数
titlesuffix: Azure Cognitive Services
description: 文本翻译 API 如何计算字符数。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: v-jansko
ms.openlocfilehash: c6234a46ae55d73739dcc23110c5e0f6375c3f96
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128734"
---
# <a name="how-the-translator-text-api-counts-characters"></a>文本翻译 API 如何计算字符数

文本翻译 API 对输入的每个字符进行计数。 Unicode 意义上的字符，而非字节。 Unicode 代理项计为两个字符。 空格和标记计为字符。 响应的长度无关紧要。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗。 但是，我们希望 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 Microsoft 保留开始对 Detect 和 BreakSentence 计数的权利。 

有关字符计数的详细信息，请参阅 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)。
