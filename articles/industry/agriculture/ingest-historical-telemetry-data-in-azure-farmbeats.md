---
title: 引入历史遥测数据
description: 本文介绍如何引入历史遥测数据。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: e0a5e89f256b562ce5f702e9ff1388cb4d021bf5
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437691"
---
# <a name="ingest-historical-telemetry-data"></a>引入历史遥测数据

本文介绍如何将历史传感器数据引入 Azure FarmBeats。

从物联网 （IoT） 资源（如设备和传感器）中引入历史数据是 FarmBeats 中的常见方案。 为设备和传感器创建元数据，然后以规范格式将历史数据引入 FarmBeats。

## <a name="before-you-begin"></a>在开始之前

在继续本文之前，请确保您已安装 FarmBeats 并从 IoT 设备收集历史数据。 您还需要启用合作伙伴访问，如以下步骤所述。

## <a name="enable-partner-access"></a>启用合作伙伴访问权限

您需要启用与 Azure FarmBeats 实例的合作伙伴集成。 此步骤将创建一个客户端，该客户端可作为设备合作伙伴访问 Azure FarmBeats 实例，并为您提供后续步骤中所需的以下值：

- API 终结点：这是 Datahub URL，例如，https://\<数据中心>.azure网站.net
- 租户 ID
- 客户端 ID
- 客户端机密
- 事件Hub连接字符串

执行以下步骤:

> [!NOTE]
> 您必须是管理员才能执行以下步骤。

1. 登录 https://portal.azure.com/。

2. **如果您在 FarmBeats 版本 1.2.7 或更高版本中，请跳过步骤 a、b 和 c，然后转到步骤 3。** 您可以通过选择 FarmBeats UI 右上角的 **"设置"** 图标来检查服务器场节拍版本。

      a.  转到**Azure 活动目录** > **应用注册**

      b. 选择作为服务器场节拍部署的一部分创建**的应用注册**。 它将具有与服务器场Beats数据库相同的名称。

      c. 选择 **"公开 API>** 选择 **"添加客户端应用程序**"并输入**04b07795-8ddb-461a-bbee-02f9e1bf7b46**并选中**授权范围**。 这将授予 Azure CLI（云外壳）以执行以下步骤的访问权限：

3. 打开 Cloud Shell。 此选项在 Azure 门户右上角的工具栏上可用。

    ![Azure 门户工具栏](./media/get-drone-imagery-from-drone-partner/navigation-bar-1.png)

4. 确保环境设置为**PowerShell**。 默认情况下，它设置为 Bash。

    ![PowerShell 工具栏设置](./media/get-sensor-data-from-sensor-partner/power-shell-new-1.png)

5. 转到主目录。

    ```azurepowershell-interactive 
    cd  
    ```

6. 运行以下命令。 这将将脚本下载到您的主目录。

    ```azurepowershell-interactive 

    wget –q https://aka.ms/farmbeatspartnerscriptv3 -O ./generatePartnerCredentials.ps1

    ```

7. 运行以下脚本。 该脚本要求提供租户 ID，可以从**Azure 活动目录** > **概述**页获取该 ID。

    ```azurepowershell-interactive 

    ./generatePartnerCredentials.ps1   

    ```

8. 按照屏幕上的说明捕获**API 终结点**、**租户 ID、****客户端 ID、****客户端密钥**和**事件Hub连接字符串**的值。


## <a name="create-device-or-sensor-metadata"></a>创建设备或传感器元数据

 现在，您已经拥有了所需的凭据，您可以定义设备和传感器。 为此，请通过调用 FarmBeats API 创建元数据。 请确保将 API 称为您在上述部分中创建的客户端应用。

 FarmBeats Datahub 具有以下 API，用于创建和管理设备或传感器元数据。

 > [!NOTE]
 > 作为合作伙伴，您只能读取、创建和更新元数据;**删除选项仅限于合作伙伴。**

- /**设备模型**：设备模型对应于设备的元数据，例如制造商和设备类型，即网关或节点。
- /**设备**：设备对应于服务器场上存在的物理设备。
- /**传感器模型**：传感器模型对应于传感器的元数据，如制造商、模拟或数字传感器的类型以及传感器测量（如环境温度和压力）。
- /**传感器**：传感器对应于记录值的物理传感器。 传感器通常连接到具有设备 ID 的设备。  


|        DeviceModel   |  建议   |
| ------- | -------             |
|     类型（节点、网关）        |          设备类型 - 节点或网关      |
|          制造商            |         制造商名称    |
|  产品代码                    |  设备产品代码或型号名称或编号。 例如，环境监视器#6800。  |
|            端口          |     端口名称和类型，即数字或模拟。
|     名称                 |  名称以标识资源。 例如，型号名称或产品名称。
      说明     | 提供模型的有意义的描述。
|    属性          |    制造商的其他属性。   |
|    **设备**             |                      |
|   设备模型 Id     |     关联设备型号的 ID。  |
|  HardwareId          | 设备的唯一 ID，如 MAC 地址。
|  ReportingInterval        |   报告间隔（以秒为单位）。
|  位置            |  设备纬度（-90 至 +90）、经度（-180 至 180）和高程（以米为单位）。   
|父设备 Id       |    此设备连接到的父设备的 ID。 例如，连接到网关的节点。 节点具有父设备 Id 作为网关。  |
|    名称            | 用于标识资源的名称。 设备合作伙伴必须发送与合作伙伴端的设备名称一致的名称。 如果合作伙伴设备名称是用户定义的，则同一用户定义的名称应传播到 FarmBeats。|
|     说明       |      提供有意义的说明。 |
|     属性    |  制造商的其他属性。
|     **传感器模型**        |          |
|       类型（模拟、数字）          |      传感器的类型，无论是模拟的还是数字的。       |
|          制造商            |       传感器的制造商。     |
|     产品代码| 产品代码或型号名称或编号。 例如，RS-CO2-N01。 |
|       传感器测量>名称       | 传感器测量值的名称。 仅支持小写。 对于不同深度的测量，请指定深度。 例如，soil_moisture_15cm。 此名称必须与遥测数据一致。  |
|          传感器测量>数据类型       |遥测数据类型。 目前，支持双精度值。|
|    传感器测量>类型    |传感器遥测数据的测量类型。 系统定义的类型是环境温度、CO2、深度、电导性、叶湿性、长度、液位、硝酸盐、O2、PH、磷酸盐、点InTime、钾、压力、雨量计、相对湿度、盐度、土壤水分、土壤温度、太阳辐射、状态、时间、紫外线辐射、UVIndex、体积、风向、风流、风速、蒸发、PAR。 要添加更多，请参阅 /扩展类型 API。|
|        传感器测量>单元              | 传感器遥测数据单位。 系统定义的单位是 NoUnit、摄氏度、华氏、开尔文、兰金、帕斯卡、水星、PSI、米数、厘米、米、英寸、英尺、英里、千米、英里珀小时、英里珀秒、KMPer秒、米佩尔小时、米佩尔秒、度、瓦特珀平、千瓦佩尔平米、 MilliWattsPerSquareCentiMeter， MilliJulesPerSquareCentiMeter， 体积水含量， 百分比， 零件百万， 微摩尔， 微摩尔PerLiter， 西门子PerSquareMPerPerPerPerMole， MilliSiemensPercentiMeter， Centibar， deciSiemensPerMeter， KiloPascal， 体积元素， 升， 毫升， 秒， unix时间戳， 微摩尔PerPerPerdper，英寸来添加更多的，参考.|
|    传感器测量>聚合类型    |  值可以是无、平均值、最大值、最小值或标准偏差。  |
|          名称            | 名称以标识资源。 例如，型号名称或产品名称。  |
|    说明        | 提供模型的有意义的描述。|
|   属性       |  制造商的其他属性。|
|    **传感器**      |          |
| HardwareId          |   制造商设置的传感器的唯一 ID。|
|  传感器模型Id     |    关联传感器型号的 ID。|
| 位置          |  传感器纬度（-90 至 +90）、经度（-180 至 180）和高程（以米为单位）。|
|   端口>名称        |  传感器在设备上连接到的端口的名称和类型。 这需要与设备模型中定义的名称相同。|
|    DeviceID  |    传感器连接到的设备 ID。 |
| 名称            |   名称以标识资源。 例如，传感器名称或产品名称、型号或产品代码。|
|    说明      | 提供有意义的说明。|
|    属性        |制造商的其他属性。|

有关对象的详细信息，请参阅[斯瓦格](https://aka.ms/FarmBeatsDatahubSwagger)。

### <a name="api-request-to-create-metadata"></a>创建元数据的 API 请求

要发出 API 请求，请将 HTTP （POST） 方法、API 服务的 URL 和 URI 合并到资源以查询、将数据提交到请求、创建或删除请求。 然后添加一个或多个 HTTP 请求标头。 API 服务的 URL 是 API 终结点，即 Datahub URL（https://\<您的数据中心>.azure网站.net）。  

### <a name="authentication"></a>身份验证

FarmBeats Datahub 使用无记名身份验证，这需要上一节中生成的以下凭据：

- 客户端 ID
- 客户端机密
- 租户 ID

使用这些凭据，调用方可以请求访问令牌。 令牌必须在后续 API 请求中（标头部分）中发送，如下所示：

```
headers = *{"Authorization": "Bearer " + access_token, …}*
```

以下示例 Python 代码提供了访问令牌，可用于对 FarmBeats 的后续 API 调用： 

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

以下是对 FarmBeats Datahub 进行 API 调用时必须指定的最常见请求标头：

- **内容类型**： 应用程序 /json
- **授权**： 承载<访问令牌>
- **接受**：应用程序/json

### <a name="input-payload-to-create-metadata"></a>输入有效负载以创建元数据

DeviceModel


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

传感器模型

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

以下示例请求创建设备。 此请求具有将 JSON 作为请求正文的有效负载。

```bash
curl -X POST "https://<datahub>.azurewebsites.net/Device" -H  
"accept: application/json" -H  "Content-Type: application/json" -H
"Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  
\"reportingInterval\": 900,  \"name\": \"Device123\",  
\"description\": \"Test Device 123\"}" *
```

下面是 Python 中的示例代码。 此示例中使用的访问令牌与身份验证期间接收的访问令牌相同。

```python
import requests
import json

# Got access token - Calling the Device Model API

headers = {
    "Authorization": "Bearer " + access_token,
    "Content-Type" : "application/json"
    }
payload = '{"type" : "Node", "productCode" : "TestCode", "ports": [{"name": "port1","type": "Analog"}], "name" : "DummyDevice"}'
response = requests.post(ENDPOINT + "/DeviceModel", data=payload, headers=headers)
```

> [!NOTE]
> API 返回所创建的每个实例的唯一 ID。 您必须保留指示，才能发送相应的遥测消息。

### <a name="send-telemetry"></a>发送遥测

现在，您已经创建了 FarmBeats 中的设备和传感器，您可以发送关联的遥测消息。

### <a name="create-a-telemetry-client"></a>创建一个遥测客户端

您必须将遥测发送到 Azure 事件中心进行处理。 Azure 事件中心是一种服务，它支持从连接的设备和应用程序引入实时数据（遥测）。 要向 FarmBeats 发送遥测数据，请创建一个客户端，该客户端将消息发送到 FarmBeats 中的事件中心。 有关发送遥测的详细信息，请参阅 Azure[事件中心](https://docs.microsoft.com/azure/event-hubs/event-hubs-dotnet-standard-getstarted-send)。

### <a name="send-a-telemetry-message-as-the-client"></a>将遥测消息作为客户端发送

建立作为事件中心客户端的连接后，可以将消息作为 JSON 发送到事件中心。

下面是将遥测作为客户端发送到指定事件中心的示例 Python 代码：

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

将历史传感器数据格式转换为 Azure FarmBeats 所理解的规范格式。 规范消息格式如下：

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

添加相应的设备和传感器后，在遥测消息中获取设备 ID 和传感器 ID，如上一节所述。

下面是遥测消息的示例：


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

## <a name="troubleshooting"></a>疑难解答

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>从传感器引入历史/流数据后无法查看遥测数据

**症状**：设备或传感器已部署，您已创建 FarmBeats 上的设备/传感器，并将遥测数据引入到 EventHub，但无法获取或查看 FarmBeats 上的遥测数据。

**纠正措施**：

1. 确保已完成适当的合作伙伴注册 - 您可以通过访问数据库摇曳器、导航到 /合作伙伴 API、执行获取并检查合作伙伴是否已注册来检查此情况。 如果没有，请按照[此处的步骤](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats)添加合作伙伴。

2. 确保已使用合作伙伴客户端凭据创建了元数据（设备模型、设备、传感器模型、传感器）。

3. 确保使用了正确的遥测消息格式（如下文所述）：

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

## <a name="next-steps"></a>后续步骤

有关基于 REST API 的集成详细信息的详细信息，请参阅[REST API](rest-api-in-azure-farmbeats.md)。
