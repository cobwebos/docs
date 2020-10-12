---
title: 在用于 VM 的 Azure Monitor 中禁用监视
description: 本文介绍如何在用于 VM 的 Azure Monitor 中停止监视虚拟机。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/12/2020
ms.openlocfilehash: 80473aa494b8fbcea5e43870b7717cd3472dd7d1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "79480515"
---
# <a name="disable-monitoring-of-your-vms-in-azure-monitor-for-vms"></a>禁用用于 VM 的 Azure Monitor 中的 Vm 监视

在启用虚拟机 (VM) 监视后，可以选择在用于 VM 的 Azure Monitor 中禁用监视。 本文介绍如何针对一个或多个 VM 禁用监视。  

目前，用于 VM 的 Azure Monitor 不支持选择性地禁用 VM 监视。 Log Analytics 工作区可能支持用于 VM 的 Azure Monitor 和其他解决方案。 它还可能收集其他监视数据。 如果 Log Analytics 工作区提供这些服务，则在开始之前，你需要了解下面所述的影响和方法。

用于 VM 的 Azure Monitor 依赖于以下组件来提供其体验：

* Log Analytics 工作区，用于存储来自 VM 和其他源的监视数据。
* 性能计数器的集合在工作区中配置。 该集合会更新已连接到该工作区的所有 VM 上的监视配置。
* `VMInsights`，它是在工作区中配置的监视解决方案。 此解决方案更新连接到工作区的所有 Vm 上的监视配置。
* Azure VM 扩展 `MicrosoftMonitoringAgent` 和 `DependencyAgent`。 这些扩展收集数据并将其发送到工作区。

准备禁用 VM 监视时，请注意以下事项：

* 如果你正在使用单个 VM 进行评估，并接受了预先选择的默认 Log Analytics 工作区，则可以通过在 VM 中卸载依赖项代理，并从此工作区断开 Log Analytics 代理的连接，来禁用监视。 如果你打算将此 VM 用于其他目的，后来决定将它重新连接到不同的工作区，则适合使用此方法。
* 如果选择的现有 Log Analytics 工作区支持其他监视解决方案并从其他源收集数据，则你可以从工作区中删除解决方案组件，而不会给工作区造成中断或影响。  

>[!NOTE]
> 从工作区中删除解决方案组件后，可以继续查看 Azure Vm 的性能和映射数据。 数据最终会在“性能”和“映射”视图中停止显示。******** **启用**选项将从选定的 Azure VM 获得，因此你可以在将来重新启用监视。  

## <a name="remove-azure-monitor-for-vms-completely"></a>完全删除用于 VM 的 Azure Monitor

如果你仍然需要 Log Analytics 工作区，请遵循以下步骤完全删除用于 VM 的 Azure Monitor。 将 `VMInsights` 从工作区中删除解决方案。  

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 在 Azure 门户中，选择“所有服务”。 在资源列表中，键入“Log Analytics”。 当你开始键入时，列表中会根据输入筛选建议。 选择“Log Analytics”。
3. 在 Log Analytics 工作区列表中，选择启用用于 VM 的 Azure Monitor 时所选的工作区。
4. 在左侧选择“解决方案”。  
5. 在解决方案列表中，选择 " **VMInsights (工作区名称") **。 在解决方案的“概述”页上选择“删除”。  出现确认提示时，请选择“是”。

## <a name="disable-monitoring-and-keep-the-workspace"></a>禁用监视并保留工作区  

如果 Log Analytics 工作区仍需支持其他源的监视，请遵循以下步骤，在评估用于 VM 的 Azure Monitor 所用的 VM 上禁用监视。 对于 Azure VM，需要直接从 VM 中删除适用于 Windows 或 Linux 的依赖项代理 VM 扩展和 Log Analytics 代理 VM 扩展。 

>[!NOTE]
>如果存在以下情况，请不要删除 Log Analytics 代理： 
>
> * Azure 自动化将管理该 VM，以协调流程或者管理配置或更新。 
> * Azure 安全中心将管理该 VM，以进行安全和威胁检测。 
>
> 删除 Log Analytics 代理会阻止这些服务和解决方案主动管理你的 VM。 

1. 登录 [Azure 门户](https://portal.azure.com)。 
2. 在 Azure 门户中，选择“虚拟机”。 
3. 从列表中选择一个虚拟机。 
4. 在左侧选择“扩展”****。 在“扩展”页上选择“DependencyAgent”。********
5. 在“扩展属性”页上选择“卸载”。****
6. 在“扩展”页上选择“MicrosoftMonitoringAgent”。******** 在“扩展属性”页上选择“卸载”。****  
