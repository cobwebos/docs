---
title: "盒中的 SaaS（使用 Azure SQL 数据库的示例 SaaS 应用程序）| Microsoft Docs"
description: "使用 SQL 数据库生成 SaaS 应用程序"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bf5745a788cd9ab6bf2ea8d5d97b8c04f083fc5d
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-the-wingtip-tickets-platform-wtp-sample-saas-application"></a>Wingtip 票证平台 (WTP) 示例 SaaS 应用程序简介

Wingtip 票证平台 (WTP) SaaS 应用程序是一个示例性的多租户应用，用于演示 SQL 数据库的独特优势。 该应用使用租户各有数据库（一种 SaaS 应用程序模式）为多个租户提供服务。 WTP 应用旨在展示支持 SaaS 方案（包括 SaaS 设计及管理模式）的 Azure SQL 数据库功能。 [WTP 应用的部署时间不到五分钟](sql-database-saas-tutorial.md)，可以快速启动并运行！

部署 WTP 应用后，请浏览基于初始部署的[教程系列](#sql-database-saas-tutorials)。 每个教程都重点介绍在 SaaS 应用程序中实施的典型任务。 任务在实施时遵循 SaaS 模式，以便充分利用 SQL 数据库的内置功能。 介绍的模式包括：预配新租户、还原租户数据库、跨所有租户运行分布式查询，以及在所有租户数据库中推出架构更改。 每个教程都包括可重用脚本，附带十分有助于理解的详细介绍，并在应用程序中实施相同的 SaaS 管理模式。

虽然 WTP 应用程序作为示例应用程序来说比较完整且功能强大，但必须专注于与数据层相关的核心 SaaS 模式，这一点很重要。 换句话说，请专注于数据层，不要将过多精力放在分析应用本身上。 了解如何实现这些核心 SaaS 模式对于在应用程序中实现这些模式很重要，同时可考虑按特定的业务要求进行任何必需的修改。



## <a name="application-architecture"></a>应用程序体系结构

WTP 使用租户各有数据库模型，并使用 SQL 弹性池来最大程度地提高效率。
使用租户目录来预配管理和连接性。
集成应用、池、数据库监视和警报 (OMS)。
跨租户架构和引用数据管理（弹性数据库作业）。
跨租户查询、运行分析（弹性查询）。
使用按地理分布的数据扩大访问范围。
业务连续性单租户恢复 (PITR) 大规模 DR（异地还原、异地复制、自动 DR）租户自助式管理（通过管理 API 进行）用于从自我错误中恢复的 PITR。

核心 Wingtip 应用程序，使用一个池和三个示例租户，以及一个目录数据库。

![WTP 体系结构](media/sql-database-wtp-overview/wtp-architecture.png)


## <a name="sql-database-wtp-saas-tutorials"></a>SQL 数据库 WTP SaaS 教程

以下教程基于初始部署的 [Wingtip 票证平台 SaaS 应用程序示例](sql-database-saas-tutorial.md)：

| 区域 | 说明 | 脚本位置 |
|:--|:--|:--|
|[“预配和编录租户”教程](sql-database-saas-tutorial-provision-and-catalog.md)| 预配新租户并将其注册到目录中 | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Provision%20and%20Catalog) |
|[“视和管理性能”教程](sql-database-saas-tutorial-performance-monitoring.md)| 监视和管理数据库和池的性能 | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management) |
|[“还原单个租户”教程](sql-database-saas-tutorial-restore-single-tenant.md)| 还原租户数据库 | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Business%20Continuity%20and%20Disaster%20Recovery/RestoreTenant) |
|[“管理租户架构”教程](sql-database-saas-tutorial-schema-management.md)| 跨所有租户执行查询  | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Schema%20Management) |
|[“运行即席分析”教程](sql-database-saas-tutorial-adhoc-analytics.md) | 创建即席分析数据库并对所有租户运行查询。  | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Adhoc%20Analytics) |
|[“使用 Log Analytics (OMS) 进行管理”教程](sql-database-saas-tutorial-log-analytics.md) | 配置和浏览 Log Analytics | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Performance%20Monitoring%20and%20Management/LogAnalytics) |
|[“运行租户分析”教程](sql-database-saas-tutorial-tenant-analytics.md) | 设置和运行租户分析查询 | [GitHub 上的脚本](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules/Operational%20Analytics/Tenant%20Analytics) |

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip 票证脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 脚本文件位于 [Learning Modules 文件夹](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules)中。 将 **Learning Modules** 文件夹下载到本地计算机，保持其文件夹结构不变。

## <a name="working-with-the-wtp-powershell-scripts"></a>使用 WTP PowerShell 脚本

使用 WTP 应用程序的好处是可以分析提供的脚本，并且可以查看不同的 SaaS 模式是如何实现的。

若要查看提供的脚本和模块并对其逐步分析以加深理解，请使用 [Windows PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。 大多数带 *Demo-* 前缀的脚本所包含的变量可以在执行前修改，因此使用 PowerShell ISE 可以简化这些脚本的使用。

每次进行 WTP 应用部署时，都会有一个 **UserConfig.psm1** 文件，其中包含两个用于设置资源组和用户名称值的参数，而这些值是在部署过程中定义的。 部署完成后，请编辑 **UserConfig.psm1** 模块，设置 _ResourceGroupName_ 和 _Name_ 参数。 这些值是成功运行其他脚本所必须使用的，因此建议在部署完成后设置它们！



### <a name="execute-scripts-by-pressing-f5"></a>按 F5 执行脚本

多个脚本使用 *$PSScriptRoot* 来启用文件夹导航，该变量仅在脚本是按 **F5** 执行的情况下才能进行进行计算。  突出显示和运行某个选择的内容 (**F8**) 可能会生成错误，因此请在运行 WTP 脚本时按 **F5**。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>以分步执行脚本的方式检查实现情况

浏览脚本的真正价值在于通过分步执行脚本来了解其功能。 请查看第一级 _Demo-_ 脚本，这些脚本提供了易读的概略性工作流，显示了完成每个任务所需的步骤。 进一步深入分析各个调用，了解不同 SaaS 模式的实现细节。

有关使用和[调试 PowerShell 脚本](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)的提示：

* 在 PowerShell ISE 中打开和配置 demo- 脚本。
* 按 **F5** 执行脚本或继续操作。 不建议使用 **F8**，因为在运行选择的脚本时不对 *$PSScriptRoot* 求值。
* 单击或选择一个行并按 **F9** 即可放置断点。
* 使用 **F10** 逐过程执行函数或脚本调用。
* 使用 **F11** 单步执行函数或脚本调用。
* 使用 **Shift + F11** 跳出当前函数或脚本调用。




## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>使用 SSMS 浏览数据库架构并执行 SQL 查询

使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 连接和浏览 WTP 服务器和数据库。

WTP 示例应用最初有两个可以连接到其中的 SQL 数据库服务器 - *tenants1* 服务器和编录服务器：


1. 打开 *SSMS* 并连接到 *tenants1-&lt;User&gt;.database.windows.net* 服务器。
2. 单击“连接” > “数据库引擎...”：

   ![编录服务器](media/sql-database-wtp-overview/connect.png)

1. 演示凭据为：登录名 = *developer*，密码 = *P@ssword1*

   ![连接](media\sql-database-wtp-overview\tenants1-connect.png)

1. 重复步骤 2-3，连接到 *catalog-&lt;User&gt;.database.windows.net* 服务器。

成功连接后，会看到两个服务器。 你看到的数据库可能会多一些或少一些，具体取决于预配的租户数：

![对象资源管理器](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>后续步骤

[部署 Wingtip 票证 SaaS 应用程序示例](sql-database-saas-tutorial.md)
