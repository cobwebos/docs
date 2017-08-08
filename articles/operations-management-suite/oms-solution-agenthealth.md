---
title: "OMS 中的代理运行状况解决方案 | Microsoft Docs"
description: "本文旨在帮助你了解如何使用此解决方案来监视代理的运行状况，这些代理直接向 OMS 或 System Center Operations Manager 报告。"
services: operations-management-suite
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 07/17/2017
ms.author: magoedte
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: b810e37e393ddab55500f636b72450789285a4f0
ms.contentlocale: zh-cn
ms.lasthandoff: 07/28/2017

---
#  <a name="agent-health-solution-in-oms"></a>OMS 中的代理运行状况解决方案
OMS 中的代理运行状况解决方案有助于你了解，在所有直接向 OMS 工作区报告或向连接到 OMS 的 System Center Operations Manager 管理组报告的代理中，哪些不响应且提交的是操作数据。  也可跟踪所部署代理的数目及其地理分布情况，并通过执行其他查询来不断了解在 Azure 或其他云环境中或本地部署的代理的分布情况。    

## <a name="prerequisites"></a>先决条件
在部署此解决方案之前，请确认你当前已安装受支持的 [Windows 代理](../log-analytics/log-analytics-windows-agents.md)，此类代理向 OMS 工作区报告或向与 OMS 工作区集成的 [Operations Manager 管理组](../log-analytics/log-analytics-om-agents.md)报告。    

## <a name="solution-components"></a>解决方案组件
此解决方案包含以下资源，这些资源添加到工作区和直接连接的代理或 Operations Manager 连接的管理组。

### <a name="management-packs"></a>管理包
如果 System Center Operations Manager 管理组已连接到 OMS 工作区，则会在 Operations Manager 中安装以下管理包。  在添加此解决方案以后，这些管理包也会安装在直接连接的 Windows 计算机上。 这些管理包不需进行配置或管理。

* Microsoft System Center Advisor HealthAssessment Direct Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentDirect)
* Microsoft System Center Advisor HealthAssessment Server Channel Intelligence Pack (Microsoft.IntelligencePacks.HealthAssessmentViaServer)。  

有关如何更新解决方案管理包的详细信息，请参阅[将 Operations Manager 连接到 Log Analytics](../log-analytics/log-analytics-om-agents.md)。

## <a name="configuration"></a>配置
执行[添加解决方案](../log-analytics/log-analytics-add-solutions.md)中所述的过程，将代理运行状况解决方案添加到 OMS 工作区。 无需进一步的配置。


## <a name="data-collection"></a>数据收集
### <a name="supported-agents"></a>支持的代理
下表介绍了该解决方案支持的连接的源。

| 连接的源 | 支持 | 说明 |
| --- | --- | --- |
| Windows 代理 | 是 | 检测信号事件从直接的 Windows 代理收集。|
| System Center Operations Manager 管理组 | 是 | 每 60 秒从向管理组报告的代理收集一次检测信号事件，然后将其转发到 Log Analytics。 不需要从 Operations Manager 代理直接连接到 Log Analytics。 检测信号事件数据从管理组转发到 Log Analytics 存储库。|

## <a name="using-the-solution"></a>使用解决方案
向 OMS 工作区添加解决方案时，“代理运行状况”磁贴将添加到 OMS 仪表板。 此磁贴显示过去 24 小时内的总代理数以及不响应的代理数。<br><br> ![仪表板上的“代理运行状况解决方案”磁贴](./media/oms-solution-agenthealth/agenthealth-solution-tile-homepage.png)

单击“代理运行状况”磁贴可打开“代理运行状况”仪表板。  仪表板包含下表中的列。 每个列按照指定时间范围内符合该列条件的计数列出了前十个事件。 可以通过在每一列右下方选择“查看全部”或单击列标题来运行提供整个列表的日志搜索。

| 列 | 说明 |
|--------|-------------|
| 某个时段的代理计数 | 在七天时段内的代理计数趋势（针对 Linux 和 Windows 代理）。|
| 无响应代理的计数 | 在过去 24 小时内未发送检测信号的代理的列表。|
| 按 OS 类型进行的分布 | 对环境中存在的 Windows 代理和 Linux 代理进行数目上的细分。|
| 按代理版本进行的分布 | 对安装在环境中的不同代理版本进行细分，并对每个版本计数。|
| 按代理类别进行的分布 | 对发送检测信号事件的不同类别的代理进行细分：直接代理、OpsMgr 代理或 OpsMgr 管理服务器。|
| 按管理组进行的分布 | 对环境中的不同 SCOM 管理组进行细分。|
| 代理的地理位置 | 对代理所在的不同国家/地区进行细分，并对安装在每个国家/地区的代理进行总计。|
| 已安装网关的计数 | 已安装 OMS 网关的服务器数，以及这些服务器的列表。|

![“代理运行状况解决方案”仪表板示例](./media/oms-solution-agenthealth/agenthealth-solution-dashboard.png)  

## <a name="log-analytics-records"></a>Log Analytics 记录
该解决方案在 OMS 存储库中创建一种类型的记录。  

### <a name="heartbeat-records"></a>检测信号记录
创建的是“Heartbeat”类型的记录。  这些记录的属性在下表中列出。  

| 属性 | 说明 |
| --- | --- |
| 类型 | Heartbeat|
| 类别 | 值为“Direct Agent”、“SCOM Agent”或“SCOM Management Server”。|
| 计算机 | 计算机名称。|
| OSType | Windows 或 Linux 操作系统。|
| OSMajorVersion | 操作系统主要版本。|
| OSMinorVersion | 操作系统次要版本。|
| 版本 | OMS 代理或 Operations Manager 代理版本。|
| SCAgentChannel | 值为“Direct”和/或“SCManagementServer”。|
| IsGatewayInstalled | 如果 OMS 网关已安装，则值为 true，否则值为 false。|
| ComputerIP | 计算机的 IP 地址。|
| RemoteIPCountry | 已部署计算机所在的地理位置。|
| ManagementGroupName | Operations Manager 管理组的名称。|
| SourceComputerId | 计算机的唯一 ID。|
| RemoteIPLongitude | 计算机的地理位置的经度。|
| RemoteIPLatitude | 计算机的地理位置的纬度。|

每个向 Operations Manager 管理服务器报告的代理都会发送两个检测信号，而 SCAgentChannel 属性的值则会包括 Direct 和 SCManagementServer，具体取决于在 OMS 订阅中启用了什么 Log Analytics 数据源和解决方案。 前面提到，解决方案的数据直接从 Operations Manager 管理服务器发送到 OMS Web 服务，或者根据在代理上收集的数据量，直接从代理发送到 OMS Web 服务。 对于值为 SCManagementServer 的检测信号事件，ComputerIP 值为管理服务器的 IP 地址，因为数据实际上是通过其上传的。  对于 SCAgentChannel 设置为 Direct 的检测信号，该值为代理的公共 IP 地址。  

## <a name="sample-log-searches"></a>示例日志搜索
下表提供了此解决方案收集的记录的示例日志搜索。

| 查询 | 说明 |
| --- | --- |
| Type=Heartbeat &#124; distinct Computer |代理总数 |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-24HOURS |过去 24 小时内无响应代理的计数 |
| Type=Heartbeat &#124; measure max(TimeGenerated) as LastCall by Computer &#124; where LastCall < NOW-15MINUTES |过去 15 分钟内无响应代理的计数 |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer IN {Type=Heartbeat TimeGenerated>NOW-24HOURS &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |（过去 24 小时内）处于联机状态的计算机数 |
| Type=Heartbeat TimeGenerated>NOW-24HOURS Computer NOT IN {Type=Heartbeat TimeGenerated>NOW-30MINUTES &#124; distinct Computer} &#124; measure max(TimeGenerated) as LastCall by Computer |（过去 24 小时中）过去 30 分钟内处于脱机状态的代理总数 |
| Type=Heartbeat &#124; measure countdistinct(Computer) by OSType |了解某个时段内按 OSType 划分的代理数的趋势|
| Type=Heartbeat&#124;measure countdistinct(Computer) by OSType |按 OS 类型进行的分布 |
| Type=Heartbeat&#124;measure countdistinct(Computer) by Version |按代理版本进行的分布 |
| Type=Heartbeat&#124;measure count() by Category |按代理类别进行的分布 |
| Type=Heartbeat&#124;measure countdistinct(Computer) by ManagementGroupName | 按管理组进行的分布 |
| Type=Heartbeat&#124;measure countdistinct(Computer) by RemoteIPCountry |代理的地理位置 |
| Type=Heartbeat IsGatewayInstalled=true&#124;Distinct Computer |已安装 OMS 网关数 |


>[!NOTE]
> 如果工作区已升级到[新 Log Analytics 查询语言](../log-analytics/log-analytics-log-search-upgrade.md)，则上述查询会更改为如下所示。
>
>| 查询 | 说明 |
|:---|:---|
| Heartbeat &#124; distinct Computer |代理总数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(24h) |过去 24 小时内无响应代理的计数 |
| Heartbeat &#124; summarize LastCall = max(TimeGenerated) by Computer &#124; where LastCall < ago(15m) |过去 15 分钟内无响应代理的计数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer in ((Heartbeat &#124; where TimeGenerated > ago(24h) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |（过去 24 小时内）处于联机状态的计算机数 |
| Heartbeat &#124; where TimeGenerated > ago(24h) and Computer !in ((Heartbeat &#124; where TimeGenerated > ago(30m) &#124; distinct Computer)) &#124; summarize LastCall = max(TimeGenerated) by Computer |（过去 24 小时中）过去 30 分钟内处于脱机状态的代理总数 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |了解某个时段内按 OSType 划分的代理数的趋势|
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by OSType |按 OS 类型进行的分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by Version |按代理版本进行的分布 |
| Heartbeat &#124; summarize AggregatedValue = count() by Category |按代理类别进行的分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by ManagementGroupName | 按管理组进行的分布 |
| Heartbeat &#124; summarize AggregatedValue = dcount(Computer) by RemoteIPCountry |代理的地理位置 |
| Heartbeat &#124; where iff(isnotnull(toint(IsGatewayInstalled)), IsGatewayInstalled == true, IsGatewayInstalled == "true") == true &#124; distinct Computer |已安装 OMS 网关数 |

## <a name="next-steps"></a>后续步骤

* 有关从 Log Analytics 生成警报的详细信息，请参阅 [Log Analytics 中的警报](../log-analytics/log-analytics-alerts.md)。

