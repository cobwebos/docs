---
title: 学术知识 API 中的会议录实体属性 | Microsoft Docs
description: 了解可以与认知服务中的会议录实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 332736c927bdaa00334546f626a6eabb8e11d3b5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365388"
---
# <a name="conference-series-entity"></a>会议录实体

<sub> *以下属性为会议录实体专用属性。(Ty = '3') </sub>

名称    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
CN      |会议录规范化名称      |String     |等于
DCN     |会议录显示名称         |String     |无
CC      |会议录总引文计数         |Int32      |无  
ECC     |会议录总引文估计数   |Int32      |无
F.FId   |与会议录相关联的研究领域实体 ID |Int64  | 等于
F.FN    |与会议录相关联的研究领域名称  | Equals、<br/>-StartsWith