---
title: "将 Cassandra 数据导入 Azure Cosmos DB | Microsoft 文档"
description: "了解如何使用 CQL Copy 命令将 Cassandra 数据复制到 Azure Cosmos DB。"
services: cosmos-db
author: govindk
manager: jhubbard
documentationcenter: 
ms.assetid: eced5f6a-3f56-417a-b544-18cf000af33a
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: govindk
ms.custom: mvc
ms.openlocfilehash: 21168d0862cfdaaaced60fa80a2dc04859f49550
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="azure-cosmos-db-import-cassandra-data"></a>Azure Cosmos DB：导入 Cassandra 数据

本教程说明如何使用 Cassandra 查询语言 (CQL) COPY 命令将 Cassandra 数据导入到 Azure Cosmos DB。 

本教程涵盖以下任务：

> [!div class="checklist"]
> * 检索连接字符串
> * 使用 cqlsh COPY 命令导入数据
> * 使用 Spark 连接器导入 

# <a name="prerequisites"></a>先决条件

* 安装 [Apache Cassandra](http://cassandra.apache.org/download/) 并特别确保 cqlsh 存在。
* 增加吞吐量：数据迁移的持续时间取决于为表预配的吞吐量。 请确保对于较大的数据迁移增加吞吐量。 完成迁移后，减少吞吐量以节约成本。 有关在 [Azure 门户](https://portal.azure.com)中增加吞吐量的详细信息，请参阅[设置 Azure Cosmos DB 容器的吞吐量](set-throughput.md)。
* 启用 SSL：Azure Cosmos DB 具有严格的安全要求和标准。 请确保在与帐户进行交互时启用 SSL。 当你将 CQL 与 SSH 配合使用时，可以选择提供 SSL 信息。 

## <a name="find-your-connection-string"></a>查找连接字符串

1. 在 [Azure 门户](https://portal.azure.com)的最左侧，单击“Azure Cosmos DB”。

2. 在“订阅”窗格中，选择帐户名称。

3. 单击“连接字符串”。 右侧窗格中包含成功连接到帐户所需的全部信息。

    ![“连接字符串”页](./media/cassandra-import-data/keys.png)

## <a name="use-cqlsh-copy"></a>使用 cqlsh COPY

若要将 Cassandra 数据导入到 Azure Cosmos DB，以与 Cassandra API 一起使用，请使用以下指南：

1. 使用门户中的连接信息登录到 cqhsh。
2. 使用 [CQL COPY命令](http://cassandra.apache.org/doc/latest/tools/cqlsh.html#cqlsh)将本地数据复制到 Apache Cassandra API 终结点。 确保源和目标位于同一数据中心，以最大程度减少延迟问题。

### <a name="guide-for-moving-data-with-cqlsh"></a>有关通过 cqlsh 移动数据的指南

1. 预创建和缩放表：
    * 默认情况下，Azure Cosmos DB 会预配一个新的 Cassandra API 表，每秒钟有 1,000 个请求单元 (RU/s)（基于 CQL 的创建预配有 400 RU/s）。 通过使用 cqlsh 开始迁移之前，从 [Azure 门户](https://portal.azure.com)或 cqlsh 预先创建所有表。 

    * 在迁移期间，从 [Azure 门户](https://portal.azure.com)将表的吞吐量从默认吞吐量（400 或 1000 RU/s）增加到 10,000 RU/s。 提高吞吐量后，可避免达到上限，并缩短迁移时间。 由于 Azure Cosmos DB 采用按小时计费，因此可以在迁移后立即降低吞吐量，以节省成本。

2. 确定某个操作的 RU 费用。 可以使用所选的 Azure Cosmos DB Cassandra API SDK 执行此操作。 此示例演示获取 RU 费用的 .NET 版本。 

    ```csharp
    var tableInsertStatement = table.Insert(sampleEntity);
    var insertResult = await tableInsertStatement.ExecuteAsync();

    foreach (string key in insertResult.Info.IncomingPayload)
            {
                byte[] valueInBytes = customPayload[key];
                string value = Encoding.UTF8.GetString(valueInBytes);
                Console.WriteLine($“CustomPayload:  {key}: {value}”);
            }
 
    ``` 

3. 确定计算机连接 Azure Cosmos DB 服务的延迟时间。 如果你在 Azure 数据中心内，延迟应是一个低于几毫秒的个位数。 如果你在 Azure 数据中心之外，则可以使用 psping 或 azurespeed.com 获取从你的位置连接的近似延迟。   

4. 计算参数（NUMPROCESS、INGESTRATE、MAXBATCHSIZE 或 MINBATCHSIZE）的正确值，以提供良好性能。 

5. 运行最终迁移命令。 运行此命令假定你已使用连接字符串信息启动 cqlsh。

   ```
   COPY exampleks.tablename FROM filefolderx/*.csv 
   ```

## <a name="use-spark-to-import-data"></a>使用 Spark 导入数据

对于驻留在 Azure 虚拟机的现有群集中的数据，也可以使用 Spark 导入数据。 这就需要将 Spark 一次性设置为中介，或者定期引入。 

## <a name="next-steps"></a>后续步骤

在本教程中，你已学习了如何完成以下任务：

> [!div class="checklist"]
> * 检索连接字符串
> * 使用 cql copy 命令导入数据
> * 使用 Spark 连接器导入 

现在可以转到“概念”部分详细了解 Azure Cosmos DB。 

> [!div class="nextstepaction"]
>[Azure Cosmos DB 中的可优化数据一致性级别](../cosmos-db/consistency-levels.md)
