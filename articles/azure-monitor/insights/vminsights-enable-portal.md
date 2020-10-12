---
title: 在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor
description: 了解如何使用 Azure 门户在单个 Azure 虚拟机或虚拟机规模集上启用用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/27/2020
ms.openlocfilehash: ba6ae9262fb0e55c53e1b1421c075e555fae8d98
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87328133"
---
# <a name="enable-azure-monitor-for-single-virtual-machine-or-virtual-machine-scale-set-in-the-azure-portal"></a>在 Azure 门户中为单个虚拟机或虚拟机规模集启用 Azure Monitor
本文介绍如何使用 Azure 门户为虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。 此过程可用于以下操作：

- Azure 虚拟机
- Azure 虚拟机规模集
- 与 Azure Arc 连接的混合虚拟机

## <a name="prerequisites"></a>必备条件

- [创建并配置 Log Analytics 工作区](vminsights-configure-workspace.md)。 或者，您可以在此过程中创建新的工作区。
- 请参阅 [支持的操作系统](vminsights-enable-overview.md#supported-operating-systems) ，以确保正在启用的虚拟机或虚拟机规模集的操作系统受支持。 

## <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor

从 Azure 门户中，选择 " **虚拟机**"、" **虚拟机规模集**" 或 "虚拟机" **-"Azure Arc** "，然后从列表中选择一个资源。 在菜单的 " **监视** " 部分，选择 " **见解** "，然后选择 " **启用**"。 下面的示例演示了 Azure 虚拟机，但是菜单与 Azure 虚拟机规模集或 Azure Arc 类似。

![为 VM 启用用于 VM 的 Azure Monitor](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

如果虚拟机尚未连接到 Log Analytics 工作区，则系统将提示您选择一个。 如果你之前未 [创建工作区](../../azure-monitor/learn/quick-create-workspace.md)，则可以选择在订阅中部署虚拟机或虚拟机规模集的位置的默认值。 如果此工作区尚不存在，则将创建并配置它。 如果选择现有的工作区，则会将其配置为用于 VM 的 Azure Monitor （如果尚未这样做）。

> [!NOTE]
> 如果选择以前未配置用于 VM 的 Azure Monitor 的工作区，则会将 *VMInsights* 管理包添加到此工作区。 这将应用到已连接到工作区的任何代理，无论是否为其启用了用于 VM 的 Azure Monitor。 将从这些虚拟机中收集性能数据，并将其存储在 *InsightsMetrics* 表中。

![选择工作区](media/vminsights-configure-workspace/select-workspace.png)

在执行配置时，你将收到状态消息。

>[!NOTE]
>如果对虚拟机规模集使用手动升级模式，请升级实例以完成设置。 可以从“设置”部分的“实例”页启动升级。********

![启用用于 VM 的 Azure Monitor 来监视部署处理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>后续步骤

* 请参阅 [使用用于 VM 的 Azure Monitor 映射](vminsights-maps.md) 查看已发现的应用程序依赖项。 
* 请参阅 [查看 AZURE VM 性能](vminsights-performance.md) ，找出瓶颈、整体利用率和 VM 的性能。
