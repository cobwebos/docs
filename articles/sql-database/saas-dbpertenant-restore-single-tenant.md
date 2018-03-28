---
title: 还原多租户 SaaS 应用中的 Azure SQL 数据库 | Microsoft Docs
description: 了解如何在意外删除数据后还原单个租户 SQL 数据库
keywords: sql 数据库教程
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.custom: scale out apps
ms.topic: article
ms.date: 05/10/2017
ms.author: sstein
ms.reviewer: billgib
ms.openlocfilehash: 7ae8bcb6172d9f9d56c531e149635434057fc2af
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/16/2018
---
# <a name="restore-a-single-tenant-with-a-database-per-tenant-saas-application"></a>通过“每租户一个数据库”SaaS 应用程序还原单个租户

通过“每租户一个数据库”模式，可将单个租户轻松换换到之前的某个时间点，而不影响其他租户。

本教程介绍两种数据恢复模式：

> [!div class="checklist"]

> * 将数据库还原为并行数据库（并行）
> * 就地还原数据库，进而替代现有数据库


|||
|:--|:--|
| **还原到并行数据库** | 此模式可用于审阅、审核和符合性等内容，使租户能够从之前的某个时间检查其数据。  租户的当前数据库保持联机状态且不会更改。 |
| **就地还原** | 此模式通常用于在租户误删除或误损坏数据后，将租户还原到之前的某个时间。 原始数据库将处于脱机状态，并且会被替换为还原的数据库。 |
|||

若要完成本教程，请确保已完成了以下先决条件：

* Wingtip SaaS 应用已部署。 若要在五分钟内进行部署，请参阅[部署和浏览 Wingtip SaaS 应用程序](saas-dbpertenant-get-started-deploy.md)
* Azure PowerShell 已安装。 有关详细信息，请参阅 [Azure PowerShell 入门](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-saas-tenant-restore-patterns"></a>SaaS 租户还原模式简介

存在两种简单的模式可用于还原单个租户的数据。 由于租户数据库彼此相互隔离，因此还原某个租户不会对其他租户的数据造成任何影响。  这两种模式均可使用 SQL 数据库时间点还原 (PITR) 功能。  PITR 总是会新建一个数据库。   

在第一种模式（即“并行还原”）中，与租户的当前数据库一起创建一个并行数据库。 随后，向租户授予访问已还原数据库的只读权限。 可访问已还原的数据库，还可使用此数据库覆盖当前数据值。 由应用设计人员决定租户访问已还原数据库的方式以及所提供的还原选项。 在某些情景中，只需授予租户访问其较早时间点的数据的权限即可。 

如果数据卷已丢失或损坏，且租户希望还原到更早的时间点，则第二种模式（即“就地还原”）非常有用。  还原数据库时，租户处于脱机状态。 这会删除原始数据库并重命名已还原的数据库。 删除后，原始数据库的备份链仍可访问，这让你能够在必要时将数据库还原到更早的时间点。

如果数据库使用[异地复制](sql-database-geo-replication-overview.md)和并行还原，则建议将已还原副本中的所有必备数据都复制到原始数据库中。 如果将原始数据库替换为还原的数据库，则需要重新配置并重新同步异地复制。

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>获取 Wingtip Tickets SaaS Database Per Tenant 应用程序的脚本

在 [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) Github 存储库中提供了 Wingtip Tickets SaaS 多租户数据库脚本和应用程序源代码。 有关下载和取消阻止 Wingtip Tickets SaaS 脚本的步骤，请参阅[常规指南](saas-tenancy-wingtip-app-guidance-tips.md)。

## <a name="before-you-start"></a>开始之前

创建数据库时，可能需要 10-15 分钟的时间，才可从第一个完整备份中进行还原。  如果仅安装了应用程序，则可能需要稍等几分钟才可尝试此方案。

## <a name="simulate-a-tenant-accidentally-deleting-data"></a>模拟租户意外删除数据

若要演示这些恢复方案，首先需要“意外”删除某个租户数据库中的事件。 

### <a name="open-the-events-app-to-review-the-current-events"></a>打开事件应用查看当前事件

1. 打开“事件中心”(http://events.wtp.&lt;user&gt;.trafficmanager.net) 并单击“Contoso Concert Hall”：

   ![事件中心](media/saas-dbpertenant-restore-single-tenant/events-hub.png)

1. 滚动事件列表，并记下列表中的最后一个事件：

   ![最后一个事件](media/saas-dbpertenant-restore-single-tenant/last-event.png)


### <a name="accidentally-delete-the-last-event"></a>“意外”删除最后一个事件

1. 在 PowerShell ISE 中打开 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\Demo-RestoreTenant.ps1，并设置以下值：
   * $DemoScenario = 1，表示删除最后一个事件（即售票量为零）。
1. 按“F5”运行脚本并删除最后一个事件。 应会显示以下确认消息：

   ```Console
   Deleting last unsold event from Contoso Concert Hall ...
   Deleted event 'Seriously Strauss' from Contoso Concert Hall venue.
   ```

1. Contoso 事件页面将打开。 向下滚动并验证该事件已不存在。 如果事件仍在列表中，请单击刷新并再次进行验证。

   ![最后一个事件](media/saas-dbpertenant-restore-single-tenant/last-event-deleted.png)


## <a name="restore-a-tenant-database-in-parallel-with-the-production-database"></a>还原与生产数据库并行的租户数据库

本练习将 Contoso Concert Hall 数据库还原到删除该事件前的某个时间点。 在此方案中，假设你仅希望查看并行数据库中已删除的数据。

 Restore-TenantInParallel.ps1 脚本会创建一个名为 ContosoConcertHall\_old 的并行租户数据库（内附并行目录条目）。 此还原模式最适合用于在丢失少量数据的情况下或在需要出于符合性或审核目的查看数据的情况下进行恢复。 使用[异地复制](sql-database-geo-replication-overview.md)时，建议也使用此方法。

1. 完成[模拟租户意外删除数据](#simulate-a-tenant-accidentally-deleting-data)部分。
1. 在 PowerShell ISE 中，打开 ...\\Learning Modules\\Business Continuity and Disaster Recovery\\RestoreTenant\\_Demo-RestoreTenant.ps1_。
1. 设置 $DemoScenario = 2，它表示并行还原租户。
1. 按 **F5** 运行脚本。

此脚本会将租户数据库还原到删除事件之前的某个时间点。 数据库还原到名为 _ContosoConcertHall\_old_ 的新数据库中。 删除此已还原数据中存在的目录元数据现，然后使用基于 *ContosoConcertHall\_old* 名称构造的密钥将此数据库添加到目录中。

演示脚本会在浏览器中为这个新的租户数据库打开事件页面。 URL 中显示的信息：```http://events.wingtip-dpt.&lt;user&gt;.trafficmanager.net/contosoconcerthall_old```此页面正在显示将 _old 添加到名称的已还原数据库中的数据。

滚动浏览器中列出的事件，确认在上一部分中删除的事件已还原。

请注意，不太可能通过将还原后的租户公开为单独的一个租户（自带事件应用），向租户授予访问已还原数据的权限，但此方法可用于显示还原模式。 实际上，你可能会授予访问旧数据的只读权限，并将还原后的此数据库保留一定时间。 在示例中，可在通过运行 _Remove restored tenant_ 方案完成操作后删除已还原的租户条目。

1. 设置 $DemoScenario = 4，它表示删除已还原的租户
1. 使用“F5”执行此操作
1. 现在已从目录中删除了 ContosoConcertHall\_old 条目。 接着，在浏览器中关闭此租户的事件页面。


## <a name="restore-a-tenant-in-place-replacing-the-existing-tenant-database"></a>就地还原租户，替换现有租户数据库

本练习会将 Contoso Concert Hall 租户还原到删除事件之间的某个时间点。 Restore-TenantInPlace 脚本会将租户数据库还原到新的数据库中并删除原始数据库。  此还原模式最适合用于从严重的数据损坏中进行恢复，因为可能存在租户不得不考虑到的大量数据丢失的情况。

1. 在 PowerShell ISE 中打开 Demo-RestoreTenant.ps1 文件
1. 设置 $DemoScenario = 5，它表示就地还原租户。
1. 使用“F5”执行此操作。

该脚本会将租户数据库还原到删除事件之前的某个时间点。 它首先使 Contoso Concert Hall 脱机，以防止继续更新。 然后，通过从还原点进行恢复来创建一个并行数据库。  还原后的数据库以时间戳命名，确保该数据库名称不与现有租户数据库名称冲突。 接着，删除旧租户数据库，并将还原的数据库重命名为原始数据库名称。 最后，将 Contoso Concert Hall 联机，使应用能够访问还原的数据库。

已成功将数据库还原到删除事件之前的某个时间点。 “事件”页面将打开，确认最后一个事件已还原。

请注意，还原数据库后，需要再稍等 10-15 分钟，才可再次通过第一个完整备份进行还原。 

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]

> * 将数据库还原为并行数据库（并行）
> * 就地还原数据库

[管理租户数据库架构](saas-tenancy-schema-management.md)

## <a name="additional-resources"></a>其他资源

* [其他基于 Wingtip SaaS 应用程序编写的教程](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [使用 Azure SQL 数据库确保业务连续性的相关概述](sql-database-business-continuity.md)
* [了解 SQL 数据库备份](sql-database-automated-backups.md)
