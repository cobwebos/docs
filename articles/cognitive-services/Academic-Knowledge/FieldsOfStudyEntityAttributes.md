---
title: 研究领域实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以与学术知识 API 的研究领域实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/31/2017
ms.author: alch
ms.openlocfilehash: e9d6badf76efd03c0520a728af7b3e47b25f200a
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2019
ms.locfileid: "55878946"
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
