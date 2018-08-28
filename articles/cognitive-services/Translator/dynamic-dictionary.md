---
title: Microsoft 文本翻译 API 动态字典 | Microsoft Docs
description: 如何使用 Microsoft 文本翻译 API 的动态字典功能。
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: dbc754093827730b8709d67f314e5b327518ef50
ms.sourcegitcommit: 17fe5fe119bdd82e011f8235283e599931fa671a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/11/2018
ms.locfileid: "41929951"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>如何使用 Microsoft 文本翻译 API 的动态字典功能

如果你已知道要应用于某个单词或短语的翻译，可以在请求中将其提供为标记。 动态词典仅适用于复合名词，例如专有名称和产品名称。 

**语法：** 

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**示例：en-de：**

源输入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目标输出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

无论使用还是不使用 HTML 模式，此功能都以相同的方式工作。 

应尽量少使用此功能。 对翻译进行自定义时，一个合适且要好得多的方法是使用自定义翻译工具。 自定义翻译工具能够充分利用上下文和统计概率。 如果你有或可以创建在上下文中显示你的工作或短语的训练数据，则会得到好得多的结果。 可以在 [http://aka.ms/CustomTranslator](http://aka.ms/CustomTranslator) 中找到有关自定义翻译的详细信息。

