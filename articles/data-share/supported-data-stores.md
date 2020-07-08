---
title: Azure 数据共享中支持的数据存储
description: 了解使用 Azure 数据共享支持的数据存储。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: e0daa2b02c16d8d5a65b5e7e0f983a4f47181d40
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "84635962"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure 数据共享中支持的数据存储

Azure 数据共享提供了开放式和灵活的数据共享，包括从和到不同数据存储区的能力。 数据访问接口可以共享一种类型的数据存储中的数据，其数据使用者可以选择要将数据接收到的数据存储区。 

在本文中，你将了解 Azure 数据共享支持的丰富 Azure 数据存储集。 你还可以找到有关数据访问接口和数据使用者可以利用的数据存储组合的信息。 

## <a name="what-data-stores-are-supported-in-azure-data-share"></a>Azure 数据共享支持哪些数据存储？ 

下表详细说明了 Azure 数据共享支持的数据源。 

| 数据存储 | 基于快照的共享 | 就地共享 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 |✓ | |
| Azure Data Lake Storage Gen1 |✓ | |
| Azure Data Lake Storage Gen2 |✓ ||
| Azure SQL Database |公共预览版 | |
| Azure Synapse Analytics （以前称为 Azure SQL DW） |公共预览版 | |
| Azure 数据资源管理器 | |公共预览版 |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

在确定数据存储区以接受中的数据时，Azure 数据共享可为数据使用者提供灵活性。 例如，可以将 Azure SQL 数据库中共享的数据接收到 Azure Data Lake Store Gen2、Azure SQL 数据库或 Azure Synapse Analytics 中。 客户可以在配置接收的数据共享时选择要接收数据的格式。 

下表详细说明了数据使用者在接受和配置其数据共享时具有的不同组合和选择。 有关如何配置数据集映射的详细信息，请参阅[如何配置数据集映射](how-to-configure-mapping.md)。

|  | Azure Blob 存储 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure 数据资源管理器
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics （以前称为 Azure SQL DW） | ✓ | | ✓ | ✓ | ✓ ||
| Azure 数据资源管理器 |||||| ✓ |

## <a name="share-from-a-storage-account"></a>从存储帐户共享
Azure 数据共享支持从 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共享文件、文件夹和文件系统。 它还支持从 Azure Blob 存储共享 blob、文件夹和容器。 目前仅支持块 blob。 在基于快照的共享中共享文件系统、容器或文件夹时，数据使用者可以选择创建共享数据的完整副本，也可以利用增量快照功能只复制新的或更新的文件。 增量快照基于文件的上次修改时间。 将覆盖具有相同名称的现有文件。

## <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
Azure 数据共享支持从 Azure SQL 数据库和 Azure Synapse Analytics （以前称为 Azure SQL DW）共享表或视图。 数据使用者可以选择接受将数据作为 csv 或 parquet 文件 Azure Data Lake Store Gen2 或 Azure Blob 存储。 请注意，默认情况下，文件格式为 csv。 如果需要，数据使用者可以选择接收 parquet 格式的数据。 在接收数据时，可以在数据集映射设置中完成此操作。 

将数据导入 Azure Data Lake Store Gen2 或 Azure Blob 存储时，完全快照将覆盖目标文件的内容。 

数据使用者可以选择将数据接收到其选择的表中。 在这种情况下，如果目标表尚不存在，Azure 数据共享将创建包含源架构的 SQL 表。 如果已存在具有相同名称的目标表，则将删除该目标表并使用最新的完整快照覆盖它。 映射目标表时，可以指定其他架构和表名。 当前不支持增量快照。 

从基于 SQL 的源共享与防火墙规则和权限相关的先决条件。 有关详细信息，请参阅[共享数据](share-your-data.md)教程中的先决条件部分。

## <a name="share-from-azure-data-explorer"></a>从 Azure 数据资源管理器进行共享
Azure 数据共享支持从 Azure 数据资源管理器群集共享就地数据库。 数据访问接口可以在数据库级别或群集级别共享。 在数据库级别共享时，数据使用者将只能访问数据提供程序所共享的特定数据库。 当在群集级别共享时，数据使用者可以从提供程序的群集访问所有数据库，包括任何将来由数据提供程序创建的数据库。

若要访问共享数据库，数据使用者需要有自己的 Azure 数据资源管理器群集。 数据使用者的 Azure 数据资源管理器群集需要在与数据提供商的 Azure 数据资源管理器群集相同的 Azure 数据中心中查找。 建立共享关系时，Azure 数据共享会在提供者和使用者的 Azure 数据资源管理器群集之间创建一个符号链接。

Azure 数据资源管理器支持两种数据引入模式：批处理和流式处理。 在共享数据库中从批处理接收的数据将在几秒钟到几分钟之间显示在数据使用者端。 从流式传输收到的数据可能需要长达24小时才会显示在数据使用者端。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
