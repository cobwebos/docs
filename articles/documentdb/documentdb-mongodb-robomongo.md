---
title: "对 Azure Cosmos DB 使用 Robomongo | Microsoft Docs"
description: "了解如何配合使用 Robomongo 与 Azure Cosmos DB: API for MongoDB 帐户"
keywords: robomongo
services: cosmosdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 352c5fb9-8772-4c5f-87ac-74885e63ecac
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/28/2017
ms.author: anhoh
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: bad2d57a6252bb30e4c8435c52da19c95304d8da
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---
# <a name="use-robomongo-with-an-azure-cosmos-db-api-for-mongodb-account"></a>配合使用 Robomongo 与 Azure Cosmos DB: API for MongoDB 帐户
若要使用 Robomongo 连接到 Azure Cosmos DB: API for MongoDB 帐户，必须：

* 下载并安装 [Robomongo](https://robomongo.org/)
* 具有 Azure Cosmos DB: API for MongoDB 帐户的[连接字符串](documentdb-connect-mongodb-account.md)信息

## <a name="connect-using-robomongo"></a>使用 Robomongo 进行连接
若要将 Azure Cosmos DB: API for MongoDB 帐户添加到 Robomongo MongoDB 连接，请执行以下步骤。

1. 使用[此处](documentdb-connect-mongodb-account.md)的说明检索 Azure Cosmos DB: API for MongoDB 帐户连接信息。

    ![连接字符串边栏选项卡的屏幕截图](./media/documentdb-mongodb-robomongo/connectionstringblade.png)
2. 运行 Robomongo.exe

3. 单击“文件”下的“连接”按钮以管理连接。 然后，在“MongoDB 连接”窗口中单击“创建”，这会打开“连接设置”窗口。

4. 在“连接设置”窗口中，选择名称。 然后，从步骤 1 的连接信息中找到**主机**和**端口**，并将其分别输入到“地址”和“端口”中。

    ![Robomongo 管理连接的屏幕截图](./media/documentdb-mongodb-robomongo/manageconnections.png)
5. 在“身份验证”选项卡上，单击“执行身份验证”。 然后，输入数据库（默认值为 Admin）、**用户名**和**密码**。
**用户名**和**密码**可以在步骤 1 的连接信息中找到。

    ![Robomongo 身份验证选项卡的屏幕截图](./media/documentdb-mongodb-robomongo/authentication.png)
6. 在“SSL”选项卡上，选中“使用 SSL 协议”，然后将“身份验证方法”更改为“自签名证书”。

    ![Robomongo SSL 选项卡的屏幕截图](./media/documentdb-mongodb-robomongo/SSL.png)
7. 最后，单击“测试”验证是否能够连接，然后单击“保存”。

## <a name="next-steps"></a>后续步骤
* 浏览 Azure Cosmos DB: API for MongoDB [示例](documentdb-mongodb-samples.md)。

