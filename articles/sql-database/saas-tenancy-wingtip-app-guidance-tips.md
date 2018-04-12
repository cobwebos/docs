---
title: SQL 数据库多租户应用示例指南 - Wingtip SaaS | Microsoft Docs
description: 提供有关安装和运行使用 Azure SQL 数据库（Wingtip Tickets SaaS 示例）的示例多租户应用程序的步骤和指南。
keywords: sql 数据库教程
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: 1e3ecd27ce7ec2e2763a7249428875e51872bf63
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="general-guidance-for-working-with-wingtip-tickets-sample-saas-apps"></a>使用 Wingtip Tickets 示例 SaaS 应用的一般指南

本文包含运行使用 Azure SQL 数据库的 Wingtip Tickets 示例 SaaS 应用的一般指南。 

## <a name="download-and-unblock-the-wingtip-tickets-saas-scripts"></a>下载和取消阻止 Wingtip Tickets SaaS 脚本

从外部源下载和提取 zip 文件时，可执行内容（脚本、ddl）可能受 Windows 阻止。 从 zip 文件提取脚本时，在提取前请按以下步骤取消阻止 .zip 文件。 这可确保允许运行该脚本。

1. 浏览到 Wingtip Tickets SaaS GitHub 存储库，查看想要了解的数据租户模式： 
    - [WingtipTicketsSaaS-StandaloneApp](https://github.com/Microsoft/WingtipTicketsSaaS-StandaloneApp)
    - [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant)
    - [WingtipTicketsSaaS-MultiTenantDb](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)
2. 单击“克隆或下载”。
3. 单击“下载 zip”并保存文件。
4. 右键单击 zip 文件并选择“属性”。 zip 文件名与存储库名称对应。 （例如： WingtipTicketsSaaS-DbPerTenant-master.zip）
5. 在“常规”选项卡上，选择“取消阻止”。
6. 单击“确定”。
7. 解压缩文件。

脚本位于 ..\\Learning Modules 文件夹中。


## <a name="working-with-the-wingtip-tickets-powershell-scripts"></a>使用 Wingtip Tickets PowerShell 脚本

若要充分利用示例，需深入了解提供的脚本。 使用断点并分步执行脚本，并检查不同 SaaS 模式的实现方式。 为了轻松地分步执行提供的脚本和模块，获得最佳理解，建议使用 [PowerShell ISE](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/introducing-the-windows-powershell-ise)。

### <a name="update-the-configuration-file-for-your-deployment"></a>为部署更新配置文件

使用在部署期间设置的资源组和用户值编辑 UserConfig.psm1 文件：

1. 打开 PowerShell ISE，并加载 ...\\Learning Modules\\UserConfig.psm1 
2. 使用部署（仅限 10 和 11 行）的特定值，更新 ResourceGroupName 和 Name。
3. 保存更改！

只需在此处设置这些值，无需在每个脚本中更新这些特定于部署的值。

### <a name="execute-the-scripts-by-pressing-f5"></a>按 F5 执行脚本

多个脚本使用 $PSScriptRoot 来浏览文件夹，且仅在按 F5 执行脚本时才对 $PSScriptRoot 求值。  突出显示和运行某个选择的内容 (F8) 时，可能会生成错误，因此请在运行脚本时按 F5。

### <a name="step-through-the-scripts-to-examine-the-implementation"></a>以分步执行脚本的方式检查实现情况

了解脚本的最好方式就是单步执行脚本，了解它们的用途。 查看包含的 Demo- 脚本，该脚本提供了一个简单易懂的高级工作流。 Demo- 脚本演示完成每个任务所需的步骤，设置断点并深入分析各个调用，查看不同 SaaS 模式的实现细节。

探索和单步执行 PowerShell 脚本的提示：

- 在 PowerShell ISE 中打开 Demo- 脚本。
- 按 F5 执行或继续（不建议使用 F8，因为在运行选择的脚本时不对 $PSScriptRoot 求值）。
- 单击或选择一个行并按 **F9** 即可放置断点。
- 使用 **F10** 逐过程执行函数或脚本调用。
- 使用 **F11** 单步执行函数或脚本调用。
- 使用 **Shift + F11** 跳出当前函数或脚本调用。


## <a name="explore-database-schema-and-execute-sql-queries-using-ssms"></a>使用 SSMS 浏览数据库架构并执行 SQL 查询

使用 [SQL Server Management Studio (SSMS)](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) 连接和浏览应用程序服务器和数据库。

该部署最初具有要连接的租户和目录 SQL 数据库服务器。 服务器的命名取决于数据库租户模式（请参阅下文了解详细信息）。 

   - **独立的应用程序：**每个租户的服务器（例如： contosoconcerthall-&lt;User&gt; 服务器）和 catalog-sa-&lt;User&gt;
   - **每个租户一个数据库：**tenants1-dpt-&lt;User&gt; 和 catalog-dpt-&lt;User&gt; 服务器
   - **多租户数据库：**tenants1-mt-&lt;User&gt; 和 catalog-mt-&lt;User&gt; 服务器

为确保成功连接演示，所有服务器的[防火墙规则](sql-database-firewall-configure.md)都允许所有 IP 通过。


1. 打开 SSMS 并连接到租户。 服务器名称取决于所选数据库租户模式（请参阅下文了解详细信息）：
    - **独立的应用程序：**各个租户的服务器（例如： contosoconcerthall-&lt;User&gt;.database.windows.net） 
    - **每个租户一个数据库：**tenants1-dpt-&lt;User&gt;.database.windows.net
    - **多租户数据库：**tenants1-mt-&lt;User&gt;.database.windows.net 
2. 单击“连接” > “数据库引擎...”：

   ![编录服务器](media/saas-tenancy-wingtip-app-guidance-tips/connect.png)

3. 演示凭据为：登录名 = *developer*，密码 = *P@ssword1*

    下图显示“每个租户一个数据库”模式的登录。 
    ![连接](media/saas-tenancy-wingtip-app-guidance-tips/tenants1-connect.png)
    
   

4. 重复步骤 2-3，并连接到目录服务器（请参阅下文，了解基于所选数据库租户模式的特定服务器名称）
    - **独立的应用程序：**catalog-sa-&lt;User&gt;.database.windows.net
    - **每个租户一个数据库：**catalog-dpt-&lt;User&gt;.database.windows.net
    - **多租户数据库：**catalog-mt-&lt;User&gt;.database.windows.net


成功连接后，会看到所有服务器。 你的数据库列表可能会有所不同，具体取决于已预配的租户。

下图显示“每个租户一个数据库”模式的登录。

![对象资源管理器](media/saas-tenancy-wingtip-app-guidance-tips/object-explorer.png)



## <a name="next-steps"></a>后续步骤
- [部署 Wingtip Tickets SaaS 独立应用程序](saas-standaloneapp-get-started-deploy.md)
- [部署 Wingtip Tickets SaaS 每个租户一个数据库应用程序](saas-dbpertenant-get-started-deploy.md)
- [部署 Wingtip Tickets SaaS 多租户数据库应用程序](saas-multitenantdb-get-started-deploy.md)

