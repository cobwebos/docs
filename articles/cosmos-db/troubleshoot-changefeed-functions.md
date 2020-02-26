---
title: 排查使用 Cosmos DB 的 Azure Functions 触发器时遇到的问题
description: 使用 Cosmos DB 的 Azure Functions 触发器时，常见问题、解决方法和诊断步骤
author: ealsur
ms.service: cosmos-db
ms.date: 07/17/2019
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f382406d164aa7378631753c2cfc85bc69003a4f
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77605085"
---
# <a name="diagnose-and-troubleshoot-issues-when-using-azure-functions-trigger-for-cosmos-db"></a>诊断并解决使用 Cosmos DB 的 Azure Functions 触发器时的问题

本文介绍使用[Cosmos DB 的 Azure Functions 触发器](change-feed-functions.md)时的常见问题、解决方法和诊断步骤。

## <a name="dependencies"></a>依赖项

Cosmos DB 的 Azure Functions 触发器和绑定依赖于基础 Azure Functions 运行时上的扩展包。 始终保持这些包的更新，因为它们可能包含修复和新功能，这些功能可能会解决你可能遇到的任何潜在问题：

* 有关 Azure Functions V2，请参阅[CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)。
* 有关 Azure Functions V1，请参阅 " [Microsoft.](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.DocumentDB)

除非显式指定，否则，每当提到运行时，本文都将始终引用 Azure Functions V2。

## <a name="consume-the-azure-cosmos-db-sdk-independently"></a>独立使用 Azure Cosmos DB SDK

扩展包的主要功能是为 Cosmos DB 的 Azure Functions 触发器和绑定提供支持。 它还包括[Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet-core.md)，如果你想要以编程方式与 Azure Cosmos DB 交互，而不使用触发器和绑定，则会很有帮助。

如果要使用 Azure Cosmos DB SDK，请确保不会将其他 NuGet 包引用添加到你的项目中。 相反，请**通过 Azure Functions 的扩展包来解析 SDK 引用**。 独立于触发器和绑定使用 Azure Cosmos DB SDK

此外，如果您要手动创建您自己的[AZURE COSMOS DB SDK 客户端](./sql-api-sdk-dotnet-core.md)实例，则应该遵循只[使用单独模式方法](../azure-functions/manage-connections.md#documentclient-code-example-c)的客户端的一个实例的模式。 此过程将避免操作中的潜在套接字问题。

## <a name="common-scenarios-and-workarounds"></a>常见方案和解决方法

### <a name="azure-function-fails-with-error-message-collection-doesnt-exist"></a>Azure Function 失败，出现错误消息集合不存在

Azure 函数失败，并出现错误消息 "源集合" 集合名称 "（在数据库" 数据库名称 "中）或租用集合" collection2 "（在数据库" database2 "中）不存在。 侦听器开始之前，这两个集合必须存在。 若要自动创建租约集合，请将 "CreateLeaseCollectionIfNotExists" 设置为 "true" "

这意味着触发器运行所需的一个或两个 Azure Cosmos 容器不存在，或者无法访问 Azure 函数。 **错误本身会告诉你哪个 Azure Cosmos 数据库和容器是**根据你的配置查找的触发器。

1. 验证 `ConnectionStringSetting` 特性，并确定**Azure Function App 中存在的设置**。 此属性的值不应是连接字符串本身，而是配置设置的名称。
2. 验证 Azure Cosmos 帐户中是否存在 `databaseName` 和 `collectionName`。 如果使用自动替换值（使用 `%settingName%` 模式），请确保 Azure Function App 中存在该设置的名称。
3. 如果未指定 `LeaseCollectionName/leaseCollectionName`，则默认值为 "租约"。 验证此类容器是否存在。 （可选）可以在触发器中设置 `CreateLeaseCollectionIfNotExists` 属性，以 `true` 自动创建它。
4. 验证你的[Azure Cosmos 帐户的防火墙配置](how-to-configure-firewall.md)，以查看它不会阻止 Azure 功能。

### <a name="azure-function-fails-to-start-with-shared-throughput-collection-should-have-a-partition-key"></a>Azure 函数无法启动，"共享吞吐量集合应具有分区键"

以前版本的 Azure Cosmos DB 扩展不支持使用在[共享吞吐量数据库](./set-throughput.md#set-throughput-on-a-database)中创建的租约容器。 若要解决此问题，请更新[CosmosDB](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.Extensions.CosmosDB)扩展以获取最新版本。

### <a name="azure-function-fails-to-start-with-the-lease-collection-if-partitioned-must-have-partition-key-equal-to-id"></a>Azure 函数无法以 "租约集合，如果已分区，则必须具有等于 id 的分区键"。

此错误表示当前租约容器已分区，但分区键路径不 `/id`。 若要解决此问题，需要用 `/id` 作为分区键来重新创建租约容器。

### <a name="you-see-a-value-cannot-be-null-parameter-name-o-in-your-azure-functions-logs-when-you-try-to-run-the-trigger"></a>出现 "值不能为 null。 当你尝试运行触发器时，Azure Functions 日志中的参数名： o "

如果使用的是 Azure 门户并且在检查使用该触发器的 Azure 函数时尝试在屏幕上选择 "**运行**" 按钮，则会出现此问题。 触发器不要求你选择 "运行到启动"，它将在部署 Azure 函数时自动启动。 如果要在 Azure 门户上查看 Azure 函数的日志流，只需访问所监视的容器并插入一些新项，就会自动查看触发器的执行情况。

### <a name="my-changes-take-too-long-to-be-received"></a>接收更改所需的时间太长

此方案可能有多种原因，并应检查所有原因：

1. Azure 函数是否部署在 Azure Cosmos 帐户所在的同一区域？ 为获得最佳的网络延迟，Azure 函数和 Azure Cosmos 帐户应共置在同一个 Azure 区域。
2. Azure Cosmos 容器中发生的更改是连续性的还是偶发性的？
如果是后者，则原因可能是存储更改的时间与 Azure 函数拾取更改的时间有一段延迟。 这是因为，在内部，当触发器检查 Azure Cosmos 容器中的更改但未找到等待读取的更改时，它会休眠配置的一段时间（默认为 5 秒），然后检查新的更改（以避免 RU 消耗量过高）。 可以通过触发器`FeedPollDelay/feedPollDelay`配置[中的 ](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) 设置来配置此休眠时间（该值预期以毫秒为单位）。
3. 你的 Azure Cosmos 容器可能会受到[速率限制](./request-units.md)。
4. 你可以使用触发器中的 `PreferredLocations` 属性指定以逗号分隔的 Azure 区域列表，以定义自定义首选连接顺序。

### <a name="some-changes-are-missing-in-my-trigger"></a>触发器中缺少某些更改

如果发现 azure Cosmos 容器中发生的某些更改未被 Azure 函数选取，则需要执行一项初步调查步骤，。

当 Azure 函数接收到更改时，它通常会处理这些更改，并可以选择将结果发送到其他目标。 调查缺少的更改时，请确保度量在**引入点接收的更改**（在 Azure 函数启动时），而不是在目标上。

如果目标上缺少某些更改，这可能意味着在收到更改后，在 Azure 函数执行过程中出现一些错误。

在这种情况下，最佳做法是在代码中和可能正在处理更改的循环内添加 `try/catch` 块，以检测特定项子集的任何故障并相应地进行处理（将其发送到另一个存储进行进一步分析或重试）。 

> [!NOTE]
> 默认情况下，如果在代码执行过程中出现未经处理的异常，则 Cosmos DB 的 Azure Functions 触发器将不会重试一批更改。 这意味着更改未到达目标的原因是因为您无法处理它们。

如果你发现触发器根本未接收到一些更改，最常见的情况是**另一个 Azure 函数正在运行**。 它可以是在 Azure 中部署的另一个 Azure 函数，或者是在开发人员计算机上以**完全相同的配置**（相同的监视容器和租赁容器）运行的 azure function，此 azure 函数正在盗取你需要 Azure 功能处理的一部分更改。

此外，如果知道有多少 Azure Function App 实例正在运行，就可以验证方案。 如果检查借用容器并统计其中的租约项数，则它们中的 `Owner` 属性的非重复值应该等于 Function App 的实例数。 如果所有者数目超过已知 Azure 函数应用实例的数目，则表示这些额外的所有者正在“窃取”更改。

解决这种情况的一个简单方法是使用新的/不同的值将 `LeaseCollectionPrefix/leaseCollectionPrefix` 应用于函数，或者使用新的租约容器来进行测试。

### <a name="need-to-restart-and-re-process-all-the-items-in-my-container-from-the-beginning"></a>需要从头开始重新启动容器中的所有项，然后重新处理它们 
从头开始重新处理容器中的所有项：
1. 如果 Azure 函数当前正在运行，请停止它。 
1. 删除租约集合中的文档（或删除并重新创建租约集合，使其为空）
1. 将函数中的[StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) CosmosDBTrigger 属性设置为 true。 
1. 重新启动 Azure function。 现在它将读取并处理开始的所有更改。 

如果将[StartFromBeginning](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration)设置为 true，则会告知 Azure 函数开始从集合历史记录开始读取更改，而不是从当前时间开始读取更改。 这仅适用于尚未创建的租约（即租约集合中的文档）。 如果已创建租约，则将此属性设置为 "true" 将不起任何作用;在此方案中，当某个函数停止并重新启动时，它将从最后一个检查点开始读取，如租约集合中所定义。 若要从头开始重新处理，请遵循上述步骤1-4。  

### <a name="binding-can-only-be-done-with-ireadonlylistdocument-or-jarray"></a>仅可通过 IReadOnlyList\<文档 > 或 JArray 进行绑定

如果 Azure Functions 项目（或任何引用的项目）包含对 Azure Cosmos DB SDK 的手动 NuGet 引用，而该版本与[Azure Functions Cosmos DB 扩展](./troubleshoot-changefeed-functions.md#dependencies)提供的版本不同，则会发生此错误。

若要解决此问题，请删除已添加的手动 NuGet 引用，并让 Azure Cosmos DB SDK 引用通过 Azure Functions Cosmos DB 扩展包进行解析。

### <a name="changing-azure-functions-polling-interval-for-the-detecting-changes"></a>更改检测更改的 Azure 函数轮询间隔

如前文所述，[若要接收到更改的时间太长](./troubleshoot-changefeed-functions.md#my-changes-take-too-long-to-be-received)，Azure 函数将在检查新更改之前睡眠（默认情况下为5秒）。 可以通过触发器`FeedPollDelay/feedPollDelay`配置[中的 ](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) 设置来配置此休眠时间（该值预期以毫秒为单位）。

## <a name="next-steps"></a>后续步骤

* [为 Azure Functions 启用监视](../azure-functions/functions-monitoring.md)
* [Azure Cosmos DB .NET SDK 疑难解答](./troubleshoot-dot-net-sdk.md)
