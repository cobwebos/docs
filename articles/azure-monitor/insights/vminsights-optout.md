---
title: 禁用监视 Azure Monitor 中为 Vm （预览版） |Microsoft Docs
description: 本文介绍如何停止监视 Vm 的 Azure Monitor 中的虚拟机。
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
ms.openlocfilehash: eb667486a6e3279cb78fefe02723f14d9f7c9b4f
ms.sourcegitcommit: 1289f956f897786090166982a8b66f708c9deea1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/17/2019
ms.locfileid: "67155687"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms-preview"></a>禁用监视的 Azure Monitor 中的 Vm 的 Vm （预览版）

启用监视的虚拟机 (Vm) 后，您可以更高版本选择禁用对虚拟机的 Azure Monitor 中监视。 本文介绍如何禁用对一个或多个 Vm 的监视。  

目前，Azure 监视的 Vm 不支持选择性禁用 VM 监视。 Log Analytics 工作区可能的 Vm 和其他解决方案支持 Azure Monitor。 它还可能收集其他监视数据。 如果你的 Log Analytics 工作区提供了这些服务，您需要了解的效果和禁用监视在开始之前的方法。

用于 VM 的 Azure Monitor 依赖于以下组件来提供其体验：

* Log Analytics 工作区，其中存储虚拟机和其他源中的监视数据。
* 配置工作区中的性能计数器的集合。 集合更新连接到工作区的所有 Vm 上的监视配置。
* `InfrastructureInsights` 和`ServiceMap`，这监视工作区中已配置的解决方案。 这些解决方案更新连接到工作区的所有 Vm 上的监视配置。
* `MicrosoftMonitoringAgent` 和`DependencyAgent`，这是 Azure VM 扩展。 这些扩展收集，并将数据发送到工作区。

准备要禁用监视的 Vm，请记住以下注意事项：

* 如果使用单个 VM 计算和使用的预先选择的默认 Log Analytics 工作区，则可以禁用通过从 VM 中卸载依赖关系代理，然后断开与此工作区的连接 Log Analytics 代理监视。 如果你想要将 VM 用于其他目的，并决定更高版本以将其重新连接到不同的工作区，这种方法适合。
* 如果选择预先存在的 Log Analytics 工作区，它支持其他监视解决方案和其他来源的数据收集，可以从工作区移除解决方案组件，不会中断或影响你的工作区。  

>[!NOTE]
> 从你的工作区中删除解决方案组件后, 可能会继续查看从 Azure Vm; 的运行状况状态具体而言，将看到性能，并将数据映射时转到门户中的任一视图。 最终，数据将不再出现于**性能**并**映射**视图。 但**运行状况**视图将继续显示为您的 Vm 的运行状况状态。 **立即试用**选项将可用于从所选的 Azure VM，因此你可以重新启用在将来监视。  

## <a name="remove-azure-monitor-for-vms-completely"></a>将 Azure Monitor 完全删除的 Vm

如果您仍需要 Log Analytics 工作区，请按照下列步骤以完全删除的 Vm 的 Azure Monitor。 将删除`InfrastructureInsights`和`ServiceMap`从工作区的解决方案。  

>[!NOTE]
>如果使用服务映射监视解决方案，为 Vm 启用 Azure Monitor 并仍依赖于它之前，不删除该解决方案，如下面的过程的最后一步中所述。  
>

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，选择“所有服务”。  在资源列表中，键入“Log Analytics”  。 开始键入时，建议根据输入筛选器列表。 选择“Log Analytics”  。
3. 在 Log Analytics 工作区列表中，选择工作区选择时为 Vm 启用 Azure Monitor。
4. 在左侧，选择**解决方案**。  
5. 在解决方案的列表中选择**InfrastructureInsights （工作区名称）** 。 上**概述**的解决方案，选择页面**删除**。 当系统提示你确认，则选择**是**。  
6. 在解决方案的列表中选择**ServiceMap （工作区名称）** 。 上**概述**的解决方案，选择页面**删除**。 当系统提示你确认，则选择**是**。  

如果没有记下为虚拟机，启用 Azure Monitor 之前[收集性能计数器](vminsights-enable-overview.md#performance-counters-enabled)工作区中的基于 Windows 的或基于 Linux 的 vm[禁用这些规则](../platform/data-sources-performance-counters.md#configuring-performance-counters)Windows 和 Linux。

## <a name="disable-monitoring-and-keep-the-workspace"></a>禁用监视并保留工作区  

如果你的 Log Analytics 工作区仍需要支持从其他源，执行以下这些步骤来禁用用于评估 Vm 的 Azure Monitor 的虚拟机上监视的监视。 对于 Azure Vm，你将依赖关系代理 VM 扩展和 Log Analytics 代理 VM 扩展适用于 Windows 或 Linux 直接从 VM 中删除。 

>[!NOTE]
>如果不删除 Log Analytics 代理： 
>
> * Azure 自动化管理 VM 安排进程或管理配置或更新。 
> * Azure 安全中心管理安全和威胁检测的 VM。 
>
> 如果你删除 Log Analytics 代理，则将阻止这些服务和解决方案主动管理你的 VM。 

1. 登录到 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“虚拟机”  。 
3. 从列表中选择一个虚拟机。 
4. 在左侧，选择**扩展**。 上**扩展**页上，选择**DependencyAgent**。
5. 在扩展属性页上选择**卸载**。
6. 上**扩展**页上，选择**MicrosoftMonitoringAgent**。 在扩展属性页上选择**卸载**。  
