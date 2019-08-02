---
title: 用 Azure Monitor 日志监视 Azure Site Recovery (Log Analytics)
description: 了解如何通过 Azure Monitor 日志监视 Azure Site Recovery (Log Analytics)
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: raynew
ms.openlocfilehash: 4eb88658437d3b29cc55d24bb83f73b660daea43
ms.sourcegitcommit: a52f17307cc36640426dac20b92136a163c799d0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/01/2019
ms.locfileid: "68718478"
---
# <a name="monitor-site-recovery-with-azure-monitor-logs"></a>使用 Azure Monitor 日志监视 Site Recovery

本文介绍如何使用[Azure Monitor 日志](../azure-monitor/platform/data-platform-logs.md)和[Log Analytics](../azure-monitor/log-query/log-query-overview.md)监视 Azure [Site Recovery](site-recovery-overview.md)复制的计算机。

Azure Monitor 日志提供了一个日志数据平台, 该平台收集活动和诊断日志以及其他监视数据。 在 Azure Monitor 日志中, 可以使用 Log Analytics 来编写和测试日志查询, 并以交互方式分析日志数据。 可以可视化和查询日志结果, 并配置警报以根据监视的数据执行操作。

对于 Site Recovery, 你可以 Azure Monitor 日志来帮助你执行以下操作:

- **监视 Site Recovery 运行状况和状态**。 例如, 你可以监视复制运行状况、测试故障转移状态、Site Recovery 事件、受保护计算机的恢复点目标 (Rpo) 以及磁盘/数据更改率。
- **为 Site Recovery 设置警报**。 例如, 可以为计算机运行状况、测试故障转移状态或 Site Recovery 作业状态配置警报。

在 Azure 到 Azure 的复制和 VMware VM/物理服务器到 Azure 的复制中, 支持使用 Site Recovery Azure Monitor 日志。
## <a name="before-you-start"></a>开始之前

下面是需要的项：

- 至少一个在恢复服务保管库中受保护的计算机。
- 用于存储 Site Recovery 日志的 Log Analytics 工作区。 [了解如何](../azure-monitor/learn/quick-create-workspace.md)设置工作区。
- 基本了解如何在 Log Analytics 中编写、运行和分析日志查询。 [了解详细信息](../azure-monitor/log-query/get-started-portal.md)。

建议在开始之前查看[常见的监视问题](monitoring-common-questions.md)。

## <a name="configure-site-recovery-to-send-logs"></a>配置 Site Recovery 以便发送日志

1. 在保管库中, 单击 "**诊断设置** > " "**添加诊断设置**"。

    ![选择诊断日志记录](./media/monitoring-log-analytics/add-diagnostic.png)

2. 在 "**诊断设置**" 中, 指定日志操作的名称, 并选择 "**发送到 Log Analytics**"。
3. 选择 Azure Monitor 日志订阅和 Log Analytics 工作区。
4. 从日志列表中, 选择前缀为**AzureSiteRecovery**的所有日志。 然后单击“确定”。

    ![选择工作区](./media/monitoring-log-analytics/select-workspace.png)

Site Recovery 日志开始进到选定工作区中的表 (**AzureDiagnostics**)。


## <a name="query-the-logs---examples"></a>查询日志-示例

使用以[Kusto 查询语言](../azure-monitor/log-query/get-started-queries.md)编写的日志查询从日志中检索数据。 本部分提供可用于 Site Recovery 监视的常见查询的几个示例。

> [!NOTE]
> 其中一些示例使用**replicationProviderName_s**设置为**A2A**。 这会检索使用 Site Recovery 复制到辅助 Azure 区域的 Azure Vm。 在这些示例中, 如果要检索使用 Site Recovery 复制到 Azure 的本地 VMware Vm 或物理服务器, 可以将**A2A**替换为**InMageAzureV2**。


### <a name="query-replication-health"></a>查询复制运行状况

此查询绘制了所有受保护 Azure Vm 的当前复制运行状况的饼图, 分为三种状态:正常、警告或严重。

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

此查询为随 Site Recovery 一起复制的 Azure Vm 绘制饼图, 并按其运行的移动代理版本细分。

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

此查询绘制使用 Site Recovery 进行复制的 Azure Vm 的条形图, 并按恢复点目标 (RPO) 细分:不到15分钟, 介于15-30 分钟之间, 超过30分钟。

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

此查询检索在过去72小时内触发的所有 Site Recovery 作业 (适用于所有灾难恢复方案) 及其完成状态。

```
AzureDiagnostics  
| where Category == "AzureSiteRecoveryJobs"  
| where TimeGenerated >= ago(72h)   
| project JobName = OperationName , VaultName = Resource , TargetName = affectedResourceName_s, State = ResultType  
```

### <a name="query-site-recovery-events"></a>查询 Site Recovery 事件

此查询检索在过去72小时内引发的所有 Site Recovery 事件 (适用于所有灾难恢复方案) 及其严重性。 

```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryEvents"   
| where TimeGenerated >= ago(72h)   
| project AffectedObject=affectedResourceName_s , VaultName = Resource, Description_s = healthErrors_s , Severity = Level  
```

### <a name="query-test-failover-state-pie-chart"></a>查询测试故障转移状态 (饼图)

此查询绘制一张饼图, 其中包含 Site Recovery 复制的 Azure Vm 的测试故障转移状态。

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

### <a name="query-test-failover-state-table"></a>查询测试故障转移状态 (表)

此查询为使用 Site Recovery 复制的 Azure Vm 的测试故障转移状态绘制一个表。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where isnotempty(failoverHealth_s) and isnotnull(failoverHealth_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , VaultName = Resource , TestFailoverStatus = failoverHealth_s 
```

### <a name="query-machine-rpo"></a>查询计算机 RPO

此查询绘制一个趋势图, 该图跟踪过去72小时内特定 Azure VM (ContosoVM123) 的 RPO。

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

### <a name="query-data-change-rate-churn-for-a-vm"></a>查询 VM 的数据更改率 (变动)

此查询绘制特定 Azure VM (ContosoVM123) 的趋势图, 该图跟踪数据更改率 (每秒写入的字节数) 和数据上传速率。 此信息仅适用于复制到辅助 Azure 区域的 Azure Vm。

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
![查询数据更改](./media/monitoring-log-analytics/example3.png)

### <a name="query-disaster-recovery-summary-azure-to-azure"></a>查询灾难恢复摘要 (Azure 到 Azure)

此查询为复制到辅助 Azure 区域的 Azure Vm 绘制摘要表。  其中显示了 VM 名称、复制和保护状态、RPO、测试故障转移状态、移动代理版本、任何活动的复制错误以及源位置。

```
AzureDiagnostics 
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, SourceLocation = primaryFabricName_s 
```

### <a name="query-disaster-recovery-summary-vmwarephysical-servers"></a>查询灾难恢复摘要 (VMware/物理服务器)

此查询为复制到 Azure 的 VMware Vm 和物理服务器绘制一个摘要表。  它显示计算机名称、复制和保护状态、RPO、测试故障转移状态、移动代理版本、任何活动的复制错误以及相关的进程服务器。

```
AzureDiagnostics  
| where replicationProviderName_s == "InMageAzureV2"   
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project VirtualMachine = name_s , Vault = Resource , ReplicationHealth = replicationHealth_s, Status = protectionState_s, RPO_in_seconds = rpoInSeconds_d, TestFailoverStatus = failoverHealth_s, AgentVersion = agentVersion_s, ReplicationError = replicationHealthErrors_s, ProcessServer = processServerName_g  
```

## <a name="set-up-alerts---examples"></a>设置警报-示例

您可以基于 Azure Monitor 数据设置 Site Recovery 警报。 [详细了解](../azure-monitor/platform/alerts-log.md#managing-log-alerts-from-the-azure-portal)如何设置日志警报。 

> [!NOTE]
> 其中一些示例使用**replicationProviderName_s**设置为**A2A**。 这会为复制到辅助 Azure 区域的 Azure Vm 设置警报。 在这些示例中, 如果要为复制到 Azure 的本地 VMware Vm 或物理服务器设置警报, 可以将**A2A**替换为**InMageAzureV2** 。

### <a name="multiple-machines-in-a-critical-state"></a>处于严重状态的多台计算机

当超过20个复制的 Azure Vm 进入严重状态时, 设置警报。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count() 
```
对于警报, 将 "**阈值**" 设置为20。

### <a name="single-machine-in-a-critical-state"></a>处于严重状态的单台计算机

如果特定的复制 Azure VM 进入严重状态, 请设置警报。

```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where replicationHealth_s == "Critical"  
| where name_s == "ContosoVM123"  
| where isnotempty(name_s) and isnotnull(name_s)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
对于警报, 将**阈值**设置为1。

### <a name="multiple-machines-exceed-rpo"></a>多台计算机超过 RPO

如果超过20个 Azure Vm 的 RPO 超过30分钟, 则设置警报。
```
AzureDiagnostics   
| where replicationProviderName_s == "A2A"   
| where isnotempty(name_s) and isnotnull(name_s)   
| where rpoInSeconds_d > 1800  
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| project name_s , rpoInSeconds_d   
| summarize count()  
```
对于警报, 将 "**阈值**" 设置为20。

### <a name="single-machine-exceeds-rpo"></a>单台计算机超过 RPO

如果单个 Azure VM 的 RPO 超过30分钟, 则设置警报。

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
对于警报, 将**阈值**设置为1。

### <a name="test-failover-for-multiple-machines-exceeds-90-days"></a>多台计算机的测试故障转移超过90天

如果上次成功的测试故障转移超过90天, 则为20个以上的 Vm 设置警报。 

```
AzureDiagnostics  
| where replicationProviderName_s == "A2A"   
| where Category == "AzureSiteRecoveryReplicatedItems"  
| where isnotempty(name_s) and isnotnull(name_s)   
| where lastSuccessfulTestFailoverTime_t <= ago(90d)   
| summarize hint.strategy=partitioned arg_max(TimeGenerated, *) by name_s   
| summarize count()  
```
对于警报, 将 "**阈值**" 设置为20。

### <a name="test-failover-for-single-machine-exceeds-90-days"></a>单台计算机的测试故障转移超过90天

如果特定 VM 的上次成功的测试故障转移超过90天之前, 请设置警报。
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
对于警报, 将**阈值**设置为1。

### <a name="site-recovery-job-fails"></a>Site Recovery 作业失败

如果 Site Recovery 作业 (在这种情况下, 重新保护作业) 对于任何 Site Recovery 方案都失败, 则设置警报, 最后一天。 
```
AzureDiagnostics   
| where Category == "AzureSiteRecoveryJobs"   
| where OperationName == "Reprotect"  
| where ResultType == "Failed"  
| summarize count()  
```

对于警报, 将 "**阈值**" 设置为 "1", 将 "**期间**" 设置为 "1440 分钟", 以检查最后一天中的失败。

## <a name="next-steps"></a>后续步骤

[了解](site-recovery-monitor-and-troubleshoot.md)内置 Site Recovery 监视。
