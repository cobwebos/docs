---
title: "使用 Azure 自动化中的 runbook 收集 Log Analytics 数据 | Microsoft Docs"
description: "分步教程，逐步讲解如何在 Azure 自动化中创建 runbook，将数据收集到 OMS 存储库，供 Log Analytics 进行分析。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: operations-management-suite
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/27/2017
ms.author: bwren
ms.openlocfilehash: 59f674c9c6404da7f5384539189f41a4ba1a939a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="collect-data-in-log-analytics-with-an-azure-automation-runbook"></a>使用 Azure 自动化 runbook 收集 Log Analytics 中的数据
可以从各种源中收集 Log Analytics 中的大量数据，包括代理上的[数据源](../log-analytics/log-analytics-data-sources.md)以及[从 Azure 中收集的数据](../log-analytics/log-analytics-azure-storage.md)。  尽管某些情况下，需要收集无法通过这些标准源访问的数据。  这时，可以使用 [HTTP 数据收集器 API](../log-analytics/log-analytics-data-collector-api.md) 将数据从任何 REST API 客户端写入到 Log Analytics。  一种执行此数据收集的常用方法是使用 Azure 自动化中的 runbook。   

本教程将逐步介绍在 Azure 自动化中创建和计划 runbook，将数据写入 Log Analytics 的过程。


## <a name="prerequisites"></a>先决条件
此方案要求在 Azure 订阅中配置以下资源。  两者皆可为免费帐户。

- [Log Analytics 工作区](../log-analytics/log-analytics-get-started.md)。
- [Azure 自动化帐户](../automation/automation-offering-get-started.md)。

## <a name="overview-of-scenario"></a>方案概述
对于本教程，将编写用于收集自动化作业相关信息的 runbook。  Azure 自动化中的 Runbook 是通过 PowerShell 实现的，所以首先是在 Azure 自动化编辑器中编写和测试脚本。  确认正在收集所需信息后，将数据写入 Log Analytics 并验证自定义数据类型。  最后，创建用于定期启动 runbook 的计划。

> [!NOTE]
> 可以将 Azure 自动化配置为在不使用此 runbook 的情况下向 Log Analytics 发送作业信息。  此方案主要用于支持该教程，此外建议将数据发送到一个测试工作区。  


## <a name="1-install-data-collector-api-module"></a>1.安装数据收集器 API 模块
每个[来自 HTTP 数据收集器的 API 请求](../log-analytics/log-analytics-data-collector-api.md#create-a-request)必须进行适当的格式设置，且包含授权标头。  可以在 runbook 中执行此操作，但可通过使用简化此过程的模块，减少所需的代码量。  一个可用的模块是 PowerShell 库中的 [OMSIngestionAPI 模块](https://www.powershellgallery.com/packages/OMSIngestionAPI)。

若要在 runbook 中使用一个[模块](../automation/automation-integration-modules.md)，则该模块必须安装在自动化帐户中。  之后同一帐户中的任何 runbook 便可使用模块中的函数。  通过在自动化帐户中选择“资产” > “模块” > “添加模块”，安装新的模块。  

而 PowerShell 库会提供一个快速选项，用于将模块直接部署到自动化帐户，因此可在本教程中使用该选项。  

![OMSIngestionAPI 模块](media/operations-management-suite-runbook-datacollect/OMSIngestionAPI.png)

1. 转到 [PowerShell 库](https://www.powershellgallery.com/)。
2. 搜索“OMSIngestionAPI”。
3. 单击“部署到 Azure 自动化”按钮。
4. 选择自动化帐户并单击“确定”，以安装模块。


## <a name="2-create-automation-variables"></a>2.创建自动化变量
[自动化变量](..\automation\automation-variables.md)保存可由自动化帐户中所有 runbook 使用的值。  这些变量允许更改这些值，而无需编辑实际的 runbook，这样可使 runbook 更为灵活。 来自 HTTP 数据收集器 API 的每个请求都需要 OMS 工作区的 ID 和密钥，并且变量资产是存储此信息的理想之选。  

![变量](media/operations-management-suite-runbook-datacollect/variables.png)

1. 在 Azure 门户中，导航到自动化帐户。
2. 在“共享资源”下选择“变量”。
2. 单击“添加变量”，在下表中创建两个变量。

| 属性 | 工作区 ID 值 | 工作区密钥值 |
|:--|:--|:--|
| 名称 | WorkspaceId | WorkspaceKey |
| 类型 | String | String |
| 值 | 粘贴在 Log Analytics 工作区的“工作区 ID”中。 | 粘贴在 Log Analytics 工作区的“主密钥”或“辅助密钥”中。 |
| 加密 | 否 | 是 |



## <a name="3-create-runbook"></a>3.创建 Runbook

Azure 自动化在门户中具有编辑器，可在其中编辑和测试 runbook。  可以选择将脚本编辑器[直接与 PowerShell](../automation/automation-edit-textual-runbook.md) 配合使用，也可以选择[创建图形 runbook](../automation/automation-graphical-authoring-intro.md)。  本教程中将使用 PowerShell 脚本。 

![编辑 runbook](media/operations-management-suite-runbook-datacollect/edit-runbook.png)

1. 导航到自动化帐户。  
2. 单击“Runbook” > “添加 runbook” > “新建 runbook”。
3. 对于 runbook 名称，键入“Collect-Automation-jobs”。  对于 runbook 类型，选择“PowerShell”。
4. 单击“创建”，创建 runbook 并启动编辑器。
5. 将以下代码复制并粘贴到 runbook 中。  有关代码的解释，请参考脚本中的注释。
    
        # Get information required for the automation account from parameter values when the runbook is started.
        Param
        (
            [Parameter(Mandatory = $True)]
            [string]$resourceGroupName,
            [Parameter(Mandatory = $True)]
            [string]$automationAccountName
        )
        
        # Authenticate to the Automation account using the Azure connection created when the Automation account was created.
        # Code copied from the runbook AzureAutomationTutorial.
        $connectionName = "AzureRunAsConnection"
        $servicePrincipalConnection=Get-AutomationConnection -Name $connectionName         
        Add-AzureRmAccount `
            -ServicePrincipal `
            -TenantId $servicePrincipalConnection.TenantId `
            -ApplicationId $servicePrincipalConnection.ApplicationId `
            -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint 
        
        # Set the $VerbosePreference variable so that we get verbose output in test environment.
        $VerbosePreference = "Continue"
        
        # Get information required for Log Analytics workspace from Automation variables.
        $customerId = Get-AutomationVariable -Name 'WorkspaceID'
        $sharedKey = Get-AutomationVariable -Name 'WorkspaceKey'
        
        # Set the name of the record type.
        $logType = "AutomationJob"
        
        # Get the jobs from the past hour.
        $jobs = Get-AzureRmAutomationJob -ResourceGroupName $resourceGroupName -AutomationAccountName $automationAccountName -StartTime (Get-Date).AddHours(-1)
        
        if ($jobs -ne $null) {
            # Convert the job data to json
            $body = $jobs | ConvertTo-Json
        
            # Write the body to verbose output so we can inspect it if verbose logging is on for the runbook.
            Write-Verbose $body
        
            # Send the data to Log Analytics.
            Send-OMSAPIIngestionFile -customerId $customerId -sharedKey $sharedKey -body $body -logType $logType -TimeStampField CreationTime
        }


## <a name="4-test-runbook"></a>4.测试 runbook
Azure 自动化包含用于[测试 runbook](../automation/automation-testing-runbook.md)（发布之前）的环境。  可以检查 runbook 收集的数据，并验证在将其发布到生产环境之前它会按预期写入 Log Analytics。 
 
![测试 runbook](media/operations-management-suite-runbook-datacollect/test-runbook.png)

6. 单击“保存”保存 runbook。
1. 单击“测试窗格”，在测试环境中打开 runbook。
3. 由于 runbook 具有参数，所以系统会提示输入这些参数的值。  输入将从其中收集作业信息的的资源组的名称和自动化帐户。
4. 单击“启动”以启动 runbook。
3. runbook 启动时的状态为“已排队”，之后变为“正在运行”状态。  
3. runbook 应显示详细输出以及以 json 格式收集的作业。  如果未列出任何作业，则可能在过去一个小时内尚未在自动化帐户中创建任何作业。  尝试启动自动化帐户中的任何 runbook，然后再次执行测试。
4. 确保所得输出未在对 Log Analytics 的 post 命令中显示任何错误。  应看到类似于下面的消息。

    ![发布输出](media/operations-management-suite-runbook-datacollect/post-output.png)

## <a name="5-verify-records-in-log-analytics"></a>5.验证 Log Analytics 中的记录
完成 runbook 的测试，并确认成功接收到输出后，便可验证记录是否是使用 [Log Analytics 中的日志搜索](../log-analytics/log-analytics-log-searches.md)创建的。

![日志输出](media/operations-management-suite-runbook-datacollect/log-output.png)

1. 在 Azure 门户中，选择 Log Analytics 工作区。
2. 单击“日志搜索”。
3. 键入后列命令 `Type=AutomationJob_CL`，然后单击“搜索”按钮。 请注意记录类型包括未在脚本中指定的 _CL。  该后缀自动追加到日志类型，以指示它是自定义日志类型。
4. 应看到返回一条或多条记录，指示 runbook 正在按预期工作。


## <a name="6-publish-the-runbook"></a>6.发布 runbook
确定 runbook 正常工作后，需要将其发布，这样才能在生产环境中运行。  可以继续编辑和测试 runbook，而无需修改已发布的版本。  

![发布 runbook](media/operations-management-suite-runbook-datacollect/publish-runbook.png)

1. 返回到自动化帐户。
2. 单击“Runbook”，选择“Collect-Automation-jobs”。
3. 单击“编辑”，然后单击“发布”。
4. 当系统要求确认覆盖先前发布的版本时，单击“是”。

## <a name="7-set-logging-options"></a>7.设置日志记录选项 
对于测试，可以查看[详细输出](../automation/automation-runbook-output-and-messages.md#message-streams)，因为已在脚本中设置 $VerbosePreference 变量。  对于生产环境，若要查看详细输出，需要设置 runbook 的日志记录属性。  对于本教程中使用的 runbook，这会显示发送到 Log Analytics 的 json 数据。

![日志记录和跟踪](media/operations-management-suite-runbook-datacollect/logging.png)

1. 在 runbook 的属性中，在“Runbook 设置”下选择“日志记录和跟踪”。
2. 将“日志详细记录”的设置更改为“开”。
3. 单击“保存” 。

## <a name="8-schedule-runbook"></a>8.计划 Runbook
启动用于收集监视数据的 runbook，其最常见的方式是将其安排为自动运行。  具体方法是创建 [Azure 自动化中的计划](../automation/automation-schedules.md)，并将其附加到 runbook。

![计划 Runbook](media/operations-management-suite-runbook-datacollect/schedule-runbook.png)

1. 在 runbook 的属性中，在“资源”下选择“计划”。
2. 单击“添加计划” > “将计划链接到 runbook” > “创建新计划”。
5. 为计划键入下列值，然后单击“创建”。

| 属性 | 值 |
|:--|:--|
| 名称 | AutomationJobs-Hourly |
| 启动 | 选择至少晚于当前时间 5 分钟的任何时间。 |
| 定期 | 重复执行 |
| 执行间隔 | 1 小时	 |
| 设置到期时间 | 否 |

创建计划后，需要设置参数值，此计划每次启动 runbook 时都要使用此参数值。

6. 单击“配置参数和运行设置”。
7. 填写“ResourceGroupName”和“AutomationAccountName”的值。
8. 单击 **“确定”**。 

## <a name="9-verify-runbook-starts-on-schedule"></a>9.验证 runbook 是否按计划启动
每次启动 runbook，都会[创建一个作业](../automation/automation-runbook-execution.md)并记录任何输出。  实际上，这些正是 runbook 在收集的作业。  通过在计划启动时间已过去后，检查 runbook 作业，可以验证 runbook 是否按预期启动。

![作业](media/operations-management-suite-runbook-datacollect/jobs.png)

1. 在 runbook 的属性中，在“资源”下选择“作业”。
2. 应看到每次启动 runbook 时的作业列表。
3. 单击其中一个作业，查看其详细信息。
4. 单击“所有日志”，查看 runbook 的日志和输出。
5. 滚动到底部，查找类似于下图的项。<br>![详细](media/operations-management-suite-runbook-datacollect/verbose.png)
6. 单击此项，查看已发送到 Log Analytics 的详细 json 数据。



## <a name="next-steps"></a>后续步骤
- 使用[视图设计器](../log-analytics/log-analytics-view-designer.md)创建一个视图，用于显示已收集到 Log Analytics 存储库的数据。
- 打包[管理解决方案](operations-management-suite-solutions-creating.md)中的 runbook，分发给客户。
- 详细了解 [Log Analytics](https://docs.microsoft.com/azure/log-analytics/)。
- 详细了解 [Azure 自动化](https://docs.microsoft.com/azure/automation/)。
- 详细了解 [HTTP 数据收集器 API](../log-analytics/log-analytics-data-collector-api.md)。