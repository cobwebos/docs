---
title: 使用 Robo 3T 连接到 Azure Cosmos DB
description: 了解如何使用 Robo 3T 和 Azure Cosmos DB 的 API for MongoDB 连接到 Azure Cosmos DB
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 12/26/2018
author: sivethe
ms.author: sivethe
ms.openlocfilehash: ab066fef4bffe35408fdc5f3ad6d66796b4d0818
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2019
ms.locfileid: "57443964"
---
# <a name="use-robo-3t-with-azure-cosmos-dbs-api-for-mongodb"></a>将 Robo 3T 与 Azure Cosmos DB 的 API for MongoDB 配合使用

若要使用 Robo 3T 连接到 Cosmos 帐户，必须：

* 下载并安装 [Robo 3T](https://robomongo.org/)
* 获得 Cosmos DB [连接字符串](connect-mongodb-account.md)信息

## <a name="connect-using-robo-3t"></a>使用 Robo 3T 进行连接
要将 Cosmos 帐户添加到 Robo 3T 连接管理器，请执行以下步骤：

1. 使用[此处](connect-mongodb-account.md)的说明检索使用 Azure Cosmos DB 的 API MongoDB 配置的 Cosmos 帐户的连接信息。

    ![连接字符串边栏选项卡的屏幕截图](./media/mongodb-robomongo/connectionstringblade.png)
2. 运行 Robomongo.exe

3. 单击“文件”下的“连接”按钮以管理连接。 然后，在“MongoDB 连接”窗口中单击“创建”，这会打开“连接设置”窗口。

4. 在“连接设置”窗口中，选择名称。 然后，从步骤 1 的连接信息中找到**主机**和**端口**，并将其分别输入到“地址”和“端口”中。

    ![Robomongo 管理连接的屏幕截图](./media/mongodb-robomongo/manageconnections.png)
5. 在“身份验证”选项卡上，单击“执行身份验证”。 然后，输入数据库（默认值为 Admin）、用户名和密码。
**用户名**和**密码**可以在步骤 1 的连接信息中找到。

    ![Robomongo 身份验证选项卡的屏幕截图](./media/mongodb-robomongo/authentication.png)
6. 在“SSL”选项卡上，选中“使用 SSL 协议”，然后将“身份验证方法”更改为“自签名证书”。

    ![Robomongo SSL 选项卡的屏幕截图](./media/mongodb-robomongo/SSL.png)
7. 最后，单击“测试”验证是否能够连接，并单击“保存”。

## <a name="next-steps"></a>后续步骤

- 了解如何配合[使用 Studio 3T](mongodb-mongochef.md) 与 Azure Cosmos DB 的用于 MongoDB 的 API。
- 使用 Azure Cosmos DB 的用于 MongoDB 的 API 浏览 MongoDB [示例](mongodb-samples.md)。
