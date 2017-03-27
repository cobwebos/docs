---
title: "在用于 Visual Studio 的 Data Lake 工具中使用顶点执行视图 | Microsoft Docs"
description: "了解如何使用顶点执行视图测试 Data Lake Analytics 作业。"
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 5366d852-e7d6-44cf-a88c-e9f52f15f7df
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/13/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: b2acf4fd95c1611ecd580b508339d1305010fdd8


---
# <a name="use-the-vertex-execution-view-in-data-lake-tools-for-visual-studio"></a>在用于 Visual Studio 的 Data Lake 工具中使用顶点执行视图
了解如何使用顶点执行视图测试 Data Lake Analytics 作业。

## <a name="prerequisites"></a>先决条件
* 基本了解如何使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本。  请参阅[教程：使用用于 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。

## <a name="open-the-vertex-execution-view"></a>打开顶点执行视图
对于特定作业，可在左下角单击“顶点执行视图”链接。 系统可能提示先加载配置文件，此操作可能需要一些时间，具体取决于网络连接状况。

![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-open-vertex-execution-view.png)

## <a name="understand-vertex-execution-view"></a>了解顶点执行视图
进入顶点执行视图后，可看到三个部分：

![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view.png)

* 顶点选择器：左侧是顶点选择器。  可按功能（例如：前 10 个数据读取，或按阶段选择）选择顶点。
  
    其中一个最常用筛选器是关键路径上的顶点。 关键路径是 U-SQL 作业的最长路径。 它可用于通过检查哪个顶点耗时最长来优化作业。
* 顶部中心窗格：
  
    ![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane2.png)
  
    此视图还可显示所有顶点的运行状态。 此外，可相应地转换本地计算机时间，并以不同颜色显示不同状态。
* 底部中心窗格：
  
    ![Data Lake Analytics 工具顶点执行视图](./media/data-lake-analytics-data-lake-tools-use-vertex-execution-view/data-lake-tools-vertex-execution-view-pane3.png)
  
  * 进程名称：顶点实例的名称。 它由 StageName|VertexName|VertexRunInstance 中的不同部分构成。 例如，SV7_Split[62].v1 顶点表示阶段 SV7_Split 中顶点数 62 的第二个运行实例（.v1，从 0 开始索引）。
  * 读取/写入的总数据：此顶点读取/写入的数据。
  * 状态/退出状态：顶点结束时的最终状态。
  * 退出代码/失败类型：顶点失败时出现的错误。
  * 创建原因：为何创建顶点。
  * 资源延迟/进程延迟/PN 队列延迟：顶点等待资源、处理数据和在队列中等待所需的时间。
  * 进程/创建者 GUID：当前运行顶点或其创建者的 GUID。
  * 版本：运行中顶点的第 N 个实例（系统可能因故障转移、计算冗余等多种原因安排顶点的新实例）
  * 版本创建时间。
  * 进程创建开始时间/进程排队时间/进程开始时间/进程完成时间：顶点进程开始创建的时间；顶点进程开始排队的时间；特定顶点进程开始时间；特定顶点完成时间。

## <a name="next-steps"></a>后续步骤
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 若要记录诊断信息，请参阅 [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要查看作业详细信息，请参阅[对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图](data-lake-analytics-data-lake-tools-view-jobs.md)
* 若要了解用于 Visual Studio 的 Data Lake 工具代码，请参阅[使用用于 Visual Studio 的 Data Lake 工具代码](data-lake-analytics-data-lake-tools-for-vscode.md)。



<!--HONumber=Nov16_HO4-->


