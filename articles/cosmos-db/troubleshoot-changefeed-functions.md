---
title: 排查使用适用于 Cosmos DB 的 Azure Functions 触发器时出现的问题
description: 使用适用于 Cosmos DB 的 Azure Functions 触发器时出现的常见问题及其解决方法和诊断步骤
author: ealsur
ms.service: cosmos-db
ms.date: 03/13/2020
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 7bf7d418e3f2680b32f61e42cffc76c921068508
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365502"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>诊断和排查使用适用于 Cosmos DB 的 Azure Functions 触发器时出现的问题

本文介绍在使用 Cosmos DB 的[Azure 函数触发器](change-feed-functions.md)时的常见问题、解决方法和诊断步骤。

## <a name="dependencies"></a>依赖项

适用于 Cosmos DB 的 Azure Functions 触发器和绑定依赖于基于基础 Azure Functions 运行时的扩展包。 请始终保持这些包的更新状态，因为它们可能包含用于解决你所遇到的任何潜在问题的修复程序和新功能：

* 对于 Azure Functions V2，请参阅 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 对于 Azure Functions V1，请参阅 [Microsoft.Azure.WebJobs.Extensions.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)。

除非明确指定，否则本文每当提到运行时，都始终会参考 Azure Functions V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>单独使用 Azure Cosmos DB SDK

该扩展包的关键功能是为适用于 Cosmos DB 的 Azure Functions 触发器和绑定提供支持。 它还包括 [Azure Cosmos DB.NET SDK](sql-api-sdk-dotnet-core.md)，用于帮助你以编程方式来与 Azure Cosmos DB 交互，而无需使用触发器和绑定。

若要使用 Azure Cosmos DB SDK，请务必不要将项目添加到另一个 NuGet 包引用。 而是**让 SDK 引用通过 Azure Functions 的扩展包进行解析**。 独立于触发器和绑定使用 Azure Cosmos DB SDK

此外，如果手动创建自己的 [Azure Cosmos DB SDK 客户端](./sql-api-sdk-dotnet-core.md)实例，应遵循以下模式：只提供一个[使用单一实例模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)的客户端实例。 此过程可避免操作中出现潜在的套接字问题。

## <a name="common-scenarios-and-workarounds"></a>常见情景和解决方法

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure 函数失败，错误消息指出集合不存在

Azure 函数失败并出现错误消息“源集合 'collection-name' (在数据库 'database-name' 中)或租约集合 'collection2-name' (在数据库 'database2-name' 中)不存在。 在侦听器启动之前，这两个集合必须存在。 若要自动创建租约集合，请将 'CreateLeaseCollectionIfNotExists' 设置为 'true'”

这表示运行触发器所需的一个或两个 Azure Cosmos 容器不存在，或者无法由 Azure 函数访问。 **错误本身将告诉您哪个 Azure Cosmos 数据库和容器是**基于您的配置查找的触发器。

1. 验证 `ConnectionStringSetting` 属性，以及它是否**引用了 Azure 函数应用中存在的设置**。 此属性中的值不应是连接字符串本身，而是配置设置的名称。
2. 验证 `databaseName` 和 `collectionName` 是否在 Azure Cosmos 帐户中存在。 如果使用自动值替换（使用 `%settingName%` 模式），请确保该设置的名称在 Azure 函数应用中存在。
3. 如果未指定 `LeaseCollectionName/leaseCollectionName`，则默认值为“leases”。 验证此类容器是否存在。 （可选）可将触发器中的 `CreateLeaseCollectionIfNotExists` 属性设置为 `true`，以自动创建该容器。
4. 验证 [Azure Cosmos 帐户的防火墙配置](how-to-configure-firewall.md)，以查看它是否未阻止 Azure 函数。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函数无法启动并出现错误“共享吞吐量集合应有分区键”

旧版 Azure Cosmos DB 扩展不支持使用在[共享吞吐量数据库](./set-throughput.md#set-throughput-on-a-database)中创建的租约容器。 若要解决此问题，请更新 [Microsoft.Azure.WebJobs.Extensions.CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB) 扩展以获取最新版本。

### <a name="azure-function-fails-to-start-with-partitionkey-must-be-supplied-for-this-operation"></a>Azure 函数无法以"必须为此操作提供分区密钥"开头。

此错误意味着您当前使用的是具有旧[扩展依赖项](#dependencies)的分区租约集合。 升级到最新版本。 如果当前在 Azure 函数 V1 上运行，则需要升级到 Azure 函数 V2。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函数无法启动并出现错误“租约集合(如果已分区)必须有与 ID 相同的分区键”。

此错误表示当前租约容器已分区，但分区键路径不是 `/id`。 若要解决此问题，需要使用 `/id` 作为分区键来重新创建租约容器。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>尝试运行触发器时， Azure Functions 日志中出现“值不能为 null。参数名称: o”

如果使用 Azure 门户，并在检查使用触发器的 Azure 函数时选择屏幕上的“运行”按钮，则会出现此问题。**** 触发器不需要选择“运行”即可启动，部署 Azure 函数时它会自动启动。 若要在 Azure 门户上检查 Azure 函数的日志流，只需转到受监视的容器并插入一些新项，然后自然就会看到触发器正在执行。

### <a name="my-changes-take-too-long-to-be-received"></a>接收更改花费了太长的时间

这种情形可能是多种原因造成的，应检查所有这些原因：

1. Azure 函数是否部署在 Azure Cosmos 帐户所在的同一区域？ 为获得最佳的网络延迟，Azure 函数和 Azure Cosmos 帐户应共置在同一个 Azure 区域。
2. Azure Cosmos 容器中发生的更改是连续性的还是偶发性的？
如果是后者，则原因可能是存储更改的时间与 Azure 函数拾取更改的时间有一段延迟。 这是因为，在内部，当触发器检查 Azure Cosmos 容器中的更改但未找到等待读取的更改时，它会休眠配置的一段时间（默认为 5 秒），然后检查新的更改（以避免 RU 消耗量过高）。 可以通过触发器[配置](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中的 `FeedPollDelay/feedPollDelay` 设置来配置此休眠时间（该值预期以毫秒为单位）。
3. Azure Cosmos 容器可能[受速率限制](./request-units.md)。
4. 可以使用触发器中的 `PreferredLocations` 属性来指定 Azure 区域的逗号分隔列表，以定义自定义的首选连接顺序。

### <a name="some-changes-are-repeated-in-my-trigger"></a>一些更改在我的触发器中重复

"更改"的概念是文档的操作。 收到同一文档事件的最常见方案是：
* 帐户使用的是最终一致性。 在"最终一致性"级别使用更改源时，后续更改源读取操作之间可能存在重复事件（一个读取操作的最后一个事件显示为下一个读取操作中的第一个事件）。
* 正在更新文档。 更改源可以包含同一文档的多个操作，如果该文档正在接收更新，它可以拾取多个事件（每次更新一个）。 区分同一文档的不同操作的一种简单方法是跟踪`_lsn`[每个更改的属性](change-feed.md#change-feed-and-_etag-_lsn-or-_ts)。 如果它们不匹配，则这些更改与同一文档不同。
* 如果仅通过`id`标识文档，请记住文档的唯一标识符是`id`及其分区键（可以有两个具有相同`id`但不同的分区键的文档）。

### <a name="some-changes-are-missing-in-my-trigger"></a>触发器中缺少某些更改

如果你发现 Azure 函数未拾取 Azure Cosmos 容器中发生的某些更改，则需要执行一个初始调查步骤。

当 Azure 函数收到更改时，它通常会处理这些更改，并可能会选择性地将结果发送到另一个目标。 调查丢失更改的问题时，请确保度量在引入时间点（启动 Azure 函数时）收到的更改，而不要度量目标上的更改。****

如果目标中缺少某些更改，可能意味着在收到更改后执行 Azure 函数期间发生了某种错误。

在这种情况下，最佳措施是在代码中以及在可能正在处理更改的循环中添加 `try/catch` 块，以检测特定的项子集中出现的任何失败，并相应地对其进行处理（将这些项发送到另一个存储以做进一步的分析或重试）。 

> [!NOTE]
> 默认情况下，如果在代码执行期间发生未经处理的异常，则适用于 Cosmos DB 的 Azure Functions 触发器不会重试一批更改。 这意味着，更改未抵达目标的原因是无法处理它们。

如果发现触发器根本不收到某些更改，最常见的方案是正在**运行另一个 Azure 函数**。 该函数可能是部署在 Azure 中的另一个 Azure 函数，或者是在开发人员计算机本地运行的、采用**完全相同配置**（相同的受监视容器和租约容器）的 Azure 函数，并且此 Azure 函数正在窃取你的 Azure 函数预期要处理的更改子集。

此外，如果你知道正在运行多少个 Azure 函数应用实例，则也可以验证这种情况。 如果检查租约容器并统计其中包含的租约项数，这些项中的非重复 `Owner` 属性值应等于函数应用的实例数。 如果所有者数多于已知的 Azure 函数应用实例，则意味着这些额外的所有者是"窃取"更改的。

解决这种情况的一个简单方法是使用新的/不同值`LeaseCollectionPrefix/leaseCollectionPrefix`对函数应用，或者使用新的租约容器进行测试。

### <a name="need-to-restart-and-reprocess-all-the-items-in-my-container-from-the-beginning"></a>需要从一开始就重新启动和重新处理容器中的所有项目 
要从一开始就重新处理容器中的所有项：
1. 如果 Azure 函数当前正在运行，请将其停止。 
1. 删除租约集合中的文档（或删除租约集合并重新创建一个空集合）
1. 将函数中的 [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger 属性设置为 true。 
1. 重启 Azure 函数。 现在，它会从头开始读取并处理所有更改。 

如果将 [StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) 设置为 true，则会告知 Azure 函数要从头开始读取集合历史记录的更改，而不是从当前时间开始读取。 仅当没有已创建的租约（即租约集合中的文档）时，这才有效。 如果已创建租约，将此属性设置为 true 将不起作用；在这种情况下，当某个函数停止并重启时，它将从租约集合中定义的最后一个检查点开始读取。 要从一开始就重新处理，请按照上述步骤 1-4 进行操作。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>只能通过 IReadOnlyList\<Document> 或 JArray 进行绑定

如果 Azure Functions 项目（或任何引用的项目）包含对 Azure Cosmos DB SDK 的手动 NuGet 引用，而该版本与 [Azure Functions Cosmos DB 扩展](./troubleshoot-changefeed-functions.md#dependencies)提供的版本不同，则会发生此错误。

要解决此问题，请删除添加的手动 NuGet 引用，让 Azure Cosmos DB SDK 引用通过 Azure 函数 Cosmos DB 扩展包解析。

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>更改 Azure 函数在检测更改时的轮询间隔

如此前针对[接收更改花费了太长的时间](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)解释的那样，Azure 函数会休眠一定的时间（可配置，默认为 5 秒），然后检查新的更改（以避免 RU 消耗量偏高）。 可以通过触发器[配置](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)中的 `FeedPollDelay/feedPollDelay` 设置来配置此休眠时间（该值预期以毫秒为单位）。

## <a name="next-steps"></a>后续步骤

* [为 Azure 函数启用监视](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 故障排除](./troubleshoot-dot-net-sdk.md)
