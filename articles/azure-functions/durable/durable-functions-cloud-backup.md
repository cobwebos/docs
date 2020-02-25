---
title: Durable Functions 中的扇出/扇入方案 - Azure
description: 了解如何在 Azure Functions 的 Durable Functions 扩展中实现扇出/扇入方案。
ms.topic: conceptual
ms.date: 11/02/2019
ms.author: azfuncdf
ms.openlocfilehash: d61600801286126ea6ffb9a97bc5655b6f233816
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562184"
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Durable Functions 中的扇出/扇入方案 - 云备份示例

“扇出/扇入”是指同时执行多个函数，然后针对结果执行某种聚合的模式。 本文讲解一个使用 [Durable Functions](durable-functions-overview.md) 实现扇入/扇出方案的示例。 该示例是一个持久函数，可将应用的全部或部分站点内容备份到 Azure 存储中。

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>方案概述

在此示例中，函数会将指定目录下的所有文件以递归方式上传到 Blob 存储。 它们还会统计已上传的字节总数。

可以编写单个函数来处理所有这些操作。 会遇到的主要问题是**可伸缩性**。 单个函数执行只能在单个虚拟机上运行，因此吞吐量会受到单个 VM 的吞吐量的限制。 另一个问题是**可靠性**。 如果在中间发生故障，或者整个进程需要5分钟以上，则备份可能会在部分完成状态下失败。 然后，需要重新开始备份。

更可靠的方法是编写两个正则函数：一个函数枚举文件并将文件名添加到队列，另一个函数从队列读取数据并将文件上传到 Blob 存储。 此方法在吞吐量和可靠性方面更好，但它需要你预配和管理队列。 更重要的是，如果想要执行其他任何操作，例如报告已上传的字节总数，则这种做法会明显增大**状态管理**和**协调**的复杂性。

Durable Functions 方法提供前面所述的所有优势，并且其系统开销极低。

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E2_BackupSiteContent`：调用 `E2_GetFileList` 的业务流程[协调程序函数](durable-functions-bindings.md#orchestration-trigger)获取要备份的文件列表，然后调用 `E2_CopyFileToBlob` 备份每个文件。
* `E2_GetFileList`：一个[活动函数](durable-functions-bindings.md#activity-trigger)，该函数返回目录中的文件的列表。
* `E2_CopyFileToBlob`：将单个文件备份到 Azure Blob 存储的活动函数。

### <a name="e2_backupsitecontent-orchestrator-function"></a>E2_BackupSiteContent orchestrator 函数

本质上，该业务流程协调程序函数执行以下操作：

1. 采用 `rootDirectory` 值作为输入参数。
2. 调用某个函数来获取 `rootDirectory` 下的文件的递归列表。
3. 发出多个并行函数调用，以将每个文件上传到 Azure Blob 存储。
4. 等待所有上传完成。
5. 返回已上传到 Azure Blob 存储的总字节数。

# <a name="c"></a>[C#](#tab/csharp)

下面的代码可实现业务流程协调程序函数：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=16-42)]

请注意 `await Task.WhenAll(tasks);` 行。 对 `E2_CopyFileToBlob` 函数的所有单独调用都*不会*等待，从而使它们可以并行运行。 将此任务数组传递给 `Task.WhenAll` 时，会获得所有复制操作完成之前不会完成的任务。 如果熟悉 .NET 中的任务并行库 (TPL) 的话，则对此过程也不会陌生。 不同之处在于，这些任务可同时在多个虚拟机上运行，而 Durable Functions 扩展可确保端到端执行可以灵活地进行回收。

完成 `Task.WhenAll` 并进入等待中状态后，我们知道所有函数调用都已完成，并已收到返回值。 每次调用 `E2_CopyFileToBlob` 都会返回已上传字节数，因此，将所有这些返回值相加就能计算出字节数总和。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

函数对业务流程协调程序函数使用了标准*函数 json* 。

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/function.json)]

下面的代码可实现业务流程协调程序函数：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_BackupSiteContent/index.js)]

请注意 `yield context.df.Task.all(tasks);` 行。 *未*生成对 `E2_CopyFileToBlob` 函数的所有单独调用，这使它们可以并行运行。 将此任务数组传递给 `context.df.Task.all` 时，会获得所有复制操作完成之前不会完成的任务。 如果你熟悉 JavaScript 中的[`Promise.all`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) ，则这不是你的新用户。 不同之处在于，这些任务可同时在多个虚拟机上运行，而 Durable Functions 扩展可确保端到端执行可以灵活地进行回收。

> [!NOTE]
> 虽然任务在概念上类似于 JavaScript 承诺，但业务流程协调程序函数应使用 `context.df.Task.all` 和 `context.df.Task.any`（而不是 `Promise.all` 和 `Promise.race`）来管理任务并行化。

从 `context.df.Task.all`开始，我们知道所有函数调用已完成，并将返回值返回给我们。 每次调用 `E2_CopyFileToBlob` 都会返回已上传字节数，因此，将所有这些返回值相加就能计算出字节数总和。

---

### <a name="helper-activity-functions"></a>帮助器活动函数

与其他示例一样，帮助器活动函数无非是使用 `activityTrigger` 触发器绑定的正则函数。

#### <a name="e2_getfilelist-activity-function"></a>E2_GetFileList 活动函数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=44-54)]

# <a name="javascript"></a>[JavaScript](#tab/javascript)

`E2_GetFileList` 的*函数 json*文件如下所示：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/function.json)]

下面是实现：

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_GetFileList/index.js)]

此函数使用 `readdirp` 模块（版本2.x）以递归方式读取目录结构。

---

> [!NOTE]
> 你可能会疑惑，为何不直接将此代码放入业务流程协调程序函数？ 可以这样做，不过，这会破坏业务流程协调程序函数的基本规则，即，它们不得执行 I/O，包括本地文件系统的访问。 有关详细信息，请参阅[Orchestrator 函数代码约束](durable-functions-code-constraints.md)。

#### <a name="e2_copyfiletoblob-activity-function"></a>E2_CopyFileToBlob 活动函数

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs?range=56-81)]

> [!NOTE]
> 你将需要安装 `Microsoft.Azure.WebJobs.Extensions.Storage` NuGet 包以运行示例代码。

此函数使用 Azure Functions 绑定（即，使用[`Binder` 参数](../functions-dotnet-class-library.md#binding-at-runtime)）的一些高级功能，但在本演练中，无需担心这些详细信息。

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*的*function.json`E2_CopyFileToBlob` 文件同样也很简单：

[!code-json[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/function.json)]

JavaScript 实现使用适用于[Node 的 Azure 存储 SDK](https://github.com/Azure/azure-storage-node)将文件上传到 Azure Blob 存储。

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E2_CopyFileToBlob/index.js)]

---

实现从磁盘加载文件，并以异步方式将内容流式传输到“backups”容器中同名的 Blob。 返回值为已复制到存储的字节数，业务流程协调程序函数随后会使用此数字来计算总和。

> [!NOTE]
> 这是一个演示如何将 I/O 操作移入 `activityTrigger` 函数的极佳示例。 工作不仅可以分布在多个不同的计算机上，还可以获得检查进度的好处。 如果主机进程出于任何原因终止，你就知道哪些上传操作已完成。

## <a name="run-the-sample"></a>运行示例

可以通过发送以下 HTTP POST 请求来启动业务流程。

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> 调用的 `HttpStart` 函数只会处理 JSON 格式的内容。 为此，`Content-Type: application/json` 标头是必需的，目录路径已编码为 JSON 字符串。 此外，HTTP 代码片段假定 `host.json` 文件中有一个条目，该条目从所有 HTTP 触发器函数 URL 中删除默认的 `api/` 前缀。 可以在示例的 `host.json` 文件中找到此配置的标记。

此 HTTP 请求会触发 `E2_BackupSiteContent` 业务流程协调程序，并将字符串 `D:\home\LogFiles` 作为参数传递。 响应提供了一个链接，可使用该链接获取备份操作的状态：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

根据函数应用中包含的日志文件数，此操作可能需要几分钟才能完成。 可以通过查询上述 HTTP 202 响应的 `Location` 标头中的 URL 来获取最新状态。

```
GET http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/runtime/webhooks/durabletask/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:16Z"}
```

在本例中，函数仍在运行。 可以查看已保存到业务流程协调程序状态中的输入，以及上次更新时间。 可以继续使用 `Location` 标头值来轮询完成状态。 当状态为“Completed”时，会看到如下所示的 HTTP 响应值：

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2019-06-29T18:50:55Z","lastUpdatedTime":"2019-06-29T18:51:26Z"}
```

现在，可以看到业务流程已完成，以及完成它大约花费的时间。 另外，还会看到 `output` 字段的值，指示已上传大约 450 KB 的日志。

## <a name="next-steps"></a>后续步骤

此示例说明了如何实现扇出/扇入模式。 下一个示例演示如何使用[持久计时器](durable-functions-timers.md)实现监视模式。

> [!div class="nextstepaction"]
> [运行监视示例](durable-functions-monitor.md)