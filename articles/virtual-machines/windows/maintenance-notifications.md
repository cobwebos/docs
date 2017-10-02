---
title: "处理 Azure 中 Windows VM 的维护通知 | Microsoft Docs"
description: "查看 Azure 中运行的 Windows 虚拟机的维护通知并开始自助式维护。"
services: virtual-machines-windows
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/14/2017
ms.author: zivr
ms.translationtype: HT
ms.sourcegitcommit: 8f9234fe1f33625685b66e1d0e0024469f54f95c
ms.openlocfilehash: 90129bee5771534b01ac12473c6cfed77840ae20
ms.contentlocale: zh-cn
ms.lasthandoff: 09/20/2017

---


# <a name="handling-planned-maintenance-notifications-for-windows-virtual-machines"></a>处理 Windows 虚拟机的计划内维护通知

Azure 定期执行更新，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 更新包括如下更改：修补托管环境或升级以及解除硬件授权。 大多数此类更新在执行时不会影响托管的虚拟机。 但是，也会存在更新产生影响的情况：

- 如果维护不需重启，Azure 会在更新主机时使用就地迁移来暂停 VM。

- 如果维护需重启，你会收到一个通知，其中会说明计划维护的时间。 在这些情况下，系统会提供一个时间窗口，方便你在适合自己的时间自行启动维护。


需要重新启动的计划内维护是按批进行计划的。 每个批具有不同的作用域（区域）。

- 一个批从向客户发送通知开始。 默认情况下，向订阅所有者和共同所有者发送通知。 可以向通知添加更多收件人和消息传送选项（如电子邮件、短信和 Webhook）。  
- 发出通知不久，将设置自助式维护时段。 在此时段内可以找到包含在此批中的虚拟机，并开始通过主动重新部署来进行维护。 
- 自助式维护时段过后，就会开始计划内维护时段。 在此期间，Azure 会计划所需的维护，并将其应用于虚拟机。 

设置这两个时段的目的是，在了解 Azure 何时将自动启动维护时，提供足够的时间来启动维护和重新启动虚拟机。


可以使用 Azure 门户、PowerShell、REST API 和 CLI 查询 VM 的维护时段并启动自助式维护。

 > [!NOTE]
 > 如果尝试启动维护失败，Azure 会将 VM 标记为“已跳过”，并在计划内维护时段期间不重新启动它。 而是，稍后通过新计划与你联系。 


[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]

## <a name="check-maintenance-status-using-powershell"></a>使用 PowerShell 检查维护状态

还可以使用 Azure Powershell 查看 VM 计划何时维护。 使用 `-status` 参数时可通过 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) cmdlet 获得计划内维护信息。
 
仅当有计划内维护时，才会返回维护信息。 如果未计划任何影响 VM 的维护，该 cmdlet 不返回任何维护信息。 

```powershell
Get-AzureRmVM -ResourceGroupName rgName -Name vmName -Status
```

在 MaintenanceRedeployStatus 下返回以下属性： 
| 值 | 说明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指示此时是否可以在 VM 上启动维护 ||
| PreMaintenanceWindowStartTime         | 可以在 VM 上启动维护的自助式维护时段的起点 ||
| PreMaintenanceWindowEndTime           | 可以在 VM 上启动维护的自助式维护时段的终点 ||
| MaintenanceWindowStartTime            | 可以在 VM 上启动维护的计划内维护时段的起点 ||
| MaintenanceWindowEndTime              | 可以在 VM 上启动维护的计划内维护时段的终点 ||
| LastOperationResultCode               | 上次尝试在 VM 上启动维护的结果 ||



还可以通过使用 [Get-AzureRmVM](/powershell/module/azurerm.compute/get-azurermvm) 并不指定 VM 来获取资源组中所有 VM 的维护状态。
 
```powershell
Get-AzureRmVM -ResourceGroupName rgName --Status
```

以下 PowerShell 函数获取订阅 ID，并输出计划维护的 VM 列表。

```powershell

function MaintenanceIterator
{
    Select-AzureRmSubscription -SubscriptionId $args[0]

    $rgList= Get-AzureRmResourceGroup 

    for ($rgIdx=0; $rgIdx -lt $rgList.Length ; $rgIdx++)
    {
        $rg = $rgList[$rgIdx]
        $vmList = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName 
        for ($vmIdx=0; $vmIdx -lt $vmList.Length ; $vmIdx++)
        {
            $vm = $vmList[$vmIdx]
            $vmDetails = Get-AzureRMVM -ResourceGroupName $rg.ResourceGroupName -Name $vm.Name -Status
              if ($vmDetails.MaintenanceRedeployStatus )
            {
                Write-Output "VM: $($vmDetails.Name)  IsCustomerInitiatedMaintenanceAllowed: $($vmDetails.MaintenanceRedeployStatus.IsCustomerInitiatedMaintenanceAllowed) $($vmDetails.MaintenanceRedeployStatus.LastOperationMessage)"               
            }
          }
    }
}

```

### <a name="start-maintenance-on-your-vm-using-powershell"></a>使用 PowerShell 在 VM 上启动维护

如果 **IsCustomerInitiatedMaintenanceAllowed** 设置为 true，以下命令使用上一部分中函数的信息，在 VM 上启动维护。

```powershell
Restart-AzureRmVM -PerformMaintenance -name $vm.Name -ResourceGroupName $rg.ResourceGroupName 
```
 

## <a name="next-steps"></a>后续步骤

了解如何使用[计划事件](scheduled-events.md)从 VM 内注册维护事件。
