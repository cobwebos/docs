---
title: "介绍 Azure Data Lake Analytics U-SQL 目录 | Microsoft Docs"
description: "介绍 Azure Data Lake Analytics U-SQL 目录"
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 55fef96f-e941-4d09-af5e-dd7c88c502b2
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/16/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 15b22d577343667c9232b3394cf64bc307ccd777


---
# <a name="use-u-sql-catalog"></a>使用 U-SQL 目录
可以使用 -SQL 目录组织数据和代码，以便通过 U-SQL 脚本共享数据和代码。 目录启用 Azure Data Lake 中数据的最高可能性能。

每个 Azure Data Lake Analytics 帐户都有一个与其关联的 U-SQL 目录。 不能删除 U-SQL 目录。 当前不能在 Data Lake Store 帐户之间共享 U-SQL 目录。

每个 U-SQL 目录都包含一个名为 **Master** 的数据库。 不能删除 Master 数据库。  每个 U-SQL 目录可以包含多个其他数据库。

U-SQL 数据库包含：

* 程序集 – 在 U-SQL 脚本之间共享 .NET 代码。
* 表值函数 – 在 U-SQL 脚本之间共享 U-SQL 代码。
* 表 – 在 U-SQL 脚本之间共享数据。
* 架构 - 在 U-SQL 脚本之间共享表架构。

## <a name="manage-catalogs"></a>管理目录
每个 Azure Data Lake Analytics 帐户都有与其关联的默认 Azure Data Lake Store 帐户。 该 Data Lake Store 帐户称作默认 Data Lake Store 帐户。 U-SQL 目录存储在 /catalog 文件夹下的默认 Data Lake Store 帐户中。 不要删除 /catalog 文件夹中的任何文件。

### <a name="use-azure-portal"></a>使用 Azure 门户
请参阅[使用门户管理 Data Lake Analytics](data-lake-analytics-manage-use-portal.md#view-u-sql-catalog)

### <a name="use-data-lake-tools-for-visual-studio"></a>使用适用于 Visual Studio 的 Data Lake 工具。
适用于 Visual Studio 的 Data Lake Tools 可用于管理目录。  有关此工具的详细信息，请参阅[使用适用于 Visual Studio 的 Data Lake 工具](data-lake-analytics-data-lake-tools-get-started.md)。

**管理目录**

1. 打开 Visual Studio，并连接到 Azure。 有关说明，请参阅 [Connect to Azure](data-lake-analytics-data-lake-tools-get-started.md#connect-to-azure)（连接到 Azure）。
2. 通过按 **CTRL+ALT+S**打开“服务器资源管理器”。
3. 在“服务器资源管理器”中依次展开 “Azure”、“Data Lake Analytics”、Data Lake Analytics 帐户、“数据库” 和 “master”。

    - 若要添加新数据库，右键单击“数据库”，然后单击“创建数据库”。
    - 若要添加新程序集，右键单击“程序集”然后单击“注册程序集”。
    - 若要添加新架构，右键单击“架构”然后单击“创建架构**”。
    - 若要添加新表，右键单击“表”，然后单击“创建表”**。
    - 若要添加新的表值函数，请参阅 [Develop U-SQL user defined operators for Data Lake Analytics jobs](data-lake-analytics-u-sql-develop-user-defined-operators.md)（为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符）。


![浏览 U-SQL Visual Studio 目录](./media/data-lake-analytics-use-u-sql-catalog/data-lake-analytics-browse-catalogs.png)

## <a name="see-also"></a>另请参阅
* 入门
  
  * [Get started with Data Lake Analytics using Azure portal](data-lake-analytics-get-started-portal.md)
  * [Get started with Data Lake Analytics using Azure PowerShell](data-lake-analytics-get-started-powershell.md)
  * [通过 Azure .NET SDK 实现 Data Lake Analytics 入门](data-lake-analytics-get-started-net-sdk.md)
  * [通过 Visual Studio 的 Data Lake 工具开发 U-SQL 脚本](data-lake-analytics-data-lake-tools-get-started.md)
  * [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
* U-SQL 和开发
  
  * [Azure Data Lake Analytics U-SQL 语言入门](data-lake-analytics-u-sql-get-started.md)
  * [对 Azure Data Lake Analytics 作业使用 U-SQL 开窗函数](data-lake-analytics-use-window-functions.md)
  * [为 Data Lake Analytics 作业开发 U-SQL 用户定义的运算符](data-lake-analytics-u-sql-develop-user-defined-operators.md)
* 管理
  
  * [使用 Azure 门户管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-portal.md)
  * [使用 Azure PowerShell 管理 Azure Data Lake Analytics](data-lake-analytics-manage-use-powershell.md)
  * [使用 Azure 门户监视 Azure Data Lake Analytics 作业以及对其进行故障排除](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
* 端到端教程
  
  * [使用 Azure Data Lake Analytics 交互式教程](data-lake-analytics-use-interactive-tutorials.md)
  * [使用 Azure Data Lake Analytics 分析网站日志](data-lake-analytics-analyze-weblogs.md)




<!--HONumber=Nov16_HO3-->


