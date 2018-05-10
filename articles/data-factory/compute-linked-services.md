---
title: Azure 数据工厂支持的计算环境 | Microsoft Docs
description: 了解可在 Azure 数据工厂管道（例如 Azure HDInsight）中用于转换或处理数据的计算环境。
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: 6f9f0f9a9bab7e6865ae5a48552ac702ae2bf6fb
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure 数据工厂支持的计算环境
本文介绍可用于处理或转换数据的不同计算环境。 同时还详细介绍了配置将这些计算环境链接到 Azure 数据工厂的链接服务时，数据工厂所支持的不同配置（按需和自带）。

下表提供了数据工厂所支持的计算环境以及可以在其上运行的活动的列表。 

| 计算环境                      | 活动                               |
| ---------------------------------------- | ---------------------------------------- |
| [按需 HDInsight 群集](#azure-hdinsight-on-demand-linked-service)或[自己的 HDInsight 群集](#azure-hdinsight-linked-service) | [Hive](transform-data-using-hadoop-hive.md)、[Pig](transform-data-using-hadoop-pig.md)、[Spark](transform-data-using-spark.md)、[MapReduce](transform-data-using-hadoop-map-reduce.md)、[Hadoop Streaming](transform-data-using-hadoop-streaming.md) |
| [Azure Batch](#azure-batch-linked-service) | [自定义](transform-data-using-dotnet-custom-activity.md) |
| [Azure 机器学习](#azure-machine-learning-linked-service) | [机器学习活动：批处理执行和更新资源](transform-data-using-machine-learning.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md) |
| [Azure SQL](#azure-sql-database-linked-service)、[Azure SQL 数据仓库](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [存储过程](transform-data-using-stored-procedure.md) |

>  

## <a name="on-demand-compute-environment"></a>按需计算环境
在此类型的配置中，计算环境由 Azure 数据工厂服务完全管理。 作业提交到进程数据前，数据工厂服务会自动创建计算环境，作业完成后则自动将其删除。 可以为按需计算环境创建并配置链接服务，以及控制作业执行、群集管理和引导操作的粒度设置。

> [!NOTE]
> 按需配置目前仅支持 Azure HDInsight 群集。
>
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 按需链接服务
Azure 数据工厂服务可自动创建按需 HDInsight 群集，以处理数据。 群集创建在与该群集相关联的存储帐户（JSON 中的 linkedServiceName 属性）所在的同一区域中。 存储帐户必须是通用的标准 Azure 存储帐户。 

请注意以下关于按需 HDInsight 链接服务的**重要**事项：

* 按需 HDInsight 群集在 Azure 订阅下创建。 当群集启动并运行时，你可以在 Azure 门户中查看群集。 
* 在按需 HDInsight 群集上运行的作业日志将复制到与 HDInsight 群集相关联的存储帐户。 在链接的服务定义中定义的 clusterUserName、clusterPassword、clusterSshUserName、clusterSshPassword 用于登录到群集，以便在群集的生命周期内进行深入故障排除。 
* 仅对 HDInsight 群集启动并运行作业的时间进行收费。

> [!IMPORTANT]
> 按需预配 Azure HDInsight 群集通常需要 **20 分钟**或更长时间。
>
> 

### <a name="example"></a>示例
以下 JSON 定义基于 Linux 的按需 HDInsight 链接服务。 数据工厂服务自动创建基于 Linux 的 HDInsight 群集来处理所需的活动。 

```json
{
  "name": "HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "clusterType": "hadoop",
      "clusterSize": 1,
      "timeToLive": "00:15:00",
      "hostSubscriptionId": "<subscription ID>",
      "servicePrincipalId": "<service principal ID>",
      "servicePrincipalKey": {
        "value": "<service principal key>",
        "type": "SecureString"
      },
      "tenant": "<tenent id>",
      "clusterResourceGroup": "<resource group name>",
      "version": "3.6",
      "osType": "Linux",
      "linkedServiceName": {
        "referenceName": "AzureStorageLinkedService",
        "type": "LinkedServiceReference"
      }
    },
    "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
  }
}
```

> [!IMPORTANT]
> HDInsight 群集在 JSON 中指定的 Blob 存储 (**linkedServiceName**).内创建**默认容器**。 HDInsight 不会在删除群集时删除此容器。 这是设计的行为。 使用按需 HDInsight 链接服务时，除非有现有的实时群集 (**timeToLive**)，否则每当需要处理切片时会创建 HDInsight 群集；并在处理完成后删除该群集。 
>
> 随着运行的活动越来越多，Azure Blob 存储中会出现大量的容器。 如果不需要使用它们对作业进行故障排除，则可能需要删除它们以降低存储成本。 这些容器的名称遵循 `adf**yourdatafactoryname**-**linkedservicename**-datetimestamp` 模式。 使用 [Microsoft 存储资源管理器](http://storageexplorer.com/) 等工具删除 Azure Blob 存储中的容器。
>
> 

### <a name="properties"></a>属性
| 属性                     | 说明                              | 必选 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | 类型属性应设置为 **HDInsightOnDemand**。 | 是      |
| clusterSize                  | 群集中辅助进程/数据节点的数量。 HDInsight 群集创建时具有 2 个头节点以及一定数量的辅助进程节点（此节点的数量是为此属性所指定的数量）。 这些节点的大小为拥有 4 个核心的 Standard_D3，因此一个具有 4 个辅助节点的群集拥有 24 个核心（辅助节点有 4\*4 = 16 个核心，头节点有 2\*4 = 8 个核心）。 请参阅[使用 Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)，了解详细信息。 | 是      |
| linkedServiceName            | 由按需群集用于存储和处理数据的 Azure 存储链接服务。 HDInsight 群集在创建时与此 Azure 存储帐户位于同一区域。 Azure HDInsight 会限制可在其支持的每个 Azure 区域中使用的核心总数。 确保在 Azure 区域中有足够的内核配额来满足所需的 clusterSize。 有关详细信息，请参阅[使用 Hadoop、Spark、Kafka 等在 HDInsight 中设置群集](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)<p>目前，无法创建使用 Azure Data Lake Store 作为存储的按需 HDInsight 群集。 如果想要存储在 Azure Data Lake Store 中处理的来自 HDInsight 的结果数据，请使用复制活动将数据从 Azure Blob 存储复制到 Azure Data Lake Store。 </p> | 是      |
| clusterResourceGroup         | 在此资源组中创建 HDInsight 群集。 | 是      |
| timetolive                   | 按需 HDInsight 群集允许的空闲时间。 指定当活动运行完成后，如果群集中没有其他的活动作业，按需 HDInsight 群集保持活动状态的时间。 允许的最小值为 5 分钟 (00: 05:00)。<br/><br/>例如，如果一个活动运行需要 6 分钟，而 timetolive 的设置是 5 分钟，则当 6 分钟的活动运行处理结束后，群集将保持 5 分钟的活动状态。 如果在这 6 分钟的时间内执行其他的活动运行，则由同一群集进行处理。<br/><br/>创建按需 HDInsight 群集是一项开销非常大的操作（可能会花费一定的时间），因此请根据需要使用此设置，以通过重复使用一个按需 HDInsight 群集来提高数据工厂的性能。<br/><br/>如果将 timetolive 值设置为 0，则将会在活动运行处理完后立即删除群集。 然而，如果设置了较高的值，则群集可能会保持空闲状态，以方便你登录进行某些故障排除工作，但这可能会导致成本高昂。 因此，根据具体需要设置适当的值非常重要。<br/><br/>如果 timetolive 属性值设置适当，多个管道则可共享按需 HDInsight 群集实例。 | 是      |
| clusterType                  | 要创建的 HDInsight 群集的类型。 允许的值是“hadoop”和“spark”。 如果未指定，默认值为 hadoop。 | 否       |
| 版本                      | HDInsight 群集的版本。 如果未指定较高的值，则使用当前 HDInsight 定义的默认版本。 | 否       |
| hostSubscriptionId           | 用于创建 HDInsight 群集的 Azure 订阅 ID。 如果未指定，则使用 Azure 登录上下文的订阅 ID。 | 否       |
| clusterNamePrefix           | HDI 群集名称的前缀，将自动在群集名称末尾追加时间戳| 否       |
| sparkVersion                 | 群集类型为“Spark”时的 spark 版本 | 否       |
| additionalLinkedServiceNames | 指定 HDInsight 链接服务的其他存储帐户，使数据工厂服务能够代为注册它们。 这些存储帐户必须与 HDInsight 群集位于同一区域中，该群集是在与 linkedServiceName 指定的存储帐户相同的区域中创建的。 | 否       |
| osType                       | 操作系统的类型。 允许的值为：Linux 和 Windows（仅适用于 HDInsight 3.3）。 默认值为 Linux。 | 否       |
| hcatalogLinkedServiceName    | 指向 HCatalog 数据库的 Azure SQL 链接服务的名称。 将 Azure SQL 数据库用作元存储以创建按需 HDInsight 群集。 | 否       |
| connectVia                   | 用于将活动分发到此 HDInsight 链接服务的集成运行时。 对于按需的 HDInsight 链接服务，它仅支持 Azure 集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否       |
| clusterUserName                   | 用于访问群集的用户名。 | 否       |
| clusterPassword                   | 用于访问群集的安全字符串类型密码。 | 否       |
| clusterSshUserName         | 用于 SSH 远程连接到群集节点的用户名（适用于 Linux）。 | 否       |
| clusterSshPassword         | 用于 SSH 远程连接到群集节点的安全字符串类型密码（适用于 Linux）。 | 否       |


> [!IMPORTANT]
> HDInsight 支持多个可部署的 Hadoop 群集版本。 每个版本选项创建 Hortonworks 数据平台 (HDP) 分发的特定版本和该分发内包含的一组组件。 支持的 HDInsight 版本列表会不断更新，以提供最新的 Hadoop 生态系统组件和修补程序。 请确保始终参考[支持的 HDInsight 版本和操作系统类型](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)的最新信息，以确保使用的是受支持的 HDInsight 版本。 
>
> 

#### <a name="additionallinkedservicenames-json-example"></a>additionalLinkedServiceNames JSON 示例

```json
"additionalLinkedServiceNames": [{
    "referenceName": "MyStorageLinkedService2",
    "type": "LinkedServiceReference"          
}]
```

### <a name="service-principal-authentication"></a>服务主体身份验证

按需 HDInsight 链接服务要求进行服务主体身份验证，以代表你创建 HDInsight 群集。 要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册应用程序实体，并向其授予在其中创建 HDInsight 群集的订阅或资源组的“参与者”角色。 有关详细步骤，请参阅[使用门户创建可访问资源的 Azure Active Directory 应用程序和服务主体](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-create-service-principal-portal)。 记下下面的值，这些值用于定义链接服务：

- 应用程序 ID
- 应用程序密钥 
- 租户 ID

通过指定以下属性使用服务主体身份验证：

| 属性                | 说明                              | 必选 |
| :---------------------- | :--------------------------------------- | :------- |
| **servicePrincipalId**  | 指定应用程序的客户端 ID。     | 是      |
| **servicePrincipalKey** | 指定应用程序的密钥。           | 是      |
| **tenant**              | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是      |

### <a name="advanced-properties"></a>高级属性

也可以为按需 HDInsight 群集的粒度配置指定以下属性。

| 属性               | 说明                              | 必选 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 为待创建的 HDInsight 群集指定核心配置参数（如在 core-site.xml 中）。 | 否       |
| hBaseConfiguration     | 为 HDInsight 群集指定 HBase 配置参数 (hbase-site.xml)。 | 否       |
| hdfsConfiguration      | 为 HDInsight 群集指定 HDFS 配置参数 (hdfs-site.xml)。 | 否       |
| hiveConfiguration      | 为 HDInsight 群集指定 hive 配置参数 (hive-site.xml)。 | 否       |
| mapReduceConfiguration | 为 HDInsight 群集指定 MapReduce 配置参数 (mapred-site.xml)。 | 否       |
| oozieConfiguration     | 为 HDInsight 群集指定 Oozie 配置参数 (oozie-site.xml)。 | 否       |
| stormConfiguration     | 为 HDInsight 群集指定 Storm 配置参数 (storm-site.xml)。 | 否       |
| yarnConfiguration      | 为 HDInsight 群集指定 Yarn 配置参数 (yarn-site.xml)。 | 否       |

#### <a name="example--on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>示例 - 具有高级属性的按需 HDInsight 群集配置

```json
{
    "name": " HDInsightOnDemandLinkedService",
    "properties": {
      "type": "HDInsightOnDemand",
      "typeProperties": {
          "clusterSize": 16,
          "timeToLive": "01:30:00",
          "hostSubscriptionId": "<subscription ID>",
          "servicePrincipalId": "<service principal ID>",
          "servicePrincipalKey": {
            "value": "<service principal key>",
            "type": "SecureString"
          },
          "tenant": "<tenent id>",
          "clusterResourceGroup": "<resource group name>",
          "version": "3.6",
          "osType": "Linux",
          "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
            },
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
            "additionalLinkedServiceNames": [{
                "referenceName": "MyStorageLinkedService2",
                "type": "LinkedServiceReference"          
            }]
        }
    },
      "connectVia": {
      "referenceName": "<name of Integration Runtime>",
      "type": "IntegrationRuntimeReference"
    }
}
```

### <a name="node-sizes"></a>节点大小
可使用以下属性指定头节点、数据节点和 Zookeeper 节点的大小： 

| 属性          | 说明                              | 必选 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | 指定头节点的大小。 默认值为：Standard_D3。 有关详细信息，请参阅**指定节点大小**部分。 | 否       |
| dataNodeSize      | 指定数据节点的大小。 默认值为：Standard_D3。 | 否       |
| zookeeperNodeSize | 指定 ZooKeeper 节点的大小。 默认值为：Standard_D3。 | 否       |

#### <a name="specifying-node-sizes"></a>指定节点大小
若要了解需要为在上面的部分中提到的属性指定的字符串值，请参阅[虚拟机的大小](../virtual-machines/linux/sizes.md)。 这些值需要符合文章中所引用的 **CMDLET 和 API**。 如文章中所示，大尺寸（默认）的数据节点拥有 7 GB 的内存，这可能无法满足具体方案的需求。 

如果想要创建 D4 大小的头节点和辅助进程节点，请将 **Standard_D4** 指定为 headNodeSize 和 dataNodeSize 属性的值。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

如果为这些属性指定了错误的值，则可能会收到以下**错误：**未能创建群集。 异常: 无法完成群集创建操作。 操作失败，代码为 '400'。 群集保持为 'Error' 状态。 消息: “PreClusterCreationValidationFailure”。 收到此错误时，请确保使用的是[虚拟机的大小](../virtual-machines/linux/sizes.md)一文中的表中的 **CMDLET 和 API** 名称。        

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
        "userName": "username",
        "password": {
            "value": "passwordvalue",
            "type": "SecureString"
          },
        "linkedServiceName": {
              "referenceName": "AzureStorageLinkedService",
              "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```

### <a name="properties"></a>属性
| 属性          | 说明                              | 必选 |
| ----------------- | ---------------------------------------- | -------- |
| type              | 类型属性应设置为 **HDInsight**。 | 是      |
| clusterUri        | HDInsight 群集的 URI。        | 是      |
| username          | 指定用于连接到现有 HDInsight 群集的用户的名称。 | 是      |
| password          | 指定用户帐户的密码。   | 是      |
| linkedServiceName | Azure 存储链接服务（指 HDInsight 群集使用的 Azure Blob 存储）的名称。 <p>目前，不能为此属性指定 Azure Data Lake Store 链接服务。 如果 HDInsight 群集有权访问 Data Lake Store，则可从 Hive/Pig 脚本访问 Azure Data Lake Store 中的数据。 </p> | 是      |
| connectVia        | 用于将活动分发到此链接服务的集成运行时。 可以使用 Azure 集成运行时或自托管集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否       |

> [!IMPORTANT]
> HDInsight 支持多个可部署的 Hadoop 群集版本。 每个版本选项创建 Hortonworks 数据平台 (HDP) 分发的特定版本和该分发内包含的一组组件。 支持的 HDInsight 版本列表会不断更新，以提供最新的 Hadoop 生态系统组件和修补程序。 请确保始终参考[支持的 HDInsight 版本和操作系统类型](../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)的最新信息，以确保使用的是受支持的 HDInsight 版本。 
>

## <a name="azure-batch-linked-service"></a>Azure Batch 链接服务

可以创建 Azure Batch 链接服务，以向数据工厂注册虚拟机 (VM) 的 Batch 池。 你可以使用 Azure Batch 运行自定义活动。

如果不熟悉 Azure Batch 服务，请参阅以下主题：

* [Azure Batch 基础知识](../batch/batch-technical-overview.md) - Azure Batch 服务的概述。
* [New-AzureRmBatchAccount](/powershell/module/azurerm.batch/New-AzureRmBatchAccount?view=azurermps-4.3.1) cmdlet - 创建 Azure Batch 帐户（或）[Azure 门户](../batch/batch-account-create-portal.md) - 使用 Azure 门户创建 Azure Batch 帐户。 请参阅 [Using PowerShell to manage Azure Batch Account](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)（使用 Azure PowerShell 管理 Azure Batch 帐户）主题，以了解有关使用此 cmdlet 的详细信息。
* [New-AzureBatchPool](/powershell/module/azurerm.batch/New-AzureBatchPool?view=azurermps-4.3.1) cmdlet - 创建 Azure Batch 池。

### <a name="example"></a>示例

```json
{
    "name": "AzureBatchLinkedService",
    "properties": {
      "type": "AzureBatch",
      "typeProperties": {
        "accountName": "batchaccount",
        "accessKey": {
          "type": "SecureString",
          "value": "access key"
        },
        "batchUri": "https://batchaccount.region.batch.azure.com",
        "poolName": "poolname",
        "linkedServiceName": {
          "referenceName": "StorageLinkedService",
          "type": "LinkedServiceReference"
        }
      },
      "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
  }
```


### <a name="properties"></a>属性
| 属性          | 说明                              | 必选 |
| ----------------- | ---------------------------------------- | -------- |
| type              | 类型属性应设置为 **AzureBatch**。 | 是      |
| accountName       | Azure Batch 帐户的名称。         | 是      |
| accessKey         | Azure Batch 帐户的访问密钥。  | 是      |
| batchUri          | 指向 Azure Batch 帐户的 URL，格式为 https://*batchaccountname.region*.batch.azure.com。 | 是      |
| poolName          | 虚拟机的池名称。    | 是      |
| linkedServiceName | 与此 Azure Batch 链接服务相关联的 Azure 存储链接服务的名称。 此链接服务用于暂存运行活动所需的文件。 | 是      |
| connectVia        | 用于将活动分发到此链接服务的集成运行时。 可以使用 Azure 集成运行时或自托管集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否       |

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
        "apiKey": {
            "type": "SecureString",
            "value": "access key"
        }
     },
     "connectVia": {
        "referenceName": "<name of Integration Runtime>",
        "type": "IntegrationRuntimeReference"
      }
    }
}
```

### <a name="properties"></a>属性
| 属性               | 说明                              | 必选                                 |
| ---------------------- | ---------------------------------------- | ---------------------------------------- |
| Type                   | 类型属性应设置为 **AzureML**。 | 是                                      |
| mlEndpoint             | 批处理计分 URL。                   | 是                                      |
| apiKey                 | 已发布的工作区模型的 API。     | 是                                      |
| updateResourceEndpoint | Azure ML Web 服务终结点的更新资源 URL 用于使用经过训练的模型文件更新预测性 Web 服务 | 否                                       |
| servicePrincipalId     | 指定应用程序的客户端 ID。     | 如果已指定 updateResourceEndpoint，则为必需 |
| servicePrincipalKey    | 指定应用程序的密钥。           | 如果已指定 updateResourceEndpoint，则为必需 |
| tenant                 | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 如果已指定 updateResourceEndpoint，则为必需 |
| connectVia             | 用于将活动分发到此链接服务的集成运行时。 可以使用 Azure 集成运行时或自托管集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否                                       |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 链接服务
创建 **Azure Data Lake Analytics** 链接服务将 Azure Data Lake Analytics 计算服务链接到 Azure 数据工厂。 管道中的 Data Lake Analytics U-SQL 活动是指此链接服务。 

### <a name="example"></a>示例

```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "azuredatalakeanalytics URI",
            "servicePrincipalId": "service principal id",
            "servicePrincipalKey": {
                "value": "service principal key",
                "type": "SecureString"
            },
            "tenant": "tenant ID",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

### <a name="properties"></a>属性

| 属性             | 说明                              | 必选                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| type                 | 类型属性应设置为 **AzureDataLakeAnalytics**。 | 是                                      |
| accountName          | Azure Data Lake Analytics 帐户名。  | 是                                      |
| dataLakeAnalyticsUri | Azure Data Lake Analytics URI。           | 否                                       |
| subscriptionId       | Azure 订阅 ID                    | 否（如果未指定，则使用数据工厂的订阅）。 |
| resourceGroupName    | Azure 资源组名称                | 否（如果未指定，则使用数据工厂的资源组）。 |
| servicePrincipalId   | 指定应用程序的客户端 ID。     | 是                                      |
| servicePrincipalKey  | 指定应用程序的密钥。           | 是                                      |
| tenant               | 指定应用程序的租户信息（域名或租户 ID）。 可将鼠标悬停在 Azure 门户右上角进行检索。 | 是                                      |
| connectVia           | 用于将活动分发到此链接服务的集成运行时。 可以使用 Azure 集成运行时或自托管集成运行时。 如果未指定，则使用默认 Azure 集成运行时。 | 否                                       |



## <a name="azure-databricks-linked-service"></a>Azure Databricks 链接服务
可以通过创建 **Azure Databricks 链接服务**来注册 Databricks 工作区，该工作区将用于运行 Databricks 工作负荷 (Notebook)。

### <a name="example---using-new-job-cluster-in-databricks"></a>示例 - 在 Databricks 中使用新的作业群集

```json
{
    "name": "AzureDatabricks_LS",
    "properties": {
        "type": "AzureDatabricks",
        "typeProperties": {
            "domain": "eastus.azuredatabricks.net",
            "newClusterNodeType": "Standard_D3_v2",
            "newClusterNumOfWorker": "1:10",
            "newClusterVersion": "4.0.x-scala2.11",
            "accessToken": {
                "type": "SecureString",
                "value": "dapif33c9c721144c3a790b35000b57f7124f"
            }
        }
    }
}

```

### <a name="example---using-existing-interactive-cluster-in-databricks"></a>示例 - 在 Databricks 中使用现有的交互式群集

```json
{
    "name": " AzureDataBricksLinedService",
    "properties": {
      "type": " AzureDatabricks",
      "typeProperties": {
        "domain": "https://westeurope.azuredatabricks.net",
        "accessToken": {
            "type": "SecureString", 
            "value": "dapif33c9c72344c3a790b35000b57f7124f"
          },
        "existingClusterId": "{clusterId}"
        }
}

```

### <a name="properties"></a>属性

| 属性             | 说明                              | 必选                                 |
| -------------------- | ---------------------------------------- | ---------------------------------------- |
| 名称                 | 链接服务的名称               | 是   |
| type                 | 类型属性应设置为 **AzureDatabricks**。 | 是                                      |
| 域               | 根据 Databricks 工作区的区域相应地指定 Azure 区域。 示例：https://eastus.azuredatabricks.net | 是                                 |
| accessToken          | 数据工厂通过 Azure Databricks 进行身份验证时，必须使用访问令牌。 需从 Databricks 工作区生成访问令牌。 [此处](https://docs.azuredatabricks.net/api/latest/authentication.html#generate-token)提供了查找访问令牌的更多详细步骤  | 是                                       |
| existingClusterId    | 现有群集的群集 ID，用于在其上运行所有作业。 该群集应该是已创建的交互式群集。 如果群集停止响应，则可能需要手动重启该群集。 Databricks 建议在新群集上运行作业，以提高可靠性。 可以通过 Databricks 工作区 ->“群集”->“交互式群集名称”->“配置”->“标记”找到交互式群集的群集 ID。 [更多详细信息](https://docs.databricks.com/user-guide/clusters/tags.html) | 否 
| newClusterVersion    | 群集的 Spark 版本。 它会在 Databricks 中创建作业群集。 | 否  |
| newClusterNumOfWorker| 此群集应该拥有的工作节点的数目。 群集有一个 Spark 驱动程序和 num_workers 个执行器，总共有 num_workers + 1 个 Spark 节点。 字符串格式的 Int32，例如“1”是指 numOfWorker 为 1，“1:10”是指自动缩放的范围为 1 到 10。  | 否                |
| newClusterNodeType   | 此字段通过单个值对提供给此群集中的每个 Spark 节点的资源进行编码。 例如，可以针对内存或计算密集型工作负荷对 Spark 节点进行预配和优化。此字段是新群集必需的                | 否               |
| newClusterSparkConf  | 一组可选的、用户指定的 Spark 配置键值对。 用户还可以分别通过 spark.driver.extraJavaOptions 和 spark.executor.extraJavaOptions 将包含额外 JVM 选项的字符串传递给驱动程序和执行器。 | 否  |


## <a name="azure-sql-database-linked-service"></a>Azure SQL 数据库链接服务
创建 Azure SQL 链接服务，并将其与[存储过程活动](transform-data-using-stored-procedure.md)配合使用，以从数据工厂管道调用存储过程。 请参阅 [Azure SQL 连接器](connector-azure-sql-database.md#linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL 数据仓库链接服务
创建 Azure SQL 数据仓库链接服务，并将其与[存储的过程活动](transform-data-using-stored-procedure.md)配合使用，以从数据工厂管道调用存储的过程。 请参阅[Azure SQL 数据仓库连接器](connector-azure-sql-data-warehouse.md#linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="sql-server-linked-service"></a>SQL Server 链接服务
创建 SQL Server 链接服务，并将其与[存储的过程活动](transform-data-using-stored-procedure.md)配合使用，以从数据工厂管道调用存储的过程。 请参阅 [SQL Server 连接器](connector-sql-server.md#linked-service-properties)一文，以了解此链接服务的详细信息。

## <a name="azure-data-factory---naming-rules"></a>Azure 数据工厂 - 命名规则
下表提供了数据工厂项目的命名规则。

| 名称                             | 名称唯一性                          | 验证检查                        |
| :------------------------------- | :--------------------------------------- | :--------------------------------------- |
| 数据工厂                     | 在 Microsoft Azure 内唯一。 名称不区分大小写，即 `MyDF` 和 `mydf` 指的是同一个数据工厂。 | <ul><li>一个数据工厂绑定到一个 Azure 订阅。</li><li>对象名称必须以字母或数字开头，并且只能包含字母、数字和短划线 (-) 字符。</li><li>每个短划线 (-) 字符的前后必须紧接字母或数字。 容器名称中不允许使用连续短划线。</li><li>名称长度为 3-63 个字符。</li></ul> |
| 链接服务/表/管道 | 在数据工厂中唯一。 名称不区分大小写。 | <ul><li>表名称的最大字符数：260。</li><li>对象名称必须以字母、数字或下划线 (_) 开头。</li><li>不允许使用以下字符：“.”、“+”、“?”、“/”、“<”、“>”、“ * ”、“%”、“&”、“:”、“\\”</li></ul> |
| 资源组                   | 在 Microsoft Azure 内唯一。 名称不区分大小写。 | <ul><li>最大字符数：1000。</li><li>名称可包含字母、数字和以下字符：“-”、“_”、“,”和“.”</li></ul> |

## <a name="next-steps"></a>后续步骤
有关 Azure 数据工厂支持的数据转换活动的列表，请参阅[转换数据](transform-data.md)。
