---
title: 会议系列实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可用于会议系列实体的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ff71b489cce01d8d6ea29e09905d7d3ac8429e34
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55155590"
---
# <a name="conference-series-entity"></a>会议录实体

<sub> *以下属性为会议录实体专用属性。(Ty = '3') </sub>

Name    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
CN      |会议录规范化名称      |String     |等于
DCN     |会议录显示名称         |String     |无
CC      |会议录总引文计数         |Int32      |无  
ECC     |会议录总引文估计数   |Int32      |无
F.FId   |与会议录相关联的研究领域实体 ID |Int64  | 等于
F.FN    |与会议录相关联的研究领域名称  | Equals、<br/>-StartsWith
