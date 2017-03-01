---
title: "使用 Azure 门户对 Azure Data Lake Analytics 作业进行故障排除 | Microsoft Docs"
description: "了解如何使用 Azure 门户对 Data Lake Analytics 作业进行疑难解答。 "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: b7066d81-3142-474f-8a34-32b0b39656dc
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 194b5d79505afbfd0208f63dd182a0e03227ba69
ms.openlocfilehash: 1915201480f57c5f132e13bb5ce4283fbea975fe


---
# <a name="troubleshoot-azure-data-lake-analytics-jobs-using-azure-portal"></a>使用 Azure 门户对 Azure Data Lake Analytics 作业进行疑难解答
了解如何使用 Azure 门户对 Data Lake Analytics 作业进行疑难解答。

在本教程中，将遇到缺少源文件问题，并使用 Azure 门户解决该问题。

**先决条件**

在开始阅读本教程前，你必须具有：

* **Data Lake Analytics 作业处理的基本知识**。 请参阅 [通过 Azure 门户实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)。
* **Data Lake Analytics 帐户**。 请参阅 [通过 Azure 门户实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md#create-data-lake-analytics-account)。
* **将示例数据复制到默认的 Data Lake Store 帐户**。  请参阅 [Prepare source data](data-lake-analytics-get-started-portal.md#prepare-source-data)（准备源数据）

## <a name="submit-a-data-lake-analytics-job"></a>提交 Data Lake Analytics 作业
现在将创建具有错误源文件名的 U-SQL 作业。  

**提交作业**

1. 在 **Azure 门户** 中，单击左上角的“Microsoft Azure”。
2. 单击显示 Data Lake Analytics 帐户名的磁贴。  该帐户在创建后即已固定在此处。
   如果该帐户未固定在此处，请参阅 [Open an Analytics account from portall](data-lake-analytics-manage-use-portal.md#access-adla-account)（从门户打开 Analytics 帐户）。
3. 在顶部菜单中单击“新建作业”。
4. 输入“作业名称”，以下 U-SQL 脚本：

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

    此脚本中定义的源文件是 **/Samples/Data/SearchLog.tsv1**，它应在 **/Samples/Data/SearchLog.tsv** 中。
5. 单击顶部的“提交作业”。 将打开新的“作业详细信息”窗格。 标题栏显示了作业状态。 需要几分钟才能完成。 可以单击“刷新”以查看最新的状态。
6. 请等待，直到作业状态变为“失败”。  如果作业显示“成功”，那是因为未删除 /Samples 文件夹。 请参见本教程开始处的**先决条件**部分。

你可能想知道 - 为什么小型作业需要花这么长的时间。  请记住 Data Lake Analytics 旨在处理大数据。  使用其分布式系统处理大数据时，它非常出色。

假定你已提交了作业并关闭了门户。  在下一部分，将学习如何解决作业问题。

## <a name="troubleshoot-the-job"></a>解决作业问题
在上一部分，你已提交了作业，而作业失败了。  

**查看所有作业**

1. 在Azure 门户中，单击左上角的 “Microsoft Azure”。
2. 单击显示 Data Lake Analytics 帐户名的磁贴。  作业摘要显示在“作业管理”磁贴上。

    ![Azure Data Lake Analytics 作业管理](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-job-management.png)

    作业管理为你提供作业状态概览。 请注意没有失败的作业。
3. 单击“作业管理”磁贴，以查看作业。 在“正在运行”、“已排队”和“已结束”部分对作业进行了分类。 可在“已结束”部分查看失败的作业。 它是列表中的第一个。 有大量的作业时，可以单击“筛选”来帮助你找到作业。

    ![Azure Data Lake Analytics 筛选作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-filter-jobs.png)
4. 从列表中单击失败的作业，在新的边栏选项卡上打开作业详细信息：

    ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job.png)

    请注意“重新提交”按钮。 解决问题后，可以重新提交作业。
5. 从之前的屏幕快照中单击突出显示的部分，打开错误的详细信息。  你会看到类似如下内容：

    ![Azure Data Lake Analytics 失败的作业](./media/data-lake-analytics-monitor-and-troubleshoot-tutorial/data-lake-analytics-failed-job-details.png)

    它会告诉你找不到源文件夹。
6. 单击“重复脚本”。
7. 将 **FROM** 路径更新到到以下：

    "/Samples/Data/SearchLog.tsv"
8. 单击“提交”。

## <a name="see-also"></a>另请参阅
* [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)
* [通过使用 Azure PowerShell 实现 Azure Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
* [通过 Visual Studio 实现 Azure Data Lake Analytics U-SQL 入门](data-lake-analytics-u-sql-get-started.md)
* [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)



<!--HONumber=Dec16_HO2-->


