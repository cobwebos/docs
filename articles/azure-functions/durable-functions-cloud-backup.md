---
title: "Durable Functions 中的扇出/扇入方案 - Azure"
description: "了解如何在 Azure Functions 的 Durable Functions 扩展中实现扇出/扇入方案。"
services: functions
author: cgillum
manager: cfowler
editor: 
tags: 
keywords: 
ms.service: functions
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 09/29/2017
ms.author: azfuncdf
ms.openlocfilehash: 85484b79012243afd374a97e7f518e9a8b1043ea
ms.sourcegitcommit: cf42a5fc01e19c46d24b3206c09ba3b01348966f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/29/2017
---
# <a name="fan-outfan-in-scenario-in-durable-functions---cloud-backup-example"></a>Durable Functions 中的扇出/扇入方案 - 云备份示例

“扇出/扇入”是指同时执行多个函数，然后针对结果执行某种聚合的模式。 本文讲解一个使用 [Durable Functions](durable-functions-overview.md) 实现扇入/扇出方案的示例。 该示例是一个持久函数，可将应用的全部或部分站点内容备份到 Azure 存储中。

## <a name="prerequisites"></a>先决条件

* 按照[安装 Durable Functions](durable-functions-install.md) 中的说明设置示例。
* 本文假定用户已完成 [Hello Sequence](durable-functions-sequence.md) 示例演练。

## <a name="scenario-overview"></a>方案概述

在此示例中，函数会将指定目录下的所有文件以递归方式上传到 Blob 存储。 它们还会统计已上传的字节总数。

可以编写单个函数来处理所有这些操作。 会遇到的主要问题是**可伸缩性**。 单个函数执行只能在单个 VM 上运行，因此，吞吐量会受到该 VM 的吞吐量限制。 另一个问题是**可靠性**。 如果中途失败或者整个过程花费的时间超过 5 分钟，则备份可能以部分完成状态失败。 然后，需要重新开始备份。

更可靠的方法是编写两个正则函数：一个函数枚举文件并将文件名添加到队列，另一个函数从队列读取数据并将文件上传到 Blob 存储。 这样可以提高吞吐量和可靠性，但需要预配和管理队列。 更重要的是，如果想要执行其他任何操作，例如报告已上传的字节总数，则这种做法会明显增大**状态管理**和**协调**的复杂性。

Durable Functions 方法提供前面所述的所有优势，并且其系统开销极低。

## <a name="the-functions"></a>函数

本文介绍示例应用中的以下函数：

* `E2_BackupSiteContent`
* `E2_GetFileList`
* `E2_CopyFileToBlob`

以下部分介绍用于 Azure 门户开发的配置和代码。 本文末尾显示了用于 Visual Studio 开发的代码。

## <a name="the-cloud-backup-orchestration"></a>云备份业务流程

`E2_BackupSiteContent` 函数对业务流程协调程序函数使用标准的 *function.json*。

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/function.json)]

实现业务流程协调程序函数的代码如下：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_BackupSiteContent/run.csx)]

本质上，该业务流程协调程序函数执行以下操作：

1. 采用 `rootDirectory` 值作为输入参数。
2. 调用某个函数来获取 `rootDirectory` 下的文件的递归列表。
3. 发出多个并行函数调用，以将每个文件上传到 Azure Blob 存储。
4. 等待所有上传完成。
5. 返回已上传到 Azure Blob 存储的总字节数。

请注意 `await Task.WhenAll(tasks);` 行。 对 `E2_CopyFileToBlob` 函数的所有调用未进入等待状态。 这是有意而为的，目的是让这些调用同时运行。 将此任务数组传递给 `Task.WhenAll` 时，会获得所有复制操作完成之前不会完成的任务。 如果熟悉 .NET 中的任务并行库 (TPL) 的话，则对此过程也不会陌生。 差别在于，这些任务可在多个 VM 上同时运行，Durable Functions 扩展可确保端到端执行能够弹性应对进程回收。

完成 `Task.WhenAll` 并进入等待中状态后，我们知道所有函数调用都已完成，并已收到返回值。 每次调用 `E2_CopyFileToBlob` 都会返回已上传字节数，因此，将所有这些返回值相加就能计算出字节数总和。

## <a name="helper-activity-functions"></a>帮助器活动函数

与其他示例一样，帮助器活动函数无非是使用 `activityTrigger` 触发器绑定的正则函数。 例如，`E2_GetFileList` 的 *function.json* 文件如下所示：

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/function.json)]

下面是实现：

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_GetFileList/run.csx)]

> [!NOTE]
> 你可能会疑惑，为何不直接将此代码放入业务流程协调程序函数？ 可以这样做，不过，这会破坏业务流程协调程序函数的基本规则，即，它们不得执行 I/O，包括本地文件系统的访问。

`E2_CopyFileToBlob` 的 *function.json* 文件同样也很简单：

[!code-json[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/function.json)]

实现也十分直截了当。 本示例恰好使用了 Azure Functions 绑定的某些高级功能（即使用了 `Binder` 参数），但对于本演练，无需考虑这些细节。

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E2_CopyFileToBlob/run.csx)]

实现从磁盘加载文件，并以异步方式将内容流式传输到“backups”容器中同名的 Blob。 返回值为已复制到存储的字节数，业务流程协调程序函数随后会使用此数字来计算总和。

> [!NOTE]
> 这是一个演示如何将 I/O 操作移入 `activityTrigger` 函数的极佳示例。 这样，不仅可以在许多不同的 VM 上分配工作，而且还能获得设置进度检查点的优势。 如果主机进程出于任何原因终止，你就知道哪些上传操作已完成。

## <a name="run-the-sample"></a>运行示例

可以通过发送以下 HTTP POST 请求来启动业务流程。

```
POST http://{host}/orchestrators/E2_BackupSiteContent
Content-Type: application/json
Content-Length: 20

"D:\\home\\LogFiles"
```

> [!NOTE]
> 调用的 `HttpStart` 函数只会处理 JSON 格式的内容。 为此，`Content-Type: application/json` 标头是必需的，目录路径已编码为 JSON 字符串。

此 HTTP 请求会触发 `E2_BackupSiteContent` 业务流程协调程序，并将字符串 `D:\home\LogFiles` 作为参数传递。 响应提供了一个链接，可使用该链接获取备份操作的状态：

```
HTTP/1.1 202 Accepted
Content-Length: 719
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

(...trimmed...)
```

根据函数应用中包含的日志文件数，此操作可能需要几分钟才能完成。 可以通过查询上述 HTTP 202 响应的 `Location` 标头中的 URL 来获取最新状态。

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 202 Accepted
Content-Length: 148
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/b4e9bdcc435d460f8dc008115ff0a8a9?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"runtimeStatus":"Running","input":"D:\\home\\LogFiles","output":null,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:16Z"}
```

在本例中，函数仍在运行。 可以查看已保存到业务流程协调程序状态中的输入，以及上次更新时间。 可以继续使用 `Location` 标头值来轮询完成状态。 当状态为“Completed”时，会看到如下所示的 HTTP 响应值：

```
HTTP/1.1 200 OK
Content-Length: 152
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"D:\\home\\LogFiles","output":452071,"createdTime":"2017-06-29T18:50:55Z","lastUpdatedTime":"2017-06-29T18:51:26Z"}
```

现在，可以看到业务流程已完成，以及完成它大约花费的时间。 另外，还会看到 `output` 字段的值，指示已上传大约 450 KB 的日志。

## <a name="visual-studio-sample-code"></a>Visual Studio 示例代码

下面是 Visual Studio 项目中以单个 C# 文件形式提供的业务流程：

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/BackupSiteContent.cs)]

## <a name="next-steps"></a>后续步骤

此示例说明了如何实现扇出/扇入模式。 下一个示例演示如何在[永久业务流程](durable-functions-eternal-orchestrations.md)中实现[有状态单一实例](durable-functions-singletons.md)模式。

> [!div class="nextstepaction"]
> [运行有状态单一实例示例](durable-functions-counter.md)
