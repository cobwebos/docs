---
title: 查询 Azure 更新管理日志
description: 本文介绍如何在 Log Analytics 工作区中查询更新管理的日志。
services: automation
ms.subservice: update-management
ms.date: 01/10/2020
ms.topic: conceptual
ms.openlocfilehash: 5a1979b0e714f35694999c04e1f890b710d54ac9
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2020
ms.locfileid: "75867066"
---
# <a name="query-update-records-for-update-management-in-azure-monitor-logs"></a>Azure Monitor 日志中更新管理的查询更新记录

除了更新管理解决方案中提供的详细信息外，还可以搜索存储在 Log Analytics 工作区中的日志。 从 "解决方案" 页的左侧窗格中，选择 "**日志**"。 此时将打开 "**日志搜索**" 页。

你还可以了解如何自定义查询，或从不同客户端使用它们，还可以访问： [Log Analytics 搜索 API 文档](https://dev.loganalytics.io/)。

## <a name="update-records"></a>更新记录

为 Windows 和 Linux Vm 更新管理收集的记录以及日志搜索结果中显示的数据类型。 以下各节介绍了这些记录。

### <a name="required-updates"></a>所需更新

将创建一个类型为 `RequiredUpdate` 的记录，表示计算机所需的更新。 这些记录的属性在下表中列出：

| 属性 | Description | 
|----------|-------------|
| Computer | 报表计算机的完全限定的域名。 |
| KBID | Windows 更新的知识库文章 ID。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 | 
| 产品 | 更新适用的产品。 | 
| PublishDate | 可从 Windows 更新下载和安装更新的日期。 |
| 服务器 | | 
| SourceHealthServiceId | 表示 Log Analytics Windows 代理 ID 的唯一标识符。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 表示 Azure Active Directory 的组织实例的唯一标识符。 | 
| TimeGenerated | 创建记录的日期和时间。 | 
| 类型 | *Update* | 
| UpdateClassification | 指示可应用的更新的类型。 对于 Windows：<br> *关键更新*<br> *安全更新*<br> *更新汇总*<br> *功能包*<br> *Service pack*<br> *定义更新*<br> *工具*<br> *更新*。 对于 Linux：<br> *关键更新和安全更新*<br> *其他* |
| UpdateSeverity | 此漏洞的严重性分级。 值为：<br> *严重*<br> *重要说明*<br> *Moderate*<br> *Low* |
| UpdateTitle | 更新的标题。|

### <a name="update"></a>更新

将创建一个类型为 "`Update`" 的记录，该记录表示计算机的可用更新及其安装状态。 这些记录的属性在下表中列出：

| 属性 | Description | 
|----------|-------------|
| ApprovalSource | 仅适用于 Windows 操作系统。 值为*Microsoft 更新*。 |
| 已批准 | *True*或*False* |
| 分类 | *更新* |
| Computer | 报表计算机的完全限定的域名。 |
| ComputerEnvironment | *Azure*或*非 azure*。 |
| MSRCBulletinID | 安全公告 ID 号 | 
| MSRCSeverity | 此漏洞的严重性分级。 值为：<br> *严重*<br> *重要说明*<br> *Moderate*<br> *Low* |  
| KBID | Windows 更新的知识库文章 ID。 |
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| UpdateID | 软件更新的唯一标识符。 |
| RevisionNumber | 更新的特定修订版本的版本号。 |
| 可选 | *True*或*False* | 
| RebootBehavior | 安装/卸载更新后的重新启动行为。 |
| _ResourceId | 与记录关联的资源的唯一标识符。 |
| 类型 | *Update* |
| VMUUID | 虚拟机的唯一标识符。 |
| MG | 管理组或 Log Analytics 工作区的唯一标识符。 | 
| TenantId | 表示 Azure Active Directory 的组织实例的唯一标识符。 | 
| SourceSystem | *OperationsManager* | 
| TimeGenerated | 创建记录的日期和时间。 | 
| SourceComputerId | 表示源计算机的唯一标识符。 | 
| 标题 | 更新的标题。 |
| PublishedDate （UTC） | 可从 Windows 更新下载和安装更新的日期。  |
| UpdateState | 更新的当前状态。 | 
| 产品 | 更新适用的产品。 |
| SubscriptionId | Azure 订阅的唯一标识符。 | 
| ResourceGroup | 资源所属的资源组的名称。 | 
| ResourceProvider | 指定资源提供程序。 | 
| 资源 | 资源的名称。 | 
| ResourceType | 资源类型的名称。 | 

### <a name="update-agent"></a>更新代理

将创建一个类型为 `UpdateAgent` 的记录，该记录提供计算机上的更新代理的详细信息。 这些记录的属性在下表中列出：

| 属性 | Description | 
|----------|-------------|
| AgeofOldestMissingRequiredUpdate | | 
| AutomaticUpdateEnabled | | 
| Computer | 报表计算机的完全限定的域名。 |
| DaySinceLastUpdateBucket | | 
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| OSVersion | 操作系统的版本。 |
| 服务器 | |
| SourceHealthServiceId | 表示 Log Analytics Windows 代理 ID 的唯一标识符。 |
| SourceSystem | *OperationsManager* | 
| TenantId | 表示 Azure Active Directory 的组织实例的唯一标识符。 |
| TimeGenerated | 创建记录的日期和时间。 |
| 类型 | *Update* | 
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WSUSServer | 如果 Windows 更新代理存在问题以帮助进行故障排除，则会显示错误。 |

### <a name="update-deployment-status"></a>更新部署状态 

将创建一个类型为 "`UpdateRunProgress`" 的记录，该记录提供计算机计划部署的更新部署状态。 这些记录的属性在下表中列出：

| 属性 | Description | 
|----------|-------------|
| Computer | 报表计算机的完全限定的域名。 |
| ComputerEnvironment | *Azure*或*非 azure*。 | 
| CorrelationId | 用于更新的 runbook 作业运行的唯一标识符。 |
| EndTime | 同步过程结束的时间。 | 
| ErrorResult | Windows 更新在更新安装失败时生成的错误代码。 | 
| InstallationStatus | 客户端计算机上的更新的可能安装状态*为 "已* *成功*"、"*部分失败*"。 |
| KBID | Windows 更新的知识库文章 ID。 | 
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| OSType | 指定操作系统、 *Windows*或*Linux*的类型。 | 
| 产品 | 更新适用的产品。 |
| 资源 | 资源的名称。 | 
| ResourceId | 与记录关联的资源的唯一标识符。 |
| ResourceProvider | 指定资源提供程序。 | 
| ResourceType | 资源类型的名称。 | 
| SourceComputerId | 表示源计算机的唯一标识符。 | 
| SourceSystem | *OperationsManager* |
| StartTime | 计划安装更新的时间。 |
| SubscriptionId | Azure 订阅的唯一标识符。 | 
| SucceededOnRetry | 显示更新执行第一次失败的时间，当前操作是重试尝试。 |
| TimeGenerated | 创建记录的日期和时间。 |
| 标题 | 更新的标题。 |
| 类型 | *UpdateRunProgress* |
| UpdateId | 软件更新的唯一标识符。 |
| VMUUID | 虚拟机的唯一标识符。 |
| _ResourceId | 与记录关联的资源的唯一标识符。 |

### <a name="update-summary"></a>更新摘要 

将创建一个类型为 `UpdateSummary` 的记录，该记录按计算机提供更新摘要。 这些记录的属性在下表中列出：

| 属性 | Description | 
|----------|-------------|
| Computer | 报表计算机的完全限定的域名。 |
| ComputerEnvironment | *Azure*或*非 azure*。 | 
| CriticalUpdatesMissing | 缺少的关键更新的数量。 | 
| ManagementGroupName | Operations Manager 管理组或 Log Analytics 工作区的名称。 |
| NETRuntimeVersion | Windows 计算机上安装的 .NET Framework 版本。 |
| OldestMissingSecurityUpdateBucket | | 
| OldestMissingSecurityUpdateInDays | |
| OsVersion | 操作系统的版本。 |
| OtherUpdatesMissing | 缺少检测到的更新计数。 |
| 资源 |  资源的名称。 | 
| ResourceGroup | 资源所属的资源组的名称。 |
| ResourceId | 与记录关联的资源的唯一标识符。 |
| ResourceProvider | 指定资源提供程序。 |
| ResourceType | 资源类型的名称。 |
| RestartPending | *True* 或 *False*。 |
| SecurityUpdatesMissing | 缺少的安全更新的适用计数。| 
| SourceComputerId | 虚拟机的唯一标识符。 |
| SourceSystem | *OpsManager* | 
| SubscriptionId | Azure 订阅的唯一标识符。 |
| TimeGenerated | 创建记录的日期和时间。 |
| TotalUpdatesMissing | 适用的缺少更新的总数。 | 
| 类型 | *UpdateSummary* |
| VMUUID | 虚拟机的唯一标识符。 |
| WindowsUpdateAgentVersion | Windows 更新代理的版本。 |
| WindowsUpdateSetting | 显示 Windows 更新代理的状态。 可能的值包括：<br> *计划安装*<br> *安装前通知*<br> 不正常的 WUA 代理返回了错误。 | 
| WSUSServer | 如果 Windows 更新代理存在问题以帮助进行故障排除，则会显示错误。 |
| _ResourceId | 与记录关联的资源的唯一标识符。 |

## <a name="sample-queries"></a>示例查询

以下各节提供了针对更新管理收集的更新记录的示例日志查询。

### <a name="confirm-that-non-azure-machines-are-onboarded"></a>确认非 Azure 计算机已加入

若要确认直接连接的计算机是否正在与 Azure Monitor 日志通信，请运行以下日志搜索之一。

#### <a name="linux"></a>Linux

```loganalytics
Heartbeat
| where OSType == "Linux" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

#### <a name="windows"></a>Windows

```loganalytics
Heartbeat
| where OSType == "Windows" | summarize arg_max(TimeGenerated, *) by SourceComputerId | top 500000 by Computer asc | render table
```

在 Windows 计算机上，你可以查看以下信息以验证代理与 Azure Monitor 日志的连接：

1. 在控制面板中，打开 **Microsoft Monitoring Agent**。 在“Azure Log Analytics”选项卡上，代理会显示以下消息：“Microsoft Monitoring Agent 已成功连接到 Log Analytics”。
2. 打开“Windows 事件日志”。 转到“应用程序和服务日志\Operations Manager”，搜索来自“服务连接器”源的事件 ID 3000 和事件 ID 5002。 这些事件指示计算机已注册到 Log Analytics 工作区并且正在接收配置。

如果代理无法与 Azure Monitor 日志通信，并且该代理已配置为通过防火墙或代理服务器与 internet 通信，请确认正确配置了防火墙或代理服务器。 若要了解如何验证防火墙或代理服务器是否已正确配置，请参阅 [Windows 代理的网络配置](../azure-monitor/platform/agent-windows.md)或 [Linux 代理的网络配置](../log-analytics/log-analytics-agent-linux.md)。

> [!NOTE]
> 如果 Linux 系统配置为与代理或 Log Analytics 网关通信，并且你将载入此解决方案，请使用以下命令更新 proxy.conf 权限来向 omiuser 组授予对文件的读取权限：
>
> `sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf`
> `sudo chmod 644 /etc/opt/microsoft/omsagent/proxy.conf`

执行评估后，新添加的 Linux 代理会显示状态“已更新”。 此过程可能需要长达 6 小时的时间。

若要确认 Operations Manager 管理组是否正在与 Azure Monitor 日志通信，请参阅[验证 Operations Manager 与 Azure Monitor 日志的集成](../azure-monitor/platform/om-agents.md#validate-operations-manager-integration-with-azure-monitor)。

### <a name="single-azure-vm-assessment-queries-windows"></a>单个 Azure VM 评估查询 (Windows)

将 VMUUID 值替换为要查询的虚拟机的 VM GUID。 可以通过在 Azure Monitor 日志中运行以下查询来找到应使用的 VMUUID： `Update | where Computer == "<machine name>" | summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"b08d5afa-1471-4b52-bd95-a44fea6e4ca8"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and VMUUID=~"8bf1ccc6-b6d3-4a0b-a643-23f346dfdf82"
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

### <a name="single-azure-vm-assessment-queries-linux"></a>单个 Azure VM 评估查询 (Linux)

对于某些 Linux 发行版, 与来自 Azure 资源管理器的 VMUUID 值和存储在 Azure Monitor 日志中的[字节序](https://en.wikipedia.org/wiki/Endianness)不匹配。 以下查询可检查任一字节序的匹配情况。 使用 GUID 的 big-endian 和 little-endian 格式替换 VMUUID 值可正常地返回结果。 可以通过在 Azure Monitor 日志中运行以下查询来找到应使用的 VMUUID： `Update | where Computer == "<machine name>"
| summarize by Computer, VMUUID`

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and (VMUUID=~"625686a0-6d08-4810-aae9-a089e68d4911" or VMUUID=~"a0865662-086d-1048-aae9-a089e68d4911")
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl

```

### <a name="multi-vm-assessment-queries"></a>多 VM 评估查询

#### <a name="computers-summary"></a>计算机摘要

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Approved, Optional, Classification) by SourceComputerId, UpdateID
    | distinct SourceComputerId, Classification, UpdateState, Approved, Optional
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed" and (Optional==false or Classification has "Critical" or Classification has "Security") and Approved!=false, iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity
| union (Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux"
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by SourceComputerId, Product, ProductArch
    | distinct SourceComputerId, Classification, UpdateState
    | summarize WorstMissingUpdateSeverity=max(iff(UpdateState=~"Needed", iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1)), 0)) by SourceComputerId
)
on SourceComputerId
| extend WorstMissingUpdateSeverity=coalesce(WorstMissingUpdateSeverity, -1)
| summarize computersBySeverity=count() by WorstMissingUpdateSeverity)
| summarize assessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity>-1), notAssessedComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==-1), computersNeedCriticalUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==4), computersNeedSecurityUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==2), computersNeedOtherUpdatesCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==1), upToDateComputersCount=sumif(computersBySeverity, WorstMissingUpdateSeverity==0)
| summarize assessedComputersCount=sum(assessedComputersCount), computersNeedCriticalUpdatesCount=sum(computersNeedCriticalUpdatesCount),  computersNeedSecurityUpdatesCount=sum(computersNeedSecurityUpdatesCount), computersNeedOtherUpdatesCount=sum(computersNeedOtherUpdatesCount), upToDateComputersCount=sum(upToDateComputersCount), notAssessedComputersCount=sum(notAssessedComputersCount)
| extend allComputersCount=assessedComputersCount+notAssessedComputersCount


```

#### <a name="missing-updates-summary"></a>缺少更新摘要

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification) by Computer, SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| summarize by Product, ProductArch, Classification
| union (Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| summarize by UpdateID, Classification )
| summarize allUpdatesCount=count(), criticalUpdatesCount=countif(Classification has "Critical"), securityUpdatesCount=countif(Classification has "Security"), otherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security")
```

#### <a name="computers-list"></a>计算机列表

```loganalytics
Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Product, Computer, ComputerEnvironment) by SourceComputerId, Product, ProductArch
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed"), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed"), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed"), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=1, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2)
| union(Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions, Computer, ResourceId, ComputerEnvironment, VMUUID) by SourceComputerId
| where Solutions has "updates"
| extend vmuuId=VMUUID, azureResourceId=ResourceId, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), scopedToUpdatesSolution=true, lastUpdateAgentSeenTime=""
| join kind=leftouter
(
    Update
    | where TimeGenerated>ago(14h) and OSType!="Linux" and SourceComputerId in ((Heartbeat
    | where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
    | summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
    | where Solutions has "updates"
    | distinct SourceComputerId))
    | summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, Optional, Approved, Computer, ComputerEnvironment) by Computer, SourceComputerId, UpdateID
    | summarize Computer=any(Computer), ComputerEnvironment=any(ComputerEnvironment), missingCriticalUpdatesCount=countif(Classification has "Critical" and UpdateState=~"Needed" and Approved!=false), missingSecurityUpdatesCount=countif(Classification has "Security" and UpdateState=~"Needed" and Approved!=false), missingOtherUpdatesCount=countif(Classification !has "Critical" and Classification !has "Security" and UpdateState=~"Needed" and Optional==false and Approved!=false), lastAssessedTime=max(TimeGenerated), lastUpdateAgentSeenTime="" by SourceComputerId
    | extend compliance=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0, 2, 1)
    | extend ComplianceOrder=iff(missingCriticalUpdatesCount > 0 or missingSecurityUpdatesCount > 0 or missingOtherUpdatesCount > 0, 1, 3)
)
on SourceComputerId
| project id=SourceComputerId, displayName=Computer, sourceComputerId=SourceComputerId, scopedToUpdatesSolution=true, missingCriticalUpdatesCount=coalesce(missingCriticalUpdatesCount, -1), missingSecurityUpdatesCount=coalesce(missingSecurityUpdatesCount, -1), missingOtherUpdatesCount=coalesce(missingOtherUpdatesCount, -1), compliance=coalesce(compliance, 4), lastAssessedTime, lastUpdateAgentSeenTime, osType=2, environment=iff(ComputerEnvironment=~"Azure", 1, 2), ComplianceOrder=coalesce(ComplianceOrder, 2) )
| order by ComplianceOrder asc, missingCriticalUpdatesCount desc, missingSecurityUpdatesCount desc, missingOtherUpdatesCount desc, displayName asc
| project-away ComplianceOrder
```

#### <a name="missing-updates-list"></a>缺少更新列表

```loganalytics
Update
| where TimeGenerated>ago(5h) and OSType=="Linux" and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=="Linux" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, BulletinUrl, BulletinID) by SourceComputerId, Product, ProductArch
| where UpdateState=~"Needed"
| project-away UpdateState, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(Product, "_", ProductArch), displayName=Product, productArch=ProductArch, classification=Classification, InformationId=BulletinID, InformationUrl=tostring(split(BulletinUrl, ";", 0)[0]), osType=1
| union(Update
| where TimeGenerated>ago(14h) and OSType!="Linux" and (Optional==false or Classification has "Critical" or Classification has "Security") and SourceComputerId in ((Heartbeat
| where TimeGenerated>ago(12h) and OSType=~"Windows" and notempty(Computer)
| summarize arg_max(TimeGenerated, Solutions) by SourceComputerId
| where Solutions has "updates"
| distinct SourceComputerId))
| summarize hint.strategy=partitioned arg_max(TimeGenerated, UpdateState, Classification, Title, KBID, PublishedDate, Approved) by Computer, SourceComputerId, UpdateID
| where UpdateState=~"Needed" and Approved!=false
| project-away UpdateState, Approved, TimeGenerated
| summarize computersCount=dcount(SourceComputerId, 2), displayName=any(Title), publishedDate=min(PublishedDate), ClassificationWeight=max(iff(Classification has "Critical", 4, iff(Classification has "Security", 2, 1))) by id=strcat(UpdateID, "_", KBID), classification=Classification, InformationId=strcat("KB", KBID), InformationUrl=iff(isnotempty(KBID), strcat("https://support.microsoft.com/kb/", KBID), ""), osType=2)
| sort by ClassificationWeight desc, computersCount desc, displayName asc
| extend informationLink=(iff(isnotempty(InformationId) and isnotempty(InformationUrl), toobject(strcat('{ "uri": "', InformationUrl, '", "text": "', InformationId, '", "target": "blank" }')), toobject('')))
| project-away ClassificationWeight, InformationId, InformationUrl
```

## <a name="next-steps"></a>后续步骤

* 使用[Azure Monitor 日志](../log-analytics/log-analytics-log-searches.md)中的日志搜索查看详细的更新数据。
* 针对更新部署状态[创建警报](automation-tutorial-update-management.md#configure-alerts)。
