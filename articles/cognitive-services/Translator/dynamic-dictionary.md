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
ms.openlocfilehash: a18348c9786669ac41c4e149577d97cd631d5531
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366817"
---
# <a name="how-to-use-the-dynamic-dictionary-feature-of-the-microsoft-translator-text-api"></a>如何使用 Microsoft 文本翻译 API 的动态字典功能

如果你已知道要应用于某个单词或短语的翻译，可以在请求中将其提供为标记。 动态词典仅适用于复合名词，例如专有名称和产品名称。 

**语法：** 

<mstrans:dictionary translation=”translation of phrase”>phrase</mstrans:dictionary>

**示例：en-de：**

源输入：The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.

目标输出：Das Wort "wordomatic" ist ein Wörterbucheintrag.

无论使用还是不使用 HTML 模式，此功能都以相同的方式工作。 

应尽量少使用此功能。 对翻译进行自定义的合适且更好的方法是使用 Microsoft Translator Hub。 该 Hub 能够充分利用上下文和统计概率。 如果你必须或者有能力创建在上下文中显示你的工作或短语的训练数据，则会得到更好的结果。 可以在 [http://hub.microsofttranslator.com](http://hub.microsofttranslator.com) 中找到有关该 Hub 的详细信息。

