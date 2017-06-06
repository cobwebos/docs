---
title: "数据工厂 - .NET API 更改日志 | Microsoft Docs"
description: "描述 Azure 数据工厂的 .NET API 的特定版本中的重大更改、新增功能、Bug 修复等。"
services: data-factory
documentationcenter: 
author: spelluru
manager: jhubbard
editor: monicar
ms.assetid: 8208271b-7f4c-4214-b665-d2ff503c4470
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/05/2017
ms.author: spelluru
ms.translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 06702281ab2a44641231783aa1ddad87c22095e8
ms.contentlocale: zh-cn
ms.lasthandoff: 03/30/2017


---
# <a name="azure-data-factory---net-api-change-log"></a>Azure 数据工厂 - .NET API 更改日志
本文提供有关特定版本的 Azure 数据工厂 SDK 中的更改信息。 可以在[此处](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories)找到 Azure 数据工厂的最新 NuGet 包

## <a name="version-4110"></a>版本 4.11.0
新增功能：

* 已添加以下链接服务类型：
  * [OnPremisesMongoDbLinkedService](https://msdn.microsoft.com/library/mt765129.aspx)
  * [AmazonRedshiftLinkedService](https://msdn.microsoft.com/library/mt765121.aspx)
  * [AwsAccessKeyLinkedService](https://msdn.microsoft.com/library/mt765144.aspx)
* 已添加以下数据集类型：
  * [MongoDbCollectionDataset](https://msdn.microsoft.com/library/mt765145.aspx)
  * [AmazonS3Dataset](https://msdn.microsoft.com/library/mt765112.aspx)
* 已添加以下复制源类型：
  * [MongoDbSource](https://msdn.microsoft.com/library/mt765123.aspx)

## <a name="version-4100"></a>版本 4.10.0
* 已向 TextFormat 添加了以下可选属性：
  * [SkipLineCount](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.skiplinecount.aspx)
  * [FirstRowAsHeader](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.firstrowasheader.aspx)
  * [TreatEmptyAsNull](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.textformat.treatemptyasnull.aspx)
* 已添加以下链接服务类型：
  * [OnPremisesCassandraLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandralinkedservice.aspx)
  * [SalesforceLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.salesforcelinkedservice.aspx)
* 已添加以下数据集类型：
  * [OnPremisesCassandraTableDataset](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisescassandratabledataset.aspx)
* 已添加以下复制源类型：
  * [CassandraSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.cassandrasource.aspx)
* 向 AzureMLBatchExecutionActivity 添加了 [WebServiceInputs](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azuremlbatchexecutionactivity.webserviceinputs.aspx) 属性
  * 能够将多个 Web 服务输入传递到 Azure 机器学习试验

## <a name="version-491"></a>版本 4.9.1
### <a name="bug-fix"></a>Bug 修复
* 针对 [WebLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.weblinkedservice.authenticationtype.aspx)，已弃用基于 WebApi 的身份验证。

## <a name="version-490"></a>版本 4.9.0
### <a name="feature-additions"></a>新增功能
* 向 CopyActivity 添加了 [EnableStaging](https://msdn.microsoft.com/library/mt767916.aspx) 和 [StagingSettings](https://msdn.microsoft.com/library/mt767918.aspx) 属性。 有关此功能的详细信息，请参阅[暂存复制](data-factory-copy-activity-performance.md#staged-copy)。

### <a name="bug-fix"></a>Bug 修复
* 引入了 [ActivityWindowOperationExtensions.List](https://msdn.microsoft.com/library/mt767915.aspx) 方法的重载，该方法采用 [ActivityWindowsByActivityListParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.activitywindowsbyactivitylistparameters.aspx) 实例。
* 在 CopySink 中将 [WriteBatchSize](https://msdn.microsoft.com/library/dn884293.aspx) 和 [WriteBatchTimeout](https://msdn.microsoft.com/library/dn884245.aspx) 标记为可选。

## <a name="version-480"></a>版本 4.8.0
### <a name="feature-additions"></a>新增功能
* 已向复制活动类型添加了以下可选属性，以实现复制性能的优化：
  * [ParallelCopies](https://msdn.microsoft.com/library/mt767910.aspx)
  * [CloudDataMovementUnits](https://msdn.microsoft.com/library/mt767912.aspx)

## <a name="version-470"></a>版本 4.7.0
### <a name="feature-additions"></a>新增功能
* 添加了新的 StorageFormat 类型 [OrcFormat](https://msdn.microsoft.com/library/mt723391.aspx) 类型，以优化的行纵栏表 (ORC) 格式复制文件。
* 向 SqlDWSink 添加了 [AllowPolyBase](https://msdn.microsoft.com/library/mt723396.aspx) 和 PolyBaseSettings 属性。
  * 能够使用 PolyBase 将数据复制到 SQL 数据仓库中。

## <a name="version-461"></a>版本 4.6.1
### <a name="bug-fixes"></a>Bug 修复
* 修复了列出活动窗口的 HTTP 请求。
  * 从请求负载中删除了资源组名称和数据工厂名称。

## <a name="version-460"></a>版本 4.6.0
### <a name="feature-additions"></a>新增功能
* 已向 [PipelineProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties_properties.aspx) 添加了以下属性：
  * [PipelineMode](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.pipelinemode.aspx)
  * [ExpirationTime](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.expirationtime.aspx)
  * [数据集](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.pipelineproperties.datasets.aspx)
* 已向 [PipelineRuntimeInfo](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.aspx) 添加了以下属性：
  * [PipelineState](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.common.models.pipelineruntimeinfo.pipelinestate.aspx)
* 添加了新的 [StorageFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.storageformat.aspx) 类型 [JsonFormat](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.jsonformat.aspx) 类型以定义其数据为 JSON 格式的数据集。

## <a name="version-450"></a>版本 4.5.0
### <a name="feature-additions"></a>新增功能
* 添加了[活动窗口的操作列表](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.activitywindowoperationsextensions.aspx)。
  * 添加了使用筛选器基于实体类型（即数据工厂、数据集、管道和活动）检索活动窗口的方法。
* 已添加以下链接服务类型：
  * [ODataLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odatalinkedservice.aspx)、[WebLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.weblinkedservice.aspx)
* 已添加以下数据集类型：
  * [ODataResourceDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.odataresourcedataset.aspx)、[WebTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.webtabledataset.aspx)
* 已添加以下复制源类型：     
  * [WebSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.websource.aspx)

## <a name="version-440"></a>版本 4.4.0
### <a name="feature-additions"></a>新增功能
* 已将下面的链接服务类型添加为复制活动的数据源和接收器：
  * [AzureStorageSasLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.azurestoragesaslinkedservice.aspx)。 有关概念性信息和示例，请参阅 [Azure 存储 SAS 链接服务](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service)。

## <a name="version-430"></a>版本 4.3.0
### <a name="feature-additions"></a>新增功能
* 已将下面的链接服务类型添加为复制活动的数据源：
  * [HdfsLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.hdfslinkedservice.aspx)。 有关概念性信息和示例，请参阅[使用数据工厂从 HDFS 中移动数据](data-factory-hdfs-connector.md)。
  * [OnPremisesOdbcLinkedService](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.onpremisesodbclinkedservice.aspx)。 有关概念性信息和示例，请参阅[使用 Azure 数据工厂从 ODBC 数据存储中移动数据](data-factory-odbc-connector.md)。

## <a name="version-420"></a>版本 4.2.0
### <a name="feature-additions"></a>新增功能
* 已添加以下新的活动类型：[AzureMLUpdateResourceActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremlupdateresourceactivity.aspx)。 有关活动的详细信息，请参阅[使用更新资源活动更新 Azure ML 模型](data-factory-azure-ml-batch-execution-activity.md)。
* 一个新的可选属性 [updateResourceEndpoint](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.updateresourceendpoint.aspx) 已被添加到 [AzureMLLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuremllinkedservice.aspx)。
* [LongRunningOperationInitialTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationinitialtimeout.aspx) 和 [LongRunningOperationRetryTimeout](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.longrunningoperationretrytimeout.aspx) 属性已被添加到 [DataFactoryManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.datafactorymanagementclient.aspx) 类。
* 允许对数据工厂服务的客户端调用配置超时。

## <a name="version-410"></a>版本 4.1.0
### <a name="feature-additions"></a>新增功能
* 已添加以下链接服务类型：
  * [AzureDataLakeStoreLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
  * [AzureDataLakeAnalyticsLinkedService](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* 已添加以下活动类型：
  * [DataLakeAnalyticsUSQLActivity](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datalakeanalyticsusqlactivity.aspx)
* 已添加以下数据集类型：
  * [AzureDataLakeStoreDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoredataset.aspx)
* 已针对复制活动添加以下源和接收器类型：
  * [AzureDataLakeStoreSource](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresource.aspx)
  * [AzureDataLakeStoreSink](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestoresink.aspx)

## <a name="version-401"></a>版本 4.0.1
### <a name="breaking-changes"></a>重大变化
已重命名下面的类。 新名称是 4.0.0 版之前的类的原始名称。

| 4.0.0 版中的名称 | 4.0.1 版中的名称 |
|:--- |:--- |
| AzureSqlDataWarehouseDataset |[AzureSqlDataWarehouseTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqldatawarehousetabledataset.aspx) |
| AzureSqlDataset |[AzureSqlTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuresqltabledataset.aspx) |
| AzureDataset |[AzureTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuretabledataset.aspx) |
| OracleDataset |[OracleTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.oracletabledataset.aspx) |
| RelationalDataset |[RelationalTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.relationaltabledataset.aspx) |
| SqlServerDataset |[SqlServerTableDataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.sqlservertabledataset.aspx) |

## <a name="version-400"></a>版本 4.0.0
### <a name="breaking-changes"></a>重大变化
* 已重命名下面的类/接口。

| 旧名称 | 新名称 |
|:--- |:--- |
| ITableOperations |[IDatasetOperations](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.idatasetoperations.aspx) |
| 表 |[Dataset](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.dataset.aspx) |
| TableProperties |[DatasetProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetproperties.aspx) |
| TableTypeProprerties |[DatasetTypeProperties](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasettypeproperties.aspx) |
| TableCreateOrUpdateParameters |[DatasetCreateOrUpdateParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateparameters.aspx) |
| TableCreateOrUpdateResponse |[DatasetCreateOrUpdateResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdateresponse.aspx) |
| TableGetResponse |[DatasetGetResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetgetresponse.aspx) |
| TableListResponse |[DatasetListResponse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetlistresponse.aspx) |
| CreateOrUpdateWithRawJsonContentParameters |[DatasetCreateOrUpdateWithRawJsonContentParameters](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.datasetcreateorupdatewithrawjsoncontentparameters.aspx) |

* **列表**方法现在返回分页的结果。 如果响应中包含非空 **NextLink** 属性，则客户端应用程序需要继续提取下一页直到返回所有页。  下面是一个示例：

    ```csharp
    PipelineListResponse response = client.Pipelines.List("ResourceGroupName", "DataFactoryName");
    var pipelines = new List<Pipeline>(response.Pipelines);

    string nextLink = response.NextLink;
    while (!string.IsNullOrEmpty(nextLink))
    {
        PipelineListResponse nextResponse = client.Pipelines.ListNext(nextLink);
        pipelines.AddRange(nextResponse.Pipelines);

        nextLink = nextResponse.NextLink;
    }
    ```
* **列表**管道 API 仅返回管道的摘要而不是完整的详细信息。 例如，管道摘要中的活动只包含名称和类型。

### <a name="feature-additions"></a>新增功能
* [SqlDWSink](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsink.aspx) 类支持两个新属性（即 **SliceIdentifierColumnName** 和 **SqlWriterCleanupScript**），以支持到 Azure SQL 数据仓库的幂等复制。 有关这些属性的详细信息，请参阅 [Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md)一文。
* 当前支持针对 Azure SQL 数据库和 Azure SQL 数据仓库源作为复制活动的一部分运行存储过程。 [SqlSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqlsource.aspx) 和 [SqlDWSource](https://msdn.microsoft.com/library/azure/microsoft.azure.management.datafactories.models.sqldwsource.aspx) 类具有以下属性：**SqlReaderStoredProcedureName** 和 **StoredProcedureParameters**。 有关这些属性的详细信息，请参阅 Azure.com 上的 [Azure SQL 数据库](data-factory-azure-sql-connector.md#sqlsource)和 [Azure SQL 数据仓库](data-factory-azure-sql-data-warehouse-connector.md#sqldwsource)文章。  

