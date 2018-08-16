---
title: 在 Azure 流分析中分析 JSON 和 AVRO
description: 本文介绍如何针对复杂数据类型（如数组、JSON、CSV 格式数据）进行操作。
services: stream-analytics
ms.service: stream-analytics
author: jasonwhowell
ms.author: jasonh
manager: kfile
ms.topic: conceptual
ms.date: 08/03/2018
ms.openlocfilehash: 208dfd1d78437e4ae8270d599f6365ad70a708c7
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579993"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 流分析中分析 JSON 和 Avro 数据

Azure 流分析支持处理采用 CSV、JSON 和 Avro 数据格式的事件。 JSON 和 Avro 数据都可能包含嵌套对象（记录）和数组等复杂类型。 
  
## <a name="array-data-types"></a>数组数据类型  
数组数据类型是按顺序排列的值集合。 下面详细介绍一些针对数组值执行的典型操作。 这些示例假定输入事件具有名为“arrayField”的数组数据类型属性。

这些事例使用函数 [GetArrayElement](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://msdn.microsoft.com/azure/stream-analytics/reference/getarraylength-azure-stream-analytics) 和 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 运算符。

## <a name="examples"></a>示例  
 选择指定索引中的数组元素（选择第一个数组元素）：  
  
```SQL 
SELECT   
    GetArrayElement(arrayField, 0) AS firstElement  
FROM input  
```  
  
 选择数组长度：  
  
```SQL  
SELECT   
    GetArrayLength(arrayField) AS arrayLength  
FROM input  
```  
  
选择所有数组元素作为各个事件。 结合使用 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 运算符和 [GetArrayElements](https://msdn.microsoft.com/azure/stream-analytics/reference/getarrayelements-azure-stream-analytics) 内置函数，提取所有数组元素作为各个事件：  
  
```SQL  
SELECT   
    arrayElement.ArrayIndex,  
    arrayElement.ArrayValue  
FROM input as event  
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement  
```  
  
## <a name="record-data-types"></a>记录数据类型  
如果在输入数据流中使用相应的格式，记录数据类型将用于表示 JSON 和 Avro 数组。 这些示例演示示例传感器，该传感器读取 JSON 格式的输入事件。 下面是单一事件的示例：
  
```json  
{  
    "DeviceId" : "12345",  
    "Location" : 
    {
        "Lat": 47,
        "Long": 122 
    },  
    "SensorReadings" :  
    {  
        "Temperature" : 80,  
        "Humidity" : 70,  
        "CustomSensor01" : 5,  
        "CustomSensor02" : 99  
    }  
}  
```  
  
## <a name="examples"></a>示例  
使用点标记 () 来访问嵌套字段。 例如，此查询选择上述 JSON 数据中 Location 属性下的经纬坐标： 
  
```SQL  
SELECT  
    DeviceID,  
    Location.Lat,  
    Location.Long  
FROM input  
```  

如果属性名称未知，则使用 [GetRecordPropertyValue](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordpropertyvalue-azure-stream-analytics) 函数。 例如，假设示例数据流需要与包含各设备传感器阈值的参考数据联接：  

```json  
{  
    "DeviceId" : "12345",  
    "SensorName" :  "Temperature",
    "Value" : 75
}  
```  
  
```SQL  
SELECT  
    input.DeviceID,  
    thresholds.SensorName  
FROM input  
JOIN thresholds  
ON  
    input.DeviceId = thresholds.DeviceId  
WHERE  
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value  
```  
  
若要将记录字段转换为单独事件，请结合使用 [APPLY](https://msdn.microsoft.com/azure/stream-analytics/reference/apply-azure-stream-analytics) 运算符和 [GetRecordProperties](https://msdn.microsoft.com/azure/stream-analytics/reference/getrecordproperties-azure-stream-analytics) 函数。 例如，若要将示例流转换为包含各个传感器读数的事件流，可使用下面的查询：  
  
```SQL  
SELECT   
    event.DeviceID,  
    sensorReading.PropertyName,  
    sensorReading.PropertyValue  
FROM input as event  
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading  
```  

可以使用“*”通配符选择嵌套记录的所有属性。 下面是一个示例：  

```SQL  
SELECT input.SensorReadings.*  
FROM input  
```  

结果为：  

```json  
{  
    "Temperature" : 80,  
    "Humidity" : 70,  
    "CustomSensor01" : 5,  
    "CustomSensor022" : 99  
}  
```  
  
## <a name="see-also"></a>另请参阅  
 [Azure 流分析中的数据类型](https://msdn.microsoft.com/azure/stream-analytics/reference/data-types-azure-stream-analytics)  
