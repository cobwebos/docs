---
title: 关键字命名指南 - 语音服务
titleSuffix: Azure Cognitive Services
description: 创建有效的关键字对于确保您的设备能够持续、准确地响应至关重要。
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79370666"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>创建有效关键字的指南

创建有效的关键字对于确保您的设备能够持续、准确地响应至关重要。 自定义关键字是区分您的设备并增强品牌的有效方法。 在本文中，您将学习创建有效关键字的一些指导原则。

## <a name="choose-an-effective-keyword"></a>选择有效的关键字

选择关键字时，请考虑以下准则：

> [!div class="checklist"]
> * 您的关键字应为英语单词或短语。
> * 说该词应该不超过两秒钟。
> * 4 到 7 个音节的单词效果最好。 例如，"嘿，计算机"是一个很好的关键字。 而只是“Hey”则是一个糟糕的唤醒词。
> * 关键字应遵循常见的英语发音规则。
> * 遵循常见英语发音规则的独特或甚至虚构的单词可以减少误报。 例如，"计算机"可能是一个很好的关键字。
> * 不要选择常用词。 例如，“eat”和“go”是人们在日常对话中经常说的词。 它们可能是设备的错误触发器。
> * 避免使用可能具有替代发音的关键字。 用户必须知道“正确”的发音才能使他们的设备做出响应。 例如，“509”的发音可以是“five zero nine”、“five oh nine”或“five hundred and nine”。 “R.E.I.” 发音可以是“r-e-i”或“ray”。 “Live”的发音可以是“/līv/”或“/liv/”。
> * 不要使用特殊字符、符号或数字。 例如，"Go+"和"20 + 猫"可能是有问题的关键字。 但是，“go sharp”或“twenty plus cats”可行。 你仍然可以在品牌中使用这些符号，并通过营销和文档来强化正确的发音。

> [!NOTE]
> 如果您选择商标字作为关键字，请确保您拥有该商标，或者您拥有商标所有者的许可才能使用该字。 对于因您选择的关键字而可能出现的任何法律问题，Microsoft 概不负责。

## <a name="next-steps"></a>后续步骤

了解如何[使用语音工作室创建自定义关键字](speech-devices-sdk-create-kws.md)。
