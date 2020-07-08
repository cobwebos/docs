---
title: 跟踪 Azure SQL Edge 中的数据更改（预览）
description: 了解 Azure SQL Edge （预览版）中的更改跟踪和更改数据捕获。
keywords: ''
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2020
ms.openlocfilehash: 6d0a081f2b0adb143a6b37a647a00014846f8fe2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84669590"
---
# <a name="track-data-changes-in-azure-sql-edge-preview"></a>跟踪 Azure SQL Edge 中的数据更改（预览）

Azure SQL Edge 支持两个 SQL Server 功能，这些功能跟踪数据库中的数据更改：[更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Tracking)和[更改数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#Capture)。 这些功能使应用程序能够确定对数据库中的用户表所做的数据修改语言更改（插入、更新和删除操作）。 您可以对同一数据库启用变更数据捕获和更改跟踪。 没有特殊的注意事项。

对于某些注重效能的应用程序来说，查询数据库中已更改的数据的能力是一项很重要的要求。 通常，为了确定数据更改，应用程序开发人员必须在其应用程序中使用触发器、时间戳列和其他表的组合来实现自定义跟踪方法。 创建这些应用程序通常涉及多项工作，导致架构更新，并且通常带来较高的性能开销。

对于 IoT 解决方案，需要定期将数据从边缘移动到云或数据中心，更改跟踪可能非常有用。 用户可以快速有效地仅查询上次同步的更改，并将这些更改上传到云或数据中心目标。 有关更多详细信息，请参阅[使用变更数据捕获或更改跟踪的好处](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#benefits-of-using-change-data-capture-or-change-tracking)。 

这两种功能不同。 有关详细信息，请参阅[变更数据捕获与更改跟踪之间的功能差异](https://docs.microsoft.com/sql/relational-databases/track-changes/track-data-changes-sql-server#feature-differences-between-change-data-capture-and-change-tracking)

## <a name="change-data-capture"></a>更改数据捕获

若要了解有关此功能的工作原理的详细信息，请参阅[关于变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-data-capture-sql-server)。

若要了解如何启用或禁用此功能，请参阅[启用和禁用变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server)。

若要管理和监视此功能，请参阅[管理和监视变更数据捕获](https://docs.microsoft.com/sql/relational-databases/track-changes/administer-and-monitor-change-data-capture-sql-server)。

若要了解如何查询和使用已更改的数据，请参阅[使用变更数据](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-data-sql-server)。

## <a name="change-tracking"></a>Change tracking

若要了解有关此功能的工作原理的详细信息，请参阅[关于更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/about-change-tracking-sql-server)。

若要了解如何启用或禁用此功能，请参阅[启用和禁用更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server)。

若要管理、监视和管理此功能，请参阅[管理和监视更改跟踪](https://docs.microsoft.com/sql/relational-databases/track-changes/manage-change-tracking-sql-server)。

若要了解如何查询和使用已更改的数据，请参阅[使用变更数据](https://docs.microsoft.com/sql/relational-databases/track-changes/work-with-change-tracking-sql-server)。

## <a name="temporal-tables"></a>临时表

Azure SQL Edge 还支持 SQL Server 的时态表功能。 此功能（也称为*经系统版本控制的临时表*）为提供有关在任何时间点中存储的数据的信息提供内置支持。 此功能不只是提供有关在当前时刻正确的数据的信息。

经系统版本控制的临时表是一种用户表，旨在保留数据更改的完整历史记录，并允许轻松分析时间点。 这种类型的时态表称为系统版本的临时表，因为每一行的有效期由系统（即数据库引擎）管理。

每个临时表有两个显式定义的列，每个列都有一个 `datetime2` 数据类型。 这些列称为*时间段*列。 系统每次修改行时，系统将以独占方式使用这些期限列来记录每行的有效期。

除了这些期限列以外，临时表还包含对使用镜像架构的另一个表的引用。 每当更新或删除了临时表中的某行后，系统将使用此表来自动存储该行的先前版本。 此附加表称为*历史记录*表，而存储当前（实际）行版本的主表称为*当前*表，或只是临时表。 创建临时表期间，用户可以指定现有的历史记录表（它必须符合架构），或者让系统创建默认的历史记录表。

有关详细信息，请参阅[临时表](https://docs.microsoft.com/sql/relational-databases/tables/temporal-tables)。

## <a name="next-steps"></a>后续步骤

- [Azure SQL Edge 中的数据流（预览版）](stream-data.md)
- [在 Azure SQL Edge 中（预览版）使用 ONNX 实现机器学习和 AI ](onnx-overview.md)
- [配置复制到 Azure SQL Edge （预览版）](configure-replication.md)
- [Azure SQL Edge 中的备份和还原数据库（预览版）](backup-restore.md)



