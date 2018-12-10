---
title: include 文件
description: include 文件
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/13/2018
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: a144433e1ff6c0f7ca4e0ed72704d339150f2d56
ms.sourcegitcommit: c8088371d1786d016f785c437a7b4f9c64e57af0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2018
ms.locfileid: "52643449"
---
1. 在新浏览器窗口中，登录到 [Azure 门户](https://portal.azure.com/)。
2. 选择“创建资源” > “数据库” > “Azure Cosmos DB”。
   
   ![Azure 门户“数据库”窗格](./media/cosmos-db-create-dbaccount/create-nosql-db-databases-json-tutorial-1.png)

3. 在“新建帐户”页上，输入新 Azure Cosmos DB 帐户的设置。 
 
    设置|值|Description
    ---|---|---
    ID|输入唯一的名称|输入标识此 Azure Cosmos DB 帐户的唯一名称。 由于 documents.azure.com 字符串将追加到所提供的 ID 以创建 URI，因此，请使用唯一的 ID。<br><br>该 ID 只能使用小写字母、数字和连字符 (-) 字符。 其长度必须为 3 到 50 个字符。
    API|SQL|API 确定要创建的帐户的类型。 Azure Cosmos DB 提供五种 API：适用于文档数据库的 SQL、适用于图形数据库的 Gremlin、适用于文档数据库的 MongoDB、表 API 和 Cassandra API。 目前，你必须为每种 API 创建单独的帐户。 <br><br>之所以选择 **SQL** 是因为本文将使用 SQL 语法创建文档数据库和查询。 <br><br>[详细了解 SQL API](../articles/cosmos-db/documentdb-introduction.md)。|
    订阅|订阅|选择要用于此 Azure Cosmos DB 帐户的 Azure 订阅。 
    资源组|新建<br><br>然后输入在 ID 中提供的同一唯一名称|选择“新建”。 然后输入帐户的新资源组名称。 为简单起见，可以使用与 ID 相同的名称。 
    位置|选择离用户最近的区域|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。
    启用异地冗余| 留空 | 此选项将在第二个（配对）区域中创建数据库的复制版本。 将此复选框留空。 

    选择“创建”。

    ![Azure Cosmos DB 的“新建帐户”页](./media/cosmos-db-create-dbaccount/azure-cosmos-db-create-new-account.png)

4. 创建帐户需要几分钟时间。 等待门户中显示“祝贺你!已创建 Azure Cosmos DB 帐户”页。

    ![Azure 门户“通知”窗格](./media/cosmos-db-create-dbaccount/azure-cosmos-db-account-created.png)

