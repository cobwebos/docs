---
title: "使用 Azure 门户和 PowerShell 监视和管理管道 | Microsoft 文档"
description: "了解如何使用 Azure 门户和 Azure PowerShell 监视和管理 Azure 数据工厂及已创建的管道。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 9b0fdc59-5bbe-44d1-9ebc-8be14d44def9
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: spelluru
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 223edfde090c9b77467e032198c2150fbaa56a5b
ms.lasthandoff: 04/12/2017


---
# <a name="monitor-and-manage-azure-data-factory-pipelines-by-using-the-azure-portal-and-powershell"></a>使用 Azure 门户和 PowerShell 监视和管理 Azure 数据工厂管道
> [!div class="op_single_selector"]
> * [使用 Azure 门户/Azure PowerShell](data-factory-monitor-manage-pipelines.md)
> * [使用“监视和管理”应用](data-factory-monitor-manage-app.md)


Azure 数据工厂提供存储、处理和数据移动服务的可靠且完整视图。 此服务提供的监视仪表板可用于执行以下任务：

* 快速评估端到端数据管道的运行状况。
* 识别问题，并根据需要采取纠正措施。
* 跟踪数据沿袭。
* 跨任何源跟踪数据之间的关系。
* 查看作业执行、系统运行状况和依赖项的完整历史核算。

本文介绍如何监视、管理和调试管道。 它还提供有关如何创建警报并获取故障通知的信息。

## <a name="understand-pipelines-and-activity-states"></a>了解管道和活动状态
使用 Azure 门户，可以：

* 以图示形式查看数据工厂。
* 查看管道中的活动。
* 查看输入和输出数据集。

本部分还介绍切片如何从某一状态转换为另一种状态。   

### <a name="navigate-to-your-data-factory"></a>导航到数据工厂
1. 登录到 [Azure 门户](https://portal.azure.com)。
2. 在左侧菜单中，单击“数据工厂”。 如未看到，请单击“更多服务 >”，然后在“智能 + 分析”类别下单击“数据工厂”。

   ![“浏览全部”->“数据工厂”](./media/data-factory-monitor-manage-pipelines/browseall-data-factories.png)

   应在“数据工厂”边栏选项卡中看到所有数据工厂。
3. 在“数据工厂”边栏选项卡中，选择感兴趣的数据工厂。

    ![选择数据工厂](./media/data-factory-monitor-manage-pipelines/select-data-factory.png)

   应显示该数据工厂的主页。

   ![“数据工厂”边栏选项卡](./media/data-factory-monitor-manage-pipelines/data-factory-blade.png)

#### <a name="diagram-view-of-your-data-factory"></a>数据工厂的图示视图
数据工厂的**图示**视图提供单个窗格来监视和管理数据工厂及其资产。

若要查看数据工厂的**图示**视图，请在数据工厂的主页上单击“图示”。

![图示视图](./media/data-factory-monitor-manage-pipelines/diagram-view.png)

可以放大、缩小、缩放到合适大小、缩放到 100%、锁定图示布局以及自动放置管道和表。 还可看到数据沿袭信息（即，显示选定项的上下游项）。

### <a name="activities-inside-a-pipeline"></a>管道中的活动
1. 右键单击管道，然后单击“打开管道”查看管道中的所有活动以及该活动的输入和输出数据集。 如果管道包含多个活动且用户想要了解单个管道的操作沿袭，此功能非常有用。

    ![“打开管道”菜单](./media/data-factory-monitor-manage-pipelines/open-pipeline-menu.png)     
2. 在下面的示例中，可看到管道中的复制活动以及输入和输出。 

    ![管道中的活动](./media/data-factory-monitor-manage-pipelines/activities-inside-pipeline.png)
3. 通过在左上角痕迹导航中单击“数据工厂”链接，可向后导航到数据工厂主页。

    ![向后导航到数据工厂](./media/data-factory-monitor-manage-pipelines/navigate-back-to-data-factory.png)

### <a name="view-the-state-of-each-activity-inside-a-pipeline"></a>查看管道中每个活动的状态
通过查看活动生成的任意数据集状态，可查看该活动的当前状态。

在“图示”中双击“OutputBlobTable”，可查看管道内不同活动运行所生成的所有切片。 可以看到过去 8 小时成功运行了复制活动，并生成了处于“就绪”状态的切片。  

![管道状态](./media/data-factory-monitor-manage-pipelines/state-of-pipeline.png)

数据工厂中的数据集切片可以具有以下任一状态：

<table>
<tr>
    <th align="left">状态</th><th align="left">子状态</th><th align="left">说明</th>
</tr>
<tr>
    <td rowspan="8">等待</td><td>ScheduleTime</td><td>未到运行切片的时间。</td>
</tr>
<tr>
<td>DatasetDependencies</td><td>上游依赖项未准备就绪。</td>
</tr>
<tr>
<td>ComputeResources</td><td>计算资源不可用。</td>
</tr>
<tr>
<td>ConcurrencyLimit</td> <td>所有活动实例都忙于运行其他切片。</td>
</tr>
<tr>
<td>ActivityResume</td><td>活动已暂停，且在恢复活动之前无法运行切片。</td>
</tr>
<tr>
<td>重试</td><td>正在重试活动执行。</td>
</tr>
<tr>
<td>验证</td><td>验证尚未开始。</td>
</tr>
<tr>
<td>ValidationRetry</td><td>验证正在等待重试。</td>
</tr>
<tr>
<tr>
<td rowspan="2">InProgress</td><td>正在验证</td><td>正在进行验证。</td>
</tr>
<td>-</td>
<td>正在处理切片。</td>
</tr>
<tr>
<td rowspan="4">已失败</td><td>已超时</td><td>活动执行时间超过活动允许的时间。</td>
</tr>
<tr>
<td>已取消</td><td>切片已由用户操作取消。</td>
</tr>
<tr>
<td>验证</td><td>验证失败。</td>
</tr>
<tr>
<td>-</td><td>切片无法生成和/或验证。</td>
</tr>
<td>就绪</td><td>-</td><td>切片已就绪，可供使用。</td>
</tr>
<tr>
<td>已跳过</td><td>无</td><td>未在处理切片。</td>
</tr>
<tr>
<td>无</td><td>-</td><td>切片过去一直以不同状态存在，但已被重置。</td>
</tr>
</table>



通过在“最近更新的切片”边栏选项卡中单击切片条目，可查看该切片的详细信息。

![切片详细信息](./media/data-factory-monitor-manage-pipelines/slice-details.png)

如果已多次执行切片，则会在“活动运行”列表中看到多行。 通过在“活动运行”列表中单击运行条目，可以查看有关活动运行的详细信息。 该列表将显示所有日志文件以及一条错误消息（如果有）。 此功能对查看和调试日志，而无需离开数据工厂非常有用。

![活动运行详细信息](./media/data-factory-monitor-manage-pipelines/activity-run-details.png)

如果切片状态不是“就绪”，可以在“未就绪的上游切片”列表中看到未就绪且阻止当前切片执行的上游切片。 如果切片处于“等待”状态，并且你希望了解等待中切片的上游依赖关系，此功能非常有用。

![未就绪的上游切片](./media/data-factory-monitor-manage-pipelines/upstream-slices-not-ready.png)

### <a name="dataset-state-diagram"></a>数据集状态图
部署数据工厂且管道拥有有效的活动期后，数据集切片便会从某一状态转换为另一种状态。 当前，切片状态如以下状态图所示：

![状态图](./media/data-factory-monitor-manage-pipelines/state-diagram.png)

数据工厂中的数据集状态转换流如下：“等待”->“正在进行/正在进行（正在验证）”->“就绪/失败”。

切片从“等待”状态开始，等待满足前提条件后执行。 然后，活动开始执行，切片进入“正在进行”状态。 活动执行可能成功，也可能失败。 根据执行结果，切片被标记为“就绪”或“失败”。

可以重置切片，将其从“就绪”或“失败”状态恢复到“等待”状态。 也可以将切片状态标记为“跳过”，从而避免执行活动而不处理切片。

## <a name="manage-pipelines"></a>管理管道
可以使用 Azure PowerShell 管理管道。 例如，通过运行 Azure PowerShell cmdlet，可以暂停和恢复管道。

### <a name="pause-and-resume-pipelines"></a>暂停和恢复管道
还可以使用 **Suspend-AzureRmDataFactoryPipeline** PowerShell cmdlet 暂停/挂起管道。 如果问题得以解决之前不准备运行管道，此 cmdlet 非常有用。

例如：在以下屏幕截图中，已在 **productrecgamalbox1dev** 数据工厂中通过 **PartitionProductsUsagePipeline** 识别问题，准备挂起该管道。

![要挂起的管道](./media/data-factory-monitor-manage-pipelines/pipeline-to-be-suspended.png)

若要挂起管道，请运行以下 PowerShell 命令：

```powershell
Suspend-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
例如：

```powershell
Suspend-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```

使用 **PartitionProductsUsagePipeline** 解决问题后，可通过运行以下 PowerShell 命令来恢复挂起的管道：

```powershell
Resume-AzureRmDataFactoryPipeline [-ResourceGroupName] <String> [-DataFactoryName] <String> [-Name] <String>
```
例如：

```powershell
Resume-AzureRmDataFactoryPipeline -ResourceGroupName ADF -DataFactoryName productrecgamalbox1dev -Name PartitionProductsUsagePipeline
```
## <a name="debug-pipelines"></a>调试管道
Azure 数据工厂提供了通过 Azure 门户和 Azure PowerShell 调试和排查管道问题的丰富功能。

### <a name="find-errors-in-a-pipeline"></a>在管道中查找错误
如果活动在管道中运行失败，则管道所生成数据集将因此故障而处于错误状态。 可以在 Azure 数据工厂中使用以下方法调试和解决错误。

#### <a name="use-the-azure-portal-to-debug-an-error"></a>使用 Azure 门户调试错误
1. 在“表”边栏选项卡中，单击“状态”设置为“失败”的问题切片。

   ![包含问题切片的“表”边栏选项卡](./media/data-factory-monitor-manage-pipelines/table-blade-with-error.png)
2. 在“数据切片”边栏选项卡中，单击失败的活动运行。

   ![出现错误的数据切片](./media/data-factory-monitor-manage-pipelines/dataslice-with-error.png)
3. 在“活动运行详细信息”边栏选项卡中，可以下载与 HDInsight 处理关联的文件。 单击“状态/stderr”所对应的“下载”可下载包含错误详细信息的错误日志文件。

   ![出现错误的“活动运行详细信息”边栏选项卡](./media/data-factory-monitor-manage-pipelines/activity-run-details-with-error.png)     

#### <a name="use-powershell-to-debug-an-error"></a>使用 PowerShell 调试错误
1. 启动 **Azure PowerShell**。
2. 运行 **Get-AzureRmDataFactorySlice** 命令查看切片及其状态。 应看到“失败”状态的切片。        

    ```powershell   
    Get-AzureRmDataFactorySlice [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime] <DateTime> [[-EndDateTime] <DateTime> ] [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```   
   例如：

    ```powershell   
    Get-AzureRmDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00
    ```

   使用为 Set-AzureRmDataFactoryPipelineActivePeriod 指定的 StartDateTime 值替换 **StartDateTime**。
3. 现在，运行 **Get-AzureRmDataFactoryRun** cmdlet 以获取有关切片活动运行的详细信息。

    ```powershell   
    Get-AzureRmDataFactoryRun [-ResourceGroupName] <String> [-DataFactoryName] <String> [-DatasetName] <String> [-StartDateTime]
    <DateTime> [-Profile <AzureProfile> ] [ <CommonParameters>]
    ```

    例如：

    ```powershell   
    Get-AzureRmDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -DatasetName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"
    ```

    StartDateTime 的值是上一步中记下的错误/问题切片的开始时间。 date-time 应括在双引号内。
4. 应看到包含错误详细信息的输出（类似以下内容）：

    ```   
    Id                      : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
    ResourceGroupName       : ADF
    DataFactoryName         : LogProcessingFactory3
    DatasetName               : EnrichedGameEventsTable
    ProcessingStartTime     : 10/10/2014 3:04:52 AM
    ProcessingEndTime       : 10/10/2014 3:06:49 AM
    PercentComplete         : 0
    DataSliceStart          : 5/5/2014 12:00:00 AM
    DataSliceEnd            : 5/6/2014 12:00:00 AM
    Status                  : FailedExecution
    Timestamp               : 10/10/2014 3:04:52 AM
    RetryAttempt            : 0
    Properties              : {}
    ErrorMessage            : Pig script failed with exit code '5'. See wasb://        adfjobs@spestore.blob.core.windows.net/PigQuery
                                    Jobs/841b77c9-d56c-48d1-99a3-
                8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for
                more details.
    ActivityName            : PigEnrichLogs
    PipelineName            : EnrichGameLogsPipeline
    Type                    :
    ```
5. 可使用从输出中看到的 ID 值运行 **Save-AzureRmDataFactoryLog** cmdlet，并通过对 cmdlet 使用 **-DownloadLogsoption** 下载日志文件。

    ```powershell
    Save-AzureRmDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
    ```

## <a name="rerun-failures-in-a-pipeline"></a>管道中的重新运行故障
### <a name="use-the-azure-portal"></a>使用 Azure 门户
排除和调试管道中的故障后，便可通过导航到错误切片并在命令栏上单击“运行”按钮来重新运行失败命令。

![重新运行失败的切片](./media/data-factory-monitor-manage-pipelines/rerun-slice.png)

在切片因策略失败（例如：如果数据不可用）而未通过验证的情况下，在命令栏上单击“验证”按钮，可以解决故障并再次验证。
![修复错误，然后验证](./media/data-factory-monitor-manage-pipelines/fix-error-and-validate.png)

### <a name="use-azure-powershell"></a>使用 Azure PowerShell
通过使用 **Set-AzureRmDataFactorySliceStatus** cmdlet，可以重新运行失败命令。 有关该 cmdlet 的语法和其他详细信息，请参阅 [Set-AzureRmDataFactorySliceStatus](https://msdn.microsoft.com/library/mt603522.aspx) 主题。

**示例：**

以下示例将 Azure 数据工厂“WikiADF”中表“DAWikiAggregatedData”的所有切片状态设置为“等待”。

将“UpdateType”设置为“UpstreamInPipeline”，这意味着表中每个切片和所有相关（上游）表的状态都将设置为“等待”。 此参数的其他可能值为“Individual”。

```powershell
Set-AzureRmDataFactorySliceStatus -ResourceGroupName ADF -DataFactoryName WikiADF -DatasetName DAWikiAggregatedData -Status Waiting -UpdateType UpstreamInPipeline -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-21T20:00:00
```

## <a name="create-alerts"></a>创建警报
创建、更新或删除 Azure 资源（例如，数据工厂）时 Azure 会记录用户事件。 可以基于这些事件创建警报。 可以使用数据工厂捕获各种指标并创建指标相关警报。 建议将事件用于实时监视，将指标用于历史查询。

### <a name="alerts-on-events"></a>事件相关警报
Azure 事件可提供有关 Azure 资源中所发生情况的有用见解。 使用 Azure 数据工厂时，以下情况会生成事件：

* 数据工厂已创建、更新或删除。
* 数据处理（作为“运行次数”）已开始或已完成。
* 创建或删除按需 HDInsight 群集。

可以根据这些用户事件创建警报，并将其配置为向订阅管理员和共同管理员发送电子邮件通知。 此外，针对需接收电子邮件通知且满足条件的其他用户，可指定其电子邮件地址。 如果希望收到故障通知，但不持续监视数据工厂，此功能非常有用。

> [!NOTE]
> 当前，门户不显示事件警报。 使用[“监视和管理”应用](data-factory-monitor-manage-app.md)查看所有警报。


#### <a name="specify-an-alert-definition"></a>指定警报定义
若要指定警报定义，可创建一个 JSON 文件，说明要对其发出警报的操作。 在以下示例中，警报会对 RunFinished 操作发送电子邮件通知。 具体而言，数据工厂中的运行已完成，但未成功运行时将发送电子邮件通知（状态 = FailedExecution）。

```JSON
{
    "contentVersion": "1.0.0.0",
     "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters": {},
    "resources":
    [
        {
            "name": "ADFAlertsSlice",
            "type": "microsoft.insights/alertrules",
            "apiVersion": "2014-04-01",
            "location": "East US",
            "properties":
            {
                "name": "ADFAlertsSlice",
                "description": "One or more of the data slices for the Azure Data Factory has failed processing.",
                "isEnabled": true,
                "condition":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.ManagementEventRuleCondition",
                    "dataSource":
                    {
                        "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleManagementEventDataSource",
                        "operationName": "RunFinished",
                        "status": "Failed",
                        "subStatus": "FailedExecution"   
                    }
                },
                "action":
                {
                    "odata.type": "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails": [ "<your alias>@contoso.com" ]
                }
            }
        }
    ]
}
```

如果不想收到特定故障的警报，可从 JSON 定义中删除 **subStatus**。

此示例将为订阅中的所有数据工厂设置警报。 如果要为特定数据工厂设置警报，可在 **dataSource** 中指定数据工厂 **resourceUri**：

```JSON
"resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>"
```

下表提供了可用操作和状态（和子状态）的列表。

| 操作名称 | 状态 | 子状态 |
| --- | --- | --- |
| RunStarted |已启动 |正在启动 |
| RunFinished |失败/成功 |FailedResourceAllocation<br/><br/>已成功<br/><br/>FailedExecution<br/><br/>已超时<br/><br/>已取消<br/><br/>FailedValidation<br/><br/>已放弃 |
| OnDemandClusterCreateStarted |已启动 | |
| OnDemandClusterCreateSuccessful |已成功 | |
| OnDemandClusterDeleted |已成功 | |

若要深入了解示例中使用的 JSON 元素，请参阅 [Create Alert Rule](https://msdn.microsoft.com/library/azure/dn510366.aspx)（创建警报规则）。

#### <a name="deploy-the-alert"></a>部署警报
若要部署警报，请使用 Azure PowerShell cmdlet **New-AzureRmResourceGroupDeployment**，如以下示例所示：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\ADFAlertFailedSlice.json  
```

成功完成资源组部署后，将看到以下消息：

```
VERBOSE: 7:00:48 PM - Template is valid.
WARNING: 7:00:48 PM - The StorageAccountName parameter is no longer used and will be removed in a future release.
Please update scripts to remove this parameter.
VERBOSE: 7:00:49 PM - Create template deployment 'ADFAlertFailedSlice'.
VERBOSE: 7:00:57 PM - Resource microsoft.insights/alertrules 'ADFAlertsSlice' provisioning status is succeeded

DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

> [!NOTE]
> 可以使用 [Create Alert Rule](https://msdn.microsoft.com/library/azure/dn510366.aspx)（创建警报规则）REST API 来创建警报规则。 JSON 有效负载类似于 JSON 示例。  


#### <a name="retrieve-the-list-of-azure-resource-group-deployments"></a>检索 Azure 资源组部署的列表
若要检索已部署 Azure 资源组部署的列表，请使用 cmdlet **Get-AzureRmResourceGroupDeployment**，如以下示例所示：

```powershell
Get-AzureRmResourceGroupDeployment -ResourceGroupName adf
```

```
DeploymentName    : ADFAlertFailedSlice
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 10/11/2014 2:01:00 AM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           :
```

#### <a name="troubleshoot-user-events"></a>用户事件故障排除
1. 单击“指标和操作”磁贴后，可查看生成的所有事件。

    ![“指标和操作”磁贴](./media/data-factory-monitor-manage-pipelines/metrics-and-operations-tile.png)
2. 单击“事件”磁贴可以查看事件。

    ![“事件”磁贴](./media/data-factory-monitor-manage-pipelines/events-tile.png)
3. 在“事件”边栏选项卡中，可看到有关事件的详细信息，以及已筛选事件等。

    ![“事件”边栏选项卡](./media/data-factory-monitor-manage-pipelines/events-blade.png)
4. 在导致出错的操作列表中单击“操作”。

    ![选择操作](./media/data-factory-monitor-manage-pipelines/select-operation.png)
5. 单击“错误”事件以查看有关错误的详细信息。

    ![事件错误](./media/data-factory-monitor-manage-pipelines/operation-error-event.png)

请参阅 [Azure Insight cmdlet](https://msdn.microsoft.com/library/mt282452.aspx)，了解可用于添加、获取或删除警报的 PowerShell cmdlet。 下面是使用 **Get-AlertRule** cmdlet 的几个示例：

```powershell
get-alertrule -res $resourceGroup -n ADFAlertsSlice -det
```

```
Properties :
Action      : Microsoft.Azure.Management.Insights.Models.RuleEmailAction
Condition   :
DataSource :
EventName             :
Category              :
Level                 :
OperationName         : RunFinished
ResourceGroupName     :
ResourceProviderName  :
ResourceId            :
Status                : Failed
SubStatus             : FailedExecution
Claims                : Microsoft.Azure.Management.Insights.Models.RuleManagementEventClaimsDataSource
Condition      :
Description : One or more of the data slices for the Azure Data Factory has failed processing.
Status      : Enabled
Name:       : ADFAlertsSlice
Tags       :
$type          : Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage
Id: /subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/ADFAlertsSlice
Location   : West US
Name       : ADFAlertsSlice
```

```powershell
Get-AlertRule -res $resourceGroup
```
```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0

Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest3
Location   : West US
Name       : FailedExecutionRunsWest3
```

```powershell
Get-AlertRule -res $resourceGroup -Name FailedExecutionRunsWest0
```

```
Properties : Microsoft.Azure.Management.Insights.Models.Rule
Tags       : {[$type, Microsoft.WindowsAzure.Management.Common.Storage.CasePreservedDictionary, Microsoft.WindowsAzure.Management.Common.Storage]}
Id         : /subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/microsoft.insights/alertrules/FailedExecutionRunsWest0
Location   : West US
Name       : FailedExecutionRunsWest0
```

运行以下 get-help 命令，以查看 Get-AlertRule cmdlet 的详细信息和示例。

```powershell
get-help Get-AlertRule -detailed
```

```powershell
get-help Get-AlertRule -examples
```


如果在门户边栏选项卡上看到警报生成事件，但未收到电子邮件通知，请检查是否已将指定的电子邮件地址设置为接收来自外部发件人的电子邮件。 警报电子邮件可能已被电子邮件设置阻止。

### <a name="alerts-on-metrics"></a>指标相关警报
在数据工厂中，可以捕获各种指标并创建指标相关警报。 在数据工厂中，可对切片监视和创建基于以下指标的警报：

* **失败的运行次数**
* **成功的运行次数**

这些指标非常有用，可帮助你大致了解数据工厂中的失败和成功运行总数。 每次运行切片都会发出指标。 每小时开始，将这些指标聚合并推送到存储帐户。 若要启用指标，请设置存储帐户。

#### <a name="enable-metrics"></a>启用指标
若要启用指标，请在“数据工厂”边栏选项卡中单击以下项：

“监视” > “指标” > “诊断设置” > “诊断”

![诊断链接](./media/data-factory-monitor-manage-pipelines/diagnostics-link.png)

在“诊断”边栏选项卡中，单击“启用”并选择存储帐户，然后单击“保存”。

![诊断边栏选项卡](./media/data-factory-monitor-manage-pipelines/diagnostics-blade.png)

可能需要长达一个小时才能在“监视”边栏选项卡中看到指标，因为指标聚合每小时发生一次。

### <a name="set-up-an-alert-on-metrics"></a>设置指标相关警报
单击“数据工厂指标”磁贴：

![“数据工厂指标”磁贴](./media/data-factory-monitor-manage-pipelines/data-factory-metrics-tile.png)

在“指标”边栏选项卡的工具栏上，单击“+ 添加警报”。
![“数据工厂指标”边栏选项卡>“添加警报”](./media/data-factory-monitor-manage-pipelines/add-alert.png)

在“添加警报规则”页，执行以下步骤，然后单击“确定”。

* 输入警报名称（示例：“failed alert”）。
* 输入警报说明（示例：“故障发生时发送一封电子邮件”）。
* 选择指标（“失败的运行次数”与“成功的运行次数”）。
* 指定条件和阈值。   
* 指定时间段。
* 指定是否向所有者、参与者和读者发送电子邮件。

![“数据工厂指标”边栏选项卡>“添加警报规则”](./media/data-factory-monitor-manage-pipelines/add-an-alert-rule.png)

成功添加警报规则后，该边栏选项卡将关闭，可在“指标”边栏选项卡中看到新警报。

![“数据工厂指标”边栏选项卡>“添加的新警报”](./media/data-factory-monitor-manage-pipelines/failed-alert-in-metric-blade.png)

还应在“警报规则”磁贴上看到警报数。 单击“警报规则”磁贴。

![“数据工厂指标”边栏选项卡 - 警报规则](./media/data-factory-monitor-manage-pipelines/alert-rules-tile-rules.png)

在“警报规则”边栏选项卡中，可看到任何现有的警报。 若要添加警报，请在工具栏上单击“添加警报”。

![“警报规则”边栏选项卡](./media/data-factory-monitor-manage-pipelines/alert-rules-blade.png)

### <a name="alert-notifications"></a>警报通知
在警报规则与条件匹配后，应收到指示警报已激活的电子邮件。 在问题得以解决且警报条件不再匹配后，将收到指示警报已解决的电子邮件。

此行为与事件中的行为不同，事件中对警报规则限定的每个故障均发送通知。

### <a name="deploy-alerts-by-using-powershell"></a>使用 PowerShell 部署警报
可以采用为事件部署警报的方法，为指标部署警报。

**警报定义**

```JSON
{
    "contentVersion" : "1.0.0.0",
    "$schema" : "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "parameters" : {},
    "resources" : [
    {
            "name" : "FailedRunsGreaterThan5",
            "type" : "microsoft.insights/alertrules",
            "apiVersion" : "2014-04-01",
            "location" : "East US",
            "properties" : {
                "name" : "FailedRunsGreaterThan5",
                "description" : "Failed Runs greater than 5",
                "isEnabled" : true,
                "condition" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.ThresholdRuleCondition, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.ThresholdRuleCondition",
                    "dataSource" : {
                        "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleMetricDataSource, Microsoft.WindowsAzure.Management.Mon.Client",
                        "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleMetricDataSource",
                        "resourceUri" : "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName
>/PROVIDERS/MICROSOFT.DATAFACTORY/DATAFACTORIES/<dataFactoryName>",
                        "metricName" : "FailedRuns"
                    },
                    "threshold" : 5.0,
                    "windowSize" : "PT3H",
                    "timeAggregation" : "Total"
                },
                "action" : {
                    "$type" : "Microsoft.WindowsAzure.Management.Monitoring.Alerts.Models.RuleEmailAction, Microsoft.WindowsAzure.Management.Mon.Client",
                    "odata.type" : "Microsoft.Azure.Management.Insights.Models.RuleEmailAction",
                    "customEmails" : ["abhinav.gpt@live.com"]
                }
            }
        }
    ]
}
```

将示例中的 *subscriptionId*、*resourceGroupName* 和 *dataFactoryName* 替换为相应的值。

*metricName* 目前支持以下两个值：

* FailedRuns
* SuccessfulRuns

**部署警报**

若要部署警报，请使用 Azure PowerShell cmdlet **New-AzureRmResourceGroupDeployment**，如以下示例所示：

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName adf -TemplateFile .\FailedRunsGreaterThan5.json
```

部署成功后，应看到以下消息：

```
VERBOSE: 12:52:47 PM - Template is valid.
VERBOSE: 12:52:48 PM - Create template deployment 'FailedRunsGreaterThan5'.
VERBOSE: 12:52:55 PM - Resource microsoft.insights/alertrules 'FailedRunsGreaterThan5' provisioning status is succeeded


DeploymentName    : FailedRunsGreaterThan5
ResourceGroupName : adf
ProvisioningState : Succeeded
Timestamp         : 7/27/2015 7:52:56 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
Outputs           
```

还可使用 **Add-AlertRule** cmdlet 来部署警报规则。 有关详细信息和示例，请参阅 [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx) 主题。  

## <a name="move-a-data-factory-to-a-different-resource-group-or-subscription"></a>将数据工厂移到其他资源组或订阅
通过使用数据工厂主页上的“移动”命令栏按钮，可以将数据工厂移动到其他资源组或其他订阅。

![移动数据工厂](./media/data-factory-monitor-manage-pipelines/MoveDataFactory.png)

还可移动任何相关资源（如与数据工厂关联的警报）以及数据工厂。

![“移动资源”对话框](./media/data-factory-monitor-manage-pipelines/MoveResources.png)

