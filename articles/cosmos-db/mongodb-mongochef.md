---
title: 对 Azure Cosmos DB 使用 Studio 3T (MongoChef) | Microsoft Docs
description: 了解如何配合使用 Studio 3T 与 Azure Cosmos DB MongoDB API 帐户
keywords: mongochef, studio 3T
services: cosmos-db
author: AndrewHoh
manager: kfile
documentationcenter: ''
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/18/2018
ms.author: anhoh
ms.openlocfilehash: 98f1a83ad2470d4e133167b6c4d140a0aa34e114
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2018
---
# <a name="azure-cosmos-db-use-studio-3t-with-a-mongodb-api-account"></a>Azure Cosmos DB：配合使用 Studio 3T 与 MongoDB API 帐户

若要连接到 Azure Cosmos DB MongoDB API 帐户，必须：

* 下载并安装 [Studio 3T](https://studio3t.com/)（以前称为 MongoChef）
* 具有 MongoDB 帐户的 Azure Cosmos DB [连接字符串](connect-mongodb-account.md)信息

## <a name="create-the-connection-in-studio-3t"></a>在 Studio 3T 中创建连接
要将 Azure Cosmos DB 帐户添加到 Studio 3T 连接管理器，请执行以下步骤：

1. 按照[将 MongoDB 应用程序连接到 Azure Cosmos DB](connect-mongodb-account.md) 一文中的说明，检索 MongoDB API 帐户的 Azure Cosmos DB 连接信息。

    ![连接字符串页的屏幕截图](./media/mongodb-mongochef/ConnectionStringBlade.png)
2. 单击“连接”以打开“连接管理器”，并单击“新建连接”

    ![Studio 3T 连接管理器的屏幕截图](./media/mongodb-mongochef/ConnectionManager.png)
3. 在“新建连接”窗口中的“服务器”选项卡上，输入 Azure Cosmos DB 帐户的主机 (FQDN) 和端口。

    ![Studio 3T 连接管理器服务器选项卡的屏幕截图](./media/mongodb-mongochef/ConnectionManagerServerTab.png)
4. 在“新建连接”窗口中的“身份验证”选项卡上，选择“基本(MONGODB-CR 或 SCARM-SHA-1)”身份验证模式，并输入用户名和密码。  接受默认的身份验证数据库（管理员），或提供自己的值。

    ![Studio 3T 连接管理器身份验证选项卡的屏幕截图](./media/mongodb-mongochef/ConnectionManagerAuthenticationTab.png)
5. 在“新建连接”窗口的“SSL”选项卡上，选中“使用 SSL 协议进行连接”复选框和“接受服务器自签名 SSL 证书”单选按钮。

    ![Studio 3T 连接管理器 SSL 选项卡的屏幕截图](./media/mongodb-mongochef/ConnectionManagerSSLTab.png)
6. 单击“测试连接”按钮以验证连接信息，单击“确定”以返回到“新建连接”窗口，并单击“保存”。

    ![Studio 3T 测试连接窗口的屏幕截图](./media/mongodb-mongochef/TestConnectionResults.png)

## <a name="use-studio-3t-to-create-a-database-collection-and-documents"></a>使用 Studio 3T 创建数据库、集合和文档
若要使用 Studio 3T 创建数据库、集合和文档，请执行以下步骤：

1. 在“连接管理器”中突出显示连接，并单击“连接”。

    ![Studio 3T 连接管理器的屏幕截图](./media/mongodb-mongochef/ConnectToAccount.png)
2. 右键单击主机，并选择“添加数据库”。  提供数据库名称，并单击“确定”。

    ![Studio 3T“添加数据库”选项的屏幕截图](./media/mongodb-mongochef/AddDatabase1.png)
3. 右键单击数据库，并选择“添加集合”。  提供集合名称，并单击“创建”。

    ![Studio 3T“添加集合”选项的屏幕截图](./media/mongodb-mongochef/AddCollection.png)
4. 单击“集合”菜单项，并单击“添加文档”。

    ![Studio 3T“添加文档”菜单项的屏幕截图](./media/mongodb-mongochef/AddDocument1.png)
5. 在“添加文档”对话框中粘贴以下内容，并单击“添加文档”。

        {
        "_id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
               { "firstName": "Thomas" },
               { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "isRegistered": true
        }
6. 添加另一个文档，但这次使用以下内容：

        {
        "_id": "WakefieldFamily",
        "parents": [
            { "familyName": "Wakefield", "givenName": "Robin" },
            { "familyName": "Miller", "givenName": "Ben" }
        ],
        "children": [
            {
                "familyName": "Merriam",
                 "givenName": "Jesse",
                "gender": "female", "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            {
                "familyName": "Miller",
                 "givenName": "Lisa",
                 "gender": "female",
                 "grade": 8 }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
        }
7. 执行示例查询。 例如，搜索姓氏为“Andersen”的家庭，并返回父母和州/省字段。

    ![Mongo Chef 查询结果的屏幕截图](./media/mongodb-mongochef/QueryDocument1.png)

## <a name="next-steps"></a>后续步骤
* 浏览 Azure Cosmos DB MongoDB API [示例](mongodb-samples.md)。
