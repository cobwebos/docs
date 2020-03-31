---
title: Azure 应用程序网关资源运行状况概述
description: 本文概述 Azure 应用程序网关的资源运行状况功能
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "67659497"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 应用程序网关资源运行状况概述

[Azure 资源运行状况](../service-health/resource-health-overview.md)可帮助您在 Azure 服务问题影响资源时诊断并获得支持。 它通知你有关资源的当前和过去运行状况的信息， 并提供技术支持以帮助你缓解问题。

对于应用程序网关，资源运行状况依赖于网关发出的信号来评估资源是否正常运行。 如果网关运行不正常，资源运行状况将分析其他信息以确定问题根源。 它还标识为了修复问题而由 Microsoft 执行的操作或你可以执行的操作。

有关如何评估运行状况的其他详细信息，请在 [Azure 资源运行状况](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)中查看资源类型和运行状况检查的完整列表。


应用程序网关的运行状况状态显示为以下状态之一：

## <a name="available"></a>可用

状态为“可用”**** 表示服务尚未检测到影响资源运行状况的任何事件。 如果过去 24 小时内网关在计划外停机后进行了恢复，则会看到“最近已解决”**** 通知。

![可用的运行状况状态](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>不可用

状态为“不可用”**** 表示服务已检测到影响网关运行状况的正在发生的平台或非平台事件。

### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基础结构的多个组件触发的。 它们包括计划的操作（例如计划内维护）和意外的事件（例如计划外的主机重启）。

资源运行状况提供了有关事件和恢复过程的更多详细信息。 还可以通过它联系支持人员，即使你没有有效的 Microsoft 支持协议。

![不可用状态](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>未知

此 **Unknown** 运行状况状态指示资源运行状况未收到此网关的相关信息已超过 10 分钟。 此状态不是网关状态的最终指示， 但它是故障排除过程中一个重要的数据点。

如果网关正在按预期方式运行，状态会在几分钟后更改为“可用”。****

如果你遇到问题，则请注意，“未知”运行状态可能表示平台中的事件正在影响网关。****

![状态未知](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>已降级

“已降级”运行状况状态指示网关检测到性能降低，虽然资源仍可供使用。****

![退化状态](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>后续步骤

要了解对应用程序网关 Web 应用程序防火墙 （WAF） 进行故障排除，请参阅[Azure 应用程序网关的 Web 应用程序防火墙疑难解答 （WAF）。](web-application-firewall-troubleshoot.md)