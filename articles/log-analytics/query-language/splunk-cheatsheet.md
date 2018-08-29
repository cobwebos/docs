---
title: 从 Splunk 到 Azure Log Analytics | Microsoft Docs
description: 帮助熟悉 Splunk 的用户学习 Log Analytics 查询语言。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/21/2018
ms.author: bwren
ms.component: na
ms.openlocfilehash: 5ea9790695b8afe7bd42b98b071869756b301350
ms.sourcegitcommit: fab878ff9aaf4efb3eaff6b7656184b0bafba13b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2018
ms.locfileid: "42447131"
---
# <a name="splunk-to-log-analytics"></a>从 Splunk 到 Log Analytics

本文旨在帮助熟悉 Splunk 的用户学习 Log Analytics 查询语言。 其中将两者做了直接的比较，让用户了解它们的主要差异和相似之处，并抉机利用现有的知识。

## <a name="structure-and-concepts"></a>结构和概念

下表比较了 Splunk 和 Log Analytics 的概念与数据结构。

 | 概念  | Splunk | Log Analytics |  注释
 | --- | --- | --- | ---
 | 部署单元  | cluster |  cluster |  Log Analytics 允许群集查询的任意性， Splunk 则不允许。 |
 | 数据缓存 |  存储桶  |  缓存和保留策略 |  控制数据的保留期和缓存级别。 此设置直接影响查询性能和部署成本。 |
 | 数据的逻辑分区  |  index  |  database  |  允许数据的逻辑隔离。 这两个实现都允许跨这些分区的联合与联接。 |
 | 结构化事件元数据 | 不适用 | 表 |  Splunk 没有向事件元数据搜索语言公开的概念。 Log Analytics 具有表的概念，表包含列。 每个事件实例映射到行。 |
 | 数据记录 | event | 行 |  仅限术语变化。 |
 | 数据记录属性 | 字段 |  列 |  在 Log Analytics 中，此概念预定义为表结构的一部分。 在 Splunk 中，每个事件有自身的字段集。 |
 | 类型 | 数据类型 |  数据类型 |  Log Analytics 数据类型更明确，因为它们是在列中设置的。 两者都能动态处理数据类型，数据类型集（包括 JSON 支持）大致相同。 |
 | 查询和搜索  | 搜索 | query |  Log Analytics 和 Splunk 的查询概念在本质上相同。 |
 | 数据引入时间 | 系统时间 | ingestion_time() |  在 Splunk 中，每个事件将获取编制事件索引时的系统时间戳。 在 Log Analytics 中，可以定义名为 ingestion_time 的策略，用于公开可通过 ingestion_time() 函数引用的系统列。 |

## <a name="functions"></a>函数

下表指定了 Log Analytics 中等效于 Splunk 函数的函数。

|Splunk | Log Analytics |注释
|---|---|---
|strcat | strcat()| (1) |
|split  | split() | (1) |
|if     | iff()   | (1) |
|tonumber | todouble()<br>tolong()<br>toint() | (1) |
|upper<br>lower |toupper()<br>tolower()|(1) |
| replace | replace() | (1)<br> 另请注意，尽管这两个产品中的 `replace()` 都采用三个参数，但这些参数不同。 |
| substr | substring() | (1)<br>另请注意，Splunk 使用从 1 开始的索引。 Log Analytics 使用从 0 开始的索引。 |
| tolower |  tolower() | (1) |
| toupper | toupper() | (1) |
| match | matches regex |  (2)  |
| regex | matches regex | 在 Splunk 中，`regex` 是运算符。 在 Log Analytics 中，它是关系运算符。 |
| searchmatch | == | 在 Splunk 中，`searchmatch` 允许搜索确切的字符串。
| random | rand()<br>rand(n) | Splunk 的函数返回从 0 到 2<sup>31</sup>-1 的数字。 Log Analytics 返回介于 0.0 和 1.0 之间的数字；如果提供了参数，则返回介于 0 和 n-1 之间的数字。
| now | now() | (1)
| relative_time | totimespan() | (1)<br>在 Log Analytics 中，与 Splunk 的 relative_time(datetimeVal, offsetVal) 等效的函数是 datetimeVal + totimespan(offsetVal)。<br>例如，<code>search &#124; eval n=relative_time(now(), "-1d@d")</code> 变成了 <code>...  &#124; extend myTime = now() - totimespan("1d")</code>。

(1) 在 Splunk 中，使用 `eval` 运算符调用该函数。 在 Log Analytics 中，它用作 `extend` 或 `project` 的一部分。<br>(2) 在 Splunk 中，使用 `eval` 运算符调用该函数。 在 Log Analytics 中，可以结合 `where` 运算符使用该函数。


## <a name="operators"></a>运算符

以下部分通过示例演示 Splunk 和 Log Analytics 如何使用不同的运算符。

> [!NOTE]
> 在以下示例中，Splunk 字段 _rule_ 映射到 Azure Log Analytics 中的某个表，Splunk 的默认时间戳映射到 Logs Analytics _ingestion_time()_ 列。

### <a name="search"></a>搜索
在 Splunk 中，可以省略 `search` 关键字，并指定不带引号的字符串。 在 Azure Log Analytics 中，必须在每个搜索的开头使用 `find`，不带引号的字符串是列名，查找值必须是带引号的字符串。 

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>search Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" earliest=-24h</code> |
| Log Analytics | **find** | <code>find Session.Id=="c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time()> ago(24h)</code> |
| | |

### <a name="filter"></a>筛选器
Azure Log Analytics 查询从包含筛选器的表格结果集开始。 在 Splunk 中，筛选是针对当前索引执行的默认操作。 还可以在 Splunk 中使用 `where` 运算符，但不建议。

| |  | |
|:---|:---|:---|
| Splunk | **search** | <code>Event.Rule="330009.2" Session.Id="c8894ffd-e684-43c9-9125-42adc25cd3fc" _indextime>-24h</code> |
| Log Analytics | **where** | <code>Office_Hub_OHubBGTaskError<br>&#124; where Session_Id == "c8894ffd-e684-43c9-9125-42adc25cd3fc" and ingestion_time() > ago(24h)</code> |
| | |


### <a name="getting-n-eventsrows-for-inspection"></a>获取用于检查的 n 个事件/行 
Azure Log Analytics 还支持将 `take` 用作 `limit` 的别名。 在 Splunk 中，如果结果已排序，则 `head` 将返回前 n 个结果。 在 Azure Log Analytics 中，limit 不会排序，而是返回找到的前 n 行。

| |  | |
|:---|:---|:---|
| Splunk | **head** | <code>Event.Rule=330009.2<br>&#124; head 100</code> |
| Log Analytics | **limit** | <code>Office_Hub_OHubBGTaskError<br>&#124; limit 100</code> |
| | |



### <a name="getting-the-first-n-eventsrows-ordered-by-a-fieldcolumn"></a>获取按字段/列排序的前 n 个事件/行
对于底部结果，在 Splunk 中可以使用 `tail`。 在 Azure Log Analytics 中，可以使用 `asc` 指定排序方向。

| |  | |
|:---|:---|:---|
| Splunk | **head** |  <code>Event.Rule="330009.2"<br>&#124; sort Event.Sequence<br>&#124; head 20</code> |
| Log Analytics | **返回页首** | <code>Office_Hub_OHubBGTaskError<br>&#124; top 20 by Event_Sequence</code> |
| | |




### <a name="extending-the-result-set-with-new-fieldscolumns"></a>使用新字段/列扩展结果集
Splunk 还有一个 `eval` 函数，该函数不能与 `eval` 运算符进行比较。 Splunk 中的 `eval` 运算符与 Azure Log Analytics 中的 `extend` 运算符仅支持标量函数和算术运算符。

| |  | |
|:---|:---|:---|
| Splunk | **eval** |  <code>Event.Rule=330009.2<br>&#124; eval state= if(Data.Exception = "0", "success", "error")</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend state = iif(Data_Exception == 0,"success" ,"error")</code> |
| | |


### <a name="rename"></a>重命名 
Azure Log Analytics 使用相同的运算符来重命名和新建字段。 Splunk 有两个独立的运算符：`eval` 和 `rename`。

| |  | |
|:---|:---|:---|
| Splunk | **rename** |  <code>Event.Rule=330009.2<br>&#124; rename Date.Exception as execption</code> |
| Log Analytics | **extend** | <code>Office_Hub_OHubBGTaskError<br>&#124; extend exception = Date_Exception</code> |
| | |




### <a name="format-resultsprojection"></a>设置结果格式/投影
Splunk 似乎没有类似于 `project-away` 的运算符。 可以使用 UI 来筛选字段。

| |  | |
|:---|:---|:---|
| Splunk | **table** |  <code>Event.Rule=330009.2<br>&#124; table rule, state</code> |
| Log Analytics | **project**<br>**project-away** | <code>Office_Hub_OHubBGTaskError<br>&#124; project exception, state</code> |
| | |



### <a name="aggregation"></a>聚合
有关不同的聚合函数，请参阅 [Log Analytics 查询中的聚合](aggregations.md)。

| |  | |
|:---|:---|:---|
| Splunk | **stats** |  <code>search (Rule=120502.*)<br>&#124; stats count by OSEnv, Audience</code> |
| Log Analytics | **summarize** | <code>Office_Hub_OHubBGTaskError<br>&#124; summarize count() by App_Platform, Release_Audience</code> |
| | |



### <a name="join"></a>Join
Splunk 中的联接具有很强的限制。 子查询限制为 10000 条结果（在部署配置文件中设置），联接形式数目也有限制。

| |  | |
|:---|:---|:---|
| Splunk | **join** |  <code>Event.Rule=120103* &#124; stats by Client.Id, Data.Alias | join Client.Id max=0 [search earliest=-24h Event.Rule="150310.0" Data.Hresult=-2147221040]</code> |
| Log Analytics | **join** | <code>cluster("OAriaPPT").database("Office PowerPoint").Office_PowerPoint_PPT_Exceptions<br>&#124; where  Data_Hresult== -2147221040<br>&#124; join kind = inner (Office_System_SystemHealthMetadata<br>&#124; summarize by Client_Id, Data_Alias)on Client_Id</code>   |
| | |



### <a name="sort"></a>排序
在 Splunk 中，若要按升序排序，必须使用 `reverse` 运算符。 Azure Log Analytics 还支持定义 null 值的放置位置：开头或末尾。

| |  | |
|:---|:---|:---|
| Splunk | **sort** |  <code>Event.Rule=120103<br>&#124; sort Data.Hresult<br>&#124; reverse</code> |
| Log Analytics | **order by** | <code>Office_Hub_OHubBGTaskError<br>&#124; order by Data_Hresult,  desc</code> |
| | |



### <a name="multivalue-expand"></a>多值扩展
此运算符在 Splunk 和 Log Analytics 中类似。

| |  | |
|:---|:---|:---|
| Splunk | **mvexpand** |  `mvexpand foo` |
| Log Analytics | **mvexpand** | `mvexpand foo` |
| | |




### <a name="results-facets-interesting-fields"></a>结果分面、相关字段
Log Analytics 门户中仅公开第一列。 可通过 API 查看所有列。

| |  | |
|:---|:---|:---|
| Splunk | **fields** |  <code>Event.Rule=330009.2<br>&#124; fields App.Version, App.Platform</code> |
| Log Analytics | **facets** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; facet by App_Branch, App_Version</code> |
| | |




### <a name="de-duplicate"></a>重复数据删除
可以改用 `summarize arg_min()` 来反转记录的选择顺序。

| |  | |
|:---|:---|:---|
| Splunk | **dedup** |  <code>Event.Rule=330009.2<br>&#124; dedup device_id sortby -batterylife</code> |
| Log Analytics | **summarize arg_max()** | <code>Office_Excel_BI_PivotTableCreate<br>&#124; summarize arg_max(batterylife, *) by device_id</code> |
| | |




## <a name="next-steps"></a>后续步骤

- 完成有关[在 Log Analytics 中编写查询](get-started-queries.md)的课程。