---
title: 学术知识 API 中的会议实例实体属性 | Microsoft Docs
description: 了解在认知服务中可与学术知识 API 中的会议实例实体结合使用的属性。
services: cognitive-services
author: alch-msft
manager: kuansanw
ms.service: cognitive-services
ms.component: academic-knowledge
ms.topic: article
ms.date: 03/23/2017
ms.author: alch
ms.openlocfilehash: ef2bca4346a4666905f3dfb7bd448720f3b0ef8b
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35365399"
---
# <a name="conference-instance-entity"></a>会议实例实体

<sub> *以下属性特定于会议实例实体。(Ty = '4') </sub>

名称    |说明                            |Type       | 操作
------- | ------------------------------------- | --------- | ----------------------------
ID      |实体 ID                              |Int64      |等于
CIN     |会议实例规范化名称 ({ConferenceSeriesNormalizedName} {ConferenceInstanceYear})        |String     |等于
DCN     |会议实例显示名称 ({ConferenceSeriesName} : {ConferenceInstanceYear})       |String     |无
CIL     |会议实例的位置    |String     |Equals、<br/>-StartsWith
CISD    |会议实例的开始日期  |日期       |Equals、<br/>IsBetween
CIED    |会议实例的结束日期    |日期       |Equals、<br/>IsBetween
CIARD   |会议实例的摘要注册截止日期  |日期       |Equals、<br/>IsBetween
CISDD   |会议实例的提交截止日期     |日期       |Equals、<br/>IsBetween
CIFVD   |会议实例的最终版本截止日期  |日期       |Equals、<br/>IsBetween
CINDD   |会议实例的通知日期   |日期       |Equals、<br/>IsBetween
CD.T    |会议实例事件的标题   |日期       |Equals、<br/>IsBetween
CD.D    |会议实例事件的日期    |日期       |Equals、<br/>IsBetween
PCS.CN  |实例的会议系列名称 |String     |等于
PCS.CId |实例的会议系列 ID |Int64    |等于
CC      |会议实例引文总计数           |Int32      |无  
ECC     |会议实例估计引文总计数 |Int32      |无


## <a name="extended-metadata-attributes"></a>扩展的元数据属性 ##

名称    | 说明               
--------|---------------------------    
FN      | 会议实例全名