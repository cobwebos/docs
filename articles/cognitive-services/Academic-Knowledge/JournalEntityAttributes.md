---
title: 学术知识 API 中的日记实体属性 | Microsoft Docs
description: 了解在认知服务中可与学术知识 API 中的日记实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: e782c57b8ac57028e070c16382c53ec76666e94d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365382"
---
# <a name="journal-entity"></a>日记实体

<sub> *以下属性特定于日记实体。(Ty = '2') </sub>

名称    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
DJN     |日记规范化名称                |String     |无
JN      |日记显示名称                   |String     |等于
CC      |日记引文总计数           |Int32      |无  
ECC     |日记估计引文总计数 |Int32      |无