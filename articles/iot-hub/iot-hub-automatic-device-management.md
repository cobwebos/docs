---
title: 使用 Azure IoT 中心进行大规模自动设备管理 | Microsoft Docs
description: 使用 Azure IoT 中心自动配置管理多个 IoT 设备和模块
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: robinsh
ms.openlocfilehash: 276f115f579fbd1ab077722b220a4a0c6c571850
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025061"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>使用 Azure 门户自动管理 IoT 设备和模块

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中心的自动设备管理功能可自动完成许多复杂且重复性的大型设备阵列管理任务。 使用自动设备管理，可以根据设备的属性将一组设备指定为目标、定义所需的配置，然后在设备进入管理范畴时让 IoT 中心更新这些设备。 此更新是使用自动设备配置或自动模块配置执行的。使用此项功能还能汇总完整度与合规性、处理合并与冲突，以及分阶段推出配置 。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自动设备管理的工作原理是使用所需属性更新一组设备孪生或模块孪生，并报告基于孪生报告属性的摘要。  其中引入了一个新类以及名为 Configuration 的 JSON 文档，其中包含三个组成部分：

* 目标条件定义要更新的设备孪生或模块孪生的范围。 目标条件在孪生标记和/或报告属性中指定为查询。

* 目标内容定义要在目标设备孪生或模块孪生中添加或更新的所需属性。 内容包括要更改的所需属性节的路径。

* **指标**定义各种配置状态（例如“成功”、“正在进行中”和“错误”）的摘要计数。   自定义指标指定为孪生报告属性中的查询。  系统指标是度量孪生更新状态的默认指标，例如，针对的孪生数，以及已成功更新的孪生数。

自动配置在配置创建不久后就首次运行，然后按五分钟间隔运行。 每次自动配置运行时，都会运行指标查询。

## <a name="implement-twins"></a>实现孪生

自动设备配置需要使用设备孪生来同步云与设备之间的状态。  有关详细信息，请参阅[了解并在 IoT 中心内使用设备孪生](iot-hub-devguide-device-twins.md)。

自动模块配置需要使用模块孪生来同步云与模块之间的状态。 有关详细信息，请参阅[了解并在 IoT 中心内使用模块孪生](iot-hub-devguide-module-twins.md)。

## <a name="use-tags-to-target-twins"></a>使用标记设置孪生目标

创建配置之前，必须指定想要影响的设备或模块。 Azure IoT 中心使用设备孪生中的标记来标识设备，使用模块孪生中的标记来标识模块。 每个设备或模块均可以拥有多个标记，你可以用适合解决方案的任何方式定义它们。 例如，在管理不同位置中的设备时，请将以下标记添加到设备孪生：

```json
"tags": {
    "location": {
        "state": "Washington",
        "city": "Tacoma"
    }
},
```

## <a name="create-a-configuration"></a>创建配置

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。

3. 选择“添加设备配置”或“添加模块配置”。

   ![添加设备配置或模块配置](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

创建配置需要执行五个步骤。 下列各节将引导完成每个步骤。 

### <a name="name-and-label"></a>名称和标签

1. 为配置指定不超过 128 个小写字母的唯一名称。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。

2. 添加标签以帮助跟踪配置。 标签是描述配置的 <名称, 值> 对。  例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

3. 选择“下一步”转到下一步。 

### <a name="specify-settings"></a>指定设置

本部分定义要在目标设备或模块孪生中设置的内容。 需要为每组设置提供两项输入。 第一项输入是孪生路径，即，要设置的孪生所需属性中 JSON 节的路径。  第二项输入是要在该节中插入的 JSON 内容。 

例如，可以将孪生路径设置为 `properties.desired.chiller-water`，然后提供以下 JSON 内容： 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![设置孪生路径和内容](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


此外，可以通过指定完整孪生路径并提供值（不带括号），来配置各项设置。 例如，对于孪生路径 `properties.desired.chiller-water.temperature`，请将内容设置为 `66`。 然后，为压力属性创建一个新的孪生设置。 

如果两个或更多个配置以同一孪生路径为目标，将会应用最高优先级配置中的内容（优先级在步骤 4 中定义）。

如果想要删除某个现有属性，请将属性值指定为 `null`。

可以通过选择“添加设备孪生设置”或“添加模块孪生设置”来添加其他设置 。

### <a name="specify-metrics-optional"></a>指定指标（可选）

指标提供应用配置内容后设备或模块可能报告回来的各种状态的摘要计数。 例如，可以针对挂起的设置更改、错误和成功的设置更改各创建一个指标。

每个配置最多可以有五个自定义指标。 

1. 在“指标名称”中输入名称。

2. 在“指标条件”中输入查询。  该查询基于设备孪生报告的属性。  指标表示查询返回的行数。

例如：

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

可以包含一个表示应用了配置的子句，例如： 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

如果要生成用于报告已配置模块的指标，请从 `devices.modules` 中选择 `moduleId`。 例如：

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>目标设备

使用孪生中的标记属性将应接收此配置的特定设备或模块指定为目标。 还可以将孪生报告的属性指定为目标。

自动设备配置只能以设备孪生标记为目标，自动模块配置只能以模块孪生标记为目标。 

由于多个配置可能以同一设备或模块为目标，因此，每个配置都需要一个优先级编号。 如果存在冲突，则优先级最高的配置将会胜出。 

1. 为配置的“优先级”输入一个正整数。 最大数值被视为最高优先级。 如果两个配置的优先级编号相同，则最近创建的配置胜出。 

2. 输入“目标条件”，确定此配置的目标设备或模块。 该条件基于孪生标记或孪生报告的属性，应与表达式格式相匹配。 

   对于自动设备配置，可以仅指定要用作目标的标记或报告的属性。 例如，`tags.environment='test'` 或 `properties.reported.chillerProperties.model='4000x'`。 可以指定 `*` 来定目标到所有设备。 
   
   对于自动模块配置，请使用查询来指定注册到 IoT 中心的模块中的标记或报告的属性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。 不能使用通配符来指定以所有模块为目标。 

3. 选择“下一步”，进入到最后一步。

### <a name="review-configuration"></a>查看配置

查看配置信息，然后选择“提交”。

## <a name="monitor-a-configuration"></a>监视配置

若要查看配置的详细信息并监视运行它的设备，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。

3. 检查配置列表。 对于每个配置，可查看以下详细信息：

   * **ID** - 配置的名称。

   * 目标条件 - 用于定义目标设备或模块的查询。

   * **优先级** - 分配给配置的优先级编号。

   * **创建时间** - 开始创建配置的时间戳。 当两个配置具有相同的优先级时，此时间戳用于消除它们的关系。 

   * **系统指标** - 指标由 IoT 中心计算，开发人员无法对其进行自定义。 “目标数”指定与目标条件匹配的设备孪生数。 “应用”指定已被配置修改的设备孪生数，这可能包括当某个不同的更高优先级配置同时做出更改时，所完成的部分修改。 

   * 自定义指标 - 开发人员针对孪生报告属性指定为查询的指标。  对于每个配置，最多可以定义五个自定义指标。 
   
4. 选择要监视的配置。  

5. 检查配置详细信息。 可以使用选项卡查看有关收到配置的设备的详细信息。

   * 目标条件 - 与目标条件匹配的设备或模块。 

   * **指标** - 系统指标和自定义指标的列表。  在下拉列表中选择指标，然后选择“查看设备”或“查看模块”，即可查看计入每个指标的设备或模块列表。 

   * 设备孪生设置或模块孪生设置 - 由配置设置的孪生设置。 

   * **配置标签** - 用于描述配置的键值对。  标签不对功能造成影响。 

## <a name="modify-a-configuration"></a>修改配置

修改配置时，更改会立即复制到所有目标设备或模块。 

如果更新目标条件，将发生以下更新：

* 如果孪生不满足旧目标条件，但满足新目标条件，并且此配置是该孪生的最高优先级，则会应用此配置。 

* 如果当前运行此配置的孪生不再满足目标条件，则会删除配置中的设置，并且孪生将被下一个最高优先级配置修改。 

* 如果当前正在运行此配置的孪生不再满足目标条件，且不满足其他任何配置的目标条件，则会删除配置中的设置，并且不会在孪生中进行其他任何更改。 

若要修改配置，请执行以下步骤： 

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。 

3. 选择要修改的配置。 

4. 更新以下字段： 

   * 目标条件 
   * 标签 
   * 优先度 
   * 指标

4. 选择“保存”。

5. 遵循 [监视配置](#monitor-a-configuration)中的步骤来观察更改的实施。 

## <a name="delete-a-configuration"></a>删除配置

删除某个配置时，所有设备孪生将会采用下一个最高优先级的配置。 如果设备孪生不满足其他任何配置的目标条件，则不会应用其他任何设置。 

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。 

3. 使用复选框选择想要删除的配置。 

4. 选择“删除”。

5. 系统会提示确认。

## <a name="next-steps"></a>后续步骤

本文已介绍如何大规模配置和监视 IoT 设备。 若要了解有关如何管理 Azure IoT 中心的详细信息，请参阅以下链接：

* [批量管理 IoT 中心设备标识](iot-hub-bulk-identity-mgmt.md)
* [IoT 中心指标](iot-hub-metrics.md)
* [操作监视](iot-hub-operations-monitoring.md)

若要进一步探索 IoT 中心的功能，请参阅：

* [IoT 中心开发人员指南](iot-hub-devguide.md)
* [使用 Azure IoT Edge 将 AI 部署到边缘设备](../iot-edge/tutorial-simulate-device-linux.md)

若要了解如何使用 IoT 中心设备预配服务启用零接触实时预配，请参阅： 

* [Azure IoT 中心设备预配服务](/azure/iot-dps)
