---
title: 作者实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可与学术知识 API 中的作者实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: nitinme
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ROBOTS: NOINDEX
ms.openlocfilehash: e63e9d3f8f17a2473caf3d31b83e318ddb132b43
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705096"
---
# <a name="author-entity"></a>作者实体
<sub> *以下属性特定于作者实体。(Ty = '1') </sub>

名称    |描述                            |类型       | 操作
------- | ------------------------------------- | --------- | ----------------------------
Id      |实体 ID                              |Int64      |等于
AuN     |作者规范化名称                 |String     |等于
DAuN    |作者显示名称                    |String     |无
CC      |作者引文总计数            |Int32      |无  
ECC     |作者估计引文总计数  |Int32      |无
E       |扩展的元数据（请参阅“扩展的元数据属性”表）  |String     |无  


## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

姓名    | 描述               
--------|---------------------------    
LKA.Afn     | 与作者相关联的隶属关系的显示名称  
LKA.AfId        | 与作者相关联的隶属关系的实体 ID
