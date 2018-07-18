---
title: include 文件
description: include 文件
services: cognitive-services
author: chliang
manager: bix
ms.service: cognitive-services
ms.technology: anomaly-finder
ms.topic: include
ms.date: 04/13/2018
ms.author: chliang
ms.custom: include file
ms.openlocfilehash: ff36202b67f6262b7ba67fe48ef37f2b656b91fa
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2018
ms.locfileid: "35366051"
---
<a name="definitions"></a>
## <a name="definitions"></a>定义

<a name="point"></a>
### <a name="point"></a>Point

|名称|说明|架构|
|---|---|---|
|**Timestamp**  <br>（可选）|数据点的时间戳。 请确保时间戳的零点一致，且使用 UTC 日期时间字符串，例如 2017-08-01T00:00:00Z。|字符串（日期时间）|
|**值**  <br>（可选）|数据度量值。|number (double)|


<a name="request"></a>
### <a name="request"></a>请求

|名称|说明|架构|
|---|---|---|
|**时间段**  <br>（可选）|数据点的周期。 如果值为 null 或不存在，则 API 将自动确定周期。|number (double)|
|**点**  <br>（可选）|时序数据点。 应按时间戳升序顺序对数据进行排序，以匹配异常结果。 如果数据未正确排序或存在重复时间戳，API 可正确检测异常点，但无法很好地将返回的点与输入匹配。 在这种情况下，响应中会添加警告消息。|< [点](#point) > 数组|


<a name="response"></a>
### <a name="response"></a>响应

|名称|说明|架构|
|---|---|---|
|**ExpectedValues**  <br>（可选）|基于模型的学习预测的值。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< number (double) > 数组|
|**IsAnomaly**  <br>（可选）|用于显示两个方向（峰值和下降）的数据点是否异常的结果。 true 表示该点异常，false 表示该点正常。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< boolean > 数组|
|**IsAnomaly_Neg**  <br>（可选）|用于显示反方向（下降）数据点是否异常的结果。 true 表示异常方向为反方向。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< boolean > 数组|
|**IsAnomaly_Pos**  <br>（可选）|用于显示正方向（峰值）数据点是否异常的结果。 true 表示异常方向为正方向。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< boolean > 数组|
|**LowerMargin**  <br>（可选）|(ExpectedValue - LowerMargin)用于确定下限，数据点在该范围内仍被视为正常。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< number (double) > 数组|
|**时间段**  <br>（可选）|用于检测异常点的 API 的使用周期。|数量（浮动）|
|**UpperMargin**  <br>（可选）|用于确定上限的 ExpectedValue 和 UpperMargin 的总和，数据点在该范围内仍被视为正常。 如果输入数据点按时间戳升序排序，则数组的索引可用于映射期望值和原始值。|< number (double) > 数组|
|**WarningText**  <br>（可选）|如果提供的输入数据点不符合 API 要求的规则，且 API 仍然可检测到数据，则 API 将分析该数据并在此字段中附加警告信息。|字符串|



