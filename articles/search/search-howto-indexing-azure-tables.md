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
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 66e6fec16aab7764b05b616efc0fccbfb2d0595e
ms.lasthandoff: 03/30/2017

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
   * 作为 `credentials.connectionString` 参数传入存储帐户连接字符串。 有关详细信息请参阅下方的[如何指定凭据](#Credentials)。
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
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

有关创建数据源 API 的详细信息，请参阅[创建数据源](https://msdn.microsoft.com/library/azure/dn946876.aspx)。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>如何指定凭据 ####

可通过以下一种方式提供表的凭据： 

- **完全访问存储帐户连接字符串**：`DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`。 可通过导航到“存储帐户”边栏选项卡 >“设置”>“密钥”（对于经典存储帐户）或“设置”>“访问密钥”（对于 Azure Resource Manager 存储帐户），从 Azure 门户获取连接字符串。
- **存储帐户共享访问签名** (SAS) 连接字符串：`TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`。 SAS 应具有容器（本例中为表）和对象（表行）的列表和读取权限。
-  **表共享访问签名**：`ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`。 SAS 应具有表的查询（读取）权限。

有关存储共享访问签名的详细信息，请参阅[使用共享访问签名](../storage/storage-dotnet-shared-access-signature-part-1.md)。

> [!NOTE]
> 如果使用 SAS 凭据，则需使用续订的签名定期更新数据源凭据，以防止其过期。 如果 SAS 凭据过期，索引器将失败，出现类似于 `Credentials provided in the connection string are invalid or have expired.` 的错误消息。  

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

