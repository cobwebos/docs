---
title: 使用 Visual Studio Code Azure IoT Edge 大规模部署模块
description: 使用 Visual Studio Code 的 IoT 扩展为 IoT Edge 设备组创建自动部署。
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 1/8/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 08299a589dc6e8f768cba7ef976e109ef1fb69d7
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774128"
---
# <a name="deploy-iot-edge-modules-at-scale-using-visual-studio-code"></a>使用 Visual Studio Code 大规模部署 IoT Edge 模块

可以使用 Visual Studio Code 创建一次**IoT Edge 自动部署**，以便同时管理多个设备的正在进行的部署。 IoT Edge 的自动部署是 IoT 中心[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是使你能够将多个模块部署到多个设备的动态进程。 还可以跟踪模块的状态和运行状况，并在必要时进行更改。

有关详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

本文介绍如何设置 Visual Studio Code 和 IoT 扩展。 然后，了解如何将模块部署到一组 IoT Edge 设备。

## <a name="prerequisites"></a>必备组件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-through-portal.md)。
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#register-with-visual-studio-code)。
* [Visual Studio Code](https://code.visualstudio.com/)。
* 适用于 Visual Studio Code 的 [Azure IoT 工具](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools#overview)。

## <a name="sign-in-to-access-your-iot-hub"></a>登录以访问 IoT 中心

你可以使用适用于 Visual Studio Code 的 Azure IoT 扩展在中心执行操作。 要使这些操作正常工作，需要登录到 Azure 帐户，并选择正在处理的 IoT 中心。

1. 在 Visual Studio Code 中打开“资源管理器”视图。

1. 在资源管理器的底部，展开 " **Azure IoT 中心**" 部分。

1. 在**Azure IoT 中心**部分标题中单击 " **...** "。 如果没有看到省略号，请将鼠标悬停在标题处。

1. 选择“选择 IoT 中心”。

1. 如果未登录到 Azure 帐户，请按照提示进行操作。

1. 选择 Azure 订阅。

1. 选择 IoT 中心。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，用于描述要部署的模块。 它还说明了模块之间数据的流动方式，以及模块孪生的所需属性。 有关详细信息，请参阅[了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)。

若要使用 Visual Studio Code 部署模块，请将部署清单本地保存为 .JSON 文件。 运行命令将配置应用到设备时，需要提供其位置。

下面是一个基本的部署清单示例，其中有一个模块：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired": {
          "schemaVersion": "1.0",
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
              "version": "1.0",
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

如果需要确定当前可以配置哪些 IoT Edge 设备，请运行**IoT Edge：获取设备信息**命令。

## <a name="identify-devices-with-target-conditions"></a>标识具有目标条件的设备

若要确定接收部署的 IoT Edge 设备，必须指定目标条件。 当指定的条件由 deviceId、标记值或报告的属性值匹配时，将满足目标条件。

在设备克隆中配置标记。 下面是包含标记的设备克隆示例：

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

如果部署的目标条件包含与某个标记值（如 `tag.location.building = '20'`）匹配的表达式，则此设备将接收部署。

如果要以特定设备为目标，而不考虑其标记或其他值，只需指定目标条件的 `deviceId`。

下面是一些更多示例：

* deviceId ='linuxprod1'
* deviceId = ' linuxprod1 ' 或 deviceId = ' linuxprod2 ' 或 deviceId = ' linuxprod3 '
* tags.environment ='prod'
* tags = "生产" 和标记。 location = \ "westus2"
* tags = "生产" 或标记。 location = \ "westus2"
* tags = "John" 和标记。环境 = "生产"，而不是 deviceId = "linuxprod1"

有关详细信息，请参阅[目标条件](module-deployment-monitoring.md#target-condition)。 有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。

### <a name="edit-the-device-twin"></a>编辑设备克隆

可以在 Visual Studio Code 中编辑设备克隆，以配置标记。 从 "**视图**" 菜单中，选择 "**命令面板**"，并运行**IoT Edge：编辑设备**克隆命令。 选择 IoT Edge 设备，并显示设备克隆。

在此示例中，未定义任何标记。 将当前的空节 `"tags": {}` 替换为您自己的标记定义。

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

保存本地文件后，请运行**IoT Edge：更新设备**克隆命令。

## <a name="create-deployment-at-scale"></a>大规模创建部署

在设备克隆中配置部署清单和已配置的标记后，便可以进行部署了。

1. 从 "**视图**" 菜单中，选择 "**命令面板**"，然后选择 " **Azure IoT Edge：创建大规模部署**" 命令。

1. 导航至要使用的部署清单 JSON 文件，然后单击“选择 Edge 部署清单”。

1. 按提示提供值，从**部署 id**开始。

   ![指定部署 id](./media/how-to-deploy-monitor-vscode/create-deployment-at-scale.png)

   为这些参数指定值：

  | 参数 | Description |
  | --- | --- |
  | 部署 id | 将在 IoT 中心内创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。 |
  | 目标条件 | 输入一个目标条件，以确定将以该部署为目标的设备。 此条件基于设备克隆标记或设备克隆报告的属性，并且应与表达式格式匹配。 例如，`tags.environment='test' and properties.reported.devicemodel='4000x'`。 |
  | 优先级 |  正整数。 如果在同一设备上针对两个或更多部署，则将应用具有最高优先级的数值的部署。 |

  指定优先级后，终端应显示类似于以下描述的输出：

   ```cmd
   [Edge] Start deployment with deployment id [{specified-value}] and target condition [{specified-value}]
   [Edge] Deployment with deployment id [{specified-value}] succeeded.
   ```

## <a name="monitoring-and-modifying-deployments"></a>监视和修改部署

使用[Azure CLI](how-to-deploy-monitor-cli.md#monitor-a-deployment)或[Azure 门户](how-to-deploy-monitor.md#monitor-a-deployment)来监视、修改和删除部署。 两者都提供部署的指标。

## <a name="next-steps"></a>后续步骤

了解有关[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)的详细信息。
