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
ms.openlocfilehash: 63cb53dc60a718892d4bf86140e7fd51303bd61c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88761707"
---
### <a name="the-data-model-of-the-schema"></a>架构的数据模型

| 字段 | 数据类型 | 说明 |
| ---- | ---- | ---- |
| **AdditionalData** | 动态 | 警报计数，书签计数，注释计数，预警产品名称和战术 |
| **AlertIds** | 动态 | 从中创建事件的警报 |
| **BookmarkIds** | 动态 | 带书签的实体 |
| **分类** | 字符串 | 事件关闭分类 |
| **ClassificationComment** | 字符串 | 事件关闭分类注释 |
| **ClassificationReason** | 字符串 | 事件关闭分类原因 |
| **ClosedTime** | datetime | 上次关闭事件的时间戳 (UTC)  |
| **注释** | 动态 | 事件注释 |
| **CreatedTime** | datetime | 事件的创建时间的时间戳 (UTC)  |
| **说明** | string | 事件描述 |
| **FirstActivityTime** | datetime | 第一个事件时间 |
| **FirstModifiedTime** | datetime | 首次修改事件的时间戳 (UTC)  |
| **事件名称** | 字符串 | 内部 GUID |
| **IncidentNumber** | int |  |
| **IncidentUrl** | 字符串 | 链接到事件 |
| **标志** | 动态 | 标记 |
| **LastActivityTime** | datetime | 上次事件时间 |
| **LastModifiedTime** | datetime | 上次修改事件的时间戳 (UTC)  <br> (当前记录描述的修改)  |
| **ModifiedBy** | 字符串 | 修改事件的用户或系统 |
| **所有者** | 动态 |  |
| **RelatedAnalyticRuleIds** | 动态 | 触发事件警报的规则 |
| **严重性** | 字符串 | 事件的严重性 (高/中/低/信息)  |
| **SourceSystem** | 字符串 | 常量 ( "Azure" )  |
| **Status** | 字符串 |  |
| **TenantId** | 字符串 |  |
| **TimeGenerated** | datetime | 创建当前记录的时间戳 (UTC)  <br>修改事件时 ()  |
| **标题** | 字符串 | 
| **类型** | 字符串 | 常量 ( "SecurityIncident" )  |
|
