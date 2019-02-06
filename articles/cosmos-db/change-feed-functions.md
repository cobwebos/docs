---
title: 如何将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
description: 将 Azure Cosmos DB 更改源与 Azure Functions 配合使用
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 93cd93b40c142d504c52f08f9005d082fb5a2a20
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/31/2019
ms.locfileid: "55469475"
---
# <a name="trigger-azure-functions-from-azure-cosmos-db"></a>从 Azure Cosmos DB 触发 Azure Functions

如果使用 Azure Functions，连接到更改源的最简单方法是将一个 [Azure Cosmos DB 触发器](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)添加到 Azure Functions 应用。 在 Azure Functions 应用中创建 Cosmos DB 触发器时，请选择要连接到的 Cosmos DB 容器，以及每当更改该容器中的某项设置时要触发的函数。

可在 Azure Functions 门户、Azure Cosmos DB 门户中或以编程方式创建触发器。 有关详细信息，请参阅[使用 Azure Cosmos DB 和 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)。

## <a name="frequently-asked-questions"></a>常见问题

### <a name="how-can-i-configure-azure-functions-to-read-from-a-particular-region"></a>如何将 Azure Functions 配置为从特定的区域读取数据？

可以在使用 Azure Cosmos DB 触发器指定区域列表时定义 [PreferredLocations](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations)。 这与自定义 ConnectionPolicy，使触发器从首选区域读取数据的效果相同。 最好是从部署 Azure Functions 的最靠近区域读取数据。

### <a name="what-is-the-default-size-of-batches-in-azure-functions"></a>Azure Functions 中的默认批大小是什么？

每次调用 Azure Functions 时，默认批大小为 100 个项。 但是，可在 function.json 文件中配置此数字。 下面是完整的[配置选项列表](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)。 如果在本地进行开发，请更新 local.settings.json 文件中的应用程序设置。

### <a name="i-am-monitoring-a-container-and-reading-its-change-feed-however-i-dont-get-all-the-inserted-documents-some-items-are-missing"></a>我正在监视某个容器并在读取其更改源，但未获取所有插入的文档，某些项已缺失，为何如此？

请确保没有其他 Azure 函数正在读取具有相同租约容器的相同容器。 缺失的文档已由其他 Azure 函数处理，这些函数使用了相同的租约。

因此，若要创建多个 Azure 函数来读取相同的更改源，这些函数必须使用不同的租约容器，或使用“leasePrefix”配置来共享相同的容器。 但是，如果使用更改源处理器库，则可以启动 Azure 函数的多个实例，SDK 会自动在不同的实例之间分割文档。

### <a name="azure-cosmos-item-is-updated-every-second-and-i-dont-get-all-the-changes-in-azure-functions-listening-to-change-feed"></a>Azure Cosmos 项每隔一秒更新一次，但我无法在侦听更改源的 Azure Functions 中获取所有更改，为何如此？

Azure Functions 会持续在更改源中轮询更改，最大默认延迟为 5 秒。 如果不存在等待读取的挂起更改，或者在应用触发器后出现了挂起的更改，则函数会立即读取这些更改。 但是，如果不存在挂起的更改，则函数将等待 5 秒，然后轮询其他更改。

如果文档在相同的间隔内收到了多个更改，使触发器轮询新的更改，则你可能会收到文档的最新版本，而不是中间版本。

若要以小于 5 秒的间隔（例如每秒）轮询更改源，可以配置轮询时间“feedPollDelay”，具体请参阅[完整的配置](https://docs.microsoft.com/dotnet/api/microsoft.azure.documents.client.connectionpolicy.preferredlocations?view=azure-dotnet#Microsoft_Azure_Documents_Client_ConnectionPolicy_PreferredLocations)。 该时间以毫秒定义，默认值为 5000。 以小于 1 秒的间隔轮询是可行的，但不建议这样做，因为这会消耗更多的 CPU 和内存。

### <a name="can-multiple-azure-functions-read-one-containers-change-feed"></a>多个 Azure 函数是否可以读取一个容器的更改源？

是的。 多个 Azure 函数可以读取同一容器的更改源。 但是，需要为 Azure 函数定义不同的“leaseCollectionPrefix”。

### <a name="if-i-am-processing-change-feed-by-using-azure-functions-in-a-batch-of-10-documents-and-i-get-an-error-at-seventh-document-in-that-case-the-last-three-documents-are-not-processed-how-can-i-start-processing-from-the-failed-document-ie-seventh-document-in-my-next-feed"></a>如果我使用 Azure Functions 以包含 10 个文档的批的形式处理更改源，并在处理第 7 个文档时收到错误， 在这种情况下，最后 3 个文档不会得到处理。如何在下一个源中从失败的文档（即第 7 个文档）开始处理？

若要处理错误，建议的模式是使用 try-catch 块包装代码。如果迭代文档列表，请将每个迭代包装在其自身的 try-catch 块中。 捕获错误，将该文档放入队列（死信），然后定义逻辑来处理已生成错误的文档。 如果批中的文档数有 200 个，并且只有一个文档失败，则使用此方法就无需丢弃整个批。

如果出错，则不应将检查点回退到开始位置，否则可以从更改源获取这些文档。 请记住，更改源保留文档的最后一个最终快照，因此，可能会丢失文档中的前一个快照。 更改源只保留文档的最后一个版本，在不同的版本之间，其他进程可能会更改文档。

在不断修复代码的过程中，你很快就会发现，死信队列中没有任何文档。 Azure Functions 由更改源系统自动调用，检查点由 Azure 函数在内部维护。 若要回滚检查点并控制其各个方面，应考虑使用更改源处理器 SDK。

### <a name="are-there-any-extra-costs-for-using-the-azure-cosmos-db-trigger"></a>使用 Azure Cosmos DB 触发器是否会产生额外的费用？

Azure Cosmos DB 触发器在内部利用更改源处理器库。 因此，它需要使用额外的集合（称为租约集合）来维护状态和部分检查点。 需要进行这种状态管理才能实现动态缩放和持续性，以便可以停止 Azure Functions 并在以后继续处理。 有关详细信息，请参阅[如何使用更改源处理器库](change-feed-processor.md)。

## <a name="next-steps"></a>后续步骤

接下来，请通过以下文章继续详细了解更改源：

* [更改源概述](change-feed.md)
* [读取更改源的方式](read-change-feed.md)
* [使用更改源处理器库](change-feed-processor.md)
* [如何使用更改源处理器库](change-feed-processor.md)
* [使用 Azure Cosmos DB 和 Azure Functions 进行无服务器数据库计算](serverless-computing-database.md)
