---
title: Azure 数据资源管理器的数据引入属性
description: 了解 Azure 数据资源管理器支持的各种数据引入属性。
author: orspod
ms.author: orspodek
ms.reviewer: tzgitlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/19/2020
ms.openlocfilehash: 48bce1bf03a0a4c8d81fff7ae54e0b22261b70f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80109571"
---
# <a name="azure-data-explorer-data-ingestion-properties"></a>Azure 数据资源管理器数据引入属性 

数据引入是将数据添加到表中并可在 Azure 数据资源管理器中查询的过程。 在`with`关键字之后向引入命令添加属性。

## <a name="ingestion-properties"></a>引入属性

下表列出了 Azure 数据资源管理器支持的属性，介绍了这些属性，并提供了示例： 

|properties              |说明                                              |示例                                             |
|----------------------|---------------------------------------------------------|----------------------------------------------------|
|`ingestionMapping`    |一个字符串值，表示如何将数据从源文件映射到表中的实际列。 使用相关`format`映射类型定义值。 请参阅[数据映射](/azure/kusto/management/mappings)。|`with (format="json", ingestionMapping = "[{\"column\":\"rownumber\", \"Properties\":{\"Path\":\"$.RowNumber\"}}, {\"column\":\"rowguid\", \"Properties\":{\"Path\":\"$.RowGuid\"}}]")`<br>（已弃用：`avroMapping`、`csvMapping`、`jsonMapping`） |
|`ingestionMappingReference`|一个字符串值，表示如何使用命名的映射策略对象将数据从源文件映射到表中的实际列。 使用相关`format`映射类型定义值。 请参阅[数据映射](/azure/kusto/management/mappings)。|`with (format="csv", ingestionMappingReference = "Mapping1")`<br>（已弃用：`avroMappingReference`、`csvMappingReference`、`jsonMappingReference`）|
|`creationTime` |在引入的数据范围的创建时要使用的日期时间值（格式化为 ISO8601 字符串）。 在未指定的情况下会使用当前值 (`now()`)。 在引入较旧的数据时，重写默认值非常有用，以便正确应用保留策略。|`with (creationTime="2017-02-13T11:09:36.7992775Z")`|
|`extend_schema`|一个布尔值，在指定的情况下会指示命令扩展表的架构（默认为 `false`）。 此选项仅适用于 `.append` 和 `.set-or-append` 命令。 进行架构扩展时，只能在表的末尾添加其他列。|如果原始表架构为`(a:string, b:int)`，则有效的架构扩展将无效`(a:string, b:int, c:datetime, d:string)``(a:string, c:datetime)`|
|`folder` |对于[从查询引入](/azure/kusto/management/data-ingestion/ingest-from-query)的命令，要分配给表的文件夹。 如果表已存在，则此属性将覆盖表的文件夹。|`with (folder="Tables/Temporary")`|
|`format` |数据格式（请参阅[支持的数据格式](ingestion-supported-formats.md)）。|`with (format="csv")`|
|`ingestIfNotExists`|一个字符串值，如果已指定，则在表的数据已通过一个具有相同值的 `ingest-by:` 标记进行了标记时，引入操作会失败。 这确保了幂等数据引入。 有关详细信息，请参阅[通过： 标记](/azure/kusto/management/extents-overview#ingest-by-extent-tags)。|这些属性`with (ingestIfNotExists='["Part0001"]', tags='["ingest-by:Part0001"]')`指示，如果具有标记`ingest-by:Part0001`的数据已存在，则不要完成当前引入。 如果不存在，则此新引入应设置此标记（以防将来再次引入相同的数据。|
|`ignoreFirstRecord` |一个布尔值，在设置为 `true` 的情况下表示引入操作应该忽略每个文件的第一条记录。 如果文件中的第一个记录是`CSV`列名，则此属性对于格式和类似格式的文件很有用。 默认情况下，`false`假定为。|`with (ignoreFirstRecord=false)`|
|`persistDetails` |一个布尔值，在指定的情况下表示命令应该保留详细的结果（即使命令已成功执行），这样 [.show operation details](/azure/kusto/management/operations#show-operation-details) 命令就能够检索它们。 默认为 `false`。|`with (persistDetails=true)`|
|`policy_ingestiontime`|一个布尔值，在指定的情况下，说明是否在通过此命令创建的表上启用[引入时间策略](/azure/kusto/management/ingestiontimepolicy)。 默认为 `true`。|`with (policy_ingestiontime=false)`|
|`recreate_schema` |一个布尔值，在指定的情况下，说明此命令是否会重新创建表的架构。 此属性仅适用于命令`.set-or-replace`。 如果同时设置两个属性`extend_schema`，则此属性优先于该属性。|`with (recreate_schema=true)`|
|`tags`|要与引入的数据关联的标记列表，该[标记](/azure/kusto/management/extents-overview#extent-tagging)的格式为 JSON 字符串 |`with (tags="['Tag1', 'Tag2']")`|
|`validationPolicy`|一个 JSON 字符串，表示在引入期间要运行哪些验证。 有关不同选项的说明，请参阅[数据引入](/azure/kusto/management/data-ingestion/)。| `with (validationPolicy='{"ValidationOptions":1, "ValidationImplications":1}')`（这实际上是默认策略）|
|`zipPattern`|从具有 ZIP 存档的存储中引入数据时，请使用此属性。 这是一个字符串值，指示在选择要引入的 ZIP 存档中的文件的正则表达式。  存档中的所有其他文件会被忽略。|`with (zipPattern="*.csv")`|

## <a name="next-steps"></a>后续步骤

* 了解有关[数据引入](/azure/data-explorer/ingest-data-overview)的更多
* 了解有关[受支持数据格式](ingestion-supported-formats.md)的信息
