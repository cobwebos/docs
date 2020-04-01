---
title: 传感器合作伙伴集成
description: 本文介绍了传感器合作伙伴集成。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 3431576acbb01a0cc3a5f372460b28be05bf7ce7
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437465"
---
# <a name="sensor-partner-integration"></a>传感器合作伙伴集成

本文提供有关 Azure FarmBeats**转换器**组件的信息，该组件支持传感器合作伙伴集成。

使用此组件，合作伙伴可以使用 FarmBeats Datahub API 与服务器场Beats集成，并将客户设备数据和遥测发送到 FarmBeats Datahub。 一旦数据在 FarmBeats 中可用，它就会使用 FarmBeats 加速器进行可视化，并可用于数据融合和构建机器学习/人工智能模型。

## <a name="before-you-start"></a>准备工作

要开发转换器组件，您需要以下凭据来启用对 FarmBeats API 的访问。

- API 终结点
- 租户 ID
- 客户端 ID
- 客户端机密
- 事件中心连接字符串

有关获取上述凭据，请参阅此部分：[启用设备集成](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)

## <a name="translator-development"></a>翻译开发

**基于 REST API 的集成**

FarmBeats 的传感器数据集成功能通过 REST API 公开。 功能包括元数据定义、设备和传感器配置以及设备和传感器管理。

**遥测引入**

遥测数据映射到在 Azure 事件中心上发布的用于处理的规范消息。 Azure 事件中心是一种服务，它支持从连接的设备和应用程序引入实时数据（遥测）。

**API 开发**

API 包含斯瓦格技术文档。 有关 API 及其相应请求或响应的详细信息，请参阅[Swagger](https://aka.ms/FarmBeatsSwagger)。

**身份验证**

服务器场节拍使用 Microsoft Azure 活动目录身份验证。Azure 应用服务提供内置身份验证和授权支持。

有关详细信息，请参阅[Azure 活动目录](https://docs.microsoft.com/azure/app-service/overview-authentication-authorization)。

FarmBeats Datahub 使用无记名身份验证，这需要以下凭据：
   - 客户端 ID
   - 客户端机密
   - 租户 ID

使用这些凭据，调用方可以请求访问令牌。 令牌需要在后续 API 请求中（标头部分）中发送，如下所示：

```
headers = {"Authorization": "Bearer " + access_token, …} 
```

以下示例 Python 代码提供了访问令牌，可用于对 FarmBeats 的后续 API 调用。

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

以下是对 FarmBeats Datahub 进行 API 调用时需要指定的最常见请求标头。


**头** | **说明和示例**
--- | ---
Content-Type | 请求格式（内容类型：应用程序/<format>）。 对于服务器场节拍 Datahub API，格式为 JSON。 Content-Type: application/json
授权 | 指定进行 API 调用所需的访问令牌。 授权：承载<访问令牌>
Accept | 响应格式。 对于服务器场节拍 Datahub API，格式为 JSON。 接受：应用程序/json

**API 请求**

要发出 REST API 请求，请将 HTTP（GET、POST 或 PUT）方法、API 服务的 URL、统一资源标识符 （URI） 与用于查询、将数据提交到、更新或删除以及一个或多个 HTTP 请求标头的资源组合在一起。 API 服务的 URL 是您提供的 API 终结点。 下面是一个示例：https://\<您的数据库网站名称>.azure 网站.net

或者，您可以在 GET 调用上包括查询参数，以筛选 、限制 的大小和在响应中对数据进行排序。

以下示例请求是获取设备列表。

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>"
```
大多数 GET、POST 和 PUT 呼叫都需要 JSON 请求正文。

以下示例请求是创建设备。 （此示例具有带有请求正文的输入 JSON。

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

## <a name="data-format"></a>数据格式

JSON 是一种与语言无关的通用数据格式，它提供了任意数据结构的简单文本表示形式。 有关详细信息，请参阅[json.org](http://json.org)。

## <a name="metadata-specifications"></a>元数据规范

FarmBeats Datahub 具有以下 API，使设备合作伙伴能够创建和管理设备或传感器元数据。

- /**设备模型**：设备模型对应于设备的元数据，例如制造商和设备类型，即网关或节点。
- /**设备**：设备对应于服务器场上存在的物理设备。
- /**传感器模型**：传感器模型对应于传感器的元数据，如制造商、模拟或数字传感器的类型以及传感器测量值（如环境温度和压力）。
- /**传感器**：传感器对应于记录值的物理传感器。 传感器通常连接到具有设备 ID 的设备。

  **设备模型** |  |
  --- | ---
  类型（节点、网关）  | 设备类型 - 节点或网关 |
  制造商  | 制造商名称 |
  产品代码  | 设备产品代码或型号名称或编号。 例如，环境监视器#6800。 |
  端口  | 端口名称和类型，即数字或模拟。  |
  名称  | 名称以标识资源。 例如，型号名称或产品名称。 |
  说明  | 提供模型的有意义的描述。 |
  属性  | 制造商的其他属性。 |
  **设备** |  |
  设备模型 Id  |关联设备型号的 ID。 |
  HardwareId   |设备的唯一 ID，如 MAC 地址。  |
  ReportingInterval |报告间隔（以秒为单位）。 |
  位置    |设备纬度（-90 至 +90）、经度（-180 至 180）和高程（以米为单位）。 |
  父设备 Id | 此设备连接到的父设备的 ID。 例如，如果节点连接到网关，则该节点具有父 DeviceID 作为网关。 |
  名称  | 名称以标识资源。 设备合作伙伴需要发送与设备合作伙伴端的设备名称一致的名称。 如果设备名称在设备合作伙伴端是用户定义的，则同一用户定义的名称应传播到 FarmBeats。  |
  说明  | 提供有意义的说明。  |
  属性  |制造商的其他属性。  |
  **传感器模型** |  |
  类型（模拟、数字）  |提及模拟或数字传感器。|
  制造商  | 制造商名称。 |
  产品代码  | 产品代码或型号名称或编号。 例如，RS-CO2-N01。  |
  传感器测量>名称  | 传感器测量值的名称。 仅支持小写。 对于不同深度的测量，请指定深度。 例如，soil_moisture_15cm。 此名称必须与遥测数据一致。 |
  传感器测量>数据类型  | 遥测数据类型。 目前，支持双精度值。 |
  传感器测量>类型  | 传感器遥测数据的测量类型。 以下是系统定义的类型：环境温度、CO2、深度、电导性、叶湿性、长度、液位、硝酸盐、O2、PH、磷酸盐、点InTime、钾、压力、雨量计、相对湿度、盐度、土壤水分、土壤温度、太阳辐射、状态、时间持续时间、紫外线辐射、UVIndex、体积、风向、风润、风速、蒸发量、PAR。 要添加更多，请参阅 /扩展类型 API。
  传感器测量>单元 | 传感器遥测数据单位。 以下是系统定义的单位：无单位、摄氏度、华氏、开尔文、兰金、帕斯卡、水星、PSI、米计、米、英寸、英尺、英里、千米、英里珀小时、英里珀小时、英里珀秒、KMPer秒、米佩尔小时、米佩尔秒、度、 瓦克珀广场、千瓦PerSquareMeter、毫瓦佩尔广场中心、米珠尔佩尔方中心米、体积水含量、百分比、零件PerM、MicroMol、MicroMolePerLiter、西门子PerperPerPerPerPerPerm、MilliSiemensPerpercentiMeter、百分之尺、德米西门子PerMeter、KiloPascal、体积、升、毫升、秒、无ix时间、微摩尔、微摩尔、百万元、微摩尔、米本、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、千米、百计、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百万元、百分之尺、百万元、百分之一、百万元数、百分之尺、百万元数、百万元、百分之一、百万元、百分之尺、百万元数、百分之尺、百分之 要添加更多，请参阅 /扩展类型 API。
  传感器测量>聚合类型  | 无、平均值、最大值、最小值或标准偏差。
  传感器测量>深度  | 传感器的深度（以厘米为单位）。 例如，测量地下10厘米的水分。
  传感器测量>说明  | 提供测量的有意义的描述。
  名称  | 名称以标识资源。 例如，型号名称或产品名称。
  说明  | 提供模型的有意义的描述。
  属性  | 制造商的其他属性。
  **传感器**  |  |
  HardwareId  | 制造商设置的传感器的唯一 ID。
  传感器模型Id  | 关联传感器型号的 ID。
  位置  | 传感器纬度（-90 至 +90）、经度（-180 至 180）和高程（以米为单位）。
  端口>名称  |传感器在设备上连接到的端口的名称和类型。 这必须与设备模型中定义的名称相同。
  DeviceId  | 传感器连接到的设备 ID。
  名称  | 名称以标识资源。 例如，传感器名称或产品名称、型号或产品代码。
  说明  | 提供有意义的说明。
  属性  | 制造商的其他属性。

 有关每个对象及其属性的信息，请参阅[斯瓦格](https://aka.ms/FarmBeatsDatahubSwagger)。

 > [!NOTE]
 > API 返回所创建的每个实例的唯一 ID。 此 ID 需要由转换器保留，以便进行设备管理和元数据同步。


**元数据同步**

翻译人员应发送元数据的更新。 例如，更新方案是更改设备或传感器名称以及更改设备或传感器位置。

转换器应该能够添加新设备或传感器，这些设备或传感器由用户在附加后链接后安装。 同样，如果用户更新了设备或传感器，则应在 FarmBeats 中更新相应的设备或传感器。 需要更新设备或传感器的典型方案是设备位置的更改或在节点中添加传感器。


> [!NOTE]
> 设备或传感器元数据不支持删除。
>
> 要更新元数据，必须在设备或传感器上调用 /Get/{id}，更新更改的属性，然后执行 /Put/{id}，以便用户设置的任何属性不会丢失。

### <a name="add-new-types-and-units"></a>添加新类型和单位

FarmBeats 支持添加新的传感器测量类型和单位。 有关 /扩展类型 API 的详细信息，请参阅[斯瓦格](https://aka.ms/FarmBeatsSwagger)。

## <a name="telemetry-specifications"></a>遥测规范

遥测数据映射到在 Azure 事件中心上发布的用于处理的规范消息。 Azure 事件中心是一种服务，它支持从连接的设备和应用程序引入实时数据（遥测）。

## <a name="send-telemetry-data-to-farmbeats"></a>向服务器场发送遥测数据

要向 FarmBeats 发送遥测数据，请创建一个客户端，该客户端将消息发送到 FarmBeats 中的事件中心。 有关遥测数据的详细信息，请参阅[向事件中心发送遥测](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)数据。

下面是一个示例 Python 代码，该代码将遥测作为客户端发送到指定的事件中心。

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

规范消息格式如下：

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
遥测 JSON 中的所有键名称都应小写。 示例包括设备 ID 和传感器数据。

例如，下面是一条遥测消息：


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
> 以下各节与其他更改相关（例如。 UI、错误管理等）传感器合作伙伴在开发转换器组件时可以参考的。


## <a name="link-a-farmbeats-account"></a>链接服务器场节拍帐户

客户购买和部署设备或传感器后，他们可以在设备合作伙伴的软件作为服务 （SaaS） 门户上访问设备数据和遥测数据。 设备合作伙伴通过提供输入以下凭据的方法，使客户能够将其帐户链接到 Azure 上的服务器场Beats实例：

   - 显示名称（用户为此集成定义名称的可选字段）
   - API 终结点
   - 租户 ID
   - 客户端 ID
   - 客户端机密
   - 事件Hub连接字符串
   - 开始日期

   > [!NOTE]
   > 开始日期启用历史数据馈送，即用户指定日期的数据。

## <a name="unlink-farmbeats"></a>取消链接服务器场节拍

设备合作伙伴可以使客户能够取消链接现有的服务器场节拍集成。 取消链接服务器场Beats 不应删除在 FarmBeats Datahub 中创建的任何设备或传感器元数据。 取消链接执行以下操作：

   - 停止遥测流。
   - 删除并擦除设备合作伙伴上的集成凭据。

## <a name="edit-farmbeats-integration"></a>编辑服务器场节拍集成

如果客户端密钥或连接字符串发生更改，设备合作伙伴可以帮助客户编辑 FarmBeats 集成设置。 在这种情况下，只有以下字段是可编辑的：

   - 显示名称（如适用）
   - 客户端机密（应以"2x8+"格式或显示/隐藏功能显示，而不是明文显示）
   - 连接字符串（应以"2x8+"格式或显示/隐藏功能显示，而不是明文显示）

## <a name="view-the-last-telemetry-sent"></a>查看发送的最后一个遥测数据

设备合作伙伴可以使客户查看上次发送遥测的时间戳，该时间戳位于 **"遥测发送"** 下。 此时，最新的遥测数据已成功发送到 FarmBeats。

## <a name="troubleshooting-and-error-management"></a>故障排除和错误管理

**排除选项或支持**

如果客户无法在指定的 FarmBeats 实例中接收设备数据或遥测数据，则设备合作伙伴应提供支持和故障排除机制。

**遥测数据保留**

遥测数据也应保留预定义的时间段，以便在发生错误或数据丢失时在调试或重新发送遥测时非常有用。

**错误管理或错误通知**

如果错误影响设备或传感器元数据或设备合作伙伴系统中的数据集成或遥测数据流，客户应收到通知。 还应设计和实施解决任何错误的机制。

**连接清单**

设备制造商或合作伙伴可以使用以下检查表确保客户提供的凭据准确无误：

   - 检查是否使用提供的凭据接收访问令牌。
   - 检查 API 调用是否成功与接收的访问令牌一起。
   - 检查事件中心客户端连接是否已建立。

## <a name="next-steps"></a>后续步骤

有关 REST API 的详细信息，请参阅[REST API](rest-api-in-azure-farmbeats.md)。
