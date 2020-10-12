---
title: 将数据移到 Azure SQL 数据库 - Team Data Science Process
description: 将 (CSV 或 TSV 格式的平面文件中的数据移动) 或从 SQL Server 中存储的数据移到 Azure SQL 数据库。
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 1f1120c78ef2a634c079705c85170d9742eabd1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87087418"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>将数据转移到 Azure SQL 数据库以便在 Azure 机器学习中处理

本文概述了用于将平面文件（CSV 或 TSV 格式）中的数据或 SQL Server 中存储的数据移到 Azure SQL 数据库的选项。 将数据移到云中的这些任务是“团队数据科学流程”的一部分。

如果需要通过某个主题来大致了解将数据移到 SQL Server 以便进行机器学习的选项，请参阅[将数据移到 Azure 虚拟机上的 SQL Server](move-sql-server-virtual-machine.md)。

下表汇总了用于将数据移到 Azure SQL 数据库的选项。

| <b>源</b> | <b>目标：Azure SQL 数据库</b> |
| --- | --- |
| <b>平面文件（CSV 或 TSV 格式）</b> |[批量插入 SQL 查询](#bulk-insert-sql-query) |
| <b>本地 SQL Server</b> |1. [导出到平面文件](#export-flat-file)<br> 2.[SQL 数据库迁移向导](#insert-tables-bcp)<br> 3.[数据库备份和还原](#db-migration)<br> 4.[Azure 数据工厂](#adf) |

## <a name="prerequisites"></a><a name="prereqs"></a>先决条件
此处所述的过程要求具有：

* 一个 **Azure 订阅**。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户**。 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)一文。 创建存储帐户后，需要获取用于访问存储的帐户密钥。 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。
* 访问 **Azure SQL 数据库**。 如果必须设置 Azure SQL 数据库，可在 [Microsoft Azure SQL 数据库入门](../../sql-database/sql-database-get-started.md)中找到相关信息，了解如何设置 Azure SQL 数据库的新实例。
* 已在本地安装和配置 **Azure PowerShell**。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/)。

**Data**：使用 [NYC 出租车数据集](https://chriswhong.com/open-data/foil_nyc_taxi/)演示迁移过程。 NYC 出租车数据集包含行程数据和费用的相关信息，且可用于 Azure Blob 存储：[NYC 出租车数据](https://www.andresmh.com/nyctaxitrips/)。 [NYC 出租车行程数据集说明](sql-walkthrough.md#dataset)中介绍了这些文件的示例和说明。

可将此处描述的流程调整为一组自己的数据，或者通过使用 NYC 出租车数据集来按照所述的步骤进行操作。 若要将 NYC 出租车数据集上传到 SQL Server 数据库中，请按照[将数据批量导入 SQL Server 数据库](sql-walkthrough.md#dbload)中概述的过程进行操作。

## <a name="moving-data-from-a-flat-file-source-to-an-azure-sql-database"></a><a name="file-to-azure-sql-database"></a> 将数据从平面文件源移动到 Azure SQL 数据库
可以使用大容量插入 SQL 查询将平面文件中的数据 (CSV 或 TSV 格式的) 移到 Azure SQL 数据库。

### <a name="bulk-insert-sql-query"></a><a name="bulk-insert-sql-query"></a>批量插入 SQL 查询
批量插入 SQL 查询使用过程的步骤类似于将数据从平面文件源移到 Azure VM 上的 SQL Server 的说明。 有关详细信息，请参阅[批量插入 SQL 查询](move-sql-server-virtual-machine.md#insert-tables-bulkquery)。

## <a name="moving-data-from-sql-server-to-an-azure-sql-database"></a><a name="sql-on-prem-to-sazure-sql-database"></a> 将数据从 SQL Server 移到 Azure SQL 数据库
如果源数据存储在 SQL Server 中，则有多种方式可将数据移到 Azure SQL 数据库：

1. [导出到平面文件](#export-flat-file)
2. [SQL 数据库迁移向导](#insert-tables-bcp)
3. [数据库备份和还原](#db-migration)
4. [Azure 数据工厂](#adf)

前三种方式的步骤非常类似于[将数据移到 Azure 虚拟机上的 SQL Server](move-sql-server-virtual-machine.md) 中涵盖相同过程的部分中所述的步骤。 以下说明提供了该主题中相应部分的链接。

### <a name="export-to-flat-file"></a><a name="export-flat-file"></a>导出到平面文件
这个导出到平面文件的操作的步骤类似于[导出到平面文件](move-sql-server-virtual-machine.md#export-flat-file)中所述的这些说明。

### <a name="sql-database-migration-wizard"></a><a name="insert-tables-bcp"></a>SQL 数据库迁移向导
使用 SQL 数据库迁移向导的步骤类似于 [SQL 数据库迁移向导](move-sql-server-virtual-machine.md#sql-migration)中所述的这些说明。

### <a name="database-back-up-and-restore"></a><a name="db-migration"></a>数据库备份和还原
使用数据库备份和还原的步骤类似于[数据库备份和还原](move-sql-server-virtual-machine.md#sql-backup)中列出的这些说明。

### <a name="azure-data-factory"></a><a name="adf"></a>Azure 数据工厂
若要了解如何使用 Azure 数据工厂 (ADF) 将数据移到 Azure SQL 数据库，请参阅此主题：[使用 Azure 数据工厂将数据从 SQL Server 移到 SQL Azure](move-sql-azure-adf.md)。 本主题演示如何使用 ADF 通过 Azure Blob 存储将数据从 SQL Server 数据库移到 Azure SQL 数据库。

如果需要使用本地和云混合源持续迁移数据，请考虑使用 ADF。  如果在迁移过程中数据需要转换或需要新的业务逻辑，ADF 也可以提供帮助。 ADF 允许使用简单的 JSON 脚本计划和监视作业，JSON 脚本可定期管理数据移动。 ADF 还具有其他功能，例如支持复杂操作。
