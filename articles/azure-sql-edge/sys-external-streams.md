---
title: sys.external_streams (Transact-SQL) - Azure SQL Edge（预览版）
description: 了解如何在 Azure SQL Edge（预览版）中使用 sys.external_streams
keywords: sys.external_streams, SQL Edge
services: sql-database-edge
ms.service: sql-database-edge
ms.topic: reference
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 05/19/2019
ms.openlocfilehash: d47139b0b2a20ecfcf92bfc55a8b47c8e3cf7dab
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83594466"
---
# <a name="sysexternal_streams-transact-sql"></a>sys.external_streams (Transact-SQL)

为在数据库范围中创建的每个外部流对象返回一行。

|列名称|数据类型|说明|  
|-----------------|---------------|-----------------|
|name|**sysname**|流的名称。 在该数据库中是唯一的。|
|object_id|**int**|流对象的对象标识号。 在该数据库中是唯一的。|
|principal_id|**int**|此程序集所属主体的 ID|
|schema_id|**int**| 包含对象的架构的 ID。|
|parent_object_id|**id**| 此流的父对象的对象标识号。 在当前实现中，此值始终为 null|
|type|**char(2)**|对象类型。 对于流对象，类型始终为“ES”|
|**type_desc**|**nvarchar(60)**| 对象类型的说明。 对于流对象，类型始终为“EXTERNAL_STREAM”|
|create_date|**datetime**| 对象的创建日期。|
|modify_date|**datetime**| 上次使用 ALTER 语句修改对象的日期。|
|is_ms_shipped|**bit**| 内部组件所创建的对象。|  
|is_published|**bit**|对象为发布对象。|  
|is_schema_published|**bit**|仅发布对象的架构。|
|max_column_id_used|**bit**| 此列用于内部目的，并会在将来删除|  
|uses_ansi_nulls|**bit**| 创建流对象时，SET ANSI_NULLS 数据库选项的设置为 ON|
|data_source_id|**int**| 流对象表示的外部数据源的对象 ID |  
|file_format_id|**int**| 流对象使用的外部文件格式的对象 ID。 需要提供外部文件格式才能指定外部流所引用数据的实际布局| 
|**location**|**varchar(max)**| 外部流对象的目标。 有关详细信息，请参阅[创建外部流](overview.md) |
|input_option|**varchar(max)**| 在创建外部流的过程中使用的输入选项。 有关详细信息，请参阅[创建外部流](overview.md) |
|output_option|**varchar(max)**| 在创建外部流的过程中使用的输出选项。 有关详细信息，请参阅[创建外部流](overview.md) | 

## <a name="permissions"></a>权限

目录视图中仅显示用户拥有的安全对象的元数据，或用户对其拥有某些权限的安全对象的元数据。 有关详细信息，请参阅 [Metadata Visibility Configuration](/sql/relational-databases/security/metadata-visibility-configuration/)。

## <a name="see-also"></a>另请参阅

- [目录视图 (Transact-SQL)](/sql/relational-databases/system-catalog-views/catalog-views-transact-sql/)
- [系统视图 (Transact-SQL)](/sql/t-sql/language-reference/)
