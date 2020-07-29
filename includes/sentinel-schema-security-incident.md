---
title: include 文件
description: include 文件
services: azure-sentinel
author: yelevin
ms.service: azure-sentinel
ms.topic: include
ms.date: 06/28/2020
ms.author: yelevin
ms.custom: include file
ms.openlocfilehash: 76020b3c1f28e5b5f6363aef181b76bc93a9613e
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294008"
---
### <a name="the-data-model-of-the-schema"></a>架构的数据模型

| 字段 | 数据类型 | 说明 |
| ---- | ---- | ---- |
| **AdditionalData** | 动态 | 警报计数，书签计数，注释计数，预警产品名称和战术 |
| **AlertIds** | 动态 | 从中创建事件的警报 |
| **BookmarkIds** | 动态 | 带书签的实体 |
| **分类** | string | 事件关闭分类 |
| **ClassificationComment** | string | 事件关闭分类注释 |
| **ClassificationReason** | string | 事件关闭分类原因 |
| **ClosureTime** | datetime | 上次关闭事件时的时间戳（UTC） |
| **注释** | 动态 | 事件注释 |
| **CreatedTime** | datetime | 创建事件时的时间戳（UTC） |
| **说明** | string | 事件描述 |
| **FirstActivityTime** | datetime | 第一个事件时间 |
| **FirstModifiedTime** | datetime | 首次修改事件时的时间戳（UTC） |
| **事件名称** | string | 内部 GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | string | 链接到事件 |
| **标签** | 动态 | Tags |
| **LastActivityTime** | datetime | 上次事件时间 |
| **LastModifiedTime** | datetime | 上次修改事件的时间戳（UTC） <br>（当前记录描述的修改） |
| **ModifiedBy** | string | 修改事件的用户或系统 |
| **所有者** | 动态 |  |
| **RelatedAnalyticRuleIds** | 动态 | 触发事件警报的规则 |
| **严重性** | string | 事件的严重性（高/中/低/信息） |
| **SourceSystem** | string | 常量（"Azure"） |
| **Status** | string |  |
| **TenantId** | string |  |
| **TimeGenerated** | datetime | 创建当前记录的时间戳（UTC） <br>（修改事件时） |
| **Title** | string | 
| 类型 | string | 常量（"SecurityIncident"） |
|
