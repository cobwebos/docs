---
title: 传感器合作伙伴集成
description: 本文介绍传感器合作伙伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 88a8dcb53ab2f845f52121b11c96c23ad0a3e791
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2020
ms.locfileid: "87078917"
---
# <a name="sensor-partner-integration"></a>传感器合作伙伴集成

本文提供有关 Azure FarmBeats 转换器组件的信息，该组件启用传感器伙伴集成。

使用此组件，合作伙伴可以使用 FarmBeats 数据中心 API 与 FarmBeats 集成，并将客户设备数据和遥测发送到 FarmBeats 数据中心。 数据在 FarmBeats 中可用后，将使用 FarmBeats 加速器对其进行可视化，并且该数据可用于数据融合和构建机器学习/人工智能模型。

## <a name="before-you-start"></a>开始之前

若要开发转换器组件，将需要以下凭据，通过这些凭据才能访问 FarmBeats API。

- API 终结点
- 租户 ID
- 客户端 ID
- 客户端机密
- EventHub 连接字符串

请参阅此部分获取上述凭据：[启用设备集成](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>转换器开发

**基于 REST API 的集成**

FarmBeats 的传感器数据集成功能通过 REST API 公开。 功能包括元数据定义、设备和传感器预配以及设备和传感器管理。

**遥测引入**

遥测数据被映射到在 Azure 事件中心发布的规范消息以进行处理。 Azure 事件中心是一个服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

**API 开发**

API 包含 Swagger 技术文档。 有关 API 及其相应的请求或响应的详细信息，请参阅 [Swagger](https://aka.ms/FarmBeatsSwagger)。

**身份验证**

FarmBeats 使用 Microsoft Azure Active Directory 身份验证。 Azure 应用服务提供内置的身份验证和授权支持。

有关详细信息，请参阅 [Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats 数据中心使用持有者身份验证，该验证方法需要以下凭据：
   - 客户端 ID
   - 客户端机密
   - 租户 ID

使用这些凭据，调用方可以请求访问令牌。 在后续 API 请求中需要发送令牌，如以下标头部分所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下面的示例 Python 代码提供访问令牌，该令牌可用于对 FarmBeats 的后续 API 调用。

```python
import requests
import json
import msal

# Your service principal App ID
CLIENT_ID = "<CLIENT_ID>"
# Your service principal password
CLIENT_SECRET = "<CLIENT_SECRET>"
# Tenant ID for your Azure subscription
TENANT_ID = "<TENANT_ID>"

AUTHORITY_HOST = 'https://login.microsoftonline.com'
AUTHORITY = AUTHORITY_HOST + '/' + TENANT_ID

ENDPOINT = "https://<yourfarmbeatswebsitename-api>.azurewebsites.net"
SCOPE = ENDPOINT + "/.default"

context = msal.ConfidentialClientApplication(CLIENT_ID, authority=AUTHORITY, client_credential=CLIENT_SECRET)
token_response = context.acquire_token_for_client(SCOPE)
# We should get an access token here
access_token = token_response.get('access_token')
```


**HTTP 请求标头**

下面是在对 FarmBeats 数据中心进行 API 调用时需要指定的最常见请求标头。


**标头** | **说明和示例**
--- | ---
Content-Type | 请求格式 (Content-Type: application/<format>)。 对于 FarmBeats 数据中心 API，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 Authorization:持有者 <Access-Token>
Accept | 响应格式。 对于 FarmBeats 数据中心 API，格式为 JSON。 Accept: application/json

**API 请求**

若要发出 REST API 请求，请将 HTTP（GET、POST 或 PUT）方法、API 服务的 URL、用于查询、提交数据、更新或删除的资源的统一资源标识符 (URI) 以及一个或多个 HTTP 请求标头组合在一起。 API 服务的 URL 是提供的 API 终结点。 下面是一个示例： https:// \<yourdatahub-website-name> . azurewebsites.net

或者，可在 GET 调用中包含查询参数以筛选数据、限制数据的大小，并对响应中的数据进行排序。

下面的示例请求是获取设备列表。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是要创建设备。 （此示例包含一个带有请求正文的输入 JSON。）

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的常见数据格式，该格式提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 [json.org](http://json.org)。

## <a name="metadata-specifications"></a>元数据规范

FarmBeats 数据中心具有以下 API，设备合作伙伴可通过这些 API 创建和管理设备或传感器元数据。

- /**DeviceModel**：DeviceModel 对应于设备的元数据，如制造商和设备类型（网关或节点）。
- /**Device**：Device 对应于存在于场上的物理设备。
- /**SensorModel**：SensorModel 对应于传感器的元数据，如制造商、传感器类型（模拟或数字）以及传感器度量值（如环境温度和压力）。
- /**Sensor**：Sensor 对应于记录值的物理传感器。 传感器通常连接到具有设备 ID 的设备。

  DeviceModel | 描述 |
  --- | ---
  类型（节点、网关）  | 设备类型 - 节点或网关 |
  制造商  | 制造商的名称 |
  ProductCode  | 设备产品代码或模型名称或编号。 例如 EnviroMonitor#6800。 |
  端口  | 端口名称和类型（数字或模拟）。  |
  名称  | 用于标识资源的名称。 例如，模型名称或产品名称。 |
  描述  | 提供对模型的有意义说明。 |
  属性  | 制造商提供的其他属性。 |
  **设备** | **描述** |
  DeviceModelId  |关联的设备模型的 ID。 |
  HardwareId   |设备的唯一 ID，如 MAC 地址。  |
  ReportingInterval |以秒为单位的报告间隔。 |
  位置    |设备纬度（-90 到 +90）、经度（-180 到 180）和海拔（以米为单位）。 |
  ParentDeviceId | 此设备连接到的父设备的 ID。 例如，如果某个节点连接到网关，则该节点将 parentDeviceID 作为网关。 |
  名称  | 用于标识资源的名称。 设备合作伙伴需要发送与设备伙伴端的设备名称一致的名称。 如果设备伙伴端的设备名称是用户定义的，则应将同一用户定义的名称传播到 FarmBeats。  |
  说明  | 提供有意义的说明。  |
  属性  |制造商提供的其他属性。  |
  **SensorModel** | **描述** |
  类型（模拟、数字）  |提及模拟或数字传感器。|
  制造商  | 制造商的名称。 |
  ProductCode  | 产品代码或模型名称或编号。 例如 RS-CO2-N01。  |
  SensorMeasures > 名称  | 传感器度量值的名称。 仅支持小写。 对于不同深度的度量，请指定深度。 例如 soil_moisture_15cm。 此名称必须与遥测数据保持一致。 |
  SensorMeasures > 数据类型  | 遥测数据类型。 目前支持 double。 |
  SensorMeasures > 类型  | 传感器遥测数据的度量类型。 以下是系统定义的类型：AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、Pressure、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要添加更多类型，请参阅 /ExtendedType API。
  SensorMeasures > 单位 | 传感器遥测数据的单位。 以下是系统定义的单位：NoUnit、Celsius、Fahrenheit、Kelvin, Rankine、Pascal、Mercury、PSI, MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond 和 InchesPerHour. 若要添加更多类型，请参阅 /ExtendedType API。
  SensorMeasures > AggregationType  | 无、平均值、最大值、最小值或标准偏差。
  SensorMeasures > 深度  | 传感器的深度（以厘米为单位）。 例如，地下 10 厘米的湿度度量。
  SensorMeasures > 说明  | 提供对度量的有意义说明。
  名称  | 用于标识资源的名称。 例如，模型名称或产品名称。
  描述  | 提供对模型的有意义说明。
  属性  | 制造商提供的其他属性。
  **传感器**  | **描述** |
  HardwareId  | 制造商设置的传感器的唯一 ID。
  SensorModelId  | 关联的传感器模型的 ID。
  位置  | 传感器纬度（-90 到 +90）、经度（-180 到 180）和海拔（以米为单位）。
  端口 > 名称  |设备上传感器连接到的端口的名称和类型。 此名称必须与设备模型中定义的名称相同。
  DeviceId  | 传感器连接到的设备的 ID。
  名称  | 用于标识资源的名称。 例如，传感器名称或产品名称以及型号或产品代码。
  说明  | 提供有意义的说明。
  属性  | 制造商提供的其他属性。

 有关每个对象及其属性的信息，请参阅 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > API 为创建的每个实例返回唯一 ID。 用于设备管理和元数据同步的转换器需要保留此 ID。


**元数据同步**

转换器应发送有关元数据的更新。 例如，更新方案是更改设备或传感器名称以及更改设备或传感器位置。

转换器应该能够添加链接 FarmBeats 后用户安装的新设备或传感器。 同样，如果用户更新了某个设备或传感器，则应在 FarmBeats 中更新相应的设备或传感器。 需要更新设备或传感器的典型方案是更改设备位置或在节点中添加传感器。


> [!NOTE]
> 不支持删除设备或传感器元数据。
>
> 若要更新元数据，必须在设备或传感器上调用 /Get/{id}，更新更改的属性，然后执行 /Put/{id}，以免丢失用户设置的任何属性。

### <a name="add-new-types-and-units"></a>添加新类型和单位

FarmBeats 支持添加新的传感器度量值类型和单位。 有关 /ExtendedType API 的详细信息，请参阅 [Swagger](https://aka.ms/FarmBeatsSwagger)。

## <a name="telemetry-specifications"></a>遥测规范

遥测数据被映射到在 Azure 事件中心发布的规范消息以进行处理。 Azure 事件中心是一个服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

## <a name="send-telemetry-data-to-farmbeats"></a>将遥测数据发送到 FarmBeats

若要将遥测数据发送到 FarmBeats，请创建一个客户端，将消息发送到 FarmBeats 中的事件中心。 有关遥测数据的详细信息，请参阅[将遥测发送到事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

下面是一个示例 Python 代码，它将遥测作为客户端发送到指定的事件中心。

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
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```
遥测 JSON 中的所有密钥名称都应为小写。 如 deviceid 和 sensordata。

例如，下面是遥测消息：


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

> [!NOTE]
> 以下各部分与其他更改有关（例如 UI、错误管理等），传感器合作伙伴在开发转换器组件时可以参考。


## <a name="link-a-farmbeats-account"></a>链接 FarmBeats 帐户

客户购买并部署了设备或传感器后，就可以在设备合作伙伴的软件即服务 (SaaS) 门户上访问设备数据和遥测。 设备合作伙伴可以提供一种方法来输入以下凭据，以便客户能够将他们的帐户链接到 Azure 上的 FarmBeats 实例：

   - 显示名称（一个可选字段，以供用户定义此集成的名称）
   - API 终结点
   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - EventHub 连接字符串
   - 开始日期

   > [!NOTE]
   > 开始日期启用历史数据馈送，即数据来自用户指定的日期。

## <a name="unlink-farmbeats"></a>取消链接 FarmBeats

设备合作伙伴可以让客户取消链接现有的 FarmBeats 集成。 取消链接 FarmBeats 不应删除在 FarmBeats 数据中心中创建的任何设备或传感器元数据。 取消链接可执行以下操作：

   - 停止遥测流。
   - 删除并清除设备合作伙伴上的集成凭据。

## <a name="edit-farmbeats-integration"></a>编辑 FarmBeats 集成

如果客户端密码或连接字符串发生更改，设备合作伙伴可以让客户编辑 FarmBeats 集成设置。 在这种情况下，只能编辑以下字段：

   - 显示名称（如果适用）
   - 客户端密码（应以“2x8***********”格式显示，或显示为“显示/隐藏”功能，而不是清除文本）
   - 连接字符串（应以“2x8***********”格式显示，或显示为“显示/隐藏”功能，而不是清除文本）

## <a name="view-the-last-telemetry-sent"></a>查看最近发送的遥测

设备合作伙伴可以让客户查看最近发送的遥测的时间戳，时间戳位于“已发送的遥测”下。 这是最新遥测成功发送到 FarmBeats 的时间。

## <a name="troubleshooting-and-error-management"></a>故障排除和错误管理

**故障排除选项或支持**

如果客户无法在指定的 FarmBeats 实例中接收设备数据或遥测，设备合作伙伴应提供支持和故障排除机制。

**遥测数据保留**

还应在预定义的时间段内保留遥测数据，如果发生错误或数据丢失，这一方法在调试或重新发送遥测时会很有用。

**错误管理或错误通知**

如果错误影响设备或传感器元数据，或影响设备伙伴系统中的数据集成或遥测数据流，客户应该会收到通知。 还应设计和实现一种解决任何错误的机制。

**连接清单**

设备制造商或合作伙伴可以使用以下清单来确保客户提供的凭据准确无误：

   - 检查是否接收到具有提供的凭据的访问令牌。
   - 检查使用接收到的访问令牌的 API 调用是否成功。
   - 检查是否已建立 EventHub 客户端连接。

## <a name="next-steps"></a>后续步骤

有关 REST API 的详细信息，请参阅 [REST API](rest-api-in-azure-farmbeats.md)。
