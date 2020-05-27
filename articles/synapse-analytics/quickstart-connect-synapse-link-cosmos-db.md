---
title: 连接到 Azure Synapse Link for Azure Cosmos DB
description: 如何通过 Synapse Link 将 Azure Cosmos DB 连接到 Synapse 工作区
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 81cc7745790c7a89576dc5d0ff925dbaff2b2334
ms.sourcegitcommit: 595cde417684e3672e36f09fd4691fb6aa739733
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/20/2020
ms.locfileid: "83700381"
---
# <a name="connect-to-azure-synapse-link-for-azure-cosmos-db"></a>连接到 Azure Synapse Link for Azure Cosmos DB

本文介绍如何使用 Synapse Link 从 Azure Synapse Analytics Studio 访问 Azure Cosmos DB 数据库。 

## <a name="prerequisites"></a>先决条件

将 Azure Cosmos DB 帐户连接到工作区之前，需要具有以下各项。

* 现有的 Azure Cosmos DB 帐户或按照此[快速入门](https://docs.microsoft.com/azure/cosmos-db/how-to-manage-database-account)创建一个新帐户
* 现有的 Synapse 工作区或按照此[快速入门](https://docs.microsoft.com/azure/synapse-analytics/quickstart-create-workspace)创建一个新工作区 

## <a name="enable-azure-cosmos-db-analytical-store"></a>启用 Azure Cosmos DB 分析存储

若要在不影响操作性能的情况下对 Azure Cosmos DB 运行大规模分析，建议启用 Synapse Link for Azure Cosmos DB。 此功能将 HTAP 功能引入容器中，并在 Azure Synapse 中提供内置支持。 按照此快速入门，为 Cosmos DB 容器启用 Synapse Link。

## <a name="navigate-to-synapse-studio"></a>导航到 Synapse Studio

在 Synapse 工作区中，选择“启动 Synapse Studio”。 在 Synapse Studio 主页上，选择“**数据”，这会将你转到“数据对象资源管理器”。

## <a name="connect-an-azure-cosmos-db-database-to-a-synapse-workspace"></a>将 Azure Cosmos DB 数据库连接到 Synapse 工作区

连接 Azure Cosmos DB 数据库作为链接服务完成。 Cosmos DB 链接服务使用户能够浏览和探索数据，并从 Apache Spark for Azure Synapse Analytics 或 SQL 读取或者写入到 Azure Cosmos DB。

可以在数据对象资源管理器中通过执行以下步骤直接连接 Azure Cosmos DB 数据库：

1. 选择数据旁边的 + 图标
2. 选择“连接到外部数据”
3. 选择要连接到的 API:SQL 或 MongoDB
4. 选择“继续”
5. 命名链接服务。 该名称将显示在对象资源管理器中，并由 Synapse 运行时用于连接到数据库和容器。 建议使用友好名称。
6. 选择“Cosmos DB 帐户名称”和“数据库名称”
7. （可选）如果没有指定区域，Synapse 运行时操作将被路由到启用分析存储的最近的区域。 但可以手动设置希望用户访问 Cosmos DB 分析存储的区域。 选择“其他连接属性”，然后选择“新建”。 在“属性名称”下，写入“PreferredRegions”，并将“值”设置为所需区域（例如：WestUS2，单词和数字之间没有空格）
8. 选择“创建”

Azure Cosmos DB 数据库会显示在 Azure Cosmos DB 部分中的“链接”选项卡下。 可以通过以下图标区分启用 HTAP 的 Azure Cosmos DB 容器和仅 OLTP 容器：

Synapse 容器：

![HTAP 容器](./media/quickstart-connect-synapse-link-cosmosdb/htap-container.png)

仅 OLTP 容器：

![OLTP 容器](./media/quickstart-connect-synapse-link-cosmosdb/oltp-container.png)

## <a name="quickly-interact-with-code-generated-actions"></a>与代码生成的操作快速交互

右键单击容器时，会获得一个用于触发 Spark 或 SQL 运行时的手势列表。 写入容器将通过 Azure Cosmos DB 的事务存储进行，并将消耗请求单位。  

## <a name="next-steps"></a>后续步骤

* [了解 Synapse 和 Azure Cosmos DB 均支持的功能](./synapse-link/concept-synapse-link-cosmos-db-support.md)
* [了解如何使用 Apache Spark for Azure Synapse Analytics 查询分析存储](synapse-link/how-to-query-analytical-store-spark.md)