---
title: 词对齐信息 - 文本翻译 API
titlesuffix: Azure Cognitive Services
description: 从文本翻译 API 接收词对齐信息。
services: cognitive-services
author: Jann-Skotdal
manager: cgronlun
ms.service: cognitive-services
ms.component: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: 16334dbcb4631bd927069620e73c1c4474ff99fa
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2018
ms.locfileid: "46126252"
---
# <a name="how-to-receive-word-alignment-information"></a>如何接收词对齐信息

## <a name="receiving-word-alignment-information"></a>接收词对齐信息
若要接收对齐信息，请使用 Translate 方法，并包含可选的 includeAlignment 参数。

## <a name="alignment-information-format"></a>对齐信息格式
对齐将作为以下格式的字符串值返回给源的每个词。 每个词的信息由一个空格分隔，其中包括非空格分隔的语言（脚本），比如中文：

[[SourceTextStartIndex]:[SourceTextEndIndex]–[TgtTextStartIndex]:[TgtTextEndIndex]] *

对齐字符串示例：“0:0-7:10 1:2-11:20 3:4-0:3 3:4-4:6 5:5-21:21”。

换而言之，冒号分隔开始和结束索引，连字符分隔语言，空格分隔词。 一个词可能与另一种语言中的 0 个、1 个或多个词对齐，而对齐的词可能是非连续的。 当没有可用的对齐信息时，Alignment 元素将为空。 在这种情况下，该方法不会返回任何错误。

## <a name="restrictions"></a>限制
目前，仅针对语言对的子集返回对齐：
* 从英语到任何其他语言；
* 从任何其他语言到英语，简体中文、繁体中文和拉脱维亚语到英语除外
* 从日语到韩语或从韩语到日语；如果句子是预录翻译，则不会收到对齐信息。 预录翻译示例有“This is a test”、“I love you”，以及其他高频率句子。

## <a name="example"></a>示例

示例 JSON

```json
[
  {
    "translations": [
      {
        "text": "Kann ich morgen Ihr Auto fahren?",
        "to": "de",
        "alignment": {
          "proj": "0:2-0:3 4:4-5:7 6:10-25:30 12:15-16:18 17:19-20:23 21:28-9:14 29:29-31:31"
        }
      }
    ]
  }
]
```
