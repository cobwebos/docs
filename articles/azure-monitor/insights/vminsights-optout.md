---
title: 如何在用于 VM 的 Azure Monitor（预览版）中禁用监视 | Microsoft Docs
description: 本文介绍如何在用于 VM 的 Azure Monitor 中停止监视虚拟机。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/05/2018
ms.author: magoedte
ms.openlocfilehash: 7b65df1489112bf6e9fe44fe4d4a11db76ff6f5b
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53183703"
---
# <a name="how-to-disable-monitoring-of-your-virtual-machines-with-azure-monitor-for-vms-preview"></a>如何在用于 VM 的 Azure Monitor（预览版）中禁用虚拟机监视

在启用虚拟机监视后，如果你决定不再使用用于 VM 的 Azure Monitor 对其进行监视，可以禁用监视。 本文介绍如何针对一个或多个 VM 实现此目的。  

目前，用于 VM 的 Azure Monitor 不支持有选择性地禁用 VM 监视。 如果 Log Analytics 工作区配置为支持此解决方案和其他解决方案，并支持收集其他监视数据，则在继续操作之前，必须了解下面所述的影响和方法。

用于 VM 的 Azure Monitor 依赖于以下组件来提供其体验：

* Log Analytics 工作区，用于存储从 VM 和其他源收集的监视数据。
* 性能计数器的收集在工作区中配置，此项配置会更新已连接到该工作区的所有 VM 上的监视配置。
* 工作区中配置了两个监视解决方案 - **InfrastructureInsights** 和 **ServiceMap**，此项配置会更新已连接到该工作区的所有 VM 上的监视配置。
* 两个 Azure 虚拟机扩展 - **MicrosoftMonitoringAgent** 和 **DepenendencyAgent**，用于收集数据并将其发送到工作区。

准备在用于 VM 的 Azure Monitor 中禁用虚拟机监视时，请考虑以下问题：

* 如果你正在使用单个 VM 进行评估，并接受了预先选择的默认 Log Analytics 工作区，则可以通过在 VM 中卸载依赖项代理，并从此工作区断开 Log Analytics 代理的连接，来禁用监视。 如果你打算将此 VM 用于其他目的，后来决定将它重新连接到不同的工作区，则适合使用此方法。
* 如果使用 Log Analytics 工作区来支持其他监视解决方案以及从其他源收集数据，则可以从工作区中删除用于 VM 的 Azure Monitor 解决方案组件，而不会给工作区造成中断或影响。  

>[!NOTE]
> 从工作区中删除解决方案组件后，可以继续查看 Azure VM 的运行状况；具体而言，在门户中导航到任一视图时，可以查看性能和映射数据。 一段时间后，数据最终会在“性能”和“映射”视图中停止显示；但是，“运行状况”视图将继续显示 VM 的运行状况。 所选 Azure VM 中会提供“立即试用”选项，让你在将来重新启用监视。  

## <a name="complete-removal-of-azure-monitor-for-vms"></a>完全删除用于 VM 的 Azure Monitor

以下步骤说明在仍然需要 Log Analytics 工作区的情况下，如何完全删除用于 VM 的 Azure Monitor。 要从工作区中删除 **InfastructureInsights** 和 **ServiceMap** 解决方案。  

>[!NOTE]
>如果以前使用了服务映射监视解决方案来启用用于 VM 的 Azure Monitor，并且现在仍依赖于该解决方案，请不要按以下步骤 6 中所述删除该解决方案。  
>

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。
2. 在 Azure 门户中，单击“所有服务”。 在资源列表中，键入 Log Analytics。 开始键入时，会根据输入筛选该列表。 选择“Log Analytics”。
3. 在 Log Analytics 工作区列表中，选择载入用于 VM 的 Azure Monitor 时所选的工作区。
4. 在左窗格中，选择“解决方案”。  
5. 在解决方案列表中选择“InfrastructureInsights(工作区名称)”，然后在解决方案的“概述”页上，单击“删除”。  出现确认提示时，请单击“是”。  
6. 在解决方案列表中选择“ServiceMap(工作区名称)”，然后在解决方案的“概述”页上，单击“删除”。  出现确认提示时，请单击“是”。  

如果在载入用于 VM 的 Azure Monitor 之前，未为工作区中基于 Windows 或 Linux 的 VM [启用性能计数器收集](vminsights-onboard.md?toc=/azure/azure-monitor/toc.json#performance-counters-enabled)，则需要遵循[此处](../../azure-monitor/platform/data-sources-performance-counters.md?toc=/azure/azure-monitor/toc.json#configuring-performance-counters)所述的适用于 Windows 和 Linux 的步骤，来禁用这些规则。

## <a name="disable-monitoring-for-an-azure-vm-and-retain-workspace"></a>禁用 Azure VM 的监视并保留工作区  

以下步骤说明如何禁用为了评估用于 VM 的 Azure Monitor 而启用的虚拟机监视。但是，仍需使用 Log Analytics 工作区来支持从其他源进行监视。 如果这是一个 Azure VM，则需要直接从 VM 中删除适用于 Windows/Linux 的依赖项代理 VM 扩展和 Log Analytics 代理 VM 扩展。 

>[!NOTE]
>如果虚拟机受 Azure 自动化的管理并用于协调过程、管理配置或管理更新，或者受 Azure 安全中心的管理并用于安全管理和威胁检测，则不应删除 Log Analytics 代理。 否则，会阻止这些服务和解决方案主动管理你的 VM。 

1. 在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。 
2. 在 Azure 门户中，选择“虚拟机”。 
3. 从列表中选择一个虚拟机。 
4. 在左窗格中选择“扩展”，然后在“扩展”页中选择“DependencyAgent”。
5. 在“扩展属性”页中，单击“卸载”。
6. 在“扩展”页中选择“MicrosoftMonitoringAgent”，然后在“扩展属性”页中单击“卸载”。  
