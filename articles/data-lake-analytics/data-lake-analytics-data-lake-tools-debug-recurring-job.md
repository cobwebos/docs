---
title: "如何对异常的定期作业进行故障排除 | Microsoft Docs"
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
ms.openlocfilehash: a358f94b117c12511028a875e56b5c9dba8d3382
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-troubleshoot-an-abnormal-recurring-job"></a>如何对异常的定期作业进行故障排除

本文档介绍如何使用[针对 Visual Studio 的 Azure Data Lake 工具](http://aka.ms/adltoolsvs)对定期作业问题进行故障排除。 从[此处](https://blogs.msdn.microsoft.com/azuredatalake/2017/09/19/managing-pipeline-recurring-jobs-in-azure-data-lake-analytics-made-easy/)深入了解管道和定期作业。
定期作业通常会共享相同的查询逻辑和类似的输入数据。 例如，你有一个定期作业，该作业在每个星期一上午 8 点运行， 用于计算过去一周的每周活动用户，这些作业的脚本共享一个包含查询逻辑的脚本模板，并且这些作业的输入均为过去一周的使用情况数据。 共享相同的查询逻辑和类似的输入通常意味着这些作业的性能类似且稳定，如果一个定期作业的执行突然发生异常、失败或减慢了很多，则可能需要：

1.  查看定期作业的预览运行的统计信息报告，了解所发生的情况。
2.  对异常作业和正常作业进行比较，找出已更改的内容。

针对 Visual Studio 的 Azure Data Lake 工具中的“相关作业视图”可帮助加快这两个案例的故障排除进度。

## <a name="step-1-find-recurring-jobs-and-open-related-job-view"></a>步骤 1：查找定期作业，并打开相关作业视图

要使用相关作业视图对定期作业问题进行故障排除，需先在 Visual Studio 中找到定期作业，然后打开相关作业视图。

### <a name="case-1-you-have-the-url-for-the-recurring-job"></a>案例 1：拥有定期作业的 URL

通过“工具”>“Data Lake”>“作业视图”，可以粘贴作业 URL，从而在 Visual Studio 中打开作业视图，并通过“查看相关作业”打开相关作业视图。

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/view-related-job.png)
 
### <a name="case-2-you-have-the-pipeline-for-the-recurring-job-but-not-the-url"></a>案例 2：拥有定期作业的管道，但没有 URL

在 Visual Studio 中，通过“服务器资源管理器”>“Data Lake Analytics 帐户”>“管道”打开管道浏览器（如果未在服务器资源管理器中找到此节点，请通过[此处](http://aka.ms/adltoolsvs)获取上述工具）。 在管道浏览器中，ADLA 帐户的所有管道均列在左侧，你可以展开管道查找所有定期作业，单击有问题的作业，右侧随即打开相关作业视图。

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/pipeline-browser.png)

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-view.png)

## <a name="step-2-analyze-statistics-report"></a>步骤 2：分析统计信息报告

摘要和统计信息报告均显示在相关作业视图顶部，你可以在其中获取异常的潜在根本原因。 

1.  首先需要在报告中查找该异常作业。 X 轴显示作业提交时间，可通过该轴定位异常作业。
2.  请执行以下过程，查看统计信息并获取对异常和可能的解决方案的见解。

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-metrics-debugging-flow.png)

## <a name="step-3-compare-the-abnormal-recurring-job-to-a-normal-job"></a>步骤 3：对异常定期作业和正常作业进行比较

通过相关作业视图底部的作业列表，可以查找所有已提交的定期作业。 通过右键单击，可以对异常作业和上一正常作业进行比较，从而在作业差异视图中查找更多见解和可能的解决方案。

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/compare-job.png)

通常需要注意这两个作业之间的较大差异，因为这些差异可能是导致性能问题的原因，你也可以按照以下步骤进行进一步检查。

![Data Lake Analytics 工具查看相关作业](./media/data-lake-analytics-data-lake-tools-debug-recurring-job/recurring-job-diff-debugging-flow.png)

## <a name="next-steps"></a>后续步骤

* [如何调试和解决数据倾斜问题](data-lake-analytics-data-lake-tools-data-skew-solutions.md)
* [如何调试失败 U-SQL 作业的用户定义代码错误](data-lake-analytics-debug-u-sql-jobs.md)