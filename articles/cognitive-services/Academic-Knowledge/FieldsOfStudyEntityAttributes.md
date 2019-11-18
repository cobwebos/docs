---
title: 研究领域实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以与学术知识 API 的研究领域实体结合使用的属性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: b6bb5e0fd822541a0a4c10ef5f275d46664f8e10
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146471"
---
# <a name="field-of-study-entity"></a>研究领域实体

> [!NOTE]
> 以下属性特定于 "学习" 实体的字段。 （Ty = "6"）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
CC      |研究领域总引文计数    |Int32      |无  
DFN     |研究领域显示名称            |String     |无
ECC     |研究领域总引文估计数|Int32      |无
FL      |研究领域层次结构级别     |Int32      |等于、IsBetween
FN      |研究领域规范化名称         |String     |等于
FC.FId  |研究领域子 ID                |Int64      |等于
FC.FN   |研究领域子名称              |String     |等于
FP.FId  |研究领域父 ID               |Int64      |等于
FP.FN   |研究领域父名称             |String     |等于
ID      |实体 ID                              |Int64      |等于
PC    | "研究总发布计数" 字段 | Int32 | 无
