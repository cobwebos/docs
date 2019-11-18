---
title: 会议实例实体属性 - 学术知识 API
titlesuffix: Azure Cognitive Services
description: 了解可以在学术知识 API 中与会议实例实体结合使用的属性。
services: cognitive-services
author: darrine
manager: kuansanw
ms.service: cognitive-services
ms.subservice: academic-knowledge
ms.topic: conceptual
ms.date: 11/14/2019
ms.author: darrine
ROBOTS: NOINDEX
ms.openlocfilehash: c6761206a58724dae96d9dc6f6234658892d4d18
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2019
ms.locfileid: "74146560"
---
# <a name="conference-instance-entity"></a>会议实例实体

> [!NOTE]
> 以下属性特定于 "会议实例" 实体。 （Ty = ' 4 '）

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
CC      |会议实例引文总计数           |Int32      |无  
CD.D    |会议实例事件的日期    |日期       |等于、IsBetween
CD.T    |会议实例事件的标题   |日期       |等于、IsBetween
CIARD   |会议实例的摘要注册截止日期  |日期       |等于、IsBetween
CIED    |会议实例的结束日期    |日期       |等于、IsBetween
CIFVD   |会议实例的最终版本截止日期  |日期       |等于、IsBetween
CIL     |会议实例的位置    |String     |等于、StartsWith
CIN     |会议实例标准化名称 |String        |等于
CINDD   |会议实例的通知日期   |日期       |等于、IsBetween
CISD    |会议实例的开始日期  |日期       |等于、IsBetween
CISDD   |会议实例的提交截止日期     |日期       |等于、IsBetween
DCN     |会议实例显示名称  |String      |无
E | 扩展元数据</br></br>**重要说明**：此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA） | [扩展](#extended) | 无
ECC     |会议实例估计引文总计数 |Int32      |无
FN | 会议实例全名 | String | 无
ID      |实体 ID                              |Int64      |等于
PC | 会议实例总发布计数 | Int32 | 无
PCS.CN  |实例的父会议序列名称 |String  |等于
PCS.CId |实例的父大会系列 ID |Int64     |等于

## <a name="extended"></a>扩展

> [!IMPORTANT]
> 此属性已弃用，并且仅适用于旧版应用程序。 单独请求此属性（即 attribute = Id、Ti、E）将导致在*序列化的 JSON 字符串*中返回所有扩展的元数据属性</br></br>扩展的元数据中包含的所有属性现在作为顶级属性提供，并且可以按如下方式请求（即 attribute = Id、Ti、DOI>10.1145、IA）

> [!IMPORTANT]
> 支持使用 "E" 请求单独的扩展属性。 作用域，即 "E. DN" 即将弃用。 虽然在技术上仍支持，但使用 "E" 请求单独的扩展属性。 范围将导致在 JSON 响应中的两个位置返回属性值，作为 "E" 对象的一部分，并作为顶级属性返回。

名称 | 说明 | 类型 | 操作
--- | --- | --- | ---
FN | 会议实例全名 | String | 无
PC | 会议实例总发布计数 | Int32 | 无
