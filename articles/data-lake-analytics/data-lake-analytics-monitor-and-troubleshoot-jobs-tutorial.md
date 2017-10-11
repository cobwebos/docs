---
title: "使用 Azure 门户对 Azure Data Lake Analytics 作业进行故障排除 | Microsoft Docs"
description: "了解如何使用 Azure 门户对 Data Lake Analytics 作业进行疑难解答。 "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
ms.openlocfilehash: b9c7453cc0a94f70d0098ed83e5f127832065a62
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>使用 Azure 门户对 Azure Data Lake Analytics 作业进行疑难解答
了解如何使用 Azure 门户对 Data Lake Analytics 作业进行疑难解答。

在本教程中，将遇到缺少源文件问题，并使用 Azure 门户解决该问题。

## <a name="submit-a-data-lake-analytics-job"></a>提交 Data Lake Analytics 作业

提交以下 U-SQL 作业：

```
@searchlog =
   EXTRACT UserId          int,
           Start           DateTime,
           Region          string,
           Query           string,
           Duration        int?,
           Urls            string,
           ClickedUrls     string
   FROM "/Samples/Data/SearchLog.tsv1"
   USING Extractors.Tsv();

OUTPUT @searchlog   
   TO "/output/SearchLog-from-adls.csv"
   USING Outputters.Csv();
```
    
此脚本中定义的源文件是 **/Samples/Data/SearchLog.tsv1**，它应在 **/Samples/Data/SearchLog.tsv** 中。


## <a name="troubleshoot-the-job"></a>解决作业问题

**查看所有作业**

1. 在Azure 门户中，单击左上角的 “Microsoft Azure”。
2. 单击显示 Data Lake Analytics 帐户名的磁贴。  作业摘要显示在“作业管理”磁贴上。

    ![Azure Data Lake Analytics 作业管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    作业管理提供作业状态概览。 请注意没有失败的作业。
3. 单击“作业管理”磁贴，以查看作业。 在“正在运行”、“已排队”和“已结束”部分对作业进行了分类。 可在“已结束”部分查看失败的作业。 它是列表中的第一个。 如果你有大量的作业，你可以单击**筛选器**来帮助你查找作业。

    ![Azure Data Lake Analytics 筛选作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. 从列表中单击失败的作业，在新的边栏选项卡上打开作业详细信息：

    ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    请注意“重新提交”按钮。 解决问题后，可以重新提交作业。
5. 从之前的屏幕快照中单击突出显示的部分，打开错误的详细信息。  会看到类似如下内容：

    ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    它告诉你找不到源文件夹。
6. 单击“重复脚本”。
7. 将 **FROM** 路径更新到到以下：

    "/Samples/Data/SearchLog.tsv"
8. 单击“提交”。

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Visual Studio 实现 Azure Data Lake Analytics U-SQL 入门](data-lake-analytics-u-sql-get-started.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
