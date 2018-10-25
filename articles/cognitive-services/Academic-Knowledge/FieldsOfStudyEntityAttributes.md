---
title: 研究领域实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以与学术知识 API 的研究领域实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: 862fd6d506d5f1ca6f7f532f80f53a29200f33db
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48900421"
---
# <a name="field-of-study-entity"></a>研究领域实体

<sub> *以下属性为研究领域实体专用属性。(Ty = '6') </sub>

名称    |Description                            |类型       | 操作
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