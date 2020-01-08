---
title: 通过 Azure IoT 中心进行大规模自动设备管理（CLI） |Microsoft Docs
description: 使用 Azure IoT 中心自动配置管理多个 IoT 设备或模块
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 9a7e2d9874f049000dadcb3e46cccb2202b53698
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75429285"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-cli"></a>使用 Azure CLI 进行自动 IoT 设备和模块管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中心的自动设备管理自动执行许多管理大型设备群的重复性任务。 使用自动设备管理，你可以基于一组设备的属性，定义所需的配置，然后让 IoT 中心在设备进入范围时更新设备。 此更新通过_自动设备配置_或_自动模块配置_完成，可让你汇总完成和符合性，处理合并和冲突，并使用分阶段的方式推出配置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自动设备管理的工作方式是：使用所需的属性更新一组设备孪生或模块孪生，并报告基于克隆报告属性的摘要。  它引入了一个名为*配置*的新类和 JSON 文档，其中包含三个部分：

* **目标条件**定义要更新的设备孪生或 module 孪生的作用域。 目标条件在设备孪生标记和/或报告属性中指定为查询。

* **目标内容**定义要在目标设备孪生或模块孪生中添加或更新的所需属性。 内容包括要更改的所需属性节的路径。

* **指标**定义各种配置状态（例如“成功”、“正在进行中”和“错误”）的摘要计数。 自定义指标被指定为对克隆报告属性的查询。  系统度量值是度量对等更新状态的默认指标，例如目标的孪生数以及已成功更新的孪生数。

自动配置在创建配置后的首次运行，然后在5分钟间隔内运行。 每次运行自动配置时，都将运行指标查询。

## <a name="cli-prerequisites"></a>CLI 先决条件

* Azure 订阅中的 [IoT 中心](../iot-hub/iot-hub-create-using-cli.md)。 
* 环境中的 [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli)。 Azure CLI 版本必须至少是 2.0.24 或更高版本。 请使用 `az –-version` 验证版本。 此版本支持 az 扩展命令，并引入了 Knack 命令框架。 
* [适用于 Azure CLI 的 IoT 扩展](https://github.com/Azure/azure-iot-cli-extension)。

## <a name="implement-twins"></a>实现孪生

自动设备配置需要使用设备孪生来同步云与设备之间的状态。  有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)。

自动模块配置要求使用 module 孪生来同步云和模块之间的状态。 有关详细信息，请参阅[了解和使用 IoT 中心的模块孪生](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用标记定位孪生

在创建配置之前，必须指定要影响的设备或模块。 Azure IoT 中心标识设备并使用设备克隆中的标记，并使用模块克隆中的标记识别模块。 每个设备或模块可以有多个标记，您可以对您的解决方案进行适当的定义。 例如，如果在不同位置管理设备，请将以下标记添加到设备克隆：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="define-the-target-content-and-metrics"></a>定义目标内容和指标

目标内容和指标查询被指定为 JSON 文档，用于描述要设置和报告要测量的属性的设备克隆或模块克隆所需属性。  若要使用 Azure CLI 创建自动配置，请将目标内容和度量值作为 .txt 文件本地保存。 当你运行命令将配置应用到设备时，请使用后面部分中的文件路径。

下面是自动设备配置的基本目标内容示例：

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

自动模块配置的行为与此类似，但目标 `moduleContent` 而不是 `deviceContent`。

```json
{
  "content": {
    "moduleContent": {
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
    "Compliant": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'",
    "Error": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='error'",
    "Pending": "select deviceId from devices where configurations.[[chillerdevicesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='pending'"
  }
}
```

模块的指标查询与设备的查询也类似，但你从 `devices.modules`中选择 `moduleId`。 例如： 

```json
{
  "queries": {
    "Compliant": "select deviceId, moduleId from devices.module where configurations.[[chillermodulesettingswashington]].status = 'Applied' AND properties.reported.chillerWaterSettings.status='current'"
  }
}
```

## <a name="create-a-configuration"></a>创建配置

通过创建包含目标内容和指标的配置来配置目标设备。 

可使用以下命令来创建配置：

```cli
   az iot hub configuration create --config-id [configuration id] \
     --labels [labels] --content [file path] --hub-name [hub name] \
     --target-condition [target query] --priority [int] \
     --metrics [metric queries]
```

* --config-id - 将在 IoT 中心内创建的配置的名称。 为配置指定不超过 128 个小写字母的唯一名称。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。

* --labels - 添加标签以帮助跟踪配置。 标签是描述部署的“名称, 值”对。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`

* --content - 要设置为孪生所需属性的目标内容的内联 JSON 或文件路径。 

* --hub-name - 将在其中创建配置的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

* --**目标-条件**-输入目标条件，以确定将以此配置为目标的设备或模块。 对于自动设备配置，条件基于设备克隆标记或设备克隆所需的属性，并且应与表达式格式匹配。 例如，`tags.environment='test'` 或 `properties.desired.devicemodel='4000x'`。 对于自动模块配置，该条件基于模块克隆标记或模块克隆所需属性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。

* --priority - 一个正整数。 如果两个或多个配置针对同一设备或模块，则会应用具有最高优先级的数值的配置。

* --metrics - 指标查询的文件路径。 度量值提供了应用配置内容后设备或模块可能会报告回的各种状态的汇总计数。 例如，可以针对挂起的设置更改、错误和成功的设置更改各创建一个指标。 

## <a name="monitor-a-configuration"></a>监视配置

使用以下命令显示配置的内容：

```cli
az iot hub configuration show --config-id [configuration id] \
  --hub-name [hub name]
```

* --config-id - 存在于 IoT 中心内的配置的名称。

* --hub-name - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅

在命令窗口中检查配置。 "**指标**" 属性列出每个中心计算的每个指标的计数：

* **targetedCount** -一种系统指标，指定与目标条件匹配的 IoT 中心中的设备孪生或模块孪生的数目。

* **appliedCount** -系统指标指定应用了目标内容的设备或模块的数量。

* **自定义指标**-定义的任何指标都是用户指标。

可以使用以下命令显示每个指标的设备 Id、模块 Id 或对象的列表：

```cli
az iot hub configuration show-metric --config-id [configuration id] \
   --metric-id [metric id] --hub-name [hub name] --metric-type [type] 
```

* --config-id - 存在于 IoT 中心内的部署的名称。

* --**指标-id** -要查看其设备 id 或模块 id 列表的指标的名称，例如 `appliedCount`。

* --hub-name - 部署所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅。

* --metric-type - 指标类型可以是 `system` 或 `user`。  系统指标为 `targetedCount` 和 `appliedCount`。 所有其他指标都是用户指标。

## <a name="modify-a-configuration"></a>修改配置

修改配置时，更改会立即复制到所有目标设备。 

如果更新目标条件，将发生以下更新：

* 如果克隆不满足旧的目标条件，但满足新的目标条件，并且此配置是该克隆的最高优先级，则会应用此配置。 

* 如果当前正在运行此配置的克隆不再满足目标条件，则将删除该配置中的设置，并将通过下一个优先级最高的配置修改克隆。 

* 如果当前运行此配置的克隆不再满足目标条件，并且不满足任何其他配置的目标条件，则将删除配置中的设置，并且不会对克隆进行其他更改。 

可使用以下命令来更新配置：

```cli
az iot hub configuration update --config-id [configuration id] \
   --hub-name [hub name] --set [property1.property2='value']
```

* --config-id - 存在于 IoT 中心内的配置的名称。

* --hub-name - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅。

* --set - 更新配置中的属性。 可以更新以下属性：

    * targetCondition - 例如 `targetCondition=tags.location.state='Oregon'`

    * 标签 

    * priority

## <a name="delete-a-configuration"></a>删除配置

删除配置时，任何设备孪生或模块孪生都采用其下一个优先级最高的配置。 如果孪生不满足任何其他配置的目标条件，则不会应用任何其他设置。 

可使用以下命令来删除配置：

```cli
az iot hub configuration delete --config-id [configuration id] \
   --hub-name [hub name] 
```
* --config-id - 存在于 IoT 中心内的配置的名称。

* --hub-name - 配置所在的 IoT 中心的名称。 此中心必须在当前订阅中。 使用 `az account set -s [subscription name]` 命令切换到所需订阅。

## <a name="next-steps"></a>后续步骤

本文介绍了如何大规模配置和监视 IoT 设备。 若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 中心设备标识](iot-hub-bulk-identity-mgmt.md)
* [IoT 中心指标](iot-hub-metrics.md)
* [操作监视](iot-hub-operations-monitoring.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)

若要了解如何使用 IoT 中心设备预配服务启用零接触实时预配，请参阅： 

* [Azure IoT 中心设备预配服务](/azure/iot-dps)
