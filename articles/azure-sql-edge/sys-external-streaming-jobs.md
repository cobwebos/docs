---
title: sys.external_streaming_jobs (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解如何在 Azure SQL Edge（预览版）中使用 sys.external_streaming_jobs
keywords: sys. external_streaming_jobs、SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: 7d4f66375c5490906e773c6f105a029c3a88465c
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594456"
---
# <a name="sysexternal_streaming_jobs-transact-sql"></a>sys.external_streaming_jobs (Transact-SQL)

为在数据库范围中创建的每个外部流式处理作业返回一行。

|列名称|数据类型|说明|  
|-----------------|---------------|-----------------|
|name|**sysname**|流的名称。 在该数据库中是唯一的。|
|object_id|**int**|流对象的对象标识号。 在该数据库中是唯一的。|
|principal_id|**int**|此程序集所属主体的 ID|
|schema_id|**int**| 包含对象的架构的 ID。|
|parent_object_id|**id**| 此流的父对象的对象标识号。 在当前实现中，此值始终为 null|
|type|**char(2)**|对象类型。 对于流对象，类型始终为“EJ”|
|**type_desc**|**nvarchar(60)**| 对象类型的说明。 对于流对象，类型始终为“EXTERNAL_STREAMING_JOB”|
|create_date|**datetime**| 对象的创建日期。|
|modify_date|**datetime**| 在当前实现中，此值与 stream 对象的 create_date 相同 |
|is_ms_shipped|**bit**| 内部组件所创建的对象。|  
|is_published|**bit**| 对象为发布对象。|  
|is_schema_published|**bit**|仅发布对象的架构。|
|uses_ansi_nulls|**bit**| 创建流对象时，SET ANSI_NULLS 数据库选项的设置为 ON|
|**语句**|**varchar(max)**| 流式处理作业的流分析查询文本。 有关详细信息，请参阅 [sp_create_streaming_job](overview.md) |
|**status**|**int**| 流式处理作业的当前状态。 可能的值包括 <br /><br /> 已创建 = 0。 流式处理作业已创建，但尚未开始。 <br /><br /> 正在启动 = 1。 流式处理作业处于开始阶段。 <br /><br /> 失败 = 6。 流式处理作业失败。 这通常表示在处理过程中出现灾难性错误。 <br /><br /> 已停止 = 4。 流式处理作业已停止。 <br /><br /> 空闲 = 7。 流式处理作业正在运行，但没有要处理的输入。 <br /><br /> 正在处理 = 8。 流式处理作业正在运行，且正在处理输入。 此状态指示流式处理作业的正常运行状态。 <br /><br /> 已降级 = 9。 流式处理作业正在运行，但在输入处理期间出现一些非致命的输入/输出序列化/反序列化错误。 输入作业将继续运行，但将删除遇到错误的输入。|

## <a name="permissions"></a>权限

目录视图中仅显示用户拥有的安全对象的元数据，或用户对其拥有某些权限的安全对象的元数据。 有关详细信息，请参阅 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另请参阅

- [T-SQL 流式处理目录视图](overview.md)
- [目录视图 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系统视图 (Transact-SQL)](/sql/t-sql/language-reference/)

