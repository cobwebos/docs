---
title: 从 Azure IoT Central (旧) 导出数据 |Microsoft Docs
description: 如何将数据从 Azure IoT Central 应用程序导出到 Azure 事件中心、Azure 服务总线和 Azure Blob 存储
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: 1202e46f2ea12db62062ac50b8e83b51fe9e5ca0
ms.sourcegitcommit: ac5cbef0706d9910a76e4c0841fdac3ef8ed2e82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2020
ms.locfileid: "89428114"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>使用数据导出 (旧) 将 IoT 数据导出到云目标

> [!Note]
> 本文介绍 IoT Central 中的旧数据导出功能。
>
> - 有关新的预览版数据导出功能的信息，请参阅 [使用数据导出将 IoT 数据导出到云目标 (预览) ](./howto-export-data.md)。
> - 若要了解预览版数据导出功能和旧数据导出功能之间的差异，请参阅 [比较表](./howto-export-data.md#comparison-of-legacy-data-export-and-preview-data-export)。

本文介绍如何使用 Azure 中的数据导出功能 IoT Central。 此功能可让你将数据连续导出到 **Azure 事件中心**、 **azure 服务总线**或 **azure Blob 存储** 实例。 数据导出使用 JSON 格式，可以包括遥测、设备信息和设备模板信息。 使用导出的数据可以获取：

- 热路径见解和分析。 此选项包括在 Azure 流分析中触发自定义规则、在 Azure 逻辑应用中触发自定义工作流，或者通过 Azure Functions 传递数据以进行转换。
- 冷路径分析，例如 Azure 机器学习中的训练模型或 Microsoft Power BI 中的长期趋势分析。

> [!Note]
> 当你打开数据导出时，你只会获得那一刻的数据。 当前，数据导出关闭时无法检索数据。 若要保留更多的历史数据，请及早打开数据导出。

## <a name="prerequisites"></a>先决条件

您必须是 IoT Central 应用程序中的管理员或具有数据导出权限。

## <a name="set-up-export-destination"></a>设置导出目标

导出目标必须存在，然后才能配置数据导出。

### <a name="create-event-hubs-namespace"></a>创建事件中心命名空间

如果当前没有可导出到的事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

2. 选择订阅。 你可以将数据导出到与你的 IoT Central 应用程序不在同一订阅中的其他订阅。 在这种情况下，需使用连接字符串进行连接。

3. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。****

### <a name="create-service-bus-namespace"></a>创建服务总线命名空间

如果当前没有可导出到的服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的服务总线命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。
2. 选择订阅。 你可以将数据导出到与你的 IoT Central 应用程序不在同一订阅中的其他订阅。 在这种情况下，需使用连接字符串进行连接。

3. 若要创建要导出到的队列或主题，请使用服务总线命名空间，然后选择 " **+ 队列** " 或 " **+ 主题**"。

选择服务总线作为导出目标时，队列和主题不得启用“会话”或“重复检测”。 如果启用了其中任一选项，则某些消息不会到达队列或主题中。

### <a name="create-storage-account"></a>创建存储帐户

如果没有要导出到的现有 Azure 存储帐户，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的 [Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount) 或 [Azure Data Lake Storage v2 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。 数据导出只能将数据写入支持块 blob 的存储帐户。 以下列表显示了已知的兼容存储帐户类型：

    |性能层|帐户类型|
    |-|-|
    |标准|常规用途 V2|
    |标准|常规用途 V1|
    |标准|Blob 存储|
    |高级|块 Blob 存储|

2. 在存储帐户中创建容器。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”********。 选择顶部的“+ 容器”以创建新容器。****

## <a name="set-up-data-export"></a>设置数据导出

现在，你已有将数据导出到的目标，请按照以下步骤设置数据导出。

1. 登录到 IoT Central 应用程序。

2. 在左窗格中，选择 " **数据导出**"。

    > [!Tip]
    > 如果左窗格中未显示 " **数据导出** "，则没有权限在应用中配置数据导出。 请与管理员联系以设置数据导出。

3. 选择“+ 新建”按钮。**** 选择一个 **Azure Blob 存储**、 **azure 事件中心**、 **Azure 服务总线队列**或 **azure 服务总线主题** 作为导出目标。 每个应用程序的最大导出数目是 5。

4. 输入导出的名称。 在下拉列表框中，选择 **命名空间**，或 **输入连接字符串**。

    - 您只能在与 IoT Central 应用程序相同的订阅中查看存储帐户、事件中心命名空间和服务总线命名空间。 如果要导出到此订阅之外的目标，请选择 " **输入连接字符串** " 并查看步骤6。
    - 对于使用免费定价计划创建的应用，配置数据导出的唯一方法是使用连接字符串。 免费定价计划中的应用没有关联的 Azure 订阅。

    ![创建新的事件中心](media/howto-export-data-legacy/export-event-hub.png)

5. 从下拉列表框中选择一个事件中心、队列、主题或容器。

6. （可选）如果选中了“输入连接字符串”，则会出现一个用于粘贴连接字符串的新框。**** 若要获取连接字符串，请执行以下操作：

    - 事件中心或服务总线，请中转到 Azure 门户中的命名空间：
        - 若要对整个命名空间使用连接字符串：
            1. 在 "**设置**" 下，选择 "**共享访问策略**"
            2. 创建新的密钥或选择具有 " **发送** " 权限的现有密钥。
            3. 复制主连接字符串或辅助连接字符串
        - 若要将连接字符串用于特定的事件中心实例或服务总线队列或主题，请参阅 **实体 > 事件中心** 或 **实体 > 队列** 或 **实体 > 主题**。 选择特定的实例，然后执行上述相同步骤以获取连接字符串。
    - 存储帐户，请在 Azure 门户中找到存储帐户：
        - 只支持整个存储帐户的连接字符串。 不支持作用域为单个容器的连接字符串。
          1. 在 "**设置**" 下，选择 "**访问密钥**"
          2. 复制密钥 1 连接字符串或密钥 2 连接字符串

    粘贴到连接字符串中。 键入实例或区分大小写的 **容器名称**。

7. 在“要导出的数据”下，通过将相应类型设置为“打开”来指定要导出的数据类型。********

8. 若要启用数据导出，请确保启用的切换为**打开****状态**。 选择“保存”。

9. 几分钟后，数据便会出现在所选目标中。

## <a name="export-contents-and-format"></a>导出内容和格式

导出的遥测数据包含设备发送到 IoT Central 的整个消息，而不仅仅是遥测值本身。 导出的设备数据包含对所有设备的属性和元数据的更改，而导出的设备模板则包含对所有设备模板的更改。

对于事件中心和服务总线，数据将以近实时的速度导出。 数据在 `body` 属性中，采用 JSON 格式。 有关示例，请参阅下面的示例。

对于 Blob 存储，将每分钟导出一次数据，其中每个文件包含自上次导出的文件以来发生的一批更改。 导出的数据以 JSON 格式放置在三个文件夹中。 存储帐户中的默认路径是：

- 遥测数据：_{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 设备：_{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 设备模板：_{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

若要浏览 Azure 门户中的已导出文件，请导航到该文件，然后选择 " **编辑 blob** " 选项卡。

## <a name="telemetry"></a>遥测

对于事件中心和服务总线，IoT Central 在接收到来自设备的消息之后迅速导出一条新消息。 每个导出的消息都包含在正文属性中以 JSON 格式发送的完整消息。

对于 Blob 存储，将每分钟分批和导出一次消息。 导出的文件使用的格式与 [IoT 中心消息路由](../../iot-hub/tutorial-routing.md) 到 blob 存储导出的消息文件相同。

> [!NOTE]
> 对于 Blob 存储，请确保你的设备正在发送具有 `contentType: application/JSON` 和 `contentEncoding:utf-8` (或 `utf-16`) `utf-32` 的消息。 有关示例，请参阅 [IoT 中心文档](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

发送遥测数据的设备由设备 ID 表示（请参阅以下部分）。 若要获取设备的名称，请导出设备数据并使用与设备消息的 **deviceId** 匹配的 **connectionDeviceId** 来关联每条消息。

以下示例显示了从事件中心或服务总线队列或主题接收的消息：

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

此消息不包括发送设备的设备 ID。

若要从 Azure 流分析查询中的消息数据检索设备 ID，请使用 [GetMetadataPropertyValue](https://docs.microsoft.com/stream-analytics-query/getmetadatapropertyvalue) 函数。 有关示例，请参阅 [使用流分析扩展 Azure IoT Central 使用自定义规则扩展 Azure、Azure Functions 和 SendGrid](./howto-create-custom-rules.md)。

若要在 Azure Databricks 或 Apache Spark 工作区中检索设备 ID，请使用 [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md)。 有关示例，请参阅 [使用 Azure Databricks 通过自定义分析扩展 Azure IoT Central](./howto-create-custom-analytics.md)中的 Databricks 工作区。

下面的示例显示了已导出到 blob 存储的记录：

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>设备

快照中的每条消息或记录表示自上次导出的消息以来对设备及其设备和云属性进行的一项或多项更改。 消息包括：

- IoT Central 中设备的 `id`
- 设备的 `displayName`
- 设备模板 ID `instanceOf`
- `simulated` 标志，如果设备为模拟设备，则为 true
- `provisioned` 标志，如果已设置设备，则为 true
- `approved` 标志，如果已批准设备发送数据，则为 true
- 属性值
- `properties` 包括设备和云属性值

删除的设备不会导出。 目前，在导出的消息中没有针对已删除设备的指示器。

对于事件中心和服务总线，IoT Central 会以近乎实时的时间将包含设备数据的消息发送到事件中心、服务总线队列或主题。

对于 Blob 存储，包含自上次写入后的所有更改的新快照每分钟导出一次。

以下示例消息显示了事件中心或服务总线队列或主题中的设备和属性数据的相关信息：

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

此快照是显示 Blob 存储中的设备和属性数据的示例消息。 导出的文件为每条记录包含一行。

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>设备模板

每条消息或快照记录表示自上次导出的消息以来对已发布设备模板进行的一项或多项更改。 在每条消息或记录中发送的信息包括：

- `id` 与 `instanceOf` 上面的设备流匹配的设备模板
- 设备模板的 `displayName`
- 设备的 `capabilityModel`，包括其 `interfaces`、遥测数据、属性和命令定义
- `cloudProperties` 定义
- 重写和初始值，与 `capabilityModel` 内联在一起

删除的设备模板不会导出。 目前，在导出的消息中没有针对已删除设备模板的指示器。

对于事件中心和服务总线，IoT Central 会以近乎实时的时间将包含设备模板数据的消息发送到事件中心、服务总线队列或主题。

对于 Blob 存储，包含自上次写入后的所有更改的新快照每分钟导出一次。

此示例显示有关事件中心或服务总线队列或主题中的设备模板数据的消息：

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

此示例快照显示包含 Blob 存储中的设备和属性数据的消息。 导出的文件为每条记录包含一行。

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "commandType": "synchronous",
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>数据格式更改通知

> [!Note]
> 遥测流数据格式不受此更改的影响。 仅设备和设备模板数据流会受到影响。

如果在已启用 " *设备* 和 *设备模板* " 流的预览应用程序中有现有的数据导出，请将导出更新为 **30 年6月 30 2020 日**。 此要求适用于导出到 Azure Blob 存储、Azure 事件中心和 Azure 服务总线。

自2020年2月3日起，启用了设备和设备模板的应用程序中的所有新导出都将具有上面所述的数据格式。 在此日期之前创建的所有导出都将保留在2020年6月30日之前的旧数据格式，此时，这些导出会自动迁移到新的数据格式。 新数据格式与 IoT Central 公共 API 中的 [设备](https://docs.microsoft.com/rest/api/iotcentral/devices/get)、 [设备属性](https://docs.microsoft.com/rest/api/iotcentral/devices/getproperties)、 [设备云属性](https://docs.microsoft.com/rest/api/iotcentral/devices/getcloudproperties)和 [设备模板](https://docs.microsoft.com/rest/api/iotcentral/devicetemplates/get) 对象匹配。

对于 **设备**，旧数据格式和新数据格式之间的显著差异包括：
- `@id` 对于删除的设备， `deviceId` 将重命名为 `id` 
- `provisioned` 添加了标记以描述设备的设置状态
- `approved` 添加了标记以描述设备的审批状态
- `properties` 包括设备和云属性，与公共 API 中的实体匹配

对于 **设备模板**，旧数据格式和新数据格式之间的显著差异包括：

- `@id` 对于设备模板已重命名为 `id`
- `@type` 对于设备模板，将重命名为 `types` ，现在为数组

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>从2020年2月3日 (格式弃用的设备) 

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>从2020年2月3日 (格式弃用的设备模板) 

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>后续步骤

了解如何将数据导出到 Azure 事件中心、Azure 服务总线和 Azure Blob 存储后，请继续执行下一步：

> [!div class="nextstepaction"]
> [如何通过 Databricks 运行自定义分析](./howto-create-custom-analytics.md)
