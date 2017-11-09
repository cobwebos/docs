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
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 1806b70ba0d34f49abfb954abebff8d29ae61291
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/16/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>将 Azure Log Analytics 升级到新的日志搜索

新的 Log Analytics 查询语言已发布，用户需升级工作区才能使用。  可以自行升级工作区，或者等待它在推出期间（从 10 月底开始到年底结束）自动升级。  本文介绍新语言的优势，以及如何转换工作区。  

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
将陆续在所有 Azure 区域中推出升级，所以某些区域可能会先于其他区域推出升级。  当工作区顶部出现邀请你进行升级的横幅时，就说明工作区可进行升级了。

![升级 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

如果工作区自动升级，你将看到一个指出工作区已升级的横幅，用一个摘要指示是否遇到了任何问题。

 ![自动升级](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>升级后会发生什么情况？
转换工作区时，将对它进行以下更改：

- 保存的任何搜索、警报规则和使用视图设计器创建的视图会自动转换为新语言。  解决方案中包含的搜索不会自动转换，但会在用户打开它们时动态转换。  
- 将摒弃[我的仪表板](log-analytics-dashboards.md)以支持[视图设计器](log-analytics-view-designer.md)和 [Azure 仪表板](https://docs.microsoft.com/en-us/azure/azure-portal/azure-portal-dashboards.md)。  添加到“我的仪表板”的磁贴仍然可用，但它们为只读模式。
- [Power BI 集成](log-analytics-powerbi.md)将被替换为一个新进程。  你创建的任何现有 Power BI 计划都将被禁用，并且需要将它们替换为新的进程。
- 使用 webhook 和 runbook 从[警报操作](log-analytics-alerts-actions.md)发出的响应采用新的格式，你可能需要相应地更新你的警报规则。
- 有关升级的常见问题，请参阅[日志搜索常见问题解答](log-analytics-log-search-faq.md)。

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>如何知道是否出现了任何升级问题？
在升级完成后，在工作区的“设置”中将会有一个“升级摘要”部分。  查看此部分以了解有关升级的信息并查看

 ![升级摘要](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>如何手动执行升级？
在门户顶部看到横幅时，可以升级工作区。  

1.  可通过单击显示“了解详细信息并执行升级”的横幅，开始升级过程。

    ![升级 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  在升级信息页面上阅读有关升级的其他信息。

    ![升级 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  单击“立即升级”即可开始升级。

    ![升级 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>右上角有一个通知框会显示状态。
    
    ![升级 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  就这么简单！  转到“日志搜索”页，查看已升级的工作区。

    ![升级 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

如果遇到导致升级失败的问题，则可以转到[论坛](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights)并从 Azure 门户发布问题或[创建支持请求](../azure-supportability/how-to-create-azure-support-request.md)。

## <a name="how-do-i-learn-the-new-language"></a>如何学习新的语言？
因为多个服务都使用新语言，所以我们创建了一个[用于承载文档的外部站点](https://docs.loganalytics.io/)，专用于承载与新语言相关的文档。  其中包括教程、示例和完整参考，可帮助用户加快升级进度。 可通过[查询入门](https://go.microsoft.com/fwlink/?linkid=856078)学习有关新语言的分步教程，并通过 [Log Analytics 查询语言](https://go.microsoft.com/fwlink/?linkid=856079)访问语言参考。  

如果想要在包括一组示例数据的演示环境中尝试使用新语言，请参阅[演练环境](https://portal.loganalytics.io/demo#/discover/home)。

如果已熟悉旧的 Log Analytics 查询语言，可使用升级过程中添加到工作区的语言转换器。  只需键入旧查询，然后单击“转换”即可得到转换后的版本。  然后可以单击“搜索”按钮运行搜索，或复制并粘贴转换后的查询，便于在警报规则等其他所需之处使用。  也可以查看我们的[备忘单](log-analytics-log-search-transition.md)，它直接比较使用旧语言的常见查询。

![语言转换器](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>后续步骤
- 查看[有关新语言的教程](https://go.microsoft.com/fwlink/?linkid=856078)。
- 学习[有关使用日志搜索门户的教程](log-analytics-log-search-log-search-portal.md)（使用新查询语言）。
- 获取对新的[高级分析门户](https://go.microsoft.com/fwlink/?linkid=856587)的简介。
