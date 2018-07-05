---
title: 使用 Azure IoT 中心大规模配置和监视 IoT 设备 (CLI) | Microsoft Docs
description: 使用 Azure IoT 中心自动设备配置向多个设备分配配置
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 04/13/2018
ms.author: chrisgre
ms.openlocfilehash: c12a07aabdecb070cfa99f8851f907499599a1fc
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/27/2018
ms.locfileid: "37034657"
---
# <a name="configure-and-monitor-iot-devices-at-scale-using-the-azure-cli"></a>使用 Azure CLI 大规模配置和监视 IoT 设备

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中心内的自动设备管理功能可将许多复杂且重复性的任务自动化，包括在大型设备阵列的整个生命周期内对其进行管理。 使用自动设备管理，可以根据设备的属性将一组设备指定为目标、定义所需的配置，并在设备进入管理范畴时让 IoT 中心更新这些设备。  此任务是使用自动设备配置执行的。使用此项功能还能汇总完整度与合规性、处理合并与冲突，以及分阶段推出配置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自动设备配置的工作原理是使用所需属性更新一组设备孪生，并根据设备孪生报告的属性报告摘要。  其中引入了一个新类以及名为 _Configuration_ 的 JSON 文档，其中包含三个组成部分：

* **目标条件**定义要更新的设备孪生的范围。 目标条件在设备孪生标记和/或报告属性中指定为查询。

* **目标内容**定义要在目标设备孪生中添加或更新的所需属性。 内容包括要更改的所需属性节的路径。

* **指标**定义各种配置状态（例如“成功”、“正在进行中”和“错误”）的摘要计数。 自定义指标指定为设备孪生报告的属性中的查询。  系统指标是度量孪生更新状态的默认指标，例如，针对的设备孪生数，以及已成功更新的孪生数。 

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 环境中的 [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 2.0 版本必须至少是 2.0.24 或更高。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 2.0 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="implement-device-twins-to-configure-devices"></a>实施设备孪生以配置设备

自动设备配置需要使用设备孪生来同步云与设备之间的状态。  有关使用设备孪生的指导，请参阅[了解并在 IoT 中心内使用设备孪生][lnk-device-twin]。

## <a name="identify-devices-using-tags"></a>使用标记标识设备

创建配置之前，必须指定想要影响的设备。 Azure IoT 中心使用设备孪生中的标记来标识设备。 每个设备均可以拥有多个标记，你可以用适合解决方案的任何方式定义它们。 例如，在管理不同位置中的设备时，可将以下标记添加到设备孪生：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```
## <a name="define-the-target-content-and-metrics"></a>定义目标内容和指标

目标内容和指标查询是作为 JSON 文档指定的，这些文档描述了要设置的设备孪生所需属性和要度量的报告属性。  若要使用 Azure CLI 2.0 创建自动设备配置，请将目标内容和指标在本地保存为 .txt 文件。 在下一部分中通过运行命令将配置应用到设备时，会用到这个文件路径。 

下面是一个基本的目标内容示例：

```json
{
  "content": {
    "deviceContent": {
      "properties.desired.chillerWaterSettings": {
        "temperature": 38,
        "pressure": 78
      }
    }
}
```

下面是指标查询的示例：

```json
{
  "queries": {
    "Compliant": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillersettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

## <a name="create-a-configuration"></a>创建配置

通过创建包含目标内容和指标的配置来配置目标设备。 

可使用以下命令来创建配置：

   ```cli
   az iot hub configuration create --config-id [configuration id] --labels [labels] --content [file path] --hub-name [hub name] --target-condition [target query] --priority [int] --metrics [metric queries]
   ```

* **--config-id** - 将在 IoT 中心内创建的配置的名称。 为配置指定不超过 128 个小写字母的唯一名称。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。
* **--labels** - 添加标签以帮助跟踪配置。 标签是描述部署的“名称, 值”对。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`
* **--content** - 要设置为孪生所需属性的目标内容的内联 JSON 或文件路径。 
* **--hub-name** - 将在其中创建配置的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--target-condition** - 输入一个目标条件，用于确定哪些设备会成为此配置的目标。 条件基于设备孪生标记或设备孪生所需属性，并且应当与表达式格式匹配。 例如 `tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。 
* **--priority** - 一个正整数。 如果在同一台设备上设定了两个或更多配置作为目标，将会应用优先级数值最高的配置。
* **--metrics** - 指标查询的文件路径。 指标提供应用配置内容后设备可能报告的各种状态的摘要计数。 例如，可以针对挂起的设置更改、错误和成功的设置更改各创建一个指标。 

## <a name="monitor-a-configuration"></a>监视配置

使用以下命令显示配置的内容：

   ```cli
az iot hub configuration show --config-id [configuration id] --hub-name [hub name]
   ```
* **--config-id** - 存在于 IoT 中心内的配置的名称。
* **--hub-name** - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查配置。 **metrics** 属性列出由每个中心评估的每个指标的计数：
* **targetedCount** - 一个系统指标，它指定 IoT 中心内与目标条件匹配的设备孪生数。
* **appliedCount** - 一个系统指标，它指定已应用了目标内容的设备数。
* **你的自定义指标** - 你定义的任何指标都会被视为用户指标。

可以使用以下命令，针对每个指标显示设备 ID 或对象的列表：

   ```cli
az iot hub configuration show-metric --config-id [configuration id] --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
   ```

* **--config-id** - 存在于 IoT 中心内的部署的名称。
* **--metric-id** - 需要查看设备 ID 列表时所对应指标的名称，例如 `appliedCount`
* **--hub-name** - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--metric-type** - 指标类型可以是 `system` 或 `user`。  系统指标为 `targetedCount` 和 `appliedCount`。 所有其他指标都是用户指标。

## <a name="modify-a-configuration"></a>修改配置

修改配置时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：
* 如果设备孪生不满足旧目标条件，但满足新目标条件，并且此配置是该设备孪生的最高优先级，则会将此配置应用到该设备孪生。 
* 如果设备孪生不再满足目标条件，则会删除配置中的设置，并且设备孪生将被下一个最高优先级配置修改。 
* 如果当前正在运行此配置的设备孪生不再满足目标条件，且不满足其他任何配置的目标条件，则会删除配置中的设置，并且不会在孪生中进行其他任何更改。 

可使用以下命令来更新配置：

   ```cli
az iot hub configuration update --config-id [configuration id] --hub-name [hub name] --set [property1.property2='value']
   ```
* **--config-id** - 存在于 IoT 中心内的配置的名称。
* **--hub-name** - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅
* **--set** - 更新配置中的属性。 可以更新以下属性：
    * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`
    * 标签 
    * priority

## <a name="delete-a-configuration"></a>删除配置

删除某个配置时，所有设备孪生将会采用下一个最高优先级的配置。 如果设备孪生不满足其他任何配置的目标条件，则不会应用其他任何设置。 

可使用以下命令来删除配置：

   ```cli
az iot hub configuration delete --config-id [configuration id] --hub-name [hub name] 
   ```
* **--config-id** - 存在于 IoT 中心内的配置的名称。
* **--hub-name** - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

## <a name="next-steps"></a>后续步骤
本文已介绍如何大规模配置和监视 IoT 设备。 若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 中心设备标识][lnk-bulkIDs]
* [IoT 中心度量值][lnk-metrics]
* [操作监视][lnk-monitor]

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南][lnk-devguide]
* [使用 Azure IoT Edge 将 AI 部署到边缘设备][lnk-iotedge]

若要了解如何使用 IoT 中心设备预配服务启用零接触实时预配，请参阅： 

* [Azure IoT 中心设备预配服务][lnk-dps]

[lnk-device-twin]: iot-hub-devguide-device-twins.md
[lnk-bulkIDs]: iot-hub-bulk-identity-mgmt.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
[lnk-portal]: https://portal.azure.com
