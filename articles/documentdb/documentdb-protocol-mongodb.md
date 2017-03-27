---
title: "什么是 DocumentDB：MongoDB 的 API？ | Microsoft 文档"
description: "了解 DocumentDB：MongoDB 的 API，以及如何在 Azure 云中轻松运行现有 MongoDB 应用程序"
keywords: "什么是 MongoDB"
services: documentdb
author: AndrewHoh
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 4afaf40d-c560-42e0-83b4-a64d94671f0a
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: f173fa709f2a7a21042752ba4b5ac936d01fe300
ms.lasthandoff: 03/15/2017


---
# <a name="what-is-documentdb-api-for-mongodb"></a>什么是 DocumentDB：MongoDB 的 API？

现在可以将 DocumentDB 数据库用作为 MongoDB 编写的应用的数据存储。 这意味着，通过使用 MongoDB 数据库的现有[驱动程序](https://docs.mongodb.org/ecosystem/drivers/)，为 MongoDB 编写的应用程序现在可与 DocumentDB 进行通信，并可使用 DocumentDB 数据库而不是 MongoDB 数据库。 在许多情况下，只需更改连接字符串便可从使用 DocumentDB 切换到使用 MongoDB。 使用此功能，客户可以在 Azure 云中轻松生成和运行 MongoDB 数据库应用程序（利用 DocumentDB 的完全托管且可缩放的 NoSQL 数据库），并且可继续使用熟悉的技能和 MongoDB 工具。

## <a name="what-is-the-benefit-of-using-documentdb-api-for-mongodb"></a>使用 DocumentDB：MongoDB 的 API 有什么好处？
**无服务器管理** - DocumentDB 是完全托管的服务，这意味着无需自己管理任何基础结构或虚拟机。 DocumentDB 在 30 多个 [Azure 区域](https://azure.microsoft.com/regions/services/)中可用。

**无限缩放** - 可以独立和弹性缩放吞吐量和存储。 可以添加容量，以便轻松地每秒为数百万个请求提供服务。

**企业级** - DocumentDB 支持多个本地副本，在面对本地和区域故障时提供 99.99％ 的可用性和数据保护。 DocumentDB 具有企业级[合规性认证](https://www.microsoft.com/trustcenter)和安全功能。 

**MongoDB 兼容性** - DocumentDB：MongoDB 的 API 旨在提供与 MongoDB 的兼容性。 可以将现有代码、应用程序、驱动程序和工具用于 DocumentDB。 

与 Scott Hanselman 和 DocumentDB 的主要工程经理 Kirill Gavrylyuk 一起，通过 Azure Friday 视频了解更多信息。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>如何入门？
在 [Azure 门户](https://portal.azure.com)中创建 DocumentDB：MongoDB 帐户的 API，并将连接交换到新帐户。 

*这就是所有的操作！*

有关详细说明，请参阅[创建帐户](documentdb-create-mongodb-account.md)和[连接到帐户](documentdb-connect-mongodb-account.md)。

## <a name="next-steps"></a>后续步骤

有关 DocumentDB：MongoDB 的 API 的信息已集成到整个 DocumentDB 文档中，但以下是几个帮助你入门的指南：
* 若要了解如何获取帐户连接字符串信息，请参阅[连接到 MongoDB 帐户](documentdb-connect-mongodb-account.md)教程。
* 若要了解如何在 MongoChef 中创建 DocumentDB 数据库和 MongoDB 应用之间的连接，请参阅[将 MongoChef 与 DocumentDB 配合使用](documentdb-mongodb-mongochef.md)教程。
* 按照[将数据迁移到具有 MongoDB 协议支持的 DocumentDB](documentdb-mongodb-migrate.md) 教程将数据导入到 MongoDB 的 API 数据库。
* 使用 [Node.js](documentdb-mongodb-samples.md) 生成第一个 MongoDB 的 API 应用。
* 使用 .[NET](documentdb-mongodb-application.md) 生成第一个 MongoDB 的 API Web 应用。
* 使用 [Robomongo](documentdb-mongodb-robomongo.md) 连接到 MongoDB 的 API 帐户。
* 了解你的操作将多少 RU 用于 [GetLastRequestStatistics 命令和 Azure 门户指标](documentdb-request-units.md#GetLastRequestStatistics)。
* 了解如何[配置全局分布的应用的读取首选项](documentdb-distribute-data-globally.md#ReadPreferencesAPIforMongoDB)。


