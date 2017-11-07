---
title: "处理 Azure 中 Linux VM 的维护通知 | Microsoft Docs"
description: "查看 Azure 中运行的 Linux 虚拟机的维护通知并开始自助式维护。"
services: virtual-machines-linux
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 10/26/2017
ms.author: zivr
ms.openlocfilehash: be062ce9cfbe7486ef500dd9d27418cbf245d6e0
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/27/2017
---
# <a name="handling-planned-maintenance-notifications-for-linux-virtual-machines"></a>处理 Linux 虚拟机的计划内维护通知

Azure 定期执行更新，以提高虚拟机的主机基础结构的可靠性、性能及安全性。 更新包括如下更改：修补托管环境或升级以及解除硬件授权。 大多数此类更新在执行时不会影响托管的虚拟机。 但是，也会存在更新产生影响的情况：

- 如果维护不需重启，Azure 会在更新主机时使用就地迁移来暂停 VM。

- 如果维护需重启，你会收到一个通知，其中会说明计划维护的时间。 在这些情况下，系统会提供一个时间窗口，方便我们在适当的时间自行启动维护。


需要重新启动的计划内维护是按批进行计划的。 每个批具有不同的作用域（区域）。

- 一个批从向客户发送通知开始。 默认情况下，向订阅所有者和共同所有者发送通知。 可以向通知添加更多收件人和消息传送选项（如电子邮件、短信和 Webhook）。 
- 发出通知不久，将设置自助式维护时段。 在此时段内可以找到包含在此批中的虚拟机，并开始通过主动重新部署来进行维护。 
- 自助式维护时段过后，就会开始计划内维护时段。 在此期间，Azure 会计划所需的维护，并将其应用于虚拟机。  

设置这两个时段的目的是，在了解 Azure 何时将自动启动维护时，提供足够的时间来启动维护和重新启动虚拟机。

可以使用 Azure CLI、PowerShell、REST API 和 Azure 门户查询 VM 的维护时段并启动自助式维护。

 > [!NOTE]
 > 如果尝试启动维护失败，Azure 会将 VM 标记为“已跳过”，并在计划内维护时段期间不重新启动它。 而是，稍后通过新计划与你联系。 

## <a name="find-vms-scheduled-for-maintenance-using-cli"></a>使用 CLI 查找计划用于维护的 VM

可以使用 [azure vm get-instance-view](/cli/azure/vm?view=azure-cli-latest#az_vm_get_instance_view) 查看计划内维护信息。
 
仅当有计划内维护时，才会返回维护信息。 如果未计划任何影响 VM 的维护，该命令不返回任何维护信息。 

```azure-cli
az vm get-instance-view  - g rgName  -n vmName 
```

在 MaintenanceRedeployStatus 下返回以下值： 

| 值 | 说明   |
|-------|---------------|
| IsCustomerInitiatedMaintenanceAllowed | 指示此时是否可以在 VM 上启动维护 ||
| PreMaintenanceWindowStartTime         | 可以在 VM 上启动维护的自助式维护时段的起点 ||
| PreMaintenanceWindowEndTime           | 可以在 VM 上启动维护的自助式维护时段的终点 ||
| MaintenanceWindowStartTime            | 可以在 VM 上启动维护的计划内维护时段的起点 ||
| MaintenanceWindowEndTime              | 可以在 VM 上启动维护的计划内维护时段的终点 ||
| LastOperationResultCode               | 上次尝试在 VM 上启动维护的结果 ||


## <a name="start-maintenance-on-your-vm-using-cli"></a>使用 CLI 在 VM 上启动维护

如果 `IsCustomerInitiatedMaintenanceAllowed` 设置为 true，以下调用会在 VM 上启动维护。

```azure-cli
az vm perform-maintenance rgName vmName 
```

[!INCLUDE [virtual-machines-common-maintenance-notifications](../../../includes/virtual-machines-common-maintenance-notifications.md)]


## <a name="faq"></a>常见问题


**问：为什么需要立即重新启动虚拟机？**

**答：**虽然对 Azure 平台的大多数更新和升级不会影响虚拟机的可用性，但在某些情况下无法避免重新启动 Azure 中托管的虚拟机。 我们累积了多个需要重启服务器的更改，这将导致重新启动虚拟机。

**问：如果我按建议使用可用性集实现高可用性，我是否安全？**

**答：**可用性集或虚拟机规模集中部署的虚拟机具有更新域 (UD) 的概念。 执行维护时，Azure 遵循 UD 约束，不会从不同 UD（在同一可用性集中）重新启动虚拟机。  Azure 还会至少等待 30 分钟，然后才移到下一组虚拟机。 

有关高可用性的详细信息，请参阅“在 Azure 中管理 Windows 虚拟机的可用性”或“在 Azure 中管理 Linux 虚拟机的可用性”。

**问：我已在另一个区域中设置灾难恢复。我是否安全？**

**答：**每个 Azure 区域都与同一地理位置（例如美国、欧洲或亚洲）中的另一区域配对。 将逐一对配对的区域进行计划内 Azure 更新，尽量减少停机时间并降低应用程序中断风险。 在计划内维护期间，Azure 可能会为用户计划类似的时段来启动维护，但在配对区域之间计划的维护时段将是不同的。  

有关 Azure 区域的详细信息，请参阅“Azure 中虚拟机的区域和可用性”。  可在此处查看完整的区域对列表。

**问：如何收到有关计划内维护的通知？**

**答：**计划内维护波是通过将计划设置到一个或多个 Azure 区域启动的。 不久以后，电子邮件通知将发送到订阅所有者（每个订阅一封电子邮件）。 可以使用活动日志警报配置此通知的其他通道和收件人。 如果将虚拟机部署到已安排计划内维护的区域，将不会收到通知，而是需要检查 VM 的维护状态。

**问：我在门户、Powershell 或 CLI 中看不到任何计划内维护的指示，是哪里出错了？**

**答：**在计划内维护波期间，与计划内维护相关的信息仅适用于将受到计划内维护波影响的 VM。 换而言之，如果看不到数据，则可能是维护波已完成（或未启动）或虚拟机已在更新的服务器中托管。

**问：是否应在虚拟机上启动维护？**

**答：**一般情况下，部署在云服务、可用性集或虚拟机规模集中的工作负荷可以弹性应对计划内维护。  在计划内维护期间，仅一个更新域会在任意指定时间受影响。 请注意，不一定按顺序来影响更新域。

在以下情况下可能需要自己启动维护：
- 应用程序在单个虚拟机上运行，并且需要在非工作时间应用所有维护
- 需要在 SLA 中协调维护时间
- 即使在可用性集中，每个 VM 重启的间隔时间也需要超过 30 分钟。
- 需要关闭整个应用程序（多个层、多个更新域），以便更快地完成维护。

**问：有什么方法可以知道虚拟机将受到影响的确切时间？**

**答：**设置计划时，我们定义了长达几天的时间窗口。 但是，服务器（和 VM）在此时间窗口内的确切排序是未知的。 想要知道其 VM 确切时间的客户可以使用计划事件和从虚拟机中查询，以便在 VM 重新启动前 10 分钟收到通知。
  
**问：重新启动虚拟机需要多长时间？**

**答：**根据 VM 的大小，重新启动最多可能需要几分钟时间。 请注意，如果使用云服务、规模集或可用性集，将在每组 VM (UD) 之间提供 30 分钟时间。 

**问：如果使用云服务、规模集和 Service Fabric，会有什么样的体验？**

**答：**虽然这些平台会受到计划内维护的影响，但使用这些平台的客户会认为安全，因为在任何给定时间，只有单个升级域 (UD) 中的 VM 会受到影响。  

**问：我收到了一封有关硬件解除的电子邮件，这与计划内维护相同吗？**

**答：**虽然硬件解除授权是计划内维护事件，但我们尚未将此用例加入新体验。  我们想如果客户收到两封关于两个不同的计划内维护波的类似电子邮件会感到困惑。

**问：我在 VM 上看不到任何维护信息，是哪里出错了？**

**答：**有很多原因会导致在 VM 上看不到任何维护信息：
1.  使用的是标记为“Microsoft 内部”的订阅。
2.  VM 未计划进行维护。 可能维护波已结束、已取消或已修改，因此 VM 不再受其影响。
3.  未将“维护”列添加到 VM 列表视图。 虽然我们已向默认视图添加此列，但配置为查看非默认列的客户必须手动将“维护”列添加到其 VM 列表视图。

**问：我的 VM 已计划进行第二次维护，为什么？**

**答：**在多种用例下，在已完成维护性的重新部署后，会看到 VM 已计划进行维护：
1.  我们已取消维护波，并已使用不同的有效负载重启它。 可能是我们已检测到出错的有效负载，只需部署其他有效负载。
2.  由于硬件故障，已在另一个节点上对 VM 进行服务修复
3.  选择了停止（解除分配）VM 并将其重启
4.  已经为 VM 启用了**自动关闭**


## <a name="next-steps"></a>后续步骤

了解如何使用[计划事件](scheduled-events.md)从 VM 内注册维护事件。