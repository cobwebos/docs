---
title: 启用 Azure 门户中的用于 VM 的 Azure Monitor
description: 了解如何在单个 Azure 虚拟机或虚拟机规模集上评估用于 VM 的 Azure Monitor。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2020
ms.openlocfilehash: 4cdb9390b3146df74f2cbe8eba7b170a5d11fb2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85507052"
---
# <a name="enable-azure-monitor-for-single-vm-or-vmss-in-the-azure-portal"></a>为 Azure 门户中的单个 VM 或 VMSS 启用 Azure Monitor
本文介绍如何使用 Azure 门户为单个虚拟机或虚拟机规模集启用用于 VM 的 Azure Monitor。 此过程可用于以下操作：

- Azure 虚拟机
- Azure 虚拟机规模集
- Azure Arc 计算机

在开始前，请查看[先决条件](vminsights-enable-overview.md)，确保订阅和资源满足要求。  

## <a name="enable-azure-monitor-for-vms"></a>启用用于 VM 的 Azure Monitor

1. 登录到 [Azure 门户](https://portal.azure.com)。

1. 选择**虚拟机**、**虚拟机规模集**或**计算机-Azure Arc**。

1. 从列表中选择一个资源。

1. 在菜单的 "**监视**" 部分，选择 "**见解**"，然后选择 "**启用**"。 下面的示例演示了 Azure 虚拟机，但菜单与 Azure VMSS 或 Azure Arc 类似。

    ![为 VM 启用用于 VM 的 Azure Monitor](media/vminsights-enable-single-vm/enable-vminsights-vm-portal.png)

1. 如果 VM 尚未连接到 Log Analytics 工作区，则系统将提示你选择一个。 如果你以前未[创建过工作区](../../azure-monitor/learn/quick-create-workspace.md)，则可以选择在订阅中部署 VM 或 VMSS 的位置的默认值。 如果此工作区尚不存在，则将创建并配置它。

2. 在执行配置时，你将收到状态消息。

    >[!NOTE]
    >如果使用规模集的手动升级模型，请升级实例以完成设置。 可以从“设置”部分的“实例”页启动升级。********

    ![启用用于 VM 的 Azure Monitor 来监视部署处理](media/vminsights-enable-single-vm/onboard-vminsights-vm-portal-status.png)



## <a name="next-steps"></a>后续步骤

* 若要查看已发现的应用程序依赖项，请参阅[使用用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。 
* 若了解瓶颈、整体利用率和 VM 的性能，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
