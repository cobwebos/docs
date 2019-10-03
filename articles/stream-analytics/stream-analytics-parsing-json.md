---
title: 在 Azure 流分析中分析 JSON 和 AVRO
description: 本文介绍如何针对复杂数据类型（如数组、JSON、CSV 格式数据）进行操作。
services: stream-analytics
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: daf5b97e4ac586f89e5964ee16ee73c86f59b01d
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/22/2019
ms.locfileid: "67329358"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 流分析中分析 JSON 和 Avro 数据

Azure Stream Analytics 支持 CSV、 JSON 和 Avro 数据格式处理事件。 JSON 和 Avro 数据可以结构化，并且包含一些复杂的类型，如嵌套的对象 （记录） 和数组。 




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
        "CustomSensor02" : 99,
        "SensorMetadata" : 
        {
        "Manufacturer":"ABC",
        "Version":"1.2.45"
        }
    }
}
```


### <a name="access-nested-fields-in-known-schema"></a>已知的架构中的访问嵌套字段
使用点表示法 （.） 来直接从你的查询轻松访问嵌套的字段。 例如，此查询将在前面的 JSON 数据中选择位置属性下的纬度和经度坐标。 可以使用点表示法来导航多个级别，如下所示。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>选择所有属性
可以使用“*”通配符选择嵌套记录的所有属性。 请看下面的示例：

```SQL
SELECT input.Location.*
FROM input
```

结果为：

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>访问嵌套字段时的属性名称是一个变量
使用[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue)函数如果属性名称是变量。 

例如，假设示例数据流需要与每个设备传感器的参考数据包含阈值联接。 此类引用数据的代码段如下所示。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 75
}
```

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
    -- the where statement selects the property value coming from the reference data
```

### <a name="convert-record-fields-into-separate-events"></a>记录字段转换为单独的事件
若要将记录字段转换为单独事件，请结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函数。 例如，如果前面的示例为 SensorReading 有几条记录，可以使用下面的查询，将它们提取到不同的事件：

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>数组数据类型

数组数据类型是按顺序排列的值集合。 下面详细介绍一些针对数组值执行的典型操作。 这些示例假定输入事件具有名为“arrayField”的数组数据类型属性。

这些事例使用函数 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) 和 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符。

### <a name="working-with-a-specific-array-element"></a>使用特定的数组元素
选择指定索引中的数组元素（选择第一个数组元素）：

```SQL
SELECT
    GetArrayElement(arrayField, 0) AS firstElement
FROM input
```

### <a name="select-array-length"></a>选择数组长度

```SQL
SELECT
    GetArrayLength(arrayField) AS arrayLength
FROM input
```

### <a name="convert-array-elements-into-separate-events"></a>数组元素转换为单独的事件
选择所有数组元素作为各个事件。 结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 内置函数，提取所有数组元素作为各个事件：

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>另请参阅
[Azure 流分析中的数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
