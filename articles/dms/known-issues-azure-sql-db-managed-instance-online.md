---
title: 联机迁移到 Azure SQL 托管实例时存在的已知问题和限制
description: 了解联机迁移到 Azure SQL 托管实例时存在的已知问题/迁移限制。
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc
ms.topic: troubleshooting
ms.date: 02/20/2020
ms.openlocfilehash: 6393624cb4520ccd28dff41dd97746873bc13f59
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91330292"
---
# <a name="known-issuesmigration-limitations-with-online-migrations-to-azure-sql-managed-instance"></a>联机迁移到 Azure SQL 托管实例时存在的已知问题/迁移限制

下面介绍了从 SQL Server 联机迁移到 Azure SQL 托管实例时的已知问题和限制。

> [!IMPORTANT]
> 将 SQL Server 联机迁移到 Azure SQL 数据库时，不支持迁移 SQL_variant 数据类型。

## <a name="backup-requirements"></a>备份要求

- **使用校验和的备份**

    Azure 数据库迁移服务使用备份和还原方法将本地数据库迁移到 SQL 托管实例。 Azure 数据库迁移服务仅支持使用校验和创建的备份。

    [在备份或还原期间启用或禁用备份校验和 (SQL Server)](https://docs.microsoft.com/sql/relational-databases/backup-restore/enable-or-disable-backup-checksums-during-backup-or-restore-sql-server?view=sql-server-2017)

    > [!NOTE]
    > 如果通过压缩进行数据库备份，则除非明确禁用，否则校验和是默认行为。

    脱机迁移时，如果选择“我将让 Azure 数据库迁移服务...”，则 Azure 数据库迁移服务进行数据库备份时会启用校验和选项。

- **备份介质**

    确保将每个备份都置于单独的备份介质（备份文件）上。 Azure 数据库迁移服务不支持追加到单个备份文件的备份。 将完整备份和日志备份置于单独的备份文件中。

## <a name="data-and-log-file-layout"></a>数据和日志文件布局

- **日志文件数**

    Azure 数据库迁移服务不支持包含多个日志文件的数据库。 如果有多个日志文件，请将其收缩并重组到单个事务日志文件中。 由于无法远程操作非空日志文件，因此需要先备份日志文件。

## <a name="sql-server-features"></a>SQL Server 功能

- **FileStream/Filetable**

    SQL 托管实例当前不支持 FileStream 和 Filetable。 对于依赖这些功能的工作负载，建议选择 Azure VM 上运行的 SQL Server 作为 Azure 目标。

- **内存中表**

    内存中 OLTP 在 SQL 托管实例高级层和业务关键层中可用；常规用途层不支持内存中 OLTP。

## <a name="migration-resets"></a>迁移重置

- **部署**

    SQL 托管实例是一种具有自动修补和版本更新功能的 PaaS 服务。 迁移 SQL 托管实例期间，非关键更新最多保留 36 小时。 之后（对于关键更新），如果迁移中断，此过程将重置为完全还原状态。

    仅在完整备份还原完成且与所有日志备份一致后才能调用迁移切换。 如果生产迁移交接受到影响，请联系 [AZURE DMS 反馈别名](mailto:dmsfeedback@microsoft.com)。
