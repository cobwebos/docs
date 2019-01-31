---
title: 日记实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解在认知服务中可与学术知识 API 中的日记实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 397293c17320ef75daa5c31af76d49183b6c435c
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193822"
---
# <a name="journal-entity"></a>日记实体

<sub> *以下属性特定于日记实体。(Ty = '2') </sub>

Name    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
DJN     |日记规范化名称                |String     |无
JN      |日记显示名称                   |String     |等于
CC      |日记引文总计数           |Int32      |无  
ECC     |日记估计引文总计数 |Int32      |无
