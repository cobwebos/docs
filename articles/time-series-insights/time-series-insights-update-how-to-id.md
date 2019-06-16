---
title: 在 Azure 时序见解预览版中选择时序 ID 的最佳做法 | Microsoft Docs
description: 了解在 Azure 时序见解预览中选择时序 ID 时的最佳做法。
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/08/2019
ms.custom: seodec18
ms.openlocfilehash: af540267e4afc1b248b66b1c6f4989b832c38b58
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66237576"
---
# <a name="best-practices-for-choosing-a-time-series-id"></a>选择时序 ID 的最佳做法

本文介绍 Azure 时序见解预览分区键、时序 ID 以及选择 ID 的最佳做法。

## <a name="choose-a-time-series-id"></a>选择时间序列 ID

选择时序 ID 与为数据库选择分区键相同。 此决定十分重要，应在设计时确定。 无法更新现有时序见解预览环境来使用其他时序 ID。 换言之，如果通过时序 ID 创建了环境，则无法更改不可变的策略属性。

> [!IMPORTANT]
> 时序 ID 需区分大小写，且不可更改（设置后不能更改）。

考虑到这一点，选择合适的时序 ID 至关重要。 选择时序 ID 时，请考虑遵循以下最佳做法：

* 选择的属性名称应具有范围较宽的值，并采用均衡的访问模式。 采用具有大量（例如，几百甚至几千个）非重复性值的分区键是最佳做法。 对许多客户而言，这与 JSON 中的 DeviceID 或 SensorID 类似。
* 在[时序模型](./time-series-insights-update-tsm.md)的叶节点级别，时序 ID 应是唯一的。
* 时序 ID 属性名称字符串最多可包含 128 个字符，时序 ID 属性值最多可包含 1024 个字符。
* 如果缺少某些唯一时序 ID 属性值，这些值将被视为 null 值，并参与唯一性约束。

此外，最多可选择三 (3) 个键属性作为时序 ID  。

  > [!NOTE]
  > 三 (3) 个键属性必须是字符串  。

以下方案介绍了选择多个键属性作为时序 ID 的情况：  

### <a name="scenario-one"></a>方案 1

* 拥有旧资产组，且每组资产都有一个唯一的密钥。
* 例如，一个组由唯一属性 deviceId 标识，另一个组的唯一属性为 objectId   。 两个组都不包含对方的唯一属性。 在此示例中，将选择两个键 deviceId 和 objectId 作为唯一键。
* 我们接受 null 值，且如果事件负载中缺少属性，则计为 `null` 值。 向两个不同事件源发送数据时，这种方法同样合适，其中每个事件源中的数据具有唯一的时序 ID。

### <a name="scenario-two"></a>方案二

* 同一组资产中需要多个唯一的属性。 
* 例如，假设你是智能建筑制造商，并且要在每个房间部署传感器。 通常每个房间的 sensorId 的值相同，例如 sensor1、sensor2 和 sensor3     。
* 此外，在 flrRm 属性中，多处建筑物的楼层和房间号存在重叠，其值为 1a、2b、3a 等等     。
* 最后还有一个位置属性，其中包含雷德蒙德、巴塞罗纳和东京等值     。 若要创建唯一性，可将以下三个属性指定为时序 ID 键：sensorId、flrRm 和位置    。

## <a name="next-steps"></a>后续步骤

* 详细了解[数据模型](./time-series-insights-update-tsm.md)。

* 计划 [Azure 时序见解（预览）环境](./time-series-insights-update-plan.md)。