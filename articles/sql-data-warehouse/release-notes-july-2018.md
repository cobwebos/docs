---
title: Azure SQL 数据仓库发行说明（2018 年 6 月）| Microsoft Docs
description: Azure SQL 数据仓库发行说明。
services: sql-data-warehouse
author: twounder
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: manage
ms.date: 07/23/2018
ms.author: twounder
ms.reviewer: twounder
ms.openlocfilehash: 5f4d39c6aa1a5c2c30e84fbf26535fe3ee7799a6
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/23/2018
ms.locfileid: "39216683"
---
# <a name="whats-new-in-azure-sql-data-warehouse-july-2018"></a>Azure SQL 数据仓库中的新增功能 2018 年 7 月
Azure SQL 数据仓库持续得到改进。 本文介绍了 2018 年 7 月发行的版本中所引入的新功能和所做的更改。


## <a name="finer-granularity-for-cross-region-and-server-restores"></a>实现跨区域和服务器还原的更精细粒度
现在可以使用任何还原点跨区域和服务器进行还原，而不是选择每 24 小时执行一次的异地冗余备份。 用户定义或自动还原点支持跨区域和服务器还原，从而实现以更精细的粒度进行其他数据保护。 由于有更多还原点可用，你可以确保在跨区域还原时，数据仓库在逻辑上是一致的。

![还原命令 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6ac23972-9ec0-4502-ab10-7b6bc1a3d947.png)
![还原选项 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/6c63bd0e-9c52-414d-b4be-d3bd3774ee08.png)

有关详细信息，请参阅[加速且灵活的还原点](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)博客文章。

## <a name="20-minute-restorations"></a>20 分钟还原
SQL 数据仓库现在可以用**不到 20 分钟**时间在同一区域内还原任何数据仓库，而不管数据库大小如何。 无论还原是在同一区域内的相同还是不同逻辑服务器上进行，此还原时间都适用。 此外，已对快照过程进行改进，以减少创建还原点所需的时间。 在较低性能级别（较低 DWU 设置）中，改进是将快照创建时间*缩短 2 倍*。

有关详细信息，请参阅[加速且灵活的还原点](https://azure.microsoft.com/blog/accelerated-and-flexible-restore-points-with-sql-data-warehouse/)博客文章。

## <a name="custom-restoration-configurations"></a>自定义还原配置
在 Azure 门户中还原时，现在可以更改性能级别 (DWU)。 还可以还原到升级后的第 2 代数据仓库。 通过还原到第 2 代实例，现在可以在[升级第 1 代数据仓库](https://docs.microsoft.com/azure/sql-data-warehouse/upgrade-to-latest-generation)之前评估第 2 代的影响。

![自定义还原配置 - Azure SQL 数据仓库](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/f4c410c7-8515-409c-a983-0976792b8628.png)

## <a name="spdescribeundeclaredparameters"></a>SP_DESCRIBE_UNDECLARED_PARAMETERS
工具通常使用 [sp_describe_undeclared_parameters](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-describe-undeclared-parameters-transact-sql) 存储过程来获取有关 Transact-SQL 批处理中的参数的元数据。 该过程为批处理中的每个参数返回一行，其中包含该参数的推断类型信息。 

```sql
EXEC sp_describe_undeclared_parameters
    @tsql = 
    N'SELECT
        object_id, name, type_desc
      FROM
        sys.indexes
      WHERE
        object_id = @id;'
```

结果集包含有关 `@id` 参数的元数据（显示部分结果）
```
parameter_ordinal | name | suggested_system_type_id | suggested_system_type_name
--------------------------------------------------------------------------------
1                 | @id  | 56                       | int
```