---
title: 传感器合作伙伴集成
description: 本文介绍了传感器合作伙伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 1570e85d93e7d82b5a842697a7755603247375b0
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/06/2019
ms.locfileid: "74896532"
---
# <a name="sensor-partner-integration"></a>传感器合作伙伴集成

本文提供了有关 Azure FarmBeats Translator 组件的信息，该组件启用传感器伙伴集成。

使用此组件，合作伙伴可以开发与 FarmBeats 集成的传感器，以使用 API 并将客户设备数据和遥测发送到 FarmBeats Datahub。 使用 FarmBeats 加速器来可视化数据。 数据可用于数据合成，并用于构建机器学习/人工智能模型。

## <a name="link-a-farmbeats-account"></a>链接 FarmBeats 帐户

购买并部署设备或传感器之后，可以在设备合作伙伴的 "软件即服务" （SaaS）门户中访问设备数据和遥测数据。 设备合作伙伴允许将帐户链接到 Azure 上的 FarmBeats 实例。 您或您的系统集成商必须填写以下凭据：

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

您可以取消链接现有的 FarmBeats 集成。 取消链接 FarmBeats 不应删除在数据中心创建的任何设备或传感器元数据。 取消链接会执行以下操作：

   - 停止遥测流。
   - 删除并清除设备伙伴上的集成凭据。

## <a name="edit-farmbeats-integration"></a>编辑 FarmBeats 集成

如果客户端密码或连接字符串发生更改，则可以编辑 FarmBeats 集成设置。 在这种情况下，只能编辑以下字段：

   - 显示名称（如果适用）
   - 客户端密码（应以 "2x8 * * * * * * * * * *" 格式显示，或者显示为 "显示/隐藏" 功能，而不是明文）
   - 连接字符串（应显示为 "2x8 * * * * * * * * * * *" 格式，或显示/隐藏功能，而不是明文）

## <a name="view-the-last-telemetry-sent"></a>查看最近发送的遥测数据

你可以查看已发送的最后一个遥测数据的时间戳，该时间戳位于**发送的遥测**下。 这是最新遥测成功发送到 FarmBeats 的时间。

## <a name="translator-development"></a>翻译人员开发

**基于 REST API 的集成**

FarmBeats 的传感器数据集成功能通过 REST API 公开。 功能包括元数据定义、设备和传感器预配以及设备和传感器管理。

**遥测引入**

遥测数据将映射到在 Azure 事件中心发布以进行处理的规范消息。 Azure 事件中心是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

**API 开发**

Api 包含 Swagger 技术文档。 有关 Api 及其相应的请求或响应的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

**身份验证**

FarmBeats 使用 Microsoft Azure Active Directory 身份验证。 Azure App Service 提供内置身份验证和授权支持。

有关详细信息，请参阅[Azure Active Directory](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats Datahub 使用持有者身份验证，需要以下凭据：
   - 客户端 ID
   - 客户端机密
   - 租户 ID

使用这些凭据，调用方可以请求访问令牌。 需要在标头部分中的后续 API 请求中发送令牌，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

下面的示例 Python 代码提供访问令牌，该令牌可用于对 FarmBeats 的后续 API 调用。

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

下面是在对 FarmBeats Datahub 进行 API 调用时需要指定的最常见请求标头。


**标头** | **说明和示例**
--- | ---
Content-Type | 请求格式（Content-type： application/<format>）。 对于 FarmBeats Datahub Api，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 授权：持有者 < 访问令牌 >
接受 | 响应格式。 对于 FarmBeats Datahub Api，格式为 JSON。 接受： application/json

**API 请求**

若要发出 REST API 请求，请将 HTTP （GET、POST 或 PUT）方法、API 服务的 URL、与要查询的资源的统一资源标识符（URI）、将数据提交到、更新或删除以及一个或多个 HTTP 请求标头组合在一起。 API 服务的 URL 是提供的 API 终结点。 下面是一个示例： https://\<yourdatahub >. appname>.azurewebsites.net

或者，您可以在 GET 调用中包含查询参数以筛选、限制的大小，并对响应中的数据进行排序。

下面的示例请求是获取设备列表。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多数 GET、POST 和 PUT 调用都需要 JSON 请求正文。

下面的示例请求是创建设备。 （此示例包含一个带有请求正文的输入 JSON。）

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的公共数据格式，提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅[json.org](http://json.org)。

## <a name="metadata-specifications"></a>元数据规范

FarmBeats Datahub 具有以下 Api，可让设备合作伙伴创建和管理设备或传感器元数据。 

- /**DeviceModel**： DeviceModel 对应于设备的元数据，如制造商和设备类型（网关或节点）。 
- /**设备**：设备对应于在场上存在的物理设备。
- /**SensorModel**： SensorModel 对应于传感器的元数据，如制造商、传感器类型（模拟或数字）以及传感器度量值（如环境温度和压力）。
- /**传感器**：传感器对应于记录值的物理传感器。 传感器通常连接到设备 ID 为的设备。
  
  **DeviceModel** |  |
  --- | ---
  类型（节点、网关）  | 1 星 |
  制造商  | 2 星 |
  ProductCode  | 设备产品代码或型号名称。 例如，EnviroMonitor # 6800。 |
  端口  | 端口名称和类型，它是数字或模拟。  |
  名称  | 标识资源的名称。 例如，模型名称或产品名称。 |
  描述  | 提供模型的有意义的说明。 |
  属性  | 制造商提供的其他属性。 |
  **设备** |  |
  DeviceModelId  |关联的设备模型的 ID。 |
  HardwareId   |设备的唯一 ID，如 MAC 地址。  |
  ReportingInterval |报告间隔（秒）。 |
  Location    |设备纬度（-90 到 + 90）、经度（-180 到180）和提升（以米为单位）。 |
  ParentDeviceId | 此设备连接到的父设备的 ID。 例如，如果某个节点连接到网关，则该节点将 parentDeviceID 作为网关。 |
  名称  | 用于标识资源的名称。 设备合作伙伴需要发送与设备伙伴端设备名称一致的名称。 如果设备名称是设备伙伴端上定义的用户，则应将同一用户定义的名称传播到 FarmBeats。  |
  描述  | 提供有意义的说明。  |
  属性  |制造商提供的其他属性。  |
  **SensorModel** |  |
  类型（模拟、数字）  |提及模拟或数字传感器。|
  制造商  | 制造商的名称。 |
  ProductCode  | 产品代码或型号名称。 例如，RS-N01。  |
  SensorMeasures > 名称  | 传感器度量值的名称。 仅支持小写。 对于不同深度的度量，请指定深度。 例如，soil_moisture_15cm。 此名称必须与遥测数据保持一致。 |
  SensorMeasures > 数据类型  | 遥测数据类型。 目前支持 double。 |
  sensorMeasures > 类型  | 传感器遥测数据的度量类型。 以下是系统定义的类型： AmbientTemperature、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、压力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、Volume、WindDirection、WindRun、WindSpeed、Evapotranspiration、PAR。 若要添加更多，请参阅/ExtendedType API。
  SensorMeasures > 单元 | 传感器遥测数据的单位。 以下是系统定义的单位： NoUnit、摄氏、华氏、开氏度、Rankine、Pascal、水星、PSI、毫米、厘米、米、英寸、英尺、英里、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、学位、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、百分比、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、升、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond 和 InchesPerHour。 若要添加更多，请参阅/ExtendedType API。
  SensorMeasures > AggregationType  | 无、平均值、最小值、最小值或 StandardDeviation。
  SensorMeasures > 深度  | 传感器的深度（厘米）。 例如，地面上的湿气10厘米测量。
  SensorMeasures > 说明  | 提供度量的有意义的说明。
  名称  | 标识资源的名称。 例如，模型名称或产品名称。
  描述  | 提供模型的有意义的说明。
  属性  | 制造商提供的其他属性。
  **器**  |  |
  HardwareId  | 制造商设置的传感器的唯一 ID。
  sensorModelId  | 关联的传感器型号的 ID。
  Location  | 传感器纬度（-90 到 + 90）、经度（-180 到180）和提升（以米为单位）。
  端口 > 名称  |设备上传感器连接到的端口的名称和类型。 此名称必须与设备模型中定义的名称相同。
  DeviceId  | 传感器连接到的设备的 ID。
  名称  | 用于标识资源的名称。 例如，传感器名称或产品名称以及型号或产品代码。
  描述  | 提供有意义的说明。
  属性  | 制造商提供的其他属性。

 有关每个对象及其属性的信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > Api 为创建的每个实例返回唯一 Id。 设备管理和元数据同步的转换器需要保留此 ID。


**元数据同步**

转换器应将更新发送到元数据。 例如，更新方案更改设备或传感器名称，更改设备或传感器位置。

转换器应该能够添加 FarmBeats 的用户发布链接所安装的新设备或传感器。 同样，如果用户更新了某个设备或传感器，则应在 FarmBeats 中为相应的设备或传感器更新相同的。 需要更新设备或传感器的典型方案是设备位置中的更改或在节点中添加传感器。


> [!NOTE]
> 设备或传感器元数据不支持删除。
>
> 若要更新元数据，必须在设备或传感器上调用/Get/{id}，更新更改的属性，然后执行/Put/{id}，以使用户设置的任何属性都不会丢失。

### <a name="add-new-types-and-units"></a>添加新类型和单位

FarmBeats 支持添加新的传感器度量值类型和单位。 有关/ExtendedType API 的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsDatahubSwagger)。

## <a name="telemetry-specifications"></a>遥测规范

遥测数据将映射到在 Azure 事件中心发布以进行处理的规范消息。 Azure 事件中心是一项服务，可用于从连接的设备和应用程序引入实时数据（遥测）。

## <a name="send-telemetry-data-to-farmbeats"></a>将遥测数据发送到 FarmBeats

若要将遥测数据发送到 FarmBeats，请创建将消息发送到 FarmBeats 中的事件中心的客户端。 有关遥测数据的详细信息，请参阅[向事件中心发送遥测](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)数据。

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
遥测 JSON 中的所有密钥名称都应为小写。 例如，deviceid 和 sensordata。

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

## <a name="troubleshooting-and-error-management"></a>故障排除和错误管理

**疑难解答选项或支持**

如果无法在指定的 FarmBeats 实例中接收设备数据或遥测数据，设备伙伴应提供支持和故障排除机制。

**遥测数据保留**

还应在预定义的时间段内保留遥测数据，以便在发生错误或数据丢失时，调试或重新发送遥测数据会很有用。

**错误管理或错误通知**

如果错误影响设备或传感器元数据或设备伙伴系统中的数据集成或遥测数据流，你应收到通知。 还应设计和实现一种解决任何错误的机制。

**连接清单**

设备制造商或合作伙伴可以使用以下清单来确保客户提供的凭据准确无误：

   - 查看是否收到具有提供的凭据的访问令牌。
   - 使用收到的访问令牌查看 API 调用是否成功。
   - 检查是否已建立 EventHub 客户端连接。

## <a name="next-steps"></a>后续步骤

有关 REST API 的详细信息，请参阅[REST API](references-for-farmbeats.md#rest-api)。
