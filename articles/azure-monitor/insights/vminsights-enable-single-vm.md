---
title: 为评估启用用于 VM 的 Azure Monitor （预览版） |Microsoft Docs
description: 了解如何在单个 Azure 虚拟机或虚拟机规模集上评估用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3b79eaeed420426fe79b2b6caf19ba1d1be61939
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77664605"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>启用用于评估的用于 VM 的 Azure Monitor （预览版）

可以在少量的 Azure 虚拟机（Vm）上或在单个 VM 或虚拟机规模集上评估用于 VM 的 Azure Monitor （预览版）。 启用监视的最简单、最直接的方法是从 Azure 门户。 你的目标是监视 Vm，并发现任何性能或可用性问题。 

在开始之前，请查看[先决条件](vminsights-enable-overview.md)，并确保你的订阅和资源符合要求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>为单个 Azure VM 启用监视
若要启用对 Azure VM 的监视：

1. 登录 [Azure 门户](https://portal.azure.com)。

1. 选择“虚拟机”。

1. 从列表中选择一个虚拟机。

1. 在“VM”页上的“监视”部分，选择“见解(预览版)”。

1. 在“见解(预览版)”页上，选择“立即试用”。

    ![为 VM 启用用于 VM 的 Azure Monitor](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  

    该列表预先选择了默认工作区和 VM 在订阅中部署的位置。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储 VM 中的监视数据，请参阅[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于[支持的区域](vminsights-enable-overview.md#log-analytics)之一。

启用监视后，你可能需要等待大约10分钟，然后才能查看 VM 的运行状况指标。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>为单个虚拟机规模集启用监视

若要启用对 Azure 虚拟机规模集的监视：

1. 登录 [Azure 门户](https://portal.azure.com)。

2. 选择 "**虚拟机规模集**"。

3. 从列表中选择虚拟机规模集。

4. 在 "虚拟机规模集" 页上的 "**监视**" 部分中，选择 "**见解（预览版）** "。

5. 在 "**见解（预览版）** " 页上，如果要使用现有 Log Analytics 工作区，请在下拉列表中选择它。

    此列表 preselects 在订阅中将 VM 部署到的默认工作区和位置。 

    ![为虚拟机规模集启用用于 VM 的 Azure Monitor](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal.png)

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区以存储来自虚拟机规模集的监视数据，请参阅[创建 Log Analytics 工作区](../learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于[支持的区域](vminsights-enable-overview.md#log-analytics)之一。

启用监视后，你可能需要等待大约10分钟，然后才能查看规模集的监视数据。

>[!NOTE]
>如果对规模集使用手动升级模型，请升级实例以完成设置。 可以在 "**设置**" 部分中，从 "**实例**" 页开始升级。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

现在，你已为 VM 或虚拟机规模集启用监视功能，现在可以在用于 VM 的 Azure Monitor 中分析监视信息。 

## <a name="next-steps"></a>后续步骤

* 若要查看已发现的应用程序依赖关系，请参阅[使用用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 
* 若要确定瓶颈、整体利用率和 VM 性能，请参阅[查看 AZURE vm 性能](vminsights-performance.md)。
