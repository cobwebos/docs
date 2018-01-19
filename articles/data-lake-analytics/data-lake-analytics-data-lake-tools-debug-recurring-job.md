---
title: "对异常的定期作业进行疑难解答 | Microsoft Docs"
description: "了解如何使用针对 Visual Studio 的 Azure Data Lake 工具调试异常的定期作业。"
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: dc9b21d8-c5f4-4f77-bcbc-eff458f48de2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2017
ms.author: yanacai
ms.openlocfilehash: 9b60c861810d6577b33aa0cdf14f26dc2cfc0e4d
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2018
---
# <a name="troubleshoot-an-abnormal-recurring-job"></a>对异常的定期作业进行疑难解答

本文展示了如何使用[针对 Visual Studio 的 Azure Data Lake 工具](http://aka.ms/adltoolsvs)对定期作业的问题进行疑难解答。 从 [Azure Data Lake 和 Azure HDInsight 博客](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)中详细了解管道和定期作业。

定期作业通常共享相同的查询逻辑和类似的输入数据。 例如，假定你有一个定期作业，该作业在每个星期一上午 8 点运行， 用于计算上周的每周活动用户。 这些作业的脚本共享一个包含查询逻辑的脚本模板。 这些作业的输入是上周的使用情况数据。 共享相同的查询逻辑和类似的输入通常意味着这些作业的性能类似且稳定。 如果你的定期作业之一突然性能异常、失败或执行速度大幅降低，则你可能希望：

- 查看定期作业的以前运行的统计信息报告来了解发生了什么。
- 对异常作业和正常作业进行比较，找出已更改的内容。

针对 Visual Studio 的 Azure Data Lake 工具中的“相关作业视图”可帮助加快这两个案例的故障排除进度。

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>步骤 1：查找定期作业，并打开相关作业视图

要使用相关作业视图对定期作业问题进行故障排除，需先在 Visual Studio 中找到定期作业，然后打开相关作业视图。

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>案例 1：拥有定期作业的 URL

通过“工具” > “Data Lake” > “作业视图”，可以在 Visual Studio 中粘贴作业 URL 以打开作业视图。 选择“查看相关作业”以打开相关作业视图。

![Data Lake Analytics 工具中的“查看相关作业”链接](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>案例 2：拥有定期作业的管道，但没有 URL

在 Visual Studio 中，可以通过“服务器资源管理器”> 你的 Azure Data Lake Analytics 帐户 >“管道”打开管道浏览器。 （如果在服务器资源管理器中没有发现此节点，请[下载最新插件](http://aka.ms/adltoolsvs)。） 

![选择“管道”节点](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

在管道浏览器中，Data Lake Analytics 帐户的所有管道都在左侧列出。 可以展开管道以找到所有定期作业，然后选择有问题的作业。 相关作业视图将在右侧打开。

![选择一个管道并打开相关作业视图](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-a-statistics-report"></a>步骤 2：分析统计信息报告

相关作业视图的顶部会显示摘要和统计信息报告。 在那里，可以找到问题的潜在根源。 

1.  在报告中，X 轴显示作业提交时间。 使用它查找异常作业。
2.  使用下图中的流程检查统计信息，了解问题和可能的解决方案。

![用于检查统计信息的流程图](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-job-to-a-normal-job"></a>步骤 3：将异常作业与正常作业进行比较

通过相关作业视图底部的作业列表，可以找到所有已提交的定期作业。 若要找到更多见解和潜在的解决方案，请右键单击异常作业。 使用“作业差异”视图将异常作业与前面的正常作业进行比较。

![用于比较作业的快捷菜单](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

请注意这两个作业之间的较大差异。 可能是这些差异导致了性能问题。 若要进一步进行检查，请使用下图中的步骤：

![用于检查作业之间的差异的流程图](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>后续步骤

* [解决数据倾斜问题](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [调试失败 U-SQL 作业的用户定义 C# 代码](data-lake-analytics-debug-u-sql-jobs.md)