---
title: Azure Cosmos DB 中的 Azure 表存储支持 | Microsoft Docs
description: 了解 Azure Cosmos DB 表 API 和 Azure 存储表如何协同工作。
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: 1ebf244aeb00b1eb87b846a5bb1db6bd4f954e26
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798162"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>使用 Azure 表存储 API 和 Azure Cosmos DB 进行开发

Azure Cosmos DB 表 API 和 Azure 表存储通过其 SDK 共享相同的表数据模型，并公开相同的创建、删除、更新和查询操作。 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>使用 Azure Cosmos DB 表 API 进行开发

当前，[Azure Cosmos DB 表 API](table-introduction.md) 具有四个可用于进行开发的 SDK： 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK。 此库不仅与公共 [Microsoft Azure 存储 SDK](https://www.nuget.org/packages/WindowsAzure.Storage) 具有相同的类和方法签名，而且还可以使用表 API 连接到 Azure Cosmos DB 帐户。 
- [Python SDK](table-sdk-python.md)。 新的 Azure Cosmos DB Python SDK 是在 Python 中唯一支持 Azure 表存储的 SDK。 此 SDK 与 Azure 表存储和 Azure Cosmos DB 表 API 连接。
- [Java SDK](table-sdk-java.md)。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。
- [Node.js SDK](table-sdk-nodejs.md)。 此 Azure 存储 SDK 能够使用表 API 连接到 Azure Cosmos DB 帐户。

[常见问题解答：使用表 API 进行开发](faq.md#develop-with-the-table-api)一文中提供了有关使用表 API 的其他信息。

## <a name="developing-with-azure-table-storage"></a>使用 Azure 表存储进行开发

Azure 表存储的以下 SDK 可用于开发：

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/)。 该库使你能够使用存储表服务。
- [Python SDK](table-sdk-python.md)。 用于 Python 的 Azure Cosmos DB 表 SDK 也支持存储表服务。
- [用于 Java 的 Azure 存储 SDK](https://github.com/azure/azure-storage-java)。 此 Azure 存储 SDK 提供了一个 Java 客户端库来使用 Azure 表存储。
- [Node.js SDK](table-sdk-nodejs.md)。 此 SDK 提供了一个 Node.js 包和一个浏览器兼容的 JavaScript 客户端库来使用存储表服务。
- [AzureRmStorageTable PowerShell 模块](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7)。 此 PowerShell 模块包含 cmdlet 以使用存储表。
- [适用于 C++ 的 Azure 存储客户端库](https://github.com/Azure/azure-storage-cpp/)。 此库使你能够针对 Azure 存储构建应用程序。
- [适用于 Ruby 的 Azure 存储表客户端库](https://github.com/azure/azure-storage-ruby/tree/master/table)。 此项目提供了一个 Ruby 包，使用该包可轻松访问 Azure 存储表服务。
- [Azure 存储表 PHP 客户端库](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table)。 此项目提供了一个 PHP 客户端库，使用该库可轻松访问 Azure 存储表服务。


   





