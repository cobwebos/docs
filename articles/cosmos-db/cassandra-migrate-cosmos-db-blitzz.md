---
title: 使用 Blitzz 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API
description: 了解如何使用 Blitzz 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 746232a85e326f08d44cf2dbe0a4ae718c7cc312
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "69984363"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API 帐户

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因各种各样，例如： 

* **管理和监视没有开销：** 它消除了跨操作系统、JVM 和 yaml 文件及其交互管理和监视无数设置的开销。

* **显著节约成本：** 您可以使用 Azure Cosmos DB 来节省成本，其中包括 VM 的成本、带宽和任何适用的许可证。 此外，您不必管理数据中心、服务器、SSD 存储、网络和电力成本。 

* **能够使用现有代码和工具：** Azure Cosmos DB 提供与现有 Cassandra SDK 和工具的线协议级兼容性。 此兼容性确保只需经过细微的更改，就可以将现有代码库用于 Azure Cosmos DB Cassandra API。

可以通过多种方式将数据库工作负荷从一个平台迁移到另一个平台。 [Blitzz](https://www.blitzz.io) 工具可以安全可靠地将各种数据库中的数据迁移到 Azure Cosmos DB，且不会造成停机。 本文介绍如何执行所需步骤，以便使用 Blitzz 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 进行迁移的优势

Blitzz 的迁移解决方案遵循一步一步的方法迁移复杂的操作工作负载。 以下是 Blitzz 零停机时间迁移计划的一些关键方面：

* 它可以自动将业务逻辑（表、索引、视图）从 Apache Cassandra 数据库迁移到 Azure Cosmos DB。 您不必手动创建架构。

* Blitzz 提供大规模并行数据库复制。 它可以使用称作“变更数据捕获 (CDC)”的技术，让源平台和目标平台在迁移过程中保持同步状态。 Blitzz 可以通过 CDC 从源数据库 (Apache Cassandra) 持续拉取更改流，并将其应用到目标数据库 (Azure Cosmos DB)。

* 它具有容错功能，可以保证数据的准确传输，即使系统出现硬件或软件故障。

* 它可以在传输过程中使用各种安全方法（例如 SSL、加密）对数据进行保护。

## <a name="steps-to-migrate-data"></a>迁移数据的步骤

此部分介绍如何执行所需步骤，以便设置 Blitzz 并将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB。

1. 在打算安装 Blitzz 复制器的计算机中，添加安全证书。 该证书是 Blitzz 复制器与指定的 Azure Cosmos DB 帐户建立 SSL 连接所需的。 可以使用以下步骤来添加证书：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. 若要获取 Blitzz 安装和二进制文件，可以在 [Blitzz 网站](https://www.blitzz.io)上请求演示版， 或者，可以向相关团队发送[电子邮件](mailto:success@blitzz.io)。

   ![Blitzz 复制器工具下载](./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png)

   ![Blitzz 复制器文件](./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png)

1. 在 CLI 终端设置源数据库配置。 使用**`vi conf/conn/cassandra.yml`** 命令打开配置文件，并添加卡桑德拉节点的 IP 地址、端口号、用户名、密码和任何其他必需详细信息的逗号分隔列表。 下面是一个示例，介绍了配置文件中的内容：

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   ![打开 Cassandra 连接编辑器](./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png)

   ![Cassandra 连接配置](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png)

   填充配置详细信息后，保存并关闭该文件。

1. （可选）设置源数据库筛选器文件。 筛选器文件指定要迁移的架构或表。 使用**`vi filter/cassandra_filter.yml`** 命令打开配置文件并输入以下配置详细信息：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   填充数据库筛选器详细信息以后，保存并关闭该文件。

1. 接下来，设置目标数据库配置。 在定义配置之前，先[创建 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)，然后创建密钥空间，接着创建一个用于存储已迁移数据的表。 由于你是从 Apache Cassandra 迁移到 Azure Cosmos DB 中的 Cassandra API，因此所使用的分区键可以与用于 Apache Cassandra 的分区键相同。

1. 在迁移数据之前，请将容器吞吐量提高到快速迁移应用程序所需的量。 例如，可将吞吐量提高到 100000 RU。 在开始迁移之前提高吞吐量可以缩短数据迁移时间。

   ![缩放 Azure Cosmos 容器吞吐量](./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png)

   在迁移完成后，请降低吞吐量。 可以根据存储的数据量以及每次操作所需的 RU 数，估算数据迁移后所需的吞吐量。 要了解有关如何估计所需的 RU 的更多详细信息，请参阅[在容器和数据库上预配吞吐量](set-throughput.md)，[以及使用 Azure Cosmos DB 容量规划器文章估计 RU/s。](estimate-ru-with-capacity-planner.md)

1. 在“连接字符串”窗格中获取“联系点、端口、用户名”，以及 Azure Cosmos 帐户的“主密码”。************ 需要在配置文件中使用这些值。

1. 在 CLI 终端设置目标数据库配置。 使用**`vi conf/conn/cosmosdb.yml`** 命令打开配置文件，并添加主机 URI、端口号、用户名、密码和其他必需参数的逗号分隔列表。 以下示例显示了配置文件的内容：

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. 接下来，使用 Blitzz 迁移数据。 可以按“完全”或“快照”模式运行 Blizz 复制器：********

   * **完全模式**– 在此模式下，复制副本在迁移后继续运行，并侦听源 Apache Cassandra 系统上的任何更改。 如果它检测到任何更改，则会将更改实时复制到目标 Azure Cosmos 帐户。

   * **快照模式**– 在此模式下，可以执行架构迁移和一次性数据复制。 此选项不支持实时复制。

   可以通过上述两种模式进行迁移，不会造成停机。 

1. 若要迁移数据，请在 Blitzz 复制器 CLI 终端运行以下命令：

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   复制器 UI 显示复制进度。 完成架构迁移和快照操作以后，进度会显示 100%。 完成迁移后，可在目标 Azure Cosmos 数据库上验证数据。

   ![Cassandra 数据迁移输出](./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png)


1. 由于你已使用“完全”模式进行迁移，因此可以执行多项操作，例如在源 Apache Cassandra 数据库上插入、更新或删除数据。 稍后验证它们是否已在目标 Azure Cosmos 数据库上实时复制。 迁移后，请务必降低为 Azure Cosmos 容器配置的吞吐量。

1. 可以随时停止复制器，并使用 **--resume** 开关重启它。 复制会从停止的点继续，不会损害数据一致性。 以下命令演示如何使用 resume 开关。

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

若要详细了解如何以实时迁移方式将数据迁移到目标，请观看 [Blitzz 复制器演示](https://www.youtube.com/watch?v=fsUhF9LUZmM)视频。

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md) 
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure 宇宙数据库容量规划器文章估计 RU/s](estimate-ru-with-capacity-planner.md)