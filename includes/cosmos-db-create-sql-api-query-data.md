---
title: include 文件
description: include 文件
services: cosmos-db
author: SnehaGunda
ms.service: cosmos-db
ms.topic: include
ms.date: 04/05/2019
ms.author: sngun
ms.custom: include file
ms.openlocfilehash: 609b62312329b3a8f9f16d15458a0a47f5eb377f
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "85115195"
---
可以在数据资源管理器中使用查询来检索和筛选数据。

1. 在数据资源管理器的“项”选项卡顶部，查看默认查询 `SELECT * FROM c`。 此查询检索并显示容器中按 ID 排序的所有文档。 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-query.png" alt-text="数据资源管理器中的默认查询是“SELECT * FROM c”":::
   
1. 若要更改查询，请选择“编辑筛选器”，将默认查询替换为 `ORDER BY c._ts DESC`，然后选择“应用筛选器”。
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edit-query.png" alt-text="数据资源管理器中的默认查询是“SELECT * FROM c”":::

   此修改后的查询根据文档的时间戳按降序显示文档，所以现在最先列出的是第二个文档。 
   
   :::image type="content" source="./media/cosmos-db-create-sql-api-query-data/azure-cosmosdb-data-explorer-edited-query.png" alt-text="数据资源管理器中的默认查询是“SELECT * FROM c”":::

如果熟悉 SQL 语法，可以在查询谓词框中输入任何受支持的 [SQL 查询](../articles/cosmos-db/sql-api-sql-query.md)。 还可以使用数据资源管理器创建存储过程、UDF 和触发器以执行服务器端业务逻辑。 

数据资源管理器可以通过 Azure 门户轻松访问 API 中提供的所有内置编程数据访问功能。 也可通过门户缩放吞吐量、获取密钥和连接字符串，以及查看 Azure Cosmos DB 帐户的指标和 SLA。 

