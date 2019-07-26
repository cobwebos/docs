---
title: 诊断并解决使用 Cosmos DB 的 Azure Functions 触发器时的问题
description: 使用 Cosmos DB 的 Azure Functions 触发器时, 常见问题、解决方法和诊断步骤
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b90986e449df7e81f97f9ef86ce3cf69621c76d6
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/19/2019
ms.locfileid: "68335753"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>诊断并解决使用 Cosmos DB 的 Azure Functions 触发器时的问题

本文介绍使用[Cosmos DB 的 Azure Functions 触发器](change-feed-functions.md)时的常见问题、解决方法和诊断步骤。

## <a name="dependencies"></a>依赖项

Cosmos DB 的 Azure Functions 触发器和绑定依赖于基础 Azure Functions 运行时上的扩展包。 请始终保持这些包的更新状态，因为它们可能包含用于解决你所遇到的任何潜在问题的修复程序和新功能：

* 对于 Azure Functions V2，请参阅 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 对于 Azure Functions V1，请参阅 [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

除非明确指定，否则本文每当提到运行时，都始终会参考 Azure Functions V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>独立使用 Azure Cosmos DB SDK

扩展包的主要功能是为 Cosmos DB 的 Azure Functions 触发器和绑定提供支持。 它还包括 [Azure Cosmos DB.NET SDK](sql-api-sdk-dotnet-core.md)，用于帮助你以编程方式来与 Azure Cosmos DB 交互，而无需使用触发器和绑定。

若要使用 Azure Cosmos DB SDK，请务必不要将项目添加到另一个 NuGet 包引用。 而是**让 SDK 引用通过 Azure Functions 的扩展包进行解析**。 独立于触发器和绑定使用 Azure Cosmos DB SDK

此外，如果手动创建自己的 [Azure Cosmos DB SDK 客户端](./sql-api-sdk-dotnet-core.md)实例，应遵循以下模式：只提供一个[使用单一实例模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)的客户端实例。 此过程可避免操作中出现潜在的套接字问题。

## <a name="common-scenarios-and-workarounds"></a>常见方案和解决方法

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function 失败, 出现错误消息集合不存在

Azure 函数失败并出现错误消息“源集合 'collection-name' (在数据库 'database-name' 中)或租约集合 'collection2-name' (在数据库 'database2-name' 中)不存在。 在侦听器启动之前，这两个集合必须存在。 若要自动创建租约集合，请将 'CreateLeaseCollectionIfNotExists' 设置为 'true'”

这表示运行触发器所需的一个或两个 Azure Cosmos 容器不存在，或者无法由 Azure 函数访问。 **该错误本身告知了触发器正在根据配置查找的 Azure Cosmos 数据库和容器**。

1. 验证 `ConnectionStringSetting` 属性，以及它是否**引用了 Azure 函数应用中存在的设置**。 此属性中的值不应是连接字符串本身，而是配置设置的名称。
2. 验证 `databaseName` 和 `collectionName` 是否在 Azure Cosmos 帐户中存在。 如果使用自动值替换（使用 `%settingName%` 模式），请确保该设置的名称在 Azure 函数应用中存在。
3. 如果未指定 `LeaseCollectionName/leaseCollectionName`，则默认值为“leases”。 验证此类容器是否存在。 （可选）可将触发器中的 `CreateLeaseCollectionIfNotExists` 属性设置为 `true`，以自动创建该容器。
4. 验证 [Azure Cosmos 帐户的防火墙配置](how-to-configure-firewall.md)，以查看它是否未阻止 Azure 函数。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函数无法启动并出现错误“共享吞吐量集合应有分区键”

旧版 Azure Cosmos DB 扩展不支持使用在[共享吞吐量数据库](./set-throughput.md#set-throughput-on-a-database)中创建的租约容器。 若要解决此问题，请更新 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 扩展以获取最新版本。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函数无法启动并出现错误“租约集合(如果已分区)必须有与 ID 相同的分区键”。

此错误表示当前租约容器已分区，但分区键路径不是 `/id`。 若要解决此问题，需要使用 `/id` 作为分区键来重新创建租约容器。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>尝试运行触发器时， Azure Functions 日志中出现“值不能为 null。参数名称: o”

如果使用 Azure 门户，并在检查使用触发器的 Azure 函数时选择屏幕上的“运行”按钮，则会出现此问题。 触发器不需要选择“运行”即可启动，部署 Azure 函数时它会自动启动。 若要在 Azure 门户上检查 Azure 函数的日志流，只需转到受监视的容器并插入一些新项，然后自然就会看到触发器正在执行。

### <a name="my-changes-take-too-long-be-received"></a>接收更改花费了过长的时间

这种情形可能是多种原因造成的，应检查所有这些原因：

1. Azure 函数是否部署在 Azure Cosmos 帐户所在的同一区域？ 为了获得最佳的网络延迟，应将 Azure 函数和 Azure Cosmos 帐户并置在同一个 Azure 区域。
2. Azure Cosmos 容器中发生的更改是持续性的还是偶发性的？
如果是后者，原因可能是存储更改与 Azure 函数拾取更改的时间有所延迟。 这是因为，在内部，当触发器检查 Azure Cosmos 容器中的更改但未找到任何等待读取的更改时，它将休眠一定的时间（可配置，默认为 5 秒），然后检查新的更改（以避免 RU 消耗量偏高）。 可以通过触发器的[配置](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration)中的 `FeedPollDelay/feedPollDelay` 设置来配置此休眠时间（该值预期以毫秒为单位）。
3. Azure Cosmos 容器可能受到[速率限制](./request-units.md)。
4. 可以使用触发器中的 `PreferredLocations` 属性来指定 Azure 区域的逗号分隔列表，以定义自定义的首选连接顺序。

### <a name="some-changes-are-missing-in-my-trigger"></a>触发器中缺少某些更改

如果你发现 Azure 函数未拾取 Azure Cosmos 容器中发生的某些更改，则需要执行一个初始调查步骤。

当 Azure 函数收到更改时，它通常会处理这些更改，并可能会选择性地将结果发送到另一个目标。 调查丢失更改的问题时，请确保度量在引入时间点（启动 Azure 函数时）收到的更改，而不要度量目标上的更改。

如果目标中缺少某些更改，可能意味着在收到更改后执行 Azure 函数期间发生了某种错误。

在这种情况下，最佳措施是在代码中以及在可能正在处理更改的循环中添加 `try/catch blocks`，以检测特定的项子集中出现的任何失败，并相应地对其进行处理（将这些项发送到另一个存储以做进一步的分析或重试）。 

> [!NOTE]
> 默认情况下, 如果在代码执行过程中出现未经处理的异常, 则 Cosmos DB 的 Azure Functions 触发器将不会重试一批更改。 这意味着，更改未抵达目标的原因是无法处理它们。

如果你发现触发器根本未收到某些更改，则最常见的情形是有**另一个 Azure 函数正在运行**。 该函数可能是部署在 Azure 中的另一个 Azure 函数，或者是在开发人员计算机本地运行的、采用**完全相同配置**（相同的受监视容器和租约容器）的 Azure 函数，并且此 Azure 函数正在窃取你的 Azure 函数预期要处理的更改子集。

此外，如果你知道正在运行多少个 Azure 函数应用实例，则也可以验证这种情况。 如果检查租约容器并统计其中包含的租约项数，这些项中的非重复 `Owner` 属性值应等于函数应用的实例数。 如果所有者数目超过已知的 Azure 函数应用实例数，则表示这些多出的所有者正在“窃取”更改。

解决此问题的简单方法之一是将采用新值/不同值的 `LeaseCollectionPrefix/leaseCollectionPrefix` 应用到你的函数，或使用新的租约容器进行测试。

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>仅可通过 IReadOnlyList\<文档 > 或 JArray 进行绑定

如果 Azure Functions 项目 (或任何引用的项目) 包含对 Azure Cosmos DB SDK 的手动 NuGet 引用, 而该版本与[Azure Functions Cosmos DB 扩展](./troubleshoot-changefeed-functions.md#dependencies)提供的版本不同, 则会发生此错误。

若要解决此问题, 请删除已添加的手动 NuGet 引用, 并让 Azure Cosmos DB SDK 引用通过 Azure Functions Cosmos DB 扩展包进行解析。

## <a name="next-steps"></a>后续步骤

* [为 Azure Functions 启用监视](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 故障排除](./troubleshoot-dot-net-sdk.md)
