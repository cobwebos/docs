---
title: 将数据移到 Azure SQL 数据库 - Team Data Science Process
description: 将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 中存储的数据移到 Azure SQL 数据库。
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
ms.openlocfilehash: f9a1424f2afe6c5153e208601b21dff9651880a8
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76722452"
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>将数据转移到 Azure SQL 数据库以便在 Azure 机器学习中处理

本文概述了用于将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 中存储的数据移到 Azure SQL 数据库的选项。 将数据移到云中的这些任务是“团队数据科学流程”的一部分。

有关概述将数据移动到本地 SQL Server 进行机器学习的选项的主题，请参阅[将数据移到 Azure 虚拟机上的 SQL Server](move-sql-server-virtual-machine.md)。

下表汇总了用于将数据移到 Azure SQL 数据库的选项。

| <b>源</b> | <b>目标：Azure SQL 数据库</b> |
| --- | --- |
| <b>平面文件（CSV 或 TSV 格式）</b> |[批量插入 SQL 查询](#bulk-insert-sql-query) |
| <b>本地 SQL Server</b> |1. [导出到平面文件](#export-flat-file)<br> 2. [SQL 数据库迁移向导](#insert-tables-bcp)<br> 3.[数据库备份和还原](#db-migration)<br> 4. [Azure 数据工厂](#adf) |

## <a name="prereqs"></a>先决条件
此处所述的过程要求具有：

* 一个 **Azure 订阅**。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户**。 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-account-create.md)一文。 创建存储帐户后，需要获取用于访问存储的帐户密钥。 请参阅[管理存储帐户访问密钥](../../storage/common/storage-account-keys-manage.md)。
* 访问 **Azure SQL 数据库**。 如果必须设置 Azure SQL 数据库，可在 [Microsoft Azure SQL 数据库入门](../../sql-database/sql-database-get-started.md)中找到相关信息，了解如何设置 Azure SQL 数据库的新实例。
* 已在本地安装和配置 **Azure PowerShell**。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

**数据**：使用 [NYC 出租车数据集](https://chriswhong.com/open-data/foil_nyc_taxi/)演示迁移过程。 NYC 出租车数据集包含行程数据和费用的相关信息，且可用于 Azure Blob 存储：[NYC 出租车数据](https://www.andresmh.com/nyctaxitrips/)。 [NYC 出租车行程数据集说明](sql-walkthrough.md#dataset)中介绍了这些文件的示例和说明。

可将此处描述的流程调整为一组自己的数据，或者通过使用 NYC 出租车数据集来按照所述的步骤进行操作。 若要将 NYC 出租车数据集上传到本地 SQL Server 数据库，请按照[将数据批量导入 SQL Server 数据库](sql-walkthrough.md#dbload)中概述的过程进行操作。 这些说明适用于 Azure 虚拟机上的 SQL Server，但是上传到本地 SQL Server 的过程是相同的。

## <a name="file-to-azure-sql-database"></a>将数据从平面文件源移动到 Azure SQL 数据库
可以使用大容量插入 SQL 查询将平面文件（CSV 或 TSV 格式）中的数据移动到 Azure SQL 数据库。

### <a name="bulk-insert-sql-query"></a>批量插入 SQL 查询
使用大容量插入 SQL 查询过程的步骤类似于将数据从平面文件源移动到 Azure VM SQL Server。 有关详细信息，请参阅[批量插入 SQL 查询](move-sql-server-virtual-machine.md#insert-tables-bulkquery)。

## <a name="sql-on-prem-to-sazure-sql-database"></a>将数据从本地 SQL Server 移到 Azure SQL 数据库
如果源数据存储在本地 SQL Server 中，则有多种可能将数据移动到 Azure SQL 数据库：

1. [导出到平面文件](#export-flat-file)
2. [SQL 数据库迁移向导](#insert-tables-bcp)
3. [数据库备份和还原](#db-migration)
4. [Azure 数据工厂](#adf)

前三个步骤的步骤类似于[将数据移到 Azure 虚拟机上 SQL Server](move-sql-server-virtual-machine.md)的那些部分涵盖了这些相同的过程。 以下说明提供了该主题中相应部分的链接。

### <a name="export-flat-file"></a>导出到平面文件
导出到平面文件的步骤类似于[导出到平面文件](move-sql-server-virtual-machine.md#export-flat-file)中所述的步骤。

### <a name="insert-tables-bcp"></a>SQL 数据库迁移向导
使用 SQL 数据库迁移向导的步骤类似于[Sql 数据库迁移向导](move-sql-server-virtual-machine.md#sql-migration)中所述的说明。

### <a name="db-migration"></a>数据库备份和还原
使用数据库备份和还原的步骤类似于 "[数据库备份和还原](move-sql-server-virtual-machine.md#sql-backup)" 中列出的那些指导。

### <a name="adf"></a>Azure 数据工厂
了解如何使用 Azure 数据工厂（ADF）将数据移至 Azure SQL 数据库。在本主题中，使用[Azure 数据工厂将数据从本地 SQL Server 移动到 SQL Azure](move-sql-azure-adf.md)。 本主题演示如何使用 ADF 通过 Azure Blob 存储将数据从本地 SQL Server 数据库移到 Azure SQL 数据库。

如果需要使用混合本地和云源继续迁移数据，请考虑使用 ADF。  ADF 还有助于在迁移过程中需要转换数据或需要新的业务逻辑。 ADF 允许使用简单的 JSON 脚本计划和监视作业，JSON 脚本可定期管理数据移动。 ADF 还具有其他功能，例如支持复杂操作。
