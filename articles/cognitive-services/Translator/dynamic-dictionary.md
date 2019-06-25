---
title: 动态字典 - 文本翻译 API
titlesuffix: Azure Cognitive Services
description: 如何使用文本翻译 API 的动态字典功能。
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 06/04/2019
ms.author: v-pawal
ms.openlocfilehash: d3a77ee9273e9f587b6157bb6ed2294865b1fca5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66514264"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用动态字典

若已知道要应用于某个单词或短语的翻译，可以在请求中将其作为标记提供。 动态词典仅适用于复合名词，例如专有名称和产品名称。

**语法：**

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**示例：en-de：**

源输入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目标输出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

无论使用还是不使用 HTML 模式，此功能都以相同的方式工作。

应尽量少使用此功能。 对翻译进行自定义时，一个合适且要好得多的方法是使用自定义翻译工具。 自定义翻译工具能够充分利用上下文和统计概率。 如果必须或能够创建在上下文中显示工作或短语的训练数据，则会得到更好的结果。 可以在 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) 中找到有关自定义翻译的详细信息。
