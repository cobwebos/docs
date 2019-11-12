---
title: 传感器合作伙伴集成
description: 描述传感器合作伙伴集成
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1e819c94732e1cbc2de39e6400f8305b7df5aca1
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927658"
---
# <a name="sensor-partner-integration"></a>传感器合作伙伴集成

本文提供了有关 Azure FarmBeats **Translator**组件的信息，该组件启用传感器伙伴集成。

使用此组件，合作伙伴可以通过利用我们的 API，并将客户设备数据和遥测数据发送到 FarmBeats 数据中心，来开发与 FarmBeats 集成的传感器。 使用 FarmBeats 加速器可视化数据。 数据可用于数据合成，并用于构建计算机语言/人工智能模型。

## <a name="link-farmbeats-account"></a>链接 FarmBeats 帐户

一旦客户购买并部署了设备/传感器，他们就可以访问设备合作伙伴的 SaaS 门户（软件即服务）上的设备数据和遥测数据。 设备合作伙伴需要使客户能够将其帐户链接到其在 Azure 上的 FarmBeats 实例。 客户/系统集成商需要以下凭据：

   - 显示名称（用于定义此集成名称的用户的可选字段）
   - API 终结点
   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - EventHub 连接字符串
   - 开始日期

   > [!NOTE]
   > 开始日期启用历史数据馈送，即来自用户指定日期的数据。

## <a name="unlink-farmbeats"></a>取消链接 FarmBeats

客户能够取消链接现有的 FarmBeats 集成。 取消链接 FarmBeats 不应删除在客户的数据中心创建的任何设备/传感器元数据。 取消链接会执行以下操作：

   - 停止遥测流。
   - 删除和清除设备伙伴上的集成凭据。

## <a name="edit-farmbeats-integration"></a>编辑 FarmBeats 集成

如果客户端机密或连接字符串发生更改，则客户可以编辑 FarmBeats 集成设置。 在这种情况下，客户只能编辑以下字段：

   - 显示名称（如果适用）
   - 客户端密码（应显示为 "2x8 * * * * * * * * * * *" 格式，或显示/隐藏功能，而不是明文）
   - 连接字符串（应显示为 "2x8 * * * * * * * * * * *" 格式，或显示/隐藏功能，而不是明文）

## <a name="view-last-telemetry-sent"></a>查看最近发送的遥测

您可以查看上次**发送的遥测**数据的时间戳。 这是最新遥测成功发送到 FarmBeats 的时间。

## <a name="translator-development"></a>翻译人员开发

**基于 Rest API 的集成**

FarmBeats 的传感器数据集成功能通过 REST API 公开。 功能包括元数据定义、设备/传感器预配、设备和传感器管理。

**遥测引入**

遥测数据将映射到在 Azure 事件中心发布以进行处理的规范消息。 Azure EventHub 是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

**API 开发**

Api 包含 Swagger 技术文档。 有关 Api 及其相应的请求/响应的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)

**身份验证**

FarmBeats 利用 Microsoft Azure 的 Active Directory 身份验证。 Azure App Service 提供内置身份验证和授权支持。

有关详细信息，请参阅[Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats 数据中心使用持有者身份验证，需要以下凭据：
   - 客户端 ID
   - 客户端机密
   - 租户 ID

使用上述凭据，调用方可以请求访问令牌，该令牌需要在标头部分的后续 API 请求中发送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下面是提供访问令牌的示例 Python 代码，可用于对 FarmBeats 的后续 API 调用： 

```python
import azure 

from azure.common.credentials import ServicePrincipalCredentials 
import adal 
#FarmBeats API Endpoint 
ENDPOINT = "https://<yourdatahub>.azurewebsites.net" [Azure website](https://<yourdatahub>.azurewebsites.net)
CLIENT_ID = "<Your Client ID>"   
CLIENT_SECRET = "<Your Client Secret>"   
TENANT_ID = "<Your Tenant ID>" 
AUTHORITY_HOST = 'https://login.microsoftonline.com' 
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID 
#Authenticating with the credentials 
context = adal.AuthenticationContext(AUTHORITY) 
token_response = context.acquire_token_with_client_credentials(ENDPOINT, CLIENT_ID, CLIENT_SECRET) 
#Should get an access token here 
access_token = token_response.get('accessToken') 
```


**HTTP 请求标头**

以下是在对 FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头：


**标头** | **说明和示例**
--- | ---
Content-Type | FarmBeats 数据中心 Api 格式的请求格式（Content-type： application/<format>）为 json。 Content-type： application/json
授权 | 指定进行 API 调用授权所需的访问令牌：持有者 < 访问令牌 >
Accept | 响应格式。 对于 FarmBeats 数据中心 Api，格式为 json Accept： application/json

**API 请求**

若要进行 REST （具象状态传输） API 请求，请将 HTTP （GET、POST 或 PUT）方法、API 服务的 URL、URI （统一资源标识符）的 URL 合并到要查询的资源、将数据提交到、更新或删除，以及一个或多个 HTTP 请求只要. API 服务的 URL 是由客户提供的 API 终结点。 下面是示例： https://\<yourdatahub >. appname>.azurewebsites.net

或者，您可以在 GET 调用中包含查询参数以筛选、限制的大小，并对响应中的数据进行排序。

下面的示例请求是获取设备的列表：

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是创建设备（此示例包含具有请求正文的输入 json）。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON （JavaScript 对象表示法）是一种与语言无关的公共数据格式，提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅[json.org](http://json.org)。

## <a name="metadata-specifications"></a>元数据规范

FarmBeats 数据中心具有以下 Api，可让设备合作伙伴创建和管理设备/传感器元数据。  

- /**DeviceModel** -设备型号对应于设备的元数据，如制造商、设备的 "网关" 或 "节点" 类型。  
- /**设备**-设备对应于在场中存在的物理设备。
- /**SensorModel**模型对应于传感器的元数据，如制造商、传感器类型、模拟或数字、传感器度量值（如环境温度、压力等）。
- **传感器**传感器 /对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。

  设备型号| DeviceModel 对应于设备的元数据，如制造商、设备的 "网关" 或 "节点" 类型。
  --- | ---
  类型（节点、网关）  | 1 星 |
  制造商  | 2 星 |
  ProductCode  | 设备产品代码或型号名称/编号。 例如，EnviroMonitor # 6800 |
  端口  | 端口名称和类型（数字/模拟）  |
  名称  | 标识资源的名称。 例如，模型名称/产品名称 |
  说明  | 提供模型的有意义说明 |
  属性  | 制造商提供的其他属性 |
  **设备** | **设备对应于在场中存在的物理设备。每台设备都具有唯一的设备 ID** |
DeviceModelId  |关联的设备模型的 ID。 |
hardwareId   |设备的唯一 ID，如 MAC 地址等。  |
reportingInterval |报告间隔（秒） |
位置    |设备纬度（-90 到 + 90）/longitude （-180 至180）/elevation （米） |
ParentDeviceId | 此设备连接到的父设备的 ID。 例如， 连接到网关的节点;节点将 parentDeviceID 作为网关 |
  名称  | 标识资源的名称。  设备合作伙伴将需要发送与设备伙伴端设备名称一致的名称。 如果设备伙伴端的设备名称是用户定义的，则应将同一用户定义的名称传播到 FarmBeats  |
  说明  | 提供有意义的说明  |
  属性  |制造商提供的其他属性  |
  **传感器型号** | SensorModel 对应于传感器的元数据，如制造商、传感器类型、模拟或数字、传感器度量值（如环境温度、压力等）。 |
  类型（模拟、数字）  |提及模拟或数字传感器|
  制造商  | 制造商名称 |
  ProductCode  | 产品代码或型号名称/编号。 例如，RS-CO2-N01  |
  SensorMeasures > 名称  | 传感器度量值的名称。 仅支持小写。 对于不同深度的度量值，请指定深度。 例如，soil_moisture_15cm 此名称必须与遥测数据保持一致。 |
  SensorMeasures > 数据类型  | 遥测数据类型。 当前支持 double  |
  sensorMeasures > 类型  | 传感器遥测数据的度量类型。 以下是系统定义的类型： AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、压力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要添加更多，请参阅/ExtendedType API
  SensorMeasures > 单元 | 传感器遥测数据的单位。 以下是系统定义的单位： NoUnit、摄氏、华氏、开氏度、Rankine、Pascal、水星、PSI、毫米、厘米、米、英寸、英尺、英里、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、学位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole，MilliSiemensPerCentiMeter，Centibar，DeciSiemensPerMeter，KiloPascal，VolumetricIonContent，升，MilliLiter，Seconds，UnixTimestamp，MicroMolPerMeterSquaredPerSecond，InchesPerHour，若要添加更多，请参阅/ExtendedType API
  SensorMeasures > aggregationType  | 无、平均值、最小值、最小值、StandardDeviation
  SensorMeasures > 深度  | 传感器的深度（以厘米为单位）（例如，地面上的湿气10厘米度量值）
  sensorMeasures > 说明  | 提供度量值的有意义说明
  name  | 标识资源的名称。 例如，模型名称/产品名称
  说明  | 提供模型的有意义说明
  properties  | 制造商提供的其他属性
  **器**  |
  hardwareId  | 制造商设置的传感器的唯一 ID
  sensorModelId  | 关联的传感器型号的 ID。
  location  | 传感器纬度（-90 到 + 90）/longitude （-180-180）/elevation （米）
  端口 > 名称  |设备上传感器连接到的端口的名称和类型。 此名称需要与设备模型中定义的名称相同
  deviceId  | 传感器连接到的设备的 ID
  name  | 标识资源的名称。 例如，"传感器名称/产品名称" 和 "型号/产品代码"。
  说明  | 提供有意义的说明
  properties  | 制造商提供的其他属性

 有关每个对象及其属性的信息，请参阅[swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > Api 为创建的每个实例返回唯一 Id。 设备管理和元数据同步的转换器需要保留此 ID。


**元数据同步**

转换器应将更新发送到元数据。 例如，更新方案包括：更改设备/传感器名称、更改设备/传感器位置。

转换器应该能够添加新设备和/或传感器，这些设备已由用户在 FarmBeats 的链接后安装。 同样，如果用户已更新设备/传感器，则应在 FarmBeats 中为相应的设备/传感器更新相同的。 更新设备/传感器的典型方案包括：更改设备位置、在节点中添加传感器等。


> [!NOTE]
> 设备/传感器元数据不支持删除。
>
> 若要更新元数据，必须在设备/传感器上调用/Get/{id}，更新更改的属性，然后执行/Put/{id}，以使用户设置的任何属性都不会丢失

### <a name="adding-new-typesunit"></a>添加新类型/单元

FarmBeats 支持添加新的传感器度量值类型和单位。 有关/ExtendedType API 的详细信息， [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="telemetry-specifications"></a>遥测规范

遥测数据将映射到在 Azure 事件中心发布以进行处理的规范消息。 Azure EventHub 是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

## <a name="send-telemetry-data-to-farmbeats"></a>将遥测数据发送到 FarmBeats

若要将遥测数据发送到 FarmBeats，需要创建将消息发送到 FarmBeats 中的事件中心的客户端。 有关遥测数据的详细信息，请参阅[将遥测发送到事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

下面是一个示例 Python 代码，它将遥测作为客户端发送到指定的事件中心：

```python
import azure
from azure.eventhub import EventHubClient, Sender, EventData, Receiver, Offset
EVENTHUBCONNECTIONSTRING = "<EventHub Connection String provided by customer>"
EVENTHUBNAME = "<EventHub Name provided by customer>"

write_client = EventHubClient.from_connection_string(EVENTHUBCONNECTIONSTRING, eventhub=EVENTHUBNAME, debug=False)
sender = write_client.add_sender(partition="0")
write_client.run()
for i in range(5):
    telemetry = "<Canonical Telemetry message>"
    print("Sending telemetry: " + telemetry)
    sender.send(EventData(telemetry))
write_client.stop()

```

规范消息格式如下所示：

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```
遥测 json 中的所有密钥名称应采用小写形式，例如 deviceid、sensordata 等。

例如，遥测消息：


```json
{
  "deviceid": "7f9b4b92-ba45-4a1d-a6ae-c6eda3a5bd12",
  "timestamp": "2019-06-22T06:55:02.7279559Z",
  "version" : "1",
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

## <a name="troubleshooterror-management"></a>故障排除/错误管理

**排查选项/支持问题**

如果客户无法在指定的 FarmBeats 实例中接收设备数据和/或遥测数据，设备伙伴应提供支持和一种解决相同问题的机制。

**遥测数据保留**

还应为预定义的时间段保留遥测数据，以便在出现错误或数据丢失的情况时调试或重新发送遥测数据。

**错误管理/错误通知**

如果错误影响设备伙伴系统中的设备/传感器元数据/数据集成或遥测数据流，则应该向客户通知相同的情况。 还应设计和实现一种解决错误的机制。

**连接清单**

设备制造商/合作伙伴可以使用以下测试/检查表来确保客户提供的凭据准确无误。

   - 使用提供的凭据检查是否收到访问令牌
   - 使用收到的访问令牌检查 API 调用是否成功
   - 检查是否已建立 EventHub 客户端连接

## <a name="next-steps"></a>后续步骤

有关 REST API 的详细信息，请参阅[REST API](references-for-farmbeats.md#rest-api)。
