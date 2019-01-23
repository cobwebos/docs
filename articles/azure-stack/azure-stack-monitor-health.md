---
title: 在 Azure Stack 中监视运行状况和警报 | Microsoft Docs
description: 了解如何在 Azure Stack 中监视运行状况和警报。
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2019
ms.author: mabrigg
ms.openlocfilehash: 4e332023e4e3a6efcca8f8a9c7961e299ebf9247
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2019
ms.locfileid: "54473786"
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>在 Azure Stack 中监视运行状况和警报

*适用于：Azure Stack 集成系统和 Azure Stack 开发工具包*

Azure Stack 包含基础结构监控功能，可帮助你查看运行状况和 Azure Stack 区域的警报。 “区域管理”磁贴默认固定在默认提供商订阅的管理员门户上，其中列出了 Azure Stack 的所有已部署区域。 该磁贴显示每个区域的活动严重和警告警报的数量。 磁贴是运行状况和警报功能的 Azure Stack 的入口点。

![“区域管理”磁贴](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack"></a>了解 Azure Stack 中的运行状况

运行状况资源提供程序管理运行状况和警报。 使用运行状况资源提供程序在 Azure Stack 部署和配置期间注册 azure Stack 基础结构组件。 注册后，可以显示每个组件的运行状况和警报。 Azure Stack 中的“运行状况”是个简单的概念。 如果存在一个组件的已注册实例的警报，该组件的运行状况状态反映了最差的活动警报严重性： 警告或严重。

## <a name="alert-severity-definition"></a>警报严重性定义

在 Azure Stack 中会发出警报只有两个严重级别：**警告**并**关键**。

- **警告**  
  操作员可以按计划方式处理警告警报。 该警报通常不会影响用户工作负荷。

- **严重**  
  操作员应紧急处理严重警报。 这些警报是目前影响或将很快影响 Azure Stack 用户的问题。


## <a name="view-and-manage-component-health-state"></a>查看和管理组件运行状况

在管理员门户中，通过 REST API 和 PowerShell，可以查看组件的运行状况状态。

若要在门户中查看运行状况，请在“区域管理”磁贴中单击想要查看的区域。 可以查看基础结构角色和资源提供程序的运行状况。

![基础结构角色列表](media/azure-stack-monitor-health/image2.png)

可以单击资源提供程序或基础结构角色来查看更详细的信息。

> [!WARNING]  
> 如果单击基础结构角色，然后单击角色实例，有的选项**启动**，**重新启动**，或**关闭**。 对集成系统应用更新时，请勿使用这些操作。 此外，**请勿**在 Azure Stack 开发工具包环境中使用这些选项。 这些选项仅专为集成的系统环境，其中每个基础结构角色的多个角色实例。 在开发工具包中重启角色实例（特别是 AzS-Xrp01）会导致系统不稳定。 如需故障排除方面的帮助，请在 [Azure Stack 论坛](https://aka.ms/azurestackforum)中提问。
>

## <a name="view-alerts"></a>查看警报

可直接从“区域管理”边栏选项卡查看每个 Azure Stack 区域的活动警报列表。 默认配置中的第一个磁贴是“警报”磁贴，其中显示区域的严重警报和警告警报摘要。 如同此边栏选项卡中的其他磁贴一样，可将“警报”磁贴固定到仪表板，以便快速访问。

![显示警告的“警报”磁贴](media/azure-stack-monitor-health/image3.png)

选择“警报”磁贴的上半部分可以导航到区域的所有活动警报列表。 如果选择磁贴中的“严重”或“警告”行项，则会导航到警报的筛选列表（“严重”或“警告”）。 

“警报”边栏选项卡支持按状态（“活动”或“已关闭”）和严重性（“严重”或“警告”）进行筛选。 默认视图显示所有活动警报。 所有已关闭的警报在七天后将从系统中删除。

![在“筛选器”窗格中按严重或警告状态进行筛选](media/azure-stack-monitor-health/alert-view.png)

“视图 API”操作显示用于生成列表视图的 REST API。 借助此操作可以快速熟悉可用于查询警报的 REST API 语法。 可在自动化中使用此 API，或者将它与现有的数据中心监视、报告和票证解决方案相集成。

可以单击特定的警报来查看警报详细信息。 警报详细信息显示与警报关联的所有字段，并可让用户快速导航到受影响的组件和警报源。 例如，如果某个基础结构角色实例脱机或不可访问，则会发生以下警报。  

![“警报详细信息”边栏选项卡](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>修复警报

可以选择**修复**某些警报中。

选中时，**修复**操作执行特定于警报以尝试解决此问题的步骤。 一次选择的状态**修复**操作是可作为一个门户通知。

![正在修复](media/azure-stack-monitor-health/repair-in-progress.png)

**修复**操作将报告成功完成或未完成的同一个门户通知，边栏选项卡中的操作。  如果修复操作失败的警报，可能会重新运行**修复**中警报的详细信息的操作。 修复操作已成功完成时，如果**不这样做**重新运行**修复**操作。

![修复成功完成](media/azure-stack-monitor-health/repair-completed.png)

基础结构角色实例重新联机后，会自动关闭此警报。 很多，但不是每个警报，会自动关闭时基础问题得以解决。 如果 Azure Stack 可以解决问题，提供的修复操作按钮的警报将自动关闭。  对于所有其他警报，选择**关闭警报**执行补救步骤之后。 如果问题仍然存在，Azure Stack 会生成新警报。 如果解决此问题，警报将保持关闭状态，并需要没有更多步骤。

## <a name="next-steps"></a>后续步骤

[在 Azure Stack 中管理更新](azure-stack-updates.md)

[Azure Stack 中的区域管理](azure-stack-region-management.md)
