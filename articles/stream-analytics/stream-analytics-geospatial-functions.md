---
title: Azure 流分析地理空间函数的简介
description: 本文介绍了 Azure 流分析作业中使用的地理空间函数。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: ad789a597da759b9a2d58138c7ed441389a12adb
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479977"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>流分析地理空间函数的简介

Azure 流分析中的地理空间函数支持对流式处理的地理空间数据进行实时分析。 只需几行代码，即可为复杂的方案开发出生产级解决方案。 

可从地理空间函数中受益的示例方案包括：

* 拼车
* 车队管理
* 资产跟踪
* 地域隔离
* 蜂窝站点之间的电话跟踪

流分析查询语言具有七种内置地理空间函数：CreateLineString、CreatePoint、CreatePolygon、ST_DISTANCE、ST_OVERLAPS、ST_INTERSECTS 和 ST_WITHIN        。

## <a name="createlinestring"></a>CreateLineString

`CreateLineString` 函数接受点并返回 GeoJSON LineString，这可在地图上被标绘成一个线条。 必须至少具有两个点才能创建 LineString。 LineString 点将按顺序相连。

下面的查询使用 `CreateLineString` 创建使用三个点的 LineString。 第一个点创建自流式处理输入数据，而另外两个点则是手动创建的。

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例  

 {"type" :"LineString", "coordinates" : [ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5] ]}

 {"type" :"LineString", "coordinates" : [ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5] ]}

若要了解详细信息，请访问 [CreateLineString](https://msdn.microsoft.com/azure/stream-analytics/reference/createlinestring) 引用。

## <a name="createpoint"></a>CreatePoint

`CreatePoint` 函数接受纬度和经度，并返回一个 GeoJSON 点，这可在地图上被标绘出来。 维度和精度必须为 float 数据类型  。

下面的示例查询使用 `CreatePoint` 创建使用流式处理输入数据中的纬度和经度的点。

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例
  
 {"type" :"Point", "coordinates" : [-10.2, 3.0]}  
  
 {"type" :"Point", "coordinates" : [20.2321, -87.33]}  

若要了解详细信息，请访问 [CreatePoint](https://msdn.microsoft.com/azure/stream-analytics/reference/createpoint) 引用。

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon` 函数接受点并返回一条 GeoJSON 多边形记录。 这些点的顺序必须遵循向右侧的环方向，否则为逆时针方向。 假设以声明的顺序从一个点走到另一个点。 在整个时间段内，此多边形的中心将偏向左侧。

下面的示例查询使用 `CreatePolygon` 通过三个点来创建多边形。 前两个点是手动创建的，最后一个点是通过输入数据创建的。

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|latitude|longitude|  
|--------------|---------------|  
|3.0|-10.2|  
|-87.33|20.2321|  
  
### <a name="output-example"></a>输出示例  

 {"type" :"Polygon", "coordinates" : [[ [-10.2, 3.0], [10.0, 10.0], [10.5, 10.5], [-10.2, 3.0] ]]}
 
 {"type" :"Polygon", "coordinates" : [[ [20.2321, -87.33], [10.0, 10.0], [10.5, 10.5], [20.2321, -87.33] ]]}

若要了解详细信息，请访问 [CreatePolygon](https://msdn.microsoft.com/azure/stream-analytics/reference/createpolygon) 引用。


## <a name="stdistance"></a>ST_DISTANCE
`ST_DISTANCE` 函数返回两个点之间的距离（以米为单位）。 

下面的查询使用 `ST_DISTANCE` 在加油站距离汽车不足 10 千米时生成事件。

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

若要了解详细信息，请访问 [ST_DISTANCE](https://msdn.microsoft.com/azure/stream-analytics/reference/st-distance) 引用。

## <a name="stoverlaps"></a>ST_OVERLAPS
`ST_OVERLAPS` 函数比较两个多边形。 如果多边形重叠，函数将返回 1。 如果多边形不重叠，函数将返回 0。 

下面的查询使用 `ST_OVERLAPS` 在某建筑位于可能发生洪灾的区域内时生成事件。

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

下面的示例查询在风暴即将袭击汽车时生成事件。

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

若要了解详细信息，请访问 [ST_OVERLAPS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-overlaps) 引用。

## <a name="stintersects"></a>ST_INTERSECTS
`ST_INTERSECTS` 函数比较两个 LineString。 如果 LineString 相交，函数将返回 1。 如果 LineString 不相交，函数将返回 0。

下面的示例查询使用 `ST_INTERSECTS` 来确定柏油路是否与泥路相交。

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>输入示例  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [0.0, 10.0], [0.0, 0.0], [0.0, -10.0] ]}|  
|{“type”:”LineString”, “coordinates”: [ [-10.0, 0.0], [0.0, 0.0], [10.0, 0.0] ]}|{“type”:”LineString”, “coordinates”: [ [-10.0, 10.0], [0.0, 10.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>输出示例  

 第  
  
 0  

若要了解详细信息，请访问 [ST_INTERSECTS](https://msdn.microsoft.com/azure/stream-analytics/reference/st-intersects) 引用。

## <a name="stwithin"></a>ST_WITHIN
`ST_WITHIN` 函数确定一个点或多边形位于另一个多边形内。 如果多边形包含点或多边形，函数将返回 1。 如果点或多边形位于所声明的多边形之外，函数将返回 0。

下面的示例查询使用 `ST_WITHIN` 来确定交付目标点是否位于给定的仓库多边形内。

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>输入示例  
  
|deliveryDestination|warehouse|  
|-------------------------|---------------|  
|{“type”:”Point”, “coordinates”: [76.6, 10.1]}|{“type”:”Polygon”, “coordinates”: [ [0.0, 0.0], [10.0, 0.0], [10.0, 10.0], [0.0, 10.0], [0.0, 0.0] ]}|  
|{“type”:”Point”, “coordinates”: [15.0, 15.0]}|{“type”:”Polygon”, “coordinates”: [ [10.0, 10.0], [20.0, 10.0], [20.0, 20.0], [10.0, 20.0], [10.0, 10.0] ]}|  
  
### <a name="output-example"></a>输出示例  

 0  
  
 第  

若要了解详细信息，请访问 [ST_WITHIN](https://msdn.microsoft.com/azure/stream-analytics/reference/st-within) 引用。

## <a name="next-steps"></a>后续步骤

* [Azure 流分析简介](stream-analytics-introduction.md)
* [Azure 流分析入门](stream-analytics-real-time-fraud-detection.md)
* [缩放 Azure 流分析作业](stream-analytics-scale-jobs.md)
* [Azure 流分析查询语言参考](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Azure 流分析管理 REST API 参考](https://msdn.microsoft.com/library/azure/dn835031.aspx)