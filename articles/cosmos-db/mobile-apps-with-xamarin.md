---
title: 教程：使用 Xamarin 和 Azure Cosmos DB 构建移动应用程序
description: 教程：介绍如何使用 Azure Cosmos DB 创建 Xamarin iOS、Android 或 Forms 应用程序的教程。 Azure Cosmos DB 是适用于移动应用的快速、全球规模的云数据库。
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: dotnet
ms.topic: tutorial
ms.date: 11/05/2019
ms.author: sngun
ms.openlocfilehash: 8fbd2065ceae1aadf1dc57c78adb21b7c2848297
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "80985552"
---
# <a name="tutorial-build-mobile-applications-with-xamarin-and-azure-cosmos-db"></a>教程：使用 Xamarin 和 Azure Cosmos DB 构建移动应用程序

> [!div class="op_single_selector"]
> * [.NET](sql-api-dotnet-application.md)
> * [Java](sql-api-java-application.md)
> * [Node.js](sql-api-nodejs-application.md)
> * [Python](sql-api-python-application.md)
> * [Xamarin](mobile-apps-with-xamarin.md)
> 

大多数移动应用需要在云中存储数据，而 Azure Cosmos DB 正是适用于移动应用的云数据库。 该产品提供移动开发人员所需的一切功能。 它是一个完全托管的数据库即服务，可按需缩放。 它能够以应用程序完全可见的方式存放全球各地用户的数据。 使用 [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)，可让 Xamarin 移动应用直接与 Azure Cosmos DB 交互，而无需任何中间层。

本文提供有关使用 Xamarin 和 Azure Cosmos DB 构建移动应用的教程。 可在 [GitHub 上的 Xamarin 和 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 中找到本教程的完整源代码，包括如何管理用户和权限。

## <a name="azure-cosmos-db-capabilities-for-mobile-apps"></a>移动应用的 Azure Cosmos DB 功能
Azure Cosmos DB 为移动应用开发人员提供以下重要功能：

![移动应用的 Azure Cosmos DB 功能](media/mobile-apps-with-xamarin/documentdb-for-mobile.png)

* 针对无架构数据的丰富查询。 Azure Cosmos DB 将数据以无架构 JSON 文档的形式存储在异构集合中。 它提供[丰富的快速查询](how-to-sql-query.md)，无需担心架构或索引问题。
* 快速吞吐量。 使用 Azure Cosmos DB 时，只需几毫秒就能读取和写入文档。 开发人员可以指定所需的吞吐量，Azure Cosmos DB 对所有单区域帐户和具有松散一致性的所有多区域帐户采用 99.99% 的可用性 SLA，对所有多区域数据库帐户采用 99.999% 的读取可用性。
* 无限规模。 Azure Cosmos 容器[随着应用的增长而增长](partition-data.md)。 一开始可以处理较小规模的数据和每秒几百个请求的吞吐量。 集合或数据库可以增长到 PB 量级的数据和每秒几亿个请求的任意大的吞吐量。
* 全球分布。 移动应用的用户特点是经常在全球不同的地点漫游。 Azure Cosmos DB 是[全球分布的数据库](distribute-data-globally.md)。 单击地图即可让用户访问数据。
* 内置丰富授权。 有了 Azure Cosmos DB，无需使用复杂的自定义授权代码，就能轻松实现常用的模式，例如，[每个用户的数据](https://github.com/kirillg/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems)，或者多个用户共享的数据。
* 地理空间查询。 许多移动应用现在可以提供区分地理环境的体验。 凭借对[地理空间类型](geospatial.md)的一流支持，Azure Cosmos DB 可轻松创建这些体验。
* 二进制附件。 应用数据通常包含二进制 Blob。 Azure Cosmos DB 原生支持附件，因此可以更轻松地将它用作应用数据的一站式存储库。

## <a name="azure-cosmos-db-and-xamarin-tutorial"></a>Azure Cosmos DB 和 Xamarin 教程
以下教程介绍如何使用 Xamarin 和 Azure Cosmos DB 构建移动应用程序。 可在 [GitHub 上的 Xamarin 和 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 中找到该教程的完整源代码。

### <a name="get-started"></a>入门
入门 Azure Cosmos DB 非常简单。 转到 Azure 门户，并创建新的 Azure Cosmos DB 帐户。 单击“快速入门”选项卡。  下载已连接到 Azure Cosmos DB 帐户的 Xamarin Forms 待办事项列表示例。 

![适用于移动应用的 Azure Cosmos DB 快速入门](media/mobile-apps-with-xamarin/cosmos-db-quickstart.png)

如果已有一个 Xamarin 应用，则可以添加 [Azure Cosmos DB NuGet 包](sql-api-sdk-dotnet-core.md)。 Azure Cosmos DB 支持 Xamarin.IOS、Xamarin.Android 和 Xamarin Forms 共享库。

### <a name="work-with-data"></a>处理数据
数据记录以无架构 JSON 文档的形式存储在 Azure Cosmos DB 的异构集合中。 可在同一个集合中存储具有不同结构的文档：

```cs
    var result = await client.CreateDocumentAsync(collectionLink, todoItem);
```

在 Xamarin 项目中，可以针对无架构数据使用语言集成的查询：

```cs
    var query = await client.CreateDocumentQuery<ToDoItem>(collectionLink)
                    .Where(todoItem => todoItem.Complete == false)
                    .AsDocumentQuery();

    Items = new List<TodoItem>();
    while (query.HasMoreResults) {
        Items.AddRange(await query.ExecuteNextAsync<TodoItem>());
    }
```
### <a name="add-users"></a>添加用户
与许多入门示例一样，下载的 Azure Cosmos DB 示例使用已在应用代码中硬编码的主密钥向服务进行身份验证。 除非在本地模拟器上运行应用，否则这种默认行为不是最佳做法。 如果未经授权的用户获取了主密钥，整个 Azure Cosmos DB 帐户中的所有数据可能会遭受损坏。 应用最好是只能访问已登录用户的记录。 Azure Cosmos DB 允许开发人员向应用程序授予对某个集合、按分区键分组的一组文档或某个特定文档的读取或读/写权限。 

遵循以下步骤将待办事项列表应用修改为多用户待办事项列表应用： 

  1. 使用 Facebook、Active Directory 或其他任何提供程序将登录名添加到应用。

  2. 创建使用 **/userId** 作为分区键的 Azure Cosmos DB UserItems 集合。 为集合指定分区键可让 Azure Cosmos DB 随着应用用户的增多而无限缩放，同时可持续提供快速查询。

  3. 添加 Azure Cosmos DB 资源令牌中转站。 这是一个简单的 Web API，可对用户进行身份验证，并向只有权访问其自己分区中的文档的已登录用户颁发短期生存的令牌。 在此示例中，资源令牌代理托管在应用服务中。

  4. 将应用修改为使用 Facebook 对资源令牌代理进行身份验证，请求已登录的 Facebook 用户的资源令牌。 然后，可在 UserItems 集合中访问其数据。  

可在 [GitHub 上的资源令牌代理](https://github.com/kirillg/azure-documentdb-dotnet/tree/master/samples/xamarin/UserItems)中找到此模式的完整代码示例。 下图演示了该解决方案：

![Azure Cosmos DB 用户和权限中转站](media/mobile-apps-with-xamarin/documentdb-resource-token-broker.png)

如果希望两个用户能够访问同一个“待办事项列表”，可将其他权限添加到资源令牌代理中的访问令牌。

### <a name="scale-on-demand"></a>按需缩放
Azure Cosmos DB 是托管型数据库即服务。 随着用户群体的扩大，不用担心是否要预配 VM 或增加核心， 只需告诉 Azure Cosmos DB 应用所需的每秒操作次数（吞吐量）是多少。 可以通过“缩放”选项卡，使用一个名为每秒请求单位数 (RU) 的吞吐量度量值来指定吞吐量。  例如，针对 1-KB 文档执行一次读取操作需要 1 个 RU。 还可以针对**吞吐量**指标添加警报，用于监视流量的增长以及在警报触发时以编程方式更改吞吐量。

![Azure Cosmos DB 按需缩放吞吐量](media/mobile-apps-with-xamarin/cosmos-db-xamarin-scale.png)

### <a name="go-planet-scale"></a>全球规模
在应用受到广泛欢迎后，可以吸收全球各地的用户。 或许需要针对一些不可预见的事件做好准备。 转到 Azure 门户，并打开 Azure Cosmos DB 帐户。 单击地图即可将数据持续复制到全球任意数目的区域。 此功能可向任意位置的用户提供数据。 还可以添加故障转移策略，针对意外状况做好准备。

![Azure Cosmos DB 跨地理区域进行缩放](media/mobile-apps-with-xamarin/cosmos-db-xamarin-replicate.png)

祝贺。 现已完成本解决方案，并使用 Xamarin 和 Azure Cosmos DB 构建了一个移动应用。 遵循类似的步骤使用 Azure Cosmos DB JavaScript SDK 构建 Cordova 应用，或者使用 Azure Cosmos DB REST API 构建本机 iOS/Android 应用。

## <a name="next-steps"></a>后续步骤
* 查看 [GitHub 上的 Xamarin 和 Azure Cosmos DB](https://github.com/Azure/azure-documentdb-dotnet/tree/master/samples/xamarin) 的源代码。
* 下载 [Azure Cosmos DB .NET Core SDK](sql-api-sdk-dotnet-core.md)。
* 查找 [.NET 应用程序](sql-api-dotnet-samples.md)的更多代码示例。
* 了解 [Azure Cosmos DB 的丰富查询功能](how-to-sql-query.md)。
* 了解 [Azure Cosmos DB 中的地理空间支持](geospatial.md)。



