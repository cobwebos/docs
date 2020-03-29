---
title: 如何使用修饰标记来突出显示文本 | 必应 Web 搜索 API
titleSuffix: Azure Cognitive Services
description: 了解如何通过必应 Web 搜索 API 使用文本修饰及在搜索结果中点击突出显示。
services: cognitive-services
author: swhite-msft
manager: nitinme
ms.assetid: 5365B568-EA55-4D97-8FBE-0AF60158D4D5
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: scottwhi
ms.openlocfilehash: a6d394fec6e7cf0a230f61ad05c236a1f84dad9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "68854004"
---
# <a name="using-decoration-markers-to-highlight-text"></a>使用修饰标记来突出显示文本

必应支持命中突出显示，该突出显示在某些答案的显示字符串中标记查询词（或必应认为相关的其他术语）。 例如，网页结果的`name`、`displayUrl`和`snippet`字段可能包含标记的查询术语。 

默认情况下，必应不在显示字符串中包括突出显示标记。 要启用标记，请在请求中包括`textDecorations`查询参数并将其设置为`true`。

## <a name="hit-highlighting-example"></a>点击突出显示示例

下面的示例显示了 的`Sailing Dinghy`Web 结果。 必应使用 E000 和 E001 Unicode 字符标记了查询术语的开始和结束。
  
![命中词突出显示](./media/cognitive-services-bing-web-api/bing-hit-highlighting.png) 

在用户界面中显示结果之前，请将 Unicode 字符替换为适合您的显示格式的字符。

## <a name="marker-formatting"></a>标记格式

必应提供了使用 Unicode 字符或 HTML 标记作为标记的选项。 要指定要使用的标记，请包括[文本格式](https://docs.microsoft.com/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#textformat)查询参数： 

| “值”             | 标记                       |
|-------------------|------------------------------|
| `textFormat=Raw`  | 单码字符（默认） |
| `textFormat=HTML` | HTML 字符              |

## <a name="additional-text-decorations"></a>其他文本装饰

必应可以返回几个不同的文本装饰。 例如，`Computation`答案可以包含`log(2)``expression`字段中查询术语的子标标记。

![计算标记](./media/cognitive-services-bing-web-api/bing-markers-computation.png) 

如果请求未指定修饰，则`expression`该字段将包含`log10(2)`。 

如果是`textDecorations``true`，必应可在显示答案字符串中包含以下标记。 如果没有等效的 HTML 标记，则表单元格为空。

|Unicode|HTML|描述
|-|-|-
|U+E000|\<b>|表示查询词的开头（命中词突出显示）
|U+E001|\</b>|表示查询词的结尾
|U+E002|\<i>|表示斜体内容的开头 
|U+E003|\</i>|表示斜体内容的结尾
|U+E004|\<br/>|表示换行
|U+E005||表示电话号码的开头
|U+E006||表示电话号码的结尾
|U+E007||表示地址的开头
|U+E008||表示地址的结尾
|U+E009|\&nbsp;|表示不间断的空格
|U+E00C|\<strong>|表示粗体内容的开头
|U+E00D|\</strong>|表示粗体内容的结尾
|U+E00E||表示其背景颜色应比其周围背景颜色浅的内容的开头
|U+E00F||表示其背景颜色应比其周围背景颜色浅的内容的结尾
|U+E010||表示其背景颜色应比其周围背景颜色深的内容的开头
|U+E011||表示其背景颜色应比其周围背景颜色深的内容的结尾
|U+E012|\<del>|表示应删除的内容的开头
|U+E013|\</del>|表示应删除的内容的结尾
|U+E016|\<sub>|表示下标内容的开头
|U+E017|\</sub>|表示下标内容的结尾
|U+E018|\<sup>|表示上标内容的开头
|U+E019|\</sup>|表示上标内容的结尾

## <a name="next-steps"></a>后续步骤

* [什么是必应 Web 搜索 API？](overview.md) 
* [调整和裁剪缩略图](resize-and-crop-thumbnails.md)