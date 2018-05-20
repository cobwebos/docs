---
title: 将数据移动到 Azure SQL 数据库以便在 Azure 机器学习中处理 | Microsoft Docs
description: 创建 SQL 表并将数据加载到 SQL 表
services: machine-learning
documentationcenter: ''
author: deguhath
manager: jhubbard
editor: cgronlun
ms.assetid: 50f8b862-4d32-44b2-a1e2-4fbc8024acaa
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/04/2018
ms.author: deguhath
ms.openlocfilehash: ce349aedc6b733d34ab61eb2e23b378727e01800
ms.sourcegitcommit: 909469bf17211be40ea24a981c3e0331ea182996
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2018
---
# <a name="move-data-to-an-azure-sql-database-for-azure-machine-learning"></a>将数据转移到 Azure SQL 数据库以便在 Azure 机器学习中处理
本主题概述了用于将数据从平面文件（CSV 或 TSV 格式）或从本地 SQL Server 中存储的数据移到 Azure SQL 数据库的选项。 将数据移到云中的这些任务是“团队数据科学流程”的一部分。

有关概述将数据移动到本地 SQL Server 进行机器学习的选项的主题，请参阅[将数据移到 Azure 虚拟机上的 SQL Server](move-sql-server-virtual-machine.md)。

下面的**菜单**将链接至一个主题，该主题介绍如何将数据引入到在 Team Data Science Process (TDSP) 期间可存储并处理数据的目标环境中。

[!INCLUDE [cap-ingest-data-selector](../../../includes/cap-ingest-data-selector.md)]

下表汇总了用于将数据移到 Azure SQL 数据库的选项。

| <b>源</b> | <b>目标：Azure SQL 数据库</b> |
| --- | --- |
| <b>平面文件（CSV 或 TSV 格式）</b> |[批量插入 SQL 查询](#bulk-insert-sql-query) |
| <b>本地 SQL Server</b> |1. [导出到平面文件](#export-flat-file)<br> 2.[SQL 数据库迁移向导](#insert-tables-bcp)<br> 3.[数据库备份和还原](#db-migration)<br> 4.[Azure 数据工厂](#adf) |

## <a name="prereqs"></a>先决条件
此处所述的过程要求具有：

* 一个 **Azure 订阅**。 如果尚无订阅，可注册[免费试用版](https://azure.microsoft.com/pricing/free-trial/)。
* 一个 **Azure 存储帐户**。 在本教程中，将使用 Azure 存储帐户存储数据。 如果还没有 Azure 存储帐户，请参阅[创建存储帐户](../../storage/common/storage-create-storage-account.md#create-a-storage-account)一文。 创建存储帐户后，需要获取用于访问存储的帐户密钥。 请参阅[管理存储访问密钥](../../storage/common/storage-create-storage-account.md#manage-your-storage-access-keys)。
* 访问 **Azure SQL 数据库**。 如果必须设置 Azure SQL 数据库，可在 [Microsoft Azure SQL 数据库入门](../../sql-database/sql-database-get-started.md)中找到相关信息，了解如何设置 Azure SQL 数据库的新实例。
* 已在本地安装和配置 **Azure PowerShell**。 有关说明，请参阅[如何安装和配置 Azure PowerShell](/powershell/azure/overview)。

**数据**：使用 [NYC 出租车数据集](http://chriswhong.com/open-data/foil_nyc_taxi/)演示迁移过程。 NYC 出租车数据集包含行程数据和费用的相关信息，且可用于 Azure Blob 存储：[NYC 出租车数据](http://www.andresmh.com/nyctaxitrips/)。 [NYC 出租车行程数据集说明](sql-walkthrough.md#dataset)中介绍了这些文件的示例和说明。

可将此处描述的流程调整为一组自己的数据，或者通过使用 NYC 出租车数据集来按照所述的步骤进行操作。 若要将 NYC 出租车数据集上传到本地 SQL Server 数据库，请按照[将数据批量导入 SQL Server 数据库](sql-walkthrough.md#dbload)中概述的过程进行操作。 这些说明适用于 Azure 虚拟机上的 SQL Server，但是上传到本地 SQL Server 的过程是相同的。

## <a name="file-to-azure-sql-database"></a>将数据从平面文件源移动到 Azure SQL 数据库
可以使用批量插入 SQL 查询将平面文件（CSV 或 TSV 格式）中的数据移动到 Azure SQL 数据库。

### <a name="bulk-insert-sql-query"></a>批量插入 SQL 查询
使用批量插入 SQL 查询过程的步骤类似于将数据从平面文件源移到 Azure VM 上的 SQL Server 部分中所述的步骤。 有关详细信息，请参阅[批量插入 SQL 查询](move-sql-server-virtual-machine.md#insert-tables-bulkquery)。

## <a name="sql-on-prem-to-sazure-sql-database"></a>将数据从本地 SQL Server 移动到 Azure SQL 数据库
如果源数据存储在本地 SQL Server 中，则有多种方式可将数据移动到 Azure SQL 数据库：

1. [导出到平面文件](#export-flat-file)
2. [SQL 数据库迁移向导](#insert-tables-bcp)
3. [数据库备份和还原](#db-migration)
4. [Azure 数据工厂](#adf)

前三种方式的步骤非常类似于[将数据移动到 Azure 虚拟机上的 SQL Server](move-sql-server-virtual-machine.md) 中涵盖相同过程的部分中所述的步骤。 以下说明提供了该主题中相应部分的链接。

### <a name="export-flat-file"></a>导出到平面文件
导出到平面文件的此步骤类似于[导出到平面文件](move-sql-server-virtual-machine.md#export-flat-file)中所述的步骤。

### <a name="insert-tables-bcp"></a>SQL 数据库迁移向导
使用 SQL 数据库迁移向导的步骤类似于 [SQL 数据库迁移向导](move-sql-server-virtual-machine.md#sql-migration)中所述的步骤。

### <a name="db-migration"></a>数据库备份和还原
使用数据库备份和恢复的步骤类似于[数据库备份和恢复](move-sql-server-virtual-machine.md#sql-backup)中所述的步骤。

### <a name="adf"></a>Azure 数据工厂
有关使用 Azure 数据工厂 (ADF) 将数据移动到 Azure SQL 数据库的过程，可参阅[使用 Azure 数据工厂将数据从本地 SQL 服务器移动到 SQL Azure](move-sql-azure-adf.md) 主题。 本主题演示如何使用 ADF，通过 Azure Blob 存储将数据从本地 SQL Server 数据库移动到 Azure SQL 数据库。

请在以下情况考虑使用 ADF：在可访问本地和云资源的混合方案中，需要连续迁移数据；需要处理或修改数据；或者已在迁移期间添加业务逻辑。 ADF 允许使用简单的 JSON 脚本计划和监视作业，JSON 脚本可定期管理数据移动。 ADF 还具有其他功能，例如支持复杂操作。
