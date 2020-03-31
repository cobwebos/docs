---
title: 了解 IoT 事件聚合的 Azure 安全中心*微软文档
description: 了解用于 IoT 事件聚合的 Azure 安全中心。
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
ms.date: 09/26/2019
ms.author: mlottner
ms.openlocfilehash: ca1d1a5761e62b2838a474dcb83f450987972998
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "73928959"
---
# <a name="azure-security-center-for-iot-event-aggregation"></a>用于 IoT 事件聚合的 Azure 安全中心

IoT 安全代理 Azure 安全中心从本地设备收集数据和系统事件，并将此数据发送到 Azure 云进行处理和分析。 安全代理收集多种类型的设备事件，包括新进程和新的连接事件。 新进程和新的连接事件可能在几秒钟内在设备上频繁发生，虽然对强大和全面的安全性很重要，但安全代理强制发送的消息数可能快速到达或超过 IoT 中心配额和成本限制。 但是，这些事件包含非常有价值的安全信息，这些信息对于保护您的设备至关重要。

为了减少额外的配额和成本，同时保持设备受到保护，IoT 代理的 Azure 安全中心聚合这些类型的事件。

默认情况下，事件聚合处于**打开状态**，尽管不建议这样做，但可以随时手动**关闭**。

聚合当前可用于以下类型的事件：
* 流程创建
* 连接创建
* 进程终止（仅限视窗）

## <a name="how-does-event-aggregation-work"></a>事件聚合如何工作？
当事件聚合处于**打开状态时**，IoT 代理的 Azure 安全中心将事件聚合为间隔期间或时间段。
间隔期结束后，代理会将聚合事件发送到 Azure 云进行进一步分析。
聚合事件存储在内存中，直到发送到 Azure 云。

为了减少代理的内存占用空间，每当代理收集与已保存在内存中的事件相同的事件时，代理都会增加此特定事件的命中计数。 当聚合时间窗口过去时，代理发送发生的每种特定类型的事件的命中计数。 事件聚合只是每个收集类型的事件的热门计数的聚合。

仅当满足以下条件时，事件才被视为相同： 

* 进程创建事件 - 当**命令行**、**可执行项**、**用户名**和**userid**相同时
* 连接创建事件 - 当**命令行**、**用户 Id**、**方向**、**本地地址**、**远程地址**、_协议**和目标端口**相同时
* 进程终止事件 - 当**可执行**和**退出状态**相同时

### <a name="working-with-aggregated-events"></a>使用聚合事件

在聚合期间，将丢弃未聚合的事件属性，并显示在值为 0 的日志分析中。 
* 进程创建事件 -**进程 Id**和**父进程Id**设置为 0
* 连接创建事件 -**进程 Id**和**源端口**设置为 0

## <a name="event-aggregation-based-alerts"></a>基于事件聚合的警报 
分析后，IoT Azure 安全中心会为可疑的聚合事件创建安全警报。 从聚合事件创建的警报仅为每个聚合事件显示一次。

每个事件的聚合开始时间、结束时间和命中计数都记录在日志分析中的"额外**详细信息**"事件字段中，以便在调查期间使用。 

每个聚合事件表示收集的警报的 24 小时周期。 使用每个事件左上角的事件选项菜单，可以**关闭**每个聚合事件。    

## <a name="event-aggregation-twin-configuration"></a>事件聚合孪生配置
更改**Azureiot 安全**模块模块的模块孪生标识的[代理配置对象](how-to-agent-configuration.md)内的 IoT 事件聚合 Azure 安全中心配置。

| 配置名称 | 可能值 | 详细信息 | 备注 |
|:-----------|:---------------|:--------|:--------|
| 聚合启用进程创建 | boolean | 启用/禁用进程创建事件的事件聚合 |
| 聚合间隔进程创建 | ISO8601 时间跨度字符串 | 进程创建事件的聚合间隔 |
| 聚合启用连接创建 | boolean| 启用/禁用连接创建事件的事件聚合 |
| 聚合间隔连接创建 | ISO8601 时间跨度字符串 | 连接创建事件的聚合间隔 |
| 聚合启用进程终止 | boolean | 启用/禁用进程终止事件的事件聚合 | 仅限 Windows|
| 聚合间隔进程终止 | ISO8601 时间跨度字符串 | 进程终止事件的聚合间隔 | 仅限 Windows|
|

## <a name="default-configurations-settings"></a>默认配置设置

| 配置名称 | 默认值 |
|:-----------|:---------------|
| 聚合启用进程创建 | true |
| 聚合间隔进程创建 | "PT1H"|
| 聚合启用连接创建 | true |
| 聚合间隔连接创建 | "PT1H"|
| 聚合启用进程终止 | true |
| 聚合间隔进程终止 | "PT1H"|
|

## <a name="next-steps"></a>后续步骤

在本文中，您了解了用于 IoT 安全代理聚合的 Azure 安全中心以及可用的事件配置选项。

要继续使用用于 IoT 部署的 Azure 安全中心，请使用以下文章：

- 了解[安全代理身份验证方法](concept-security-agent-authentication-methods.md)
- 选择并部署[安全代理](how-to-deploy-agent.md)
- 查看 Azure 安全中心，查看 IoT[服务先决条件](service-prerequisites.md)
- 了解如何在[IoT 中心启用 IoT 服务的 Azure 安全中心](quickstart-onboard-iot-hub.md)
- 从[IoT 常见问题解答的 Azure 安全中心](resources-frequently-asked-questions.md)了解有关该服务
