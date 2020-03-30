---
title: 在 Azure 门户中为 VM 启用 Azure 监视器
description: 了解如何在单个 Azure 虚拟机或虚拟机规模集上评估用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 45bc8f16a547d4a95820f9dcd02132844b3be83c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480702"
---
# <a name="enable-azure-monitor-for-vms-in-the-azure-portal"></a>在 Azure 门户中为 VM 启用 Azure 监视器

本文介绍如何使用 Azure 门户在少数 Azure 虚拟机 （VM） 上启用 VM 的 Azure 监视器。 你的目标是监视 VM，发现性能或可用性问题。 

在开始前，请查看[先决条件](vminsights-enable-overview.md)，确保订阅和资源满足要求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>为单个 Azure VM 启用监视
若要启用对 Azure VM 的监视，请执行以下操作：

1. 登录到 Azure[门户](https://portal.azure.com)。

1. 选择“虚拟机”。****

1. 从列表中选择一个虚拟机。

1. 在 VM 页上，在 **"监视**"部分中，选择 **"见解"，** 然后**启用**。

    ![为 VM 启用用于 VM 的 Azure Monitor](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区****。  

    该列表预先选择了默认工作区和 VM 在订阅中部署的位置。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储 VM 中的监视数据，请参阅[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于[支持的区域](vminsights-enable-overview.md#log-analytics)之一。

6. 执行配置时，您将收到状态消息。

    ![启用用于 VM 的 Azure Monitor 来监视部署处理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>为单个虚拟机规模集启用监视

若要启用对 Azure 虚拟机规模集的监视，请执行以下操作：

1. 登录到 Azure[门户](https://portal.azure.com)。

2. 选择“虚拟机规模集”。****

3. 从列表中选择虚拟机规模集。

4. 在虚拟机缩放集页上，在 **"监视**"部分中，选择 **"见解****"，然后启用**。

5. 在 **"见解"** 页上，如果要使用现有的日志分析工作区，请在下拉列表中选择它。

    列表预先选择了 VM 在订阅中部署到的默认工作区和位置。 

    ![为虚拟机规模集启用用于 VM 的 Azure Monitor](media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储来自虚拟机规模集的监视数据，请参阅[创建 Log Analytics 工作区](../learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于[支持的区域](vminsights-enable-overview.md#log-analytics)之一。

6. 执行配置时，您将收到状态消息。

    >[!NOTE]
    >如果使用规模集的手动升级模型，请升级实例以完成设置。 可以从“设置”部分的“实例”页启动升级。********
    
    ![启用用于 VM 的 Azure Monitor 来监视部署处理](media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status.png)

现已为 VM 或虚拟机规模集启用监视，可以在用于 VM 的 Azure Monitor 中使用监视信息进行分析了。 

## <a name="next-steps"></a>后续步骤

* 若要查看已发现的应用程序依赖项，请参阅[使用用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 
* 若了解瓶颈、整体利用率和 VM 的性能，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
