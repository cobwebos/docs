<properties 
   pageTitle="Microsoft Azure Data Lake Analytics 概述 | Azure" 
   description="Data Lake Analytics 是一种 Azure 大数据计算服务，让你使用各种数据，借助基于云中数据收集的见解推动企业的发展，而不用考虑数据位置和大小。而且 Data Lake Analytics 以最简单、最具扩展性和最经济的方式实现了上述目标。" 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="edmacauley" 
   manager="jhubbard" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="05/16/2016"
   ms.author="edmaca"/>

# Microsoft Azure Data Lake Analytics 概述

## 什么是 Azure Data Lake Analytics？

Azure Data Lake Analytics 是一项新服务，旨在使大数据分析变得更加轻松。借助此服务，你可以集中精力编写、运行和管理作业，不用运行分布式基础结构。无需部署、配置和调整硬件，只需编写查询即可转换数据并提取有价值的见解。只需将表盘设置为所需值，该分析服务就可以立即处理任何规模的作业。你只需要为你的运行作业付费，让服务变得更为经济高效。该分析服务支持 Azure Active Directory，让你可简单管理访问和角色，并与你的本地识别系统集成。它还包括了 U-SQL 语言，有效结合了 SQL 的优点和用户代码的表达力。U-SQL 的可缩放分布式运行时可让你高效地分析存储中的数据，以及跨 Azure 中的 SQL Server、Azure SQL 数据库和 Azure SQL 数据仓库的数据。


## 关键功能

- **动态缩放**

    Data Lake Analytics 从头开始针对云缩放和性能需求进行构建。它能动态地预配资源并让你以千吉字节甚至百亿亿字节为单位进行分析。当作业完成时，它自动释放资源，你仅需为所用的处理功能付费。增加或减少存储数据的大小或使用的计算量时，不需要重写代码。这可使你仅关注自己的业务逻辑而非如何处理和存储大数据集。

- **使用熟悉的工具更快开发、调试和更聪明地优化**

    Data Lake Analytics 与 Visual Studio 深度集成，从而你可以使用熟悉的工具运行、调试和调整你的代码。U-SQL 作业可视化允许你看见你的代码如何大规模运行，因此你可以轻松找到性能瓶颈并优化成本。

- **U-SQL：简单熟悉、功能强大且易于扩展**

    Data Lake Analytics 包含 U-SQL，这是一种查询语言，扩展了 SQL 的简单熟悉的声明性本质和 C# 的表现力。U-SQL 语言基于在 Microsoft 内部支持大数据系统的同一分布式运行时。现在，数以百万计的 SQL 和 .NET 开发人员可以凭借自身已有的技能处理和分析自己全部的数据。

- **与你的 IT 投资无缝集成**

    Data Lake Analytics 可以使用你现有的 IT 投资进行识别、管理、安全和数据仓库工作来应对这个挑战。这样就简化了数据管理，使你当前的数据应用程序更容易扩展。Data Lake Analytics 与适用于用户管理和权限的 Active Directory 集成且随附内置的监控与审核功能。

- **价格合理且经济高效**

    Data Lake Analytics 是用于运行大数据工作负荷的经济高效的解决方案。处理数据时按每个作业付费。无需硬件、许可证或服务特定的支持协议。作业开始和完成时，系统自动缩放大小，这意味着你永远无需为你所需之外的东西付费。

- **可用于所有 Azure 数据**

    Data Lake Analytics 可用于各种 Azure 数据源：Azure Blob 存储空间、Azure SQL 数据库，当然 Data Lake Analytics 经过特别优化，可用于 Azure Data Lake 存储，从而为大数据工作负荷提供最高级别的性能、吞吐量和并行化。

## 另请参阅

- 入门
    - [通过 Azure 门户实现 Data Lake Analytics 入门](data-lake-analytics-get-started-portal.md)
    - [通过 Azure PowerShell 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-powershell.md)
    - [通过 Azure .NET SDK 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-net-sdk.md)
    - [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
    - [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
    
- U-SQL 和开发
    - [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
    - [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
    - [为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)
    
- 管理
    - [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
    - [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
    - [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
    - [访问 Azure Data Lake Analytics 的诊断日志](data-lake-analytics-diagnostic-logs.md)

- 端到端教程
    - [使用 Azure Data Lake Analytics 交互式教程](data-lake-analytics-use-interactive-tutorials.md)
    - [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)

- 让我们知道你的想法
    - [请对我们的文档积压工作发表评论](data-lake-analytics-documentation-backlog.md)
    - [提交功能请求](http://aka.ms/adlafeedback)
    - [在论坛中获得帮助](http://aka.ms/adlaforums)

<!---HONumber=AcomDC_0921_2016-->