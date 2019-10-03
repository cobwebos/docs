---
title: Azure Monitor 日志查询示例 | Microsoft Docs
description: 使用 Kusto 查询语言在 Azure Monitor 中进行日志查询的示例。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/01/2019
ms.author: bwren
ms.openlocfilehash: 7cdd471e6618e83483f6cc304f284a1669f3b67b
ms.sourcegitcommit: a19f4b35a0123256e76f2789cd5083921ac73daf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/02/2019
ms.locfileid: "71718914"
---
# <a name="azure-monitor-log-query-examples"></a>Azure Monitor 日志查询示例
本文包含使用 [Kusto 查询语言](/azure/kusto/query/)从 Azure Monitor 中检索不同类型的日志数据的各种[查询](log-query-overview.md)示例。 其中使用了不同的方法来合并和分析数据，因此，你可以使用这些示例来识别符合自身要求的不同策略。  

有关这些示例中使用的不同关键字的详细信息，请参阅 [Kusto 语言参考](https://docs.microsoft.com/azure/kusto/query/)。 如果你是初次接触 Azure Monitor，请仔细阅读[有关创建查询的课程](get-started-queries.md)。

## <a name="events"></a>Events

### <a name="search-application-level-events-described-as-cryptographic"></a>搜索描述为“加密”的应用程序级事件
此示例在 **Events** 表中搜索 **EventLog** 为 _Application_ 并且 **RenderedDescription** 包含 _cryptographic_ 的记录。 包括过去 24 小时的记录。

```Kusto
Event
| where EventLog == "Application" 
| where TimeGenerated > ago(24h) 
| where RenderedDescription contains "cryptographic"
```

### <a name="search-events-related-to-unmarshaling"></a>搜索拆收相关的事件
在 Event 和 SecurityEvents 表中搜索提到了 unmashaling 的记录。

```Kusto
search in (Event, SecurityEvent) "unmarshaling"
```

## <a name="heartbeat"></a>检测信号

### <a name="chart-a-week-over-week-view-of-the-number-of-computers-sending-data"></a>绘制包含发送数据的计算机数的每周视图

以下示例绘制每周发送检测信号的非重复计算机数的图表。

```Kusto
Heartbeat
| where TimeGenerated >= startofweek(ago(21d))
| summarize dcount(Computer) by endofweek(TimeGenerated) | render barchart kind=default
```

### <a name="find-stale-computers"></a>查找陈旧的计算机

以下示例查找在前一日处于活动状态，但过去一小时未发送检测信号的计算机。

```Kusto
Heartbeat
| where TimeGenerated > ago(1d)
| summarize LastHeartbeat = max(TimeGenerated) by Computer
| where isnotempty(Computer)
| where LastHeartbeat < ago(1h)
```

### <a name="get-the-latest-heartbeat-record-per-computer-ip"></a>获取每个计算机 IP 的最新检测信号记录

此示例返回每个计算机 IP 的最后一条检测信号记录。
```Kusto
Heartbeat
| summarize arg_max(TimeGenerated, *) by ComputerIP
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>将受保护状态记录与检测信号记录相匹配

此示例查找按“计算机”和时间匹配的相关保护状态记录和检测信号记录。
请注意，时间字段将舍入为最接近的分钟。 我们已使用运行时 bin 计算来实现此目的：`round_time=bin(TimeGenerated, 1m)`。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```

### <a name="server-availability-rate"></a>服务器可用率

基于检测信号记录计算服务器可用率。 可用性定义为“每小时至少发送了 1 个检测信号”。
因此，如果某台服务器在 100 个小时中有 98 个小时保持可用状态，则可用率为 98%。

```Kusto
let start_time=startofday(datetime("2018-03-01"));
let end_time=now();
Heartbeat
| where TimeGenerated > start_time and TimeGenerated < end_time
| summarize heartbeat_per_hour=count() by bin_at(TimeGenerated, 1h, start_time), Computer
| extend available_per_hour=iff(heartbeat_per_hour>0, true, false)
| summarize total_available_hours=countif(available_per_hour==true) by Computer 
| extend total_number_of_buckets=round((end_time-start_time)/1h)+1
| extend availability_rate=total_available_hours*100/total_number_of_buckets
```


## <a name="multiple-data-types"></a>多个数据类型

### <a name="chart-the-record-count-per-table"></a>绘制每个表的记录计数图表
以下示例收集过去五个小时内所有表的所有记录，并统计每个表中的记录数。 结果将在时间表中显示。

```Kusto
union withsource=sourceTable *
| where TimeGenerated > ago(5h) 
| summarize count() by bin(TimeGenerated,10m), sourceTable
| render timechart
```

### <a name="count-all-logs-collected-over-the-last-hour-by-type"></a>按类型统计过去一小时收集的所有日志
以下示例搜索过去一小时内报告的任何内容，并按“类型”统计每个表的记录数。 结果将在条形图中显示。

```Kusto
search *
| where TimeGenerated > ago(1h) 
| summarize CountOfRecords = count() by Type
| render barchart
```

## <a name="azurediagnostics"></a>AzureDiagnostics

### <a name="count-azure-diagnostics-records-per-category"></a>按类别统计 Azure 诊断记录数
此示例统计每个唯一类别的所有 Azure 诊断记录数。

```Kusto
AzureDiagnostics 
| where TimeGenerated > ago(1d)
| summarize count() by Category
```

### <a name="get-a-random-record-for-each-unique-category"></a>获取每个唯一类别的随机记录
此示例获取每个唯一类别的单个随机 Azure 诊断记录。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize any(*) by Category
```

### <a name="get-the-latest-record-per-category"></a>获取每个类别的最新记录
此示例获取每个唯一类别中的最新 Azure 诊断记录。

```Kusto
AzureDiagnostics
| where TimeGenerated > ago(1d) 
| summarize arg_max(TimeGenerated, *) by Category
```

## <a name="network-monitoring"></a>网络监视

### <a name="computers-with-unhealthy-latency"></a>延迟不正常的计算机
此示例创建延迟不正常的非重复计算机列表。

```Kusto
NetworkMonitoring 
| where LatencyHealthState <> "Healthy" 
| where Computer != "" 
| distinct Computer
```

## <a name="performance"></a>性能

### <a name="join-computer-perf-records-to-correlate-memory-and-cpu"></a>联接计算机 perf 记录以关联内存和 CPU
此示例关联特定计算机的 **perf** 记录，并创建包含平均 CPU 和最大内存的两个时间图表。

```Kusto
let StartTime = now()-5d;
let EndTime = now()-4d;
Perf
| where CounterName == "% Processor Time"  
| where TimeGenerated > StartTime and TimeGenerated < EndTime
and TimeGenerated < EndTime
| project TimeGenerated, Computer, cpu=CounterValue 
| join kind= inner (
   Perf
    | where CounterName == "% Used Memory"  
    | where TimeGenerated > StartTime and TimeGenerated < EndTime
    | project TimeGenerated , Computer, mem=CounterValue 
) on TimeGenerated, Computer
| summarize avgCpu=avg(cpu), maxMem=max(mem) by TimeGenerated bin=30m  
| render timechart
```

### <a name="perf-cpu-utilization-graph-per-computer"></a>每台计算机的 Perf CPU 利用率图形
此示例计算以 _Contoso_ 开头的计算机的 CPU 利用率并绘制图表。

```Kusto
Perf
| where TimeGenerated > ago(4h)
| where Computer startswith "Contoso" 
| where CounterName == @"% Processor Time"
| summarize avg(CounterValue) by Computer, bin(TimeGenerated, 15m) 
| render timechart
```

## <a name="protection-status"></a>保护状态

### <a name="computers-with-non-reporting-protection-status-duration"></a>处于“未报告”保护状态的计算机以及此状态的持续时间
此示例列出保护状态为 _Not Reporting_ 的计算机，及其处于此状态的持续时间。

```Kusto
ProtectionStatus
| where ProtectionStatus == "Not Reporting"
| summarize count(), startNotReporting = min(TimeGenerated), endNotReporting = max(TimeGenerated) by Computer, ProtectionStatusDetails
| join ProtectionStatus on Computer
| summarize lastReporting = max(TimeGenerated), startNotReporting = any(startNotReporting), endNotReporting = any(endNotReporting) by Computer
| extend durationNotReporting = endNotReporting - startNotReporting
```

### <a name="match-protected-status-records-with-heartbeat-records"></a>将受保护状态记录与检测信号记录相匹配
此示例查找按“计算机”和时间匹配的相关保护状态记录和检测信号记录。
时间字段将使用 **bin** 舍入为最接近的分钟。

```Kusto
let protection_data = ProtectionStatus
    | project Computer, DetectionId, round_time=bin(TimeGenerated, 1m);
let heartbeat_data = Heartbeat
    | project Computer, Category, round_time=bin(TimeGenerated, 1m);
protection_data | join (heartbeat_data) on Computer, round_time
```


## <a name="security-records"></a>安全记录

### <a name="count-security-events-by-activity-id"></a>按活动 ID 统计安全事件数


此示例依赖于 **Activity** 列的固定结构：\<ID\>-\<名称\>。
它将 **Activity** 值分析为两个新列，并统计每个 **activityID** 的出现次数。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m) 
| project Activity 
| parse Activity with activityID " - " activityDesc
| summarize count() by activityID
```

### <a name="count-security-events-related-to-permissions"></a>统计与权限相关的安全事件数
此示例中显示 **securityEvent** 记录的数目，这些记录中的 **Activity** 列中包含整个单词 _Permissions_。 查询将应用到在过去 30 分钟内创建的记录。

```Kusto
SecurityEvent
| where TimeGenerated > ago(30m)
| summarize EventCount = countif(Activity has "Permissions")
```

### <a name="find-accounts-that-failed-to-log-in-from-computers-with-a-security-detection"></a>查找无法从启用了安全检测的计算机登录的帐户
此示例查找无法从启用了安全检测的计算机登录的帐户，并统计其数目。

```Kusto
let detections = toscalar(SecurityDetection
| summarize makeset(Computer));
SecurityEvent
| where Computer in (detections) and EventID == 4624
| summarize count() by Account
```

### <a name="is-my-security-data-available"></a>我的安全数据是否可用？
数据探索通常是从数据可用性检查开始的。 此示例显示过去 30 分钟内的 **SecurityEvent** 记录数。

```Kusto
SecurityEvent 
| where TimeGenerated  > ago(30m) 
| count
```

### <a name="parse-activity-name-and-id"></a>分析活动名称和 ID
以下两个示例依赖于 **Activity** 列的固定结构：\<ID\>-\<名称\>。 第一个示例使用 **parse** 运算符将值分配给两个新列：**activityID** 和 **activityDesc**。

```Kusto
SecurityEvent
| take 100
| project Activity 
| parse Activity with activityID " - " activityDesc
```

此示例使用 **split** 运算符创建不同值的数组
```Kusto
SecurityEvent
| take 100
| project Activity 
| extend activityArr=split(Activity, " - ") 
| project Activity , activityArr, activityId=activityArr[0]
```

### <a name="explicit-credentials-processes"></a>显式凭据进程
以下示例显示过去一周内使用了显式凭据的进程的饼图

```Kusto
SecurityEvent
| where TimeGenerated > ago(7d)
// filter by id 4648 ("A logon was attempted using explicit credentials")
| where EventID == 4648
| summarize count() by Process
| render piechart 
```

### <a name="top-running-processes"></a>最常见的运行中进程

以下示例显示过去三天内五个最常见进程的活动时间线。

```Kusto
// Find all processes that started in the last three days. ID 4688: A new process has been created.
let RunProcesses = 
    SecurityEvent
    | where TimeGenerated > ago(3d)
    | where EventID == "4688";
// Find the 5 processes that were run the most
let Top5Processes =
RunProcesses
| summarize count() by Process
| top 5 by count_;
// Create a time chart of these 5 processes - hour by hour
RunProcesses 
| where Process in (Top5Processes) 
| summarize count() by bin (TimeGenerated, 1h), Process
| render timechart
```


### <a name="find-repeating-failed-login-attempts-by-the-same-account-from-different-ips"></a>查找同一帐户从不同的 IP 重复登录但失败的尝试

以下示例查找过去六个小时内同一帐户从不同的 IP 重复登录但失败的尝试。

```Kusto
SecurityEvent 
| where AccountType == "User" and EventID == 4625 and TimeGenerated > ago(6h) 
| summarize IPCount = dcount(IpAddress), makeset(IpAddress)  by Account
| where IPCount > 5
| sort by IPCount desc
```

### <a name="find-user-accounts-that-failed-to-log-in"></a>查找无法登录的用户帐户 
以下示例识别过去一天登录失败超过五次的用户帐户，及其上次尝试登录的时间。

```Kusto
let timeframe = 1d;
SecurityEvent
| where TimeGenerated > ago(1d)
| where AccountType == 'User' and EventID == 4625 // 4625 - failed log in
| summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
| where failed_login_attempts > 5
| project-away Account1
```

使用 **join** 和 **let** 语句可以检查相同的可疑帐户后来是否能够成功登录。

```Kusto
let timeframe = 1d;
let suspicious_users = 
    SecurityEvent
    | where TimeGenerated > ago(timeframe)
    | where AccountType == 'User' and EventID == 4625 // 4625 - failed login
    | summarize failed_login_attempts=count(), latest_failed_login=arg_max(TimeGenerated, Account) by Account 
    | where failed_login_attempts > 5
    | project-away Account1;
let suspicious_users_that_later_logged_in = 
    suspicious_users 
    | join kind=innerunique (
        SecurityEvent
        | where TimeGenerated > ago(timeframe)
        | where AccountType == 'User' and EventID == 4624 // 4624 - successful login,
        | summarize latest_successful_login=arg_max(TimeGenerated, Account) by Account
    ) on Account
    | extend was_login_after_failures = iif(latest_successful_login>latest_failed_login, 1, 0)
    | where was_login_after_failures == 1
;
suspicious_users_that_later_logged_in
```

## <a name="usage"></a>用法

### <a name="calculate-the-average-size-of-perf-usage-reports-per-computer"></a>计算每台计算机的 perf 使用率报告的平均大小

此示例计算过去 3 小时内每台计算机的 perf 使用率报告的平均大小。
结果将显示在条形图中。
```Kusto
Usage 
| where TimeGenerated > ago(3h)
| where DataType == "Perf" 
| where QuantityUnit == "MBytes" 
| summarize avg(Quantity) by Computer
| sort by avg_Quantity desc nulls last
| render barchart
```

### <a name="timechart-latency-percentiles-50-and-95"></a>时间表延迟第 50 和 95 百分位

此示例计算过去 24 小时内每小时报告的 **avgLatency** 的第 50 和 95 百分位并绘制图表。

```Kusto
Usage
| where TimeGenerated > ago(24h)
| summarize percentiles(AvgLatencyInSeconds, 50, 95) by bin(TimeGenerated, 1h) 
| render timechart
```

### <a name="usage-of-specific-computers-today"></a>当天特定计算机的使用情况
此示例包含字符串 _ContosoFile_ 的计算机名在过去一天的**使用情况**数据。 结果将按 **TimeGenerated** 排序。

```Kusto
Usage
| where TimeGenerated > ago(1d)
| where  Computer contains "ContosoFile" 
| sort by TimeGenerated desc nulls last
```

## <a name="updates"></a>更新

### <a name="computers-still-missing-updates"></a>仍缺少更新的计算机
此示例显示几天前缺少一个或多个关键更新并且当前仍然缺少更新的计算机列表。

```Kusto
let ComputersMissingUpdates3DaysAgo = Update
| where TimeGenerated between (ago(30d)..ago(1h))
| where Classification !has "Critical" and UpdateState =~ "Needed"
| summarize makeset(Computer);
Update
| where TimeGenerated > ago(1d)
| where Classification has "Critical" and UpdateState =~ "Needed"
| where Computer in (ComputersMissingUpdates3DaysAgo)
| summarize UniqueUpdatesCount = dcount(Product) by Computer, OSType
```


## <a name="next-steps"></a>后续步骤

- 有关语言的详细信息，请参阅 [Kusto 语言参考](/azure/kusto/query)。
- 演练[有关在 Azure Monitor 中编写日志查询的课程](get-started-queries.md)。
