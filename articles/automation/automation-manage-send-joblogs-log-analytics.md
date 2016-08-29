<!-- not suitable for Mooncake -->

<properties
    pageTitle="将作业状态和作业流从自动化转发到 Log Analytics (OMS) | Azure"
    description="本文演示如何将作业状态和 Runbook 作业流发送到 Microsoft Operations Management Suite Log Analytics，以提供附加见解和管理信息。"
    services="automation"
    documentationCenter=""
    authors="MGoedtel"
    manager="jwhit"
    editor="tysonn" />
<tags
	ms.service="automation"
	ms.date="08/08/2016"
	wacn.date=""/>

# 将作业状态和作业流从自动化转发到 Log Analytics (OMS)

自动化可以将 Runbook 作业状态和作业流发送到 Microsoft Operations Management Suite (OMS) Log Analytics 工作区。尽管你可以在 Azure 门户或 PowerShell 中根据特定自动化帐户的单个作业状态或所有作业查看此信息，但在满足操作要求之前所要做好的一切准备工作都需要你创建自定义 PowerShell 脚本。使用 Log Anaytics 可以：

- 获取有关自动化作业的见解
- 基于 Runbook 作业状态（例如失败或暂停）触发电子邮件或警报
- 针对所有作业流编写高级查询
- 跨自动化帐户关联作业
- 可视化不同时间段的作业历史记录

## 先决条件和部署注意事项

若要开始将自动化日志发送到 Log Analytics，必须做好以下准备：

1. 一个 OMS 订阅。有关更多信息，请参阅 [Get started with Log Analytics](/documentation/articles/log-analytics-get-started/)（Log Analytics 入门）。

    >[AZURE.NOTE]要使这种配置正常工作，OMS 工作区和自动化帐户需要在相同的 Azure 订阅中。
  
2. 一个 [Azure 存储帐户](/documentation/articles/storage-create-storage-account/)。
   
    >[AZURE.NOTE]该存储帐户*必须*位于自动化帐户所在的同一个区域中。
 
3. 装有 Operational Insights cmdlet 1.0.8 或更高版本的 Azure PowerShell。有关此版本及其安装方法的信息，请参阅 [How to install and configure Azure PowerShell](/documentation/articles/powershell-install-configure/)（如何安装和配置 Azure PowerShell）。
4. Azure Diagnostic and Log Analytics PowerShell。有关此版本及其安装方法的更多信息，请参阅 [Azure Diagnostic and Log Analytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/0.1)。
5. 从 [PowerShell 库](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/1.0/DisplayScript)下载 PowerShell 脚本 **Enable-AzureDiagnostics.ps1**。此脚本将配置以下各项：
 - 一个存储帐户，用于保存你指定的自动化帐户的 Runbook 作业状态和流数据。
 - 启用从自动化帐户收集此数据能够以 JSON 格式将其存储在 Azure Blob 存储帐户中。
 - 配置将 Blob 存储帐户中的数据收集到 OMS Log Analytics 的过程。
 - 为 OMS 工作区启用自动化 Log Analytics 解决方案。

在执行期间，**Enable-AzureDiagnostics.ps1** 脚本需要以下参数：

- *AutomationAccountName* - 自动化帐户的名称
- *LogAnalyticsWorkspaceName* - OMS 工作区的名称

若要查找 *AutomationAccountName* 的值，请在 Azure 门户中，从“自动化帐户”边栏选项卡选择你的自动化帐户，然后选择“所有设置”。在“所有设置”边栏选项卡中，选择“帐户设置”下面的“属性”。在“属性”边栏选项卡中，可以记下这些值。<br>![自动化帐户属性](./media/automation-manage-send-joblogs-log-analytics/automation-account-properties.png)


## 设置与 Log Analytics 的集成

1. 在计算机上，从“开始”屏幕启动 **Windows PowerShell**。
2. 从 PowerShell 命令行 shell 中，导航到下载的脚本所在的文件夹并执行该脚本，请记得更改 *-AutomationAccountName* 和 *-LogAnalyticsWorkspaceName* 参数的值。

    >[AZURE.NOTE] 执行脚本后，系统将提示你在 Azure 上进行身份验证。**必须**以订阅管理员角色成员和订阅共同管理员的帐户登录。
    
        .\Enable-AzureDiagnostics -AutomationAccountName <NameofAutomationAccount> `
        -LogAnalyticsWorkspaceName <NameofOMSWorkspace> `

3. 运行此脚本并将新的诊断数据写入存储大约 30 分钟后，你应会在 Log Analytics 中看到记录。如果经过这段时间后未看到记录，请参阅 [JSON files in blob storage](/documentation/articles/log-analytics-azure-storage-json/#troubleshooting-configuration-for-azure-diagnostics-written-to-blob-in-json)（Blob 存储中的 JSON 文件）中的故障排除部分。

### 验证配置

若要确认脚本是否成功配置了你的自动化帐户和 OMS 工作区，可以在 PowerShell 中执行以下步骤。在此之前，为了查找 OMS 工作区名称和资源组名称的值，请从 Azure 门户导航到“Log Analytics (OMS)”，然后在“Log Analytics (OMS)”边栏选项卡中，记下“名称”和“资源组”的值。<br> ![OMS Log Analytics 工作区列表](./media/automation-manage-send-joblogs-log-analytics/oms-la-workspaces-list-blade.png)当我们使用 PowerShell cmdlet [Get-AzureRmOperationalInsightsStorageInsight](https://msdn.microsoft.com/zh-cn/library/mt603567.aspx) 验证 OMS 工作区中的配置时，将会用到这两个值。

1.  从 Azure 门户导航到“存储帐户”，并搜索以下使用命名约定的存储帐户 - *AutomationAccountNameomsstorage*。Runbook 作业完成不久之后，你应会看到已创建两个 Blob 容器 - **insights-logs-joblogs** 和 **insights-logs-jobstreams**。

2.  从 PowerShell 运行以下 PowerShell 代码，请记得更改前面复制或记下的 **ResourceGroupName** 和 **WorkspaceName** 参数的值。

    Login-AzureRmAccount
    Get-AzureRmSubscription -SubscriptionName 'SubscriptionName' | Set-AzureRmContext
    Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName "OMSResourceGroupName" `
    -Workspace "OMSWorkspaceName" 

    随后将返回指定的 OMS 工作区的存储见解。我们想要确认前面指定的自动化帐户是否存在存储见解，以及 **State** 对象是否显示 **OK** 值。<br>![Get-AzureRmOperationalInsightsStorageInsights cmdlet 的结果](./media/automation-manage-send-joblogs-log-analytics/automation-posh-getstorageinsights-results.png)

## 在 Log Analytics 中查看自动化日志 

既然你已开始将自动化作业日志发送到 Log Analytics，让我们看看你可以在 OMS 中对这些日志执行哪些操作。

### Runbook 作业失败或暂停时发送电子邮件 

我们的一家重要客户提出的要求是，当 Runbook 作业出现问题时能够发送电子邮件或短信。

若要创建警报规则，首先请针对应该调用警报的 Runbook 作业记录创建日志搜索。然后，“警报”按钮便可供使用，让你创建和配置警报规则。

1.	在“OMS 概述”页中，单击“日志搜索”。
2.	在查询字段中键入以下内容，针对警报创建日志搜索查询：`Category=JobLogs (ResultType=Failed || ResultType=Suspended)`。你也可以使用以下命令按 RunbookName 分组：`Category=JobLogs (ResultType=Failed || ResultType=Suspended) | measure Count() by RunbookName_s`。
  
    如果你已设置在工作区中收集来自多个自动化帐户或订阅的日志，则还可能想要按照订阅或自动化帐户来为警报分组。自动化帐户名称可能派生自 JobLogs 搜索中的 Resource 字段。

3.	单击页面顶部的“警报”，打开“添加警报规则”屏幕。有关用于配置警报的选项的详细信息，请参阅 [Alerts in Log Analytics](/documentation/articles/log-analytics-alerts/#creating-an-alert-rule)（Log Analytics 中的警报）。

### 查找已完成但出错的所有作业 

除了在失败时收到警报以外，你还可能想要知道 Runbook 作业何时出现了非终止错误（PowerShell 生成错误流，但非终止错误不会导致作业暂停或失败）。

1. 在 OMS 门户中，单击“日志搜索”。
2. 在查询字段中键入 `Category=JobStreams StreamType_s=Error | measure count() by JobId_g`，然后单击“搜索”。


### 查看作业的作业流  

当你调试作业时，可能想要深入查看作业流。以下查询将显示 GUID 为 2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0 的单个作业的所有流：

`Category=JobStreams JobId_g="2ebd22ea-e05e-4eb9-9d76-d73cbd4356e0" | sort TimeGenerated | select ResultDescription`  


### 查看历史作业状态 

最后，你可能想要可视化不同时间段的作业历史记录。你可以使用此查询来搜索作业在不同时间段的状态。

`Category=JobLogs NOT(ResultType="started") | measure Count() by ResultType interval 1day`  
<br> ![OMS Historical Job Status Chart](./media/automation-manage-send-joblogs-log-analytics/historical-job-status-chart.png)<br>

## 摘要

通过将自动化作业状态和流数据发送到 Log Analytics，你可以设置警报来接收有关出现问题的通知，并在自定义仪表板中使用高级查询可视化 Runbook 结果、Runbook 作业状态和其他相关的重要指标或度量值，从而更好地洞察自动化作业的状态。这有助于提高操作可见性并更快地解决事件。


## 后续步骤

- 若要详细了解如何使用 Log Analytics 构造不同的搜索查询和查看自动化作业日志，请参阅 [Log searches in Log Analytics](/documentation/articles/log-analytics-log-searches/)（Log Analytics 中的日志搜索）
- 若要了解如何创建输出和错误消息以及从 Runbook 检索此类消息，请参阅 [Runbook output and messages](/documentation/articles/automation-runbook-output-and-messages/)（Runbook 输出和消息）
- 若要详细了解 Runbook 执行方式、如何监视 Runbook 作业和其他技术详细信息，请参阅 [Track a runbook job](/documentation/articles/automation-runbook-execution/)（跟踪 Runbook 作业）
- 若要了解有关 OMS Log Analytics 和数据收集源的详细信息，请参阅 [Collecting Azure storage data in Log Analytics overview](/documentation/articles/log-analytics-azure-storage/)（在 Log Analytics 中收集 Azure 存储空间数据概述）

<!---HONumber=Mooncake_0822_2016-->