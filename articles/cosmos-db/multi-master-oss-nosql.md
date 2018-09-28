---
title: 将 Azure Cosmos DB 多主数据库与开放源代码 NoSQL 数据库结合使用
description: ''
services: cosmos-db
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: mjbrown
ms.reviewer: sngun
ms.openlocfilehash: ea3a16cf6b5aa4e46ad401e59aacb4d936a7429a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46963894"
---
# <a name="using-azure-cosmos-db-multi-master-with-open-source-nosql-databases"></a>将 Azure Cosmos DB 多主数据库与开放源代码 NoSQL 数据库结合使用

Azure Cosmos DB 多主数据库支持是本机服务器端实现，可供 Cosmos DB 支持的所有开放源代码 NoSQL 产品/服务使用。 它也可供 Cosmos DB 支持的所有 SDK 访问。

Azure Cosmos DB 是世界上首项为这些开放源代码 NoSQL 数据库提供多主数据库支持的服务。

|模型  |支持  |
|---------|---------|
|MongoDB  | 主动-主动  |
|图形  | 主动-主动 |
|Cassandra  | 主动-主动   |

## <a name="use-mongodb-clients-with-multi-master"></a>将 MongoDB 客户端与多主数据库结合使用

为 MongoDB API 帐户启用多主数据库功能就像为其他 Azure Cosmos DB API 启用一样。 为 MongoDB API 帐户启用多主数据库后，客户端应用程序的每个实例都可以选择它的首选读写区域。 从 MongoDB 驱动程序的角度来看，客户端将首选区域视为主要副本集。 这样一来，分布式数据库的每个区域都可以充当主要副本集。 借助 Azure Cosmos DB 多主数据库，可显著减少全局分布式 MongoDB 应用程序的写入延迟。 

### <a name="set-the-primary-region"></a>设置主要区域

MongoDB 客户端的每个实例都可以选择主要区域，具体方法是将 `@<preferred_primary_region_name>` 追加到 MongoDB 客户端驱动程序接受的“应用程序名称”字段。 大多数驱动程序接受在连接字符串中追加此内容，如：

`mongodb://fabrikam:KEY@fabrikam.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

连接后，可能会发生下列情况之一：

* 如果首选区域名称是作为写入区域提供，那么 Azure Cosmos DB 会将它选为主要区域。

* 如果首选区域名称未提供，那么 Azure Cosmos DB 会将默认区域选为主要区域。

* 如果首选区域名称未作为数据库帐户的写入区域提供，那么 Azure Cosmos DB 会将最近的可选择写入区域选为主要区域。

某些驱动程序（如 NodeJS 驱动程序）始终先向初始连接字符串中指定的主机分发写入。 对于此类驱动程序，为了确保写入能够定向到首选区域，应修改连接字符串中的 DNS 名称，使其除了包含应用程序名称之外，还包含区域名称。 指定的区域名称不得包含任何空格。 例如：

`mongodb://fabrikam:KEY@fabrikam-westus.documents.azure.com:10255/?ssl=true&replicaSet=globaldb&appname=@West US`

### <a name="conflict-resolution-mode"></a>冲突解决模式

Azure Cosmos DB MongoDB API 帐户的冲突解决模式始终是最后写入者获胜（以已接受写入的区域服务器时间戳为准）。

## <a name="next-steps"></a>后续步骤

本文介绍了 Azure Cosmos DB MongoDB API 帐户的多主数据库支持。 可供查看的后续资源如下：

* [如何为 Azure Cosmos DB 帐户启用多主数据库](enable-multi-master.md)

* [了解 Azure Cosmos DB 中的冲突解决](multi-master-conflict-resolution.md)
