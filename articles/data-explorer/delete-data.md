---
title: 从 Azure 数据资源管理器中删除数据
description: 本文介绍 Azure 数据资源管理器中的删除方案，包括清除、删除区和基于保留的删除。
author: orspod
ms.author: orspodek
ms.reviewer: avneraa
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/12/2020
ms.openlocfilehash: 681cfd71d2666630b192935d66ba32eaf16c92de
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79204607"
---
# <a name="delete-data-from-azure-data-explorer"></a>从 Azure 数据资源管理器中删除数据

Azure 数据资源管理器支持本文中所述的各种删除方案。 

## <a name="delete-data-using-the-retention-policy"></a>使用保留策略删除数据

Azure 数据资源管理器会根据[保留策略](/azure/kusto/management/retentionpolicy)自动删除数据。 此方法是删除数据最有效且无障碍的方式。 设置数据库或表级别的保留策略。

假设某个数据库或表的保留期设置为 90 天。 如果只需要60天的数据，请按如下所示删除旧数据：

```kusto
.alter-merge database <DatabaseName> policy retention softdelete = 60d

.alter-merge table <TableName> policy retention softdelete = 60d
```

## <a name="delete-data-by-dropping-extents"></a>通过删除区删除数据

[区（data 分片）](/azure/kusto/management/extents-overview)是存储数据的内部结构。 每个范围可容纳多达数百万条记录。 可以使用 "[删除范围" 命令](/azure/kusto/management/extents-commands#drop-extents)逐个或以组的方式删除区。 

### <a name="examples"></a>示例

您可以删除表中的所有行或只删除特定范围。

* 删除表中的所有行：

    ```kusto
    .drop extents from TestTable
    ```

* 删除特定范围：

    ```kusto
    .drop extent e9fac0d2-b6d5-4ce3-bdb4-dea052d13b42
    ```

## <a name="delete-individual-rows-using-purge"></a>使用清除删除单个行

[数据清除](/azure/kusto/management/data-purge)可用于删除个人行。 删除不是即时的，需要大量的系统资源。 这种情况下，只建议符合法规要求。  

