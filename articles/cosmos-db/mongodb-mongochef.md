---
title: 使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB
description: 了解如何使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB。
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: how-to
ms.date: 03/20/2020
author: timsander1
ms.author: tisande
ms.custom: seodec18
ms.openlocfilehash: 18a9f97d2cee9dd17345a1c8c0ae0efe442d79a4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85799388"
---
# <a name="connect-to-an-azure-cosmos-account-using-studio-3t"></a>使用 Studio 3T 连接到 Azure Cosmos 帐户

若要使用 Studio 3T 连接到 Azure Cosmos DB 的 API for MongoDB，必须执行以下操作：

* 下载并安装 [Studio 3T](https://studio3t.com/)。
* 获取 Azure Cosmos 帐户的[连接字符串](connect-mongodb-account.md)信息。

## <a name="create-the-connection-in-studio-3t"></a>在 Studio 3T 中创建连接

若要将 Azure Cosmos 帐户添加到 Studio 3T 连接管理器，请执行以下步骤：

1. 按照[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 一文中的说明，检索 Azure Cosmos DB 的 API for MongoDB 帐户的连接信息。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionStringBlade.png" alt-text="连接字符串页的屏幕截图":::

2. 单击“连接”  以打开“连接管理器”，并单击“新建连接” 

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManager.png" alt-text="连接字符串页的屏幕截图":::
3. 在“新建连接”窗口中的“服务器”选项卡上，输入 Azure Cosmos 帐户的主机 (FQDN) 和端口   。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerServerTab.png" alt-text="连接字符串页的屏幕截图":::
4. 在“新建连接”  窗口中的“身份验证”  选项卡上，选择“基本(MONGODB-CR 或 SCARM-SHA-1)”  身份验证模式，并输入用户名和密码。  接受默认的身份验证数据库（管理员），或提供自己的值。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png" alt-text="连接字符串页的屏幕截图":::
5. 在“新建连接”  窗口的“SSL”  选项卡上，选中“使用 SSL 协议进行连接”  复选框和“接受服务器自签名 SSL 证书”  单选按钮。

    :::image type="content" source="./media/mongodb-mongochef/ConnectionManagerSSLTab.png" alt-text="连接字符串页的屏幕截图":::
6. 单击“测试连接”  按钮以验证连接信息，单击“确定”  以返回到“新建连接”窗口，并单击“保存”  。

    :::image type="content" source="./media/mongodb-mongochef/TestConnectionResults.png" alt-text="连接字符串页的屏幕截图":::

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>使用 Studio 3T 创建数据库、集合和文档
若要使用 Studio 3T 创建数据库、集合和文档，请执行以下步骤：

1. 在“连接管理器”  中突出显示连接，并单击“连接”  。

    :::image type="content" source="./media/mongodb-mongochef/ConnectToAccount.png" alt-text="连接字符串页的屏幕截图":::
2. 右键单击主机，并选择“添加数据库”  。  提供数据库名称，并单击“确定”  。

    :::image type="content" source="./media/mongodb-mongochef/AddDatabase1.png" alt-text="连接字符串页的屏幕截图":::
3. 右键单击数据库，并选择“添加集合”  。  提供集合名称，并单击“创建”  。

    :::image type="content" source="./media/mongodb-mongochef/AddCollection.png" alt-text="连接字符串页的屏幕截图":::
4. 单击“集合”  菜单项，并单击“添加文档”  。

    :::image type="content" source="./media/mongodb-mongochef/AddDocument1.png" alt-text="连接字符串页的屏幕截图" },
        "isRegistered": false
    }
    ```

7. 执行示例查询。 例如，搜索姓氏为“Andersen”的家庭，并返回父母和州/省字段。

    :::image type="content" source="./media/mongodb-mongochef/QueryDocument1.png" alt-text="连接字符串页的屏幕截图":::

## <a name="next-steps"></a>后续步骤

- 了解如何将 [Robo 3T](mongodb-robomongo.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API 配合使用。
- 通过 Azure Cosmos DB 的用于 MongoDB 的 API 来浏览 MongoDB [示例](mongodb-samples.md)。
