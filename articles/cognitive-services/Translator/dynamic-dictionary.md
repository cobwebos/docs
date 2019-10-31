---
title: 动态字典 - 文本翻译 API
titleSuffix: Azure Cognitive Services
description: 如何使用文本翻译 API 的动态字典功能。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: swmachan
ms.openlocfilehash: c268616c015c1c8735b2bdddc057f235d02cdc2a
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73161740"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用动态字典

若已知道要应用于某个单词或短语的翻译，可以在请求中将其作为标记提供。 动态词典仅适用于复合名词，例如专有名称和产品名称。

**语法：**

< mstrans：字典转换 = "短语翻译" > 短语 </mstrans：字典 >

**要求**

* `From` 和 `To` 语言必须不同。 
* 你必须在 API 翻译请求中包含 `From` 参数，而不是使用自动检测功能。 

**示例：en-de：**

源输入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目标输出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

无论使用还是不使用 HTML 模式，此功能都以相同的方式工作。

应尽量少使用此功能。 对翻译进行自定义时，一个合适且要好得多的方法是使用自定义翻译工具。 自定义翻译工具能够充分利用上下文和统计概率。 如果必须或能够创建在上下文中显示工作或短语的训练数据，则会得到更好的结果。 可以在 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) 中找到有关自定义翻译的详细信息。
