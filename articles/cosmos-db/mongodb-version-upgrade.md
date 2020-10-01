---
title: 升级 Azure Cosmos DB 的 API for MongoDB 帐户的 Mongo 版本
description: 如何无缝地为现有 Azure Cosmos DB 的 API 升级 MongoDB 线路协议版本
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: guide
ms.date: 09/22/2020
ms.author: jasonh
ms.openlocfilehash: c6369be39d0a964f07c64083e3269bb1c0c49c7f
ms.sourcegitcommit: b48e8a62a63a6ea99812e0a2279b83102e082b61
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2020
ms.locfileid: "91409657"
---
# <a name="upgrade-the-mongodb-wire-protocol-version-of-your-azure-cosmos-dbs-api-for-mongodb-account"></a>升级 Azure Cosmos DB API for MongoDB 帐户的 MongoDB 线路协议版本

本文介绍如何升级适用于 MongoDB 帐户的 Azure Cosmos DB 的 API 的网络协议版本。 升级无线协议版本后，可以使用 Azure Cosmos DB 的 API for MongoDB 中的最新功能。 升级过程不会中断帐户的可用性，也不会在任何时间点使用 RU/秒或降低数据库的容量。 此过程不会影响现有数据或索引。

>[!Note]
> 目前，只能将使用服务器版本3.2 的限定帐户升级到版本3.6。 如果你的帐户未显示升级选项，请提交 [支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。

## <a name="upgrading-from-version-32-to-36"></a>从版本3.2 升级到3。6

### <a name="benefits-of-upgrading-to-version-36"></a>升级到版本 3.6 的优点

以下是版本3.6 中包含的新功能：
- 增强性能和稳定性
- 支持新的数据库命令
- 支持聚合管道（默认）和新的聚合阶段
- 对更改流的支持
- 支持复合索引
- 对以下操作的跨分区支持：更新、删除、计数和排序
- 提高了以下聚合操作的性能： $count、$skip、$limit 和 $group
- 现在支持通配符索引

### <a name="changes-from-version-32"></a>版本3.2 中的更改

- **RequestRateIsLarge 错误**已被删除。 来自客户端应用程序的请求将不再返回16500错误。 相反，请求将继续执行，直到完成或完成超时。
- 每个请求超时设置为60秒。
- 默认情况下，在新的线路协议版本上创建的 MongoDB 集合将仅 `_id` 索引属性。

### <a name="action-required"></a>必需的操作

升级到版本3.6 时，数据库帐户终结点后缀将更新为以下格式：

```
<your_database_account_name>.mongo.cosmos.azure.com
```

你需要将应用程序和驱动程序中的现有终结点替换为此数据库帐户。 **只有使用新终结点的连接才能访问 MongoDB 版本3.6 中的功能**。 前一个终结点应具有后缀 `.documents.azure.com` 。

>[!Note]
> 如果你的帐户是在主权、政府或受限制的 Azure 云中创建的，则此终结点可能会略有不同。

### <a name="how-to-upgrade"></a>如何升级

1. 首先，转到 Azure 门户，导航到 Azure Cosmos DB API for MongoDB 帐户概述 "边栏选项卡。 验证服务器版本是否为 `3.2` 。 

    :::image type="content" source="./media/mongodb-version-upgrade/1.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

2. 从左侧的选项中，选择边栏选项 `Features` 卡。 这会显示可用于数据库帐户的帐户级别功能。

    :::image type="content" source="./media/mongodb-version-upgrade/2.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

3. 单击 `Upgrade to Mongo server version 3.6` 该行。 如果看不到此选项，则表示你的帐户可能没有此升级的资格。 如果是这种情况，请提交 [支持票证](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) 。

    :::image type="content" source="./media/mongodb-version-upgrade/3.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

4. 查看关于此特定升级显示的信息。 请注意，只有在应用程序使用更新的终结点时才会完成升级，如本部分中所述。 `Enable`一旦准备好开始该过程，请单击 "打开"。

    :::image type="content" source="./media/mongodb-version-upgrade/4.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

5. 启动进程后， `Features` 菜单将显示升级状态。 状态将从 `Pending` 、到到 `In Progress` `Upgraded` 。 此过程不会影响数据库帐户的现有功能或操作。

    :::image type="content" source="./media/mongodb-version-upgrade/5.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

6. 升级完成后，状态将显示为 `Upgraded` 。 单击它以了解有关完成此过程所需执行的后续步骤和操作的详细信息。 如果处理请求时出现问题，请 [联系支持人员](https://azure.microsoft.com/en-us/support/create-ticket/) 。

    :::image type="content" source="./media/mongodb-version-upgrade/6.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

7. **若要开始使用数据库帐户的升级版本**，请返回到 `Overview` 边栏选项卡，并复制要在应用程序中使用的新连接字符串。 当应用程序连接到新终结点后，将立即开始使用升级后的版本。 现有连接不会中断，并且可以在方便时进行更新。 为了确保获得一致的体验，所有应用程序都必须使用新的终结点。

    :::image type="content" source="./media/mongodb-version-upgrade/7.png" alt-text="Azure 门户与 MongoDB 帐户概述" border="false":::

## <a name="next-steps"></a>后续步骤

- 了解 [MongoDB 版本 3.6](mongodb-feature-support-36.md)的支持和不支持的功能。
- 有关详细信息，请查看 [Mongo 3.6 版本功能](https://devblogs.microsoft.com/cosmosdb/azure-cosmos-dbs-api-for-mongodb-now-supports-server-version-3-6/)
