---
title: "什么是 DocumentDB 的 MongoDB 协议支持？ | Microsoft Docs"
description: "什么是 DocumentDB 的 MongoDB 协议支持？ 通过该协议支持，可以将 Azure DocumentDB（一种托管的基于云的服务）用作为 MongoDB 编写的应用的数据存储。"
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
ms.date: 01/16/2017
ms.author: anhoh
translationtype: Human Translation
ms.sourcegitcommit: 2de367287a4bee05694070dbeeb4d4a458d47f11
ms.openlocfilehash: 38d811a7a08a6f8a6dd07a29e9a49bb34810ee91


---
# <a name="what-is-documentdb-protocol-support-for-mongodb"></a>什么是 DocumentDB 的 MongoDB 协议支持？

现在可以将 DocumentDB 数据库用作为 MongoDB 编写的应用的数据存储。 这意味着，通过使用 MongoDB 数据库的现有[驱动程序](https://docs.mongodb.org/ecosystem/drivers/)，为 MongoDB 编写的应用程序现在可与 DocumentDB 进行通信，并可使用 DocumentDB 数据库而不是 MongoDB 数据库。 在许多情况下，只需更改连接字符串便可从使用 DocumentDB 切换到使用 MongoDB。 使用此功能，客户可以在 Azure 云中轻松生成和运行 MongoDB 数据库应用程序（利用 DocumentDB 的完全托管且可缩放的 NoSQL 数据库），并且可继续使用熟悉的技能和 MongoDB 工具。

## <a name="what-is-the-benefit-of-using-documentdb-protocol-support-for-mongodb"></a>使用 DocumentDB 的 MongoDB 协议支持有什么好处？
**无服务器管理** - DocumentDB 是完全托管的服务，这意味着无需自己管理任何基础结构或虚拟机。 DocumentDB 在 20 多个 [Azure 区域](https://azure.microsoft.com/regions/services/)可用。

**无限缩放** - 可以独立和弹性缩放吞吐量和存储。 可以添加容量，以便轻松地每秒为数百万个请求提供服务。

**企业级** - DocumentDB 支持多个本地副本，在面对本地和区域故障时提供 99.99％ 的可用性和数据保护。 DocumentDB 具有企业级[合规性认证](https://www.microsoft.com/trustcenter)和安全功能。 

**MongoDB 兼容性** - DocumentDB 的 MongoDB 协议支持旨在提供与 MongoDB 的兼容性。 可以将现有代码、应用程序、驱动程序和工具用于 DocumentDB。 

与 Scott Hanselman 和 DocumentDB 的主要工程经理 Kirill Gavrylyuk 一起，通过 Azure Friday 视频了解更多信息。

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/DocumentDB-Database-as-a-Service-for-MongoDB-Developers/player]
> 


## <a name="how-to-get-started"></a>如何入门？
在 [Azure 门户](https://portal.azure.com)中创建具有 MongoDB 协议支持的 DocumentDB 帐户，并将连接交换到新帐户。 

*这就是所有的操作！*

有关详细说明，请参阅[创建帐户](documentdb-create-mongodb-account.md)和[连接到帐户](documentdb-connect-mongodb-account.md)。

## <a name="next-steps"></a>后续步骤
* 若要创建 DocumentDB 帐户，请参阅[创建具有 MongoDB 协议支持的 DocumentDB 帐户](documentdb-create-mongodb-account.md)教程。
* 若要了解如何获取帐户连接字符串信息，请参阅[连接到具有 MongoDB 协议支持的 DocumentDB 帐户](documentdb-connect-mongodb-account.md)教程。
* 若要了解如何在 MongoChef 中创建 DocumentDB 数据库和 MongoDB 应用之间的连接，请参阅[通过具有 MongoDB 协议支持的 DocumentDB 帐户使用 MongoChef](documentdb-mongodb-mongochef.md) 教程。
* 浏览具有 MongoDB 协议支持的 DocumentDB [示例](documentdb-mongodb-samples.md)。




<!--HONumber=Jan17_HO3-->


