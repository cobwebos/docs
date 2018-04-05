---
title: Azure 数据工厂支持的计算环境 | Microsoft Docs
description: 了解可在 Azure 数据工厂管道（例如 Azure HDInsight）中用于转换或处理数据的计算环境。
services: data-factory
documentationcenter: ''
author: sharonlo101
manager: craigg
ms.assetid: 6877a7e8-1a58-4cfb-bbd3-252ac72e4145
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2018
ms.author: shlo
robots: noindex
ms.openlocfilehash: 09568dcbbec90bcba2f2782072b83cc04d9e8a87
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/23/2018
---
# <a name="compute-environments-supported-by-azure-data-factory"></a>Azure 数据工厂支持的计算环境
> [!NOTE]
> 本文适用于 Azure 数据工厂版本 1（即正式版 (GA)）。 如果使用数据工厂服务版本 2（预览版），请参阅[版本 2 中的计算链接服务](../compute-linked-services.md)。

本文介绍可用于处理或转换数据的计算环境。 同时还详细介绍了配置将这些计算环境链接到 Azure 数据工厂的链接服务时，数据工厂所支持的不同配置（按需和自带）。

下表提供了数据工厂所支持的计算环境以及可以在其上运行的活动的列表。 

| 计算环境                      | 活动                               |
| ---------------------------------------- | ---------------------------------------- |
| [按需 Azure HDInsight 群集](#azure-hdinsight-on-demand-linked-service)或[自己的 HDInsight 群集](#azure-hdinsight-linked-service) | [DotNet](data-factory-use-custom-activities.md)、[Hive](data-factory-hive-activity.md)、[Pig](data-factory-pig-activity.md)、[MapReduce](data-factory-map-reduce.md)、[Hadoop 流式处理](data-factory-hadoop-streaming-activity.md) |
| [Azure Batch](#azure-batch-linked-service) | [DotNet](data-factory-use-custom-activities.md) |
| [Azure 机器学习](#azure-machine-learning-linked-service) | [机器学习活动：批处理执行和更新资源](data-factory-azure-ml-batch-execution-activity.md) |
| [Azure Data Lake Analytics](#azure-data-lake-analytics-linked-service) | [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |
| [Azure SQL](#azure-sql-linked-service)、[Azure SQL 数据仓库](#azure-sql-data-warehouse-linked-service)、[SQL Server](#sql-server-linked-service) | [存储过程活动](data-factory-stored-proc-activity.md) |

## <a name="supported-hdinsight-versions-in-azure-data-factory"></a>数据工厂支持的 HDInsight 版本
Azure HDInsight 支持多个可随时部署的 Hadoop 群集版本。 每个支持的版本创建 Hortonworks 数据平台 (HDP) 分发版的特定版本，以及该分发版中的一组组件。 

Microsoft 会不断更新支持的 HDInsight 版本，以及最新的 Hadoop 生态系统组件和修复程序列表。 有关详细信息，请参阅[支持的 HDInsight 版本](../../hdinsight/hdinsight-component-versioning.md#supported-hdinsight-versions)。

> [!IMPORTANT]
> 基于 Linux 的 HDInsight 版本 3.3 已在 2017 年 7 月 31 日停用。 数据工厂版本 1 按需 HDInsight 链接服务的客户需在 2017 年 12 月 15 日前测试并升级到更高版本的 HDInsight。 基于 Windows 的 HDInsight 将在 2018 年 7 月 31 日停用。
>
> 

### <a name="after-the-retirement-date"></a>在停用日期之后 

2017 年 12 月 15日后：

- 客户不再能够使用数据工厂版本 1 中的按需 HDInsight 链接服务创建基于 Linux 的 HDInsight 版本 3.3（或更低版本）的群集。 
- 如果未在现有数据工厂版本 1 按需 HDInsight 链接服务的 JSON 定义中显式指定 [osType 和 Version 属性](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)，则默认值将 从“Version=3.1, osType=Windows”更改为“Version=\<最新 HDI 默认版本\>(https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions), osType=Linux”。

2018 年 7 月 31 日后：

- 客户不再能够使用 Azure 数据工厂版本 1 中的按需 HDInsight 链接服务创建任何版本的基于 Windows 的 HDInsight 群集。 

### <a name="recommended-actions"></a>建议的操作 

- 为确保可以使用最新的 Hadoop 生态系统组件和修复程序，请将受影响 Azure 数据工厂版本 1 按需 HDInsight 链接服务定义的 [**osType** 和 **Version** 属性](https://docs.microsoft.com/azure/data-factory/v1/data-factory-compute-linked-services#azure-hdinsight-on-demand-linked-service)更新到较新的基于 Linux 的 HDInsight 版本 (HDInsight 3.6)。 
- 在 2017 年 12 月 15 日之前，请测试引用受影响链接服务的数据工厂版本 1 Hive、Pig、MapReduce 和 Hadoop 流活动。 确保它们与新的 **osType** 和 **Version** 默认值 (**Version=3.6**, **osType=Linux**) 或者要升级到的显式 HDInsight 版本和 OS 类型兼容。 
  若要了解有关兼容性的详细信息，请参阅[从基于 Windows 的 HDInsight 群集迁移到基于 Linux 的群集](https://docs.microsoft.com/azure/hdinsight/hdinsight-migrate-from-windows-to-linux)和 [HDInsight 提供了哪些 Hadoop 组件和版本？](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hortonworks-release-notes-associated-with-hdinsight-versions)。 
- 若要继续使用数据工厂版本 1 按需 HDInsight 链接服务创建基于 Windows 的 HDInsight 群集，请在 2017 年 12 月 15 日之前将 **osType** 显式设置为 **Windows**。 我们建议在 2018 年 7 月 31 日之前迁移到基于 Linux 的 HDInsight 群集。 
- 如果目前使用按需 HDInsight 链接服务执行数据工厂版本 1 DotNet 自定义活动，请更新 DotNet 自定义活动的 JSON 定义，以改用 Azure Batch 链接服务。 有关详细信息，请参阅[在数据工厂管道中使用自定义活动](https://docs.microsoft.com/azure/data-factory/v1/data-factory-use-custom-activities)。 

> [!Note]
> 如果在数据工厂版本 1 中使用现有的自带群集 HDInsight 链接设备，或者在 Azure 数据工厂版本 2 中使用自带和按需的 HDInsight 链接服务，则不需要执行任何操作。 在这些情况下，已实施最新版本的 HDInsight 群集支持策略。 
>
> 


## <a name="on-demand-compute-environment"></a>按需计算环境
在按需配置中，计算环境完全由数据工厂管理。 在提交用于处理数据的作业之前，数据工厂会自动创建计算环境。 完成作业后，数据工厂会删除计算环境。 

可为按需计算环境创建链接服务。 使用该链接服务可以配置计算环境，以及控制作业执行、群集管理和引导操作的粒度设置。

> [!NOTE]
> 按需配置目前仅支持 Azure HDInsight 群集。
> 

## <a name="azure-hdinsight-on-demand-linked-service"></a>Azure HDInsight 按需链接服务
数据工厂可自动创建基于 Windows 或 Linux 的按需 HDInsight 群集用于处理数据。 该群集是在与它关联的存储帐户所在的同一区域中创建的。 使用 JSON **linkedServiceName** 属性创建该群集。

请注意以下有关按需 HDInsight 链接服务的要点：

* 按需 HDInsight 群集不会出现在 Azure 订阅中。 数据工厂服务会代你管理按需 HDInsight 群集。
* 在按需 HDInsight 群集上运行的作业日志将复制到与 HDInsight 群集相关联的存储帐户。 若要访问这些日志，请在 Azure 门户中转到“活动运行详细信息”窗格。 有关详细信息，请参阅[监视和管理管道](data-factory-monitor-manage-pipelines.md)。
* 只需为启动 HDInsight 群集和运行作业的时间付费。

> [!IMPORTANT]
> 预配按需 HDInsight 群集通常需要 20 分钟或更长时间。
>
> 

### <a name="example"></a>示例
以下 JSON 定义基于 Linux 的按需 HDInsight 链接服务。 处理数据切片时，数据工厂会自动创建基于 Linux 的 HDInsight 群集。 

```json
{
    "name": "HDInsightOnDemandLinkedService",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.6",
            "osType": "Linux",
            "clusterSize": 1,
            "timeToLive": "00:05:00",            
            "linkedServiceName": "AzureStorageLinkedService"
        }
    }
}
```

> [!IMPORTANT]
> HDInsight 群集在 JSON **linkedServiceName** 属性中指定的 Azure Blob 存储内创建默认容器。 根据设计，HDInsight 不会在删除群集时删除此容器。 在按需 HDInsight 链接服务中，除非存在现有的实时群集 (**timeToLive**)，否则每次需要处理切片时，都会创建 HDInsight 群集。 处理完成后会删除该群集。 
>
> 随着处理的切片越来越多，Blob 存储中会出现大量的容器。 如果不需要使用容器来排查作业问题，可以删除容器以降低存储成本。 这些容器的名称遵循 `adf<your Data Factory name>-<linked service name>-<date and time>` 模式。 可以使用 [Microsoft 存储资源管理器](http://storageexplorer.com/)等工具来删除 Blob 存储中的容器。
>
> 

### <a name="properties"></a>属性
| 属性                     | 说明                              | 必选 |
| ---------------------------- | ---------------------------------------- | -------- |
| type                         | 将 type 属性设置为 **HDInsightOnDemand**。 | 是      |
| clusterSize                  | 群集中的工作节点/数据节点数。 HDInsight 群集创建时具有 2 个头节点以及一定数量的工作节点（为此属性指定的数量）。 节点大小为 Standard_D3，采用 4 个核心。 包含 4 个工作节点的群集采用 24 个核心（工作节点采用 4\*4 = 16 个核心，头节点采用 2\*4 = 8 个核心）。 有关 Standard_D3 层的详细信息，请参阅[在 HDInsight 中创建基于 Linux 的 Hadoop 群集](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md)。 | 是      |
| timeToLive                   | 按需 HDInsight 群集允许的空闲时间。 指定当活动运行完成后，如果群集中没有其他的活动作业，按需 HDInsight 群集保持活动状态的时间。<br /><br />例如，如果一个活动运行需要 6 分钟，而 **timeToLive** 的设置是 5 分钟，则当 6 分钟的活动运行处理结束后，群集将保持 5 分钟的活动状态。 如果在这 6 分钟的时间内执行其他的活动运行，则由同一群集进行处理。<br /><br />创建按需 HDInsight 群集是一项开销很大的操作（可能需要花费一段时间）。 请根据需要使用此设置，以通过重复使用一个按需 HDInsight 群集来提高数据工厂的性能。<br /><br />如果将 **timeToLive** 值设置为 **0**，则会在活动运行处理完后立即删除群集。 但是，如果设置较高的值，群集可能会保持不必要的空闲状态，从而造成较高成本。 根据具体需要设置适当的值非常重要。<br /><br />如果 **timeToLive** 值设置得当，则多个管道可以共享按需 HDInsight 群集的实例。 | 是      |
| 版本                      | HDInsight 群集的版本。 有关允许的 HDInsight 版本，请参阅[支持的 HDInsight 版本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions)。 如果未指定此值，将使用[最新的 HDI 默认版本](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#hadoop-components-available-with-different-hdinsight-versions)。 | 否       |
| linkedServiceName            | 由按需群集用于存储和处理数据的 Azure 存储链接服务。 HDInsight 群集在创建时与此存储帐户位于同一区域。<p>目前，无法创建使用 Azure Data Lake Store 作为存储的按需 HDInsight 群集。 若要将 HDInsight 处理后的结果数据存储在 Data Lake Store 中，请使用“复制活动”将数据从 Blob 存储复制到 Data Lake Store。 </p> | 是      |
| additionalLinkedServiceNames | 为 HDInsight 链接服务指定更多的存储帐户。 数据工厂会代你注册存储帐户。 这些存储帐户必须与 HDInsight 群集位于同一区域。 HDInsight 群集是在 **linkedServiceName** 属性指定的存储帐户所在的同一区域中创建的。 | 否       |
| osType                       | 操作系统的类型。 允许的值为 **Linux** 和 **Windows**。 如果未指定此值，将使用 **Linux**。  <br /><br />我们强烈建议使用基于 Linux 的 HDInsight 群集。 Windows 上的 HDInsight 的停用日期是 2018 年 7 月 31 日。 | 否       |
| hcatalogLinkedServiceName    | 指向 HCatalog 数据库的 Azure SQL 链接服务的名称。 按需 HDInsight 群集是使用 SQL 数据库作为元存储创建的。 | 否       |

#### <a name="example-linkedservicenames-json"></a>示例：LinkedServiceNames JSON

```json
"additionalLinkedServiceNames": [
    "otherLinkedServiceName1",
    "otherLinkedServiceName2"
  ]
```

### <a name="advanced-properties"></a>高级属性
若要对按需 HDInsight 群集进行粒度配置，可指定以下属性：

| 属性               | 说明                              | 必选 |
| :--------------------- | :--------------------------------------- | :------- |
| coreConfiguration      | 为要创建的 HDInsight 群集指定核心配置参数 (core-site.xml)。 | 否       |
| hBaseConfiguration     | 为 HDInsight 群集指定 HBase 配置参数 (hbase-site.xml)。 | 否       |
| hdfsConfiguration      | 为 HDInsight 群集指定 HDFS 配置参数 (hdfs-site.xml)。 | 否       |
| hiveConfiguration      | 为 HDInsight 群集指定 Hive 配置参数 (hive-site.xml)。 | 否       |
| mapReduceConfiguration | 为 HDInsight 群集指定 MapReduce 配置参数 (mapred-site.xml)。 | 否       |
| oozieConfiguration     | 为 HDInsight 群集指定 Oozie 配置参数 (oozie-site.xml)。 | 否       |
| stormConfiguration     | 为 HDInsight 群集指定 Storm 配置参数 (storm-site.xml)。 | 否       |
| yarnConfiguration      | 为 HDInsight 群集指定 YARN 配置参数 (yarn-site.xml)。 | 否       |

#### <a name="example-on-demand-hdinsight-cluster-configuration-with-advanced-properties"></a>示例：具有高级属性的按需 HDInsight 群集配置

```json
{
  "name": " HDInsightOnDemandLinkedService",
  "properties": {
    "type": "HDInsightOnDemand",
    "typeProperties": {
      "version": "3.6",
      "osType": "Linux",
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
若要指定头节点、数据节点和 ZooKeeper 节点的大小，请使用以下属性： 

| 属性          | 说明                              | 必选 |
| :---------------- | :--------------------------------------- | :------- |
| headNodeSize      | 设置头节点的大小。 默认值为 **Standard_D3**。 有关详细信息，请参阅[指定节点大小](#specify-node-sizes)。 | 否       |
| dataNodeSize      | 设置数据节点的大小。 默认值为 **Standard_D3**。 | 否       |
| zookeeperNodeSize | 设置 ZooKeeper 节点的大小。 默认值为 **Standard_D3**。 | 否       |

#### <a name="specify-node-sizes"></a>指定节点大小
有关必须为上一部分中所述属性指定的字符串值，请参阅[虚拟机的大小](../../virtual-machines/linux/sizes.md)。 这些值必须符合[虚拟机大小](../../virtual-machines/linux/sizes.md)中提到的 cmdlet 和 API。 较大（默认）的数据节点使用 7 GB 内存。 这可能无法满足具体方案的需求。 

若要创建 D4 大小的头节点和工作节点，请将 **Standard_D4** 指定为 **headNodeSize** 和 **dataNodeSize** 属性的值。 

```json
"headNodeSize": "Standard_D4",    
"dataNodeSize": "Standard_D4",
```

如果为这些属性设置了错误的值，可能会看到以下消息：

  未能创建群集。 异常: 无法完成群集创建操作。 操作失败，代码为 '400'。 群集保持为 'Error' 状态。 消息: “PreClusterCreationValidationFailure”。 
  
如果看到此消息，请确保使用[虚拟机大小](../../virtual-machines/linux/sizes.md)的表格中的 cmdlet 和 API 名称。  

> [!NOTE]
> 目前，数据工厂不支持将 Data Lake Store 用作主存储的 HDInsight 群集。 请将 Azure 存储用作 HDInsight 群集的主存储。 
>
> 


## <a name="bring-your-own-compute-environment"></a>自带计算环境
可在数据工厂中将现有的计算环境注册为链接服务。 该计算环境由你自行管理。 数据工厂服务使用该计算环境来执行活动。

此类型的配置支持以下计算环境：

* Azure HDInsight
* Azure 批处理
* Azure 机器学习
* Azure Data Lake Analytics
* Azure SQL 数据库、Azure SQL 数据仓库、SQL Server

## <a name="azure-hdinsight-linked-service"></a>Azure HDInsight 链接服务
可以创建 HDInsight 链接服务，以便将自己的 HDInsight 群集注册到数据工厂。

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
| 属性          | 说明                              | 必选 |
| ----------------- | ---------------------------------------- | -------- |
| type              | 将 type 属性设置为 **HDInsight**。 | 是      |
| clusterUri        | HDInsight 群集的 URI。        | 是      |
| username          | 用于连接到现有 HDInsight 群集的用户帐户名。 | 是      |
| password          | 用户帐户的密码。   | 是      |
| linkedServiceName | 引用 HDInsight 群集所用的 Azure Blob 存储的存储链接服务的名称。 <p>目前，不能为此属性指定 Data Lake Store 链接服务。 如果 HDInsight 群集有权访问 Data Lake Store，则可从 Hive 或 Pig 脚本访问 Data Lake Store 中的数据。 </p> | 是      |

## <a name="azure-batch-linked-service"></a>Azure Batch 链接服务
可以创建 Batch 链接服务，用于向数据工厂注册虚拟机 (VM) 的 Batch 池。 可以使用 Batch 或 HDInsight 运行 Microsoft .NET 自定义活动。

如果你不熟悉 Batch 服务的用法，可以：

* 学习 [Azure Batch 的基础知识](../../batch/batch-technical-overview.md)。
* 了解 [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt125880.aspx) cmdlet。 使用此 cmdlet 创建 Batch 帐户。 或者，可以使用 [Azure 门户](../../batch/batch-account-create-portal.md)创建 Batch 帐户。 有关使用该 cmdlet 的详细信息，请参阅[使用 PowerShell 管理 Batch 帐户](http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx)。
* 了解 [New-AzureBatchPool](https://msdn.microsoft.com/library/mt125936.aspx) cmdlet。 使用此 cmdlet 创建 Batch 池。

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

对于 **accountName** 属性，请在 Batch 帐户名称的末尾追加 **.\<区域名称\>**。 例如：

```json
"accountName": "mybatchaccount.eastus"
```

另一种做法是提供 **batchUri** 终结点。 例如：

```json
"accountName": "adfteam",
"batchUri": "https://eastus.batch.azure.com",
```

### <a name="properties"></a>属性
| 属性          | 说明                              | 必选 |
| ----------------- | ---------------------------------------- | -------- |
| type              | 将 type 属性设置为 **AzureBatch**。 | 是      |
| accountName       | Batch 帐户的名称。         | 是      |
| accessKey         | Batch 帐户的访问密钥。  | 是      |
| poolName          | VM 池的名称。    | 是      |
| linkedServiceName | 与此 Batch 链接服务关联的存储链接服务的名称。 此链接服务用于暂存运行活动所需的文件，以及用于存储活动执行日志。 | 是      |

## <a name="azure-machine-learning-linked-service"></a>Azure 机器学习链接服务
可以创建机器学习链接服务，用于向数据工厂注册机器学习 Batch 评分终结点。

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
| 属性   | 说明                              | 必选 |
| ---------- | ---------------------------------------- | -------- |
| Type       | 将 type 属性设置为 **AzureML**。 | 是      |
| mlEndpoint | 批处理计分 URL。                   | 是      |
| apiKey     | 已发布的工作区模型的 API。     | 是      |

## <a name="azure-data-lake-analytics-linked-service"></a>Azure Data Lake Analytics 链接服务
可以创建 Data Lake Analytics 链接服务，用于将 Data Lake Analytics 计算服务链接到 Azure 数据工厂。 管道中的 Data Lake Analytics U-SQL 活动是指此链接服务。 

下表描述了 JSON 定义中使用的泛型属性。

| 属性                 | 说明                              | 必选                                 |
| ------------------------ | ---------------------------------------- | ---------------------------------------- |
| type                 | 将 type 属性设置为 **AzureDataLakeAnalytics**。 | 是                                      |
| accountName          | Data Lake Analytics 帐户名。  | 是                                      |
| dataLakeAnalyticsUri | Data Lake Analytics URI。           | 否                                       |
| subscriptionId       | Azure 订阅 ID。                    | 否<br /><br />（如果未指定，将使用数据工厂订阅。） |
| resourceGroupName    | Azure 资源组名称。                | 否<br /><br /> （如果未指定，将使用数据工厂资源组。） |

### <a name="authentication-options"></a>身份验证选项
对于 Data Lake Analytics 链接服务，可以在服务主体或用户凭据身份验证之间做出选择。

#### <a name="service-principal-authentication-recommended"></a>服务主体身份验证（推荐）
若要使用服务主体身份验证，请在 Azure Active Directory (Azure AD) 中注册一个应用程序实体。 然后，向 Azure AD 授予 Data Lake Store 访问权限。 有关详细步骤，请参阅[服务到服务身份验证](../../data-lake-store/data-lake-store-authenticate-using-active-directory.md)。 记下下面的值，这些值用于定义链接服务：
* 应用程序 ID
* 应用程序密钥 
* 租户 ID

通过指定以下属性使用服务主体身份验证：

| 属性                | 说明                              | 必选 |
| :---------------------- | :--------------------------------------- | :------- |
| servicePrincipalId  | 应用程序的客户端 ID。     | 是      |
| servicePrincipalKey | 应用程序的密钥。           | 是      |
| tenant              | 应用程序所在的租户的信息（域名或租户 ID）。 若要获取此信息，请将鼠标悬停在 Azure 门户的右上角。 | 是      |

**示例：服务主体身份验证**
```json
{
    "name": "AzureDataLakeAnalyticsLinkedService",
    "properties": {
        "type": "AzureDataLakeAnalytics",
        "typeProperties": {
            "accountName": "adftestaccount",
            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
            "servicePrincipalId": "<service principal id>",
            "servicePrincipalKey": "<service principal key>",
            "tenant": "<tenant info, e.g. microsoft.onmicrosoft.com>",
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="user-credential-authentication"></a>用户凭据身份验证
若要对 Data Lake Analytics 使用用户凭据身份验证，请指定以下属性：

| 属性          | 说明                              | 必选 |
| :---------------- | :--------------------------------------- | :------- |
| authorization | 在数据工厂编辑器中，选择“授权”按钮。 在此属性中输入用于分配自动生成的授权 URL 的凭据。 | 是      |
| sessionId     | OAuth 授权会话中的 OAuth 会话 ID。 每个会话 ID 都是唯一的，并且只能使用一次。 使用数据工厂编辑器时，会自动生成此设置。 | 是      |

**示例：用户凭据身份验证**
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
            "subscriptionId": "<optional, subscription id of ADLA>",
            "resourceGroupName": "<optional, resource group name of ADLA>"
        }
    }
}
```

#### <a name="token-expiration"></a>令牌过期
选择“授权”按钮后生成的授权代码将在设置的间隔后过期。 

身份验证令牌过期时，可能会显示以下错误消息： 

  凭据操作错误: invalid_grant - AADSTS70002: 验证凭据时出错。 AADSTS70008：提供的访问权限已过期或已被吊销。 跟踪 ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 相关 ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 时间戳: 2015-12-15 21:09:31Z

下表按用户帐户类型显示了过期时间： 

| 用户类型                                | 过期时间                            |
| :--------------------------------------- | :--------------------------------------- |
| 不是由 Azure AD 管理的用户帐户（Hotmail、Live 等等） | 12 小时。                                 |
| 由 Azure AD 管理的用户帐户 | 最后一次运行切片后的 14 天。 <br /><br />如果以基于 OAuth 的链接服务为基础的切片每 14 天至少运行一次，则为 90 天。 |

若要避免或解决此错误，请在令牌过期时，选择“授权”按钮重新授权。 然后重新部署链接服务。 还可使用以下代码以编程方式生成 **sessionId** 和 **authorization** 属性的值：

```csharp
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

有关此代码示例中使用的数据工厂类的详细信息，请参阅：
* [AzureDataLakeStoreLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx)
* [AzureDataLakeAnalyticsLinkedService 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx)
* [AuthorizationSessionGetResponse 类](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx)

对于 **WindowsFormsWebAuthenticationDialog** 类，请添加对 Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll 的引用。 

## <a name="azure-sql-linked-service"></a>Azure SQL 链接服务
可以创建一个 SQL 链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 有关详细信息，请参阅 [Azure SQL 连接器](data-factory-azure-sql-connector.md#linked-service-properties)。

## <a name="azure-sql-data-warehouse-linked-service"></a>Azure SQL 数据仓库链接服务
可以创建一个 SQL 数据仓库链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 有关详细信息，请参阅 [Azure SQL 数据仓库连接器](data-factory-azure-sql-data-warehouse-connector.md#linked-service-properties)。

## <a name="sql-server-linked-service"></a>SQL Server 链接服务
可以创建一个 SQL Server 链接服务，并将其与[存储过程活动](data-factory-stored-proc-activity.md)配合使用，以从数据工厂管道调用存储过程。 有关详细信息，请参阅 [SQL Server 连接器](data-factory-sqlserver-connector.md#linked-service-properties)。

