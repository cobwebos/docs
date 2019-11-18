---
title: 利用 Azure Monitor 日志监视 Azure Site Recovery
description: 了解如何使用 Azure Monitor 日志 (Log Analytics) 监视 Azure Site Recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: raynew
ms.openlocfilehash: f20d0d38a7fbd831d3e97a69373bac04b9b330aa
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/16/2019
ms.locfileid: "74133418"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视 Site Recovery

本文介绍如何使用 [Azure Monitor 日志](site-recovery-overview.md)和 [Log Analytics](../azure-monitor/platform/data-platform-logs.md) 监视 Azure [Site Recovery](../azure-monitor/log-query/log-query-overview.md) 复制的计算机。

Azure Monitor 日志提供一个日志数据平台用于收集活动和诊断日志，以及其他监视数据。 在 Azure Monitor 日志中，可以使用 Log Analytics 编写和测试日志查询，并以交互方式分析日志数据。 可以可视化和查询日志结果，并配置警报来根据监视的数据采取措施。

对于 Site Recovery，Azure Monitor 日志可帮助你执行以下操作：

- **监视 Site Recovery 运行状况和状态**。 例如，可以监视复制运行状况、测试故障转移状态、Site Recovery 事件、受保护计算机的恢复点目标 (RPO)，以及磁盘/数据更改率。
- **为 Site Recovery 设置警报**。 例如，可以针对计算机运行状况、测试故障转移状态或 Site Recovery 作业状态配置警报。

在**azure 到 azure**的复制和**VMware VM/物理服务器到 azure**的复制中，支持使用 Site Recovery Azure Monitor 日志。

> [!NOTE]
> 若要获取 VMware 和物理计算机的改动数据日志和上传速率日志，需要在进程服务器上安装 Microsoft monitoring agent。 此代理将复制计算机的日志发送到工作区。 此功能仅适用于9.30 移动代理版本。

## <a name="before-you-start"></a>开始之前

下面是需要的项：

- 至少一台在恢复服务保管库中受保护的计算机。
- 用于存储 Site Recovery 日志的 Log Analytics 工作区。 [了解](../azure-monitor/learn/quick-create-workspace.md)如何设置工作区。
- 基本了解如何在 Log Analytics 中编写、运行和分析日志查询。 [了解详细信息](../azure-monitor/log-query/get-started-portal.md)。

在开始之前，我们建议查看[常见监视问题](monitoring-common-questions.md)。

## <a name="configure-site-recovery-to-send-logs"></a>配置 Site Recovery 以发送日志

1. 在保管库中，单击“诊断设置” **“添加诊断设置”。**  > 

    ![选择诊断日志记录](./media/monitoring-log-analytics/add-diagnostic.png)

2. 在 "**诊断设置**" 中，指定一个名称，并选中 "**发送到 Log Analytics**" 框。
3. 选择 Azure Monitor 日志订阅和 Log Analytics 工作区。
4. 在切换中选择**Azure 诊断**。
5. 在日志列表中，选择带有 **AzureSiteRecovery** 前缀的所有日志。 然后单击“确定”。

    ![选择工作区](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 日志将开始馈送到选定工作区中的某个表 (**AzureDiagnostics**) 内。

## <a name="configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs"></a>在进程服务器上配置 Microsoft monitoring agent 以发送改动和上传速率日志

你可以在本地捕获 VMware/物理计算机的数据更改率信息和源数据上传速率信息。 若要启用此操作，需要在进程服务器上安装 Microsoft monitoring agent。

1. 中转到 Log Analytics 工作区，并单击 "**高级设置**"。
2. 单击 "**连接的源**" 页，然后选择 " **Windows 服务器**"。
3. 在进程服务器上下载 Windows 代理（64位）。 
4. [获取工作区 ID 和密钥](../azure-monitor/platform/agent-windows.md#obtain-workspace-id-and-key)
5. [将代理配置为使用 TLS 1。2](../azure-monitor/platform/agent-windows.md#configure-agent-to-use-tls-12)
6. 通过提供获得的工作区 ID 和密钥[完成代理安装](../azure-monitor/platform/agent-windows.md#install-the-agent-using-setup-wizard)。
7. 安装完成后，请跳到 Log Analytics 工作区，并单击 "**高级设置**"。 中转到 "**数据**" 页，然后单击 " **Windows 性能计数器**"。 
8. 单击 **"+"** 添加以下两个计数器，其采样间隔为300秒：

        ASRAnalytics(*)\SourceVmChurnRate 
        ASRAnalytics(*)\SourceVmThrpRate 

改动和上传速率数据将开始送入工作区。


## <a name="query-the-logs---examples"></a>查询日志 - 示例

使用以 [Kusto 查询语言](../azure-monitor/log-query/get-started-queries.md)编写的日志查询从日志中检索数据。 本部分提供几个可用于 Site Recovery 监视的常见查询示例。

> [!NOTE]
> 其中一些示例使用设置为 **A2A** 的 **replicationProviderName_s**。 此查询检索已使用 Site Recovery 复制到次要 Azure 区域的 Azure VM。 若要检索已使用 Site Recovery 复制到 Azure 的本地 VMware VM 或物理服务器，可在这些示例中将 **A2A** 替换为 **InMageAzureV2**。


### <a name="query-replication-health"></a>查询复制运行状况

此查询绘制了所有受保护 Azure Vm 的当前复制运行状况的饼图，分为三种状态：正常、警告或严重。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , replicationHealth_s  
| summarize count() by replicationHealth_s  
| render piechart   
```
### <a name="query-mobility-service-version"></a>查询移动服务版本

此查询为使用 Site Recovery 复制的 Azure VM 绘制饼图，其中的信息已按这些 VM 运行的移动代理版本划分。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , agentVersion_s  
| summarize count() by agentVersion_s  
| render piechart 
```

### <a name="query-rpo-time"></a>查询 RPO 时间

此查询绘制使用 Site Recovery 进行复制的 Azure Vm 的条形图，并按恢复点目标（RPO）细分：在15-30 分钟到超过30分钟的时间不到15分钟。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| extend RPO = case(rpoInSeconds_d <= 900, "<15Min",   
rpoInSeconds_d <= 1800, "15-30Min", ">30Min")  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , RPO  
| summarize Count = count() by RPO  
| render barchart 
```

![查询 RPO](./media/monitoring-log-analytics/example1.png)

### <a name="query-site-recovery-jobs"></a>查询 Site Recovery 作业

此查询检索过去 72 小时触发的所有 Site Recovery 作业（适用于所有灾难恢复方案）及其完成状态。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>查询 Site Recovery 事件

此查询检索过去 72 小时引发的所有 Site Recovery 事件（适用于所有灾难恢复方案）及其严重性。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>查询测试故障转移状态（饼图）

此查询绘制使用 Site Recovery 复制的 Azure VM 的测试故障转移状态饼图。

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)  
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s  
| project name_s , Resource, failoverHealth_s  
| summarize count() by failoverHealth_s  
| render piechart 
```

### <a name="query-test-failover-state-table"></a>查询测试故障转移状态（表格）

此查询绘制使用 Site Recovery 复制的 Azure VM 的测试故障转移状态表格。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>查询计算机 RPO

此查询绘制一个趋势图，用于跟踪特定 Azure VM (ContosoVM123) 在过去 72 小时的 RPO。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where TimeGenerated > ago(72h)  
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| project TimeGenerated, name_s , RPO_in_seconds = rpoInSeconds_d   
| render timechart 
```
![查询计算机 RPO](./media/monitoring-log-analytics/example2.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-an-azure-vm"></a>Azure VM 的查询数据更改率（变动）和上传速率

此查询绘制特定 Azure VM （ContosoVM123）的趋势图，该图表示数据更改率（每秒写入的字节数）和数据上传速率。 

```
AzureDiagnostics   
| where Category in ("AzureSiteRecoveryProtectedDiskDataChurn", "AzureSiteRecoveryReplicationDataUploadRate")   
| extend CategoryS = case(Category contains "Churn", "DataChurn",   
Category contains "Upload", "UploadRate", "none")  
| extend InstanceWithType=strcat(CategoryS, "_", InstanceName_s)   
| where TimeGenerated > ago(24h)   
| where InstanceName_s startswith "ContosoVM123"   
| project TimeGenerated , InstanceWithType , Churn_MBps = todouble(Value_s)/1048576   
| render timechart  
```
![查询数据更改率](./media/monitoring-log-analytics/example3.png)

### <a name="query-data-change-rate-churn-and-upload-rate-for-a-vmware-or-physical-machine"></a>查询数据更改率（变动）以及 VMware 或物理计算机的上传速率

> [!Note]
> 请确保在进程服务器上设置监视代理以提取这些日志。 请参阅[配置监视代理的步骤](#configure-microsoft-monitoring-agent-on-the-process-server-to-send-churn-and-upload-rate-logs)。

此查询为复制项**9r7sfh9qlru**的特定磁盘**disk0**绘制一个趋势图，该图表示数据更改率（每秒写入的字节数）和数据上传速率。 可以在恢复服务保管库中的复制项的 "**磁盘**" 边栏选项卡上找到磁盘名称。 要在查询中使用的实例名称是计算机的 DNS 名称后跟 _ 和磁盘名称，如本示例中所示。

```
Perf
| where ObjectName == "ASRAnalytics"
| where InstanceName contains "win-9r7sfh9qlru_disk0"
| where TimeGenerated >= ago(4h) 
| project TimeGenerated ,CounterName, Churn_MBps = todouble(CounterValue)/5242880 
| render timechart
```
进程服务器每5分钟将此数据推送到 Log Analytics 工作区。 这些数据点表示平均计算时间为5分钟。

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>查询灾难恢复摘要（Azure 到 Azure）

此查询绘制已复制到次要 Azure 区域的 Azure VM 的摘要表格。  其中显示 VM 名称、复制和保护状态、RPO、测试故障转移状态、移动代理版本、任何活动的复制错误以及源位置。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>查询灾难恢复摘要（VMware/物理服务器）

此查询绘制已复制到 Azure 的 VMware VM 和物理服务器的摘要表格。  其中显示计算机名称、复制和保护状态、RPO、测试故障转移状态、移动代理版本、任何活动的复制错误以及相关的进程服务器。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>设置警报 - 示例

可以基于 Azure Monitor 数据设置 Site Recovery 警报。 [详细了解](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)如何设置日志警报。 

> [!NOTE]
> 其中一些示例使用设置为 **A2A** 的 **replicationProviderName_s**。 这会针对已复制到次要 Azure 区域的 Azure VM 设置警报。 若要针对已复制到 Azure 的本地 VMware VM 或物理服务器设置警报，可在这些示例中将 **A2A** 替换为 **InMageAzureV2**。

### <a name="multiple-machines-in-a-critical-state"></a>多台计算机处于严重状态

如果有 20 台以上的已复制 Azure VM 进入严重状态，则设置警报。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
对于警报，请将“阈值”设置为 20。

### <a name="single-machine-in-a-critical-state"></a>一台计算机处于严重状态

如果特定的已复制 Azure VM 进入严重状态，则设置警报。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
对于警报，请将“阈值”设置为 1。

### <a name="multiple-machines-exceed-rpo"></a>多台计算机超过 RPO

如果有 20 台以上的 Azure VM 的 RPO 超过 30 分钟，则设置警报。
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
对于警报，请将“阈值”设置为 20。

### <a name="single-machine-exceeds-rpo"></a>一台计算机超过 RPO

如果只有一台 Azure VM 的 RPO 超过 30 分钟，则设置警报。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where name_s == "ContosoVM123"  
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
对于警报，请将“阈值”设置为 1。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>多台计算机的测试故障转移超过 90 天

如果 20 台以上的 VM 的上次成功测试故障转移超过 90 天，则设置警报。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
对于警报，请将“阈值”设置为 20。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>一台计算机的测试故障转移超过 90 天

如果某个特定 VM 的上次成功测试故障转移超过 90 天，则设置警报。
```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| where name_s == "ContosoVM123"  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
对于警报，请将“阈值”设置为 1。

### <a name="site-recovery-job-fails"></a>Site Recovery 作业失败

如果在过去一天，某个 Site Recovery 作业（在本例中为“重新保护”作业）在任何 Site Recovery 方案中失败，则设置警报。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

对于警报，请将“阈值”设置为 1，将“期限”设置为 1440 分钟，以检查过去一天发生的失败。

## <a name="next-steps"></a>后续步骤

[了解](site-recovery-monitor-and-troubleshoot.md)内置的 Site Recovery 监视。
