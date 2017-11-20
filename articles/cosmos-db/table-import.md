---
title: "导入要在 Azure Cosmos DB 表 API 中使用的数据 | Microsoft Docs"
description: "了解如何导入要在 Azure Cosmos DB 表 API 中使用的数据。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: b60743e2-0227-43ab-965a-0ae3ebacd917
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: 984510b2dae99849507953163f94ad2f925651cd
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2017
---
# <a name="import-data-for-use-with-the-azure-cosmos-db-table-api"></a>导入要在 Azure Cosmos DB 表 API 中使用的数据

本教程说明如何导入要在 Azure Cosmos DB [表 API](table-introduction.md) 中使用的数据。 如果已将数据存储在 Azure 表存储中，可以使用数据迁移工具或 az copy 将数据导入 Azure Cosmos DB。 导入数据后，便可以利用 Azure Cosmos DB 提供的高级功能，例如统包式全局分发、专用吞吐量、99% 的情况下低至个位数的毫秒级延迟、有保障的高可用性，以及自动辅助索引。

本教程涵盖以下任务：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据

## <a name="data-migration-tool"></a>数据迁移工具

Azure Cosmos DB 数据迁移工具是导入现有 Azure 表存储数据的一个选项。 若要使用该工具导入数据，需要选择 Azure 表存储作为源，选择 Azure Cosmos DB 表 API 作为目标，并根据工具中的要求提供剩余的信息。 可以批量导出数据，也可以使用顺序记录导入来导出数据。 

以下部分提供了有关将 Azure 表存储定义为源，将表 API 定义为目标的信息：
- [使用 Azure 表存储作为数据迁移源](import-data.md#AzureTableSource)。 
- [使用批量导入导出到表 API](import-data.md#tableapibulkexport)
- [使用顺序记录导入导出到表 API](import-data.md#tableapiseqtarget)

## <a name="azcopy-command"></a>AzCopy 命令

使用 AzCopy 命令行实用工具是将数据从 Azure 表存储迁移到 Azure Cosmos DB 表 API 的另一个选项。 若要使用 AzCopy，首先需要根据[从表存储导出数据](../storage/common/storage-use-azcopy.md#export-data-from-table-storage)中所述将数据导出到清单文件，然后将清单文件中的数据导入[Azure Cosmos DB 表 API](../storage/common/storage-use-azcopy.md#import-data-into-table-storage)。

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 使用数据迁移工具导入数据
> * 使用 AzCopy 导入数据

现在可以继续学习下一教程并了解如何使用 Azure Cosmos DB 表 API 查询数据。 

> [!div class="nextstepaction"]
>[如何查询数据？](../cosmos-db/tutorial-query-table.md)



