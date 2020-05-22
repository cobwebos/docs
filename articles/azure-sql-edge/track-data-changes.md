---
title: 在 Azure SQL Edge（预览版）中跟踪数据更改
description: 了解 Azure SQL Edge（预览版）中的更改跟踪和变更数据捕获
keywords: ''
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: a91ed5d4763aa521c9f6ed913dc532b08e37039f
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594446"
---
# <a name="tracking-data-changes-in-azure-sql-edge-preview"></a>在 Azure SQL Edge（预览版）中跟踪数据更改

Azure SQL Edge 支持两个 SQL Server 功能，这些功能可跟踪数据库中的数据更改：[更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking)和[变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)。 这两个功能使应用程序能够确定对数据库中的用户表所做的 DML 更改（插入、更新和删除操作）。 可对同一个数据库启用变更数据捕获和更改跟踪；没有特殊的注意事项。

对于某些注重效能的应用程序来说，查询数据库中已更改的数据的能力是一项很重要的要求。 通常，为了确定数据更改，应用程序开发人员必须在其应用程序中使用触发器、时间戳列和其他表的组合来实现自定义跟踪方法。 创建这些应用程序通常涉及多项工作，导致架构更新，并且通常带来较高的性能开销。 对于需要定期将数据从边缘移动到云或数据中心的 IoT 解决方案，更改跟踪可能非常有用。 这样，就可以快速有效地查询自上次同步以来的增量更改，并将这些更改上传到云或数据中心目标。 要详细了解使用更改跟踪和变更数据捕获的好处，请参阅[使用变更数据捕获或更改跟踪的优势](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)。 

要了解更改跟踪与变更数据捕获之间的功能差异，请参阅[变更数据捕获与更改跟踪之间的功能差异](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>更改数据捕获

若要了解变更数据捕获的工作原理的详细信息，请参阅[关于变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

若要了解如何启用或禁用变更数据捕获，请参阅[启用和禁用变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)

若要管理和监视变更数据捕获，请参阅[管理和监视变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)

若要了解如何查询和处理更改后的数据，请参阅[处理变更数据](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)

## <a name="change-tracking"></a>更改跟踪

若要详细了解更改跟踪的工作原理，请参阅[关于更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)。

若要了解如何启用或禁用更改跟踪，请参阅[启用和禁用更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)

若要管理、监视和管理更改跟踪，请参阅[管理和监视更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)

若要了解如何查询和处理更改后的数据，请参阅[处理变更数据](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)

## <a name="temporal-tables"></a>临时表

除了支持 SQL Server 的更改跟踪和变更数据捕获功能以外，Azure SQL Edge 还支持 SQL Server 的时态表功能。 时态表（也称为版本由系统控制的时态表）以数据库功能的形式引入了内置支持，可以提供表中存储的数据在任意时间点的相关信息，而不仅仅是数据在当前时刻正确的信息。

版本由系统控制的临时表是用户表的一种类型，旨在保留完整的数据更改历史记录，并实现轻松的时间点分析。 这种类型的时态表之所以称为版本由系统控制的时态表，是因为每一行的有效期由系统（即数据库引擎）管理。

每个时态表有两个显式定义的列，其中每个列都有一个 datetime2 数据类型。 这些列称为期限列。 每当修改了某行后，系统将以独占方式使用这些期限列来记录每行的有效期。

除了这些期限列以外，临时表还包含对使用镜像架构的另一个表的引用。 每当更新或删除了临时表中的某行后，系统将使用此表来自动存储该行的先前版本。 此附加表称为历史记录表，而存储当前（实际）行版本的主表称为当前表，或直接称为临时表。 在创建临时表期间，用户可以指定现有的历史记录表（必须与架构相符），或者让系统创建默认的历史记录表。

有关时态表的详细信息，请参阅[时态表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)

## <a name="see-also"></a>另请参阅

- [Azure SQL Edge（预览版）中的数据流式处理 ](stream-data.md)
- [在 Azure SQL Edge 中（预览版）使用 ONNX 实现机器学习和 AI ](onnx-overview.md)
- [配置到 Azure SQL Edge（预览版）的复制](configure-replication.md)
- [在 Azure SQL Edge（预览版）中备份和还原数据库](backup-restore.md)



