---
title: 了解用于 IoT 解决方案的 Azure 安全中心解决方案事件聚合 |Microsoft Docs
description: 了解如何在 Azure 安全中心中为 IoT 服务聚合事件。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: d40dac0b-abd0-4ff5-82eb-0f511ee13725
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/21/2019
ms.author: mlottner
ms.openlocfilehash: a8f751d0a40a8d8e1555549c200a9a7ca8ef0661
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2019
ms.locfileid: "68600613"
---
# <a name="security-agent-event-aggregation"></a>安全代理事件聚合

用于 IoT 安全代理的 Azure 安全中心从本地设备收集数据和系统事件, 并将此数据发送到 Azure 云进行处理和分析。 安全代理收集许多类型的设备事件, 包括新进程和新连接事件。 新进程和新连接事件可能会在一秒钟内在设备上合法发生, 但这一点对于强健和全面的安全性而言, 这会强制安全代理发送的消息量可能很快就会达到或超过 IoT 中心配额和开销限制。 但是, 这些事件包含非常重要的安全信息, 这对于保护你的设备至关重要。

为了降低额外的配额和成本, 同时保护设备, Azure 安全中心的 IoT 代理会聚合这些类型的事件。

默认情况下, 事件聚合是**打开**的, 尽管不建议这样做, 但可以随时手动**关闭**。

聚合当前可用于以下类型的事件:
* ProcessCreate
* ConnectionCreate
* ProcessTerminate (仅限 Windows)

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？
当事件聚合为**On**时, 用于 IoT 代理的 Azure 安全中心会聚合间隔时间段或时间范围内的事件。
在间隔时间过后, 代理会将聚合事件发送到 Azure 云进行进一步分析。
聚合事件存储在内存中, 直到将其发送到 Azure 云。

为了降低代理的内存占用量, 每当代理收集到已保留在内存中的事件时, 代理都会增加此特定事件的命中次数。 当聚合时间窗口通过时, 代理将发送所发生的每个特定事件类型的命中次数。 事件聚合只是聚合每个收集事件类型的命中计数。

仅当满足以下条件时, 事件才被视为完全相同: 

* ProcessCreate 事件-**命令行**、**可执行文件**、**用户名**和**用户 id**相同
* ConnectionCreate 事件-**命令行**、 **userId**、 **direction**、 **local address**、**远程地址**、**协议**和**目标端口**相同
* ProcessTerminate 事件-当**可执行文件**和**退出状态**相同时

### <a name="working-with-aggregated-events"></a>使用聚合事件

在聚合期间, 未聚合的事件属性将被丢弃, 并显示在 log analytics 中, 其值为0。 
* ProcessCreate events- **processId**和**parentProcessId**设置为0
* ConnectionCreate events- **processId**,**源端口**设置为0

## <a name="event-aggregation-based-alerts"></a>基于事件聚合的警报 
分析之后, 适用于 IoT 的 Azure 安全中心为可疑的聚合事件创建安全警报。 对于每个聚合事件, 从聚合事件创建的警报仅出现一次。

每个事件的 "聚合开始时间"、"结束时间" 和 "命中计数" 记录在 Log Analytics 的 "事件**ExtraDetails** " 字段中, 以便在调查过程中使用。 


## <a name="event-aggregation-twin-configuration"></a>事件聚合克隆配置
更改**azureiotsecurity**模块的模块克隆标识的[代理配置对象](how-to-agent-configuration.md)内的 IoT 事件聚合的 Azure 安全中心配置。

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
| aggregationEnabledProcessCreate | 真 |
| aggregationIntervalProcessCreate | PT1H.JSON|
| aggregationEnabledConnectionCreate | 真 |
| aggregationIntervalConnectionCreate | PT1H.JSON|
| aggregationEnabledProcessTerminate | 真 |
| aggregationIntervalProcessTerminate | PT1H.JSON|
|

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心的 IoT 安全代理聚合和可用事件配置选项。

若要继续开始使用 Azure 安全中心进行 IoT 部署, 请使用以下文章:

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心以了解 IoT[服务先决条件](service-prerequisites.md)
- 了解如何[在 Iot 中心为 iot 服务启用 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 详细了解[Azure 安全中心提供的 IOT 常见问题解答](resources-frequently-asked-questions.md)
