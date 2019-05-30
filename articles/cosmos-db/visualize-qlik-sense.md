---
title: 将 Qlik Sense 连接到 Azure Cosmos DB 并可视化数据
description: 本文介绍了将 Azure Cosmos DB 连接到 Qlik Sense 并可视化数据所需执行的步骤。
ms.service: cosmos-db
author: SnehaGunda
ms.author: sngun
ms.topic: conceptual
ms.date: 05/23/2019
ms.reviewer: sngun
ms.openlocfilehash: eea4366ca19c01af1f79566a5ca6bcae8805b6c3
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/27/2019
ms.locfileid: "66242498"
---
# <a name="connect-qlik-sense-to-azure-cosmos-db-and-visualize-your-data"></a>将 Qlik Sense 连接到 Azure Cosmos DB 并可视化数据

Qlik Sense 是一个数据可视化工具，可将来自不同源的数据合并到单个视图。 Qlik Sense 对数据中每种可能的关系编制了索引，以便可以快速深入了解数据。 可以使用 Qlik Sense 可视化 Azure Cosmos DB 数据。 本文介绍了将 Azure Cosmos DB 连接到 Qlik Sense 并可视化数据所需执行的步骤。 

> [!NOTE]
> 目前仅 SQL API 和 Azure Cosmos DB 的 API for MongoDB 帐户支持将 Qlik Sense 连接到 Azure Cosmos DB。

可以将 Qlik Sense 连接到以下 Azure Cosmos DB：

* Cosmos DB SQL API，通过使用 ODBC 连接器。

* Azure Cosmos DB 的 API for MongoDB，通过使用 Qlik Sense MongoDB 连接器（目前提供预览版）。

* Azure Cosmos DB 的 API for MongoDB 和 SQL API，通过使用 Qlik Sense 中的 REST API 连接器。

* Cosmos DB Mongo DB API，通过使用 Qlik Core 的 gRPC 连接器。
本文介绍了使用 ODBC 连接器连接到 Cosmos DB SQL API 的详细信息。

本文介绍了使用 ODBC 连接器连接到 Cosmos DB SQL API 的详细信息。

## <a name="prerequisites"></a>必备组件

在按照本文中的说明操作之前，请确保以下资源准备就绪：

* 下载 [Qlik Sense 桌面](https://www.qlik.com/us/try-or-buy/download-qlik-sense)或通过 [安装 Qlik Sense 市场项](https://azuremarketplace.microsoft.com/marketplace/apps/qlik.qlik-sense)在 Azure 中设置 Qlik Sense。

* 下载[电子游戏机数据](https://www.kaggle.com/gregorut/videogamesales)，此示例数据采用 CSV 格式。 将此数据存储在 Cosmos DB 帐户中并在 Qlik Sense 可视化该数据。

* 使用快速入门文章的[创建帐户](create-sql-api-dotnet.md#create-account)部分所述的步骤创建 Azure Cosmos DB SQL API 帐户。

* [创建数据库和集合](create-sql-api-dotnet.md#create-collection-database) – 可以将集合吞吐量值设置为 1000 RU/秒。 

* 将示例电子游戏机销售数据加载到 Cosmos DB 帐户。 可以使用 Azure Cosmos DB 数据迁移工具导入数据，可以执行[按顺序](import-data.md#SQLSeqTarget)或[批量导入](import-data.md#SQLBulkTarget)数据。 大约需要 3 到 5 分钟才能将数据导入到 Cosmos DB 帐户。

* 使用[通过 ODBC 驱动程序连接到 Cosmos DB](odbc-driver.md)一文中的步骤下载、安装和配置 ODBC 驱动程序。 电子游戏机数据是简单的数据集，无需编辑架构，只需使用默认的集合映射架构。

## <a name="connect-qlik-sense-to-cosmos-db"></a>将 Qlik Sense 连接到 Cosmos DB

1. 打开 Qlik Sense，然后选择“创建新应用”  。 为应用提供一个名称，然后选择“创建”  。

   ![创建新的 Qlik Sense 应用](./media/visualize-qlik-sense/create-new-qlik-sense-app.png)

2. 成功创建新应用后，选择“打开应用”  ，然后选择“添加来自文件和其他源的数据”  。 

3. 从数据源中，选择“ODBC”  以打开新的连接设置窗口。 

4. 切换到“用户 DSN”  ，然后选择之前创建的 ODBC 连接。 为连接提供一个名称，然后选择“创建”  。 

   ![创建新连接](./media/visualize-qlik-sense/create-new-connection.png)

5. 创建连接后，可以选择数据库、电子游戏机数据所在的集合，然后进行预览。

   ![选择数据库和集合](./media/visualize-qlik-sense/choose-database-and-collection.png) 

6. 接下来，选择“添加数据”  以将数据加载到 Qlik Sense。 将数据加载到 Qlik Sense 后，可以生成见解并对数据执行分析。 可以使用见解，也可以自行生成用来浏览电子游戏机销售的应用。 下图显示 

   ![可视化数据](./media/visualize-qlik-sense/visualize-data.png)

### <a name="limitations-when-connecting-with-odbc"></a>使用 ODBC 连接时的限制 

Cosmos DB 是一个无架构的分布式数据库，其中驱动程序是根据开发人员需要建模的。 ODBC 驱动程序需要数据库带有用来推断列、其数据类型和其他属性的架构。 由于 SQL API 不是 ANSI SQL，因此具有关系功能的常规 SQL 查询或 DML 语法不适用于 Cosmos DB SQL API。 由于此原因，通过 ODBC 驱动程序发出的 SQL 语句会转换为不具有所有构造的等效项的特定于 Cosmos DB 的 SQL 语法。 若要防止这些转换问题，必须在设置 ODBC 连接时应用架构。 [通过 ODBC 驱动程序连接](odbc-driver.md)一文提供了用来帮助你配置架构的建议和方法。 请确保为 Cosmos DB 帐户内的每个数据库/集合创建此映射。

## <a name="next-steps"></a>后续步骤

如果使用 Power BI 等不同的可视化工具，则可以使用以下文档中的说明连接到该工具：

* [使用 Power BI 连接器可视化 Cosmos DB 数据](powerbi-visualize.md)
