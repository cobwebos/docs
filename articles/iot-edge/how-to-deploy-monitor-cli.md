---
title: 从命令行创建自动部署 - Azure IoT Edge | Microsoft Docs
description: 使用 Azure CLI 的 IoT 扩展为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/17/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 68cc82733bb264eedb96239e7353ac30224bda64
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/24/2019
ms.locfileid: "74457398"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模部署并监视 IoT Edge 模块

Create an **IoT Edge automatic deployment** using the Azure command-line interface to manage ongoing deployments for many devices at once. Automatic deployments for IoT Edge are part of the [automatic device management](/azure/iot-hub/iot-hub-automatic-device-management) feature of IoT Hub. Deployments are dynamic processes that enable you to deploy multiple modules to multiple devices, track the status and health of the modules, and make changes when necessary. 

For more information, see [Understand IoT Edge automatic deployments for single devices or at scale](module-deployment-monitoring.md).

在本文中，将安装 Azure CLI 和 IoT 扩展。 You then learn how to deploy modules to a set of IoT Edge devices and monitor the progress using the available CLI commands.

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#prerequisites-for-the-azure-cli)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.24 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 For more information, see [Learn how to deploy modules and establish routes in IoT Edge](module-composition.md).

若要使用 Azure CLI 来部署模块，请将部署清单在本地另存为 .txt 文件。 You use the file path in the next section when you run the command to apply the configuration to your device. 

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
              "registryCredentials": {
                "registryName": {
                  "username": "",
                  "password": "",
                  "address": ""
                }
              }
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
                "createOptions": "{}"
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
            "route": "FROM /* INTO $upstream"
          },
          "storeAndForwardConfiguration": {
            "timeToLiveSecs": 7200
          }
        }
      },
      "SimulatedTemperatureSensor": {
        "properties.desired": {}
      }
    }
  }
}
```

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 Each device can have multiple tags that you define in any way that makes sense for your solution. 例如，如果管理有智能楼宇的校园，可能要将以下标记添加到设备：

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

有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生](../iot-hub/iot-hub-devguide-device-twins.md)。

## <a name="create-a-deployment"></a>创建部署

请创建一个包含部署清单和其他参数的部署，以便将模块部署到目标设备。 

Use the [az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create) command to create a deployment:

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

The deployment create command takes the following parameters: 

* **--deployment-id** - 将在 IoT 中心创建的部署的名称。 为部署命名唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
* **--hub-name** - 将在其中创建部署的 IoT 中心的名称。 此中心必须在当前订阅中。 Change your current subscription with the `az account set -s [subscription name]` command.
* **--content** - 部署清单 JSON 的文件路径。 
* **--labels** - 添加用于跟踪部署的标签。 标签是描述部署的“名称, 值”对。 标签对名称和值采用 JSON 格式设置。 例如： `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此部署的目标。 The condition is based on device twin tags or device twin reported properties and should match the expression format. For example, `tags.environment='test' and properties.reported.devicemodel='4000x'`. 
* **--priority** - 一个正整数。 如果在同一台设备上定目标到两个或多个部署，将会应用优先级数值最高的部署。

## <a name="monitor-a-deployment"></a>监视部署

Use the [az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show) command to display the details of a single deployment:

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

The deployment show command takes the following parameters:
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查部署。 The **metrics** property lists a count for each metric that is evaluated by each hub:

* **targetedCount** - 一个系统指标，根据目标条件指定 IoT 中心的设备孪生数。
* **appliedCount** - 一个系统指标，指定已在 IoT 中心将部署内容应用到其模块孪生的设备数。
* **reportedSuccessfulCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting success from the IoT Edge client runtime.
* **reportedFailedCount** - A device metric that specifies the number of IoT Edge devices in the deployment reporting failure from the IoT Edge client runtime.

You can show a list of device IDs or objects for each of the metrics by using the [az iot edge deployment show-metric](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric) command:

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
```

The deployment show-metric command takes the following parameters: 
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--metric-id** - 需要查看设备 ID 列表时所对应指标的名称，例如 `reportedFailedCount`
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。 
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。 
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。 

Use the [az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update) command to update a deployment:

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

The deployment update command takes the following parameters:
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新部署中的属性。 可以更新以下属性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 标签 
  * priority


## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。 

Use the [az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete) command to delete a deployment:

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
```

The deployment delete command takes the following parameters: 
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤

Learn more about [Deploying modules to IoT Edge devices](module-deployment-monitoring.md).
