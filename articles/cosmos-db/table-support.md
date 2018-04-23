---
title: Azure Cosmos DB 中的 Azure 表存储支持 | Microsoft Docs
description: 了解 Azure Cosmos DB 表 API 和 Azure 存储表如何协同工作。
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 2560c2ee34a83ce86db043e17fb41192c31de398
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/16/2018
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 表 API 进行开发

当前，[Azure Cosmos DB 表 API](table-introduction.md) 具有四个可用于进行开发的 SDK： 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK。 此库不仅与公共 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，而且还可以使用表 API 连接到 Azure Cosmos DB 帐户。 
- [Python SDK](table-sdk-python.md)。 新的 Azure Cosmos DB Python SDK 是在 Python 中唯一支持 Azure 表存储的 SDK。 此 SDK 与 Azure 表存储和 Azure Cosmos DB 表 API 连接。
- [Java SDK](table-sdk-java.md)。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。
- [Node.js SDK](table-sdk-nodejs.md)。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

[常见问题解答：使用表 API 进行开发](faq.md#develop-with-the-table-api)一文中提供了有关使用表 API 的其他信息。

## <a name="developing-with-the-azure-table-storage"></a>使用 Azure 表存储进行开发

[Azure 表存储](table-storage-overview.md)中提供了许多可用的 SDK 和教程，可在 [Azure 表存储](table-storage-overview.md)部分查看。 这些文章作为 Azure 表存储 SDK 和 Azure Cosmos DB 表 API 之间的互操作性说明更新，现在可以查看具体内容。  





