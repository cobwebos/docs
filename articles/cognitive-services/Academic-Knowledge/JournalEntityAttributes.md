---
title: 日记实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解在认知服务中可与学术知识 API 中的日记实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: cgronlun
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: conceptual
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: 45b2860b56e79c197d96998350131a142326157a
ms.sourcegitcommit: 7824e973908fa2edd37d666026dd7c03dc0bafd0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/10/2018
ms.locfileid: "48902801"
---
# <a name="journal-entity"></a>日记实体

<sub> *以下属性特定于日记实体。(Ty = '2') </sub>

名称    |Description                            |类型       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
DJN     |日记规范化名称                |String     |无
JN      |日记显示名称                   |String     |等于
CC      |日记引文总计数           |Int32      |无  
ECC     |日记估计引文总计数 |Int32      |无