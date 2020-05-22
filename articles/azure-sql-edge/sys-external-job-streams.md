---
title: sys.external_job_streams (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解如何在 Azure SQL Edge（预览版）中使用 sys.external_job_streams
keywords: sys.external_job_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7f26c87c0de09618b2d24413f7ff692fd764b4cf
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594476"
---
# <a name="sysexternal_job_streams-transact-sql"></a>sys.external_job_streams (Transact-SQL)

为映射到外部流式处理作业的输入或输出外部流对象返回一行。

|列名称|数据类型|说明|  
|-----------------|---------------|-----------------|
|**job_id**|**int**| 流式处理作业对象的对象标识号。 此列映射到 sys.external_streaming_jobs 的 object_id 列。|
|**stream_id**|**int**| 流对象的对象标识号。 此列映射到 sys.external_streams 的 object_id 列。 |
|is_input|**bit**| 如果将流对象用于流式处理作业的输入，则为 1；否则为 0。|
|is_output|**bit**| 如果将流对象用于流式处理作业的输出，则为 1；否则为 0。|

## <a name="example"></a>示例

此目录视图与 `sys.external_streams` 和 `sys.external_streaming_jobs` 目录视图一起使用。 以下显示了一个查询示例

```sql
Select
    sj.Name as Job_Name,
    sj.Create_date as Job_Create_date,
    sj.modify_date as Job_Modify_date,
    sj.statement as Stream_Job_Query,
    Input_Stream_Name =
        Case js.is_input
            when 1 then s.Name
            else null
        END,
    output_Stream_Name =
        case js.is_output
            when 1 then s.Name
            else null
        END,
    s.location as Stream_Location
from sys.external_job_streams js
inner join sys.external_streams s on s.object_id = js.stream_id
inner join sys.external_streaming_jobs sj on sj.object_id = js.job_id
```

## <a name="permissions"></a>权限

目录视图中仅显示用户拥有的安全对象的元数据，或用户对其拥有某些权限的安全对象的元数据。 有关详细信息，请参阅 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另请参阅

- [目录视图 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系统视图 (Transact-SQL)](/sql/t-sql/language-reference/)
