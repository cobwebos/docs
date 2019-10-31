---
title: 导出 Azure IoT Central 数据 |Microsoft Docs
description: 如何将数据从 Azure IoT Central 应用程序导出到 Azure 事件中心、Azure 服务总线和 Azure Blob 存储
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 10/15/2019
ms.topic: conceptual
ms.service: iot-central
manager: peterpr
ms.openlocfilehash: 118d2b42bc14a943aa3fa60b34aa1c151d5dea4c
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2019
ms.locfileid: "73176305"
---
# <a name="export-your-azure-iot-central-datapreview-features"></a>导出 Azure IoT Central 数据（预览功能）

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

*本主题适用于管理员。*

本文介绍如何使用 Azure IoT Central 中的连续数据导出功能将数据导出到 Azure 事件中心、Azure 服务总线或 Azure Blob 存储实例。 数据是以 JSON 格式导出的，可以包括遥测、设备信息和设备模板信息。 使用导出的数据进行以下操作：

- 热路径见解和分析。 此选项包括触发 Azure 流分析中的自定义规则、在 Azure 逻辑应用中触发自定义工作流，或通过要转换的 Azure Functions 传递自定义规则。
- 冷路径分析，如 Microsoft Power BI 中 Azure 机器学习或长期趋势分析中的定型模型。

> [!Note]
> 启用连续数据导出时，只能获得从那时之后的数据。 目前，关闭连续数据导出后将暂时无法检索数据。 若要保留更多的历史数据，请及早启用连续数据导出。

## <a name="prerequisites"></a>必备组件

你必须是 IoT Central 应用程序中的管理员

## <a name="set-up-export-destination"></a>设置导出目标

你的导出目标必须存在，然后才能配置你的连续数据导出。

### <a name="create-event-hubs-namespace"></a>创建事件中心命名空间

如果没有要导出到的现有事件中心命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的事件中心命名空间](https://ms.portal.azure.com/#create/Microsoft.EventHub)。 可以在 [Azure 事件中心文档](../../event-hubs/event-hubs-create.md)中进行详细的了解。

2. 选择订阅。 你可以将数据导出到与即用即付 IoT Central 应用程序不在同一订阅中的其他订阅。 在这种情况下，使用连接字符串进行连接。

3. 在事件中心命名空间中创建事件中心。 转到命名空间，选择顶部的“+ 事件中心”，以便创建事件中心实例。

### <a name="create-service-bus-namespace"></a>创建服务总线命名空间

如果没有要导出到的现有服务总线命名空间，请执行以下步骤：

1. [在 Azure 门户中创建新的 Service Bus 命名空间](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5)。 可以在 [Azure 服务总线文档](../../service-bus-messaging/service-bus-create-namespace-portal.md)中进行详细的了解。
2. 选择订阅。 你可以将数据导出到与即用即付 IoT Central 应用程序不在同一订阅中的其他订阅。 在这种情况下，使用连接字符串进行连接。

3. 转到服务总线命名空间，选择顶部的“+ 队列”或“+ 主题”，以便创建要向其导出内容的队列或主题。

选择 "服务总线" 作为导出目标时，队列和主题不得启用会话或重复检测。 如果启用了其中任一选项，则某些消息不会到达队列或主题中。

### <a name="create-storage-account"></a>创建存储器帐户

如果没有要导出到的现有 Azure 存储帐户，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的[Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 存储帐户](../../storage/blobs/data-lake-storage-quickstart-create-account.md)。

    - 如果选择将数据导出到 Azure Data Lake Storage v2 存储帐户，则必须选择**BlobStorage**作为**帐户类型**。
    - 你可以将数据导出到订阅中不同于即用即付 IoT Central 应用程序的存储帐户。 在此示例中，将使用连接字符串进行连接。

2. 在存储帐户中创建容器。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择顶部的“+ 容器”以创建新容器。

## <a name="set-up-continuous-data-export"></a>设置连续数据导出

现在，你已有将数据导出到的目标，请按照以下步骤设置连续数据导出。

1. 登录到 IoT Central 应用程序。

2. 在左窗格中，选择 "**数据导出**"。

    > [!Note]
    > 如果在左窗格中看不到 "数据导出"，则不是应用中的管理员。 请与管理员联系以设置数据导出。

3. 选择右上方的 " **+ 新建**" 按钮。 选择**Azure 事件中心**、 **azure 服务总线**或**azure Blob 存储**中的一个作为导出目标。 每个应用程序的最大导出数为5。

    ![创建新的连续数据导出](media/howto-export-data-pnp/export-new2.png)

4. 在下拉列表框中，选择 "**事件中心命名空间**"、"**服务总线命名空间**"、"**存储帐户命名空间**" 或**输入连接字符串**。

    - 您只能在与 IoT Central 应用程序相同的订阅中查看存储帐户、事件中心命名空间和服务总线命名空间。 若要导出到此订阅外部的某个目标，请选择“输入连接字符串”，然后参阅步骤 5。
    - 对于七天试用应用，配置连续数据导出的唯一方法是通过连接字符串。 七天试用版应用没有关联的 Azure 订阅。

    ![创建新的事件中心](media/howto-export-data-pnp/export-eh.png)

5. （可选）如果选中了“输入连接字符串”，则会出现一个用于粘贴连接字符串的新框。 若要获取连接字符串，请执行以下操作：
    - 事件中心或服务总线，请中转到 Azure 门户中的命名空间。
        - 在 "**设置**" 下，选择 "**共享访问策略**"
        - 选择默认的 **RootManageSharedAccessKey**，或者创建一个新的
        - 复制主连接字符串或辅助连接字符串
    - 存储帐户，请在 Azure 门户中找到存储帐户：
        - 在 "**设置**" 下，选择 "**访问密钥**"
        - 复制 key1 连接字符串或 key2 连接字符串

6. 从下拉列表框中选择 "事件中心"、"队列"、"主题" 或 "容器"。

7. 在 "**要导出的数据**" 下，选择要导出的数据类型，方法是将 "类型" 设置为 **"开**"。

8. 若要启用连续数据导出，请确保已**启用** **数据导出**切换。 选择“保存”。

9. 几分钟后，你的数据将显示在所选目标。

## <a name="data-format"></a>数据格式

数据将以近乎实时的顺序导出到事件中心、服务总线队列或主题。

每分钟将数据导出到存储帐户一次，每个文件包含自上次导出的文件以来发生的一批更改。 导出的数据被放置在采用 JSON 格式的三个文件夹中。 存储帐户中的默认路径是：

- 遥测： _{container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 设备： _{container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_
- 设备模板： _{container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}_

可以通过导航到文件并选择 "**编辑 blob** " 选项卡，在 Azure 门户中浏览导出的文件。

导出的遥测数据包含设备发送到 IoT Central 的完整消息，而不只是遥测值本身。 导出的设备数据包含对所有设备的属性和元数据的更改，导出的设备模板包含对所有设备模板所做的更改。 导出的数据在正文属性中，采用 JSON 格式。

### <a name="telemetry"></a>遥测

当 IoT Central 从设备收到新消息后，该消息会快速导出。

- 事件中心和服务总线中的每个导出的消息都包含设备在正文属性中以 JSON 格式发送的完整消息。
- Blob 存储中的导出文件使用的格式与[IoT 中心消息路由](../../iot-hub/iot-hub-csharp-csharp-process-d2c.md)到 blob 存储导出的消息文件相同。 确保你的设备正在发送的消息 `contentType: application/JSON` 和 `contentEncoding:utf-8` （或 `utf-16``utf-32`）。 有关示例，请参阅[IoT 中心文档](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body)。

发送遥测数据的设备以设备 Id 表示（请参阅以下部分）。 若要获取设备的名称，请导出设备数据，并使用与设备消息的**deviceId**匹配的**connectionDeviceId**来关联每条消息。

下面的示例演示有关在事件中心或服务总线队列或主题中收到的遥测数据的消息。

```json
{
  "body":{
    "temp":67.96099945281145,
    "humid":58.51139305465015,
    "pm25":36.91162432340187
  },
  "annotations":{
    "iothub-connection-device-id":"<deviceId>",
    "iothub-connection-auth-method":"{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "iothub-connection-auth-generation-id":"<generationId>",
    "iothub-enqueuedtime":1539381029965,
    "iothub-message-source":"Telemetry",
    "x-opt-sequence-number":25325,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539381030200
  },
  "sequenceNumber":25325,
  "enqueuedTimeUtc":"2018-10-12T21:50:30.200Z",
  "offset":"<offset>",
  "properties":{
    "content_type":"application/json",
    "content_encoding":"utf-8"
  }
}
```

下面的示例在 blob 存储中显示 JSON 格式的记录：

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

### <a name="devices"></a>设备

每隔数分钟，包含设备数据的消息就会发送到事件中心或服务总线队列或主题。 新快照每分钟写入一次 blob 存储。 每个消息或快照都包含有关以下内容的数据：

- 已添加的新设备
- 已更改属性值的设备

快照中的每条消息或记录表示自上次导出的消息以来对设备进行的一项或多项更改。 信息包括：

- IoT Central 中设备的 `@id`
- 设备的 `name`
- [设备预配服务](/azure/iot-central/core/howto-connect-nodejs)中的 `deviceId`
- 设备模板信息
- 属性值

上一批次导出后删除的设备不会导出。 目前，在导出的消息中没有针对已删除设备的指示器。

每台设备所属的设备模板由设备模板 ID 表示。 若要获取设备模板的名称，请务必也导出设备模板数据。

以下示例显示了一条消息，该消息描述在事件中心或服务总线的队列或主题中的设备数据：

```json
{
  "body":{
    "@id":"<id>",
    "@type":"Device",
    "displayName":"Airbox - 266d30aedn5",
    "data":{
      "$cloudProperties":{
        "Color":"blue"
      },
      "EnvironmentalSensor":{
        "thsensormodel":{
          "reported":{
            "value":"A1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "pm25sensormodel":{
          "reported":{
            "value":"P1",
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      },
      "urn_azureiot_DeviceManagement_DeviceInformation":{
        "totalStorage":{
          "reported":{
            "value":3088.1959855710156,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        },
        "totalMemory":{
          "reported":{
            "value":16005.703586477555,
            "$lastUpdatedTimestamp":"2019-10-02T18:14:49.3820326Z"
          }
        }
      }
    },
    "instanceOf":"<templateId>",
    "deviceId":"<deviceId>",
    "simulated":true
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
  "enqueuedTimeUtc":"2019-10-02T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

导出的文件包含每条记录一行。 下面的示例显示 JSON 格式的记录。

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

### <a name="device-templates"></a>设备模板

每隔数分钟，包含设备模板数据的消息就会发送到事件中心或服务总线队列或主题。 新快照每分钟写入到 blob 存储一次。 因此，每隔几分钟就会收到一批消息，其中包含有关以下内容的数据：

包含设备模板数据的消息将每隔几分钟发送到事件中心或服务总线队列或主题。 新快照每分钟写入一次 blob 存储。 每个消息或快照都包含有关以下内容的数据：

- 添加或版本控制的新设备模板
- 具有更改的功能模型、云属性、替代和初始值的设备模板

每条消息或快照记录表示自上次导出的消息以来对设备模板进行的一项或多项更改。 每条消息或记录中发送的信息包括：

- 设备模板的 `@id`
- 设备模板的 `name`
- 设备模板的 `version`
- 设备 `capabilityModel` 包括其 `interfaces`、遥测、属性和命令定义
- `cloudProperties` 定义
- 与 `capabilityModel` 内联的替代和初始值

上一批次导出后删除的设备模板不会导出。 目前，在导出的消息中没有针对已删除设备模板的指示器。

以下示例显示了事件中心或服务总线队列或主题中的设备模板消息：

```json
{
  "body":{
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

导出的文件包含每条记录一行。 下面的示例显示 JSON 格式的记录。

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

了解如何将数据导出到 Azure 事件中心和 Azure 服务总线后，请继续进行下一步：

> [!div class="nextstepaction"]
> [如何触发 Azure Functions](howto-trigger-azure-functions.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
