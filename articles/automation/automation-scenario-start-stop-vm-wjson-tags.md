---
title: "使用 JSON 格式的标记来安排 Azure VM 状态 | Microsoft Docs"
description: "本文演示如何在标记上使用 JSON 字符串来自动计划 VM 启动和关闭。"
services: automation
documentationcenter: 
author: MGoedtel
manager: jwhit
editor: tysonn
ms.assetid: 6afed5d2-e939-4749-8b2c-9312b4c16fb2
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: magoedte;paulomarquesc
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: af0bc6b24a403bd09092ac0a099c500d651d15b7
ms.lasthandoff: 04/27/2017


---
# <a name="azure-automation-scenario-using-json-formatted-tags-to-create-a-schedule-for-azure-vm-startup-and-shutdown"></a>Azure 自动化方案 - 使用 JSON 格式的标记创建 Azure VM 启动和关闭计划
通常，客户想要计划虚拟机的启动和关闭，以减少订阅成本或支持业务和技术要求。

下面的方案允许你使用称为 Schedule 的标记在 Azure 中的资源组级别或虚拟机级别设置 VM 的自动启动和关闭。 可以将此计划配置为从星期日至星期六，并可以配置启动时间和关闭时间。

我们确实有一些现成可用的选项。 其中包括：

* [虚拟机规模集](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)，具有可进行缩减或扩展的自动缩放设置。
* [DevTest Labs](../devtest-lab/devtest-lab-overview.md) 服务，提供用于安排启动和关闭操作的内置功能。

但是，这些选项仅支持特定的方案，不适用于基础结构即服务 (IaaS) VM。

当 Schedule 标记应用于资源组时，它也将应用于该资源组中的所有虚拟机。 如果计划还直接应用于 VM，则最后一个计划将按以下顺序优先：

1. 应用于资源组的计划
2. 应用于资源组和资源组中的虚拟机的计划
3. 应用于虚拟机的计划

此方案实际上接受指定格式的 JSON 字符串，并将其添加为称为 Schedule 的标记的值。 然后，Runbook 将列出所有资源组和虚拟机，并根据上面列出的方案确定每个 VM 的计划。 接下来，它将使用附加的计划循环访问这些 VM，并评估它应执行什么操作。 例如，它可以确定需要停止、关闭或忽略哪些 VM。

这些 Runbook 使用 [Azure 运行方式帐户](automation-sec-configure-azure-runas-account.md)进行身份验证。

## <a name="download-the-runbooks-for-the-scenario"></a>下载方案的 Runbook
此方案包含该项目的四个 PowerShell 工作流 Runbook（可以从 [TechNet 库](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)或 [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) 存储库下载）。

| Runbook | 说明 |
| --- | --- |
| Test-ResourceSchedule |检查每个虚拟机的计划，并根据计划执行关闭或启动。 |
| Add-ResourceSchedule |将 Schedule 标记添加到 VM 或资源组。 |
| Update-ResourceSchedule |通过将现有 Schedule 标记替换为新标记来修改 Schedule 标记。 |
| Remove-ResourceSchedule |从 VM 或资源组中删除 Schedule 标记。 |

## <a name="install-and-configure-this-scenario"></a>安装和配置此方案
### <a name="install-and-publish-the-runbooks"></a>安装和发布 Runbook
下载 Runbook 之后，可以使用[在 Azure 自动化中创建或导入 Runbook](automation-creating-importing-runbook.md#importing-a-runbook-from-a-file-into-azure-automation)中的过程来导入这些 Runbook。  在成功将 Runbook 导入到自动化帐户后，发布每个 Runbook。

### <a name="add-a-schedule-to-the-test-resourceschedule-runbook"></a>将计划添加到 Test-ResourceSchedule Runbook
按照以下步骤为 Test-ResourceSchedule Runbook 启用计划。 此 Runbook 将验证要启动、关闭或原样保留哪些虚拟机。

1. 从 Azure 门户打开自动化帐户，然后单击“Runbook”磁贴。
2. 在“Test-ResourceSchedule”边栏选项卡上，单击“计划”磁贴。
3. 在“计划”边栏选项卡上，单击“添加计划”。
4. 在“计划”边栏选项卡上，选择“将计划链接到 Runbook”。 然后选择“创建新计划”。
5. 在“新建计划”边栏选项卡上，键入此计划的名称，例如：*HourlyExecution*。
6. 对于计划的“开始时间”，请将开始时间设置为一小时增量。
7. 选择“定期”，并为“重复执行间隔”选择“1 小时”。
8. 验证“设置过期时间”是否设置为“否”，然后单击“创建”以保存新计划。
9. 在“计划 Runbook”选项边栏选项卡上，选择“参数和运行设置”。 在 Test-ResourceSchedule“参数”边栏选项卡上，在“SubscriptionName”字段中输入订阅的名称。  这是该 Runbook 所需的唯一参数。  完成后，单击“确定”。

Runbook 计划完成后应如下所示：

![已配置 Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## <a name="format-the-json-string"></a>设置 JSON 字符串格式
此解决方案基本上接受指定格式的 JSON 字符串，并将其添加为称为 Schedule 的标记的值。 然后，Runbook 将列出所有资源组和虚拟机，并列出每个虚拟机的计划。

Runbook 将使用附加的计划循环访问虚拟机，并检查应执行什么操作。 下面是有关如何设置解决方案格式的示例：

```json
{
    "TzId": "Eastern Standard Time",
    "0": {
        "S": "11",
        "E": "17"
    },
    "1": {
        "S": "9",
        "E": "19"
    },
    "2": {
        "S": "9",
        "E": "19"
    },
}
```

下面是有关此结构的详细信息：

1. 此 JSON 结构的格式已经过优化，以避开 Azure 中单个标记值的 256 个字符限制。
2. *TzId* 表示虚拟机的时区。 可以在 PowerShell 会话中使用 TimeZoneInfo .NET 类 **[System.TimeZoneInfo]::GetSystemTimeZones()** 来获取此 ID。

   ![PowerShell 中的 GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

   * 星期几使用数字值 0 到 6 表示。 0 值等于星期日。
   * 开始时间使用 **S** 属性来表示，它的值采用 24 小时格式。
   * 结束或关闭时间使用 **E** 属性来表示，它的值采用 24 小时格式。

     如果 **S** 和 **E** 属性的值均为零 (0)，则虚拟机在评估时将保留其当前状态。
3. 如果想要在一周中的某天跳过评估，请勿添加一周中该天的部分。 在以下示例中，将仅在星期一评估，并忽略其他星期日期：

    ```json
    {
        "TzId": "Eastern Standard Time",
        "1": {
            "S": "11",
            "E": "17"
        }
    }
    ```

## <a name="tag-resource-groups-or-vms"></a>标记资源组或 VM
若要关闭 VM，需要标记 VM 或 VM 所在的资源组。 不会评估没有 Schedule 标记的虚拟机。 因此这些虚拟机不会启动，也不会关闭。

此解决方案以两种方式标记资源组或 VM。 可以直接从门户标记。 或者，可以使用 Add-ResourceSchedule、Update-ResourceSchedule 和 Remove-ResourceSchedule Runbook。

### <a name="tag-through-the-portal"></a>通过门户进行标记
在门户中按照以下步骤标记虚拟机或资源组：

1. 平展 JSON 字符串，并确认其没有任何空格。  JSON 字符串应如下所示：

    ```json
    {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
    ```

2. 选择要应用此计划的 VM 或资源组旁边的“标记”图标。

   ![VM 标记选项](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)

3. 为标记定义以下键/值对。 在“键”字段中键入“Schedule”，然后在“值”字段中粘贴 JSON 字符串。 单击“保存” 。 新标记现在应在资源的标记列表中显示。

   ![VM schedule 标记](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)

### <a name="tag-from-powershell"></a>从 PowerShell 进行标记
所有导入的 Runbook 均在脚本的开头包含帮助信息，以说明如何直接从 PowerShell 执行 Runbook。 可以从 PowerShell 调用 Add-ScheduleResource 和 Update-ScheduleResource Runbook。 为此，请传递必需的参数，以便在门户外部的 VM 或资源组上创建或更新 Schedule 标记。

若要通过 PowerShell 创建、添加和删除标记，首先需要[为 Azure 设置 PowerShell 环境](/powershell/azure/overview)。 完成设置后，可以继续执行以下步骤。

### <a name="create-a-schedule-tag-with-powershell"></a>使用 PowerShell 创建 schedule 标记
1. 打开 PowerShell 会话。 然后参考以下示例使用运行方式帐户进行身份验证并指定订阅：

    ```powershell
    $Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. 定义计划哈希表。 下面是应如何构造它的示例：

    ```powershell
    $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}
    ```

3. 定义 Runbook 所需的参数。 在以下示例中，我们以 VM 为目标：

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "VmName"="VM01";"Schedule"=$schedule}
    ```

    若要标记资源组，请从 $params 哈希表中删除 *VMName* 参数，如下所示：

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; "Schedule"=$schedule}
    ```

4. 结合以下参数运行 Add-ResourceSchedule Runbook，以创建 Schedule 标记：

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

5. 若要更新资源组或虚拟机标记，请结合以下参数执行 **Update-ResourceSchedule** Runbook：

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

### <a name="remove-a-schedule-tag-with-powershell"></a>使用 PowerShell 删除 schedule 标记
1. 打开 PowerShell 会话，然后运行以下命令来使用运行方式帐户进行身份验证并选择及指定订阅：

    ```powershell
    Conn = Get-AutomationConnection -Name AzureRunAsConnection
    Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
    -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

2. 定义 Runbook 所需的参数。 在以下示例中，我们以 VM 为目标：

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01";"VmName"="VM01"}
    ```

    若要从资源组中删除标记，请从 $params 哈希表中删除 *VMName* 参数，如下所示：

    ```powershell
    $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}
    ```

3. 执行 Remove-ResourceSchedule Runbook，以删除 Schedule 标记：

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

4. 若要更新资源组或虚拟机标记，请结合以下参数执行 Remove-ResourceSchedule Runbook：

    ```powershell
    Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
    -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"
    ```

> [!NOTE]
> 建议你主动监视这些 Runbook（和虚拟机状态），以验证虚拟机是否正在相应地关闭和启动。
>

若要在 Azure 门户中查看 Test-ResourceSchedule Runbook 作业的详细信息，请选择 Runbook 的“作业”磁贴。 作业摘要将显示输入参数和输出流，此外，还显示有关作业的一般信息以及发生的任何异常。

“作业摘要”包括来自输出、警告和错误流的消息。 选择“输出”磁贴可查看 Runbook 执行的详细结果。

![Test-ResourceSchedule 输出](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## <a name="next-steps"></a>后续步骤
* 若要开始使用 PowerShell 工作流 Runbook，请参阅 [我的第一个 PowerShell 工作流 Runbook](automation-first-runbook-textual.md)。
* 若要了解有关 Runbook 类型、优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](automation-runbook-types.md)。
* 有关 PowerShell 脚本支持功能的详细信息，请参阅 [Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)。
* 若要了解有关 Runbook 日志记录和输出的详细信息，请参阅 [Azure 自动化中的 Runbook 输出和消息](automation-runbook-output-and-messages.md)。
* 若要了解有关 Azure 运行方式帐户以及如何使用它进行 Runbook 身份验证的详细信息，请参阅[使用 Azure 运行方式帐户进行 Runbook 身份验证](automation-sec-configure-azure-runas-account.md)。

