---
title: "Azure Cosmos DB 帐户的 MongoDB 连接字符串 | Microsoft Docs"
description: "了解如何使用 MongoDB 连接字符串，将 MongoDB 应用连接到 Azure Cosmos DB 帐户。"
keywords: "mongodb 连接字符串"
services: cosmos-db
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: e36f7375-9329-403b-afd1-4ab49894f75e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/19/2017
ms.author: anhoh
ms.openlocfilehash: 41c0b360b54f9a9dcd2458ce5bdc05911c4f73a5
ms.sourcegitcommit: 5ac112c0950d406251551d5fd66806dc22a63b01
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2018
---
# <a name="connect-a-mongodb-application-to-azure-cosmos-db"></a>将 MongoDB 应用程序连接到 Azure Cosmos DB
了解如何使用 MongoDB 连接字符串，将 MongoDB 应用连接到 Azure Cosmos DB 帐户。 然后，可使用 Azure Cosmos DB 数据库作为 MongoDB 应用的数据存储。 

本教程提供了两种方法来检索连接字符串信息：

- [快速启动方法](#QuickstartConnection)，适合搭配 .NET、Node.js、MongoDB Shell、Java 和 Python 驱动程序使用
- [自定义连接字符串方法](#GetCustomConnection)，适合搭配其他驱动程序使用

## <a name="prerequisites"></a>先决条件

- 一个 Azure 帐户。 如果没有 Azure 帐户，则创建[免费的 Azure 帐户](https://azure.microsoft.com/free/)。 
- 一个 Azure Cosmos DB 帐户。 有关介绍，请参阅[使用 .NET 和 Azure 门户生成 MongoDB API Web 应用](create-mongodb-dotnet.md)。

## <a id="QuickstartConnection"></a>使用快速启动方法获取 MongoDB 连接字符串
1. 在 Internet 浏览器中，登录 [Azure 门户](https://portal.azure.com)。
2. 在“Azure Cosmos DB”边栏选项卡中，选择适用于 MongoDB 帐户的 API。 
3. 在“帐户”边栏选项卡左侧窗格中，单击“快速启动”。 
4. 选择平台 (.NET、Node.js、MongoDB Shell、Java、Python)。 如果未列出驱动程序或工具，别担心，我们持续记录了更多连接代码片段。 请在下面留言，告知我们你想知道的内容。 若要了解如何创建自己的连接，请阅读[获取该帐户的连接字符串信息](#GetCustomConnection)。
5. 将代码片段复制粘贴到 MongoDB 应用。

    ![“快速启动”边栏选项卡](./media/connect-mongodb-account/QuickStartBlade.png)

## <a id="GetCustomConnection"></a>获取 MongoDB 连接字符串进行自定义
1. 在 Internet 浏览器中，登录 [Azure 门户](https://portal.azure.com)。
2. 在“Azure Cosmos DB”边栏选项卡中，选择适用于 MongoDB 帐户的 API。 
3. 在“帐户”边栏选项卡的左侧窗格中，单击“连接字符串”。 
4. 此时将打开“连接字符串”边栏选项卡。 其中显示了使用 MongoDB 驱动程序连接到帐户所需的所有信息，包括预先构造的连接字符串。

    ![“连接字符串”边栏选项卡](./media/connect-mongodb-account/ConnectionStringBlade.png)

## <a name="connection-string-requirements"></a>连接字符串要求
> [!Important]
> Azure Cosmos DB 具有严格的安全要求和标准。 Azure Cosmos DB 帐户需要通过 SSL 进行身份验证和安全通信。 
>
>

Azure Cosmos DB 支持标准 MongoDB 连接字符串 URI 格式，但要满足几个具体的要求：Azure Cosmos DB 帐户需要通过 SSL 进行身份验证和安全通信。 因此，连接字符串格式为：

    mongodb://username:password@host:port/[database]?ssl=true

此字符串的值可用于前面显示的“连接字符串”边栏选项卡：

* 用户名称（必需）：Azure Cosmos DB 帐户名称。
* 密码（必需）：Azure Cosmos DB 帐户密码。
* 主机（必需）：Azure Cosmos DB 帐户的 FQDN。
* 端口（必需）：10255。
* 数据库（可选）：连接使用的数据库。 如果未提供任何数据库，则默认数据库是“test”。
* ssl=true（必需）

以“连接字符串”边栏选项卡中显示的帐户为例。 有效的连接字符串为：

    mongodb://contoso123:0Fc3IolnL12312asdfawejunASDF@asdfYXX2t8a97kghVcUzcDv98hawelufhawefafnoQRGwNj2nMPL1Y9qsIr9Srdw==@contoso123.documents.azure.com:10255/mydatabase?ssl=true

## <a name="next-steps"></a>后续步骤
* 了解如何配合[使用 Studio 3T (MongoChef)](mongodb-mongochef.md) 与 Azure Cosmos DB API for MongoDB 帐户。
* 查看[示例](mongodb-samples.md)，了解 Azure Cosmos DB API for MongoDB。
