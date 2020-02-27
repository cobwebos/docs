---
title: 联机迁移到 Azure SQL 数据库托管实例的已知问题和限制
description: 了解与 Azure SQL 数据库托管实例的联机迁移相关的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: article
ms.date: 02/20/2020
ms.openlocfilehash: 88e2b5894686ee93caecf33e04940803eb75f394
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77648659"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-database-managed-instance"></a>对 Azure SQL 数据库托管实例的联机迁移的已知问题/迁移限制

下面介绍了与从 SQL Server 到 Azure SQL 数据库托管实例的联机迁移相关的已知问题和限制。

> [!IMPORTANT]
> 通过联机 SQL Server 到 Azure SQL 数据库的迁移，不支持 SQL_variant 数据类型的迁移。

## <a name="backup-requirements"></a>备份要求

- **校验和备份**

    Azure 数据库迁移服务使用备份和还原方法将本地数据库迁移到 SQL 数据库托管实例。 Azure 数据库迁移服务仅支持使用校验和创建的备份。

    [在备份或还原期间启用或禁用备份校验和 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > 如果使用压缩执行数据库备份，则校验和是默认行为，除非显式禁用。

    使用脱机迁移时，如果选择 "**我将允许 Azure 数据库迁移服务 ...** "，Azure 数据库迁移服务会在启用 checksum 选项的情况下执行数据库备份。

- **备份媒体**

    请确保将每个备份都置于单独的备份介质上（备份文件）。 Azure 数据库迁移服务不支持附加到单个备份文件的备份。 将完整备份和日志备份进行单独的备份文件。

## <a name="data-and-log-file-layout"></a>数据和日志文件布局

- **日志文件数**

    Azure 数据库迁移服务不支持包含多个日志文件的数据库。 如果有多个日志文件，请将它们收缩并重新组织为单个事务日志文件。 由于不能远程记录不为空的文件，因此需要先备份日志文件。

## <a name="sql-server-features"></a>SQL Server 功能

- **FileStream/Filetable**

    SQL 数据库托管实例当前不支持 FileStream 和 Filetable。 对于依赖于这些功能的工作负荷，建议选择将 Azure Vm 上运行的 SQL Server 作为 Azure 目标。

- **内存中表**

    在 SQL 数据库托管实例的高级层和业务关键层中，内存中 OLTP 可用;常规用途层不支持内存中 OLTP。

## <a name="migration-resets"></a>迁移重置

- **部署**

    SQL 数据库托管实例是一种具有自动修补和版本更新的 PaaS 服务。 在 SQL 数据库托管实例的迁移过程中，非关键更新最多可达36小时。 之后（对于关键更新），如果迁移中断，进程将重置为完全还原状态。

    仅在还原完整备份并捕获所有日志备份后，才能调用转换迁移。 如果生产迁移交接受到影响，请联系[AZURE DMS 反馈别名](mailto:dmsfeedback@microsoft.com)。
