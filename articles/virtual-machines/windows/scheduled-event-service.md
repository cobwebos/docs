---
title: 监视 Azure 中 Windows Vm 的计划事件
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
ms.openlocfilehash: 1cda07c18e4f5ef2a8c00b6a275f22ecc0935751
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/14/2019
ms.locfileid: "74073317"
---
# <a name="monitoring-scheduled-events"></a>监视计划事件

每天都有更新应用到 Azure 的不同组成部分，使其中的服务保持安全和最新状态。 除了计划内更新以外，还可能发生计划外事件。 例如，如果检测到任何硬件降级或故障，Azure 服务可能需要执行计划外维护。 使用实时迁移时，内存预留将会更新，同时，更新所造成的影响通常受到严密跟踪，在大多数情况下，这些事件对于客户而言几乎是透明的，不会对他们造成任何影响，或者最多只会造成虚拟机冻结几秒钟。 但是，对于某些应用程序而言，即使是几秒钟的虚拟机冻结，也可能会造成影响。 提前了解即将进行的 Azure 维护非常重要，这可以确保为这些应用程序提供的最佳体验。 [计划事件服务](scheduled-events.md)提供一个编程接口，即将进行维护时它会发出通知，并使你能够正确处理维护。 

本文将会介绍如何使用计划事件来接收有关可能影响 VM 的维护事件的通知，并构建某种简单的自动化机制来帮助进行监视和分析。


## <a name="routing-scheduled-events-to-log-analytics"></a>将计划事件路由到 Log Analytics

计划事件作为 [Azure 实例元数据服务](instance-metadata-service.md)的一部分提供，该服务已在每个 Azure 虚拟机上提供。 客户可以编写自动化代码来查询其虚拟机的终结点，以查找计划性维护通知并执行缓解措施，例如，保存状态，并从轮换列表中删除其虚拟机。 我们建议生成自动化代码来记录计划事件，以便可以获取 Azure 维护事件的审核日志。 

本文逐步介绍如何将维护计划事件捕获到 Log Analytics。 然后，将触发一些基本的通知操作，例如，将电子邮件发送给团队，并获取对虚拟机造成了影响的所有事件的历史视图。 对于事件聚合与自动化，我们将使用 [Log Analytics](/azure/azure-monitor/learn/quick-create-workspace)，但你可以使用任何监视解决方案来收集这些日志并触发自动化。

![显示事件生命周期的示意图](./media/notifications/events.png)

## <a name="prerequisites"></a>先决条件

对于本示例，需要[在可用性集中创建一个 Windows 虚拟机](tutorial-availability-sets.md)。 对于可能会影响可用性集、云服务、虚拟机规模集或独立 VM 中的任何虚拟机的更改，计划事件会提供相关的通知。 我们将运行一个[服务](https://github.com/microsoft/AzureScheduledEventsService)来轮询充当收集器的某个 VM 上的计划事件，以获取可用性集中所有其他 VM 的事件。    

在本教程结束时，请不要删除组资源组。

还需要[创建一个 Log Analytics 工作区](/azure/azure-monitor/learn/quick-create-workspace)，用于从可用性集中的 VM 聚合信息。

## <a name="set-up-the-environment"></a>设置环境

现在，可用性集中应有 2 个初始 VM。 现在，需要在同一个可用性集中创建名为 myCollectorVM 的第 3 个 VM。 

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
 

从 [GitHub](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip) 下载项目的 .zip 安装文件。

连接到 **myCollectorVM**，将该 .zip 文件复制到虚拟机并解压缩其中的所有文件。 在 VM 上打开 PowerShell 提示符。 在提示符下切换到包含 `SchService.ps1` 的文件夹（例如 `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>`），然后设置服务。

```powershell
.\SchService.ps1 -Setup
```

启动该服务。

```powershell
.\SchService.ps1 -Start
```

现在，该服务将开始每隔 10 秒轮询所有计划事件，并审批事件以加速维护。  “冻结”、“重新启动”、“重新部署”和“抢占”是计划事件捕获的事件。   请注意，可以扩展脚本，以便在审批事件之前触发某些缓解措施。

验证服务状态，确保它正在运行。

```powershell
.\SchService.ps1 -status  
```

此命令应返回 `Running`。

现在，该服务将开始每隔 10 秒轮询所有计划事件，并审批事件以加速维护。  “冻结”、“重新启动”、“重新部署”和“抢占”是计划事件捕获的事件。 可以扩展脚本，以便在审批事件之前触发某些缓解措施。

当计划事件服务捕获到上述任一事件时，该事件将记录到“应用程序事件日志事件状态”、“事件类型”、“资源”（虚拟机名称）和“不早于”（最小通知期限）中。 可以在应用程序事件日志中找到 ID 为 1234 的事件。

设置并启动服务后，它会将事件记录到 Windows 应用程序日志中。   若要验证是否可正常执行此操作，请重启可用性集中的某个虚拟机，然后，应会在事件查看器的“Windows 日志”>“应用程序日志”中看到记录了一个事件，其中显示 VM 已重启。 

![事件查看器的屏幕截图。](./media/notifications/event-viewer.png)

当计划事件服务捕获到事件时，该事件将记录到应用程序事件日志中，并显示“事件状态”、“事件类型”、“资源”（VM 名称）和“不早于”（最小通知期限）属性。 可以在应用程序事件日志中找到 ID 为 1234 的事件。

> [!NOTE] 
> 在本示例中，虚拟机位于可用性集中，因此，我们可将一个虚拟机指定为收集器，以侦听计划事件并将其路由到 Log Analytics 工作区。 如果你有独立的虚拟机，可在每个虚拟机上运行该服务，然后将这些虚拟机分别连接到 Log Analytics 工作区。
>
> 在我们的设置中，我们选择了“Windows”，但你可以在 Linux 上设计类似的解决方案。

随时可以使用开关 `–stop` 和 `–remove` 来停止/删除计划事件服务。

## <a name="connect-to-the-workspace"></a>连接到工作区


现在，我们想要将 Log Analytics 工作区连接到收集器 VM。 Log Analytics 工作区充当存储库。我们将配置事件日志收集以从收集器 VM 捕获应用程序日志。 

 若要将计划事件路由到事件日志（服务会将其保存为应用程序日志），需要将虚拟机连接到 Log Analytics 工作区。  
 
1. 打开所创建的工作区的页面。
1. 在“连接到数据源”下，选择“Azure 虚拟机(VM)”。

    ![连接到用作数据源的 VM](./media/notifications/connect-to-data-source.png)

1. 搜索并选择“myCollectorVM”。 
1. 在“myCollectorVM”的新页面上，选择“连接”。

这会在虚拟机中安装 [Microsoft 监视代理](/azure/virtual-machines/extensions/oms-windows)。 将 VM 连接到工作区并安装扩展的过程需要几分钟时间。 

## <a name="configure-the-workspace"></a>配置工作区

1. 打开工作区的页面，选择“高级设置”。
1. 在左侧菜单中选择“数据”，然后选择“Windows 事件日志”。
1. 在“从以下事件日志收集”中键入“应用程序”，然后从列表中选择“应用程序”。

    ![选择“高级设置”](./media/notifications/advanced.png)

1. 保留“错误”、“警告”和“信息”，然后选择“保存”以保存设置。


> [!NOTE]
> 此时会出现一定的延迟，最长可能需要在 10 分钟之后才会显示日志。 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>使用 Azure Monitor 创建警报规则 


将事件推送到 Log Analytics 后，可运行以下[查询](/azure/azure-monitor/log-query/get-started-portal)来查找计划事件。

1. 在页面顶部选择“日志”，将以下内容粘贴到文本框中：

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

1. 选择“保存”，键入 **logQuery** 作为名称，保留“查询”作为类型，键入 *VMLogs* 作为**类别**，然后选择“保存”。 

    ![保存查询](./media/notifications/save-query.png)

1. 选择“新建警报规则”。 
1. 在“创建规则”页中，保留  **作为**资源`collectorworkspace`。
1. 在“条件”下，选择条目“每当客户日志搜索为  **时”。** *<login undefined>* 此时将打开“配置信号逻辑”页。
1. 在“阈值”下输入 **0**，然后选择“完成”。
1. 在“操作”下，选择“创建操作组”。 此时将打开“添加操作组”页。
1. 在“操作组名称”中键入 **myActionGroup**。
1. 在“短名称”中键入 **myActionGroup**。
1. 在 "**资源组**" 中，选择**myResourceGroupAvailability**。
1. 在 "操作" 下，在 "**操作名称**" 中键入**email**，然后选择**电子邮件/短信/推送/语音**。 **电子邮件/短信/推送/语音**页面将打开。
1. 选择“电子邮件”，键入电子邮件地址，然后选择“确定”。
1. 在“添加操作组”页中选择“确定”。 
1. 在“创建规则”页中的“警报详细信息”下，为“警报规则名称”键入 **myAlert**，然后为“说明”键入“电子邮件警报规则”。
1. 完成后，选择“创建警报规则”。
1. 重启可用性集中的一个 VM。 几分钟后，你应会收到一封电子邮件，指出已触发该警报。

若要管理警报规则，请转到资源组，在左侧菜单中选择“警报”，然后在页面顶部选择“管理警报规则”。

     
## <a name="next-steps"></a>后续步骤

有关详细信息，请参阅 GitHub 上的[计划事件服务](https://github.com/microsoft/AzureScheduledEventsService)页。
