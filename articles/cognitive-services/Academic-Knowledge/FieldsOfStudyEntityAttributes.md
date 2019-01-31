---
title: 研究领域实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以与学术知识 API 的研究领域实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 793b35d9c6412c40a87f3f91fcd772476d57584f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55154561"
---
# <a name="field-of-study-entity"></a>研究领域实体

<sub> *以下属性为研究领域实体专用属性。(Ty = '6') </sub>

Name    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
FN      |研究领域规范化名称         |String     |等于
DFN     |研究领域显示名称            |String     |无
CC      |研究领域总引文计数    |Int32      |无  
ECC     |研究领域总引文估计数|Int32      |无
FL      |研究领域层次结构级别     |Int32      |Equals、 <br/>IsBetween
FP.FN   |研究领域父名称             |String     |等于
FP.FId  |研究领域父 ID               |Int64      |等于
FC.FN   |研究领域子名称              |String     |等于
FC.FId  |研究领域子 ID                |Int64      |等于
