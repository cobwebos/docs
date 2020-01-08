---
title: 选择时序 ID 的最佳实践-Azure 时序见解 |Microsoft Docs
description: 了解在 Azure 时序见解预览中选择时序 ID 时的最佳实践。
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/19/2019
ms.custom: seodec18
ms.openlocfilehash: 6fd8d6187c86306840c33b3aaf334e71086b20a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75452747"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>选择时序 ID 的最佳做法

本文总结了 Azure 时序见解预览环境的时序 ID 的重要性，以及选择其中的最佳实践。

## <a name="choose-a-time-series-id"></a>选择时间序列 ID

选择适当的时序 ID 是至关重要的。 选择时序 ID 与为数据库选择分区键相同。 创建时序见解预览环境时，需要使用此方法。 

> [!IMPORTANT]
> 时序 Id 如下：
> * *区分大小写*的属性：字母和字符大小写用于在搜索、比较、更新和分区时使用。
> * *不可变*属性：创建后将无法更改。

> [!TIP]
> 如果事件源是 IoT 中心，则时序 ID 可能是***iothub***的。

要遵循的关键最佳实践包括：

* 选择包含多个非重复值的分区键（例如，成百上千个）。 在许多情况下，这可能是 JSON 中的设备 ID、传感器 ID 或标记 ID。
* 在[时序模型](./time-series-insights-update-tsm.md)的叶节点级别，时序 ID 应是唯一的。
* 时序 ID 的属性名称字符串的字符限制为128。 对于时序 ID 的属性值，字符限制为1024。
* 如果缺少时序 ID 的唯一属性值，则将其视为 null 值并遵循相同的唯一性约束规则。
* 你还可以选择最多*三个*键属性作为时序 ID。 它们的组合将是表示时序 ID 的组合键。  
  > [!NOTE]
  > 三个键属性必须是字符串。
  > 您必须一次对此复合键（而不是一个属性）进行查询。

## <a name="select-more-than-one-key-property"></a>选择多个键属性

以下方案说明了如何选择多个键属性作为时序 ID。  

### <a name="example-1-time-series-id-with-a-unique-key"></a>示例1：具有唯一键的时序 ID

* 你有一些旧群资产。 每个都有一个唯一键。
* 一个汽油由属性**deviceId**唯一标识。 对于其他汽油，unique 属性为**objectId**。 这两个汽油都不包含其他汽油的唯一属性。 在此示例中，您将选择两个密钥： **deviceId**和**objectId**作为唯一键。
* 我们接受 null 值，而事件负载中缺少属性的存在性将计为 null 值。 这也是处理发送到两个事件源的数据的适当方法，其中每个事件源中的数据具有唯一的时序 ID。

### <a name="example-2-time-series-id-with-a-composite-key"></a>示例2：具有组合键的时序 ID

* 同一组资产中需要多个唯一的属性。 
* 你是智能建筑的制造商，并在每个房间中部署传感器。 对于每个房间，你通常具有相同的**sensorId**值。 例如， **sensor1**、 **sensor2**和**sensor3**。
* 您的大楼在**flrRm**属性中的站点之间有重叠的楼层和房间号。 这些数字的值如**1a**、 **2b**和**3a**。
* 您有一个属性， **location**，其中包含**Redmond**、**巴塞罗纳**和**东京**等值。 若要创建唯一性，请指定以下三个属性作为时序 ID 键： **sensorId**、 **flrRm**和**location**。

示例原始事件：

```JSON
{
  "sensorId": "sensor1",
  "flrRm": "1a",
  "location": "Redmond",
  "temperature": 78
}
```

在 Azure 门户中，可以输入组合键，如下所示： 

```JSON
[{"name":"sensorId","type":"String"},{"name":"flrRm","type":"String"},{"name":"location","type":"string"}]
```

## <a name="next-steps"></a>后续步骤

* 阅读有关[数据建模](./time-series-insights-update-tsm.md)的详细信息。

* 规划[Azure 时序见解预览版环境](./time-series-insights-update-plan.md)。