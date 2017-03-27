---
title: "启动和停止虚拟机 - Graph | Microsoft Docs"
description: "Azure 自动化方案的 PowerShell 工作流版本，包括启动和停止经典虚拟机所需的 Runbook。"
services: automation
documentationcenter: 
author: mgoedtel
manager: jwhit
editor: tysonn
ms.assetid: 62d93170-ca3d-42ef-ac1d-6d50b61ac87d
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/06/2016
ms.author: magoedte;bwren
redirect_url: https://docs.microsoft.com/azure/automation/automation-solution-vm-management
redirect_document_id: FALSE
translationtype: Human Translation
ms.sourcegitcommit: 00b217a4cddac0a893564db27ffb4f460973c246
ms.openlocfilehash: ba67183cf0518bee102579a8ab16a9be92ed7b73

---

# <a name="azure-automation-scenario---starting-and-stopping-virtual-machines"></a>Azure 自动化方案 - 启动和停止虚拟机
此 Azure 自动化方案包括启动和停止经典虚拟机所需的 Runbook。  你可以将此方案用于以下任何情形：  

* 在你自己的环境中使用 Runbook，无需修改。
* 修改 Runbook 以执行自定义功能。  
* 从另一作为整体解决方案一部分的 Runbook 调用 Runbook。
* 使用 Runbook 作为教程来学习 Runbook 创作概念。

> [!div class="op_single_selector"]
> * [图形](automation-solution-startstopvm-graphical.md)
> * [PowerShell 工作流](automation-solution-startstopvm-psworkflow.md)
>
>

这是此方案的图形 Runbook 版本。 也可以使用 [PowerShell 工作流 Runbook](automation-solution-startstopvm-psworkflow.md)。

## <a name="getting-the-scenario"></a>获取方案
此方案包含两个可通过以下链接下载的图形 Runbook。  请参阅此方案的 [PowerShell 工作流版本](automation-solution-startstopvm-psworkflow.md)，以获取 PowerShell 工作流 Runbook 的链接。

| Runbook | 链接 | 类型 | 说明 |
|:--- |:--- |:--- |:--- |
| StartAzureClassicVM |[启动 Azure 经典 VM 图形 Runbook](https://gallery.technet.microsoft.com/scriptcenter/Start-Azure-Classic-VM-c6067b3d) |图形 |启动 Azure 订阅者中的所有经典虚拟机，或者启动所有具有特定服务名称的虚拟机。 |
| StopAzureClassicVM |[停止 Azure 经典 VM 图形 Runbook](https://gallery.technet.microsoft.com/scriptcenter/Stop-Azure-Classic-VM-397819bd) |图形 |停止自动化帐户中的所有虚拟机，或者停止所有具有特定服务名称的虚拟机。 |

## <a name="installing-and-configuring-the-scenario"></a>安装和配置方案
### <a name="1-install-the-runbooks"></a>1.安装 Runbook
下载 Runbook 之后，可以使用[图形 Runbook 过程](automation-graphical-authoring-intro.md#graphical-runbook-procedures)中的过程导入它们。

### <a name="2-review-the-description-and-requirements"></a>2.查看说明和要求
Runbook 包含一个名为 **Read Me** 的活动，其中包含说明和所需的资产。  选择“Read Me”活动，再选择“工作流脚本”参数，即可查看此信息。  你也可以从本文中获取相同的信息。

### <a name="3-configure-assets"></a>3.配置资产
Runbook 需要以下资产，你必须创建这些资产并在其中填充适当的值。  名称使用默认值。  可以用不同的名称来使用这些资产，但必须在启动 Runbook 时，先在[输入参数](#using-the-runbooks)中指定这些名称。

| 资产类型 | 默认名称 | 说明 |
|:--- |:--- |:--- |:--- |
| [凭据](automation-credentials.md) |AzureCredential |包含帐户凭据，该帐户有权在 Azure 订阅中启动和停止虚拟机。 |
| [变量](automation-variables.md) |AzureSubscriptionId |包含 Azure 订阅的订阅 ID。 |

## <a name="using-the-scenario"></a>使用方案
### <a name="parameters"></a>parameters
每个 Runbook 具有以下[输入参数](automation-starting-a-runbook.md#runbook-parameters)。  必需参数必须提供值，其他参数则可根据要求选择性地提供值。

| 参数 | 类型 | 必需 | 说明 |
|:--- |:--- |:--- |:--- |
| ServiceName |字符串 |否 |如果提供了值，则会启动或停止具有该服务名称的所有虚拟机。  如果没有提供值，则会启动或停止 Azure 订阅中的所有经典虚拟机。 |
| AzureSubscriptionIdAssetName |字符串 |否 |包含[变量资产](#installing-and-configuring-the-scenario)的名称，该资产中包含 Azure 订阅的订阅 ID。  如果不指定值，则会使用 *AzureSubscriptionId*。 |
| AzureCredentialAssetName |字符串 |否 |包含[凭据资产](#installing-and-configuring-the-scenario)的名称，该资产中包含要使用的 Runbook 的凭据。  如果不指定值，则会使用 *AzureCredential*。 |

### <a name="starting-the-runbooks"></a>启动 Runbook
可以使用[在 Azure 自动化中启动 Runbook](automation-starting-a-runbook.md) 中的任何方法来启动本文中的任一 Runbook。

以下示例命令使用 Windows PowerShell 来运行 **StartAzureClassicVM**，以便启动服务名称为 *MyVMService* 的所有虚拟机。

    $params = @{"ServiceName"="MyVMService"}
    Start-AzureAutomationRunbook –AutomationAccountName "MyAutomationAccount" –Name "StartAzureClassicVM" –Parameters $params

### <a name="output"></a>输出
这些 Runbook 会为每个虚拟机[输出一条消息](automation-runbook-output-and-messages.md)，指示是否已成功提交启动或停止指令。  你可以在输出中查找特定字符串，以确定每个 Runbook 的结果。  可能的输出字符串列在下表中。

| Runbook | 条件 | 消息 |
|:--- |:--- |:--- |
| StartAzureClassicVM |虚拟机已运行 |MyVM 已运行 |
| StartAzureClassicVM |已成功提交虚拟机启动请求 |MyVM 已启动 |
| StartAzureClassicVM |虚拟机启动请求失败 |MyVM 无法启动 |
| StopAzureClassicVM |虚拟机已运行 |MyVM 已停止 |
| StopAzureClassicVM |已成功提交虚拟机启动请求 |MyVM 已启动 |
| StopAzureClassicVM |虚拟机启动请求失败 |MyVM 无法启动 |

下图显示在示例图形 Runbook 中使用 **StartAzureClassicVM** 作为[子 Runbook](automation-child-runbooks.md)。  其中使用了下表中的条件链接。

| 链接 | 条件 |
|:--- |:--- |
| 成功链接 |$ActivityOutput['StartAzureClassicVM'] - 例如 "\* 已启动" |
| 错误链接 |$ActivityOutput['StartAzureClassicVM'] - 不是 "\* 已启动" |

![子 Runbook 示例](media/automation-solution-startstopvm/graphical-childrunbook-example.png)

## <a name="detailed-breakdown"></a>明细
下面是此方案中 Runbook 的明细。  你可以使用此信息来自定义 Runbook，或只是从中学习如何创作自己的自动化方案。

### <a name="authentication"></a>身份验证
![身份验证](media/automation-solution-startstopvm/graphical-authentication.png)

Runbook 首先使用活动来设置将要用于 Runbook 剩余部分的[凭据](automation-credentials.md)和 Azure 订阅。

前两个活动是**获取订阅 ID** 和**获取 Azure 凭据**，负责检索供后续两个活动使用的[资产](#installing-the-runbook)。  这些活动可以直接指定资产，但需要资产名称。  由于我们允许用户在[输入参数](#using-the-runbooks)中指定这些名称，因此需要这些活动，以根据输入参数所指定的名称来检索资产。

**Add-AzureAccount** 设置将用于 Runbook 剩余部分的凭据。  从**获取 Azure 凭据**检索的凭据资产必须有权启动和停止 Azure 订阅中的虚拟机。  所用的订阅通过 **Select-AzureSubscription** 使用**获取订阅 ID** 中的订阅 ID 进行选择。

### <a name="get-virtual-machines"></a>获取虚拟机
![获取 VM](media/automation-solution-startstopvm/graphical-getvms.png)

Runbook 必须确定将使用的虚拟机，以及这些虚拟机是否已启动或停止（取决于 Runbook）。   这两个活动中，其中一个检索 VM。  如果 Runbook 的 *ServiceName* 输入参数中有值，**获取服务中的 VM** 将运行。  如果 Runbook 的 *ServiceName* 输入参数中没有值，**获取所有 VM** 将运行。  每个活动之前的条件链接执行此逻辑。

这两个活动都使用 **Get-AzureVM** cmdlet。  **获取所有 VM** 使用 **ListAllVMs** 参数集来返回所有虚拟机。  **获取服务中的 VM** 使用 **GetVMByServiceAndVMName** 参数集，并提供 **ServiceName** 输入参数作为 **ServiceName** 参数。  

### <a name="merge-vms"></a>合并 VM
![合并 VM](media/automation-solution-startstopvm/graphical-mergevms.png)

**合并 VM** 活动需要将输入提供给 **Start-AzureVM**，后者需要获取要启动的 VM 的名称和服务名称。  输入可能来自**获取所有 VM** 或**获取服务中的 VM**，但 **Start-AzureVM** 只能指定一个活动作为输入。   

应用场景是创建**合并 VM** 来运行 **Write-Output** cmdlet。  该 cmdlet 的 **InputObject** 参数是结合前面两个活动的输入所形成的 PowerShell 表达式。  其中只有一个活动运行，因此只有一组输出。  **Start-AzureVM** 可以使用该输出作为输入参数。

### <a name="startstop-virtual-machines"></a>启动/停止虚拟机
![启动 VM](media/automation-solution-startstopvm/graphical-startvm.png) ![停止 VM](media/automation-solution-startstopvm/graphical-stopvm.png)

根据 Runkbook，后续活动使用 **Start-AzureVM** 或 **Stop-AzureVM** 来尝试启动或停止 Runbook。  由于活动的前面有管道链接，因此对**合并 VM** 返回的每个对象运行一次。  链接是条件性的，仅当虚拟机的 *RunningState* 在 **Start-AzureVM** 中是“已停止”，而在 **Stop-AzureVM** 中是“已启动”时，活动才运行。 如果不满足此条件，则运行**通知已启动**或**通知已停止**，以使用 **Write-Output** 发送消息。

### <a name="send-output"></a>发送输出
![通知启动 VM](media/automation-solution-startstopvm/graphical-notifystart.png) ![通知停止 VM](media/automation-solution-startstopvm/graphical-notifystop.png)

Runbook 的最后一个步骤是发送输出，而不论是否成功提交每个虚拟机的启动或停止请求。 由于各自有一个 **Write-Output** 活动，我们根据条件链接确定运行哪一个。  如果 *OperationStatus* 为“成功”，则运行**通知 VM 已启动**或**通知 VM 已停止**。  如果 *OperationStatus* 为其他任何值，则运行**通知无法启动**或**通知无法停止**。

## <a name="next-steps"></a>后续步骤
* [Azure 自动化中的图形创作](automation-graphical-authoring-intro.md)
* [Azure 自动化中的子 Runbook](automation-child-runbooks.md)
* [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)



<!--HONumber=Nov16_HO3-->


