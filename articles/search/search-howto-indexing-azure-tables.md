---
title: "使用 Azure 搜索对 Azure 表存储编制索引"
description: "了解如何使用 Azure 搜索对 Azure 表中存储的数据编制索引"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 10/27/2016
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: fc2f30569acc49dd383ba230271989eca8a14423
ms.openlocfilehash: 031667b3a6f0265e57568706e5454cd275ec9ecc

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>使用 Azure 搜索对 Azure 表存储编制索引
本文介绍如何使用 Azure 搜索对 Azure 表存储中存储的数据编制索引。 新的 Azure 搜索表索引器可快速、无缝地实现此过程。

## <a name="setting-up-azure-table-indexing"></a>设置 Azure 表索引
若要创建和配置 Azure 表索引器，可以使用 Azure 搜索 REST API 创建并管理“索引器”和“数据源”，如[索引器操作](https://msdn.microsoft.com/library/azure/dn946891.aspx)中所述。 还可以使用 .NET SDK [2.0 预览版](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx)。 将来，对表索引的支持会添加到 Azure 门户。

数据源指定要索引的数据、访问数据所需的凭据和支持 Azure 搜索有效标识数据更改（新建的行、修改的行或删除的行）的策略。

索引器从数据源读取数据，然后将它载入目标搜索索引。

若要创建表索引，请执行以下步骤:

1. 创建数据源
   * 将 `type` 参数设置为 `azuretable`
   * 作为 `credentials.connectionString` 参数传入存储帐户连接字符串
   * 使用 `container.name` 参数指定表名称
   * （可选）使用 `container.query` 参数指定查询。 如有可能，请在 PartitionKey 上使用筛选器，以获得最佳性能；任何其他查询将导致全表扫描，这可能会导致大型表性能降低。
2. 使用与要编制索引的表中的列对应的模式创建搜索索引。
3. 通过将数据源连接到搜索索引即可创建索引器。

### <a name="create-data-source"></a>创建数据源
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<my storage connection string>" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

有关创建数据源 API 的详细信息，请参阅[创建数据源](https://msdn.microsoft.com/library/azure/dn946876.aspx)。

### <a name="create-index"></a>创建索引
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

有关创建索引 API，请参阅[创建索引](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>创建索引器
最后，创建引用数据源和目标索引的索引器。 例如：

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

有关创建索引器 API 的更多详细信息，请参阅[创建索引器](https://msdn.microsoft.com/library/azure/dn946899.aspx)。

就是这么简单 - 已成功创建索引！

## <a name="dealing-with-different-field-names"></a>处理不同的字段名称
通常，现有索引中的字段名称将不同于表中的属性名称。 可以使用**字段映射**将表中的属性名称映射到搜索索引中的字段名称。 若要详细了解字段映射，请参阅 [Azure 搜索索引器字段映射弥补数据源和搜索索引之间的差异](search-indexer-field-mappings.md)。

## <a name="handling-document-keys"></a>处理文档键
在 Azure 搜索中，文档键唯一标识某个文档。 每个搜索索引必须只有一个类型为 `Edm.String` 的键字段。 键字段对于要添加到索引的每个文档必不可少（事实上，它是唯一的必填字段）。

由于表行具有复合键，因此 Azure 搜索会生成一个名为 `Key` 的合成字段，该字段是分区键值和行键值的串接。 例如，如果行的 PartitionKey 为 `PK1`、RowKey 为 `RK1`，那么 `Key` 字段的值将为 `PK1RK1`。

> [!NOTE]
> `Key` 值中可能含有文档键中无效的字符（如短划线）。 可以通过使用 `base64Encode` [字段映射函数](search-indexer-field-mappings.md#base64EncodeFunction)处理无效字符。 如果执行此操作，在 API 调用（如 Lookup）中传递文档键时，还请记得使用 URL-safe Base64 编码。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>增量索引和删除检测
当将表索引器设置为按计划运行时，它仅对由行的 `Timestamp` 值确定的新行或更新行重新编制索引。 无需指定更改检测策略 - 将自动为你启用增量索引。

若要指示必须从索引中删除某些文档，可以使用软删除策略（不是删除行）、添加表明文档已删除的属性，然后在数据源上设置软删除检测策略。 例如，以下所示策略会在行具有值为 `"true"` 的属性 `IsDeleted` 的情况下，将该行视为已删除：

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>帮助我们改进 Azure 搜索
如果你有功能请求或改进建议，请通过我们的 [UserVoice 站点](https://feedback.azure.com/forums/263029-azure-search/)与我们联系。



<!--HONumber=Nov16_HO3-->


