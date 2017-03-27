---
title: "Azure 数据工厂支持的计算环境 | Microsoft Docs"
description: "了解可在 Azure 数据工厂管道中用于转换/处理数据的计算环境。"
services: data-factory
documentationcenter: 
author: sharonlo101
manager: jhubbard
editor: monicar
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: shlo
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 5b3ff989c31f45f3344d406f9f419510dd380f8b
ms.lasthandoff: 03/07/2017


---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure 数据工厂支持的计算环境
本文介绍可用于处理或转换数据的不同计算环境。 同时还详细介绍了配置将这些计算环境链接到 Azure 数据工厂的链接服务时，数据工厂所支持的不同配置（按需和自带）。

下表提供了数据工厂所支持的计算环境以及可以在其上运行的活动的列表。 

| 计算环境 | 活动 |
| --- | --- |
| [按需 HDInsight 群集](#azure-hdinsight-on-demand-linked-service)或[自己的 HDInsight 群集](#azure-hdinsight-linked-service) |[DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop 流式处理](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) |[DotNet](data-factory-use-custom-activities.md) |
| [Azure 机器学习](#azure-machine-learning-linked-service) |[机器学习活动：批处理执行和更新资源](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) |[Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL 数据仓库](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) |[存储过程](data-factory-stored-proc-activity.md) |

## <a name="on-demand-compute-environment"></a>按需计算环境
在此类型的配置中，计算环境由 Azure 数据工厂服务完全管理。 作业提交到进程数据前，数据工厂服务将自动创建计算环境，作业完成后则自动将其删除。 可以为按需计算环境创建并配置链接服务，以及控制作业执行、群集管理和引导操作的粒度设置。

> [!NOTE]
> 按需配置目前仅支持 Azure HDInsight 群集。
> 
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 按需链接服务
Azure 数据工厂服务可自动创建基于 Windows/Linux 的按需 HDInsight 群集，以处理数据。 群集创建在与该群集相关联的存储帐户（JSON 中的 linkedServiceName 属性）所在的同一区域中。

请注意以下关于按需 HDInsight 链接服务的**重要**事项：

* 将无法看到创建在 Azure 订阅中的按需 HDInsight 群集。 Azure 数据工厂服务会代为管理按需 HDInsight 群集。
* 在按需 HDInsight 群集上运行的作业日志将复制到与 HDInsight 群集相关联的存储帐户。 可在 Azure 门户的“活动运行详细信息”边栏选项卡中访问这些日志。 有关详细信息，请参阅[监视和管理管道](data-factory-monitor-manage-pipelines.md)一文。
* 仅对 HDInsight 群集启动并运行作业的时间进行收费。

> [!IMPORTANT]
> 按需预配 Azure HDInsight 群集通常需要 **20 分钟**或更长时间。
> 
> 

### <a name="example"></a>示例
以下 JSON 定义基于 Linux 的按需 HDInsight 链接服务。 处理数据切片时，数据工厂服务将自动创建**基于 Linux 的** HDInsight 群集。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "clusterSize": 4,
            "timeToLive": "00:05:00",
            "osType": "linux",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

若要使用基于 Windows 的 HDInsight 群集，请将 **osType** 设置为 **windows**或者不使用属性，因为默认值为 windows。  

> [!IMPORTANT]
> HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。 
> 
> 随着处理的切片越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循模式：“adf**yourdatafactoryname**-**linkedservicename**-datetimestamp”。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。
> 
> 

### <a name="properties"></a>属性
| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **HDInsightOnDemand**。 |是 |
| clusterSize |群集中辅助进程/数据节点的数量。 HDInsight 群集创建时具有 2 个头节点以及一定数量的辅助进程节点（此节点的数量是为此属性所指定的数量）。 这些节点的大小为拥有 4 个核心的 Standard_D3，因此一个具有 4 个辅助节点的群集拥有 24 个核心（辅助节点有 4\*4 = 16 个核心，头节点有 2\*4 = 8 个核心）。 有关 Standard_D3 层的详细信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 |是 |
| timetolive |按需 HDInsight 群集允许的空闲时间。 指定当活动运行完成后，如果群集中没有其他的活动作业，按需 HDInsight 群集保持活动状态的时间。<br/><br/>例如，如果一个活动运行需要 6 分钟，而 timetolive 的设置是 5 分钟，则当 6 分钟的活动运行处理结束后，群集将保持 5 分钟的活动状态。 如果在这 6 分钟的时间内执行其他的活动运行，则由同一群集进行处理。<br/><br/>创建按需 HDInsight 群集是一项开销非常大的操作（可能会花费一定的时间），因此请根据需要使用此设置，以通过重复使用一个按需 HDInsight 群集来提高数据工厂的性能。<br/><br/>如果将 timetolive 值设置为 0，则群集在活动运行处理完后很快便会被删除。 另一方面，如果设置较高的值，群集可能会保持不必要的空闲状态，从而造成较高成本。 因此，根据具体需要设置适当的值非常重要。<br/><br/>如果设置了适当的 timetolive 属性值，多个管道则可以共享按需 HDInsight 群集的同一实例 |是 |
| 版本 |HDInsight 群集的版本。 对于 Windows 群集，默认值为 3.1；对于 Linux 群集，则为 3.2。 |否 |
| linkedServiceName |由按需群集用于存储和处理数据的 Azure 存储链接服务。 <p>目前，无法创建使用 Azure Data Lake Store 作为存储的按需 HDInsight 群集。 如果想要存储在 Azure Data Lake Store 中处理的来自 HDInsight 的结果数据，请使用复制活动将数据从 Azure Blob 存储复制到 Azure Data Lake Store。</p>  | 是 |
| additionalLinkedServiceNames |指定 HDInsight 链接服务的其他存储帐户，使数据工厂服务能够代为注册它们。 |否 |
| osType |操作系统的类型。 允许的值为：Windows（默认值）和 Linux |否 |
| hcatalogLinkedServiceName |指向 HCatalog 数据库的 Azure SQL 链接服务的名称。 将 Azure SQL 数据库用作元存储以创建按需 HDInsight 群集。 |否 |

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON 示例

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>高级属性
也可以为按需 HDInsight 群集的粒度配置指定以下属性。

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| coreConfiguration |为待创建的 HDInsight 群集指定核心配置参数（如在 core-site.xml 中）。 |否 |
| hBaseConfiguration |为 HDInsight 群集指定 HBase 配置参数 (hbase-site.xml)。 |否 |
| hdfsConfiguration |为 HDInsight 群集指定 HDFS 配置参数 (hdfs-site.xml)。 |否 |
| hiveConfiguration |为 HDInsight 群集指定 hive 配置参数 (hive-site.xml)。 |否 |
| mapReduceConfiguration |为 HDInsight 群集指定 MapReduce 配置参数 (mapred-site.xml)。 |否 |
| oozieConfiguration |为 HDInsight 群集指定 Oozie 配置参数 (oozie-site.xml)。 |否 |
| stormConfiguration |为 HDInsight 群集指定 Storm 配置参数 (storm-site.xml)。 |否 |
| yarnConfiguration |为 HDInsight 群集指定 Yarn 配置参数 (yarn-site.xml)。 |否 |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>示例 - 具有高级属性的按需 HDInsight 群集配置

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterSize": 16,
      "timeToLive": "01:30:00",
      "linkedServiceName": "adfods1",
      "coreConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "hiveConfiguration": {
        "templeton.mapper.memory.mb": "5000"
      },
      "mapReduceConfiguration": {
        "mapreduce.reduce.java.opts": "-Xmx4000m",
        "mapreduce.map.java.opts": "-Xmx4000m",
        "mapreduce.map.memory.mb": "5000",
        "mapreduce.reduce.memory.mb": "5000",
        "mapreduce.job.reduce.slowstart.completedmaps": "0.8"
      },
      "yarnConfiguration": {
        "yarn.app.mapreduce.am.resource.mb": "5000",
        "mapreduce.map.memory.mb": "5000"
      },
      "additionalLinkedServiceNames": [
        "datafeeds",
        "adobedatafeed"
      ]
    }
  }
}
```

### <a name="node-sizes"></a>节点大小
可使用以下属性指定头节点、数据节点和 Zookeeper 节点的大小： 

| 属性 | 说明 | 必选 |
|:--- |:--- |:--- |
| headNodeSize |指定头节点的大小。 默认值为：Standard_D3。 有关详细信息，请参阅下面的**指定节点大小**部分。 |否 |
| dataNodeSize |指定数据节点的大小。 默认值为：Standard_D3。 |否 |
| zookeeperNodeSize |指定 ZooKeeper 节点的大小。 默认值为：Standard_D3。 |否 |

#### <a name="specifying-node-sizes"></a>指定节点大小
若要了解需要为以上属性指定的字符串值，请参阅[虚拟机的大小](../virtual-machines/virtual-machines-linux-sizes.md?toc=%2fazure%2fdata-factory%2ftoc.json#size-tables)。 这些值需要符合文章中所引用的 **CMDLET 和 API**。 如文章中所示，大尺寸（默认）的数据节点拥有 7 GB 的内存，这可能无法满足具体方案的需求。 

如果想要创建 D4 大小的头节点和辅助进程节点，需要将 **Standard_D4** 指定为 headNodeSize 和 dataNodeSize 属性的值。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

如果为这些属性指定了错误的值，则可能会收到以下**错误：**未能创建群集。 异常: 无法完成群集创建操作。 操作失败，代码为 '400'。 群集保持为 'Error' 状态。 消息: “PreClusterCreationValidationFailure”。 收到此错误时，请确保使用的是上面文章中表格中的 **CMDLET 和 API** 名称。  

## <a name="bring-your-own-compute-environment"></a>自带计算环境
在此类型的配置中，用户可在数据工厂中将现有的计算环境注册为链接服务。 该计算环境由用户进行管理，数据工厂服务用它来执行活动。

此类型的配置支持以下计算环境：

* Azure HDInsight
* Azure 批处理
* Azure 机器学习
* Azure Data Lake Analytics
* Azure SQL DB、Azure SQL DW、SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 链接服务
可以创建 Azure HDInsight 链接服务，以向数据工厂注册自己的 HDInsight 群集。

### <a name="example"></a>示例

```json
{
  "name": "HDInsightLinkedService",
  "properties": {
    "type": "HDInsight",
    "typeProperties": {
      "clusterUri": " https://<hdinsightclustername>.azurehdinsight.net/",
      "userName": "admin",
      "password": "<password>",
      "linkedServiceName": "MyHDInsightStoragelinkedService"
    }
  }
}
```

### <a name="properties"></a>属性
| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **HDInsight**。 |是 |
| clusterUri |HDInsight 群集的 URI。 |是 |
| username |指定用于连接到现有 HDInsight 群集的用户的名称。 |是 |
| password |指定用户帐户的密码。 |是 |
| linkedServiceName | Azure 存储链接服务（指 HDInsight 群集使用的 Azure Blob 存储）的名称。 <p>目前，不能为此属性指定 Azure Data Lake Store 链接服务。 如果 HDInsight 群集有权访问 Data Lake Store，那么可以使用 Hive/Pig 脚本访问 Azure Data Lake Store 中的数据。 </p>  |是 |

## <a name="azure-batch-linked-service"></a>Azure Batch 链接服务
可以创建 Azure Batch 链接服务，以向数据工厂注册虚拟机 (VM) 的 Batch 池。 可以使用 Azure Batch 或 Azure HDInsight 运行 .NET 自定义活动。

如果不熟悉 Azure Batch 服务，请参阅以下主题：

* [Azure Batch 基础知识](../batch/batch-technical-overview.md) - Azure Batch 服务的概述。
* [New-azurebatchaccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet - 创建 Azure Batch 帐户（或）[Azure 门户](../batch/batch-account-create-portal.md) - 使用 Azure 门户创建 Azure Batch 帐户。 请参阅 [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)（使用 Azure PowerShell 管理 Azure Batch 帐户）主题，以了解有关使用此 cmdlet 的详细信息。
* [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet - 创建 Azure Batch 池。

### <a name="example"></a>示例

```json
{
  "name": "AzureBatchLinkedService",
  "properties": {
    "type": "AzureBatch",
    "typeProperties": {
      "accountName": "<Azure Batch account name>",
      "accessKey": "<Azure Batch account key>",
      "poolName": "<Azure Batch pool name>",
      "linkedServiceName": "<Specify associated storage linked service reference here>"
    }
  }
}
```

在 **accountName** 属性的批处理帐户名后追加 “**.\<region name\>**”。 示例：

```json
"accountName": "mybatchaccount.eastus"
```

另一种方法是提供 batchUri 终结点，如下所示。  

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>属性
| 属性 | 说明 | 必选 |
| --- | --- | --- |
| type |类型属性应设置为 **AzureBatch**。 |是 |
| accountName |Azure Batch 帐户的名称。 |是 |
| accessKey |Azure Batch 帐户的访问密钥。 |是 |
| poolName |虚拟机的池名称。 |是 |
| linkedServiceName |与此 Azure Batch 链接服务相关联的 Azure 存储链接服务的名称。 此链接服务用于暂存运行活动所需的文件，以及用于存储活动执行日志。 |是 |

## <a name="azure-machine-learning-linked-service"></a>Azure 机器学习链接服务
可以创建 Azure 机器学习链接服务，以向数据工厂注册机器学习批处理计分终结点。

### <a name="example"></a>示例

```json
{
  "name": "AzureMLLinkedService",
  "properties": {
    "type": "AzureML",
    "typeProperties": {
      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
      "apiKey": "<apikey>"
    }
  }
}
```

### <a name="properties"></a>属性
| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 类型 |类型属性应设置为 **AzureML**。 |是 |
| mlEndpoint |批处理计分 URL。 |是 |
| apiKey |已发布的工作区模型的 API。 |是 |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 链接服务
在使用管道中使用 [Data Lake Analytics U-SQL 活动](data-factory-usql-activity.md)之前，可以创建 **Azure Data Lake Analytics** 链接服务，将 Azure Data Lake Analytics 计算服务链接到 Azure 数据工厂。

以下示例为 Azure Data Lake Analytics 链接服务提供 JSON 定义。

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "authorization": "<authcode>",
            "sessionId": "<session ID>",
            "subscriptionId": "<subscription id>",
            "resourceGroupName": "<resource group name>"
        }
    }
}
```

下表为 JSON 定义中使用的属性提供了描述。

| 属性 | 说明 | 必选 |
| --- | --- | --- |
| 类型 |类型属性应设置为 **AzureDataLakeAnalytics**。 |是 |
| accountName |Azure Data Lake Analytics 帐户名。 |是 |
| dataLakeAnalyticsUri |Azure Data Lake Analytics URI。 |否 |
| authorization |在数据工厂编辑器中单击“授权”按钮并完成 OAuth 登录后，将自动检索授权代码。 |是 |
| subscriptionId |Azure 订阅 ID |否（如果未指定，则使用数据工厂的订阅）。 |
| resourceGroupName |Azure 资源组名称 |否（如果未指定，则使用数据工厂的资源组）。 |
| sessionId |OAuth 授权会话中的会话 ID。 每个会话 ID 都是唯一的，并且可能仅可使用一次。 此 ID 在数据工厂编辑器中自动生成。 |是 |

使用“授权”按钮生成的授权代码在一段时间后便会过期。 请参阅下表，了解不同类型用户帐户的过期时间。 身份验证**令牌过期**时可能会看到以下错误消息：凭据操作错误: invalid_grant-AADSTS70002: 验证凭据时出错。 AADSTS70008：提供的访问权限已过期或已被吊销。 跟踪 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相关 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 时间戳: 2015-12-15 21:09:31Z

| 用户类型 | 过期时间 |
|:--- |:--- |
| 不由 Azure Active Directory 管理的用户帐户（例如 @hotmail.com、@live.com、@outlook.com） |12 小时 |
| 由 Azure Active Directory (AAD) 管理的用户帐户 |最后一次运行切片后的&14; 天。 <br/><br/>如果以基于 OAuth 的链接服务为基础的切片每 14 天至少运行一次，则为 90 天。 |

若要避免/解决此错误，需要在**令牌过期**时使用“授权”按钮重新授权，并重新部署链接服务。 还可使用以下部分中的代码以编程方式生成 sessionId 和 authorization 属性的值。 

### <a name="to-programmatically-generate-sessionid-and-authorization-values"></a>以编程方式生成 sessionId 和 authorization 值
以下代码生成 **sessionId** 和 **authorization** 值。  

```CSharp

if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
    linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
{
    AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

    WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
    string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

    AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
    if (azureDataLakeStoreProperties != null)
    {
        azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeStoreProperties.Authorization = authorization;
    }

    AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
    if (azureDataLakeAnalyticsProperties != null)
    {
        azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
        azureDataLakeAnalyticsProperties.Authorization = authorization;
    }
}
```

请参阅 [AzureDataLakeStoreLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)、[AzureDataLakeAnalyticsLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)和 [AuthorizationSessionGetResponse 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)主题，了解关于代码中使用的数据工厂类的详细信息。 需要为 WindowsFormsWebAuthenticationDialog 类添加对 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll 的引用。 

## <a name="azure-sql-linked-service"></a>Azure SQL 链接服务
创建 Azure SQL 链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL 数据仓库链接服务
创建 Azure SQL 数据仓库链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 请参阅[Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="sql-server-linked-service"></a>SQL Server 链接服务
创建 SQL Server 链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#sql-server-linked-service-properties)一文，以了解此链接服务的详细信息。


