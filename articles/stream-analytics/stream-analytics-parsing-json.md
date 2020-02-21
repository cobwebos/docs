---
title: 在 Azure 流分析中分析 JSON 和 AVRO
description: 本文介绍如何针对复杂数据类型（如数组、JSON、CSV 格式数据）进行操作。
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 01/29/2020
ms.openlocfilehash: 73905483850a47a9d036bef1b9e1ee60d3484555
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2020
ms.locfileid: "77484581"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>在 Azure 流分析中分析 JSON 和 Avro 数据

Azure 流分析支持处理 CSV、JSON 和 Avro 数据格式的事件。 JSON 和 Avro 数据都可以进行结构化，并且包含某些复杂类型（如嵌套对象（记录）和数组）。 

>[!NOTE]
>事件中心捕获创建的 AVRO 文件使用特定的格式，该格式要求你使用*自定义反序列*化功能。 有关详细信息，请参阅[使用 .net 自定义反以任意格式读取输入](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples)。



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

### <a name="access-nested-fields-in-known-schema"></a>访问已知架构中的嵌套字段
使用点表示法（.）可以轻松地直接从查询中访问嵌套字段。 例如，下面的查询在前面的 JSON 数据的 Location 属性下选择纬度和经度坐标。 点表示法可用于导航多个级别，如下所示。

```SQL
SELECT
    DeviceID,
    Location.Lat,
    Location.Long,
    SensorReadings.Temperature,
    SensorReadings.SensorMetadata.Version
FROM input
```

结果为：

|设备 ID|Lat|Long|温度|版本|
|-|-|-|-|-|
|12345|47|122|80|1.2.45|


### <a name="select-all-properties"></a>选择所有属性
可以使用“*”通配符选择嵌套记录的所有属性。 请考虑以下示例：

```SQL
SELECT
    DeviceID,
    Location.*
FROM input
```

结果为：

|设备 ID|Lat|Long|
|-|-|-|
|12345|47|122|


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>当属性名称是变量时访问嵌套字段

如果属性名称是变量，请使用[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics)函数。 这允许在不硬编码属性名称的情况下生成动态查询。

例如，假设示例数据流需要与包含每个设备传感器阈值的**引用数据联接**。 下面显示了此类引用数据的代码段。

```json
{
    "DeviceId" : "12345",
    "SensorName" : "Temperature",
    "Value" : 85
},
{
    "DeviceId" : "12345",
    "SensorName" : "Humidity",
    "Value" : 65
}
```

此处的目标是将示例数据集从文章顶部联接到引用数据，并为每个传感器度量值超过其阈值输出一个事件。 这意味着，如果由于加入，多个传感器超出各自的阈值，则上述单个事件可以生成多个输出事件。 若要在没有联接的情况下实现类似的结果，请参阅下面的部分。

```SQL
SELECT
    input.DeviceID,
    thresholds.SensorName,
    "Alert : Sensor above threshold" AS AlertMessage
FROM input      -- stream input
JOIN thresholds -- reference data input
ON
    input.DeviceId = thresholds.DeviceId
WHERE
    GetRecordPropertyValue(input.SensorReadings, thresholds.SensorName) > thresholds.Value
```

**GetRecordPropertyValue**选择*SensorReadings*中的属性，该属性的名称与来自引用数据的属性名称匹配。 然后，提取*SensorReadings*中的关联值。

结果为：

|设备 ID|SensorName|AlertMessage|
|-|-|-|
|12345|湿度|警报：传感器超出阈值|

### <a name="convert-record-fields-into-separate-events"></a>将记录字段转换为单独的事件

若要将记录字段转换为单独事件，请结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 函数。

使用原始示例数据时，可以使用以下查询将属性提取到不同的事件中。

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```

结果为：

|设备 ID|SensorName|AlertMessage|
|-|-|-|
|12345|温度|80|
|12345|湿度|70|
|12345|CustomSensor01|5|
|12345|CustomSensor02|99|
|12345|SensorMetadata|[对象对象]|

使用[WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)，可以将这些事件路由到不同的目标：

```SQL
WITH Stage0 AS
(
    SELECT
        event.DeviceID,
        sensorReading.PropertyName,
        sensorReading.PropertyValue
    FROM input as event
    CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
)

SELECT DeviceID, PropertyValue AS Temperature INTO TemperatureOutput FROM Stage0 WHERE PropertyName = 'Temperature'
SELECT DeviceID, PropertyValue AS Humidity INTO HumidityOutput FROM Stage0 WHERE PropertyName = 'Humidity'
```

### <a name="parse-json-record-in-sql-reference-data"></a>分析 SQL 引用数据中的 JSON 记录
使用 Azure SQL 数据库作为作业中的引用数据时，可能会有一个数据采用 JSON 格式的列。 下面显示了一个示例。

|设备 ID|data|
|-|-|
|12345|{"key"： "value1"}|
|54321|{"key"： "value2"}|

可以通过编写简单的 JavaScript 用户定义函数来分析*数据*列中的 JSON 记录。

```javascript
function parseJson(string) {
return JSON.parse(string);
}
```

然后，你可以在流分析查询中创建一个步骤，如下所示，访问 JSON 记录的字段。

 ```SQL
 WITH parseJson as
 (
 SELECT DeviceID, udf.parseJson(sqlRefInput.Data) as metadata,
 FROM sqlRefInput
 )
 
 SELECT metadata.key
 INTO output
 FROM streamInput
 JOIN parseJson 
 ON streamInput.DeviceID = parseJson.DeviceID
```

## <a name="array-data-types"></a>数组数据类型

数组数据类型是按顺序排列的值集合。 下面详细介绍一些针对数组值执行的典型操作。 这些事例使用函数 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics)、[GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics)、[GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) 和 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符。

下面是单个事件的示例。 `CustomSensor03` 和 `SensorMetadata` 均为**数组**类型：

```json
{
    "DeviceId" : "12345",
    "SensorReadings" :
    {
        "Temperature" : 80,
        "Humidity" : 70,
        "CustomSensor01" : 5,
        "CustomSensor02" : 99,
        "CustomSensor03": [12,-5,0]
     },
    "SensorMetadata":[
        {          
            "smKey":"Manufacturer",
            "smValue":"ABC"                
        },
        {
            "smKey":"Version",
            "smValue":"1.2.45"
        }
    ]
}
```

### <a name="working-with-a-specific-array-element"></a>使用特定数组元素

选择指定索引中的数组元素（选择第一个数组元素）：

```SQL
SELECT
    GetArrayElement(SensorReadings.CustomSensor03, 0) AS firstElement
FROM input
```

结果为：

|firstElement|
|-|
|12|

### <a name="select-array-length"></a>选择数组长度

```SQL
SELECT
    GetArrayLength(SensorReadings.CustomSensor03) AS arrayLength
FROM input
```

结果为：

|arrayLength|
|-|
|3|

### <a name="convert-array-elements-into-separate-events"></a>将数组元素转换为单独的事件

选择所有数组元素作为各个事件。 结合使用 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 运算符和 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 内置函数，提取所有数组元素作为各个事件：

```SQL
SELECT
    DeviceId,
    CustomSensor03Record.ArrayIndex,
    CustomSensor03Record.ArrayValue
FROM input
CROSS APPLY GetArrayElements(SensorReadings.CustomSensor03) AS CustomSensor03Record

```

结果为：

|DeviceId|ArrayIndex|ArrayValue|
|-|-|-|
|12345|0|12|
|12345|1|-5|
|12345|2|0|

```SQL
SELECT   
    i.DeviceId, 
    SensorMetadataRecords.ArrayValue.smKey as smKey,
    SensorMetadataRecords.ArrayValue.smValue as smValue
FROM input i
CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords
 ```
 
结果为：

|DeviceId|smKey|smValue|
|-|-|-|
|12345|制造商|ABC|
|12345|版本|1.2.45|

如果提取的字段需要显示在列中，则除了[联接](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics)操作外，还可以使用[WITH](https://docs.microsoft.com/stream-analytics-query/with-azure-stream-analytics)语法来透视数据集。 该联接需要一个阻止重复的[时间边界](https://docs.microsoft.com/stream-analytics-query/join-azure-stream-analytics#BKMK_DateDiff)条件：

```SQL
WITH DynamicCTE AS (
    SELECT   
        i.DeviceId,
        SensorMetadataRecords.ArrayValue.smKey as smKey,
        SensorMetadataRecords.ArrayValue.smValue as smValue
    FROM input i
    CROSS APPLY GetArrayElements(SensorMetadata) AS SensorMetadataRecords 
)

SELECT
    i.DeviceId,
    i.Location.*,
    V.smValue AS 'smVersion',
    M.smValue AS 'smManufacturer'
FROM input i
LEFT JOIN DynamicCTE V ON V.smKey = 'Version' and V.DeviceId = i.DeviceId AND DATEDIFF(minute,i,V) BETWEEN 0 AND 0 
LEFT JOIN DynamicCTE M ON M.smKey = 'Manufacturer' and M.DeviceId = i.DeviceId AND DATEDIFF(minute,i,M) BETWEEN 0 AND 0
```

结果为：

|DeviceId|Lat|Long|smVersion|smManufacturer|
|-|-|-|-|-|
|12345|47|122|1.2.45|ABC|

## <a name="see-also"></a>另请参阅
[Azure 流分析中的数据类型](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
