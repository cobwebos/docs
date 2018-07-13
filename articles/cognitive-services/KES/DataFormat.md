---
title: 知识探索服务 API 中的数据格式 | Microsoft Docs
description: 了解认知服务中知识探索服务 (KES) API 中的数据格式。
services: cognitive-services
author: bojunehsu
manager: stesp
ms.service: cognitive-services
ms.component: knowledge-exploration
ms.topic: article
ms.date: 03/26/2016
ms.author: paulhsu
ms.openlocfilehash: a763505ac6458d68df74ae73e71029b81202ec8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365426"
---
# <a name="data-format"></a>数据格式
数据文件描述要建立索引的对象的列表。
文件中的每一行都使用 UTF-8 编码指定 [JSON 格式](http://json.org/)的对象的属性值。
除了在[模式](SchemaFormat.md)中定义的属性之外，每个对象都有一个可选的“logprob”属性，用于指定对象之间的相对对数概率。
当服务按概率递减的顺序返回对象时，我们可以使用“logprob”来指示匹配对象的返回顺序。
假定概率 *p* 介于 0 和 1 之间，相应的对数概率可以计算为 log(*p*)，其中 log() 是自然对数函数。
如果没有为 logprob 指定值，则使用默认值 0。

```json
{"logprob":-5.3, "Title":"latent dirichlet allocation", "Year":2003, "Author":{"Name":"david m blei", "Affiliation":"uc berkeley"}, "Author":{"Name":"andrew y ng", "Affiliation":"stanford"}, "Author":{"Name":"michael i jordan", "Affiliation":"uc berkeley"}}
{"logprob":-6.1, "Title":"probabilistic latent semantic indexing", "Year":1999, "Author":{"Name":"thomas hofmann", "Affiliation":"uc berkeley"}}
...
```

对于 String、GUID 和 Blob 属性，该值表示为带引号的 JSON 字符串。  对于数值属性（Int32、Int64、Double），该值表示为 JSON 数字。  对于复合属性，该值是用于指定子属性值的 JSON 对象。  为了更快地构建索引，可通过降低对数概率来预先分类对象。

通常，一个属性可能具有 0 个或更多个值。  如果属性没有任何值，则我们只需将其从 JSON 中删除即可。  如果属性具有 2 个或更多个值，则我们可以在 JSON 对象中重复属性值对。  或者，我们可以将包含多个值的 JSON 数组分配给该属性。

```json
{"logprob":0, "Title":"0 keyword"}
{"logprob":0, "Title":"1 keyword", "Keyword":"foo"}
{"logprob":0, "Title":"2 keywords", "Keyword":"foo", "Keyword":"bar"}
{"logprob":0, "Title":"2 keywords (alt)", "Keyword":["foo", "bar"]}
```
