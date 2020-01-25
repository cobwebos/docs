---
title: Azure Stream Analytics에서 JSON 및 AVRO 구문 분석
description: 이 문서에서는 배열, JSON, CSV 형식의 데이터와 같은 복합 데이터 형식을 조작하는 방법을 설명합니다.
ms.service: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.topic: conceptual
ms.date: 06/21/2019
ms.openlocfilehash: cbfa6f8b85814f0f77234e014ade0ff757a4c4b8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76720072"
---
# <a name="parse-json-and-avro-data-in-azure-stream-analytics"></a>Azure Stream Analytics에서 JSON 및 Avro 데이터 구문 분석

Azure 流分析支持处理 CSV、JSON 和 Avro 数据格式的事件。 JSON 和 Avro 数据都可以进行结构化，并且包含某些复杂类型（如嵌套对象（记录）和数组）。 




## <a name="record-data-types"></a>레코드 데이터 형식
레코드 데이터 형식은 입력 데이터 스트림에서 사용될 때 JSON 및 Avro 배열을 나타내는 데 사용됩니다. 다음 예제에서는 입력 이벤트를 JSON 형식으로 읽는 샘플 센서를 보여 줍니다. 단일 이벤트의 예제는 다음과 같습니다.

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
    SensorReadings.SensorMetadata.Version
FROM input
```

### <a name="select-all-properties"></a>选择所有属性
중첩된 레코드의 모든 속성은 '*' 와일드카드를 사용하여 선택할 수 있습니다. 다음과 같은 예제를 참조하세요.

```SQL
SELECT input.Location.*
FROM input
```

결과는 다음과 같습니다.

```json
{
    "Lat" : 47,
    "Long" : 122
}
```


### <a name="access-nested-fields-when-property-name-is-a-variable"></a>当属性名称是变量时访问嵌套字段
如果属性名称是变量，请使用[GetRecordPropertyValue](https://docs.microsoft.com/stream-analytics-query/getrecordpropertyvalue-azure-stream-analytics)函数。 

例如，假设有一个示例数据流需要与包含每个设备传感器的阈值的引用数据联接。 下面显示了此类引用数据的代码段。

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

### <a name="convert-record-fields-into-separate-events"></a>将记录字段转换为单独的事件
레코드 필드를 별도의 이벤트로 변환하려면 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자를 [GetRecordProperties](https://docs.microsoft.com/stream-analytics-query/getrecordproperties-azure-stream-analytics) 함수와 함께 사용합니다. 例如，如果前面的示例具有 SensorReading 的多个记录，可以使用以下查询将它们提取到不同的事件中：

```SQL
SELECT
    event.DeviceID,
    sensorReading.PropertyName,
    sensorReading.PropertyValue
FROM input as event
CROSS APPLY GetRecordProperties(event.SensorReadings) AS sensorReading
```



## <a name="array-data-types"></a>배열 데이터 형식

배열 데이터 형식은 정렬된 값의 컬렉션입니다. 배열 값에 일반적인 몇 가지 연산은 아래에 자세히 나와 있습니다. 이러한 예제에서는 입력 이벤트에 배열 데이터 형식인 "arrayField"라는 속성이 있다고 가정합니다.

이러한 예제에서는 [GetArrayElement](https://docs.microsoft.com/stream-analytics-query/getarrayelement-azure-stream-analytics), [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics), [GetArrayLength](https://docs.microsoft.com/stream-analytics-query/getarraylength-azure-stream-analytics) 함수 및 [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자를 사용합니다.

### <a name="working-with-a-specific-array-element"></a>使用特定数组元素
지정된 인덱스에서 배열 요소를 선택합니다(첫 번째 배열 요소 선택).

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

### <a name="convert-array-elements-into-separate-events"></a>将数组元素转换为单独的事件
모든 배열 요소를 개별 이벤트로 선택합니다. [APPLY](https://docs.microsoft.com/stream-analytics-query/apply-azure-stream-analytics) 연산자는 [GetArrayElements](https://docs.microsoft.com/stream-analytics-query/getarrayelements-azure-stream-analytics) 기본 제공 함수와 함께 모든 배열 요소를 개별 이벤트로 추출합니다.

```SQL
SELECT
    arrayElement.ArrayIndex,
    arrayElement.ArrayValue
FROM input as event
CROSS APPLY GetArrayElements(event.arrayField) AS arrayElement
```


## <a name="see-also"></a>참고 항목
[Azure Stream Analytics의 데이터 형식](https://docs.microsoft.com/stream-analytics-query/data-types-azure-stream-analytics)
