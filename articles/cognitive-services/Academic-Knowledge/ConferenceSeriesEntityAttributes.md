---
title: 会议系列实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可用于会议系列实体的属性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: ecd8042212987849d9d302642f03e59493235599
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74143908"
---
# <a name="conference-series-entity"></a>会议录实体

> [!NOTE]
> 以下属性特定于 "会议系列" 实体。 （Ty = ' 3 '）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
CC      |会议录总引文计数         |Int32      |无  
CN      |会议录规范化名称      |String     |等于
DCN     |会议录显示名称         |String     |无
ECC     |会议录总引文估计数   |Int32      |无
F.FId   |与会议录相关联的研究领域实体 ID |Int64  | 等于
F.FN    |与会议录相关联的研究领域名称  | Equals、<br/>-StartsWith
ID      |实体 ID                              |Int64      |等于
PC    |会议系列总发布计数 |Int32 | 无
