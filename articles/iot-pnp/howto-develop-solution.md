---
title: 从 Azure IoT 解决方案与 IoT 即插即用预览版设备交互 | Microsoft Docs
description: 本教程向解决方案开发人员介绍如何使用服务 SDK 来与 IoT 即插即用设备交互。
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: tutorial
ms.custom: mvc
ms.service: iot-pnp
services: iot-pnp
manager: philmea
ms.openlocfilehash: 485b17ff236de32eab5388629c1bb6044ba19197
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531337"
---
# <a name="connect-to-and-interact-with-an-iot-plug-and-play-preview-device"></a>连接到 IoT 即插即用预览版设备并与之交互

本操作指南介绍如何使用 Node 服务 SDK 中的示例，其中演示了 IoT 解决方案如何与 IoT 即插即用预览版设备交互。

如果你尚未完成[将 IoT 即插即用设备连接到解决方案](quickstart-connect-pnp-device-solution-node.md)快速入门，请先完成该教程。 本快速入门介绍如何下载和安装 SDK，以及运行一些示例。

在运行服务示例之前，请打开新的终端，转到克隆的存储库的根文件夹，导航到 **digitaltwins/quickstarts/service** 文件夹，然后运行以下命令安装依赖项：

```cmd/sh
npm install
```

## <a name="run-the-service-samples"></a>运行服务示例

使用以下示例来浏览 Node.js 服务 SDK 的功能。 确保在使用的 shell 中设置 `IOTHUB_CONNECTION_STRING` 环境变量：

### <a name="retrieve-a-digital-twin-and-list-the-interfaces"></a>检索数字孪生并列出接口

**get_digital_twin.js** 获取与设备关联的数字孪生，并在命令行中输出其组成部分。 此脚本无需运行设备示例即可成功。

**get_digital_twin_interface_instance.js** 获取与设备关联的数字孪生的单个接口实例，并在命令行中输出此信息。 它不要求运行设备示例。

### <a name="get-and-set-properties-using-the-node-service-sdk"></a>使用 Node 服务 SDK 获取和设置属性

**update_digital_twin.js** 使用完整修补程序更新设备数字孪生上的可写属性。 如果需要，可以更新多个接口上的多个属性。 要使此脚本成功，同时需要运行设备示例。 如果成功，设备示例会输出有关更新属性的内容，并且服务示例会在终端中输出更新的数字孪生。

### <a name="send-a-command-and-retrieve-the-response-using-the-node-service-sdk"></a>使用 Node 服务 SDK 发送命令并检索响应

**invoke_command.js** 在设备数字孪生上调用一个同步命令。 要使此脚本成功，同时需要运行设备示例。 如果成功，设备示例会输出有关确认命令的内容，并且服务客户端会在终端中输出该命令的结果。

### <a name="connect-to-the-public-repository-and-retrieve-a-model-definition-using-the-node-service-sdk"></a>使用 Node 服务 SDK 连接到公共存储库并检索模型定义

使用适用于服务和设备示例的相同说明时，需要设置以下环境变量：

* `AZURE_IOT_MODEL_REPOSITORY_CONNECTION_STRING`

可以在 [Azure IoT 认证门户](https://preview.catalog.azureiotsolutions.com)中“公司存储库”的“连接字符串”选项卡上找到此连接字符串。  

连接字符串如以下示例所示：

```text
HostName={repo host name};RepositoryId={repo ID};SharedAccessKeyName={repo key ID};SharedAccessKey={repo key secret}
```

设置这四个环境变量后，像运行其他示例一样运行该示例：

```cmd/sh
node model_repo.js
```

此示例将下载 **ModelDiscovery** 接口，并在终端中输出此模型。

### <a name="run-queries-in-iot-hub-based-on-capability-models-and-interfaces"></a>基于功能模型和接口在 IoT 中心运行查询

IoT 中心查询语言支持以下示例所示的 `HAS_INTERFACE` 和 `HAS_CAPABILITYMODEL`：

```sql
select * from devices where HAS_INTERFACE('id without version', version)
```

```sql
select * from devices where HAS_CAPABILITYMODEL('id without version', version)
```

### <a name="creating-digital-twin-routes"></a>创建数字孪生路由

解决方案可以接收数字孪生更改事件的通知。 要订阅这些通知，请使用 [IoT 中心路由功能](../iot-hub/iot-hub-devguide-endpoints.md)将通知发送到某个终结点，例如 blob 存储、事件中心或服务总线队列。

若要创建数字孪生路由：

1. 在 Azure 门户中，转到你的 IoT 中心资源。
1. 选择“消息路由”。 
1. 在“路由”选项卡上，选择“添加”。  
1. 在“名称”字段中输入一个值，然后选择一个**终结点**。  如果尚未配置终结点，请选择“添加终结点”。 
1. 在“数据源”下拉列表中，选择“数字孪生更改事件”。  
1. 选择“保存”。 

以下 JSON 显示了数字孪生更改事件的示例：

```json
{
  "interfaces": {
    "urn_azureiot_ModelDiscovery_DigitalTwin": {
      "name": "urn_azureiot_ModelDiscovery_DigitalTwin",
      "properties": {
        "modelInformation": {
          "reported": {
            "value": {
              "modelId": "urn:domain:capabilitymodel:TestCapability:1",
              "interfaces": {
                "MyInterfaceFoo": "urn:domain:interfaces:FooInterface:1",
                "urn_azureiot_ModelDiscovery_DigitalTwin": "urn:azureiot:ModelDiscovery:DigitalTwin:1"
              }
            }
          }
        }
      }
    },
    "MyInterfaceFoo": {
      "name": "MyInterfaceFoo",
      "properties": {
        "property_1": { "desired": { "value": "value_1" } },
        "property_2": {
          "desired": { "value": 20 },
          "reported": {
            "value": 10,
            "desiredState": {
              "code": 200,
              "version": 22,
              "subCode": 400,
              "description": ""
            }
          }
        },
        "property_3": { "reported": { "value": "value_3" } }
      }
    }
  },
  "version": 4
}
```

## <a name="next-steps"></a>后续步骤

了解可与 IoT 即插即用设备交互的服务解决方案后，我们建议接下来了解[模型发现](concepts-model-discovery.md)。
