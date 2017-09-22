---
title: "还原多租户 SaaS 应用中的 Azure SQL 数据库 | Microsoft Docs"
description: "了解如何在意外删除数据后还原单个租户 SQL 数据库"
keywords: "sql 数据库教程"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: billgib;sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: 792476849e796695dde3f2ec80b56431a17e8fc0
ms.contentlocale: zh-cn
ms.lasthandoff: 09/13/2017

---
# <a name="restore-a-single-tenants-azure-sql-database-in-a-multi-tenant-saas-app"></a>在多租户 SaaS 应用中还原单个租户 Azure SQL 数据库

Wingtip SaaS 应用是使用租户各有数据库的模型生成的，其中每个租户有自己的数据库。 此模型的好处之一是在不影响其他租户的情况下能轻松地单独还原单个租户的数据。

本教程介绍两种数据恢复模式：

> [!div class="checklist"]

> * 将数据库还原为并行数据库（并行）
> * 就地还原数据库


|||
|:--|:--|
| 将租户还原到之前的时间点并还原为并行数据库 | 租户可使用此模式进行查看、审核以及符合性检测等等。原始数据库将保持联机状态且保持不变。 |
| 就地还原租户 | 此模式通常用于在租户意外删除数据后，将租户恢复到之前的时间点。 原始数据库将处于脱机状态，并且会被替换为还原的数据库。 |
|||

若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](sql-database-saas-tutorial.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-pattern"></a>SaaS 租户还原模式简介

对于租户还原模式，有两种用于还原单个租户的数据的简单模式。 由于租户数据库彼此相互隔离，因此还原某个租户不会对其他租户的数据造成任何影响。

在第一种模式中，数据会被还原到新数据库中。 然后将授予租户访问此数据库及其生产数据的权限。 此模式允许租户管理员查看还原的数据，并可以使用该数据选择性地覆盖当前数据值。 数据恢复选项的复杂程度由 SaaS 应用设计器决定。 在某些方案中，可能仅需要能够查看在某个给定时间点时当前状态的数据即可。 如果数据库使用[异地复制](sql-database-geo-replication-overview.md)，建议从还原的副本将所需数据复制到原始数据库中。 如果将原始数据库替换为还原的数据库，则需要重新配置并重新同步异地复制。

在第二种模式中（假定租户出现数据丢失或损坏的情况下），租户的生产数据库会被还原到之前的某个时间点。 在就地还原模式中，数据库进行还原时，租户会在短时间内处于脱机状态，直到完成后才重新回到联机状态。 原始数据库会被删除，但如有需要，仍可以从更早的时间点将其还原。 尽管此模式的变体能够重命名数据库，而不是将其删除，但就数据安全方面而言，重命名的数据库没有特别的优势。

## <a name="get-the-wingtip-application-scripts"></a>获取 Wingtip 应用程序脚本

Wingtip SaaS 脚本和应用程序源代码可在 [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS) GitHub 存储库中找到。 [下载 Wingtip SaaS 脚本的步骤](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts)。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>模拟租户意外删除数据

若要演示这些恢复方案，需要意外删除某个租户数据库中的一些数据。 虽然可以删除任何记录，但下一步会将演示设置为不会受到引用完整性冲突的阻止！ 它还添加了稍后可在 Wingtip SaaS 分析教程中使用的某些购票数据。

运行门票生成器脚本并创建其他数据。 门票生成器并不打算购买每个租户最后一个事件的门票。

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Utilities\\Demo-TicketGenerator.ps1
1. 按“F5”运行脚本并生成客户和购票数据。


### <a name="open-the-events-app-to-review-the-current-events"></a>打开事件应用查看当前事件

1. 打开“事件中心”(http://events.wtp.&lt;user&gt;.trafficmanager.net) 并单击“Contoso Concert Hall”：

   ![事件中心](media/sql-database-saas-tutorial-restore-single-tenant/events-hub.png)

1. 滚动事件列表，并记下列表中的最后一个事件：

   ![最后一个事件](media/sql-database-saas-tutorial-restore-single-tenant/last-event.png)


### <a name="run-the-demo-scenario-to-accidentally-delete-the-last-event"></a>运行意外删除最后一个事件的演示方案

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\Demo-RestoreTenant.ps1，并设置以下值：
   * $DemoScenario = 1，设置为“1”会删除没有门票销售的事件。
1. 按“F5”运行脚本并删除最后一个事件。 应出现类似于下面的确认消息：

   ```Console
   Deleting unsold events from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso 事件页面将打开。 向下滚动并验证该事件已不存在。 如果事件仍在列表中，请单击刷新并再次进行验证。

   ![最后一个事件](media/sql-database-saas-tutorial-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>还原与生产数据库并行的租户数据库

本练习将 Contoso Concert Hall 数据库还原到删除该事件前的某个时间点。 在先前的步骤中删除该事件后，用户希望恢复并查看已删除的数据。 用户不需要还原具有已删除记录的生产数据库，但出于其他商业原因，需要恢复旧数据库以访问旧数据。

 Restore-TenantInParallel.ps1 脚本将创建均名为 ContosoConcertHall\_old 的并行租户数据库和并行目录条目。 此还原模式最适合用于从少量数据丢失中进行恢复或符合性和审核恢复方案。 使用[异地复制](sql-database-geo-replication-overview.md)时，建议也使用此方法。

1. 完成[模拟用户意外删除数据](#simulate-a-tenant-accidentally-deleting-data)部分。
1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\Demo-RestoreTenant.ps1。
1. 设置 $DemoScenario = 2，将其设置为“2”以并行还原租户。
1. 按 **F5** 运行脚本。

该脚本将租户数据库还原（到并行数据库）到在上一部分中删除该事件之前的某个时间点。 它将创建第二个数据库，删除此数据库中存在的任何现有目录元数据，并将数据库添加到 ContosoConcertHall\_old 条目下的目录。

演示脚本会在浏览器中打开事件页面。 从 URL ```http://events.wtp.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old``` 中可以看到：它从还原的数据库中显示数据，其中，在名称里添加了 _old。

滚动浏览器中列出的事件，确认在上一部分中删除的事件已还原。

请注意，将还原的租户公开为一个额外的租户，且该租户使用自己的事件应用浏览门票，尽管这可能与为租户提供的访问还原数据的方式不同，但却很容易阐释清楚此还原模式。

实际上，用户可能仅会在一定的时间内保留此还原的数据库。 完成操作后，可以通过调用 Remove-RestoredTenant.ps1 脚本删除还原的租户条目。

1. 将 $DemoScenario 设置为“4”，以选择“删除还原的租户”方案。
1. 使用“F5”执行此操作
1. 现在已从目录中删除了 ContosoConcertHall\_old 条目。 接着，在浏览器中关闭此租户的事件页面。


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>就地还原租户，替换现有租户数据库

本练习将 Contoso Concert Hall 租户还原到删除该事件前的某个时间点。 Restore-TenantInPlace 脚本将当前租户数据库还原到指向上一时间点的新数据库，并删除原始数据库。 此还原模式最适合用于从严重的数据损坏中进行恢复，因为可能存在租户不得不考虑到的大量数据丢失的情况。

1. 在 PowerShell ISE 中打开 Demo-RestoreTenant.ps1 文件
1. 将 $DemoScenario 设置为“5”，以选择“就地还原租户方案”。
1. 使用“F5”执行此操作。

该脚本将租户数据库还原到删除该事件前五分钟的时间点。 这会通过首先让 Contoso Concert Hall 租户脱机，使数据不会进一步更新来进行。 然后，通过从还原点进行还原创建并行数据库，并以时间戳命名，确保该数据库名称不与现有租户数据库名称冲突。 接着，删除旧租户数据库，并将还原的数据库重命名为原始数据库名称。 最后，将 Contoso Concert Hall 联机，使应用能够访问还原的数据库。

已成功将数据库还原到删除事件之前的某个时间点。 “事件”页面将打开，确认最后一个事件已还原。


## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 将数据库还原为并行数据库（并行）
> * 就地还原数据库

[管理租户数据库架构](sql-database-saas-tutorial-schema-management.md)

## <a name="additional-resources"></a>其他资源

* 其他[基于 Wingtip SaaS 应用程序编写的教程](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [使用 Azure SQL 数据库确保业务连续性的相关概述](sql-database-business-continuity.md)
* [了解 SQL 数据库备份](sql-database-automated-backups.md)

