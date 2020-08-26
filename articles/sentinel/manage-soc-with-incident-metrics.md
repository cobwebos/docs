---
title: 利用 Azure Sentinel 中的事件指标更好地管理 SOC |Microsoft Docs
description: 使用 Azure Sentinel 事件指标屏幕和工作簿中的信息来帮助你管理安全运营中心 (SOC) 。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2020
ms.author: yelevin
ms.openlocfilehash: 9d8d0fc46a463bda31595988d807854ef146d333
ms.sourcegitcommit: f1b18ade73082f12fa8f62f913255a7d3a7e42d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/24/2020
ms.locfileid: "88761708"
---
# <a name="manage-your-soc-better-with-incident-metrics"></a>利用事件指标更好地管理 SOC

> [!IMPORTANT]
> 事件指标功能当前提供公共预览版。
> 提供这些功能没有服务级别协议，不建议用于生产工作负荷。
> 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

作为安全运营中心 (SOC) manager，你需要获得整体效率指标和措施，以衡量你的团队的业绩。 你需要按多个不同的条件（如严重性、MITRE 战术、平均时间到会审、平均解决时间等）来查看一段时间内的事件操作。 Azure Sentinel 现在使此数据可供你使用 Log Analytics 中的新的 **SecurityIncident** 表和架构以及附带的 **安全操作效率** 工作簿。 随着时间的推移，你将能够直观显示团队的表现，并使用这种见解提高效率。 你还可以针对事件表编写和使用你自己的 KQL 查询，以创建符合特定审核需求和 Kpi 的自定义工作簿。

## <a name="use-the-security-incidents-table"></a>使用安全事件表

**SecurityIncident**表内置于 Azure Sentinel 中。 在 "**日志**" 下，你会发现**SecurityInsights**集合中的其他表。 可以像在 Log Analytics 中的任何其他表一样对其进行查询。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incident-table.png" alt-text="安全事件表":::

每次创建或更新事件时，都会向表中添加一个新的日志条目。 这使你可以跟踪对事件所做的更改，并允许更强大的 SOC 指标，但你需要注意这一点，因为你可能需要删除事件的重复条目 (依赖于你) 运行的确切查询。 

例如，如果想要返回按事件编号排序的所有事件的列表，但只想返回每个事件的最新日志，则可以使用 KQL [汇总运算符](https://docs.microsoft.com/azure/data-explorer/kusto/query/summarizeoperator) 和 `arg_max()` [聚合函数](https://docs.microsoft.com/azure/data-explorer/kusto/query/arg-max-aggfunction)来执行此操作：


```Kusto
SecurityIncident
| summarize arg_max(LastModifiedTime, *) by IncidentNumber
```
### <a name="more-sample-queries"></a>更多示例查询

平均关闭时间：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToClosure =  (ClosedTime - CreatedTime)/1h
| summarize 5th_Percentile=percentile(TimeToClosure, 5),50th_Percentile=percentile(TimeToClosure, 50), 
  90th_Percentile=percentile(TimeToClosure, 90),99th_Percentile=percentile(TimeToClosure, 99)
```

会审的平均时间：
```Kusto
SecurityIncident
| summarize arg_max(TimeGenerated,*) by IncidentNumber 
| extend TimeToTriage =  (FirstModifiedTime - CreatedTime)/1h
| summarize 5th_Percentile=max_of(percentile(TimeToTriage, 5),0),50th_Percentile=percentile(TimeToTriage, 50), 
  90th_Percentile=percentile(TimeToTriage, 90),99th_Percentile=percentile(TimeToTriage, 99) 
```

## <a name="security-operations-efficiency-workbook"></a>安全操作效率工作簿

为了补充 **SecurityIncidents** 表，我们提供了一个现成的 **安全操作效率** 工作簿模板，可用于监视 SOC 操作。 工作簿包含以下指标： 
- 事件创建时间 
- 通过关闭分类、严重性、所有者和状态创建的事件 
- 会审的平均时间 
- 平均关闭时间 
- 按时间严重性、所有者、状态、产品和策略创建的事件 
- 会审百分位数时间 
- 结束百分位数时间 
- 每个所有者的平均会审时间 
- 最近的活动 
- 最近的结束分类  

可以通过从 Azure Sentinel 导航菜单中选择 **工作** 簿并选择 " **模板** " 选项卡来查找此新的工作簿模板。从库中选择 " **安全操作效率** "，然后单击 " **查看保存的工作簿** " 和 " **查看模板** " 按钮之一。

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-incidents-workbooks-gallery.png" alt-text="安全事件工作簿库":::

:::image type="content" source="./media/manage-soc-with-incident-metrics/security-operations-workbook-1.png" alt-text="安全事件工作簿完成":::

您可以使用模板创建您自己的自定义工作簿，以满足您的特定需要。

## <a name="securityincidents-schema"></a>SecurityIncidents 架构

[!INCLUDE [SecurityIncidents schema](../../includes/sentinel-schema-security-incident.md)]

## <a name="next-steps"></a>后续步骤

- 若要开始使用 Azure Sentinel，需要订阅 Microsoft Azure。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/free/)。
- 了解如何[将数据载入到 Azure Sentinel](quickstart-onboard.md)，以及[获取数据和潜在威胁的见解](quickstart-get-visibility.md)。
