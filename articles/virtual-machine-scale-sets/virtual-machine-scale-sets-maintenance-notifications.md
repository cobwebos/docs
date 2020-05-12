---
title: Azure 中虚拟机规模集的维护通知
description: 查看 Azure 中虚拟机规模集的维护通知并开始自助式维护。
author: mimckitt
ms.author: mimckitt
ms.topic: conceptual
ms.service: virtual-machine-scale-sets
ms.subservice: management
ms.date: 08/20/2019
ms.reviewer: jushiman
ms.custom: mimckitt
ms.openlocfilehash: c4b0cb8204891538ef9c4eef3fa0ff5fd9686536
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83200090"
---
# <a name="planned-maintenance-notifications-for-virtual-machine-scale-sets"></a>虚拟机规模集的计划内维护通知


Azure 定期执行更新，以提高虚拟机 (VM) 的主机基础结构的可靠性、性能及安全性。 更新可能包括修补托管环境或升级以及解除硬件授权。 大多数更新不影响托管的 VM。 但是在以下情况下，更新会影响 VM：

- 如果维护不需重启，Azure 会在更新主机时使用就地迁移来暂停 VM。 无需重启的维护操作会按逐个容错域方式进行应用。 如果接收到任何警告健康状况信号，则进程将停止。

- 如果维护需重新启动，系统会告知计划维护的时间。 在这些情况下，系统会提供一个时间窗口（通常为 35 天），方便你在适当的时间自行启动维护。


需要重启的计划内维护是按批进行计划的。 每个批具有不同的作用域（区域）：

- 一个批从向客户发送通知开始。 默认情况下，向订阅所有者和共同所有者发送通知。 可以使用 Azure [活动日志警报](../azure-monitor/platform/platform-logs-overview.md)，向通知添加收件人和消息发送选项（如电子邮件、短信和 Webhook）。  
- 出现通知时会提供自助时段  。 在此窗口（通常为 35 天）内，可以发现此批中包括了哪些 VM。 可以根据自身计划需要，主动启动维护。
- 自助时段过后，就会开始计划内维护时段。  在此时段的某个时刻，Azure 会计划所需的维护，并将其应用于 VM。 

设置这两个时段的目的是，在了解 Azure 何时将自动启动维护时，提供足够的时间来启动维护和重新启动 VM。

可以使用 Azure 门户、PowerShell、REST API 和 Azure CLI 查询虚拟机规模集 VM 的维护时段并启动自助维护。

## <a name="should-you-start-maintenance-during-the-self-service-window"></a>是否应在自助时段启动维护？  

以下指南可帮助你决定是否在所选时间启动维护。

> [!NOTE] 
> 自助维护不一定适用于所有 VM。 要确定是否可以对 VM 进行主动重新部署，请在维护状态中查找“立即启动”。  自助维护目前不适用于 Azure 云服务（Web/辅助角色）和 Azure Service Fabric。


不推荐将自助维护用于使用可用性集的部署  。 可用性集是高度可用的设置，其中任何时候仅一个更新域会受到影响。 对于可用性集，请注意以下事项：

- 让 Azure 触发维护。 对于需要重启的维护，会按更新域依次执行维护。 更新域不一定按顺序接收维护。 更新域之间存在 30 分钟的暂停。
- 如果担心暂时性丢失部分容量（1/更新域计数），通过在维护期间分配更多的实例即可轻松弥补容量丢失。
- 对于无需重启的维护，将在容错域级别应用更新。 
    
以下情况**请勿**使用自助维护： 

- 通过手动方式、开发测试实验室、自动关闭或按照一定时间计划频繁关闭虚拟机。 在这些情况下，自助维护可能还原维护状态和造成额外故障事件。
- VM 的生存期短，已确定在维护结束之前就会被删除。 
- 工作负荷的状态为“大”，存储在本地（临时）磁盘中，这些磁盘需要在更新后进行维护。 
- 经常重设 VM 大小。 这种情况可能还原维护状态。 
- 已采用的计划事件允许在维护关闭开始前 15 分钟对工作负载执行主动故障转移或正常关闭。

如果打算在计划性维护阶段不间断地运行 VM，而且上述禁忌均不适用，则可使用自助维护  。 

以下情况最好使用自助维护：

- 需要向管理层或客户告知确切的维护时段。 
- 需要在特定日期前完成维护。 
- 需要控制维护顺序，例如，应用程序为多层应用程序，需要确保安全地进行恢复。
- 在两个更新域之间，需要的 VM 恢复时间超出 30 分钟。 为了控制更新域之间的时间，一次只能在一个更新域的 VM 上触发维护。

 
## <a name="view-virtual-machine-scale-sets-that-are-affected-by-maintenance-in-the-portal"></a>在门户中查看受维护影响的虚拟机规模集

安排了大量计划内维护后，可以使用 Azure 门户查看受即将到来的大量维护影响的虚拟机规模集列表。 

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“所有服务”，然后选择“虚拟机规模集”   。
3. 在“虚拟机规模集”下，选择“编辑列”打开可用列的列表   。
4. 在“可用列”  部分中，选择“自助维护”  ，然后将其移至“选定的列”  列表中。 选择“应用”。   

    为便于查找“自助维护”项，可将“可用列”部分中的下拉列表选项从“全部”更改为“属性”     。

现在，“自助维护”列将显示在虚拟机规模集的列表中  。 每个虚拟机规模集可以具有以下自助维护列的值之一：

| Value | 说明 |
|-------|-------------|
| 是 | 虚拟机规模集中至少有一个 VM 处于自助时段。 你可以在此自助时段随时启动维护。 | 
| 否 | 受影响的虚拟机规模集中的自助时段中没有任何 VM。 | 
| - | 你的虚拟机规模集不属于大量的计划内维护。| 

## <a name="notification-and-alerts-in-the-portal"></a>门户中的通知和警报

Azure 通过向订阅所有者和共有者组发送电子邮件来传达计划维护的安排。 可以通过创建活动日志警报，为此通信添加收件人和通道。 有关详细信息，请参阅[使用 Azure 活动日志监视订阅活动](../azure-monitor/platform/platform-logs-overview.md)。

1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，选择“监视”  。 
3. 在“监视 - 警报(经典)”窗格中，选择“+添加活动日志警报”   。
4. 在“添加活动日志警报”页面中，选择或输入要求的信息  。 在“条件”中，确保设置以下值  ：
   - **事件类别**：选择“服务运行状况”  。
   - **服务**：选择“虚拟机规模集和虚拟机”  。
   - **类型**：选择“计划内维护”  。 
    
要详细了解如何配置活动日志警报，请参阅[创建活动日志警报](../azure-monitor/platform/activity-log-alerts.md)
    
    
## <a name="start-maintenance-on-your-virtual-machine-scale-set-from-the-portal"></a>从门户中开始维护虚拟机规模集

在虚拟机规模集概述中可查看更多与维护相关的详细信息。 如果至少一个虚拟机规模集中的 VM 包括在大量的计划内维护中，则将会在页面顶部附近添加新的通知功能区。 选择通知功能区，转到“维护”页面  。 

在“维护”页面上可以查看受计划内维护影响的 VM 实例  。 要启动维护，请选择受影响 VM 对应的复选框。 然后选择“开始维护”  。

开始维护后，虚拟机规模集中受影响的 VM 将接受维护并暂时不可用。 如果错过了自助时段，则由 Azure 维护虚拟机规模集时，你将仍可以看到该时段。
 
## <a name="check-maintenance-status-by-using-powershell"></a>使用 PowerShell 查看维护状态

可以使用 Azure PowerShell 查看虚拟机规模集中的 VM 计划何时维护。 使用 `-InstanceView` 参数时可通过使用 [Get-AzVmss](https://docs.microsoft.com/powershell/module/az.compute/get-azvmss) cmdlet 获得计划内维护信息。
 
仅当有计划内维护时，才会返回维护信息。 如果未计划影响 VM 实例的维护，则 cmdlet 不会返回任何维护信息。 

```powershell
Get-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -InstanceView
```

在 MaintenanceRedeployStatus 下返回以下属性  ： 

| Value | 说明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指示此时是否可以在 VM 上启动维护。 |
| PreMaintenanceWindowStartTime         | 可以在 VM 上启动维护的自助式维护时段的起点。 |
| PreMaintenanceWindowEndTime           | 可以在 VM 上启动维护的自助式维护时段的终点。 |
| MaintenanceWindowStartTime            | Azure 在 VM 上启动维护的计划内维护时段的起点。 |
| MaintenanceWindowEndTime              | Azure 在 VM 上启动维护的计划内维护时段的终点。 |
| LastOperationResultCode               | 上次尝试在 VM 上启动维护的结果。 |



### <a name="start-maintenance-on-your-vm-instance-by-using-powershell"></a>使用 PowerShell 在 VM 实例上启动维护

如果 IsCustomerInitiatedMaintenanceAllowed 设置为 true，则可以在 VM 上启动维护   。 使用含 `-PerformMaintenance` 参数的 [Set-AzVmss](/powershell/module/az.compute/set-azvmss) cmdlet。

```powershell
Set-AzVmss -ResourceGroupName rgName -VMScaleSetName vmssName -InstanceId id -PerformMaintenance 
```

## <a name="check-maintenance-status-by-using-the-cli"></a>使用 CLI 查看维护状态

可以使用 [az vmss list-instances](/cli/azure/vmss?view=azure-cli-latest#az-vmss-list-instances) 查看计划内维护信息。
 
仅当有计划内维护时，才会返回维护信息。 如果未计划影响 VM 实例的维护，则该命令不会返回任何维护信息。 

```azurecli
az vmss list-instances -g rgName -n vmssName --expand instanceView
```

在每个 VM 实例的 MaintenanceRedeployStatus 下返回以下属性  ： 

| Value | 说明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指示此时是否可以在 VM 上启动维护。 |
| PreMaintenanceWindowStartTime         | 可以在 VM 上启动维护的自助式维护时段的起点。 |
| PreMaintenanceWindowEndTime           | 可以在 VM 上启动维护的自助式维护时段的终点。 |
| MaintenanceWindowStartTime            | Azure 在 VM 上启动维护的计划内维护时段的起点。 |
| MaintenanceWindowEndTime              | Azure 在 VM 上启动维护的计划内维护时段的终点。 |
| LastOperationResultCode               | 上次尝试在 VM 上启动维护的结果。 |


### <a name="start-maintenance-on-your-vm-instance-by-using-the-cli"></a>使用 CLI 在 VM 实例上启动维护

如果 `IsCustomerInitiatedMaintenanceAllowed` 设置为 true，以下调用会在 VM 实例上启动维护  ：

```azurecli
az vmss perform-maintenance -g rgName -n vmssName --instance-ids id
```

## <a name="faq"></a>常见问题

**问：为什么需要立即重新启动 VM？**

**答:** 尽管 Azure 平台大多数更新和升级不会影响 VM 可用性，但是在某些情况下，无法避免重启 Azure 中托管的 VM。 我们累积了多个需要重启服务器的更改，这将导致重新启动 VM。

**问：如果我按建议使用可用性集实现高可用性，我是否安全？**

**答:** 可用性集或虚拟机规模集中部署的虚拟机使用更新域。 执行维护时，Azure 遵循更新域约束，不会从其他更新域（在同一可用性集中）重新启动 VM。 Azure 还会至少等待 30 分钟，然后才移到下一组 VM。 

有关高可用性的详细信息，请参阅 [Azure 中虚拟机的区域和可用性](../virtual-machines/windows/availability.md)。

**问：如何收到有关计划内维护的通知？**

**答:** 一次计划内维护是通过将计划设置到一个或多个 Azure 区域启动的。 不久以后，电子邮件通知将发送到订阅所有者（每个订阅一封电子邮件）。 你可以使用活动日志警报为此通知添加通道和收件人。 如果将 VM 部署到其中已经安排有计划内维护的区域，则无法接收通知。 此时而应查看 VM 的维护状态。

**问：我在门户、PowerShell 或 CLI 中看不到任何计划内维护的指示。这是为何？**

**答:** 在计划内维护批次期间，与计划内维护相关的信息仅适用于受此计划内维护影响的 VM。 如果未显示有数据，则维护批次可能已经结束（或尚未开始），或者 VM 可能已经托管在已更新的服务器上。

**问：有什么方法可以知道 VM 受影响的确切时间？**

**答:** 设置计划时，我们定义了一个长达几天的时间段。 虽然服务器（和 VM）在此时段的确切重启顺序尚未可知， 如果要知道 VM 更新的确切时间，可使用[计划事件](../virtual-machines/windows/scheduled-events.md)。 如果使用计划事件，则可从 VM 内进行查询，并可在 VM 重启前 15 分钟内收到通知。

**问：重启 VM 需要多长时间？**

**答:** 根据 VM 的大小，在自助维护时段内，重启最多可能需要几分钟时间。 当 Azure 在计划性维护时段内启动重启时，重启通常需要 25 分钟左右。 如果使用云服务（Web/辅助角色）、虚拟机规模集或可用性集，则在计划性维护时段内每组 VM（更新域）之间有 30 分钟的可用时间。 

**问：我在 VM 上看不到任何维护信息，是哪里出错了？**

**答:** 有很多原因会导致在 VM 上看不到任何维护信息：
   - 使用的是标记为“Microsoft 内部”的订阅  。
   - VM 未计划进行维护。 可能是此次维护已结束、已取消或已修改，因此你的 VM 不再受其影响。
   - 未将“维护”列添加到 VM 列表视图。  虽然我们已向默认视图添加此列，但如果你将视图配置为查看非默认列，则仍需手动将“维护”列添加到 VM 列表视图  。

**问：我的 VM 已计划进行第二次维护，为什么？**

**答:** 在多种用例下，在维护和重新部署已经完成后，会对 VM 计划维护：
   - 我们已取消这次维护，并使用不同的有效负载重新启动它。 可能是我们已检测到出错的有效负载，只需部署其他有效负载。
   - 由于硬件故障，已在另一个节点上对 VM 进行服务修复。 
   - 选择了停止（解除分配）VM 并将其重启。
   - 已经为 VM 启用了**自动关闭**。

## <a name="next-steps"></a>后续步骤

了解如何使用[计划事件](../virtual-machines/windows/scheduled-events.md)从 VM 内注册维护事件。
