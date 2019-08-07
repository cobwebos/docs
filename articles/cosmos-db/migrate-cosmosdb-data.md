---
title: 将数百 TB 的数据迁移到 Azure Cosmos DB
description: 此文档介绍了如何将数百 tb 的数据迁移到 Cosmos DB
author: bharathsreenivas
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/26/2019
ms.author: bharathb
ms.openlocfilehash: 041f9c95b22fd4b8c238c603deb0558f2bca01a8
ms.sourcegitcommit: c662440cf854139b72c998f854a0b9adcd7158bb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/02/2019
ms.locfileid: "68737623"
---
# <a name="migrate-hundreds-of-terabytes-of-data-into-azure-cosmos-db"></a>将数百 TB 的数据迁移到 Azure Cosmos DB 

Azure Cosmos DB 可以存储数 tb 的数据。 可以执行大规模数据迁移, 将生产工作负荷移动到 Azure Cosmos DB。 本文介绍将大规模数据迁移到 Azure Cosmos DB 涉及到的难题, 并向您介绍用于帮助解决问题并将数据迁移到 Azure Cosmos DB 的工具。 在此示例中, 客户使用 Cosmos DB SQL API。  

在将整个工作负荷迁移到 Azure Cosmos DB 之前, 您可以迁移数据的一个子集以验证某些方面, 如分区键选择、查询性能和数据建模。 验证概念证明后, 可以将整个工作负荷移动到 Azure Cosmos DB。  

## <a name="tools-for-data-migration"></a>用于数据迁移的工具 

Azure Cosmos DB 迁移策略当前因 API 选项和数据大小而异。 迁移较小的数据集–用于验证数据建模、查询性能、分区键选择等等–可以选择[数据迁移工具](import-data.md)或[Azure 数据工厂的 Azure Cosmos DB 连接器](../data-factory/connector-azure-cosmos-db.md)。 如果熟悉 Spark, 还可以选择使用[Azure Cosmos DB spark 连接器](spark-connector.md)来迁移数据。

## <a name="challenges-for-large-scale-migrations"></a>大规模迁移的挑战 

用于将数据迁移到 Azure Cosmos DB 的现有工具在大规模规模上特别明显:

 * **有限的 scale out 功能**:为了尽可能快地将 tb 数据迁移到 Azure Cosmos DB 中, 并有效地使用整个预配的吞吐量, 迁移客户端应能够无限扩展。  

* **缺少进度跟踪和检查指针**:需要跟踪迁移进度, 并在迁移大型数据集时进行检查。 否则, 迁移过程中出现的任何错误都将停止迁移, 必须从头开始处理。 如果已完成了 99% 的迁移过程, 则重新启动整个迁移过程将不会提高效率。  

* **缺少死信队列**:在大型数据集中, 在某些情况下, 源数据的某些部分可能会出现问题。 此外, 客户端或网络可能存在暂时性问题。 其中的任何一种情况都不应导致整个迁移失败。 尽管大多数迁移工具都具有强大的重试功能, 可防止间歇性问题, 但并不总是那么多。 例如, 如果少于 0.01% 的源数据文档的大小超过 2 MB, 将导致文档写入 Azure Cosmos DB 失败。 理想情况下, 迁移工具会将这些 "失败" 文档保存到另一个死信队列中, 从而可以在迁移后对其进行处理。 

其中的许多限制是针对 Azure 数据工厂、Azure 数据迁移服务之类的工具修复的。 

## <a name="custom-tool-with-bulk-executor-library"></a>具有批量执行程序库的自定义工具 

如上一节所述的难题可通过使用可在多个实例中轻松扩展的自定义工具来解决, 并且它可以恢复暂时性故障。 此外, 该自定义工具可以在不同的检查点暂停和恢复迁移。 Azure Cosmos DB 已经提供了包含其中一些功能的[批量执行程序库](https://docs.microsoft.com/azure/cosmos-db/bulk-executor-overview)。 例如, 大容量执行程序库已有处理暂时性错误的功能, 并且可以横向扩展单个节点中的线程, 以消耗大约每个节点 500 K 个 ru。 大容量执行库还会将源数据集分区为可独立作为检查点形式进行操作的微批处理。  

自定义工具使用大容量执行器库, 并支持跨多个客户端横向扩展和跟踪引入过程中的错误。 若要使用此工具, 应将源数据分区为 Azure Data Lake Storage (ADLS) 中的不同文件, 以便不同的迁移工作人员可以选取每个文件并将它们引入 Azure Cosmos DB。 自定义工具使用单独的集合, 该集合在 ADLS 中存储有关每个单独源文件的迁移进度的元数据, 并跟踪与它们关联的任何错误。  

下图描述了使用此自定义工具的迁移过程。 该工具在一组虚拟机上运行, 并且每个虚拟机都在 Azure Cosmos DB 中查询跟踪集合以获取其中一个源数据分区的租约。 完成此操作后, 该工具将读取源数据分区, 并使用大容量执行程序库将引入到 Azure Cosmos DB 中。 接下来, 更新跟踪集合以记录数据引入的进度和遇到的任何错误。 处理数据分区后, 该工具将尝试查询下一个可用的源分区。 它将继续处理下一个源分区, 直到迁移所有数据。 [此处](https://github.com/Azure-Samples/azure-cosmosdb-bulkingestion)提供该工具的源代码。  

 
![迁移工具设置](./media/migrate-cosmosdb-data/migrationsetup.png)
 

 

跟踪集合包含文档, 如以下示例中所示。 对于源数据中的每个分区, 你都将看到这样的文档。  每个文档都包含源数据分区的元数据, 如其位置、迁移状态和错误 (如果有):  

```json
{ 
  "owner": "25812@bulkimporttest07", 
  "jsonStoreEntityImportResponse": { 
    "numberOfDocumentsReceived": 446688, 
    "isError": false, 
    "totalRequestUnitsConsumed": 3950252.2800000003, 
    "errorInfo": [], 
    "totalTimeTakenInSeconds": 188, 
    "numberOfDocumentsImported": 446688 
  }, 
  "storeType": "AZURE_BLOB", 
  "name": "sourceDataPartition", 
  "location": "sourceDataPartitionLocation", 
  "id": "sourceDataPartitionId", 
  "isInProgress": false, 
  "operation": "unpartitioned-writes", 
  "createDate": { 
    "seconds": 1561667225, 
    "nanos": 146000000 
  }, 
  "completeDate": { 
    "seconds": 1561667515, 
    "nanos": 180000000 
  }, 
  "isComplete": true 
} 
```
 

## <a name="prerequisites-for-data-migration"></a>数据迁移的先决条件 

在开始数据迁移之前, 需要考虑以下几个先决条件:  

#### <a name="estimate-the-data-size"></a>估计数据大小:  

源数据的大小可能无法精确地映射到 Azure Cosmos DB 中的数据大小。 可以插入来自源的几个示例文档, 以检查其在 Azure Cosmos DB 中的数据大小。 根据示例文档大小, 可以估计 Azure Cosmos DB 迁移后的总数据大小。 

例如, 如果在 Azure Cosmos DB 中进行迁移之后的每个文档都大约为 1 KB, 并且源数据集中存在60000000000个文档, 则表示 Azure Cosmos DB 中的估计大小接近 60 TB。 

 

#### <a name="pre-create-containers-with-enough-rus"></a>具有足够的 ru 的预创建容器: 

尽管 Azure Cosmos DB 自动扩展存储, 但不建议从最小容器大小开始。 较小的容器具有更低的吞吐量可用性, 这意味着迁移需要更长的时间才能完成。 相反, 可以使用最终数据大小创建容器 (如前一步骤中所述), 并确保迁移工作负载完全消耗预配的吞吐量。  

在上一步中。 由于预计数据大小约为 60 TB, 因此需要至少 2.4 M ru 的容器才能容纳整个数据集。  

 

#### <a name="estimate-the-migration-speed"></a>估计迁移速度: 

假设迁移工作负荷可以使用整个预配的吞吐量, 则在整个中预配的吞吐量将会估计迁移速度。 继续前面的示例, 需要5个 ru, 才能将 1 KB 的文档写入 Azure Cosmos DB SQL API 帐户。  2400000 ru 允许传输480000个文档/秒 (或 480 MB/秒)。 这意味着, 60 TB 的完整迁移需要125000秒或约34小时。  

如果希望在一天内完成迁移, 应将预配的吞吐量提高到5000000个 ru。 

 

#### <a name="turn-off-the-indexing"></a>关闭索引:  

由于迁移应该尽快完成, 因此最好最大程度地减少为每个文档引入创建索引所需的时间和 ru。  Azure Cosmos DB 自动为所有属性编制索引, 则有必要将索引降到选定的几个术语, 或将其完全关闭, 以便进行迁移。 可以通过将 Indexingpolicy.indexingmode 更改为 "无" 来关闭容器的索引策略, 如下所示:  

 
```
  { 
        "indexingMode": "none" 
  } 
```
 

迁移完成后, 可以更新索引。  

## <a name="migration-process"></a>迁移过程 

完成先决条件后, 可以通过以下步骤迁移数据:  

1. 首先将数据从源导入到 Azure Blob 存储。 若要提高迁移速度, 可以跨不同的源分区进行并行化。 在开始迁移之前, 应将源数据集分区为大小约 200 MB 的文件。   

2. 大容量执行程序库可以扩展, 以在单个客户端 VM 中使用 500000 ru。 由于可用吞吐量为 5000000 ru, 因此应在 Azure Cosmos 数据库所在的同一区域中预配10个 Ubuntu 16.04 Vm (Standard_D32_v3)。 应在迁移工具及其设置文件中准备这些 Vm。  

3. 在其中一个客户端虚拟机上运行 queue 步骤。 此步骤将创建跟踪集合, 该操作将扫描 ADLS 容器, 并为每个源数据集的分区文件创建进度跟踪文档。  

4. 接下来, 在所有客户端 Vm 上运行导入步骤。 每个客户端都可以在源分区上取得所有权, 并将其数据引入 Azure Cosmos DB。 完成后, 在跟踪集合中更新其状态之后, 客户端就可以在跟踪集合中查询下一个可用的源分区。  

5. 此过程将一直继续, 直到整个源分区集引入。 处理完所有源分区后, 应在相同跟踪集合上的错误更正模式下重新运行该工具。 此步骤需要标识由于错误而应重新处理的源分区。  

6. 其中一些错误可能是由于源数据中的文档不正确引起的。 应标识并修复这些项。 接下来, 应在失败的分区上重新运行导入步骤以 reingest 它们。 

完成迁移后, 可以验证 Azure Cosmos DB 中的文档计数是否与源数据库中的文档计数相同。 在此示例中, 的总大小 Azure Cosmos DB 为 65 tb。 迁移后, 可以有选择地打开索引, 并且可以将 ru 降低到工作负荷操作所需的级别。   

## <a name="next-steps"></a>后续步骤
* 通过尝试使用[.net](bulk-executor-dot-net.md)和[Java](bulk-executor-java.md)中的批量执行程序库的示例应用程序, 了解详细信息。 
* 大容量执行程序库已集成到 Cosmos DB Spark 连接器, 若要了解详细信息, 请参阅[Azure Cosmos DB Spark 连接器](spark-connector.md)一文。  

