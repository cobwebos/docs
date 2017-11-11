---
title: "监视运行状况和 Azure 堆栈中的警报 |Microsoft 文档"
description: "了解如何监视运行状况和 Azure 堆栈中的警报。"
services: azure-stack
documentationcenter: 
author: twooley
manager: byronr
editor: 
ms.assetid: 69901c7b-4673-4bd8-acf2-8c6bdd9d1546
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/10/2017
ms.author: twooley
ms.openlocfilehash: cf454a438f088d8079352ac60ce845185b741327
ms.sourcegitcommit: 6a22af82b88674cd029387f6cedf0fb9f8830afd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2017
---
# <a name="monitor-health-and-alerts-in-azure-stack"></a>监视运行状况和 Azure 堆栈中的警报

*适用范围： Azure 堆栈集成系统和 Azure 堆栈开发工具包*

Azure 堆栈包括基础结构监视功能，您可以查看运行状况和 Azure 堆栈区域的警报。 **区域管理**固定的磁贴，默认情况下，在管理员门户中默认提供程序订阅，列出 Azure 堆栈的所有已部署的区域。 该磁贴显示每个区域的活动关键和警告警报数和是运行状况和警报功能的 Azure 堆栈你入口点。

 ![区域管理磁贴](media/azure-stack-monitor-health/image1.png)

 ## <a name="understand-health-in-azure-stack"></a>了解 Azure 堆栈中的运行状况

 运行状况和警报是由运行状况资源提供程序管理。 在 Azure 堆栈部署和配置期间注册的运行状况资源提供程序中 azure 堆栈的基础结构组件。 此注册使运行状况和每个组件的警报的显示。 Azure 堆栈中的运行状况是一个简单的概念。 如果存在一个组件的已注册实例的警报，该组件的运行状况状态将反映最差的活动警报严重性;警告，或严重。
 
 ## <a name="view-and-manage-component-health-state"></a>查看和管理组件运行状况状态
 
 作为 Azure 堆栈操作员，您可以查看组件的运行状况状态，在管理员门户中，通过 REST API 和 PowerShell。
 
若要在门户中查看运行状况状态，请单击你想要查看中的区域**区域管理**磁贴。 你可以查看基础结构角色和资源提供程序的运行状况状态。

![基础结构角色的列表](media/azure-stack-monitor-health/image2.png)

你可以单击要查看更多详细的信息的资源提供程序或基础结构角色。

> [!WARNING]
>如果你单击基础结构中的角色，，然后单击角色实例，有用于选项启动，请重新启动或关闭。 当你将更新应用到集成系统，则不使用这些操作。 此外，执行操作**不**的 Azure 堆栈开发工具包环境中使用这些选项。 这些选项旨在仅用于一个集成的系统的环境，其中每个基础结构角色的多个角色实例。 在开发工具包中重新启动角色实例 (尤其是 AzS Xrp01) 会导致系统不稳定。 有关疑难解答的帮助，发布到你的问题[Azure 堆栈论坛](https://aka.ms/azurestackforum)。
>
 
## <a name="view-alerts"></a>查看警报

为每个 Azure 堆栈区域的活动警报的列表是可直接从**区域管理**边栏选项卡。 在默认配置的第一个磁贴是**警报**磁贴，这将显示的关键摘要和区域的警告性警报。 你可以将固定警报磁贴的在此边栏选项卡，到进行快速访问仪表板上的任何其他磁贴类似。   

![警报将显示警告的磁贴](media/azure-stack-monitor-health/image3.png)

通过选择顶部**警报**磁贴，你导航到区域的所有活动警报的列表。 如果你选择任何**严重**或**警告**在磁贴中的行项，你导航到警报 （严重或警告） 的筛选列表。 

![筛选警告性警报](media/azure-stack-monitor-health/image4.png)
  
**警报**边栏选项卡支持来筛选状态 （活动或已关闭） 和严重级别 （严重或警告） 的功能。 默认视图将显示所有活动警报。 7 天后从系统中删除所有已关闭的警报。

![对筛选器按关键或警告状态的筛选器窗格](media/azure-stack-monitor-health/image5.png)

**视图 API**操作显示的 REST API，用于生成列表视图。 此操作提供熟悉的 REST API 语法，你可以用于查询警报的快速方法。 在自动化中集成; 二是与你现有的数据中心监视、 报告和票证解决方案，你可以使用此 API。 

![显示 REST API 视图 API 选项](media/azure-stack-monitor-health/image6.png)

你可以单击一个特定的警报，以查看警报详细信息。 警报详细信息显示了与警报，并启用快速导航到的受影响的组件和警报源的所有字段。 例如，如果其中一个基础结构角色实例脱机或不可访问，则会发生以下警报。  

![警报详细信息边栏选项卡](media/azure-stack-monitor-health/image7.png)

基础结构角色实例重新联机后，此警报会自动关闭。 在基础问题解决后，许多，但不是每个警报会自动关闭。 我们建议你选择**关闭警报**执行修正步骤之后。 如果问题仍然存在，Azure 堆栈会生成新警报。 如果你解决此问题，警报将保持关闭状态，并需要执行任何其他操作。

## <a name="next-steps"></a>后续步骤

[管理 Azure 堆栈中的更新](azure-stack-updates.md)

[Azure 堆栈中的区域管理](azure-stack-region-management.md)
