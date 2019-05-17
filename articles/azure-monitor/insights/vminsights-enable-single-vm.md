---
title: 为 Vm （预览版） 的 Azure Monitor 启用评估 |Microsoft Docs
description: 本文介绍如何在 Azure Monitor 为 Vm 启用单个 Azure 虚拟机或虚拟机规模集用于评估目的。
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
ms.openlocfilehash: 673f153b551e4b0c89a564c96d6bd9819ca26f5d
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2019
ms.locfileid: "65524080"
---
# <a name="enable-azure-monitor-for-vms-preview-for-evaluation"></a>为评估的 Vm （预览版） 中启用 Azure Monitor

若要评估 Azure Monitor 为少量的 Azure 虚拟机或单个虚拟机或虚拟机规模集上的 Vm （预览版），若要启用监视的最简单且最直接的方法是从 Azure 门户。 在此过程结束时，你将具有已成功开始监视它们，并了解他们遇到的任何性能或可用性问题。 

获取启动，请务必查看之前[先决条件](vminsights-enable-overview.md)和验证你的订阅和资源符合的要求。  

## <a name="enable-monitoring-for-a-single-azure-vm"></a>为单个 Azure VM 启用监视
若要在 Azure 门户中启用对 Azure VM 的监视，请执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择“虚拟机”。

1. 从列表中选择一个虚拟机。

1. 在“VM”页上的“监视”部分，选择“见解(预览版)”。

1. 在“见解(预览版)”页上，选择“立即试用”。

    ![为 VM 启用用于 VM 的 Azure Monitor](./media/vminsights-enable-single-vm/enable-vminsights-vm-portal-01.png)

1. 在“Azure Monitor Insights 载入”页上，如果现有的 Log Analytics 工作区与群集在同一订阅中，请从下拉列表中选择该工作区。  

    列表中预先选择了虚拟机在订阅中部署到的默认工作区和位置。 

    >[!NOTE]
    >如果你想要创建新的 Log Analytics 工作区用于存储从虚拟机的监视数据，请按照中的说明[创建 Log Analytics 工作区](../../azure-monitor/learn/quick-create-workspace.md)某个受支持区域中列出[此处](vminsights-enable-overview.md#log-analytics)。

启用监视后，可能需要约 10 分钟才能查看虚拟机的运行状况指标。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)

## <a name="enable-monitoring-for-a-single-virtual-machine-scale-set"></a>为单个虚拟机规模集启用监视

若要启用监视的 Azure 虚拟机规模集在 Azure 门户中，执行以下操作：

1. 登录到 [Azure 门户](https://portal.azure.com)。

2. 选择**虚拟机规模集**。

3. 从列表中，选择虚拟机规模集。

4. 在虚拟机规模集页上，在**监视**部分中，选择**Insights （预览版）**。

5. 上**Insights （预览版）** 页上，如果你想要使用，请在下拉列表中选择了现有 Log Analytics 工作区。

    列表中预先选择了虚拟机在订阅中部署到的默认工作区和位置。 

    ![Azure Monitor 为 Vm 启用虚拟机规模集](./media/vminsights-enable-single-vm/enable-vminsights-vmss-portal-01.png)

    >[!NOTE]
    >如果你想要创建新的 Log Analytics 工作区用于存储从虚拟机的监视数据，请按照中的说明[创建 Log Analytics 工作区](../learn/quick-create-workspace.md)某个受支持区域中列出[此处](vminsights-enable-overview.md#log-analytics)。

启用监视后，可能需要约 10 分钟，然后才能查看规模集的监视数据。

>[!NOTE]
>如果规模集使用手动升级模型需要升级的实例以完成设置。  这可以从下的实例页**设置**部分。

![启用用于 VM 的 Azure Monitor 来监视部署处理](./media/vminsights-enable-single-vm/onboard-vminsights-vmss-portal-status-01.png)

## <a name="next-steps"></a>后续步骤

现在，为虚拟机或虚拟机规模集启用了监视，此信息是可用于分析的 Vm 的 Azure monitor。 若要了解如何使用运行状况功能，请参阅[查看用于 VM 的 Azure Monitor 的运行状况](vminsights-health.md)。 若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 若要查明 VM 性能的瓶颈和整体利用率，请参阅[查看 Azure VM 性能](vminsights-performance.md)；若要查看已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。