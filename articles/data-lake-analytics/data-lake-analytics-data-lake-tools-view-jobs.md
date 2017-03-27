---
title: "对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图 | Microsoft Docs"
description: "了解如何对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图。 "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: bdf27b4d-6f58-4093-ab83-4fa3a99b5650
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/17/2016
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ac5a64b759376c06e058ae015b73f1b73b7d1e7b
ms.openlocfilehash: 8f3c0f7a1b16afdbada39017d28bed49620cc34c


---
# <a name="use-job-browser-and-job-view-for-azure-data-lake-analytics-jobs"></a>对 Azure Data Lake Analytics 作业使用作业浏览器和作业视图
Azure Data Lake Analytics 服务将已提交作业存档在[查询存储](#query-store)中。 本文介绍如何在用于 Visual Studio 的 Azure Data Lake 工具中使用作业浏览器和作业视图查找历史作业消息。 

默认情况下，Data Lake Analytics 服务将作业存档 30 天。 通过配置自定义过期策略，可在 Azure 门户中配置有效期。 过期后，无法访问作业信息。 

## <a name="prerequisites"></a>先决条件
请参阅[用于 Visual Studio 的 Data Lake 工具先决条件](data-lake-analytics-data-lake-tools-get-started.md#prerequisites)。

## <a name="open-the-job-browser"></a>打开作业浏览器
在 Visual Studio，通过“服务器资源管理器”>“Azure”>“Data Lake Analytics”>“作业”可访问作业浏览器。  使用此浏览器，可访问 Data Lake Analytics 帐户的查询存储。 作业浏览器左侧显示查询存储，其中显示作业基本信息；右侧的作业视图提供有关作业的详细信息。

## <a name="job-view"></a>作业视图
作业视图显示作业详细信息。 若要打开作业，可在作业浏览器中双击作业，或单击作业视图从 Data Lake 菜单打开。 随即可看到填充有作业 URL 的对话框。

![Data Lake 工具 Visual Studio 作业浏览器](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view.png)

作业视图包含：

* 作业摘要
  
    刷新作业视图可查看运行中作业的最新信息。
  
  * 作业状态（图表）：
    
      作业状态会概述作业阶段：
    
      ![Azure Data Lake Analytics 作业阶段状态](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-phases.png)
    
    * 正在准备：将脚本上传到云，使用编译服务编译和优化脚本。
    * 已排队：作业排队等待足够资源，或作业超出每个帐户的最大并发作业数限制。 优先级设置决定排队作业的顺序 - 编号越低，优先级越高。
    * 正在运行：作业正在 Data Lake Analytics 帐户中运行。
    * 正在完成：作业正在完成（例如，正在完成文件）。
      
      作业在任何阶段都可能失败。 例如，“正在准备”阶段的编译错误、“已排队”阶段的超时错误，以及“正在运行”阶段的执行错误等。
  * 基本信息
    
      “作业摘要”窗格的下半部分会显示基本作业信息。
    
      ![Azure Data Lake Analytics 作业阶段状态](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-info.png)
    
    * 作业结果：成功或失败。 作业在任何阶段都可能失败。
    * 总持续时间：提交时间和结束时间之间的时钟时间（持续时间）。
    * 总计算时间：每个顶点执行时间的总和，可将其视作仅在一个顶点中执行作业的时间。 有关顶点的详细信息，请参阅“顶点总数”。
    * 提交/开始/结束时间：Data Lake Analytics 服务收到作业提交/开始运行作业/结束作业（无论成功与否）的时间。
    * 编译/已排队/正在运行：正在准备/已排队/正在运行阶段所用的时钟时间。
    * 帐户：用于运行作业的 Data Lake Analytics 帐户。
    * 作者：提交作业的用户，可以是真正的用户帐户，也可以是系统帐户。
    * 优先级：作业优先级。 编号越低，优先级越高。 仅影响作业在队列中的顺序。 设置更高优先级不能优先于正在运行的作业。
    * 并行度：请求的 Azure Data Lake Analytics 单元 (ADLAUs) 最大并发数，又称顶点。 目前，一个顶点等同于一台配有两个虚拟核心和 6 GB RAM 的 VM，但将来可在 Data Lake Analytics 更新中对此进行升级。
    * 剩余字节数：作业完成前需处理的字节数。
    * 读取/写入字节数：自作业开始运行起，已读取/写入的字节数。
    * 顶点总数：作业分成了多项工作，每项工作称为顶点。 此值说明作业包含的工作数。 可将一个顶点看作一个基本进程单元（也称 Azure Data Lake Analytics 单元 (ADLAU)），顶点可以并行运行。 
    * 已完成/正在运行/失败：已完成/正在运行/失败顶点的计数。 顶点可能因用户代码和系统故障而失败，但系统会自动重试几次失败的顶点。 如果重试后，顶点仍失败，则整个作业将失败。
* 作业图
  
    U-SQL 脚本表示将输入数据转换为输出数据的逻辑。 该脚本在“准备”阶段会编译和优化为物理执行计划。 作业图用于显示物理执行计划。  下图演示了此概过程：
  
    ![Azure Data Lake Analytics 作业阶段状态](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-logical-to-physical-plan.png)
  
    作业分成了多项工作。 每项工作称为顶点。 顶点分组为超级顶点（又称阶段），并可视化为作业图。 作业图中的绿色阶段标牌显示各个阶段。
  
    同一阶段内的每个顶点使用相同数据的不同片段执行相同类型的工作。 例如，如果有一个含 1 TB 数据的文件，有数百个顶点从中读取数据，则每个顶点各读取一个区块。 这些顶点在同一阶段内分组，并对同一输出文件的不同区块执行相同工作。
  
  * <a name="state-information"></a>阶段信息
    
      在特定阶段，某些数字会显示在标牌中。
    
      ![Azure Data Lake Analytics 作业图阶段](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage.png)
    
    * SV1 提取：由数字和操作方法命名的阶段名称。
    * 84 个顶点：此阶段的顶点总数。 上图说明此阶段分成了多少项工作。
    * 12.90 秒/顶点：此阶段的顶点平均执行时间。 此图按 SUM（每个顶点的执行时间）/（顶点总数）计算。 这意味着，如果可以并行执行所有顶点，整个阶段的完成时间约为 12.90 秒。 另外，如果按序列完成此阶段中的所有工作，所需时间为：顶点数 * 平均时间。
    * 写入 850,895 行：此阶段写入的总行数。
    * R/W：此阶段读取/写入的数据量（以字节为单位）。
    * 颜色：本阶段使用颜色来指示不同的顶点状态。
      
      * 绿色指示顶点成功。
      * 橙色指示顶点重试。 重试顶点已失败，但系统自动重试并成功完成该顶点，进而成功完成整个阶段。 如果顶点重试后仍失败，颜色将变为红色，整个作业也将失败。
      * 红色指示失败，这意味着系统已多次重试某特定顶点，但仍以失败告终。 此情况会导致整个作业失败。
      * 蓝色表示某特定顶点正在运行。
      * 白色指示顶点处于等待状态。 顶点可能正等待 ADLAU 可用后得以安排，或者因输入数据尚未就绪而等待输入。
      
      通过将鼠标悬停在某一状态上，可查看阶段详细信息：
      
      ![Azure Data Lake Analytics 作业图阶段详细信息](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-stage-details.png)
  * 顶点：描述顶点详细信息，例如顶点总数、已完成顶点数、它们是否失败或仍在运行/处于等待中，等等。
  * 跨 pod/pod 内数据读取：文件和数据存储在分布式文件系统的多个 pod 内。 此处的值描述在同一 pod 或跨 pod 内已读取的数据量。
  * 总计算时间：阶段中每个顶点的执行时间总和，可将其视作仅在一个顶点中执行阶段内所有作业所用的时间。
  * 写入/读取的数据量和行数：指示已读取/写入或者需读取的数据量或行数。
  * 顶点读取失败数：说明读取数据时失败的顶点数。
  * 重复顶点放弃数：如果顶点运行速度太慢，系统可能安排多个顶点来运行同一项工作。 其中一个顶点成功完成后，将放弃冗余顶点。 顶点重复会放弃在阶段中作为重复被放弃的顶点数记录。
  * 顶点吊销数：出于某些原因，顶点在成功后重新运行。 例如，如果下游顶点丢失中间输入数据，则会要求上游顶点重新运行。
  * 顶点计划执行数：顶点已执行的总次数。
  * 读取的最小/平均/最大顶点数据：每个顶点读取数据的最小/平均/最大值。
  * 持续时间：阶段所用的时钟时间，需加载配置文件才能查看此值。
  * 作业播放
    
      Data Lake Analytics 运行作业并存档该作业的顶点运行信息，例如顶点开始、结束、失败的时间，以及如何重试等。所有信息均自动记录在查询存储内，并存储在作业配置文件中。 可通过作业视图中的“加载配置文件”下载该作业配置文件，并可在下载后查看作业播放。
    
      作业播放是反映群集所发生情况的典型可视化效果。 它有助于观看作业执行进度，并在超短时间内（通常小于 30 秒）直观地检测出性能异常和瓶颈。
  * 作业热度地图显示 
    
      通过作业图中的“显示”下拉列表可选择作业热度地图。 
    
      ![Azure Data Lake Analytics 作业图热度地图显示](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-display.png)
    
      它可显示作业 I/O、时间和吞吐量热度地图，通过此热度地图，可找到作业在哪部分占用的时间最多，或辨别作业是否为 I/O 边界作业等。
    
      ![Azure Data Lake Analytics 作业图热度地图示例](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-graph-heat-map-example.png)
    
    * 进度：作业执行进度，请参阅[阶段信息](#stage-information)中的相关信息。
    * 数据读取/写入：每个阶段读取/写入的总数据量热度地图。
    * 计算时间：SUM（每个顶点执行时间）热度地图，可将其视作仅在 1 个顶点内执行阶段内所有工作所用的时间。
    * 平均执行时间/节点：SUM（每个顶点执行时间）/（顶点数）热度地图。 这意味着，如果可以并行执行所有顶点，整个阶段将在此时间范围内完成。
    * 输入/输出吞吐量：每个阶段的输入/输出吞吐量热度地图，通过此热度地图，可确认作业是否为 I/O 边界作业。
* 元数据操作
  
    可在 U-SQL 脚本中执行某些元数据操作，例如创建数据库、删除表等。编译完成后，元数据操作中会显示这些操作。 可在此处查找断言、创建实体、删除实体。
  
    ![Azure Data Lake Analytics 作业视图元数据操作](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-metadata-operations.png)
* 状态历史记录
  
    也可在作业摘要中查看状态历史记录，还可在此处获得更多详细信息。 可找到作业准备/排队/开始运行/结束时间等详细信息。 此外，还可找到作业已编译次数 (CcsAttempts: 1)、作业分派到群集的实际时间（详细信息：将作业分派到集群）等。
  
    ![Azure Data Lake Analytics 作业视图元数据状态历史记录](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-state-history.png)
* 诊断
  
    此工具可自动诊断作业执行。 作业中出现一些错误或性能问题时，将会收到警报。 请注意，需下载配置文件才能获取此处的完整信息。 
  
    ![Azure Data Lake Analytics 作业视图诊断](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-view-diagnostics.png)
  
  * 警告：出现编译器警告时，此处显示警报。 警报显示后，可单击“x 问题”链接了解详细信息。
  * 顶点运行时间过长：如果任何顶点运行超时（如 5 小时），此处将显示问题。
  * 资源使用情况：如果所分配并行度多于或少于所需并行度，此处将出现问题。 此外，还可单击“资源使用情况”查看更多详细信息，并执行假设方案以找到更好的资源分配（有关详细信息，请参阅本指南）。
  * 内存检查：如果任何顶点占用超过 5 GB 内存，此处将出现问题。 如果作业执行占用内存超出系统限制，系统将终止作业执行。

## <a name="job-detail"></a>作业详细信息
作业详细信息显示作业的详细信息，包括脚本、资源和顶点执行视图。

![Azure Data Lake Analytics 作业详细信息](./media/data-lake-analytics-data-lake-tools-view-jobs/data-lake-tools-job-details.png)

* 脚本
  
    作业的 U-SQL 脚本存储在查询存储中。 如果需要，可查看原始 U-SQL 脚本并重新提交该脚本。
* 资源
  
    通过“资源”，可找到存储在查询存储中的作业编译输出。 例如，可在此处找到用于显示作业图的“algebra.xml”、注册的程序集等。
* 顶点执行视图
  
    它显示顶点执行详细信息。 作业配置文件存档每个顶点执行日志，例如读取/写入的总数据量、运行时、状态等。通过此视图，可详细了解作业的运行方式。 有关详细信息，请参阅[使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)。

## <a name="next-steps"></a>后续步骤
* 有关 Data Lake Analytics 的概述，请参阅 [Azure Data Lake Analytics 概述](data-lake-analytics-overview.md)。
* 若要着手开发 U-SQL 应用程序，请参阅 [使用 Data Lake Tools for Visual Studio 开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)。
* 若要了解 U-SQL，请参阅 [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)。
* 有关管理任务，请参阅 [Manage Azure Data Lake Analytics using Azure Portal](data-lake-analytics-manage-use-portal.md)（使用 Azure 门户管理 Azure Data Lake Analytics）。
* 若要记录诊断信息，请参阅 [Accessing diagnostics logs for Azure Data Lake Analytics](data-lake-analytics-diagnostic-logs.md)
* 若要查看更复杂的查询，请参阅 [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)。
* 若要使用顶点执行视图，请参阅[使用用于 Visual Studio 的 Data Lake 工具中的顶点执行视图](data-lake-analytics-data-lake-tools-use-vertex-execution-view.md)
* 若要了解用于 Visual Studio 的 Data Lake 工具代码，请参阅[使用用于 Visual Studio 的 Data Lake 工具代码](data-lake-analytics-data-lake-tools-for-vscode.md)。




<!--HONumber=Nov16_HO4-->


