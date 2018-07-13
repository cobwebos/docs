---
title: 学术知识 API 中的作者实体属性 | Microsoft Docs
description: 了解在认知服务中可与学术知识 API 中的作者实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: afcc3257c49522a4631c4aae0e0c2b88373f9af1
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365385"
---
# <a name="author-entity"></a>作者实体
<sub> *以下属性特定于作者实体。(Ty = '1') </sub>

名称    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
AuN     |作者规范化名称                 |String     |等于
DAuN    |作者显示名称                    |String     |无
CC      |作者引文总计数            |Int32      |无  
ECC     |作者估计引文总计数  |Int32      |无
E       |扩展的元数据（请参阅“扩展的元数据属性”表）  |String     |无  


## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称    | 说明               
--------|---------------------------    
LKA.Afn     | 与作者相关联的隶属关系的显示名称  
LKA.AfId        | 与作者相关联的隶属关系的实体 ID