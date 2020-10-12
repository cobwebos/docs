---
title: Azure Data Share 中支持的数据存储
description: 了解使用 Azure 数据共享支持的数据存储。
ms.service: data-share
author: jifems
ms.author: jife
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: bb8b13e1141a8cb4610e15ed693e28042dd20d72
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "89259007"
---
# <a name="supported-data-stores-in-azure-data-share"></a>Azure Data Share 中支持的数据存储

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
| Azure Synapse Analytics (以前的 Azure SQL DW)  |公共预览版 | |
| Azure 数据资源管理器 | |✓ |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

在确定数据存储区以接受中的数据时，Azure 数据共享可为数据使用者提供灵活性。 例如，可以将 Azure SQL 数据库中共享的数据接收到 Azure Data Lake Store Gen2、Azure SQL 数据库或 Azure Synapse Analytics 中。 客户可以在配置接收的数据共享时选择要接收数据的格式。 

下表详细说明了数据使用者在接受和配置其数据共享时具有的不同组合和选择。 有关如何配置数据集映射的详细信息，请参阅 [如何配置数据集映射](how-to-configure-mapping.md)。

| 数据存储 | Azure Blob 存储 | Azure Data Lake Storage Gen1 | Azure Data Lake Storage Gen2 | Azure SQL Database | Azure Synapse Analytics | Azure 数据资源管理器
|:--- |:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 | ✓ || ✓ ||
| Azure Data Lake Storage Gen1 | ✓ | | ✓ ||
| Azure Data Lake Storage Gen2 | ✓ | | ✓ ||
| Azure SQL Database | ✓ | | ✓ | ✓ | ✓ ||
| Azure Synapse Analytics (以前的 Azure SQL DW)  | ✓ | | ✓ | ✓ | ✓ ||
| Azure 数据资源管理器 |||||| ✓ |

## <a name="share-from-a-storage-account"></a>从存储帐户共享
Azure 数据共享支持从 Azure Data Lake Gen1 和 Azure Data Lake Gen2 共享文件、文件夹和文件系统。 它还支持从 Azure Blob 存储共享 blob、文件夹和容器。 目前仅支持块 blob。 在基于快照的共享中共享文件系统、容器或文件夹时，数据使用者可以选择创建共享数据的完整副本，也可以利用增量快照功能只复制新的或更新的文件。 增量快照基于文件的上次修改时间。 将覆盖具有相同名称的现有文件。

有关详细信息 [，请参阅共享和接收来自 Azure Blob 存储的数据和 Azure Data Lake Storage](how-to-share-from-storage.md) 。

## <a name="share-from-a-sql-based-source"></a>从基于 SQL 的源共享
Azure 数据共享支持在以前的 Azure SQL DW) 中共享 Azure SQL 数据库和 Azure Synapse (分析中的表或视图。 数据使用者可以选择接受将数据作为 csv 或 parquet 文件的 Azure Data Lake Storage Gen2 或 Azure Blob 存储，以及作为表的 Azure SQL 数据库和 Azure Synapse 分析。

在将数据接收到 Azure Data Lake Store Gen2 或 Azure Blob 存储时，如果已存在，则完全快照将覆盖目标文件的内容。
当将数据接收到表中，并且如果目标表尚不存在，Azure 数据共享将创建包含源架构的 SQL 表。 如果已存在具有相同名称的目标表，则将删除该目标表并使用最新的完整快照覆盖它。 当前不支持增量快照。

有关详细信息，请参阅 [在 AZURE SQL 数据库和 Azure Synapse Analytics 中共享和接收数据](how-to-share-from-sql.md) 。

## <a name="share-from-azure-data-explorer"></a>从 Azure 数据资源管理器进行共享
Azure 数据共享支持从 Azure 数据资源管理器群集共享就地数据库。 数据访问接口可以在数据库级别或群集级别共享。 在数据库级别共享时，数据使用者将只能访问数据提供程序共享)  (的特定数据库。 当在群集级别共享时，数据使用者可以从提供程序的群集访问所有数据库，包括任何将来由数据提供程序创建的数据库。

若要访问共享数据库，数据使用者需要有自己的 Azure 数据资源管理器群集。 数据使用者的 Azure 数据资源管理器群集需要在与数据提供商的 Azure 数据资源管理器群集相同的 Azure 数据中心中查找。 建立共享关系时，Azure 数据共享会在提供者和使用者的 Azure 数据资源管理器群集之间创建一个符号链接。 使用批处理模式进入源 Azure 数据资源管理器群集的数据引入将在几秒钟到几分钟内显示在目标群集上。

有关详细信息，请参阅 [从 Azure 数据资源管理器共享和接收数据](/azure/data-explorer/data-share) 。 

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读 [共享数据](share-your-data.md) 教程。
