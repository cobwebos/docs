---
title: Azure 应用程序网关资源运行状况概述
description: 本文是 Azure 应用程序网关资源运行状况功能的概述
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 7/9/2019
ms.author: victorh
ms.openlocfilehash: db29551a8150b70e797d45fe659482470c8aca2a
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/08/2019
ms.locfileid: "67659497"
---
# <a name="azure-application-gateway-resource-health-overview"></a>Azure 应用程序网关资源运行状况概述

[Azure 资源运行状况](../service-health/resource-health-overview.md)可帮助您诊断和获取支持，在 Azure 服务问题影响到你的资源。 它通知你有关资源的当前和过去运行状况的信息， 并提供技术支持以帮助你缓解问题。

对于应用程序网关，资源运行状况依赖于网关发出信号来评估是否无论为正常运行。 如果网关不正常，资源运行状况将分析其他信息以确定问题根源。 它还给出了 Microsoft 正在采取的操作或解决该问题可以执行的操作。

有关如何评估运行状况的其他详细信息，请在 [Azure 资源运行状况](../service-health/resource-health-checks-resource-types.md#microsoftnetworkapplicationgateways)中查看资源类型和运行状况检查的完整列表。


应用程序网关的运行状况状态显示为以下状态之一：

## <a name="available"></a>可用

**可用**状态意味着该服务尚未检测到影响资源的运行状况的任何事件。 你将看到**最近已解决**中网关已在恢复计划外停机时间在过去 24 小时期间的通知。

![可用的运行状况状态](media/resource-health-overview/available-full.png)

## <a name="unavailable"></a>不可用

**不可用**状态意味着服务检测到正在运行的平台或非平台事件影响的网关的运行状况。

### <a name="platform-events"></a>平台事件

平台事件是由 Azure 基础结构的多个组件触发的。 它们包括计划的操作（例如计划内维护）和意外的事件（例如计划外的主机重启）。

资源运行状况提供了有关事件和恢复过程的更多详细信息。 还可以通过它联系支持人员，即使你没有有效的 Microsoft 支持协议。

![不可用状态](media/resource-health-overview/unavailable.png)

## <a name="unknown"></a>Unknown

**未知**运行状况状态指示资源运行状况未收到有关网关的时间超过 10 分钟的信息。 此状态不是状态的最终的网关的指示。 但它是故障排除过程中一个重要的数据点。

如果网关正在按预期运行，状态将变为**可用**几分钟后。

如果遇到问题，**未知**状况可能暗示平台中的事件影响网关。

![未知的状态](media/resource-health-overview/unknown.png)

## <a name="degraded"></a>已降级

**已降级**运行状况状态指示你的网关检测到丢失的性能，但仍可供使用。

![Degrated 的状态](media/resource-health-overview/degraded.png)

## <a name="next-steps"></a>后续步骤

若要了解应用程序网关 Web 应用程序防火墙 (WAF) 的故障排除，请参阅[排查 Web 应用程序防火墙 (WAF) 为 Azure 应用程序网关](web-application-firewall-troubleshoot.md)。