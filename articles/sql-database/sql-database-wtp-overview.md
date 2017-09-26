---
title: "Azure SQL 数据库多租户应用示例 - Wingtip SaaS | Microsoft Docs"
description: "借助使用 Azure SQL 数据库的示例多租户应用程序，了解 Wingtip SaaS 示例"
keywords: "sql 数据库教程"
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 68a9d97a881f3a7628a08b66091c3feb4c4dbbfe
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="introduction-to-a-sql-database-multi-tenant-saas-app-example"></a>SQL 数据库多租户 SaaS 应用示例简介

Wingtip SaaS 应用程序是一个示例多租户应用，用于演示 SQL 数据库的独特优势。 该应用使用租户各有数据库（一种 SaaS 应用程序模式）为多个租户提供服务。 该应用旨在展示支持 SaaS 方案（包括多个 SaaS 设计及管理模式）的 Azure SQL 数据库功能。 Wingtip SaaS 应用的部署时间不到五分钟，可快速启动并运行！

可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) github 存储库获取应用程序源代码和管理脚本。 若要运行这些脚本，请将[Learning Modules 文件夹下载](#download-and-unblock-the-wingtip-saas-scripts)到本地计算机。

## <a name="sql-database-wingtip-saas-tutorials"></a>SQL 数据库 Wingtip SaaS 教程

部署该应用后，请浏览基于初始部署的以下教程。 这些教程探索常见的 SaaS 模式，这些模式利用 SQL 数据库、SQL 数据仓库和其他 Azure 服务的内置功能。 教程包括 PowerShell 脚本，附带十分有助于理解的详细介绍，并在应用程序中实施相同的 SaaS 管理模式。


| 教程 | 说明 |
|:--|:--|
|[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)| 从此处开始！ 向 Azure 订阅部署并浏览 Wingtip SaaS 应用程序。 |
|[预配和编录租户](sql-database-saas-tutorial-provision-and-catalog.md)| 了解应用程序如何使用目录数据库连接到租户，以及目录如何将租户映射到其数据。 |
|[监视和管理性能](sql-database-saas-tutorial-performance-monitoring.md)| 了解如何使用 SQL 数据库的监视功能，以及如何在超出性能阈值时设置警报。 |
|[使用 Log Anaytics 进行监视 (OMS)](sql-database-saas-tutorial-log-analytics.md) | 了解如何使用 [Log Analytics](../log-analytics/log-analytics-overview.md) 跨多个池监视大量资源。 |
|[还原单个租户](sql-database-saas-tutorial-restore-single-tenant.md)| 了解如何将租户数据库还原到先前的时间点。 还包括在使现有租户数据库保持联机的情况下，还原为并行数据库的步骤。 |
|[管理租户架构](sql-database-saas-tutorial-schema-management.md)| 了解如何在所有 Wingtip SaaS 租户中更新架构和更新引用数据。 |
|[运行即席分析](sql-database-saas-tutorial-adhoc-analytics.md) | 创建即席分析数据库并对所有租户运行实时分布式查询。  |
|[运行租户分析](sql-database-saas-tutorial-tenant-analytics.md) | 将租户数据提取到分析数据库或数据仓库，从而运行脱机分析查询。 |



## <a name="application-architecture"></a>应用程序体系结构

Wingtip SaaS 应用使用租户各有数据库模型，并使用 SQL 弹性池来最大程度地提高效率。 为了预配租户并将其映射到租户数据，需使用目录数据库。 核心 Wingtip SaaS 应用程序使用一个池和三个示例租户，外加目录数据库。 完成许多 Wingtip SaaS 教程后，初始部署中会存在附加设备，这是因为引入了分析数据库，跨数据库架构管理等。


![Wingtip SaaS 体系结构](media/sql-database-wtp-overview/app-architecture.png)


学习教程及使用应用时，应关注 SaaS 模式，因为其与数据层相关。 换句话说，请专注于数据层，不要将过多精力放在分析应用本身上。 了解如何实现这些 SaaS 模式对于在应用程序中实现这些模式很重要，同时可考虑按特定的业务要求进行任何必需的修改。

## <a name="download-and-unblock-the-wingtip-saas-scripts"></a>下载和取消阻止 Wingtip SaaS 脚本

从外部源下载和提取 zip 文件时，可执行内容（脚本、ddl）可能受 Windows 阻止。 从 zip 文件提取脚本时，在提取前请按以下步骤取消阻止 .zip 文件。 这可确保允许运行该脚本。

1. 浏览到 [Wingtip SaaS github 存储库](https://github.com/Microsoft/WingtipSaaS)。
1. 单击“克隆或下载”。
1. 单击“下载 ZIP”并保存文件。
1. 右键单击“WingtipSaaS-master.zip”文件，并选择“属性”。
1. 在“常规”选项卡上，选择“取消阻止”。
1. 单击 **“确定”**。
1. 解压缩文件。

脚本位于 ..\\WingtipSaaS-master\\Learning Modules 文件夹中。


## <a name="working-with-the-wingtip-saas-powershell-scripts"></a>使用 Wingtip SaaS PowerShell 脚本

若要充分利用示例，需深入了解提供的脚本。 使用断点并分步执行脚本，详细检查不同 SaaS 模式实现方式的详细信息。 为了轻松地分步执行提供的脚本和模块，获得最佳理解，建议使用 [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。

### <a name="update-the-configuration-file-for-your-deployment"></a>为部署更新配置文件

使用在部署期间设置的资源组和用户值编辑 UserConfig.psm1 文件：

1. 打开 PowerShell ISE，并加载 ...\\Learning Modules\\UserConfig.psm1 
1. 使用部署（仅限 10 和 11 行）的特定值，更新 ResourceGroupName 和 Name。
1. 保存更改！

只需在此处设置这些值，无需在每个脚本中更新这些特定于部署的值。

### <a name="execute-scripts-by-pressing-f5"></a>按 F5 执行脚本

多个脚本使用 $PSScriptRoot 来浏览文件夹，且仅在按 F5 执行脚本时才对 $PSScriptRoot 求值。  突出显示和运行某个选择的内容 (F8) 时，可能会生成错误，因此请在运行脚本时按 F5。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>以分步执行脚本的方式检查实现情况

了解脚本的最好方式就是单步执行脚本，了解它们的用途。 查看包含的 Demo- 脚本，该脚本提供了一个简单易懂的高级工作流。 Demo- 脚本演示完成每个任务所需的步骤，设置断点并深入分析各个调用，查看不同 SaaS 模式的实现细节。

探索和单步执行 PowerShell 脚本的提示：

* 在 PowerShell ISE 中打开 Demo- 脚本。
* 按 F5 执行或继续（不建议使用 F8，因为在运行选择的脚本时不对 $PSScriptRoot 求值）。
* 单击或选择一个行并按 **F9** 即可放置断点。
* 使用 **F10** 逐过程执行函数或脚本调用。
* 使用 **F11** 单步执行函数或脚本调用。
* 使用 **Shift + F11** 跳出当前函数或脚本调用。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>使用 SSMS 浏览数据库架构并执行 SQL 查询

使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 连接和浏览应用程序服务器和数据库。

部署最初有两个要连接的 SQL 数据库服务器 - tenants1-&lt;User&gt; 服务器和 catalog-&lt;User&gt; 服务器。 为确保成功连接演示，两台服务器的[防火墙规则](sql-database-firewall-configure.md)都允许所有 IP 通过。


1. 打开 *SSMS* 并连接到 *tenants1-&lt;User&gt;.database.windows.net* 服务器。
1. 单击“连接” > “数据库引擎...”：

   ![编录服务器](media/sql-database-wtp-overview/connect.png)

1. 演示凭据为：登录名 = *developer*，密码 = *P@ssword1*

   ![连接](media\sql-database-wtp-overview\tenants1-connect.png)

1. 重复步骤 2-3，连接到 *catalog-&lt;User&gt;.database.windows.net* 服务器。

成功连接后，会看到两个服务器。 你的数据库列表可能会有所不同，具体取决于已预配的租户：

![对象资源管理器](media/sql-database-wtp-overview/object-explorer.png)



## <a name="next-steps"></a>后续步骤

[部署 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
