<!-- not suitable for Mooncake -->

<properties
   pageTitle="使用 JSON 格式的标记创建 Azure VM 启动和关闭计划 | Azure"
   description="本文演示如何在标记上使用 JSON 字符串来自动计划 VM 启动和关闭。"
   services="automation"
   documentationCenter=""
   authors="MGoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags 
	ms.service="automation"
	ms.date="07/18/2016"
	wacn.date=""/>

# Azure 自动化方案 - 使用 JSON 格式的标记创建 Azure VM 启动和关闭计划

通常，客户想要计划虚拟机的启动和关闭，以减少订阅成本或支持业务和技术要求。

下面的方案允许你使用称为 Schedule 的标记在 Azure 中的资源组级别或虚拟机级别设置 VM 的自动启动和关闭。可以将此计划配置为从星期日至星期六，并可以配置启动时间和关闭时间。虽然我们确实有一些现成的选项，如将[虚拟机规模集](/documentation/articles/virtual-machine-scale-sets-overview/)与自动缩放设置配合使用以允许扩大或缩小，以及具有计划启动和关闭操作的内置功能的 [DevTest Labs](/documentation/articles/devtest-lab-overview/) 服务，但是这些选项仅支持特定方案并且不能应用于 IaaS VM。

当 Schedule 标记应用于资源组时，它将应用于该资源组中的所有虚拟机。如果计划还直接应用于 VM，则最后一个计划将按以下顺序优先：

1.  应用于资源组的计划
2.  应用于资源组和资源组中的虚拟机的计划
3.  应用于虚拟机的计划

此方案实际上接受指定格式的 JSON 字符串，并将其添加为称为 Schedule 的标记的值。然后，Runbook 将列出所有资源组和虚拟机，并根据上面列出的方案确定每个 VM 的计划，再然后它将使用附加的计划循环访问这些 VM，并评估它应执行什么操作（可以是停止、关闭或忽略）。

这些 Runbook 使用 [Azure 运行方式帐户](/documentation/articles/automation-sec-configure-azure-runas-account/)进行身份验证。

## 获取方案

此方案包含该项目的四个 PowerShell 工作流 Runbook（可以从 [TechNet 库](https://gallery.technet.microsoft.com/Azure-Automation-Runbooks-84f0efc7)或 [GitHub](https://github.com/paulomarquesdacosta/azure-automation-scheduled-shutdown-and-startup) 存储库下载）。

Runbook | 说明 
----------|----------
Test-ResourceSchedule | 检查每个虚拟机的计划（根据计划，执行虚拟机的关闭或重新启动）
Add-ResourceSchedule | 将 Schedule 标记添加到 VM 或资源组
Update-ResourceSchedule | 通过将现有 Schedule 标记替换为新标记来修改 Schedule 标记
Remove-ResourceSchedule | 从 VM 或资源组中删除 Schedule 标记 


## 安装和配置此方案

### 安装和发布 Runbook

下载 Runbook 之后，可以使用[导入 Runbook 过程](/documentation/articles/automation-creating-importing-runbook/#importing-a-runbook-from-a-file-into-Azure-Automation)中的过程导入它们。在成功将 Runbook 导入到自动化帐户后，发布每个 Runbook。


### 将计划添加到 Test-ResourceSchedule

按照以下步骤为 Test-ResourceSchedule Runbook 启用计划。此 Runbook 将测试要启动、关闭或原样保留哪些虚拟机。

1. 从 Azure 门户打开你的自动化帐户，然后单击“Runbook”磁贴。
2. 在“Test-ResourceSchedule”边栏选项卡上，单击“计划”磁贴。
3. 在“计划”边栏选项卡上，单击“添加计划”。
4. 在“计划”边栏选项卡中，选择“将计划链接到 Runbook”，并在“计划”边栏选项卡中，选择“创建新计划”。
5.  在“新建计划”边栏选项卡中，键入此计划的名称。例如，HourlyExecution。
6. 对于计划“开始”计划，将开始时间设置为整小时增量。
7. 选择“定期”，并为“重复执行间隔”选择“1 小时”。
8. 验证“设置过期时间”是否设置为“否” ，然后单击“创建”以保存新计划。
9. 在“计划 Runbook”选项边栏选项卡中，选择“参数和运行设置”选项，然后在 Test-ResourceSchedule 的“参数”边栏选项卡的 **SubscriptionName** 字段中，输入订阅的名称。这是此 Runbook 所需的唯一参数。完成时，单击“确定”。
   

完成后，Runbook 计划应如下所示：<br> ![已配置的 Test-ResourceSchedule Runbook](./media/automation-scenario-start-stop-vm-wjson-tags/automation-schedule-config.png)<br>

## 计划 JSON 格式

此解决方案基本上接受指定格式的 JSON 字符串，并将其添加为称为 Schedule 的标记的值。然后，Runbook 将列出所有资源组和虚拟机，并确定每个虚拟机的计划，再然后它将使用附加的计划循环访问这些虚拟机，并检查它应执行什么操作。下面是应如何设置其格式的示例。

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

有关此结构的详细信息：

1. 此 JSON 结构的格式已经过优化，以避开 Azure 中单个标记值的 256 个字符限制

2. *TzId* 表示虚拟机的时区。可以在 PowerShell 会话中使用 TimeZoneInfo .NET 类（**[System.TimeZoneInfo]::GetSystemTimeZones()**）来获取此 ID。<br>

    ![PowerShell 中的 GetSystemTimeZones](./media/automation-scenario-start-stop-vm-wjson-tags/automation-get-timzone-powershell.png)

    - 星期几使用数值 0 到 6 来表示。值 0 等于星期日。
    - 开始时间使用 **S** 属性来表示，它的值采用 24 小时格式。
    - 结束或关闭时间使用 **E** 属性来表示，它的值采用 24 小时格式。

    如果 S 和 E 属性的值为零 (0)，则虚拟机在评估时将保留其当前状态。

3. 如果你想要在一周中的特定天跳过评估，请勿添加相关星期几部分。在下面的示例中，将仅在星期一评估，将忽略一周中的其他天。
   
        {
          "TzId": "Eastern Standard Time",
           "1": {
             "S": "11",
             "E": "17"
           }
        }

## 标记资源组或 VM

若要关闭 VM，你需要标记 VM 或 VM 所在的资源组。不会评估没有 Schedule 标记的虚拟机，因此这些虚拟机不会启动，也不会关闭。此解决方案以两种方式标记资源组或 VM：直接从门户标记或使用 **Add-ResourceSchedule**、**Update-ResourceSchedule** 和 **Remove-ResourceSchedule** Runbook 标记。

### 通过门户进行标记

在门户中按照以下步骤标记虚拟机或资源组。

1. 平展 JSON 字符串，并确认其没有任何空格。JSON 字符串应如下所示：

        {"TzId":"Eastern Standard Time","0":{"S":"11","E":"17"},"1":{"S":"9","E":"19"},"2": {"S":"9","E":"19"},"3":{"S":"9","E":"19"},"4":{"S":"9","E":"19"},"5":{"S":"9","E":"19"},"6":{"S":"11","E":"17"}}
   

2. 通过选择“标记”图标选择要应用此计划的 VM 或资源组。<br>![VM 标记选项](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-tag-option.png)
3. 为标记定义以下键/值对。在“键”字段中键入“Schedule”，在“值”字段中粘贴 JSON 字符串，然后单击“保存”。现在，新标记应出现在资源的标记列表中。<br>![VM Schedule 标记](./media/automation-scenario-start-stop-vm-wjson-tags/automation-vm-schedule-tag.png)


### 通过 PowerShell 进行标记

所有导入的 Runbook 均在脚本的开头包含帮助信息，以说明如何直接从 PowerShell 执行 Runbook。Add-ScheduleResource 和 Update-ScheduleResource Runbook 可以从 PowerShell 通过传递必需的参数（用于在门户外部的 VM 或资源组上创建或更新 Schedule 标记）进行调用。

若要通过 PowerShell 创建、添加和删除标记，首先需要[为 Azure 设置 PowerShell 环境](/documentation/articles/powershell-install-configure/)。设置完成后，便可以继续执行以下步骤。

### 使用 PowerShell 创建 Schedule 标记

1. 打开 PowerShell 会话，然后执行以下命令来使用运行方式帐户进行身份验证并指定订阅：
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"
   
2. 定义计划哈希表。下面是应如何构造它的示例：
    
        $schedule= @{ "TzId"="Eastern Standard Time"; "0"= @{"S"="11";"E"="17"};"1"= @{"S"="9";"E"="19"};"2"= @{"S"="9";"E"="19"};"3"= @{"S"="9";"E"="19"};"4"= @{"S"="9";"E"="19"};"5"= @{"S"="9";"E"="19"};"6"= @{"S"="11";"E"="17"}}

3. 定义 Runbook 所需的参数。在以下示例中，我们以 VM 为目标：

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "VmName"="VM01";"Schedule"=$schedule}

    如果要标记资源组，只需从 $params 哈希表中移除 *VMName* 参数，如下所示：

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"; `
        "Schedule"=$schedule}

4. 使用以下参数执行 **Add-ResourceSchedule** Runbook，以创建 Schedule 标记：

        Start-AzureRmAutomationRunbook -Name "Add-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

5. 如果要更新资源组或虚拟机标记，请使用以下参数执行 **Update-ResourceSchedule** Runbook：

        Start-AzureRmAutomationRunbook -Name "Update-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

### 使用 PowerShell 删除 Schedule 标记

1. 打开 PowerShell 会话，然后执行以下命令来使用运行方式帐户进行身份验证并选择和指定订阅：
    
        Conn = Get-AutomationConnection -Name AzureRunAsConnection
        Add-AzureRMAccount -ServicePrincipal -Tenant $Conn.TenantID `
        -ApplicationId $Conn.ApplicationID -CertificateThumbprint $Conn.CertificateThumbprint
        Select-AzureRmSubscription -SubscriptionName "MySubscription"

2. 定义 Runbook 所需的参数。在以下示例中，我们以 VM 为目标：

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01" ` 
        ;"VmName"="VM01"}

    如果要从资源组中删除标记，只需从 $params 哈希表中移除 *VMName* 参数，如下所示：

        $params = @{"SubscriptionName"="MySubscription";"ResourceGroupName"="ResourceGroup01"}

3. 执行 **Remove-ResourceSchedule** Runbook，以删除 Schedule 标记：

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"

4. 如果要更新资源组或虚拟机标记，请使用以下参数执行 **Remove-ResourceSchedule** Runbook：

        Start-AzureRmAutomationRunbook -Name "Remove-ResourceSchedule" -Parameters $params `
        -AutomationAccountName "AutomationAccount" -ResourceGroupName "ResourceGroup01"


>[AZURE.NOTE] 建议你主动监视这些 Runbook（和虚拟机状态），以验证虚拟机是否正在相应地关闭和启动。

可以通过选择 Runbook 的“作业”磁贴在 Azure 门户中查看 **Test-ResourceSchedule** Runbook 作业的详细信息。作业的“摘要”将显示输入参数和输出流，此外，还显示有关作业的一般信息以及发生的任何异常。历史记录将包括来自输出流以及警告和错误流的消息。选择“输出”磁贴可查看 Runbook 执行的详细结果。<br> ![Test-ResourceSchedule 输出](./media/automation-scenario-start-stop-vm-wjson-tags/automation-job-output.png)

## 后续步骤

-  若要开始使用 PowerShell 工作流 Runbook，请参阅 [My first PowerShell workflow runbook](/documentation/articles/automation-first-runbook-textual/)（我的第一个 PowerShell 工作流 Runbook）
-  若要了解有关 Runbook 类型、其优点和限制的详细信息，请参阅 [Azure 自动化 Runbook 类型](/documentation/articles/automation-runbook-types/)
-  有关 PowerShell 脚本支持功能的详细信息，请参阅 [Azure 自动化中的本机 PowerShell 脚本支持](https://azure.microsoft.com/blog/announcing-powershell-script-support-azure-automation-2/)
-  若要了解有关 Runbook 日志记录和输出的详细信息，请参阅 [Azure 自动化中的 Runbook 输出和消息](/documentation/articles/automation-runbook-output-and-messages/)
-  若要了解有关 Azure 运行方式帐户以及如何使用它进行 Runbook 身份验证的详细信息，请参阅 [Authenticate Runbooks with Azure Run As account（使用 Azure 运行方式帐户进行 Runbook 身份验证）](/documentation/articles/automation-sec-configure-azure-runas-account/)

<!---HONumber=Mooncake_0725_2016-->