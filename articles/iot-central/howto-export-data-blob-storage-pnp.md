---
title: 将数据导出到 Azure Blob 存储 | Microsoft Docs
description: 如何将数据从 Azure IoT Central 应用程序导出到 Azure Blob 存储
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 09/26/2019
ms.topic: conceptual
ms.service: iot-central
manager: corywink
ms.openlocfilehash: 9ac650273a53da715b89e3233b30cf50710cfcfd
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973233"
---
# <a name="export-your-data-to-azure-blob-storage-preview-features"></a>将数据导出到 Azure Blob 存储（预览功能）

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*本主题适用于管理员。*

本文介绍如何使用 Azure IoT Central 中的连续数据导出功能定期将数据导出到**Azure Blob 存储帐户**或**Azure Data Lake Storage Gen2 存储帐户**。 可以将**遥测**、**设备**和**设备模板**导出为 JSON 格式的文件。 导出的数据可用于冷路径分析，例如 Azure 机器学习中的训练模型或 Microsoft Power BI 中的长期趋势分析。

> [!Note]
> 启用连续数据导出时，只能获得从那时之后的数据。 目前，关闭连续数据导出后将暂时无法检索数据。 若要保留更多的历史数据，请及早启用连续数据导出。


## <a name="prerequisites"></a>先决条件

- 你必须是 IoT Central 应用程序中的管理员

## <a name="create-storage-account"></a>创建存储帐户
如果没有要导出到的现有存储，请执行以下步骤：

1. [在 Azure 门户中创建新的存储帐户](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)。 可以详细了解如何创建新的[Azure Blob 存储帐户](https://aka.ms/blobdocscreatestorageaccount)或[Azure Data Lake Storage v2 存储帐户](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-quickstart-create-account)。

    > [!Note] 
    > 如果选择将数据导出到 ADLS v2 存储帐户，则必须选择 "**帐户类型**" 作为 " **BlobStorage**"。 

    > [!Note] 
    > 你可以将数据导出到订阅中不同于即用即付 IoT Central 应用程序的存储帐户。 在此示例中，将使用连接字符串进行连接。

2. 在存储帐户中创建容器。 转到存储帐户。 在“Blob 服务”下选择“浏览 Blob”。 选择顶部的“+ 容器”以创建新容器。


## <a name="set-up-continuous-data-export"></a>设置连续数据导出

现在，你有了一个要将数据导出到的存储目标，请按照以下步骤设置连续数据导出。 

1. 登录到 IoT Central 应用程序。

2. 在左侧菜单中，选择 "**数据导出**"。

    > [!Note]
    > 如果在左侧菜单中看不到数据导出，则不是应用中的管理员。 请与管理员联系以设置数据导出。

3. 选择右上方的 " **+ 新建**" 按钮。 选择 " **Azure Blob 存储**" 作为导出目标。 

    > [!NOTE] 
    > 每个应用的最大导出数目是 5。 

    ![创建新的连续数据导出](media/howto-export-data-pnp/export-new2.png)

4. 在下拉列表框中，选择**存储帐户命名空间**。 也可选取列表中的最后一个选项，即“输入连接字符串”。 

    > [!NOTE] 
    > 你将只能看到**与 IoT Central 应用相同的订阅**中的存储帐户命名空间。 若要导出到此订阅外部的某个目标，请选择“输入连接字符串”，然后参阅步骤 5。

    > [!NOTE] 
    > 若要通过 7 天试用期的应用来配置连续事件导出，则唯一的方式是使用连接字符串。 这是因为 7 天试用期的应用没有关联的 Azure 订阅。

    ![创建新的导出到 Blob](media/howto-export-data-pnp/export-create-blob2.png)

5. （可选）如果选中了“输入连接字符串”，则会出现一个用于粘贴连接字符串的新框。 若要获取存储帐户的连接字符串，请转到 Azure 门户中的存储帐户：
    - 在 "**设置**" 下，选择 "**访问密钥**"
    - 复制 key1 连接字符串或 key2 连接字符串
 
6. 从下拉列表框中选择一个容器。 如果没有容器，请在 Azure 门户中找到存储帐户：
    - 在 " **blob 服务**" 下，选择 " **blob**"。 单击 " **+ 容器**" 并为容器指定名称。 选择数据的公共访问级别（any 将适用于连续数据导出）。 了解[Azure 存储文档](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)中的详细信息。

7.  在“要导出的数据”下，通过将类型设置为“打开”来指定要导出的各类数据。
   
    > [!NOTE] 
    > 数据采用 JSON 格式导出。

8. 若要启用连续数据导出，请确保已启用**数据导出**切换。 选择“保存”。

   ![配置连续数据导出](media/howto-export-data-pnp/export-list-blob2.png)

9. 几分钟后，你的数据将显示在存储帐户中。


## <a name="path-structure"></a>路径结构

遥测、设备和设备模板数据每分钟导出到你的存储帐户一次，每个文件包含自上次导出的文件以来发生的一批更改。 导出的数据被放置在采用 JSON 格式的三个文件夹中。 存储帐户中的默认路径是：
- 遥测： {container}/{app-id}/telemetry/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 设备： {container}/{app-id}/devices/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}
- 设备模板： {container}/{app-id}/deviceTemplates/{YYYY}/{MM}/{dd}/{hh}/{mm}/{filename}

可以通过导航到文件并选择 "**编辑 blob** " 选项卡，在 Azure 门户中浏览导出的文件。

## <a name="data-format"></a>数据格式
### <a name="telemetry"></a>遥测

导出的遥测数据包含在该时间段内所有设备 IoT Central 收到的所有新消息。 导出的文件所用格式与通过 [IoT 中心消息路由](https://docs.microsoft.com/azure/iot-hub/iot-hub-csharp-csharp-process-d2c)导出到 Blob 存储中的消息文件格式相同。

> [!NOTE]
> 确保你的设备正在发送的消息 `contentType: application/JSON` 并 `contentEncoding:utf-8` （或 `utf-16`，`utf-32`）。 有关示例，请参阅[IoT 中心文档](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-routing-query-syntax#message-routing-query-based-on-message-body)。

> [!NOTE]
> 发送遥测数据的设备以设备 Id 表示（请参阅以下部分）。 若要获取设备名称，请导出设备快照。 使用与设备记录的 deviceId 匹配的 connectionDeviceId 来关联每条消息记录。

下面的示例显示 JSON 格式的记录。

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

首次启用连续数据导出时，会导出包含所有设备的单个快照。 每个设备包括：
- IoT Central 中设备的 `@id`
- 设备的 `name`
- [设备预配服务](https://aka.ms/iotcentraldocsdps)中的 `deviceId`
- 设备模板信息
- 属性值

每分钟写入一次新的快照。 快照包含：

- 上一快照导出后添加的新设备。
- 自上一快照后更改了属性值的设备。

> [!NOTE]
> 上一快照导出后删除的设备不会导出。 目前，快照没有已删除设备标记。
>
> 每个设备所属的设备模板由 `instanceOf` 字段表示。 若要获取设备模板的名称，请导出设备模板快照。

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

首次启用连续数据导出时，会导出包含所有设备模板的单个快照。 每个设备模板包括：
- 设备模板的 `@id`
- 设备模板的 `name`
- 设备模板的 `version`
- 设备 `capabilityModel`，包括其 `interfaces`、遥测、属性和命令定义
- `cloudProperties` 定义
- 用 `capabilityModel` 内联的替代和初始值

每分钟写入一次新的快照。 快照包含：

- 上一快照导出后添加的新设备模板。 这包括新版本的设备模板。
- 自上次拍摄快照以来更改了替代、初始值和云属性定义的设备模板。

> [!NOTE]
> 上一快照导出后删除的设备模板不会导出。 目前，快照没有已删除设备模板标记。

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

了解如何导出数据后，继续进行下一步：

> [!div class="nextstepaction"]
> [如何使用 IoT Central 设备桥来连接其他 IoT 云](howto-build-iotc-device-bridge.md)
