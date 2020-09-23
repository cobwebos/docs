---
title: 事件聚合
description: 了解用于 IoT 事件聚合的 Defender。
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: aec750d246ce99fa65431e23ef68e70418db0017
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/22/2020
ms.locfileid: "90934848"
---
# <a name="defender-for-iot-event-aggregation"></a>用于 IoT 事件聚合的 Defender

用于 IoT 安全代理的 Defender 从本地设备收集数据和系统事件，并将此数据发送到 Azure 云进行处理和分析。 安全代理收集许多类型的设备事件，包括新进程和新连接事件。 新进程和新连接事件可能会在一秒钟内在设备上合法发生，但这一点对于强健和全面的安全性很重要，安全代理被强制发送的消息数可能很快就会达到或超出 IoT 中心配额和成本限制。 但是，这些事件包含非常重要的安全信息，这对于保护你的设备至关重要。

为了降低额外的配额和成本，同时保护设备受保护，IoT 代理的 Defender 聚合了这些类型的事件。

默认情况下，事件聚合是 **打开** 的，尽管不建议这样做，但可以随时手动 **关闭** 。

聚合当前可用于以下类型的事件：

* ProcessCreate
* ConnectionCreate
* ProcessTerminate (仅限 Windows) 

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？

当事件聚合保持 **开启**时，IoT 代理的 Defender 会聚合间隔时间段或时间范围内的事件。
在间隔时间过后，代理会将聚合事件发送到 Azure 云进行进一步分析。
聚合事件存储在内存中，直到将其发送到 Azure 云。

为了降低代理的内存占用量，每当代理收集到已保留在内存中的事件时，代理都会增加此特定事件的命中次数。 当聚合时间窗口通过时，代理将发送所发生的每个特定事件类型的命中次数。 事件聚合只是聚合每个收集事件类型的命中计数。

仅当满足以下条件时，事件才被视为完全相同：

* ProcessCreate 事件- **命令行**、 **可执行文件**、 **用户名**和 **用户 id** 相同
* ConnectionCreate 事件- **命令行**、 **userId**、 **direction**、 **local address**、 **remote address**、* * 协议和 **目标端口** 相同
* ProcessTerminate 事件-当 **可执行文件** 和 **退出状态** 相同时

### <a name="working-with-aggregated-events"></a>使用聚合事件

在聚合期间，未聚合的事件属性将被丢弃，并显示在 log analytics 中，其值为0。

* ProcessCreate events- **processId**和 **parentProcessId** 设置为0
* ConnectionCreate events- **processId**， **源端口** 设置为0

## <a name="event-aggregation-based-alerts"></a>基于事件聚合的警报

分析后，IoT for IoT 为可疑的聚合事件创建安全警报。 对于每个聚合事件，从聚合事件创建的警报仅出现一次。

每个事件的 "聚合开始时间"、"结束时间" 和 "命中次数" 记录在 Log Analytics 中的 "事件 **ExtraDetails** " 字段中，以便在调查过程中使用。

每个聚合事件表示收集的警报的24小时时间段。 使用每个事件左上方的 "事件选项" 菜单，可以 **消除** 每个单独的聚合事件。

## <a name="event-aggregation-twin-configuration"></a>事件聚合克隆配置

更改**azureiotsecurity**模块的模块克隆标识的[代理配置对象](how-to-agent-configuration.md)内的 Defender for IoT 事件聚合配置。

| 配置名称 | 可能的值 | 详细信息 | 备注 |
|:-----------|:---------------|:--------|:--------|
| aggregationEnabledProcessCreate | boolean | 启用/禁用进程创建事件的事件聚合 |
| aggregationIntervalProcessCreate | ISO8601 Timespan 字符串 | 进程创建事件的聚合间隔 |
| aggregationEnabledConnectionCreate | boolean| 启用/禁用连接创建事件的事件聚合 |
| aggregationIntervalConnectionCreate | ISO8601 Timespan 字符串 | 连接创建事件的聚合间隔 |
| aggregationEnabledProcessTerminate | boolean | 启用/禁用进程终止事件的事件聚合 | 仅限 Windows|
| aggregationIntervalProcessTerminate | ISO8601 Timespan 字符串 | 进程终止事件的聚合间隔 | 仅限 Windows|
|

## <a name="default-configurations-settings"></a>默认配置设置

| 配置名称 | 默认值 |
|:-----------|:---------------|
| aggregationEnabledProcessCreate | 是 |
| aggregationIntervalProcessCreate | PT1H.JSON|
| aggregationEnabledConnectionCreate | 是 |
| aggregationIntervalConnectionCreate | PT1H.JSON|
| aggregationEnabledProcessTerminate | 是 |
| aggregationIntervalProcessTerminate | PT1H.JSON|
|

## <a name="next-steps"></a>后续步骤

本文介绍了有关 IoT 安全代理聚合和可用事件配置选项的信息。

若要继续使用适用于 IoT 的 Defender 部署，请使用以下文章：

- 了解 [安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署 [安全代理](how-to-deploy-agent.md)
- 查看 Defender 以了解 IoT [服务先决条件](service-prerequisites.md)
- 了解如何 [在 Iot 中心为 iot 服务启用 Defender](quickstart-onboard-iot-hub.md)
- 从[用于 IoT 的 DEFENDER 常见问题](resources-frequently-asked-questions.md)中了解有关该服务的详细信息
