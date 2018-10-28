---
title: 字符计数 - 文本翻译 API
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
ms.openlocfilehash: 8e04158d34765b8a077fc56f2108ea6d7b4b03a6
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2018
ms.locfileid: "49649188"
---
# <a name="how-the-translator-text-api-counts-characters"></a>文本翻译 API 如何计算字符数

文本翻译 API 对输入的每个字符进行计数。 Unicode 意义上的字符，而非字节。 Unicode 代理项计为两个字符。 空格和标记计为字符。 响应的长度无关紧要。

对 Detect 和 BreakSentence 方法的调用不计入字符消耗。 但是，我们希望 Detect 和 BreakSentence 方法的调用次数与其他计数函数的使用次数成合理的比例。 Microsoft 保留开始对 Detect 和 BreakSentence 计数的权利。

有关字符计数的详细信息，请参阅 [Microsoft Translator FAQ](https://www.microsoft.com/en-us/translator/faq.aspx)。
