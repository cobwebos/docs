---
title: "将 Azure Log Analytics 升级到新的日志搜索 | Microsoft Docs"
description: "新的 Log Analytics 查询语言即将发布，用户可以参与公共预览版。  本文介绍新语言的优势，以及如何转换工作区。"
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 4a3ee3c4d1fa9b626a51f24997603adceed8311f
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2017
---
# <a name="upgrade-your-azure-log-analytics-workspace-to-new-log-search"></a>将 Azure Log Analytics 工作区升级到新的日志搜索

> [!NOTE]
> 可选择也可不选择升级到新的 Log Analytics 查询语言，这样用户便有时间[提升新语言方面的技能](https://go.microsoft.com/fwlink/?linkid=856078)。  

新的 Log Analytics 查询语言已发布，用户需升级工作区才能使用。  本文介绍新语言的优势，以及如何转换工作区。  如果不选择立即升级，则工作区会继续按照之前的方式运行，但会在稍后某天自动转换。  设置该日期时，会收到重要时间和通知。

本文提供有关新的语言和升级过程的详细信息。

## <a name="why-the-new-language"></a>为什么使用新语言？
我们了解任何转换都涉及成本和精力，而我们更改查询语言也绝非只是因为新语言更好用。  更改语言可为 Log Analytics 客户带来巨大的价值，原因如下。

- **简单亦强大。** 相比旧语言，新语言更易于理解，类似于 SQL，其结构更接近自然语言。
- **完整的管道语言。**  相比旧语言，新语言的管道功能更丰富。  几乎任何输出都可以通过管道传递给另一个命令，创建比以前更复杂的查询。
- **搜索 - 时间字段提取。**  相比旧语言，新语言支持更高级的运行时计算字段。  可将复杂计算用于扩展的字段，然后将计算字段用于更多命令（包括联接和聚合）。
- **高级联接。**  相比旧语言，新语言提供更高级的联接功能，包括根据多个字段联接表、使用内部和外部联接以及按扩展字段联接。
- **日期/时间函数。**  相比旧语言，新语言具有更高级的日期/时间函数。
- **智能分析。**  新语言具有高级算法，用于计算数据集中的模式并比较不同的数据集。
- **高级分析门户。**  高级分析门户提供 Log Analytics 门户中不提供的分析功能，包括多行查询编辑、更多可视化效果和高级诊断。
- **与其他应用程序一致。**  新语言和高级分析门户已用于 Application Insights 中的分析。  为 Log Analytics 实现新语言可提供 Azure 服务间的一致性。
- **使用 Power BI 实现更好的集成。** 可以将新语言中的查询导出到 Power BI Desktop，以便使用其丰富的数据转换功能。
- **更多其他原因。** 有关新语言的完整详细信息和教程，请参阅 [Azure Log Analytics 查询语言](https://docs.loganalytics.io)网站。


## <a name="when-can-i-upgrade"></a>何时可以升级？
将陆续在所有 Azure 区域中推出升级，所以某些区域可能会先于其他区域推出升级。  当工作区顶部出现邀请升级的紫色横幅时，就说明工作区可进行升级了。

![升级 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

## <a name="what-happens-when-i-upgrade"></a>升级时会发生什么情况？
转换工作区时，保存的任何搜索、警报规则和使用视图设计器创建的视图会自动转换到新语言。  解决方案中包含的搜索不会自动转换，但会在用户打开它们时动态转换。  这对用户是完全透明的。

## <a name="can-i-go-back-after-i-upgrade"></a>升级后是否可复原？
升级时，会获得工作区的完整备份，包括所有已保存的搜索、警报规则和视图的快照。  这样便可在稍后需要时还原至旧的工作区。

若要恢复旧工作区，请转到工作区中的“设置”，然后“升级摘要”。  然后可以选择“还原旧工作区”这一选项。  

![还原旧的](media/log-analytics-log-search-upgrade/restore-legacy-b.png)

## <a name="how-do-i-perform-the-upgrade"></a>如何执行升级？
在门户顶部看到紫色横幅时，可以升级工作区。  

1.  可通过单击显示“了解详细信息并执行升级”的紫色横幅，开始升级过程。<br>![升级 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>
2.  在升级信息页面上阅读有关升级的其他信息。<br>![升级 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>
3.  单击“立即升级”即可开始升级。<br>![升级 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>右上角有一个通知框会显示状态。<br>![升级 5](media/log-analytics-log-search-upgrade/upgrade-05.png)
4.  就这么简单！  转到“日志搜索”页，查看已升级的工作区。<br>![升级 6](media/log-analytics-log-search-upgrade/upgrade-06.png)<br>

如果遇到导致升级失败的问题，则可以转到[论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)并从 Azure 门户发布问题或[创建支持请求](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="how-do-i-learn-the-new-language"></a>如何学习新的语言？
因为多个服务都使用新语言，所以我们创建了一个[用于承载文档的外部站点](https://docs.loganalytics.io/)，专用于承载与新语言相关的文档。  其中包括教程、示例和完整参考，可帮助用户加快升级进度。 可通过[查询入门](https://go.microsoft.com/fwlink/?linkid=856078)学习有关新语言的分步教程，并通过 [Log Analytics 查询语言](https://go.microsoft.com/fwlink/?linkid=856079)访问语言参考。  

如果已熟悉旧的 Log Analytics 查询语言，可使用升级过程中添加到工作区的语言转换器。

只需键入旧查询，然后单击“转换”即可得到转换后的版本。  然后可以单击“搜索”按钮运行搜索，或复制并粘贴转换后的查询，便于在警报规则等其他所需之处使用。

![语言转换器](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>后续步骤
- 查看[有关新语言的教程](https://go.microsoft.com/fwlink/?linkid=856078)。
- 学习[有关使用日志搜索门户的教程](log-analytics-log-search-log-search-portal.md)（使用新查询语言）。
- 获取对新的[高级分析门户](https://go.microsoft.com/fwlink/?linkid=856587)的简介。
