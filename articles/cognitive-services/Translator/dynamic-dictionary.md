---
title: 动态字典-转换器
titleSuffix: Azure Cognitive Services
description: 本文介绍如何使用 Azure 认知服务转换器的动态字典功能。
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: de45867e717f001ab54e16c4b21f04494affd326
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996975"
---
# <a name="how-to-use-a-dynamic-dictionary"></a>如何使用动态字典

若已知道要应用于某个单词或短语的翻译，可以在请求中将其作为标记提供。 动态字典仅适用于复合名词，如正确的名称和产品名称。

**语法：**

<mstrans：字典转换 = "短语翻译" >短语</mstrans：字典>

**要求：**

* `From`和 `To` 语言必须包含英语和另一种受支持的语言。 
* 你必须 `From` 在 API 翻译请求中包含参数，而不是使用自动检测功能。 

**示例：en-de：**

源输入：`The word <mstrans:dictionary translation=\"wordomatic\">word or phrase</mstrans:dictionary> is a dictionary entry.`

目标输出：`Das Wort "wordomatic" ist ein Wörterbucheintrag.`

无论使用还是不使用 HTML 模式，此功能都以相同的方式工作。

请谨慎使用此功能。 自定义翻译的更好方法是使用自定义转换器。 自定义翻译工具能够充分利用上下文和统计概率。 如果必须或能够创建在上下文中显示工作或短语的训练数据，则会得到更好的结果。 可以在中找到有关自定义转换器的详细信息 [https://aka.ms/CustomTranslator](https://aka.ms/CustomTranslator) 。