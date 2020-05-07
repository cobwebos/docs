---
title: 监视 Azure Stack 边缘设备 |Microsoft Docs
description: 描述如何使用 Azure 门户和本地 web UI 监视 Azure Stack 的边缘。
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 04/15/2019
ms.author: alkohli
ms.openlocfilehash: 2fc7435988a07968e65aaf265c33878c6e72e85b
ms.sourcegitcommit: 856db17a4209927812bcbf30a66b14ee7c1ac777
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "82569428"
---
# <a name="monitor-your-azure-stack-edge"></a>监视 Azure Stack 边缘

本文介绍如何监视 Azure Stack 边缘。 若要监视设备，可以使用 Azure 门户或本地 Web UI。 使用 Azure 门户查看设备事件、配置和管理警报，以及查看指标。 使用物理设备上的本地 Web UI 查看各种设备组件的硬件状态。

在本文中，学习如何：

> [!div class="checklist"]
> * 查看设备事件和相应的警报
> * 查看设备组件的硬件状态
> * 查看设备的容量和事务指标
> * 配置和管理警报

## <a name="view-device-events"></a>查看设备事件

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-device-events.md)]

## <a name="view-hardware-status"></a>查看硬件状态

在本地 Web UI 中执行以下步骤可查看设备组件的硬件状态。

1. 连接到设备的本地 Web UI。
2. 单击“维护”>“硬件状态”****。 可查看各种设备组件的运行状况。 

    ![查看硬件状态](media/azure-stack-edge-monitor/view-hardware-status.png)

## <a name="view-metrics"></a>查看指标

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-view-metrics.md)]

## <a name="manage-alerts"></a>管理警报

[!INCLUDE [Supported OS for clients connected to device](../../includes/data-box-edge-gateway-manage-alerts.md)]

## <a name="next-steps"></a>后续步骤 

了解如何[管理带宽](azure-stack-edge-manage-bandwidth-schedules.md)。