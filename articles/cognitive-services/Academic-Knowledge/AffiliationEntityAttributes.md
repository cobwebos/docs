---
title: 学术知识 API 中的隶属关系实体属性
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的隶属关系实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 82e6a5b66342e58e62da029d617cbd1d74c28149
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61340503"
---
# <a name="affiliation-entity"></a>隶属关系实体

<sub> *以下属性特定于隶属关系实体。(Ty = '5') </sub>

名称    |描述                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
AfN     |隶属关系规范化名称        |String     |等于
DAfN    |隶属关系显示名称       |String     |无
CC      |隶属关系引文总计数           |Int32      |无  
ECC     |隶属关系估计引文总计数 |Int32      |无

## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称    | 描述               
--------|---------------------------    
PC      |隶属关系的论文计数
