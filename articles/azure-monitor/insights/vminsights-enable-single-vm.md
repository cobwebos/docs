---
title: 为 Vm （预览版） 的 Azure Monitor 启用评估 |Microsoft Docs
description: 了解如何评估 Vm 的 Azure Monitor，单个 Azure 虚拟机上或在虚拟机规模集上。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/09/2019
ms.author: magoedte
ms.openlocfilehash: ec909bcd16f923bbd7036f6a69df2bbb07e561b8
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67122516"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>为评估的 Vm （预览版） 中启用 Azure Monitor

可以在少量的 Azure 虚拟机 (Vm) 上的 Vm （预览版） 评估 Azure Monitor 或上单个 VM 或虚拟机规模集。 若要启用监视的最简单且最直接方式是从 Azure 门户。 你的目标是监视 Vm 并在发现任何性能或可用性问题。 

在开始之前，请查看[先决条件](vminsights-enable-overview.md)并确保你的订阅和资源满足的要求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>为单个 Azure VM 启用监视
若要启用 Azure VM 的监视：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“虚拟机”。 

1. 从列表中选择一个虚拟机。

1. 在“VM”页上的“监视”部分，选择“见解(预览版)”。  

1. 在“见解(预览版)”页上，选择“立即试用”。  

    ![为 VM 启用用于 VM 的 Azure Monitor](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区  。  

    默认工作区和其中将 VM 部署在订阅中的位置，预先选择就列表。 

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区来存储 VM 中的监视数据，请参阅[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于某个[支持的区域](vminsights-enable-overview.md#log-analytics)。

启用监视后，你可能需要等待大约 10 分钟，你可以查看 VM 的运行状况度量值之前。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>为单个虚拟机规模集启用监视

若要启用监视的 Azure 虚拟机规模集：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择**虚拟机规模集**。

3. 从列表中，选择虚拟机规模集。

4. 在虚拟机规模集页上，在**监视**部分中，选择**Insights （预览版）** 。

5. 上**Insights （预览版）** 页上，如果你想要使用现有的 Log Analytics 工作区，在下拉列表中选择它。

    默认工作区并将 VM 部署到订阅中的位置，预先选择就列表。 

    ![Azure Monitor 为 Vm 启用虚拟机规模集](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >若要创建新的 Log Analytics 工作区来存储中的虚拟机规模集的监视数据，请参阅[创建 Log Analytics 工作区](../learn/quick-create-workspace.md)。 Log Analytics 工作区必须属于某个[支持的区域](vminsights-enable-overview.md#log-analytics)。

启用监视后，你可能需要等待大约 10 分钟，然后才能查看规模集的监视数据。

>[!NOTE]
>如果规模集使用手动升级模型，升级的实例以完成设置。 你可以开始从升级**实例**页上，在**设置**部分。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

现在，已启用监视 VM 或虚拟机规模集，是可用于分析的 Vm 的 Azure Monitor 中的监视信息。 

## <a name="next-steps"></a>后续步骤

* 若要了解如何使用运行状况的功能，请参阅[了解 Azure 监视器 Vm 的运行状况](vminsights-health.md)。 
* 若要查看发现的应用程序依赖关系，请参阅[Vm 映射为使用 Azure Monitor](vminsights-maps.md)。 
* 若要标识瓶颈、 整体利用率，以及 VM 的性能，请参阅[查看 Azure VM 性能](vminsights-performance.md)。