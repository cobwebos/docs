---
title: Azure 数据共享中支持的数据存储
description: 了解使用 Azure 数据共享支持的数据存储。
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: 762cea6dce3e0c6be3f5e977c5f9de806ca0880e
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/04/2019
ms.locfileid: "73516391"
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
| Azure SQL 数据库 |公共预览版 | |
| Azure SQL 数据仓库 |公共预览版 | |
| Azure 数据资源管理器 | |[有限预览](https://aka.ms/azuredatasharepreviewsignup) |

## <a name="data-store-support-matrix"></a>数据存储支持矩阵

在确定数据存储区以接受中的数据时，Azure 数据共享可为数据使用者提供灵活性。 例如，可将 Azure SQL 数据库中共享的数据接收到 Azure Data Lake Store Gen2、Azure SQL 数据库或 Azure SQL 数据仓库。 客户可以在配置接收的数据共享时选择要接收数据的格式。 

下表详细说明了数据使用者在接受和配置其数据共享时具有的不同组合和选择。 有关如何配置数据集映射的详细信息，请参阅[如何配置数据集映射](how-to-configure-mapping.md)。

|  | Azure Blob 存储 | Azure SQL Data Lake Gen1 | Azure SQL Data Lake Gen2 | Azure SQL 数据库 | Azure SQL 数据仓库 
|:--- |:--- |:--- |:--- |:--- |:--- |
| Azure Blob 存储 |✓ ||✓|
| Azure Data Lake Storage Gen1 |✓ | |✓|
| Azure Data Lake Storage Gen2 |✓ | |✓|
| Azure SQL 数据库 |✓ | |✓|✓|✓|
| Azure SQL 数据仓库 |✓ | |✓|✓|✓|

## <a name="next-steps"></a>后续步骤

若要了解如何开始共享数据，请继续阅读[共享数据](share-your-data.md)教程。
