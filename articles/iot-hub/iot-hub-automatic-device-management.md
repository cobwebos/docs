---
title: 通过 Azure IoT 中心进行大规模自动设备管理 |Microsoft Docs
description: 使用 Azure IoT 中心自动配置管理多个 IoT 设备和模块
author: ChrisGMsft
manager: bruz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 12/13/2019
ms.author: chrisgre
ms.openlocfilehash: 75c6b7d89e7ae540e7428afde127281aa3f15fc6
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79271299"
---
# <a name="automatic-iot-device-and-module-management-using-the-azure-portal"></a>使用 Azure 门户进行自动 IoT 设备和模块管理

[!INCLUDE [iot-edge-how-to-deploy-monitor-selector](../../includes/iot-hub-auto-device-config-selector.md)]

Azure IoT 中心的自动设备管理自动执行许多管理大型设备群的重复性任务。 使用自动设备管理，你可以基于一组设备的属性，定义所需的配置，然后让 IoT 中心在设备进入范围时更新设备。 此更新通过_自动设备配置_或_自动模块配置_完成，可让你汇总完成和符合性，处理合并和冲突，并使用分阶段的方式推出配置。

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

自动设备管理的工作方式是：使用所需的属性更新一组设备孪生或模块孪生，并报告基于克隆报告属性的摘要。  它引入了一个名为*配置*的新类和 JSON 文档，其中包含三个部分：

* **目标条件**定义要更新的设备孪生或 module 孪生的作用域。 目标条件指定为对每个标记和/或报告的属性的查询。

* **目标内容**定义要在目标设备孪生或模块孪生中添加或更新的所需属性。 内容包括要更改的所需属性节的路径。

* **指标**定义各种配置状态（例如“成功”、“正在进行中”和“错误”）的摘要计数。 自定义指标被指定为对克隆报告属性的查询。  系统度量值是度量对等更新状态的默认指标，例如目标的孪生数以及已成功更新的孪生数。

自动配置在创建配置后的首次运行，然后在5分钟间隔内运行。 每次运行自动配置时，都将运行指标查询。

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

## <a name="create-a-configuration"></a>创建配置

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。

3. 选择 "**添加设备配置**" 或 "**添加模块配置**"。

   ![添加设备配置或模块配置](./media/iot-hub-automatic-device-management/create-automatic-configuration.png)

创建配置需要执行五个步骤。 下列各节将引导完成每个步骤。 

### <a name="name-and-label"></a>名称和标签

1. 为配置指定不超过 128 个小写字母的唯一名称。 避免空格和以下无效字符：`& ^ [ ] { } \ | " < > /`。

2. 添加标签以帮助跟踪配置。 标签是描述配置的 <名称, 值> 对。 例如，`HostPlatform, Linux` 或 `Version, 3.0.1`。

3. 选择 "**下一**步" 转到下一步。 

### <a name="specify-settings"></a>指定设置

本节定义要在目标设备或模块孪生中设置的内容。 需要为每组设置提供两项输入。 第一个是克隆路径，它是要设置的成对所需属性内 JSON 部分的路径。  第二项输入是要在该节中插入的 JSON 内容。 

例如，可以将 "克隆路径" 设置为 "`properties.desired.chiller-water`"，然后提供以下 JSON 内容： 

```json
{
  "temperature": 66,
  "pressure": 28
}
```

![设置 "克隆路径和内容"](./media/iot-hub-automatic-device-management/module-config-twin-settings.png)


您还可以通过指定整个克隆路径并提供不带括号的值来设置单个设置。 例如，对于 `properties.desired.chiller-water.temperature`的克隆路径，请将内容设置为 `66`。 然后为压力属性创建新的双子比。 

如果两个或两个以上配置的目标路径相同，则将应用最高优先级配置中的内容（优先级在步骤4中定义）。

若要删除现有属性，请将属性值指定为 `null`。

你可以通过选择 "**添加设备克隆设置**" 或 "**添加模块**克隆" 设置来添加其他设置。

### <a name="specify-metrics-optional"></a>指定指标（可选）

度量值提供了应用配置内容后设备或模块可能会报告回的各种状态的汇总计数。 例如，可以针对挂起的设置更改、错误和成功的设置更改各创建一个指标。

每个配置最多可以有五个自定义指标。 

1. 在“指标名称”中输入名称。

2. 在“指标条件”中输入查询。  该查询基于设备孪生报告的属性。  指标表示查询返回的行数。

例如：

```sql
SELECT deviceId FROM devices 
  WHERE properties.reported.chillerWaterSettings.status='pending'
```

可以包含应用配置的子句，例如： 

```sql
/* Include the double brackets. */
SELECT deviceId FROM devices 
  WHERE configurations.[[yourconfigname]].status='Applied'
```

如果要生成用于报告已配置模块的指标，请从 `devices.modules`中选择 `moduleId`。 例如：

```sql
SELECT deviceId, moduleId FROM devices.modules
  WHERE properties.reported.lastDesiredStatus.code = 200
```

### <a name="target-devices"></a>目标设备

使用孪生中的 tags 属性来定位应该接收此配置的特定设备或模块。 你还可以将克隆的报告属性设定为目标。

自动设备配置只能以设备克隆标记为目标，并且自动模块配置只能以模块克隆标记为目标。 

由于多个配置可能面向相同的设备或模块，因此每个配置都需要一个优先级编号。 如果存在冲突，则优先级最高的配置将会胜出。 

1. 为配置的“优先级”输入一个正整数。 最大数值被认为是最高优先级。 如果两个配置的优先级编号相同，则最近创建的配置胜出。 

2. 输入**目标条件**，以确定将以此配置为目标的设备或模块。 此条件基于个克隆的标记或克隆的已报告属性，并且应与表达式格式匹配。 

   对于自动设备配置，可以仅指定要定位的标记或报告属性。 例如，`tags.environment='test'` 或 `properties.reported.chillerProperties.model='4000x'`。 可以指定 `*` 来定目标到所有设备。 
   
   对于自动模块配置，请使用查询来指定注册到 IoT 中心的模块中的标记或报告的属性。 例如，`from devices.modules where tags.environment='test'` 或 `from devices.modules where properties.reported.chillerProperties.model='4000x'`。 通配符不能用于以所有模块为目标。 

3. 选择“下一步”，进入到最后一步。

### <a name="review-configuration"></a>查看配置

查看配置信息，然后选择“提交”。

## <a name="monitor-a-configuration"></a>监视配置

若要查看配置的详细信息并监视运行它的设备，请执行以下步骤：

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。

3. 检查配置列表。 对于每个配置，可查看以下详细信息：

   * **ID** - 配置的名称。

   * **目标条件**-用于定义目标设备或模块的查询。

   * **优先级** - 分配给配置的优先级编号。

   * **创建时间** - 开始创建配置的时间戳。 当两个配置具有相同的优先级时，此时间戳用于消除它们的关系。 

   * **系统指标** - 指标由 IoT 中心计算，开发人员无法对其进行自定义。 “目标数”指定与目标条件匹配的设备孪生数。 “应用”指定已被配置修改的设备孪生数，这可能包括当某个不同的更高优先级配置同时做出更改时，所完成的部分修改。 

   * **自定义指标**-由开发人员指定为对克隆报告属性进行查询的指标。  对于每个配置，最多可以定义五个自定义指标。 
   
4. 选择要监视的配置。  

5. 检查配置详细信息。 你可以使用选项卡来查看有关接收配置的设备的特定详细信息。

   * **目标条件**-匹配目标条件的设备或模块。 

   * **指标** - 系统指标和自定义指标的列表。  通过在下拉列表中选择 "度量值"，然后选择 "**查看设备**" 或 "**查看模块**"，可以查看为每个指标计算的设备或模块的列表。

   * **设备**克隆设置或**模块克隆设置**-由配置设置的克隆设置。 

   * **配置标签** - 用于描述配置的键值对。  标签不对功能造成影响。 

## <a name="modify-a-configuration"></a>修改配置

修改配置时，更改会立即复制到所有目标设备或模块。 

如果更新目标条件，将发生以下更新：

* 如果克隆不满足旧的目标条件，但满足新的目标条件，并且此配置是该克隆的最高优先级，则会应用此配置。 

* 如果当前正在运行此配置的克隆不再满足目标条件，则将删除该配置中的设置，并将通过下一个优先级最高的配置修改克隆。 

* 如果当前运行此配置的克隆不再满足目标条件，并且不满足任何其他配置的目标条件，则将删除配置中的设置，并且不会对克隆进行其他更改。 

若要修改配置，请执行以下步骤： 

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。 

3. 选择要修改的配置。 

4. 更新以下字段： 

   * 目标条件 
   * 标签 
   * 优先度 
   * 度量值

4. 选择“保存”。

5. 按照[监视配置](#monitor-a-configuration)中的步骤来监视更改的推出。 

## <a name="delete-a-configuration"></a>删除配置

删除某个配置时，所有设备孪生将会采用下一个最高优先级的配置。 如果设备孪生不满足其他任何配置的目标条件，则不会应用其他任何设置。 

1. 在 [Azure 门户](https://portal.azure.com)中，转到 IoT 中心。 

2. 选择“IoT 设备配置”。 

3. 使用复选框选择想要删除的配置。 

4. 选择“删除”。

5. 系统会提示确认。

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
