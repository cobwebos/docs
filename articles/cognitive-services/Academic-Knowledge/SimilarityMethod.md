---
title: 相似性方法 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 使用相似性方法计算两个字符串的学术相似性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 01/18/2017
ms.author: alch
ms.openlocfilehash: 44930ad0f941ea174d95658f220db7aa95012133
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868678"
---
# <a name="similarity-method"></a>相似性方法

相似性 REST API 用于计算两个字符串之间的学术相似性。 
<br>

REST 终结点：
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?
```

## <a name="request-parameters"></a>请求参数
参数        |数据类型      |必选 | 说明
----------|----------|----------|------------
s1        |String   |是  |要比较的字符串*
s2        |String   |是  |要比较的字符串*
<sub> *要比较的字符串的最大长度为 1 MB。</sub>
<br>
## <a name="response"></a>响应
Name | 说明
--------|---------
SimilarityScore        |浮点值表示 s1 和 s2 的余弦相似性，值接近 1.0 表示更相似，值接近 -1.0 表示不那么相似
<br>

## <a name="successerror-conditions"></a>成功/错误条件
HTTP 状态 | 原因 | 响应
-----------|----------|--------
**200**         |Success | 浮点数
**400**         | 错误的请求或请求无效 | 错误消息      
**500**         |内部服务器错误 | 错误消息
已超时     | 请求已超时。  | 错误消息
<br>
## <a name="example-calculate-similarity-of-two-partial-abstracts"></a>示例：计算两个部分摘要的相似性
#### <a name="request"></a>请求：
```
https://westus.api.cognitive.microsoft.com/academic/v1.0/similarity?s1=Using complementary priors, we derive a fast greedy algorithm that can learn deep directed belief networks one layer at a time, provided the top two layers form an undirected associative memory
&s2=Deepneural nets with a large number of parameters are very powerful machine learning systems. However, overfitting is a serious problem in such networks
```
在此示例中，我们使用相似性 API 生成两个部分摘要之间的相似性得分。
#### <a name="response"></a>响应：
```
0.520
```
#### <a name="remarks"></a>备注：
通过单词嵌入评估学术概念来确定相似性得分。 在此示例中，0.52 表示两个部分摘要有些相似。
<br>
