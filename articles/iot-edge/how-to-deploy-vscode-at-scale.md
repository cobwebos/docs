---
title: 使用 Visual Studio Code 大规模部署模块 - Azure IoT Edge
description: 使用 Visual Studio Code 的 IoT 扩展为 IoT Edge 设备组创建自动部署。
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 7f6e90edc0503326dc9dbb06abfcf59fa2d51e1e
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92043810"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>使用 Visual Studio Code 大规模部署 IoT Edge 模块

可以使用 Visual Studio Code 接口创建 **IoT Edge 自动部署**，以便同时管理多个设备的持续部署。 IoT Edge 的自动部署属于 IoT 中心的[自动设备管理](../iot-hub/iot-hub-automatic-device-management.md)功能。 部署是可让你将多个模块部署到多个设备的动态过程。 还可以跟踪模块的状态和运行状况，并按需进行更改。

有关详细信息，请参阅[了解单个设备或大规模的 IoT Edge 自动部署](module-deployment-monitoring.md)。

在本文中，你将设置 Visual Studio Code 和 IoT 扩展。 然后，了解如何将模块部署到一组 IoT Edge 设备。

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 一个或多个 IoT Edge 设备。

  如果未设置 IoT Edge 设备，则可以在 Azure 虚拟机中创建一个。 按照其中一篇快速入门文章中的步骤 [创建虚拟 Linux 设备](quickstart-linux.md) ，或 [创建虚拟 Windows 设备](quickstart.md)。

* [Visual Studio Code](https://code.visualstudio.com/)。
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

可以使用 Visual Studio Code 的 Azure IoT 扩展来执行与中心相关的操作。 为让操作顺利进行，需登录到 Azure 帐户并选择要使用的 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图。

1. 在资源管理器底部，展开“Azure IoT 中心”部分。

1. 单击“Azure IoT 中心”部分标题中的“...” 。 如果没有看到省略号，请将鼠标悬停在标题处。

1. 选择“选择 IoT 中心”。

1. 如果尚未登录 Azure 帐户，请按照提示登录。

1. 选择 Azure 订阅。

1. 选择 IoT 中心。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，用于描述要部署的模块。 它还描述了模块之间的数据流动方式，以及模块孪生的所需属性。 有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

若要使用 Visual Studio Code 部署模块，请将部署清单本地保存为 .JSON 文件。 运行命令将配置应用到设备时，需要提供清单的位置。

下面是一个基本的部署清单示例，其中有一个模块：

>[!NOTE]
>此示例部署清单使用架构版本1.1 作为 IoT Edge 代理和中心。 架构版本1.1 与 IoT Edge 版本1.0.10 一起发布，并启用了模块启动顺序和路由优先级等功能。

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.1",
          "runtime": {
            "type": "docker",
            "settings": {
              "minDockerVersion": "v1.25",
              "loggingOptions": "",
              "registryCredentials": {}
            }
          },
          "systemModules": {
            "edgeAgent": {
              "type": "docker",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                "createOptions": "{}"
              }
            },
            "edgeHub": {
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}],\"443/tcp\":[{\"HostPort\":\"443\"}]}}}"
              }
            }
          },
          "modules": {
            "SimulatedTemperatureSensor": {
              "version": "1.1",
              "type": "docker",
              "status": "running",
              "restartPolicy": "always",
              "settings": {
                "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
                "createOptions": "{}"
              }
            }
          }
        }
      },
      "$edgeHub": {
        "properties.desired": {
          "schemaVersion": "1.0",
          "routes": {
            "upstream": "FROM /messages/* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {
          "SendData": true,
          "SendInterval": 5
        }
      }
    }
  }
}
```

如果需要确定当前可以配置哪些 IoT Edge 设备，请运行“IoT Edge:获取设备信息”命令。

## <a name="identify-devices-with-target-conditions"></a>使用目标条件标识设备

若要标识接收部署的 IoT Edge 设备，必须指定目标条件。 当 deviceId、标记值或报告属性值与指定的条件匹配时，将满足目标条件。

在设备孪生中配置标记。 下面是具有标记的设备孪生示例：

```json
"tags":{
  "location":{
    "building": "20",
    "floor": "2"
  },
  "roomtype": "conference",
  "environment": "prod"
}
```

如果部署的目标条件包含与某个标记值匹配的表达式（例如 `tag.location.building = '20'`），则此设备将接收部署。

若要以特定的设备为目标，而不管其标记或其他值为何，只需指定目标条件的 `deviceId`。

下面提供了更多示例：

* deviceId ='linuxprod1'
* deviceId = 'linuxprod1' OR deviceId = 'linuxprod2' OR deviceId = 'linuxprod3'
* tags.environment ='prod'
* tags.environment = 'prod' AND tags.location = 'westus2'
* tags.environment = 'prod' OR tags.location = 'westus2'
* tags.operator = 'John' AND tags.environment = 'prod' AND NOT deviceId = 'linuxprod1'

有关详细信息，请参阅[目标条件](module-deployment-monitoring.md#target-condition)。 有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。

### <a name="edit-the-device-twin"></a>编辑设备孪生

可以在 Visual Studio Code 中编辑设备孪生，以配置标记。 在“视图”菜单中，选择“命令面板”并运行“IoT Edge:  编辑设备孪生”命令。 选择 IoT Edge 设备，随即会显示设备孪生。

此示例中未定义任何标记。 将当前的空节 `"tags": {}` 替换为自己的标记定义。

```json
{
    "deviceId": "myEdgeDevice",
    "etag": "AAAAAAAAAAE=",
    "deviceEtag": "NTgwMDg5MDAz",
    "status": "enabled",
    "statusUpdateTime": "0001-01-01T00:00:00Z",
    "connectionState": "Disconnected",
    "lastActivityTime": "0001-01-01T00:00:00Z",
    "cloudToDeviceMessageCount": 0,
    "authenticationType": "sas",
    "x509Thumbprint": {
        "primaryThumbprint": null,
        "secondaryThumbprint": null
    },
    "version": 2,
    "properties": {
        "desired": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        },
        "reported": {
            "$metadata": {
                "$lastUpdated": "2019-12-29T00:58:49.9315265Z"
            },
            "$version": 1
        }
    },
    "capabilities": {
        "iotEdge": true
    },
    "deviceScope": "ms-azure-iot-edge://myEdgeDevice-637131779299315265",
    "tags": {}
}
```

保存本地文件后，运行“IoT Edge:更新设备孪生”命令。

## <a name="create-deployment-at-scale"></a>大规模创建部署

配置部署清单并在设备孪生中配置标记后，便可以开始部署了。

1. 在“视图”菜单中，选择“命令面板”并选择“Azure IoT Edge:  大规模创建部署”命令。

1. 导航至要使用的部署清单 JSON 文件，然后单击“选择 Edge 部署清单”。

1. 按提示提供值，从“部署 ID”开始。

   ![指定部署 ID](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   指定以下参数的值：

  | 参数 | 说明 |
  | --- | --- |
  | 部署 ID | 将在 IoT 中心创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。 |
  | 目标条件 | 输入目标条件，确定用作此部署的目标的设备。 该条件基于设备孪生标记或设备孪生报告的属性，应与表达式格式相匹配。 例如 `tags.environment='test' and properties.reported.devicemodel='4000x'`。 |
  | 优先级 |  正整数。 如果同一设备上确定的部署目标至少有两个，则会应用优先级数值最高的部署。 |

  指定优先级后，终端应会显示类似于以下描述内容的输出：

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>监视和修改部署

使用 [Azure 门户](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-in-the-azure-portal)或 [Azure CLI](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli) 来监视、修改和删除部署。 这两个工具都会提供有关部署的指标。

## <a name="next-steps"></a>后续步骤

详细了解[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)。