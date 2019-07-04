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
ms.openlocfilehash: 4fec9be34e390498b85ecfcb3f3b61055a08fdd2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/18/2019
ms.locfileid: "67172641"
---
1. 在新浏览器窗口中，登录到 [Azure 门户](https://portal.azure.com/)。
2. 在左侧导航窗格中，选择“创建资源”  。 选择“数据库”，然后选择“Azure Cosmos DB”   。
   
   ![Azure 门户的屏幕截图，其中突出显示“更多服务”和“Azure Cosmos DB”](./media/cosmos-db-create-dbaccount-table/create-nosql-db-databases-json-tutorial-1.png)

3. 在“创建 Azure Cosmos DB 帐户”页中，输入新 Azure Cosmos DB 帐户的设置  ：
 
    设置|值|Description
    ---|---|---
    订阅|订阅|选择要用于此 Azure Cosmos DB 帐户的 Azure 订阅。 
    资源组|新建<br><br>然后输入在 ID 中提供的同一唯一名称|选择“新建”。  然后输入帐户的新资源组名称。 为简单起见，可以使用与 ID 相同的名称。 
    帐户名|输入唯一的名称|输入标识此 Azure Cosmos DB 帐户的唯一名称。<br><br>该 ID 只能使用小写字母、数字和连字符 (-) 字符。 长度必须在 3 到 31 个字符之间。
    API|Azure 表|API 确定要创建的帐户的类型。 Azure Cosmos DB 提供五种 API：适用于文档数据库的 Core(SQL)、适用于图形数据库的 Gremlin、适用于文档数据库的 MongoDB、Azure 表和 Cassandra。 目前，你必须为每种 API 创建单独的帐户。 <br><br>选择“Azure 表”，因为在本快速入门中，将创建一个使用表 API 的表  。 <br><br>[详细了解表 API](../articles/cosmos-db/table-introduction.md)。|
    位置|选择离用户最近的区域|选择用于托管 Azure Cosmos DB 帐户的地理位置。 使用离用户最近的位置，使他们能够以最快的速度访问数据。

    可以将“异地冗余”和“多区域写入”选项保留为默认值（“禁用”）以避免产生额外的 RU 费用    。 可以跳过“网络”和“标记”部分   。

5. 选择“查看 + 创建”  。 完成验证后，选择“创建”以创建帐户  。 
 
   ![Azure Cosmos DB 的“新建帐户”页](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-create-new-account.png)

6. 创建帐户需要几分钟时间。 你将看到一条说明“部署正在进行”的消息  。 等待部署完成，然后选择“转到资源”  。

    ![Azure 门户“通知”窗格](./media/cosmos-db-create-dbaccount-table/azure-cosmos-db-account-created.png)
