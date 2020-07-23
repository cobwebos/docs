---
title: 使用 Blitzz 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API
description: 了解如何使用 Blitzz 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: d3eda4694decb74912cc125ef0a33de04838be2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85260621"
---
# <a name="migrate-data-from-cassandra-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>使用 Blitzz 将数据从 Cassandra 迁移到 Azure Cosmos DB Cassandra API 帐户

Azure Cosmos DB 中的 Cassandra API 已成为在 Apache Cassandra 上运行的企业工作负荷的极佳选择，原因各种各样，例如： 

* **无管理和监视开销：** 它不需管理和监视跨 OS、JVM 和 yaml 文件的大量设置，也不需进行交互。

* **显著节省成本：** 可以通过 Azure Cosmos DB 节省成本，包括 VM 的成本、带宽和任何适用的许可证。 此外，无需管理数据中心、服务器、SSD 存储、网络和电力成本。 

* **能够使用现有的代码和工具：** Azure Cosmos DB 提供的线路协议级别与现有 Cassandra SDK 和工具兼容。 此兼容性确保只需经过细微的更改，就可以将现有代码库用于 Azure Cosmos DB Cassandra API。

可以通过多种方式将数据库工作负荷从一个平台迁移到另一个平台。 [Blitzz](https://www.blitzz.io) 是一项工具，可以通过它安全且可靠地执行不停机的迁移，从各种数据库迁移到 Azure Cosmos DB。 本文介绍如何执行所需步骤，以便使用 Blitzz 将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB Cassandra API。

## <a name="benefits-using-blitzz-for-migration"></a>使用 Blitzz 进行迁移的优势

Blitzz 的迁移解决方案遵循迁移复杂的操作工作负荷的分步方法。 下面是 Blitzz 的零停机迁移计划的一些关键方面：

* 它可以自动将业务逻辑（表、索引、视图）从 Apache Cassandra 数据库迁移到 Azure Cosmos DB。 无需手动创建架构。

* Blitzz 提供大规模并行数据库复制。 它可以使用名为“变更数据捕获 (CDC)”的技术，让源平台和目标平台在迁移过程中保持同步状态。 Blitzz 可以通过 CDC 从源数据库 (Apache Cassandra) 持续拉取更改流，并将其应用到目标数据库 (Azure Cosmos DB)。

* 它具有容错功能，可以保证数据的准确传输，即使系统出现硬件或软件故障。

* 它可以在传输过程中使用各种安全方法（例如 TLS、加密）对数据进行保护。

## <a name="steps-to-migrate-data"></a>迁移数据的步骤

此部分介绍如何执行所需步骤，以便设置 Blitzz 并将数据从 Apache Cassandra 数据库迁移到 Azure Cosmos DB。

1. 在打算安装 Blitzz 复制器的计算机中，添加安全证书。 该证书是 Blitzz 复制器与指定的 Azure Cosmos DB 帐户建立 TLS 连接所需的。 可以使用以下步骤来添加证书：

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. 若要获取 Blitzz 安装和二进制文件，可以在 [Blitzz 网站](https://www.blitzz.io)上请求演示版， 也可以向相关团队发送[电子邮件](mailto:success@blitzz.io)。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz 复制器工具下载":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz 复制器文件":::

1. 在 CLI 终端设置源数据库配置。 使用 **`vi conf/conn/cassandra.yml`** 命令打开配置文件，添加一个包含 Cassandra 节点 IP 地址的逗号分隔的列表，并添加端口号、用户名、密码以及任何其他必需的详细信息。 下面是一个示例，介绍了配置文件中的内容：

   ```bash
   type: CASSANDRA
  
   host: 172.17.0.2
   port: 9042

   username: 'cassandra'
   password: 'cassandra'

   max-connections: 30

   ```

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/open-connection-editor-cassandra.png" alt-text="打开 Cassandra 连接编辑器":::

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-connection-configuration.png" alt-text="Cassandra 连接配置":::

   填充配置详细信息以后，保存并关闭该文件。

1. （可选）设置源数据库筛选器文件。 筛选器文件指定要迁移的架构或表。 使用 **`vi filter/cassandra_filter.yml`** 命令打开配置文件，输入以下配置详细信息：

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```

   填充数据库筛选器详细信息以后，保存并关闭该文件。

1. 接下来，设置目标数据库配置。 在定义配置之前，先[创建 Azure Cosmos DB Cassandra API 帐户](create-cassandra-dotnet.md#create-a-database-account)，然后创建密钥空间，接着创建一个用于存储已迁移数据的表。 由于你是从 Apache Cassandra 迁移到 Azure Cosmos DB 中的 Cassandra API，因此所使用的分区键可以与用于 Apache Cassandra 的分区键相同。

1. 在迁移数据之前，请将容器吞吐量提高到快速迁移应用程序所需的量。 例如，可将吞吐量提高到 100000 RU。 在开始迁移之前提高吞吐量可以缩短数据迁移时间。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="缩放 Azure Cosmos 容器吞吐量":::

   在迁移完成后，请降低吞吐量。 可以根据存储的数据量以及每次操作所需的 RU 数，估算数据迁移后所需的吞吐量。 若要详细了解如何估算所需的 ru，请参阅使用 Azure Cosmos DB 容量规划器文章为[容器和数据库预配吞吐量](set-throughput.md)和[估计 RU/秒](estimate-ru-with-capacity-planner.md)。

1. 在“连接字符串”窗格中获取“联系点、端口、用户名”，以及 Azure Cosmos 帐户的“主密码”。    需要在配置文件中使用这些值。

1. 在 CLI 终端设置目标数据库配置。 使用 **`vi conf/conn/cosmosdb.yml`** 命令打开配置文件，添加一个包含主机 URI 的逗号分隔的列表，并添加端口号、用户名、密码以及其他必需的参数。 以下示例显示了配置文件的内容：

   ```bash
   type: COSMOSDB

   host: '<Azure Cosmos account’s Contact point>'
   port: 10350

   username: 'blitzzdemo'
   password: '<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   ```

1. 接下来，使用 Blitzz 迁移数据。 可以按“完全”或“快照”模式运行 Blizz 复制器：  

   * **完全模式**–在此模式下，replicant 将继续在迁移后运行，并侦听源 Apache Cassandra 系统中的任何更改。 如果它检测到任何更改，则会将更改实时复制到目标 Azure Cosmos 帐户。

   * **快照模式**–在此模式下，你可以执行架构迁移和一次性数据复制。 此选项不支持实时复制。

   可以通过上述两种模式进行迁移，不会造成停机。 

1. 若要迁移数据，请在 Blitzz 复制器 CLI 终端运行以下命令：

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing
   ```

   复制器 UI 显示复制进度。 完成架构迁移和快照操作以后，进度会显示 100%。 完成迁移后，即可在目标 Azure Cosmos 数据库上验证数据。

   :::image type="content" source="./media/cassandra-migrate-cosmos-db-blitzz/cassandra-data-migration-output.png" alt-text="Cassandra 数据迁移输出":::


1. 由于你已使用“完全”模式进行迁移，因此可以执行多项操作，例如在源 Apache Cassandra 数据库上插入、更新或删除数据。 稍后验证它们是否已在目标 Azure Cosmos 数据库上实时复制。 迁移后，请务必降低为 Azure Cosmos 容器配置的吞吐量。

1. 可以随时停止复制器，并使用 **--resume** 开关重启它。 复制会从停止的点继续，不会损害数据一致性。 以下命令演示如何使用 resume 开关。

   ```bash
   ./bin/replicant full conf/conn/cassandra.yaml conf/conn/cosmosdb.yaml --filter filter/cassandra_filter.yaml --replace-existing --resume
   ```

若要详细了解如何以实时迁移方式将数据迁移到目标，请观看 [Blitzz 复制器演示](https://www.youtube.com/watch?v=fsUhF9LUZmM)视频。

## <a name="next-steps"></a>后续步骤

* [在容器和数据库上预配吞吐量](set-throughput.md) 
* [分区键最佳做法](partitioning-overview.md#choose-partitionkey)
* [使用 Azure Cosmos DB 容量规划器文章估算 RU/秒](estimate-ru-with-capacity-planner.md)
