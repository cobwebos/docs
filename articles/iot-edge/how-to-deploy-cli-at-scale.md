---
title: 使用 Azure CLI 大规模部署模块 - Azure IoT Edge
description: 使用 Azure CLI 的 IoT 扩展为 IoT Edge 设备组创建自动部署
keywords: ''
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
ms.custom: devx-track-azurecli
services: iot-edge
ms.openlocfilehash: 0a73651b11c9ca6f7cb34deb755543c3b5a6d710
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/14/2020
ms.locfileid: "92042977"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模部署并监视 IoT Edge 模块

使用 Azure 命令行接口创建 IoT Edge 自动部署，同时为多个设备管理正在进行的部署。 IoT Edge 的自动部署是 IoT 中心[自动设备管理](../iot-hub/iot-hub-automatic-device-management.md)功能的一部分。 部署是允许将多个模块部署到多个设备、跟踪模块的状态和运行状况以及在必要时做出更改的动态过程。

有关详细信息，请参阅[了解单设备 IoT Edge 自动部署或大规模 IoT Edge 自动部署](module-deployment-monitoring.md)。

在本文中，将安装 Azure CLI 和 IoT 扩展。 然后，了解如何使用可用的 CLI 命令将模块部署到一组 IoT Edge 设备并监视进度。

## <a name="prerequisites"></a>先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。
* 一个或多个 IoT Edge 设备。

  如果未设置 IoT Edge 设备，则可以在 Azure 虚拟机中创建一个。 按照其中一篇快速入门文章中的步骤 [创建虚拟 Linux 设备](quickstart-linux.md) ，或 [创建虚拟 Windows 设备](quickstart.md)。

* 环境中的 [Azure CLI](/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.70 或更高版本。 请使用 `az --version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 有关详细信息，请参阅[了解如何在 IoT Edge 中部署模块和建立路由](module-composition.md)。

若要使用 Azure CLI 来部署模块，请将部署清单在本地另存为 .txt 文件。 在下一部分通过运行命令将配置应用到设备时，会用到这个文件路径。

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

## <a name="layered-deployment"></a>分层部署

分层部署是一种自动部署类型，可以彼此重叠。 有关分层部署的详细信息，请参阅[了解单设备或大规模 IoT Edge 自动部署](module-deployment-monitoring.md)。

使用 Azure CLI 创建和管理分层部署，就像任何自动部署一样（只有少量差异）。 创建分层部署后，与任何部署一样，相同 Azure CLI 适用于分层部署。 若要创建分层部署，请将 `--layered` 标志添加到 create 命令。

第二个区别在于部署清单的构造。 标准自动部署除了包含任何用户模块外，还必须包含系统运行时模块，而分层部署只能包含用户模块。 相反，分层部署还需要在设备上进行标准自动部署，以便提供每个 IoT Edge 设备的必需组件（如系统运行时模块）。

下面是具有一个模块的基本分层部署清单示例：

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

前面的示例显示了模块的分层部署设置 `properties.desired`。 如果此分层部署针对已应用相同模块的设备，则会覆盖任何现有的所需属性。 若要更新，而不是覆盖所需的属性，可以定义新的子节。 例如：

```json
"SimulatedTEmperatureSensor": {
  "properties.desired.layeredProperties": {
    "SendData": true,
    "SendInterval": 5
  }
}
```

有关在分层部署中配置模块孪生的详细信息，请参阅[分层部署](module-deployment-monitoring.md#layered-deployment)

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备都可以具有多个标记，你可以采用适合你的解决方案的任何方式定义这些标记。 例如，如果管理有智能楼宇的校园，可将以下标记添加到设备：

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

使用 [az iot edge deployment create](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-create) 命令创建部署：

```cli
az iot edge deployment create --deployment-id [deployment id] --hub-name [hub name] --content [file path] --labels "[labels]" --target-condition "[target query]" --priority [int]
```

使用与 `--layered` 标志相同的命令创建分层部署。

deployment create 命令采用以下参数：

* --layered - 用于将部署标识为分层部署的可选标志。
* **--deployment-id** - 将在 IoT 中心创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。 必需参数。
* **--content** - 部署清单 JSON 的文件路径。 必需参数。
* **--hub-name** - 将在其中创建部署的 IoT 中心的名称。 此中心必须在当前订阅中。 通过 `az account set -s [subscription name]` 命令更改当前订阅。
* **--labels** - 添加用于跟踪部署的标签。 标签是描述部署的“名称, 值”对。 标签对名称和值采用 JSON 格式设置。 例如： `{"HostPlatform":"Linux", "Version:"3.0.1"}`
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此部署的目标。 该条件基于设备孪生标记或设备孪生报告的属性，应与表达式格式相匹配。 例如，`tags.environment='test' and properties.reported.devicemodel='4000x'` 。
* **--priority** - 一个正整数。 如果同一设备上确定的部署目标至少有两个，则会应用优先级数值最高的部署。
* --metrics - 创建查询 edgeHub 报告的属性以跟踪部署状态的指标。 指标采用 JSON 输入或 filepath。 例如，`'{"queries": {"mymetric": "SELECT deviceId FROM devices WHERE properties.reported.lastDesiredStatus.code = 200"}}'`。

若要使用 Azure CLI 监视部署，请参阅[监视 IoT Edge 部署](how-to-monitor-iot-edge-deployments.md#monitor-a-deployment-with-azure-cli)。

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。

如果更新目标条件，将发生以下更新：

* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。

你无法更新部署的内容，其中包括在部署清单中定义的模块和路由。 如果你想要更新部署的内容，则可以通过创建针对具有较高优先级的相同设备的新部署来实现此目的。 你可以修改现有模块的某些属性，包括目标条件、标签、指标和优先级。

使用 [az iot edge deployment update](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-update) 命令更新部署：

```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
```

deployment update 命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新部署中的属性。 可以更新以下属性：
  * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
  * 标签
  * priority
* --add - 向部署中添加新属性，包括目标条件或标签。
* --remove - 删除现有属性，包括目标条件或标签。

## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。

使用 [az iot edge deployment delete](/cli/azure/ext/azure-iot/iot/edge/deployment#ext-azure-iot-az-iot-edge-deployment-delete) 命令删除部署：

```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name]
```

deployment delete 命令采用以下参数：

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤

详细了解[将模块部署到 IoT Edge 设备](module-deployment-monitoring.md)。