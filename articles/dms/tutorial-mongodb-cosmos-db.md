---
title: 教程：使用 Azure 数据库迁移服务将 MongoDB 脱机迁移到 Azure Cosmos DB 的用于 MongoDB 的 API | Microsoft Docs
description: 了解如何使用 Azure 数据库迁移服务从本地 MongoDB 脱机迁移到 Azure Cosmos DB 的用于 MongoDB 的 API。
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: baf3c372d0c84d4daf439fdc92fa6eeac5d12d0b
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501023"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>教程：使用 DMS 将 MongoDB 脱机迁移到 Azure Cosmos DB 的用于 MongoDB 的 API

可以使用 Azure 数据库迁移服务将数据库从 MongoDB 的本地或云实例脱机（一次性）迁移到 Azure Cosmos DB 的用于 MongoDB 的 API。

本教程介绍如何执行下列操作：
> [!div class="checklist"]
>
> * 创建 Azure 数据库迁移服务的实例。
> * 使用 Azure 数据库迁移服务创建迁移项目。
> * 运行迁移。
> * 监视迁移。

在本教程中，你将使用 Azure 数据库迁移服务，将 Azure 虚拟机中托管的 MongoDB 中的某个数据集迁移到 Azure Cosmos DB 的用于 MongoDB 的 API。 如果尚未设置 MongoDB 源，请参阅[在 Azure 中的 Windows VM 上安装和配置 MongoDB](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb) 一文。

## <a name="prerequisites"></a>先决条件

要完成本教程，需要：

* [完成迁移前](../cosmos-db/mongodb-pre-migration.md)步骤，例如估计吞吐量、选择分区键和索引策略。
* [创建 Azure Cosmos DB 的用于 MongoDB 的 API 帐户](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)。
* 使用 Azure 资源管理器部署模型创建 Azure 数据库迁移服务的 Azure 虚拟网络 (VNet)，它将使用 [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) 或 [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways) 为本地源服务器提供站点到站点连接。 有关创建 VNet 的详细信息，请参阅[虚拟网络文档](https://docs.microsoft.com/azure/virtual-network/)，尤其是提供了分步详细信息的快速入门文章。

    > [!NOTE]
    > 在 VNet 设置期间，如果将 ExpressRoute 与 Microsoft 的网络对等互连一起使用，请将以下服务[终结点](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)添加到将在其中预配服务的子网：
    >
    > * 目标数据库终结点（例如，SQL 终结点、Cosmos DB 终结点等）
    > * 存储终结点
    > * 服务总线终结点
    >
    > Azure 数据库迁移服务缺少 Internet 连接，因此必须提供此配置。

* 请确保 VNet 网络安全组 (NSG) 规则未阻止以下通信端口：53、443、445、9354 以及 10000-20000。 有关 Azure VNet NSG 流量筛选的更多详细信息，请参阅[使用网络安全组筛选网络流量](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)一文。
* 打开 Windows 防火墙，使 Azure 数据库迁移服务能够访问源 MongoDB 服务器（默认情况下为 TCP 端口 27017）。
* 在源数据库的前面使用了防火墙设备时，可能需要添加防火墙规则以允许 Azure 数据库迁移服务访问要迁移的源数据库。

## <a name="register-the-microsoftdatamigration-resource-provider"></a>注册 Microsoft.DataMigration 资源提供程序

1. 登录到 Azure 门户，选择“所有服务”  ，然后选择“订阅”  。

   ![显示门户订阅](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. 选择要在其中创建 Azure 数据库迁移服务实例的订阅，再选择“资源提供程序”  。

    ![显示资源提供程序](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. 搜索迁移服务，再选择“Microsoft.DataMigration”右侧的“注册”   。

    ![注册资源提供程序](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>创建实例

1. 在 Azure 门户中，选择 **+ 创建资源**，搜索 Azure 数据库迁移服务，然后从下拉列表选择**Azure 数据库迁移服务**。

    ![Azure 市场](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. 在“Azure 数据库迁移服务”屏幕上，选择“创建”   。

    ![创建 Azure 数据库迁移服务实例](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. 在“创建迁移服务”屏幕上，为服务、订阅以及新的或现有资源组指定名称  。

4. 选择要在其中创建 Azure 数据库迁移服务实例的位置。 

5. 选择现有的 VNet，或新建一个 VNet。

    VNet 为 Azure 数据库迁移服务提供了对源 MongoDB 实例和目标 Azure Cosmos DB 帐户的访问权限。

    若要详细了解如何在 Azure 门户中创建 VNet，请参阅[使用 Azure 门户创建虚拟网络](https://aka.ms/DMSVnet)一文。

6. 选择定价层。

    有关成本和定价层的详细信息，请参阅[价格页](https://aka.ms/dms-pricing)。

    ![配置 Azure 数据库迁移服务实例设置](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. 选择“创建”  来创建服务。

## <a name="create-a-migration-project"></a>创建迁移项目

创建服务后，在 Azure 门户中找到并打开它，然后创建一个新的迁移项目。

1. 在 Azure 门户中，选择“所有服务”  ，搜索 Azure 数据库迁移服务，然后选择“Azure 数据库迁移服务”  。

      ![查找 Azure 数据库迁移服务的所有实例](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. 在“Azure 数据库迁移服务”屏幕上，搜索你创建的 Azure 数据库迁移服务实例名称，然后选择该实例  。

3. 选择“+ 新建迁移项目”  。

4. 在“新建迁移项目”屏幕上指定项目名称，在“源服务器类型”文本框中选择“MongoDB”，在“目标服务器类型”文本框中选择“CosmosDB (MongoDB API)”，然后在“选择活动类型”中选择“脱机数据迁移”。        

    ![创建数据库迁移服务项目](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. 选择“创建并运行活动”，以便创建项目并运行迁移活动。 

## <a name="specify-source-details"></a>指定源详细信息

1. 在“源详细信息”  屏幕上，指定源 MongoDB 服务器的连接详细信息。

    可通过三种模式连接到源：
   * **标准模式**：接受完全限定的域名或 IP 地址、端口号和连接凭据。
   * **连接字符串模式**：接受[连接字符串 URI 格式](https://docs.mongodb.com/manual/reference/connection-string/)一文中所述的 MongoDB 连接字符串。
   * **Azure 存储中的数据**：接受 Blob 容器 SAS URL。 如果 Blob 容器包含 MongoDB [bsondump 工具](https://docs.mongodb.com/manual/reference/program/bsondump/)生成的 BSON 转储，请选择“Blob 包含 BSON 转储”；如果容器包含 JSON 文件，请取消选择该选项。 

     如果选择此选项，则请确保存储帐户连接字符串按以下格式显示：

     ```
     https://blobnameurl/container?SASKEY
     ```

     可以在 Azure 存储资源管理器中找到此 blob 容器 SAS 连接字符串。 为相关容器创建 SA 将为你提供上述请求格式的 URL。
     
     此外，根据 Azure 存储中的类型转储选项，记住以下详细信息。

     * 对于 BSON 转储，blob 容器中的数据必须采用 bsondump 格式，这样数据文件才会放置到按 collection.bson 格式以包含数据库命令的文件夹中。 元数据文件（如有）应采用 collection.metadata.json 格式进行命名  。

     * 对于 JSON 转储，blob 容器中的文件必须放置到以包含数据库命名的文件夹中。 在每个数据库文件夹中，数据文件必须放置到名为“数据”且采用 collection.json 格式命名的子文件夹中  。 元数据文件（如有）必须放置到名为“元数据”且同样采用 collection.json 格式命名的子文件夹中  。 元数据文件必须采用由 MongoDB bsondump 工具所生成的相同格式。

    > [!IMPORTANT]
    > 建议不要在 mongo 服务器上使用自签名证书。 但是，如果使用了自签名证书，请使用**连接字符串模式**连接到服务器，并确保连接字符串包含 “”
    >
    >```
    >&sslVerifyCertificate=false
    >```

   即使不能使用 DNS 名称解析，也可使用 IP 地址。

   ![指定源详细信息](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. 选择“保存”。 

## <a name="specify-target-details"></a>指定目标详细信息

1. 在“迁移目标详细信息”屏幕上，指定目标 Azure Cosmos DB 帐户的连接详细信息，该帐户是要将 MongoDB 数据迁移到其中的已提前预配的 Azure Cosmos DB 的用于 MongoDB 的 API 帐户  。

    ![指定目标详细信息](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. 选择“保存”。 

## <a name="map-to-target-databases"></a>映射到目标数据库

1. 在“映射到目标数据库”  屏幕上，映射源和目标数据库以进行迁移。

    如果目标数据库包含的数据库名称与源数据库的相同，则 Azure 数据库迁移服务默认会选择目标数据库。

    如果字符串 **Create** 显示在数据库名称旁边，则表明 Azure 数据库迁移服务没有找到目标数据库，因此会为你创建该数据库。

    在迁移的这个时候，可以[预配吞吐量](https://docs.microsoft.com/azure/cosmos-db/set-throughput)。 在 Cosmos DB 中，可在数据库级别预配吞吐量，也可为每个集合单独进行预配。 吞吐量以[请求单位](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU) 来度量。 深入了解 [Azure Cosmos DB 定价](https://azure.microsoft.com/pricing/details/cosmos-db/)。

    ![映射到目标数据库](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. 选择“保存”。 
3. 在“集合设置”屏幕上，展开集合列表，然后查看要迁移的集合的列表。 

    Azure 数据库迁移服务会自动选择存在于源 MongoDB 实例上但不存在于目标 Azure Cosmos DB 帐户上的所有集合。 若要重新迁移已含数据的集合，需要在此边栏选项卡上明确选择集合。

    可以指定希望集合使用的 RU 量。 Azure 数据库迁移服务建议使用根据集合大小设置的智能默认值。

    > [!NOTE]
    > 必要时使用 Azure 数据库迁移服务的多个实例并行执行数据库迁移和集合，以便加快运行速度。

    也可指定分片键来利用 [Azure Cosmos DB 中的分区](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview)，以便优化可伸缩性。 确保查看[选择分片/分区键的最佳做法](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey)。

    ![选择集合表](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. 选择“保存”。 

5. 在“迁移摘要”屏幕的“活动名称”文本框中指定迁移活动的名称。  

    ![迁移摘要](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>运行迁移

* 选择“运行迁移”  。

    迁移活动窗口随即出现，活动的“状态”为“未启动”   。

    ![活动状态](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>监视迁移

* 在迁移活动屏幕上，选择“刷新”  来更新显示，直到迁移的“状态”  显示为“已完成”  。

   > [!NOTE]
   > 可以选择“活动”，以便获取数据库级别和集合级别迁移指标的详细信息。

    ![活动状态为“已完成”](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>验证 Cosmos DB 中的数据

* 在迁移完成后，可以检查你的 Azure Cosmos DB 帐户，验证所有集合是否已成功迁移。

    ![活动状态为“已完成”](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>迁移后优化

将 MongoDB 数据库中存储的数据迁移到 Azure Cosmos DB 的 API for MongoDB 后，你可以连接到 Azure Cosmos DB 并管理数据。 你还可以执行其他迁移后优化步骤，例如优化索引策略，为你的 Azure Cosmos DB 帐户更新默认一致性级别或配置全局分发。 有关详细信息，请参阅[迁移后优化](../cosmos-db/mongodb-post-migration.md)一文。

## <a name="additional-resources"></a>其他资源

* [Cosmos DB 服务信息](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>后续步骤

* 在 Microsoft 的 [Database Migration Guide](https://datamigration.microsoft.com/)（数据库迁移指南）中查看迁移指南以了解其他方案。
