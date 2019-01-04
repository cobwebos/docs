---
title: 用于 VM 的 Azure Monitor（预览版）的已知问题 | Microsoft Docs
description: 本文介绍用于 VM 的 Azure Monitor 的已知问题。用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它结合了 Azure VM 操作系统的运行状况和性能监视功能。 用于 VM 的 Azure Monitor 可自动发现应用程序组件以及与其他资源的依赖关系并映射它们之间的通信。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/07/2018
ms.author: magoedte
ms.openlocfilehash: b2a60ddee58a7a9c37582085132ee8a1df767cb4
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190350"
---
# <a name="known-issues-with-azure-monitor-for-vms-preview"></a>用于 VM 的 Azure Monitor（预览版）的已知问题

本文介绍用于 VM 的 Azure Monitor 的已知问题。用于 VM 的 Azure Monitor 是 Azure 中的一个解决方案，它结合了 Azure VM 操作系统的运行状况和性能监视功能。 

下面是“运行状况”功能的已知问题：

- 针对连接到 Log Analytics 工作区的所有 VM 启用“运行状况”功能。 即使操作在单个 VM 中开始和结束，也是如此。
- 使用支持的方法对 VM 禁用监视功能后，再次尝试部署该 VM 时，应将它部署在同一个工作区中。 如果使用新工作区并尝试查看该 VM 的运行状况，它可能显示异常行为。
- 如果删除了某个 Azure VM，它会在 VM 列表视图中显示 3 到 7 天。 此外，单击已删除 VM 的状态会打开“运行状况诊断”视图，然后启动加载循环。 选择已删除 VM 的名称会打开一个窗格，其中显示一条指出该 VM 已被删除的消息。
- 在此版本中，无法修改运行状况条件的时间段和频率。 
- 无法禁用运行状况条件。 
- 部署后，可能需要经过一段时间，数据才会显示在“Azure Monitor” > “虚拟机” > “运行状况”窗格或“VM 资源” > “见解”窗格中。
- 运行状况诊断体验的更新速度比其他任何视图更快。 在视图之间切换时，信息可能会延迟。 
- 用于 VM 的 Azure Monitor 随附的“警报”摘要仅显示在受监视 Azure VM 中检测到的运行状况问题所导致的警报。
- 单个 VM 和 Azure Monitor 中的“警报列表”窗格显示其监视条件在过去 30 天设置为“已激发”的警报。 警报不可配置。 但是，打开“警报列表”窗格后，可以单击摘要来更改监视条件和时间范围的筛选器值。
- 我们建议不要在“警报列表”窗格中修改“资源类型”、“资源”和“监视服务”筛选器。 他们已根据特定的解决方案进行配置。 此列表视图显示的字段比“Azure Monitor” > “警报”列表视图要多。   
- 在 Linux VM 中，“运行状况诊断”视图显示 VM 的整个域名，而不是用户定义的 VM 名称。
- 关闭 VM 会将一些运行状况条件更新为“关键”，将另一些运行状况条件更新为“正常”。 网络 VM 状态显示为“关键”。
- 无法修改运行状况警报的严重性。 只能将它启用或禁用。 另外，某些严重性会根据运行状况条件的状态进行更新。
- 如果修改某个运行状况条件实例的任何设置，则会修改 VM 上相同类型的所有运行状况条件实例。 例如，如果修改对应于逻辑磁盘 C: 的磁盘可用空间运行状况条件实例的阈值，则该阈值会应用到在同一 VM 中发现和监视的其他所有逻辑磁盘。  
- 针对 Windows VM 的运行状况条件的阈值不可修改，因为其运行状况状态已设置为“正在运行”或“可用”。 从[工作负荷监视器 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) 查询运行状态时，如果出现以下情况，则会服务或实例的 **LessThan** 或 **GreaterThan** 的 *comparisonOperator* 值，以及阈值 **4**：
   - DNS 客户端服务运行状况 - 服务未运行。 
   - DHCP 客户端服务运行状况 - 服务未运行。 
   - RPC 服务运行状况 - 服务未运行。 
   - Windows 防火墙服务运行状况 - 服务未运行。
   - Windows 事件日志服务运行状况 - 服务未运行。 
   - 服务器服务运行状况 - 服务未运行。 
   - Windows 远程管理服务运行状况 - 服务未运行。 
   - 文件系统错误或损坏 - 逻辑磁盘不可用。

- 以下 Linux 运行状况条件的阈值不可修改，因为其运行状态已设置为 *true*。 从实体的工作负荷监视 API 查询时，运行状态会显示带有值 **LessThan** 的 *comparisonOperator* 以及阈值 **1**，具体取决于上下文：
   - 逻辑磁盘状态 - 逻辑磁盘未联机/不可用
   - 磁盘状态 - 磁盘未联机/不可用
   - 网络适配器状态 - 网络适配器已禁用  

- 在门户和工作负荷监视 API 中，Windows 中的“总 CPU 利用率”运行状况条件显示**不等于 4** 的阈值。 当“总 CPU 利用率”超过 95% 并且系统队列长度大于 15 时，即会达到该阈值。 在此版本中无法修改此运行状况条件。 
- 即使门户或工作负荷监视器 API 可以立即更新，但配置更改（例如更新阈值）最长需要 30 分钟才会生效。 
- Windows 中不提供单独的处理器和逻辑处理器级运行状况条件。 只为 Windows VM 提供“总 CPU 利用率”。 
- 为每个运行状况条件定义的警报规则不会在 Azure 门户中显示。 只能在[工作负荷监视器 API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/workloadmonitor/resource-manager) 中启用或禁用运行状况警报规则。 
- 无法在 Azure 门户中为运行状况警报分配 [Azure Monitor 操作组](../../azure-monitor/platform/action-groups.md)。 只能使用通知设置 API 来配置每当激发运行状况警报时要触发的操作组。 目前，可以针对 VM 分配操作组，以便针对 VM 激发的所有运行状况警报都会触发相同的操作组。 如传统的 Azure 警报不同，每个运行状况警报规则没有单独的操作组概念。 此外，触发运行状况警报时，仅支持配置为提供电子邮件或短信通知的操作组。 

## <a name="next-steps"></a>后续步骤
若要了解有关为虚拟机启用监视的要求和方法，请参阅[部署用于 VM 的 Azure Monitor](vminsights-onboard.md)。
