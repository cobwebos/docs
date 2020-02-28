---
title: 在用于 VM 的 Azure Monitor 中禁用监视（预览版） |Microsoft Docs
description: 本文介绍如何在用于 VM 的 Azure Monitor 中停止监视虚拟机。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/05/2018
ms.openlocfilehash: fb4347e610920380792a17bb620e6d97a7d72505
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669499"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>禁用用于 VM 的 Azure Monitor 中的 Vm 监视（预览版）

启用虚拟机（Vm）的监视之后，稍后可以选择在用于 VM 的 Azure Monitor 中禁用监视。 本文介绍如何为一个或多个 Vm 禁用监视。  

目前，用于 VM 的 Azure Monitor 不支持选择禁用 VM 监视。 Log Analytics 工作区可能支持用于 VM 的 Azure Monitor 和其他解决方案。 它还可能会收集其他监视数据。 如果 Log Analytics 工作区提供这些服务，则需要了解在开始之前禁用监视的影响和方法。

用于 VM 的 Azure Monitor 依赖于以下组件来提供其体验：

* Log Analytics 工作区，用于存储来自 Vm 和其他源的监视数据。
* 工作区中配置的性能计数器的集合。 集合将更新连接到工作区的所有 Vm 上的监视配置。
* `InfrastructureInsights` 和 `ServiceMap`，这是在工作区中配置的监视解决方案。 这些解决方案更新连接到工作区的所有 Vm 上的监视配置。
* `MicrosoftMonitoringAgent` 和 `DependencyAgent`，它们是 Azure VM 扩展。 这些扩展会收集数据并将其发送到工作区。

在准备禁用 Vm 监视时，请注意以下注意事项：

* 如果你使用单个 VM 进行评估，并使用预选默认 Log Analytics 工作区，则可以通过从 VM 卸载依赖关系代理并从该工作区断开 Log Analytics 代理来禁用监视。 如果要将 VM 用于其他目的，并决定稍后将其重新连接到其他工作区，则此方法适用。
* 如果选择了一个支持其他监视解决方案和来自其他源的数据收集的预先存在的 Log Analytics 工作区，则可以从工作区中删除解决方案组件，而不会中断或影响工作区。  

>[!NOTE]
> 从工作区中删除解决方案组件后，可以继续查看 Azure Vm 的运行状况状态;具体而言，当您在门户中切换到任一视图时，您将看到性能和映射数据。 数据最终将停止出现在 "**性能**" 和 "**映射**" 视图中。 但**运行状况**视图将继续显示 vm 的运行状况状态。 "**立即试用**" 选项可从选定的 Azure VM 获得，因此你可以在将来重新启用监视。  

## <a name="remove-azure-monitor-for-vms-completely"></a>完全删除用于 VM 的 Azure Monitor

如果仍需要 Log Analytics 工作区，请执行以下步骤以完全删除用于 VM 的 Azure Monitor。 将从工作区中删除 `InfrastructureInsights` 和 `ServiceMap` 解决方案。  

>[!NOTE]
>如果在启用用于 VM 的 Azure Monitor 之前使用了服务映射监视解决方案，但仍依赖于它，请不要删除此解决方案，如以下过程的最后一步所述。  
>

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 开始键入时，列表会根据输入筛选建议。 选择“Log Analytics”。
3. 在 Log Analytics 工作区列表中，选择启用用于 VM 的 Azure Monitor 时选择的工作区。
4. 在左侧选择 "**解决方案**"。  
5. 在解决方案列表中，选择 " **InfrastructureInsights （工作区名称）** "。 在解决方案的 "**概述**" 页上，选择 "**删除**"。 出现确认提示时，请选择 **"是"** 。  
6. 在解决方案列表中，选择 " **ServiceMap （工作区名称）** "。 在解决方案的 "**概述**" 页上，选择 "**删除**"。 出现确认提示时，请选择 **"是"** 。  

启用用于 VM 的 Azure Monitor 之前，如果未在工作区中收集基于 Windows 或基于 Linux 的虚拟机的[性能计数器](vminsights-enable-overview.md#performance-counters-enabled)，请禁用适用于 Windows 和 linux 的[规则](../platform/data-sources-performance-counters.md#configuring-performance-counters)。

## <a name="disable-monitoring-and-keep-the-workspace"></a>禁用监视并保留工作区  

如果 Log Analytics 工作区仍需要支持其他源的监视，请遵循以下步骤在用于评估用于 VM 的 Azure Monitor 的 VM 上禁用监视。 对于 Azure Vm，你会直接从 VM 中删除依赖关系代理 VM 扩展和适用于 Windows 或 Linux 的 Log Analytics 代理 VM 扩展。 

>[!NOTE]
>如果以下情况，请勿删除 Log Analytics 代理： 
>
> * Azure Automation 管理 VM 以协调进程或管理配置或更新。 
> * Azure 安全中心管理 VM 的安全和威胁检测。 
>
> 如果删除 Log Analytics 代理，则会阻止这些服务和解决方案主动管理你的 VM。 

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“虚拟机”。 
3. 从列表中选择一个虚拟机。 
4. 在左侧选择 "**扩展**"。 在 "**扩展**" 页上，选择 " **DependencyAgent**"。
5. 在 "扩展属性" 页上，选择 "**卸载**"。
6. 在 "**扩展**" 页上，选择 " **MicrosoftMonitoringAgent**"。 在 "扩展属性" 页上，选择 "**卸载**"。  
