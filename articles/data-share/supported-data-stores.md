---
title: Azure 数据共享中受支持的数据存储
description: 了解支持使用 Azure 数据共享的数据存储。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 624bb45de3e2ff184326949611d437f71f3e2def
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501812"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 数据共享中受支持的数据存储

Azure 数据共享提供开放和灵活的数据共享，包括从不同数据存储共享和共享的能力。 数据提供程序可以共享来自一种类型的数据存储的数据，并且其数据使用者可以选择接收数据到哪个数据存储。 

在本文中，您将了解 Azure 数据共享中支持的丰富的 Azure 数据存储集。 您还可以找到数据提供商和数据使用者可以利用的数据存储组合的信息。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 数据共享中支持哪些数据存储？ 

下表详细介绍了 Azure 数据共享的支持数据源。 

| 数据存储 | 基于快照的共享 | 就地共享 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL 数据库 |公共预览版 | |
| Azure 突触分析（以前的 Azure SQL DW） |公共预览版 | |
| Azure 数据资源管理器 | |公共预览版 |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

Azure 数据共享在决定使用数据存储以接受 中的数据时，为数据使用者提供了灵活性。 例如，可以从 Azure SQL 数据库共享的数据可以接收到 Azure 数据存储第 2 代、Azure SQL 数据库或 Azure 突触分析中。 客户可以选择在配置接收的数据共享时接收数据的格式。 

下表详细介绍了数据使用者在接受和配置其数据共享时的不同组合和选择。 有关如何配置数据集映射的详细信息，请参阅[如何配置数据集映射](how-to-configure-mapping.md)。

|  | Azure Blob 存储 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL 数据库 | Azure Synapse Analytics 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 | ✓ || ✓|
| Azure Data Lake Storage Gen1 | ✓ | | ✓|
| Azure Data Lake Storage Gen2 | ✓ | | ✓|
| Azure SQL 数据库 | ✓ | | ✓| ✓| ✓|
| Azure 突触分析（以前的 Azure SQL DW） | ✓ | | ✓| ✓| ✓|

## <a name="share-from-a-storage-account"></a>从存储帐户共享
Azure 数据共享支持共享来自 Azure 数据湖第 1 代和 Azure 数据湖 Gen2 的文件、文件夹和文件系统。 它还支持共享 Azure Blob 存储中的 Blob、文件夹和容器。 当前仅支持块 Blob。 当文件夹在基于快照的共享中共享时，数据使用者可以选择创建共享数据的完整副本，或者利用增量快照功能仅复制新文件或更新的文件。 同名的现有文件将被覆盖。

## <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
Azure 数据共享支持共享来自 Azure SQL 数据库和 Azure 突触分析（以前的 Azure SQL DW）的表或视图。 数据使用者可以选择将数据作为 csv 或镶木地板文件接受数据到 Azure 数据湖存储 Gen2 或 Azure Blob 存储中。 请注意，默认情况下，文件格式为 csv。 如果需要，数据使用者可以选择以镶木地板格式接收数据。 这可以在接收数据时在数据集映射设置中完成。 

当接受数据到 Azure 数据湖存储 Gen2 或 Azure Blob 存储时，完整快照将覆盖目标文件的内容。 

数据使用者可以选择将数据接收到自己选择的表中。 在这种情况下，如果目标表不存在，Azure 数据共享将创建具有源架构的 SQL 表。 如果目标表已存在同名，则将删除该表，并覆盖最新的完整快照。 映射目标表时，可以指定替代架构和表名称。 当前不支持增量快照。 

从基于 SQL 的源共享具有与防火墙规则和权限相关的先决条件。 有关详细信息，请参阅[共享数据](share-your-data.md)教程的先决条件部分。

## <a name="share-from-azure-data-explorer"></a>从 Azure 数据资源管理器进行共享
Azure 数据共享支持从 Azure 数据资源管理器群集就地共享数据库的功能。 数据提供程序可以在数据库或群集级别共享。 在数据库级别共享时，数据使用者将只能访问数据提供程序共享的特定数据库。 在群集级别共享时，数据使用者可以从提供程序的群集访问所有数据库，包括数据提供程序创建的任何将来数据库。

要访问共享数据库，数据使用者需要有自己的 Azure 数据资源管理器群集。 数据使用者的 Azure 数据资源管理器群集需要定位在与数据提供程序的 Azure 数据资源管理器群集相同的 Azure 数据中心中。 建立共享关系时，Azure 数据共享会在提供程序和消费者的 Azure 数据资源管理器群集之间创建符号链接。

Azure 数据资源管理器支持两种数据引入模式：批处理和流式处理。 从共享数据库中的批处理接收的数据将在数据使用者端出现几秒钟到几分钟之间。 从流中接收的数据最多可能需要 24 小时才能显示在数据使用者端。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
