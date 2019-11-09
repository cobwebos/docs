---
title: 引入历史遥测数据
description: 描述如何获取引入历史遥测数据
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e6bd9b5c09e1af5ec587e1f0e52ab25d21d2293b
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889610"
---
# <a name="ingest-historical-telemetry-data"></a>引入历史遥测数据

本文介绍如何将历史传感器数据引入 Azure FarmBeats。

FarmBeats 中常见的情况是引入（IoT）用于资源（如设备和传感器）的历史物联网数据。 为设备和传感器创建元数据，然后将历史数据以规范格式引入 FarmBeats。

## <a name="before-you-begin"></a>开始之前

在继续阅读本文之前，请确保已安装 FarmBeats，并从 IoT 收集了历史数据。

## <a name="enable-partner-access"></a>启用合作伙伴访问

需要启用与 Azure FarmBeats 实例的合作伙伴集成。 此步骤将创建一个客户端，该客户端将有权访问你的 Azure FarmBeats 作为你的设备伙伴，并提供后续步骤中所需的以下值。

- API 终结点–这是数据中心 URL，例如， https://<datahub>。 azurewebsites.net
- 租户 ID
- 客户端 ID
- 客户端机密
- EventHub 连接字符串

请按照以下步骤生成这些内容：

>[!NOTE]
> 您必须是管理员才能执行以下步骤。

1. 下载此[脚本](https://aka.ms/farmbeatspartnerscript)，并将其解压缩到本地驱动器上。 你将在 ZIP 文件中找到两个文件。
2. 登录到[Azure 门户](https://portal.azure.com/)并打开 Cloud Shell （门户右上栏中提供了此选项）  

    ![项目场节拍](./media/for-tutorials/navigation-bar-1.png)

3. 确保环境设置为**PowerShell**。

    ![项目场节拍](./media/for-tutorials/power-shell-new-1.png)

4. 上载 Cloud Shell 中下载的两个文件（从上面的步骤1开始）。  

    ![项目场节拍](./media/for-tutorials/power-shell-two-1.png)

5. 中转到上载文件的目录

   >[!NOTE]
   > 默认情况下，文件将上传到主目录/home/username/。
6. 使用命令运行脚本：  

    ```azurepowershell-interactive
    ./generateCredentials.ps1
    ```

7. 按照屏幕上的说明完成该过程。

## <a name="create-devicesensor-metadata"></a>创建设备/传感器元数据

 现在，你已拥有所需的凭据，可以通过使用 FarmBeats Api 创建元数据来定义设备和传感器。

 FarmBeats 数据中心具有以下 Api，可用于创建和管理设备/传感器元数据。   

- /**DeviceModel** -设备型号对应于设备的元数据，如制造商、设备的 "网关" 或 "节点" 类型。  
- /**设备**-设备对应于在场中存在的物理设备。  
- /**SensorModel**模型对应于传感器的元数据，如制造商、模拟或数字、传感器度量值（如环境温度或压力）。
- **传感器**传感器 /对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。  


|        设备模式   |  建议   |
| ------- | -------             |
|     类型（节点、网关）        |          1 星      |
|          制造商            |         2 星     |
|  ProductCode                    |  设备产品代码或型号名称/编号。 例如，EnviroMonitor # 6800。  |
|            端口          |     端口名称和类型（数字/模拟）
|     名称                 |  标识资源的名称。 例如，模型名称/产品名称。
      说明     | 提供模型的有意义说明
|    属性          |    制造商提供的其他属性   |
|    **设备**             |                      |
|   DeviceModelId     |     关联设备模型的 ID  |
|  hardwareId          | 设备的唯一 ID，如 MAC 地址等。
|  reportingInterval        |   报告间隔（秒）
|  位置            |  设备纬度（-90 到 + 90）/Longitude （-180 至180）/Elevation （米）   
|ParentDeviceId       |    此设备连接到的父设备的 ID。 例如，连接到网关的节点。 节点将 parentDeviceId 作为网关。  |
|    名称            | 用于标识资源的名称。 设备合作伙伴必须发送与合作伙伴端设备名称一致的名称。 如果合作伙伴设备名称是用户定义的，则相同的用户定义名称应传播到 FarmBeats。|
|     说明       |      提供有意义的说明  |
|     属性    |  制造商提供的其他属性
|     **传感器型号**        |          |
|       类型（模拟、数字）          |      传感器类型（模拟或数字）       |
|          制造商            |       传感器的制造商     |
|     ProductCode| 产品代码或型号名称/编号。 例如，RS-N01。 |
|       SensorMeasures > 名称       | 传感器度量值的名称。 仅支持小写。 对于不同深度的度量值，请指定深度。 例如，soil_moisture_15cm。 此名称必须与遥测数据一致  |
|          SensorMeasures > 数据类型       |遥测数据类型。 当前支持 double|
|    sensorMeasures > 类型    |传感器遥测数据的度量类型。 以下是系统定义的类型： AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、压力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要添加更多，请参阅/ExtendedType API。|
|        SensorMeasures > 单元              | 传感器遥测数据的单位。 以下是系统定义的单位： NoUnit、摄氏、华氏、开氏度、Rankine、Pascal、水星、PSI、毫米、厘米、米、英寸、英尺、英里、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、学位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole，MilliSiemensPerCentiMeter，Centibar，DeciSiemensPerMeter，KiloPascal，VolumetricIonContent，升，MilliLiter，Seconds，UnixTimestamp，MicroMolPerMeterSquaredPerSecond，InchesPerHour，若要添加更多，请参阅/ExtendedType API。|
|    SensorMeasures > aggregationType    |  值可以是 "无"、"平均值"、"最大值" 或 "StandardDeviation"  |
|          名称            | 标识资源的名称。 例如，模型名称/产品名称。  |
|    说明        | 提供模型的有意义说明  |
|   属性       |  制造商提供的其他属性  |
|    **器**      |          |
| hardwareId          |   制造商设置的传感器的唯一 ID |
|  sensorModelId     |    关联的传感器型号的 ID   |
| location          |  传感器纬度（-90 到 + 90）/Longitude （-180-180）/Elevation （米）|
|   端口 > 名称        |  设备上传感器连接到的端口的名称和类型。 此名称需要与设备模型中定义的名称相同。 |
|    DeviceID  |    传感器连接到的设备的 ID     |
| 名称            |   标识资源的名称。 例如，"传感器名称/产品名称" 和 "型号/产品代码"。|
|    说明      | 提供有意义的说明 |
|    属性        |制造商提供的其他属性 |

有关对象的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

**用于创建元数据的 API 请求**

若要发出 API 请求，请将 HTTP （POST）方法、API 服务的 URL、要查询的资源的 URI、提交数据以创建或删除请求以及添加一个或多个 HTTP 请求标头组合起来。 API 服务的 URL 是 API 终结点，即数据中心 URL （ https://<yourdatahub>. azurewebsites.net）  

**身份验证**：

FarmBeats 数据中心使用持有者身份验证，此身份验证需要我们在上一节中生成的以下凭据。

- 客户端 ID
- 客户端机密
- 租户 ID  

使用上述凭据，调用方可以请求访问令牌，该令牌需要在标头部分的后续 API 请求中发送，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

**HTTP 请求标头**：

以下是在对 FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头：

- Content-type： application/json
- 授权：持有者 < 访问令牌 >
- 接受： application/json

**用于创建元数据的输入有效负载**：

**DeviceModel**


```json
{
  "type": "Node",
  "manufacturer": "string",
  "productCode": "string",
  "ports": [
    {
      "name": "string",
      "type": "Analog"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

设备

```json
{
  "deviceModelId": "string",
  "hardwareId": "string",
  "farmId": "string",
  "reportingInterval": 0,
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "parentDeviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}
```

SensorModel

```json
{
  "type": "Analog",
  "manufacturer": "string",
  "productCode": "string",
  "sensorMeasures": [
    {
      "name": "string",
      "dataType": "Double",
      "type": "string",
      "unit": "string",
      "aggregationType": "None",
      "depth": 0,
      "description": "string"
    }
  ],
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
传感器

```json
{
  "hardwareId": "string",
  "sensorModelId": "string",
  "location": {
    "latitude": 0,
    "longitude": 0,
    "elevation": 0
  },
  "depth": 0,
  "port": {
    "name": "string",
    "type": "Analog"
  },
  "deviceId": "string",
  "name": "string",
  "description": "string",
  "properties": {
    "additionalProp1": {},
    "additionalProp2": {},
    "additionalProp3": {}
  }
}

```
下面的示例请求是创建一个设备（该设备的输入 json 为带有请求正文的有效负载）。  

```azurepowershell-interactive
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "
{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\",}"*
```

> [!NOTE]
> Api 为创建的每个实例返回唯一 Id。 您必须保留用于发送相应遥测消息的 Id。

**发送遥测数据**

现在，你已在 FarmBeats 中创建了设备和传感器，接下来可以发送关联的遥测消息。  

**创建遥测客户端**

必须将遥测发送到 Azure 事件中心进行处理。 Azure EventHub 是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。 若要将遥测数据发送到 FarmBeats，需要创建将消息发送到 FarmBeats 中的事件中心的客户端。 有关发送遥测数据的详细信息，请参阅[Azure 事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

**将遥测消息作为客户端发送**

建立作为 EventHub 客户端的连接后，可以将消息作为 json 发送到 EventHub。  
将历史传感器数据格式转换为 Azure FarmBeats 了解的规范格式。 规范消息格式如下所示：  



 ```
  {   
      “deviceid”: “<id of the Device created>”,   
      "timestamp": "<timestamp in ISO 8601 format>",     
      "version" : "1",   
      "sensors":
      [     
      {        
          "id": "<id of the sensor created>”       
          "sensordata": [         
          {            
              "timestamp": "< timestamp in ISO 8601 format >",           
              "<sensor measure name (as defined in the Sensor Model)>": value          
    },          
    {            
    "timestamp": "<timestamp in ISO 8601 format>",           
     "<sensor measure name (as defined in the Sensor Model)>": value          
    }        
    ]      
    }  
    }
```


添加相应的设备和传感器之后，请按照上一部分中所述，在遥测消息中获取 deviceid 和 sensorid。

遥测消息示例：


 ```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version": "1",
  "sensors": [
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 15
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 42
        }
      ]
    },
    {
      "id": "d8e7beb4-72de-4eed-9e00-45e09043a0b3",
      "sensordata": [
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_max": 20
        },
        {
          "timestamp": "2019-06-22T06:55:02.7279559Z",
          "hum_min": 89
        }
      ]
    }
  ]
}
```


## <a name="next-steps"></a>后续步骤

有关基于 Rest API 的集成详细信息的详细信息，请参阅[REST API](references-for-farmbeats.md#rest-api)。
