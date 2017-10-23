---
title: "Azure Log Analytics 查询语言备忘单 | Microsoft Docs"
description: "本文提供有关转换到新的 Log Analytics 查询语言的帮助内容，前提是用户已熟悉旧的语言。"
services: operations-management-suite
documentationcenter: 
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/23/2017
ms.author: bwren
ms.openlocfilehash: 50713d69f6dce6b7b154b6b4a6df3f679eb7b7c7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="transitioning-to-azure-log-analytics-new-query-language"></a>转换到 Azure Log Analytics 新查询语言

> [!NOTE]
> 若要了解有关新的 Log Analytics 查询语言的详细信息和工作区的升级过程，请参阅[将 Azure Log Analytics 工作区升级到新的日志搜索](log-analytics-log-search-upgrade.md)。

本文提供有关转换到新的 Log Analytics 查询语言的帮助内容，前提是用户已熟悉旧的语言。

## <a name="language-converter"></a>语言转换器

如果熟悉旧的 Log Analytics 查询语言，则采用新语言创建相同的查询的最简单方法是使用转换工作区时安装在日志搜索门户中的语言转换器。  与在旧查询顶部的文本框中键入然后单击“转换”一样，转换器的使用非常简单。  可以单击“搜索”按钮运行查询，或者将其复制并粘贴到其他位置使用。

![语言转换器](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="cheat-sheet"></a>备忘单

下表提供分别使用新、旧查询语言在 Azure Log Analytics 中对等效命令进行各种常见查询时的查询间比较。

| 说明 | 旧的 | 新的 |
|:--|:--|:--|
| 搜索所有表格      | error | 搜索“错误”（不区分大小写） |
| 从表中选择数据 | Type=Event |  事件 |
|                        | Type=Event &#124; select Source, EventLog, EventID | Event &#124; project Source, EventLog, EventID |
|                        | Type=Event &#124; top 100 | Event &#124; take 100 |
| 字符串比较      | Type=Event Computer=srv01.contoso.com   | Event &#124; where Computer == "srv01.contoso.com" |
|                        | Type=Event Computer=contains("contoso") | Event &#124; where Computer contains "contoso"（不区分大小写）<br>Event &#124; where Computer contains_cs "Contoso"（不区分大小写） |
|                        | Type=Event Computer=RegEx("@contoso@")  | Event &#124; where Computer matches regex ".*contoso*" |
| 日期比较        | Type=Event TimeGenerated > NOW-1DAYS | Event &#124; where TimeGenerated > ago(1d) |
|                        | Type=Event TimeGenerated>2017-05-01 TimeGenerated<2017-05-31 | Event &#124; where TimeGenerated between (datetime(2017-05-01) .. datetime(2017-05-31)) |
| 布尔比较     | Type=Heartbeat IsGatewayInstalled=false  | 检测信号 \| where IsGatewayInstalled == false |
| 排序                   | Type=Event &#124; sort Computer asc, EventLog desc, EventLevelName asc | Event \| sort by Computer asc, EventLog desc, EventLevelName asc |
| 区别               | Type=Event &#124; dedup Computer \| select Computer | Event &#124; summarize by Computer, EventLog |
| 扩展列         | Type=Perf CounterName="% Processor Time" &#124; EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION | Perf &#124; where CounterName == "% Processor Time" \| extend Utilization = iff(CounterValue > 50, "HIGH", "LOW") |
| 聚合            | Type=Event &#124; measure count() as Count by Computer | Event &#124; summarize Count = count() by Computer |
|                                | Type=Perf ObjectName=Processor CounterName="% Processor Time" &#124; measure avg(CounterValue) by Computer interval 5minute | Perf &#124; where ObjectName=="Processor" and CounterName=="% Processor Time" &#124; summarize avg(CounterValue) by Computer, bin(TimeGenerated, 5min) |
| 带限制的聚合 | Type=Event &#124; measure count() by Computer &#124; top 10 | Event &#124; summarize AggregatedValue = count() by Computer &#124; limit 10 |
| 联合                  | Type=Event or Type=Syslog | union Event, Syslog |
| Join                   | Type=NetworkMonitoring &#124; join inner AgentIP (Type=Heartbeat) ComputerIP | NetworkMonitoring &#124; join kind=inner (search Type == "Heartbeat") on $left.AgentIP == $right.ComputerIP |



## <a name="next-steps"></a>后续步骤
- 查看使用新查询语言的[查询编写教程](https://go.microsoft.com/fwlink/?linkid=856078)。
- 若要深入了解新查询语言的所有命令、运算符和函数，请参阅[查询语言参考](https://go.microsoft.com/fwlink/?linkid=856079)。  
