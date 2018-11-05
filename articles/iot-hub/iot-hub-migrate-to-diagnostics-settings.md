---
title: Azure IoT 中心迁移到诊断设置 | Microsoft Docs
description: 如何更新 Azure IoT 中心以使用 Azure 诊断设置而非使用操作监视功能来实时监视 IoT 中心内的操作状态。
author: kgremban
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/10/2017
ms.author: kgremban
ms.openlocfilehash: 3cb0f91f3143e6a4828548f3a15678b3814cba17
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/26/2018
ms.locfileid: "50154855"
---
# <a name="migrate-your-iot-hub-from-operations-monitoring-to-diagnostics-settings"></a>将 IoT 中心从操作监视迁移到诊断设置

使用[操作监视](iot-hub-operations-monitoring.md)跟踪 IoT 中心内的操作状态的客户可以将该工作流迁移到 [Azure 诊断设置](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)（Azure Monitor 的一项功能）。 诊断设置针对许多 Azure 服务提供了资源级诊断信息。

IoT 中心的操作监视功能已弃用，将来会被删除。 本文提供了将工作负荷从操作监视移动到诊断设置的步骤。 若要详细了解弃用日程表，请参阅[利用 Azure Monitor 和 Azure 资源运行状况监视 Azure IoT 解决方案](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health/)。

## <a name="update-iot-hub"></a>更新 IoT 中心

若要在 Azure 门户中更新 IoT 中心，请首先开启诊断设置，然后关闭操作监视。  

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="turn-off-operations-monitoring"></a>关闭操作监视

在工作流上测试新的诊断设置后，可以关闭操作监视功能。 

1. 在 IoT 中心菜单中，选择“操作监视”。

2. 在每个监视类别下，选择“无”。

3. 保存操作监视更改。

## <a name="update-applications-that-use-operations-monitoring"></a>更新使用操作监视的应用程序

操作监视和诊断设置的架构略有不同。 请更新当前使用操作监视的应用程序以映射到诊断设置使用的架构，这非常重要。 

此外，诊断设置还针对五个新类别提供跟踪。 更新应用程序的现有架构后，还要添加新类别：

* 云到设备孪生操作
* 设备到云孪生操作
* 孪生查询
* 作业操作
* 直接方法

有关特定的架构结构，请参阅[了解诊断设置的架构](iot-hub-monitor-resource-health.md#understand-the-logs)。

## <a name="next-steps"></a>后续步骤

* [监视 Azure IoT 中心的运行状况并快速诊断问题](iot-hub-monitor-resource-health.md)