---
title: 学术知识 API 中的隶属关系实体属性
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的隶属关系实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 344b26b16f74cd44982e3c93fa69295792daa9a0
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55190614"
---
# <a name="affiliation-entity"></a>隶属关系实体

<sub> *以下属性特定于隶属关系实体。(Ty = '5') </sub>

Name    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
AfN     |隶属关系规范化名称        |String     |等于
DAfN    |隶属关系显示名称       |String     |无
CC      |隶属关系引文总计数           |Int32      |无  
ECC     |隶属关系估计引文总计数 |Int32      |无

## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

Name    | 说明               
--------|---------------------------    
PC      |隶属关系的论文计数
