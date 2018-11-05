---
title: 用于 VM 的 Azure Monitor（预览版）的已知问题 | Microsoft Docs
description: 用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它合并了 Azure VM 操作系统的运行状况和性能监视、应用程序组件及其与其他资源的依赖关系的自动发现功能，并映射这些组件和资源之间的通信。 本文介绍已知问题。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/25/2018
ms.author: magoedte
ms.openlocfilehash: eba9e26f12fd9e1862727adec4f8c8f594e8f659
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/25/2018
ms.locfileid: "50091668"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor（预览版）的已知问题

下面是用于 VM 的 Azure Monitor 的“运行状况”功能的已知问题：

- 即使在单个 VM 中启动并完成了载入，但运行状况功能也会载入到连接到 Log Analytics 工作区的所有 VM。
- 如果 Azure VM 由于被移除或删除而不存在了，那么它将在 VM 列表视图中显示三至七天。 此外，单击已移除或已删除的 VM 的状态会为其启动“运行状况诊断”视图，然后进入加载循环。 选择已删除的 VM 的名称会启动边栏选项卡，其中显示一条消息，说明已删除 VM。
- 此版本的运行状况条件的时间段和频率不能修改。 
- 无法禁用运行状况条件。 
- 登记后，可能需要一定的时间才能通过“Azure Monitor”->“虚拟机”->“运行状况”或“VM 资源”边栏选项卡 ->“见解”查看数据
- 运行状况诊断体验的更新速度快于任何其他视图，因此在视图之间切换时，可能会体验到信息延迟  
- 在“用于 VM 的 Azure Monitor 运行状况”中提供的警报摘要仅适用于已针对运行状况问题触发的警报，而这些问题是使用受监视的 Azure VM 检测到的。
- 单个 VM 和 Azure Monitor 中的“警报列表”视图页显示其监视条件在过去 30 天设置为“已触发”的警报。  无法对其进行配置。 但是，在单击摘要以后，一旦启动“警报列表”视图页，即可更改监视器条件和时间范围的筛选器值。
- 在“警报列表”视图页上，建议不要修改“资源类型”、“资源”和“监视器服务”筛选器，因为它们是针对解决方案配置的（与“Azure Monitor”->“警报列表”视图相比，此列表视图显示一些额外的字段）。    
- 在 Linux VM 中，“运行状况诊断”视图使用 VM 的整个域名，而不是用户定义的 VM 名称。
- 关闭 VM 会将其部分运行状况条件更新为严重状况，另一些更新为正常状况，VM 的总状况为严重状况。
- 运行状况警报严重性不能修改，只能启用或禁用。  另外，某些严重性会根据运行状况条件的状态进行更新。
- 修改某个运行状况条件实例的任何设置时，也会修改 VM 上同一类型的所有运行状况条件实例的相同设置。 例如，如果修改对应于逻辑磁盘 C: 的磁盘可用空间运行状况条件实例的阈值，则该阈值会应用到所有其他针对同一 VM 发现和监视的逻辑磁盘。   
- 针对 Windows VM 的以下运行状况条件的阈值不可修改，因为其运行状态已设置为“运行”或“可用”。 从[工作负载监视器 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager)查询时，若出现以下情况，运行状态会显示 comparisonOperator 值，LessThan 或 GreaterThan，以及服务或实体的阈值 4：
   - DNS 客户端服务运行状况 - 服务未运行 
   - DHCP 客户端服务运行状况 - 服务未运行 
   - RPC 服务运行状况 - 服务未运行 
   - Windows 防火墙服务运行状况 - 服务未运行
   - Windows 事件日志服务运行状况 - 服务未运行 
   - 服务器服务运行状况 - 服务未运行 
   - Windows 远程管理服务运行状况 - 服务未运行 
   - 文件系统错误或损坏 - 逻辑磁盘不可用

- 以下 Linux 运行状况条件的阈值不可修改，因为它们的运行状态已设置为“true”。  从实体的工作负载监视 API 查询时，运行状态会显示带有值 LessThan 的 comparisonOperator 以及阈值 1，具体取决于上下文：
   - 逻辑磁盘状态 - 逻辑磁盘未联机/不可用
   - 磁盘状态 - 磁盘未联机/不可用
   - 网络适配器状态 - 网络适配器已禁用  

- Windows 中的总 CPU 使用率运行状况条件显示，门户的阈值“不等于 4”，并且从工作负载监视 API 查询，且 CPU 使用率超过 95% 时，系统队列长度大于 15。 无法在此发布中修改此运行状况条件。  
- 即使门户或工作负载监视器 API 可能会立即更新，但配置更改（例如更新阈值）需要最多 30 分钟才会生效。  
- Windows 中没有单独的处理器和逻辑处理器级别的运行状况条件，只有总 CPU 使用率可用于 Windows VM。  
- 为每个运行状况条件定义的预警规则不会在 Azure 门户中公开。 它们只是可以从[工作负载监视器 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) 中配置，以启用或禁用运行状况预警规则。  
- 无法从 Azure 门户为运行状况警报分配 [操作组](../monitoring-and-diagnostics/monitoring-action-groups.md)。 必须使用通知设置 API 来配置在任何时候触发运行状况警报时要触发的操作组。 目前，可以针对 VM 分配操作组，这样针对 VM 触发的所有运行状况警报都会触发相同的操作组。 如传统的 Azure 警报一样，每个运行状况警报规则没有单独的操作组的概念。 此外，触发运行状况警报时，仅支持配置为通过发送电子邮件或 SMS 进行通知的操作组。 

## <a name="next-steps"></a>后续步骤
查看[载入用于 VM 的 Azure Monitor](monitoring-vminsights-onboard.md)，以了解启用虚拟机监视的要求和方法。