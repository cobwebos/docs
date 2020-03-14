---
title: 关键字命名准则-语音服务
titleSuffix: Azure Cognitive Services
description: 创建有效的关键字对于确保你的设备能够一致准确地做出响应至关重要。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370666"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>创建有效关键字的准则

创建有效的关键字对于确保你的设备能够一致准确地做出响应至关重要。 自定义关键字是区分设备并增强品牌的有效方法。 本文介绍了一些用于创建有效关键字的指导原则。

## <a name="choose-an-effective-keyword"></a>选择有效关键字

选择关键字时，请考虑以下准则：

> [!div class="checklist"]
> * 关键字应为英语单词或短语。
> * 说该词应该不超过两秒钟。
> * 4 到 7 个音节的单词效果最好。 例如，"你好，Computer" 是一个不错的关键字。 而只是“Hey”则是一个糟糕的唤醒词。
> * 关键字应遵循常见的英语发音规则。
> * 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，"computerama" 可能是一个不错的关键字。
> * 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。
> * 避免使用可能包含备用发音的关键字。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。
> * 不要使用特殊字符、符号或数字。 例如，"中转 #" 和 "20 + 猫" 可能是问题的关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果选择 "商标字" 一词作为关键字，请确保你拥有该商标，或者你拥有商标所有者的许可才能使用此词。 对于您选择的关键字可能产生的任何法律问题，Microsoft 不承担任何责任。

## <a name="next-steps"></a>后续步骤

了解如何[使用 Speech Studio 创建自定义关键字](speech-devices-sdk-create-kws.md)。
