---
title: "Azure Cosmos DB 中的 Azure 表存储支持 | Microsoft Docs"
description: "了解 Azure Cosmos DB 表 API 和 Azure 存储表如何协同工作。"
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 378f00f2-cfd9-4f6b-a9b1-d1e4c70799fd
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/03/2017
ms.author: mimig
ms.openlocfilehash: 1513fc53501f1cfec93134841fbef9a8552dd43c
ms.sourcegitcommit: 38c9176c0c967dd641d3a87d1f9ae53636cf8260
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/06/2017
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。 

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 表 API 进行开发

现在，[Azure Cosmos DB 表 API](table-introduction.md) 有一个 .NET SDK 可用 - [Microsoft Azure 存储高级表（预览版）](https://aka.ms/premiumtablenuget)。 此库与公共 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，但还具有使用表 API（预览版）连接到 Azure Cosmos DB 帐户的功能。 有关 Azure Cosmos DB 表 API 的快速入门和使用教程，请参阅以下文章：
- 快速入门：[Azure Cosmos DB：使用表 API 生成 .NET 应用程序](create-table-dotnet.md)
- 教程：[Azure Cosmos DB：在 .NET 中使用表 API 进行开发](tutorial-develop-table-dotnet.md)

[常见问题解答：使用表 API 进行开发](faq.md#develop-with-the-table-api-preview)一文中提供了有关使用表 API 的其他信息。

## <a name="developing-with-the-azure-table-storage"></a>使用 Azure 表存储进行开发

[Azure 表存储](table-storage-overview.md)中提供了许多可用的 SDK 和教程，可在 [Azure 表存储](table-storage-overview.md)部分查看。 这些文章作为 Azure 表存储 SDK 和 Azure Cosmos DB 表 API 之间的互操作性说明更新，现在可以查看具体内容。  





