---
title: 监视 Azure 中 Windows Vm 的计划事件 |Microsoft Docs
description: 了解如何监视 Azure 虚拟机的计划事件。
services: virtual-machines-windows
documentationcenter: ''
author: mysarn
manager: gwallace
ms.service: virtual-machines-windows
ms.tgt_pltfrm: vm-windows
ms.date: 08/20/2019
ms.author: sarn
ms.topic: conceptual
ms.openlocfilehash: 49c82339e5a3774cd286d700d709371d46cf0571
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051856"
---
# <a name="monitoring-scheduled-events"></a>监视 Scheduled Events

每日将更新应用到 Azure 的不同部分, 以使这些服务在安全和最新的状态下运行。 除了计划的更新以外, 还可能会发生计划外事件。 例如, 如果检测到任何硬件降级或故障, Azure 服务可能需要执行计划外维护。 使用实时迁移时, 内存保留更新, 并且通常会对更新的影响进行严格的跟踪, 在大多数情况下, 这些事件对于客户来说几乎是透明的, 并且不会产生任何影响, 也不会导致虚拟机冻结几秒钟。 但是, 对于某些应用程序, 甚至几秒钟的虚拟机冻结可能会造成影响。 提前了解即将到来的 Azure 维护非常重要, 可确保这些应用程序的最佳体验。 [Scheduled Events 服务](scheduled-events.md)提供了一个编程接口, 用于通知即将进行的维护, 并使你能够正确地处理维护。 

在本文中, 我们将演示如何使用计划事件来通知可能会影响 Vm 的维护事件, 并构建一些有助于监视和分析的基本自动化。


## <a name="routing-scheduled-events-to-log-analytics"></a>将计划事件路由到 Log Analytics

Scheduled Events 在[Azure 实例元数据服务](instance-metadata-service.md)中提供, 可在每个 azure 虚拟机上使用。 客户可以编写自动化以查询其虚拟机的终结点, 以查找计划的维护通知并执行缓解, 如保存状态并使虚拟机退出旋转。 建议生成自动化来记录 Scheduled Events, 以便可以获得 Azure 维护事件的审核日志。 

本文介绍如何捕获要 Log Analytics 的维护 Scheduled Events。 接下来, 我们将触发一些基本的通知操作, 例如将电子邮件发送给你的团队, 并获取对虚拟机有影响的所有事件的历史视图。 对于事件聚合和自动化, 我们将使用[Log Analytics](/azure/azure-monitor/learn/quick-create-workspace), 但你可以使用任何监视解决方案来收集这些日志, 并触发自动化。

![显示事件生命周期的关系图](./media/notifications/events.png)

## <a name="prerequisites"></a>先决条件

在此示例中, 你将需要[在可用性集中创建 Windows 虚拟机](tutorial-availability-sets.md)。 Scheduled Events 提供有关可能影响可用性集中的任何虚拟机、云服务、虚拟机规模集或独立 Vm 的更改的通知。 我们将运行一项[服务](https://github.com/microsoft/AzureScheduledEventsService), 该服务在将充当收集器的某个 vm 上轮询计划事件, 以获取可用性集中所有其他 vm 的事件。    

请不要在本教程结束时删除组资源组。

还需要[创建一个 Log Analytics 工作区, 该工作区](/azure/azure-monitor/learn/quick-create-workspace)将用于聚合来自可用性集中的 vm 的信息。

## <a name="set-up-the-environment"></a>设置环境

现在, 可用性集中应有2个初始 Vm。 现在, 我们需要在同一可用性集中创建名为 myCollectorVM 的第三个 VM。 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

从[GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip)下载项目的安装 .zip 文件。

连接到**myCollectorVM**并将 .zip 文件复制到虚拟机, 并提取所有文件。 在 VM 上, 打开 PowerShell 提示符。 将提示移动到包含`SchService.ps1`的文件夹, 例如: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`, 并设置服务。

```powershell
.\SchService.ps1 -Setup
```

启动服务。

```powershell
.\SchService.ps1 -Start
```

对于任何计划的事件, 该服务现在都将开始每10秒轮询一次, 并批准事件以加速维护。  "冻结"、"重新启动"、"重新部署" 和 "抢占" 是通过计划事件捕获的事件。   请注意, 你可以在批准事件之前扩展脚本以触发某些缓解措施。

验证服务状态, 并确保其正在运行。

```powershell
.\SchService.ps1 -status  
```

这应返回`Running`。

对于任何计划的事件, 该服务现在都将开始每10秒轮询一次, 并批准事件以加速维护。  冻结、重新启动、重新部署和抢占是通过计划事件捕获的事件。 在批准事件之前, 可以扩展脚本以触发某些缓解措施。

当 "计划事件" 服务捕获上述任何事件时, 它将记录在应用程序事件日志事件状态、事件类型、资源 (虚拟机名称) 和 NotBefore (最小通知期)。 可以在应用程序事件日志中找到 ID 为1234的事件。

设置并启动服务后, 它会将事件记录到 Windows 应用程序日志中。   若要验证此操作是否有效, 请在可用性集中重新启动其中一个虚拟机, 并应在 Windows 日志中看到一个事件记录在事件查看器中 > 应用程序日志显示 VM 已重新启动。 

![事件查看器的屏幕截图。](./media/notifications/event-viewer.png)

当 "计划" 事件服务捕获事件时, 该事件将记录在应用程序中, 甚至会记录事件状态、事件类型、资源 (VM 名称) 和 NotBefore (最小通知期)。 可以在应用程序事件日志中找到 ID 为1234的事件。

> [!NOTE] 
> 在此示例中, 虚拟机位于可用性集中, 这使我们能够将一个虚拟机指定为收集器来侦听和将计划事件路由到 log analytics 工作区。 如果你有独立的虚拟机, 则可以在每个虚拟机上运行该服务, 然后将它们分别连接到 log analytics 工作区。
>
> 对于我们的设置, 我们选择了 Windows, 但你可以在 Linux 上设计类似的解决方案。

随时都可以使用开关`–stop`和`–remove`来停止/删除计划事件服务。

## <a name="connect-to-the-workspace"></a>连接到工作区


现在, 我们想要将 Log Analytics 工作区连接到收集器 VM。 Log Analytics 工作区用作存储库, 我们将配置事件日志收集, 以便从收集器 VM 捕获应用程序日志。 

 若要将 Scheduled Events 路由到服务将另存为应用程序日志的事件日志, 需要将虚拟机连接到 Log Analytics 工作区。  
 
1. 打开所创建的工作区的页面。
1. 在 "**连接到数据源**" 下, 选择 " **Azure 虚拟机 (vm)** "。

    ![作为数据源连接到 VM](./media/notifications/connect-to-data-source.png)

1. 搜索并选择 " **myCollectorVM**"。 
1. 在**myCollectorVM**的新页上, 选择 "**连接**"。

这会在你的虚拟机中安装[Microsoft Monitoring agent](/azure/virtual-machines/extensions/oms-windows) 。 将 VM 连接到工作区并安装扩展需要几分钟时间。 

## <a name="configure-the-workspace"></a>配置工作区

1. 打开工作区页面, 并选择 "**高级设置**"。
1. 从左侧菜单中选择 "**数据**", 然后选择 " **Windows 事件日志**"。
1. 在 **"从以下事件日志收集**" 中, 开始键入*应用程序*, 然后从列表中选择 "**应用程序**"。

    ![选择高级设置](./media/notifications/advanced.png)

1. 保留 "**错误**"、"**警告**" 和 "**信息**", 然后选择 "**保存**" 以保存设置。


> [!NOTE]
> 会出现一些延迟, 可能需要长达10分钟的时间才能使用日志。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>使用 Azure Monitor 创建警报规则 


将事件推送到 Log Analytics 后, 可以运行以下[查询](/azure/azure-monitor/log-query/get-started-portal)来查找计划事件。

1. 在页面顶部, 选择 "**日志**", 并将以下内容粘贴到文本框中:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. 选择 "**保存**", 在 "名称" 中键入*LogQuery* , 将**Query**保留为类型, 键入*VMLogs*作为**类别**, 然后选择 "**保存**"。 

    ![保存查询](./media/notifications/save-query.png)

1. 选择“新建警报规则”。 
1. 在 "**创建规则**" 页中`collectorworkspace` , 将保留为**资源**。
1. 在 "**条件**" 下, 选择 *"客户<login undefined>日志搜索"* 时的条目。 "**配置信号逻辑**" 页将打开。
1. 在 "**阈值**" 下, 输入*0* , 然后选择 "**完成**"。
1. 在 "**操作**" 下, 选择 "**创建操作组**"。 "**添加操作组**" 页将打开。
1. 在 "**操作组名称**" 中, 键入*myActionGroup*。
1. 在 "**短名称**" 中, 键入**myActionGroup**。
1. 在 "**资源组**" 中, 选择 * myResourceGroupAvailability * *。
1. 在 "操作" 下, 在 "**操作名称**" 中键入**email**, 然后选择**电子邮件/短信/推送/语音**。 **电子邮件/短信/推送/语音**页面将打开。
1. 选择 "**电子邮件**", 键入电子邮件地址, 然后选择 **"确定"** 。
1. 在 "**添加操作组**" 页中, 选择 **"确定"** 。 
1. 在 "**创建规则**" 页的 "**警报详细信息**" 下, 键入*myAlert*作为**预警规则名称**, 然后键入 "*电子邮件预警规则*" 以了解**说明**。
1. 完成后, 选择 "**创建警报规则**"。
1. 重新启动可用性集中的某个 Vm。 几分钟后, 应该会收到一封电子邮件, 指出警报已被触发。

若要管理警报规则, 请在资源组中, 从左侧菜单中选择 "**警报**", 然后从页面顶部选择 "**管理警报规则**"。

     
## <a name="next-steps"></a>后续步骤

若要了解详细信息, 请参阅 GitHub 上的[计划事件服务](https://github.com/microsoft/AzureScheduledEventsService)页。
