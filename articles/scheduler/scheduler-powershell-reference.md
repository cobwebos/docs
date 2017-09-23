---
title: "计划程序 PowerShell Cmdlet 参考"
description: "计划程序 PowerShell Cmdlet 参考"
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: 
ms.assetid: 9a26c457-d7a1-4e4a-bc79-f26592155218
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/18/2016
ms.author: deli
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 419e489e4e99935dea49b2d3f9a405013b94048c
ms.contentlocale: zh-cn
ms.lasthandoff: 04/27/2017

---
# <a name="scheduler-powershell-cmdlets-reference"></a>计划程序 PowerShell Cmdlet 参考
下表介绍 Azure 计划程序中每个主要 cmdlet 的参考页并链接到该页。

若要安装 Azure PowerShell 并将其与 Azure 订阅相关联，请参阅 [如何安装和配置 Azure PowerShell](/powershell/azure/overview)。 

有关 [Azure Resource Manager cmdlet](/powershell/azure/overview) 的详细信息，请参阅[将 Azure PowerShell 与 Azure Resource Manager 配合使用](../powershell-azure-resource-manager.md)。

| Cmdlet | Cmdlet 说明 |
| --- | --- |
| [Disable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |禁用某一作业集合。 |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |启用某一作业集合。 |
| [Get-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |获取计划程序作业。 |
| [Get-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |获取作业集合。 |
| [Get-AzureRmSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |获取作业历史记录。 |
| [New-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |创建 HTTP 作业。 |
| [New-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |创建作业集合。 |
| [New-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) |创建服务总线队列作业。 |
| [New-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |创建服务总线主题作业。 |
| [New-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |创建存储队列作业。 |
| [Remove-AzureRmSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |删除计划程序作业。 |
| [Remove-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |删除作业集合。 |
| [Set-AzureRmSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |修改计划程序 HTTP 作业。 |
| [Set-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |修改作业集合。 |
| [Set-AzureRmSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |修改服务总线队列作业。 |
| [Set-AzureRmSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |修改服务总线主题作业。 |
| [Set-AzureRmSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |修改存储队列作业。 |

若要获取详细信息，可以运行以下任意 cmdlet： 

```
Get-Help <cmdlet name> -Detailed
```
```
Get-Help <cmdlet name> -Examples
```
```
Get-Help <cmdlet name> -Full
```

## <a name="see-also"></a>另请参阅
 [计划程序是什么？](scheduler-intro.md)

 [Azure 计划程序的概念、术语和实体层次结构](scheduler-concepts-terms.md)

 [开始在 Azure 门户中使用计划程序](scheduler-get-started-portal.md)

 [Azure 计划程序中的计划和计费](scheduler-plans-billing.md)

 [Azure 计划程序 REST API 参考](https://msdn.microsoft.com/library/mt629143)

 [Azure 计划程序的高可用性和可靠性](scheduler-high-availability-reliability.md)

 [Azure 计划程序的限制、默认值和错误代码](scheduler-limits-defaults-errors.md)

 [Azure 计划程序出站身份验证](scheduler-outbound-authentication.md)


