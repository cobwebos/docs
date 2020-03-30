---
title: 从 Azure 数据资源管理器中删除数据
description: 本文介绍 Azure 数据资源管理器中的删除方案，包括清除、删除扩展盘区和基于保留的删除。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: dd0f8740d148a7817bcfe2fbad591ceeb1610d0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501412"
---
# <a name="delete-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器中删除数据

Azure 数据资源管理器支持本文中描述的各种删除方案。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留策略删除数据

Azure 数据资源管理器根据[保留策略](/azure/kusto/management/retentionpolicy)自动删除数据。 此方法是删除数据的最有效和无忧无虑的方法。 在数据库或表级别设置保留策略。

假设某个数据库或表的保留期设置为 90 天。 如果只需要 60 天的数据，请删除较旧的数据，如下所示：

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>通过删除扩展区删除数据

[范围（数据分片）](/azure/kusto/management/extents-overview)是存储数据的内部结构。 每个范围可以容纳多达数百万条记录。 可以使用[放置范围（s） 命令](/azure/kusto/management/extents-commands#drop-extents)单独删除扩展盘区或作为组删除。 

### <a name="examples"></a>示例

您可以删除表中的所有行，也可以仅删除特定范围。

* 删除表中的所有行：

    ```kusto
    .drop extents from TestTable
    ```

* 删除特定范围：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除删除单个行

[数据清除](/azure/kusto/concepts/data-purge)可用于删除个人行。 删除不是即时的，需要大量的系统资源。 因此，仅建议针对合规性方案。  

