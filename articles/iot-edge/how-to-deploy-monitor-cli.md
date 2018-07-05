---
title: 部署并监视 Azure IoT Edge 的模块 (CLI) | Microsoft Docs
description: 管理在边缘设备上运行的模块
keywords: ''
author: kgremban
manager: timlt
ms.author: kgremban
ms.date: 06/07/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 705f7bfa62154bff62b2357bd8f33c01e97404d1
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034383"
---
# <a name="deploy-and-monitor-iot-edge-modules-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模部署并监视 IoT Edge 模块

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-edge-how-to-deploy-monitor-selector.md)]

使用 Azure IoT Edge 可以将分析移至边缘并提供云接口，以便能够无需实际访问每个设备即可管理和监控 IoT Edge 设备。 随着物联网解决方案日益庞大和复杂，远程管理设备功能变得越来越重要。 Azure IoT Edge 旨在支持业务目标，不管添加的设备数量。

可以管理单独的设备并一次性将模块部署到设备中。 但是，如果想大规模更改设备，则可以创建一个 IoT Edge 自动部署，该部署是 IoT 中心中自动设备管理的一部分。 部署是允许同时将多个模块部署到多个设备、跟踪模块的状态和运行状况以及在必要时做出更改的动态过程。 

在本文中，将安装 Azure CLI 2.0 和 IoT 扩展。 然后，了解如何使用可用的 CLI 命令将模块部署到一组 IoT Edge 设备并监视进度。

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 已安装 IoT Edge 运行时的 [IoT Edge 设备](how-to-register-device-cli.md)。
* 环境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 2.0 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="configure-a-deployment-manifest"></a>配置部署清单

部署清单是一个 JSON 文档，其中描述了要部署的模块、数据在模块间的流动方式以及模块孪生的所需属性。 若要详细了解部署清单的工作原理及创建方式，请参阅[了解如何使用、配置和重用 IoT Edge 模块](module-composition.md)。

若要使用 Azure CLI 2.0 来部署模块，请将部署清单在本地另存为 .txt 文件。 在下一部分通过运行命令将配置应用到设备时，会用到这个文件路径。 

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
               },
               "systemModules": {
                 "edgeAgent": {
                   "type": "docker",
                   "settings": {
                     "image": "microsoft/azureiotedge-agent:1.0-preview",
                     "createOptions": "{}"
                   }
                 },
                 "edgeHub": {
                   "type": "docker",
                   "status": "running",
                   "restartPolicy": "always",
                   "settings": {
                     "image": "microsoft/azureiotedge-hub:1.0-preview",
                     "createOptions": "{}"
                   }
                 }
               },
               "modules": {
                 "tempSensor": {
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
           "tempSensor": {
             "properties.desired": {}
           }
         }
       }
   }
   ```


## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建部署之前，必须能够指定想要影响的设备。 Azure IoT Edge 标识使用设备孪生中的标记标识设备。 每个设备均可以拥有多个标记，你可以用适合解决方案的任何方式定义它们。 例如，如果管理有智能楼宇的校园，可将以下标记添加到设备：

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

有关设备孪生和标记的详细信息，请参阅[了解和使用 IoT 中心的设备孪生][lnk-device-twin]。

## <a name="create-a-deployment"></a>创建部署

请创建一个包含部署清单和其他参数的部署，以便将模块部署到目标设备。 

使用以下命令创建部署：

   ```cli
   az iot edge deployment create --deployment-id [deployment id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int]
   ```

* **--deployment-id** - 将在 IoT 中心创建的部署的名称。 为部署提供唯一名称（最多包含 128 个小写字母）。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
* **--labels** - 添加用于跟踪部署的标签。 标签是描述部署的“名称, 值”对。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`
* **--content** - 部署清单 JSON 的文件路径。 
* **--hub-name** - 将在其中创建部署的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此部署的目标。 此条件依据的是设备孪生标记或设备孪生所需属性，应与表达式格式保持一致。 例如 `tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。 
* **--priority** - 一个正整数。 如果同一设备上确定的部署目标至少有两个，则会应用优先级数值最高的部署。

## <a name="monitor-a-deployment"></a>监视部署

使用以下命令显示部署的内容：

   ```cli
az iot edge deployment show --deployment-id [deployment id] --hub-name [hub name]
   ```
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查部署。 **metrics** 属性列出由每个中心评估的每个指标的计数：
* **targetedCount** - 一个系统指标，根据目标条件指定 IoT 中心的设备孪生数。
* **appliedCount** - 一个系统指标，指定已在 IoT 中心将部署内容应用到其模块孪生的设备数。
* **reportedSuccessfulCount** - 一个设备指标，指定已通过 IoT Edge 客户端运行时报告成功的部署中的 Edge 设备数。
* **reportedFailedCount** - 一个设备指标，指定已通过 IoT Edge 客户端运行时报告失败的部署中的 Edge 设备数。

可以使用以下命令，针对每个指标显示设备 ID 或对象的列表：

   ```cli
az iot edge deployment show-metric --deployment-id [deployment id] --metric-id [metric id] --hub-name [hub name] 
   ```

* **--deployment-id** - IoT 中心存在的部署的名称。
* **--metric-id** - 需要查看设备 ID 列表时所对应指标的名称，例如 `reportedFailedCount`
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="modify-a-deployment"></a>修改部署

修改部署时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：
* 如果设备虽然不满足旧的目标条件，但满足新的目标条件，且此部署是此设备的最高优先级，则此部署将应用到设备。 
* 如果当前运行此部署的设备不再满足目标条件，则它将卸载此部署并采用下一个最高优先级部署。 
* 如果当前运行此部署的设备不再满足目标条件且不满足任何其他部署的目标条件，则此设备上不会发生任何更改。 设备在当前状态下继续运行当前模块，但不再作为此部署的一部分被托管。 一旦它满足任何其他部署的目标条件，将卸载此部署并采用新的部署。 

使用以下命令更新部署：

   ```cli
az iot edge deployment update --deployment-id [deployment id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新部署中的属性。 可以更新以下属性：
    * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
    * 标签 
    * priority


## <a name="delete-a-deployment"></a>删除部署

删除部署时，任何设备都将采用下一个最高优先级部署。 如果设备不满足任何其他部署的目标条件，则删除该部署时不会删除模块。 

使用以下命令删除部署：

   ```cli
az iot edge deployment delete --deployment-id [deployment id] --hub-name [hub name] 
   ```
* **--deployment-id** - IoT 中心存在的部署的名称。
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤

了解有关[将模块部署到边缘设备][lnk-deployments]的详细信息。

<!-- Images -->
[1]: ./media/how-to-deploy-monitor/iot-edge-deployments.png

<!-- Links -->
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-portal]: https://portal.azure.com
[lnk-docker-create]: https://docs.docker.com/engine/reference/commandline/create/
[lnk-deployments]: module-deployment-monitoring.md

<!-- Anchor links -->
[anchor-monitor]: #monitor-a-deployment
