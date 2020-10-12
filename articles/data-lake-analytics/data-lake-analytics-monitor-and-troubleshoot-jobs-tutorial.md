---
title: 监视 Azure Data Lake Analytics-Azure 门户
description: 本文介绍如何使用 Azure 门户对 Azure Data Lake Analytics 作业进行故障排除。
ms.service: data-lake-analytics
ms.reviewer: jasonh
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.topic: troubleshooting
ms.date: 12/05/2016
ms.openlocfilehash: 5445ad61295be3637005ead734cc31126c88a440
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87132069"
---
# <a name="monitor-jobs-in-azure-data-lake-analytics-using-the-azure-portal"></a>使用 Azure 门户监视 Azure Data Lake Analytics 中的作业

## <a name="to-see-all-the-jobs"></a>查看所有作业

1. 在Azure 门户中，单击左上角的 “Microsoft Azure”****。

2. 单击显示 Data Lake Analytics 帐户名的磁贴。  作业摘要显示在“作业管理”**** 磁贴上。

   ![Azure Data Lake Analytics 作业管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    作业管理提供作业状态概览。 请注意没有失败的作业。
3. 单击“作业管理”**** 磁贴，以查看作业。 在“正在运行”****、“已排队”**** 和“已结束”**** 部分对作业进行了分类。 可在“已结束”**** 部分查看失败的作业。 它是列表中的第一个。 有大量作业时，可以单击“筛选”**** 来帮助找到作业。

   ![Azure Data Lake Analytics 筛选作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)

4. 从列表中单击失败的作业，以打开作业详细信息：

   ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    请注意“重新提交”**** 按钮。 解决问题后，可以重新提交作业。

5. 从之前的屏幕快照中单击突出显示的部分，打开错误的详细信息。  会看到类似如下内容：

   ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

   它会告诉你找不到源文件夹。

6. 单击“重复脚本”****。

7. 将 **从** 路径更新为： `/Samples/Data/SearchLog.tsv`

8. 单击“提交”****。

## <a name="next-steps"></a>后续步骤

* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
