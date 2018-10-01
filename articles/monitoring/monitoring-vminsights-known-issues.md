---
title: 用于 VM 的 Azure Monitor 的已知问题 | Microsoft Docs
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
ms.date: 09/18/2018
ms.author: magoedte
ms.openlocfilehash: 819c3e74355cf80c7a998abb8b02b10c9e077059
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2018
ms.locfileid: "47062761"
---
# <a name="known-issues-with-azure-monitor-for-vms"></a>用于 VM 的 Azure Monitor 的已知问题

下面是用于 VM 的 Azure Monitor 的“运行状况”功能的已知问题：

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
- 某些 Windows 运行状况条件（例如 DNS 客户端服务运行状况）的阈值不可修改，因为其运行状况已根据上下文锁定为服务或实体的“正在运行”、“可用”状态。  在未来版本中，此值不由数字 4 来代表，而是会转换为实际的显示字符串。  
- 某些 Linux 运行状况条件（例如逻辑磁盘运行状况）的阈值不可修改，因为它们已经设置为在状况不正常时触发。  这些条件指示某个项目是联机还是脱机，是启用还是禁用，并通过显示值 1 或 0 来代表和指示相同的意思。
- 在对预先选择的订阅和资源组使用“Azure Monitor”->“虚拟机”->“运行状况”-> 任何列表视图在任何资源组中大规模更新“资源组”筛选器时，会导致列表视图显示“无结果”。  返回到“Azure Monitor”->“虚拟机”->“运行状况”选项卡，选择所需的订阅和资源组，然后导航到列表视图。

## <a name="next-steps"></a>后续步骤
查看[载入用于 VM 的 Azure Monitor](monitoring-vminsights-onboard.md)，以了解启用虚拟机监视的要求和方法。