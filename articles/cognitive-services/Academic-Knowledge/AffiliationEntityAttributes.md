---
title: 学术知识 API 中的隶属关系实体属性 | Microsoft Docs
description: 了解在认知服务中可与学术知识 API 中的隶属关系实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: a0ec67cb811ca207b3d038028491da2516028f0b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365384"
---
# <a name="affiliation-entity"></a>隶属关系实体

<sub> *以下属性特定于隶属关系实体。(Ty = '5') </sub>

名称    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
AfN     |隶属关系规范化名称        |String     |等于
DAfN    |隶属关系显示名称       |String     |无
CC      |隶属关系引文总计数           |Int32      |无  
ECC     |隶属关系估计引文总计数 |Int32      |无

## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称    | 说明               
--------|---------------------------    
PC      |隶属关系的论文计数