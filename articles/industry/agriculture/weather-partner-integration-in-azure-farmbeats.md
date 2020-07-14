---
title: 天气合作伙伴集成
description: 本文介绍天气数据提供程序如何与 FarmBeats 集成
author: sunasing
ms.topic: article
ms.date: 07/09/2020
ms.author: sunasing
ms.openlocfilehash: d4ceb25b9b81c831ed1b285a875742ebfaa6d24f
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232638"
---
# <a name="weather-partner-integration"></a>天气合作伙伴集成

本文提供了有关以下内容的信息： FarmBeats**连接器**docker 组件：天气数据提供商可通过利用其 Api 与 FarmBeats 进行集成，并将天气数据发送到 FarmBeats。 数据在 FarmBeats 中可用后，可用于数据合成，并用于构建机器学习/人工智能模型。

 > [!NOTE]
 > 出于本文档的目的，我们将使用使用 NOAA 从 Azure 开放式数据集生成的参考实现，并且可从获取 [https://github.com/azurefarmbeats/noaa_docker](https://github.com/azurefarmbeats/noaa_docker) 。
 > 相应的 docker 映像位于[https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa](https://hub.docker.com/r/azurefarmbeats/farmbeats-noaa)

天气合作伙伴需要提供 docker 映像/程序 (，其中包含下面所述的规格) 并在可由客户访问的容器注册表中托管 docker 映像。 天气合作伙伴需要为其客户提供以下信息：

- Docker 图像 URL
- Docker 映像标记
- 用于访问 docker 映像的密钥/凭据
- 用于从天气合作伙伴的系统访问数据的客户特定的 API 密钥/凭据
- VM SKU 详细信息 (合作伙伴可提供此项，以防其 docker 具有特定的 VM 要求，否则客户可以从 Azure 中受支持的 VM Sku 中选择) 

使用上述 docker 信息，客户将在其 FarmBeats 实例中注册天气合作伙伴。 若要了解有关客户如何使用 docker 在 FarmBeats 中引入天气数据的详细信息，请参阅[获取天气数据](https://docs.microsoft.com/azure/industry/agriculture/get-weather-data-from-weather-partner)指南

## <a name="connector-docker-development"></a>连接器 docker 开发

**基于 REST API 的集成**

FarmBeats Api 包含 Swagger 技术文档。 有关所有 Api 及其相应的请求或响应的信息，请参阅[FarmBeats Swagger](https://aka.ms/farmbeatsswagger)。 

如果已安装 FarmBeats，则可以访问 FarmBeats swagger，网址为[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net/swagger)

请注意，"-api" 将追加到你的 FarmBeats 网站名称。
API 终结点将为：[https://yourfarmbeatswebsitename-api.azurewebsites.net](https://yourfarmbeatswebsitename-api.azurewebsites.net)

### <a name="datahub-lib"></a>Datahub lib

FarmBeats 将提供可供天气合作伙伴使用的 lib。 此 lib 当前在[此处](https://github.com/azurefarmbeats/noaa_docker/tree/master/datahub_lib)引用实现中提供。 将来，这一功能可用作多种语言的 SDK。

### <a name="authentication"></a>身份验证

**通过 FarmBeats Api 进行身份验证**

FarmBeats 使用持有者身份验证，并且可以通过在请求的标头部分提供访问令牌来访问 Api，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

可以从客户的 FarmBeats 实例上运行的 Azure 函数请求访问令牌。 将 Azure Function URL 作为参数提供给 docker 程序，并通过对 URL 发出 GET 请求来获取访问令牌。 URL 中的响应将包含访问令牌。 Datahub lib 提供 helper 函数，使合作伙伴可以获取访问令牌。 [此处](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_auth_helper.py)提供了更多详细信息。

访问令牌仅在几个小时内有效，在过期后需要重新请求。

**通过合作伙伴端 Api 进行身份验证**

为了使客户能够在 docker 执行期间向合作伙伴端 Api 进行身份验证，客户需要在进行合作伙伴注册期间提供凭据，如下所示：

```json
{
 "partnerCredentials": {
   "key1": "value1",
   "key2": "value2"
   }
}
```
API 服务将序列化此 dict，并将其存储在[KeyVault](https://docs.microsoft.com/azure/key-vault/basic-concepts)中。

[Azure 数据工厂](https://docs.microsoft.com/azure/data-factory/introduction)用于协调天气作业并加速资源以执行 docker 代码。 它还提供了一种机制，用于将数据安全地推送到执行 docker 作业的 VM。 现已安全存储在 KeyVault 中的 API 凭据作为安全字符串从 KeyVault 读取，并在 docker 容器的工作目录中作为扩展属性提供，因为文件的 (路径上的 activity.js是 "/mnt/working_dir/activity.js" ) docker 代码可在运行时从该文件读取凭据，以代表客户访问合作伙伴端 Api。 这些凭据将在文件中提供，如下所示：

```json
{ 
   "typeProperties" : {
      "extendedProperties" : { 
         "partnerCredentials": "" } 
   } 
}
```
请注意，"partnerCredentials" 将以客户在合作伙伴注册期间提供的准确方式提供

FarmBeats lib 提供帮助程序函数，使合作伙伴能够从活动属性中读取凭据。 [此处](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/auth/partner_adf_helper.py)提供了更多详细信息。

文件的生存期仅在 docker 代码执行过程中执行，并将在 docker 运行结束后被删除。

有关 ADF 管道和活动的工作原理的更多详细信息，请参阅 [https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping](https://docs.microsoft.com/azure/data-factory/copy-activity-schema-and-type-mapping) 。

**HTTP 请求标头**

下面是在对 FarmBeats 进行 API 调用时需要指定的最常见请求标头。

**标头** | **说明和示例**
--- | ---
Content-Type | 请求格式 (Content-Type: application/<format>)。 对于 FarmBeats 数据中心 API，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 Authorization:持有者 <Access-Token>
Accept | 响应格式。 对于 FarmBeats 数据中心 API，格式为 JSON。 Accept: application/json

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的常见数据格式，该格式提供任意数据结构的简单文本表示形式。 有关详细信息，请参阅 [json.org](http://json.org)。

## <a name="docker-specifications"></a>Docker 规范

Docker 程序需要有两个组件：**启动**和**作业**。 可以有多个作业。

### <a name="bootstrap"></a>自举

当客户在 FarmBeats 上启动 docker 注册时，应执行此组件。 将传递给此程序的参数 (arg1、arg2) ：

- FarmBeats API 终结点： API 请求的 FarmBeats API 终结点：这是对 FarmBeats 部署进行 API 调用的终结点。
- Azure Function URL：这是你自己的个人终结点，它将为你提供 FarmBeats Api 的访问令牌。 只需对此 url 调用 GET，就会在其响应中获取访问令牌。

启动的责任是创建所需的元数据，以便用户可以运行你的作业来获取天气数据。 请参阅[此处](https://github.com/azurefarmbeats/noaa_docker)的参考实现。 您可以根据需要更新文件上的 bootstrap_manifest.js，引用启动程序将为您创建所需的元数据。

在此过程中，将创建以下元数据。 

 > [!NOTE]
 > **请注意**，如果你按[引用实现](https://github.com/azurefarmbeats/noaa_docker)中所述更新文件上的 bootstrap_manifest.js，则无需创建以下元数据，因为启动将基于清单文件创建相同的元数据。

- /**WeatherDataModel**： WeatherDataModel 是一种用于表示天气数据并对应于源提供的不同数据集的模型。 例如，DailyForecastSimpleModel 可以每天提供一次平均温度、湿度和降水量信息，而 DailyForecastAdvancedModel 可能会提供更多的信息。 可以创建任意数量的 WeatherDataModels。
- /**JobType**： FarmBeats 具有可扩展的作业管理系统。 作为天气预报，你将具有不同的数据集/Api (例如 GetDailyForecasts) ，你可以在 FarmBeats 中将其启用为 JobType。 创建 JobType 后，客户可以触发该类型的作业，以获取其所在地/场的天气数据 (参阅[FarmBeats Swagger](https://aka.ms/farmbeatsswagger)) 中的 JobType 和作业 api。

### <a name="jobs"></a>作业

每次 FarmBeats 用户运行你在启动过程中创建的/JobType 作业时，都会调用此组件。 作业的 docker run 命令定义为你创建的 **/JobType**的一部分。
- 作业的责任是从源提取数据并将其推送到 FarmBeats。 获取数据所需的参数应在启动过程中定义为/JobType 的一部分。
- 作为作业的一部分，程序必须根据在启动过程中创建的/WeatherDataModel 创建 **/WeatherDataLocation** 。 **/WeatherDataLocation**对应于一个位置 (lat/long) ，用户作为该作业的参数提供该位置。

### <a name="details-of-the-objects"></a>对象的详细信息

  **WeatherDataModel** |  |
  --- | ---
  名称  | 天气数据模型的名称 |
  说明  | 提供对模型的有意义说明。 |
  属性  | 由数据提供程序定义的附加属性。 |
  weatherMeasures > 名称  | 天气度量值的名称。 例如 humidity_max |
  weatherMeasures > 数据类型  | Double 或 Enum。 如果为 Enum，则需要 measureEnumDefinition |
  weatherMeasures > measureEnumDefinition  | 仅当 DataType 为 Enum 时才是必需的。 例如： {"NoRain"：0，"雪"：1，"Drizzle"：2，"Rain"： 3} |
  weatherMeasures > 类型  | 天气遥测数据的类型。 例如 "RelativeHumidity"。 以下是系统定义的类型： AmbientTemperature、NoUnit、CO2、Depth、ElectricalConductivity、LeafWetness、Length、LiquidLevel、Nitrate、O2、PH、Phosphate、PointInTime、Potassium、压力、RainGauge、RelativeHumidity、Salinity、SoilMoisture、SoilTemperature、SolarRadiation、State、TimeDuration、UVRadiation、UVIndex、WindDirection、WindRun、WindSpeed、Evapotranspiration、、、、、、、、 若要添加更多，请参阅下面的/ExtendedType API，或在下面的 "[添加类型和单位" 部分](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype)中
  weatherMeasures > 单元 | 天气遥测数据的单位。 以下是系统定义的单位：NoUnit、Celsius、Fahrenheit、Kelvin, Rankine、Pascal、Mercury、PSI, MilliMeter、CentiMeter、Meter、Inch、Feet、Mile、KiloMeter、MilesPerHour、MilesPerSecond、KMPerHour、KMPerSecond、MetersPerHour、MetersPerSecond、Degree、WattsPerSquareMeter、KiloWattsPerSquareMeter、MilliWattsPerSquareCentiMeter、MilliJoulesPerSquareCentiMeter、VolumetricWaterContent、Percentage、PartsPerMillion、MicroMol、MicroMolesPerLiter、SiemensPerSquareMeterPerMole、MilliSiemensPerCentiMeter、Centibar、DeciSiemensPerMeter、KiloPascal、VolumetricIonContent、Liter、MilliLiter、Seconds、UnixTimestamp、MicroMolPerMeterSquaredPerSecond 和 InchesPerHour. 若要添加更多，请参阅下面的 "/ExtendedType API" 或 "[添加类型和单位" 部分](weather-partner-integration-in-azure-farmbeats.md#add-extendedtype)。
  weatherMeasures > AggregationType  | None、Average、Maximum、Average、StandardDeviation、Sum、Total
  weatherMeasures > 深度  | 传感器的深度（以厘米为单位）。 例如，地下 10 厘米的湿度度量。
  weatherMeasures > 说明  | 提供对度量的有意义说明。 |
  **JobType** |  |
  名称  | 作业的名称-例如 Get_Daily_Forecast;客户将运行以获取天气数据的作业|
  pipelineDetails > 参数 > 名称  | 参数的名称 |
  pipelineDetails > 参数 > 类型 | String、Int、Float、Bool、Array |
  pipelineDetails > 参数 > isRequired | 变量如果需要参数，则为 true; 否则为 false。默认值为 true |
  pipelineDetails > 参数 > defaultValue | 参数的默认值 |
  pipelineDetails > 参数 > 说明 | 参数说明 |
  属性  | 制造商提供的其他属性。
  **ProgramRunCommand**属性 > | docker 运行命令-当客户运行天气作业时，将执行此命令。 |
  **WeatherDataLocation** |  |
  weatherDataModelId  | 在启动过程中创建的相应 WeatherDataModel 的 ID|
  location  | 表示纬度、经度和仰角 |
  名称 | 对象的名称 |
  描述 | 描述 |
  farmId | **可选**作为作业参数一部分的 customer 提供的场 ID |
  属性  | 制造商提供的其他属性。

 有关每个对象及其属性的信息，请参阅 [Swagger](https://aka.ms/FarmBeatsSwagger)。

 > [!NOTE]
 > API 为创建的每个实例返回唯一 ID。 用于设备管理和元数据同步的转换器需要保留此 ID。

**元数据同步**

连接器 docker 应该能够在元数据上发送更新。 更新方案的示例包括：添加天气提供商的数据集中的新天气参数，添加功能 (例如）。 添加了30天预测) 

> [!NOTE]
> 元数据（例如）不支持删除。 天气数据模型。
>
> 若要更新元数据，必须在天气数据模型上调用/Get/{ID}，更新更改的属性，然后执行/Put/{ID}，以使用户设置的任何属性都不会丢失。

## <a name="weather-data-telemetry-specifications"></a>天气数据 (遥测) 规范

天气数据映射到一个规范消息，该消息将被推送到 Azure 事件中心进行处理。 Azure EventHub 是一项服务，可用于从连接的设备和应用程序) 引入实时数据 (遥测数据。 若要将天气数据发送到 FarmBeats，需要创建将消息发送到 FarmBeats 中的事件中心的客户端。 若要了解有关发送遥测数据的详细信息，请参阅向[事件中心发送遥测](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)数据

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
   "weatherstations": [
   {
   "id": "id of the WeatherDataLocation",
   "weatherdata": [
   {
     "timestamp": "timestamp of the data. For historical, this is the time for which the observations are sent. For forecast this is the time for which data is forecasted. Format is ISO 8601. Default time-zone is UTC",
     "predictiontimestamp": "timestamp on which the forecast data is predicted i.e time of prediction. Required only for forecast data. Format is ISO 8601. Default timezone is UTC ",
     "weathermeasurename1": <value>,
     "weathermeasurename2": <value>
     }
     ]
    }
   ]
}
```

例如，下面是遥测消息：

```json
{
   "weatherstations": [
   {
     "id": "5e4b34e7-bf9e-4f39-xxxx-f3c06d0366ea",
     "weatherdata": [
     {
      "timestamp": "2019-07-10T00:00:00Z",
      "predictiontimestamp": "2019-07-05T00:00:00Z",
      "PrecipitationTotalLiquidEquivalent": 0,
      "AvgPressure": 0,
      "AvgRelativeHumidity": 72
     }
    ] 
  }
 ]
}

```

## <a name="troubleshooting-and-error-management"></a>故障排除和错误管理

**错误日志记录**

由于伙伴作业将在现有的作业框架中运行，因此，在 FarmBeats (如 GetFarmData、SensorPlacement 等 ) 中，错误与其他预先存在的作业的错误记录方式相同。 ADF 管道内运行的 ADF 活动记录了 STDERR 和 STDOUT。 这两个文件都在 FarmBeats 资源组中的 "datahublogs-xxx" 存储帐户中可用。

Datahub lib 提供帮助程序函数，用于启用日志记录作为总体 Datahub 日志的一部分。 [此处](https://github.com/azurefarmbeats/noaa_docker/blob/master/datahub_lib/framework/logger.py)提供了更多详细信息。

**故障排除选项或支持**

如果客户无法在指定的 FarmBeats 实例中接收天气数据，天气合作伙伴应提供支持和一种解决相同问题的机制。

## <a name="add-extendedtype"></a>添加 ExtendedType

FarmBeats 支持添加新的传感器度量值类型和单位。 请注意，天气合作伙伴可以通过更新[此处](https://github.com/azurefarmbeats/noaa_docker)引用实现中的文件 bootstrap_manifest.js来添加新的单位/类型

若要添加新的 WeatherMeasure 类型，例如 "PrecipitationDepth"，请执行以下步骤。

1. 使用查询筛选器-key = WeatherMeasureType 在/ExtendedType 上发出 GET 请求
2. 记下返回对象的 ID。
3. 在返回的对象中，将新类型添加到列表中，并在/ExtendedType{ID} 上使用以下新列表发出 PUT 请求。 输入负载应与上面收到的响应相同，并且新的单元追加到值列表的末尾。

有关 /ExtendedType API 的详细信息，请参阅 [Swagger](https://aka.ms/FarmBeatsSwagger)。

## <a name="next-steps"></a>后续步骤

现在，你有一个与 FarmBeats 集成的连接器 docker。 接下来，可以了解如何使用 docker 将天气数据导入 FarmBeats。 请参阅[获取天气数据](get-weather-data-from-weather-partner.md)。
