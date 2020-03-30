---
title: 查询引入遥测数据
description: 本文介绍如何查询引入的遥测数据。
author: sunasing
ms.topic: article
ms.date: 03/11/2020
ms.author: sunasing
ms.openlocfilehash: f717903b3f953e04c793092c86802f2006de7e82
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349805"
---
# <a name="query-ingested-telemetry-data"></a>查询引入遥测数据

本文介绍如何查询 Azure FarmBeats 中引入的传感器数据。

从物联网 （IoT） 资源（如设备和传感器）中引入数据是 FarmBeats 中常见的方案。 为设备和传感器创建元数据，然后以规范格式将历史数据引入 FarmBeats。 一旦在 FarmBeats Datahub 上提供了传感器数据，我们可以查询相同的数据来生成可操作的见解或生成模型。

## <a name="before-you-begin"></a>开始之前

在继续本文之前，请确保已安装 FarmBeats 并将传感器遥测数据从 IoT 设备引入 FarmBeats。

要引入传感器遥测数据，请访问[引入历史遥测数据](ingest-historical-telemetry-data-in-azure-farmbeats.md)

在继续操作之前，还需要确保熟悉 FarmBeats REST API，因为您将使用 API 查询引入的遥测数据。 有关服务器场节拍 API 的详细信息，请参阅[服务器场节拍 REST API](rest-api-in-azure-farmbeats.md)。 **确保您能够向服务器场数据库终结点发出 API 请求**。

## <a name="query-ingested-sensor-telemetry-data"></a>查询引入传感器遥测数据

有两种方法可以从 FarmBeats 访问和查询遥测数据：

- API 和
- 时间序列见解 （TSI）。

### <a name="query-using-rest-api"></a>使用 REST API 查询

按照以下步骤使用 FarmBeats REST API 查询引入的传感器遥测数据：

1. 确定您感兴趣的传感器。 您可以通过对 /传感器 API 发出 GET 请求来执行此操作。

> [!NOTE]
> 相关传感器对象的**ID**和**传感器模型 Id。**

2. 在 /传感器模型 API 上为**传感器 ModelId**制作 GET/{id}，如步骤 1 所述。 "传感器模型"具有传感器引入遥测的所有元数据和详细信息。 例如，"**传感器模型"** 对象中的**传感器测量**具有有关传感器发送的度量值以及类型和单位的详细信息。 例如，

  ```json
  {
      "name": "moist_soil_last <name of the sensor measure - this is what we will receive as part of the queried telemetry data>",
      "dataType": "Double <Data Type - eg. Double>",
      "type": "SoilMoisture <Type of measure eg. temperature, soil moisture etc.>",
      "unit": "Percentage <Unit of measure eg. Celsius, Percentage etc.>",
      "aggregationType": "None <either of None, Average, Maximum, Minimum, StandardDeviation>",
      "description": "<Description of the measure>"
  }
  ```
记下来自传感器型号 GET/{id} 调用的响应。

3. 使用以下输入负载对 /遥测 API 执行 POST 调用

  ```json
  {
    "sensorId": "<id of the sensor as noted in step 1>",
    "searchSpan": {
      "from": "<desired start timestamp in ISO 8601 format; default is UTC>",
      "to": "<desired end timestamp in ISO 8601 format; default is UTC>"
    },
    "filter": {
      "tsx": "string"
    },
    "projectedProperties": [
      {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    ]
  }
  ```
4. 来自 /遥测 API 的响应如下所示：

  ```json
  {
    "timestamps": [
      "2020-XX-XXT07:30:00Z",
      "2020-XX-XXT07:45:00Z"
    ],
    "properties": [
      {
        "values": [
          "<id of the sensor>",
          "<id of the sensor>"
        ],
        "name": "Id",
        "type": "String"
      },
      {
        "values": [
          2.1,
          2.2
        ],
        "name": "moist_soil_last <name of the SensorMeasure as defined in the SensorModel object>",
        "type": "Double <Data Type of the value - eg. Double>"
      }
    ]
  }
  ```
在上述示例响应中，查询的传感器遥测数据提供了两个时间戳的数据以及两个时间戳中的度量值名称（"moist_soil_last"）和报告遥测的值。 您需要参考关联的传感器模型（如步骤 2 中所述），以解释报告值的类型和单位。

### <a name="query-using-azure-time-series-insights-tsi"></a>使用 Azure 时间序列见解 （TSI） 查询

FarmBeats 利用[Azure 时间序列见解 （TSI）](https://azure.microsoft.com/services/time-series-insights/)在 IoT 比例范围内引入、存储、查询和可视化数据，这些数据高度上下文化和针对时间序列进行了优化。

遥测数据在 EventHub 上接收，然后处理并推送到 FarmBeats 资源组中的 TSI 环境。 然后可以直接从 TSI 查询数据。 有关详细信息，请参阅[TSI 文档](https://docs.microsoft.com/azure/time-series-insights/time-series-insights-explorer)

按照以下步骤在 TSI 上可视化数据：

1. 转到**Azure 门户** > **服务器场数据Hub 资源组**>选择**时间序列见解**环境 （tsi-xxx） >**数据访问策略**。 使用读取器或参与者访问权限添加用户。
2. 转到**时间序列见解**环境 （tsi-xxxx）**的概述**页面，然后选择**时序见解资源管理器 URL**。 现在，您将能够可视化引入的遥测数据。

除了存储、查询和可视化遥测之外，TSI 还支持集成到 Power BI 仪表板。 有关详细信息，请参阅[此处]( https://docs.microsoft.com/azure/time-series-insights/how-to-connect-power-bi)

## <a name="next-steps"></a>后续步骤

现在，您从 Azure FarmBeats 实例中查询了传感器数据。 现在，了解如何为服务器场[生成地图](generate-maps-in-azure-farmbeats.md#generate-maps)。
