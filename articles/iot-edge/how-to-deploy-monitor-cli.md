---
title: 使用 Azure CLI Azure IoT Edge 大规模部署模块
description: 使用 Azure CLI 的 IoT 扩展为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9152b38a0155b610f39f7de239bcc377ad96be5d
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78893177"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模部署并监视 IoT Edge 模块

使用 Azure 命令行接口创建一个**IoT Edge 自动部署**，为多个设备同时管理正在进行的部署。 IoT Edge 的自动部署是 IoT 中心[自动设备管理](/azure/iot-hub/iot-hub-automatic-device-management)功能的一部分。 部署是动态过程，使你能够将多个模块部署到多个设备、跟踪模块的状态和运行状况，并在必要时进行更改。

有关详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

在本文中，将安装 Azure CLI 和 IoT 扩展。 然后，了解如何将模块部署到一组 IoT Edge 设备，并使用可用的 CLI 命令监视进度。

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device.md#prerequisites-for-the-azure-cli)。
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本至少必须是2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 有关详细信息，请参阅[了解如何部署模块和在 IoT Edge 中建立路由](module-composition.md)。

若要使用 Azure CLI 来部署模块，请将部署清单在本地另存为 .txt 文件。 运行命令将配置应用到设备时，可以使用下一部分中的文件路径。

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

## <a name="layered-deployment"></a>分层部署

分层部署是一种自动部署类型，可以彼此重叠。 有关分层部署的详细信息，请参阅[了解 IoT Edge 单个设备的自动部署或大规模部署](module-deployment-monitoring.md)。

可以使用与任何自动部署相同的 Azure CLI 创建和管理分层部署，只需几个差异。 创建分层部署后，同一个 Azure CLI 适用于分层部署，这与任何部署相同。 若要创建分层部署，请将 `--layered` 标志添加到 create 命令。

第二个区别在于部署清单的构造。 尽管标准自动部署除了包含任何用户模块外，还必须包含系统运行时模块，但分层部署只能包含用户模块。 相反，分层部署还需要在设备上进行标准自动部署，以便提供每个 IoT Edge 设备（如系统运行时模块）的必需组件。

下面是一个基本分层部署清单，其中包含一个模块作为示例：

```json
{
  "content": {
    "modulesContent": {
      "$edgeAgent": {
        "properties.desired.modules.SimulatedTemperatureSensor": {
          "settings": {
            "image": "mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0",
              "createOptions": ""
          },
          "type": "docker",
          "status": "running",
          "restartPolicy": "always",
          "version": "1.0"
        }
      },
      "$edgeHub": {
        "properties.desired.routes.upstream": "FROM /messages/* INTO $upstream"
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

前面的示例显示了一个分层部署设置模块的 `properties.desired`。 如果此分层部署针对已应用相同模块的设备，则会覆盖任何现有的所需属性。 若要更新，而不是覆盖所需的属性，可以定义新的子节。 例如：

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

有关在分层部署中配置 module 孪生的详细信息，请参阅[分层部署](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以有多个标记，您可以使用这些标记来定义解决方案。 例如，如果管理有智能楼宇的校园，可将以下标记添加到设备：

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

使用[az iot edge deployment create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-create)命令创建部署：

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

使用与 `--layered` 标志相同的命令创建分层部署。

部署 create 命令采用以下参数：

* **--分层**-用于将部署标识为分层部署的可选标志。
* **--deployment-id** - 将在 IoT 中心创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。 必需的参数。
* **--content** - 部署清单 JSON 的文件路径。 必需的参数。
* **--hub-name** - 将在其中创建部署的 IoT 中心的名称。 此中心必须在当前订阅中。 通过 `az account set -s [subscription name]` 命令更改当前订阅。
* **--labels** - 添加用于跟踪部署的标签。 标签是描述部署的“名称, 值”对。 标签对名称和值采用 JSON 格式设置。 例如： `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此部署的目标。 此条件基于设备克隆标记或设备克隆报告的属性，并且应与表达式格式匹配。 例如，`tags.environment='test' and properties.reported.devicemodel='4000x'`。
* **--priority** - 一个正整数。 如果同一设备上确定的部署目标至少有两个，则会应用优先级数值最高的部署。
* **--指标**-创建查询 edgeHub 报告属性以跟踪部署状态的指标。 指标采用 JSON 输入或 filepath。 例如，`'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'` 。

## <a name="monitor-a-deployment"></a>监视部署

使用[az iot edge deployment show](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show)命令显示单个部署的详细信息：

```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
```

Deployment show 命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。 必需的参数。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查部署。 "**指标**" 属性列出每个中心计算的每个指标的计数：

* **targetedCount** - 一个系统指标，根据目标条件指定 IoT 中心的设备孪生数。
* **appliedCount** - 一个系统指标，指定已在 IoT 中心将部署内容应用到其模块孪生的设备数。
* **reportedSuccessfulCount** -一种设备指标，用于指定 IoT Edge 客户端运行时的部署中报告成功的 IoT Edge 设备数。
* **reportedFailedCount** -一种设备指标，用于指定 IoT Edge 客户端运行时部署报告失败中的 IoT Edge 设备数。

你可以使用[az iot edge deployment show-公制](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-show-metric)命令显示每个指标的设备 id 或对象的列表：

```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name]
```

Deployment show-公制命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--公制**-要查看其设备 id 列表的指标的名称，例如 `reportedFailedCount`。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。

你无法更新部署的内容，其中包括在部署清单中定义的模块和路由。 如果你想要更新部署的内容，则可以通过创建以较高优先级为目标相同设备的新部署来实现此目的。 您可以修改现有模块的某些属性，包括目标条件、标签、指标和优先级。

使用[az iot edge deployment update](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-update)命令更新部署：

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

部署更新命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新部署中的属性。 可以更新以下属性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 标签
  * priority
* **--add** -向部署添加新属性，包括目标条件或标签。
* **--remove** -删除现有的属性，包括目标条件或标签。

## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。

使用[az iot edge deployment delete](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment?view=azure-cli-latest#ext-azure-cli-iot-ext-az-iot-edge-deployment-delete)命令删除部署：

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

部署删除命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤

了解有关[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)的详细信息。
