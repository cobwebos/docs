---
title: Azure IoT 中心迁移到诊断设置 | Microsoft Docs
description: 如何更新 Azure IoT 中心以使用 Azure 诊断设置而非使用操作监视功能来实时监视 IoT 中心内的操作状态。
author: kgremban
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: kgremban
ms.openlocfilehash: ab07da38c01b052a4220274fb059683a22950a3f
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750696"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>将 IoT 中心从操作监视迁移到诊断设置

使用[操作监视](iot-hub-operations-monitoring.md)跟踪 IoT 中心内的操作状态的客户可以将该工作流迁移到 [Azure 诊断设置](../azure-monitor/platform/platform-logs-overview.md)（Azure Monitor 的一项功能）。 诊断设置针对许多 Azure 服务提供了资源级诊断信息。

**IoT 中心的操作监视功能已弃用**，并已从门户中删除。 本文提供了将工作负荷从操作监视移动到诊断设置的步骤。 若要详细了解弃用日程表，请参阅[利用 Azure Monitor 和 Azure 资源运行状况监视 Azure IoT 解决方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中心

若要在 Azure 门户中更新 IoT 中心，请首先开启诊断设置，然后关闭操作监视。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>关闭操作监视

> [!NOTE]
> 从2019年3月11日起，将从 IoT 中心的 Azure 门户界面中删除操作监视功能。 下面的步骤不再适用。 若要迁移，请确保在上述 Azure Monitor 诊断设置中启用了正确的类别。

在工作流中测试新的诊断设置后，可以关闭操作监视功能。 

1. 在 IoT 中心菜单中，选择“操作监视”。

2. 在每个监视类别下，选择“无”。

3. 保存操作监视更改。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用操作监视的应用程序

操作监视和诊断设置的架构略有不同。 请更新当前使用操作监视的应用程序以映射到诊断设置使用的架构，这非常重要。 

同时，诊断设置还提供五种新类别用于跟踪。 更新应用程序的现有架构后，还要添加新类别：

* 云到设备孪生操作
* 设备到云孪生操作
* 孪生查询
* 作业操作
* 直接方法

有关特定的架构结构，请参阅[了解诊断设置的架构](iot-hub-monitor-resource-health.md#understand-the-logs)。

## <a name="monitoring-device-connect-and-disconnect-events-with-low-latency"></a>以低延迟监视设备连接和断开连接事件

若要在生产环境中监视设备连接和断开连接事件，建议订阅事件网格上的[**设备断开**连接事件](iot-hub-event-grid.md#event-types)，以获取警报并监视设备连接状态。 使用此[教程](iot-hub-how-to-order-connection-state-events.md)了解如何在 IoT 解决方案中集成 IoT 中心的设备已连接和设备已断开连接事件。

## <a name="next-steps"></a>后续步骤

[监视 Azure IoT 中心的运行状况并快速诊断问题](iot-hub-monitor-resource-health.md)
