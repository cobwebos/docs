---
title: 概念：用于 FHIR 的 Azure IoT Connector 中的映射模板 () 预览用于 FHIR 的 Azure API 的功能
description: 了解如何在 Azure IoT Connector 中创建两种类型的映射模板用于 FHIR (预览) 。 设备映射模板将设备数据转换为规范化架构。 FHIR 映射模板将规范化消息转换为基于 FHIR 的观察资源。
services: healthcare-apis
author: ms-puneet-nagpal
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: conceptual
ms.date: 08/03/2020
ms.author: punagpal
ms.openlocfilehash: da5eb43f8bc2fc8b4ac213f6ff90464de5995a47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87553640"
---
# <a name="azure-iot-connector-for-fhir-preview-mapping-templates"></a>适用于 FHIR 的 Azure IoT 连接器（预览版）映射模版
本文详细介绍如何使用映射模板配置适用于 FHIR * 的 Azure IoT 连接器。

适用于 FHIR 的 Azure IoT 连接器需要两种基于 JSON 的映射模板。 第一种类型 " **设备映射**" 负责映射发送到 `devicedata` Azure 事件中心终结点的设备负载。 它提取类型、设备标识符、度量日期时间和度量值 (s) 。 第二种类型 **FHIR 映射**控制 FHIR 资源的映射。 它允许配置观察期长度、FHIR 用于存储值的数据类型，以及术语代码 (s) 。 

映射模板基于其类型组合到 JSON 文档中。 然后，这些 JSON 文档将添加到 Azure IoT 连接器，以便通过 Azure 门户进行 FHIR。 设备映射文档是通过配置 **设备映射** 页添加的，FHIR 映射文档通过 **配置 FHIR 映射** 页添加。

> [!NOTE]
> 映射模板存储在基础 blob 存储中，并从 blob 每个计算执行加载。 更新后，它们应立即生效。 

## <a name="device-mapping"></a>设备映射
设备映射提供了用于将设备内容提取为常见格式以供进一步评估的映射功能。 收到的每条消息都针对所有模板进行评估。 此方法允许将单个入站消息投影到多个出站消息，这些出站消息稍后会映射到 FHIR 中的不同观察。 结果是一个规范化数据对象，表示由模板分析的一个或哪些值。 规范化数据模型具有几个必需的属性，这些属性必须进行查找和提取：

| 属性 | 描述 |
| - | - |
|**Type**|要对度量值进行分类的名称/类型。 此值用于绑定到所需的 FHIR 映射模板。  可以将多个模板输出到同一个类型，使你能够将不同表示形式的不同表示形式映射到单个常见输出。|
|**OccurenceTimeUtc**|测量发生的时间。|
|**DeviceId**|设备的标识符。 此值应与目标 FHIR 服务器上存在的设备资源上的标识符相匹配。|
 |**属性**|提取至少一个属性，以便可以将该值保存在创建的观察资源中。  属性是在规范化期间提取的键值对的集合。|

下面是在规范化过程中所发生情况的概念示例。

![规范化示例](media/concepts-iot-mapping-templates/normalization-example.png)

内容负载本身是 Azure 事件中心消息，由三个部分组成：正文、属性和 SystemProperties。 `Body`是表示 utf-8 编码字符串的字节数组。 在模板计算期间，字节数组自动转换为字符串值。 `Properties` 是供消息创建者使用的键值集合。 `SystemProperties` 也是由 Azure 事件中心框架保留的键值集合，其中的条目由其自动填充。

```json
{
    "Body": {
        "content": "value"
    },
    "Properties": {
        "key1": "value1",
        "key2": "value2"
    },
    "SystemProperties": {
        "x-opt-sequence-number": 1,
        "x-opt-enqueued-time": "2019-02-01T22:46:01.8750000Z",
        "x-opt-offset": 1,
        "x-opt-partition-key": "1"
    }
}
```

### <a name="mapping-with-json-path"></a>与 JSON 路径映射
目前支持的两种设备内容模板类型依赖于 JSON 路径来匹配所需的模板和提取的值。 可在 [此处](https://goessner.net/articles/JsonPath/)找到有关 JSON 路径的详细信息。 这两种模板类型使用 [json .net 实现](https://www.newtonsoft.com/json/help/html/QueryJsonSelectTokenJsonPath.htm) 来解析 json 路径表达式。

#### <a name="jsonpathcontenttemplate"></a>JsonPathContentTemplate
JsonPathContentTemplate 允许使用 JSON 路径从事件中心消息中匹配和提取值。

| 属性 | 说明 |<div style="width:150px">示例</div>
| --- | --- | --- 
|**TypeName**|与与模板匹配的度量值关联的类型。|`heartrate`
|**TypeMatchExpression**|针对事件中心有效负载计算的 JSON 路径表达式。 如果找到匹配的 JToken，则将模板视为匹配项。 所有后续表达式都将针对此处匹配的提取的 JToken 进行计算。|`$..[?(@heartRate)]`
|**TimestampExpression**|用于提取度量值的 OccurenceTimeUtc 的时间戳值的 JSON 路径表达式。|`$.endDate`
|**DeviceIdExpression**|用于提取设备标识符的 JSON 路径表达式。|`$.deviceId`
|**PatientIdExpression**|*可选*：用于提取患者标识符的 JSON 路径表达式。|`$.patientId`
|**EncounterIdExpression**|*可选*：用于提取遇到的标识符的 JSON 路径表达式。|`$.encounterId`
|**值 []。ValueName**|与后续表达式提取的值关联的名称。 用于绑定 FHIR 映射模板中所需的值/组件。 |`hr`
|**值 []。ValueExpression**|用于提取所需值的 JSON 路径表达式。|`$.heartRate`
|**值 []。必填**|将要求值存在于有效负载中。  如果找不到，则不会生成度量，将引发 InvalidOperationException。|`true`

##### <a name="examples"></a>示例
---
**心率**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血压**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@systolic && @diastolic)]",
    "deviceIdExpression": "$.deviceid",
    "timestampExpression": "$.endDate",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```
---

**投影单个消息中的多个度量**

*Message*
```json
{
    "Body": {
        "heartRate": "78",
        "steps": "2",
        "endDate": "2019-02-01T22:46:01.8750000Z",
        "deviceId": "device123"
    },
    "Properties": {},
    "SystemProperties": {}
}
```
*模板1*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
*模板2*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "stepcount",
        "typeMatchExpression": "$..[?(@steps)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.steps",
                "valueName": "steps"
            }
        ]
    }
}
```
---

**从消息中的数组投影多个度量值**

*Message*
```json
{
    "Body": [
        {
            "heartRate": "78",
            "endDate": "2019-02-01T22:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "81",
            "endDate": "2019-02-01T23:46:01.8750000Z",
            "deviceId": "device123"
        },
        {
            "heartRate": "72",
            "endDate": "2019-02-01T24:46:01.8750000Z",
            "deviceId": "device123"
        }
    ],
    "Properties": {},
    "SystemProperties": {}
}
```
*模板*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
#### <a name="iotjsonpathcontenttemplate"></a>IotJsonPathContentTemplate
IotJsonPathContentTemplate 类似于 JsonPathContentTemplate，但 DeviceIdExpression 和 TimestampExpression 不是必需的。

使用此模板时的假设是使用 [Azure IoT 中心设备 sdk](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-sdks#azure-iot-hub-device-sdks)发送正在评估的消息。 使用这些 Sdk 时，设备标识 (假设 Azure Iot 中心/中心的设备标识符注册为目标 FHIR 服务器上的设备资源的标识符) 并且消息的时间戳是已知的。 如果使用的是 Azure IoT 中心设备 Sdk，但在消息正文中使用自定义属性来获取设备标识或度量时间戳，则仍可使用 JsonPathContentTemplate。

*注意：使用 IotJsonPathContentTemplate 时，TypeMatchExpression 应将整个消息解析为 JToken。请参阅以下示例。* 
##### <a name="examples"></a>示例
---
**心率**

*Message*
```json
{
    "Body": {
        "heartRate": "78"        
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*模板*
```json
{
    "templateType": "JsonPathContent",
    "template": {
        "typeName": "heartrate",
        "typeMatchExpression": "$..[?(@Body.heartRate)]",
        "deviceIdExpression": "$.deviceId",
        "timestampExpression": "$.endDate",
        "values": [
            {
                "required": "true",
                "valueExpression": "$.Body.heartRate",
                "valueName": "hr"
            }
        ]
    }
}
```
---
**血压**

*Message*
```json
{
    "Body": {
        "systolic": "123",
        "diastolic" : "87"
    },
    "Properties": {
        "iothub-creation-time-utc" : "2019-02-01T22:46:01.8750000Z"
    },
    "SystemProperties": {
        "iothub-connection-device-id" : "device123"
    }
}
```
*模板*
```json
{
    "typeName": "bloodpressure",
    "typeMatchExpression": "$..[?(@Body.systolic && @Body.diastolic)]",
    "values": [
        {
            "required": "true",
            "valueExpression": "$.Body.systolic",
            "valueName": "systolic"
        },
        {
            "required": "true",
            "valueExpression": "$.Body.diastolic",
            "valueName": "diastolic"
        }
    ]
}
```

## <a name="fhir-mapping"></a>FHIR 映射
将设备内容提取到规范化模型后，将根据设备标识符、测量类型和时间段收集和分组数据。 此分组的输出将发送到 FHIR 资源 ([观察](https://www.hl7.org/fhir/observation.html) 当前) 。 此处的 FHIR 映射模板控制如何将数据映射到 FHIR 观察。 是否应为某个时间点或一小时内的某个时间点创建观察值？ 应将哪些代码添加到观察？ 该值应表示为 [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData) 还是 [数量](https://www.hl7.org/fhir/datatypes.html#Quantity)？ 这些数据类型都是 FHIR 映射配置控件的选项。

### <a name="codevaluefhirtemplate"></a>CodeValueFhirTemplate
目前，CodeValueFhirTemplate 是 FHIR 映射中目前支持的唯一模板。  它允许您定义代码、有效期以及观察值。 支持多个值类型： [SampledData](https://www.hl7.org/fhir/datatypes.html#SampledData)、 [CodeableConcept](https://www.hl7.org/fhir/datatypes.html#CodeableConcept)和 [数量](https://www.hl7.org/fhir/datatypes.html#Quantity)。 与这些可配置值一起，会自动处理观察资源的标识符，并将其链接到正确的设备和患者资源。

| 属性 | 说明 
| --- | ---
|**TypeName**| 此模板应绑定到的度量类型。 应至少有一个输出此类型的设备映射模板。
|**PeriodInterval**|创建的观察应表示的时间段。 支持的值为 0 (实例) ，60 (一小时) ，1440 (日) 。
|**类别**|任意数量的 [CodeableConcepts](http://hl7.org/fhir/datatypes-definitions.html#codeableconcept) ，用于对创建的观察类型进行分类。
|**代码**|要应用于所创建的观察的一个或多个 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代码 []。编写**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)代码。
|**代码 []。主板**|用于 [编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|**代码 []。显示**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。
|**值**|要在观察中提取并表示的值。 有关详细信息，请参阅 [值类型模板](#valuetypes)。
|**组件**|*可选：* 要在观察上创建的一个或多个组件。
|**组件 []。条码**|要应用于组件的一个或多个 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**组件 []。负值**|要在组件中提取并表示的值。 有关详细信息，请参阅 [值类型模板](#valuetypes)。

### <a name="value-type-templates"></a>值类型模板 <a name="valuetypes"></a>
下面是当前支持的值类型模板。 将来可能会添加更多模板。
#### <a name="sampleddata"></a>SampledData
表示 [SampledData](http://hl7.org/fhir/datatypes.html#SampledData) FHIR 数据类型。观察度量值将写入到一个时间点开始的值流，并使用定义的时间范围递增。 如果不存在任何值， `E` 将写入数据流。 如果该时间段是在数据流中占用相同位置的两个值，则使用最新的值。 当更新使用 SampledData 的观察时，将应用相同的逻辑。

| 属性 | 说明 
| --- | ---
|**DefaultPeriod**|要使用的默认时间段（以毫秒为单位）。 
|**单位**|要在 SampledData 的原点设置的单位。 

#### <a name="quantity"></a>数量
表示 FHIR 数据类型的 [数量](http://hl7.org/fhir/datatypes.html#Quantity) 。 如果组中存在多个值，则仅使用第一个值。 当新值到达时，如果映射到相同的观察值，则会覆盖旧值。

| 属性 | 说明 
| --- | --- 
|**单位**| 单位表示。
|**代码**| 单元的编码形式。
|**系统**| 定义编码单元窗体的系统。

### <a name="codeableconcept"></a>CodeableConcept
表示 [CodeableConcept](http://hl7.org/fhir/datatypes.html#CodeableConcept) FHIR 数据类型。 不使用实际值。

| 属性 | 说明 
| --- | --- 
|**Text**|纯文本表示形式。 
|**代码**|要应用于所创建的观察的一个或多个 [Codings](http://hl7.org/fhir/datatypes-definitions.html#coding) 。
|**代码 []。编写**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)代码。
|**代码 []。主板**|用于 [编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的系统。
|**代码 []。显示**|[编码](http://hl7.org/fhir/datatypes-definitions.html#coding)的显示。

### <a name="examples"></a>示例
**心率-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "8867-4",
                "system": "http://loinc.org",
                "display": "Heart rate"
            }
        ],
        "periodInterval": 60,
        "typeName": "heartrate",
        "value": {
            "defaultPeriod": 5000,
            "unit": "count/min",
            "valueName": "hr",
            "valueType": "SampledData"
        }
    }
}
```
---
**步骤-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "55423-8",
                "system": "http://loinc.org",
                "display": "Number of steps"
            }
        ],        
        "periodInterval": 60,
        "typeName": "stepsCount",
        "value": {
            "defaultPeriod": 5000,
            "unit": "",
            "valueName": "steps",
            "valueType": "SampledData"
        }
    }
}
```
---
**血压-SampledData**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 60,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "SampledData"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "defaultPeriod": 5000,
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "SampledData"
                }
            }
        ]
    }
}
```
---
**血压-数量**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "85354-9",
                "display": "Blood pressure panel with all children optional",
                "system": "http://loinc.org"
            }
        ],
        "periodInterval": 0,
        "typeName": "bloodpressure",
        "components": [
            {
                "codes": [
                    {
                        "code": "8867-4",
                        "display": "Diastolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "diastolic",
                    "valueType": "Quantity"
                }
            },
            {
                "codes": [
                    {
                        "code": "8480-6",
                        "display": "Systolic blood pressure",
                        "system": "http://loinc.org"
                    }
                ],
                "value": {
                    "unit": "mmHg",
                    "valueName": "systolic",
                    "valueType": "Quantity"
                }
            }
        ]
    }
}
```
---
**设备已删除-CodeableConcept**
```json
{
    "templateType": "CodeValueFhir",
    "template": {
        "codes": [
            {
                "code": "deviceEvent",
                "system": "https://www.mydevice.com/v1",
                "display": "Device Event"
            }
        ],
        "periodInterval": 0,
        "typeName": "deviceRemoved",
        "value": {
            "text": "Device Removed",
            "codes": [
                {
                    "code": "deviceRemoved",
                    "system": "https://www.mydevice.com/v1",
                    "display": "Device Removed"
                }
            ],
            "valueName": "deviceRemoved",
            "valueType": "CodeableConcept"
        }
    }
}
```
---

## <a name="next-steps"></a>后续步骤

查看 Azure IoT 连接器上有关 FHIR 的常见问题 (预览) 。

>[!div class="nextstepaction"]
>[适用于 FHIR 常见问题的 Azure IoT 连接器](fhir-faq.md#azure-iot-connector-for-fhir-preview)

*在 Azure 门户中，适用于 FHIR 的 Azure IoT 连接器称为 IoT Connector（预览版）。

FHIR 是 HL7 的注册商标，经 HL7 许可使用。
