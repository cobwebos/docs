---
title: Azure Batch 中的错误处理和检测
description: 从开发角度了解 Batch 服务工作流中的错误处理。
ms.topic: article
ms.date: 05/15/2020
ms.openlocfilehash: 3bd460598dae08fa18415e1c9865249f3ca4c9c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85964271"
---
# <a name="error-handling-and-detection-in-azure-batch"></a>Azure Batch 中的错误处理和检测

有时，你可能发现需要处理 Batch 解决方案中的任务和应用程序失败。 本文介绍错误类型以及解决方法。

## <a name="error-codes"></a>错误代码

常规错误类型包括：

- 请求从未到达 Batch 或 Batch 响应未及时到达客户端的网络故障。
- 内部服务器错误（标准 5xx 状态代码 HTTP 响应）。
- 与限制相关的错误，如带有 Retry-after 标头的 429 或 503 状态代码 HTTP 响应。
- 4xx 错误，如 AlreadyExists 和 InvalidOperation。 这意味着资源未处于状态转换所需的正确状态。

有关特定错误代码的详细信息（包括 REST API、Batch 服务和作业任务/调度的错误代码），请参阅 [Batch 状态和错误代码](/rest/api/batchservice/batch-status-and-error-codes)。

## <a name="application-failures"></a>应用程序失败

在执行过程中，应用程序可以生成诊断输出，这些信息可用于排查问题。 如[文件和目录](files-and-directories.md)中所述，Batch 服务会将标准输出和标准错误输出发送到计算节点上任务目录中的 `stdout.txt` 和 `stderr.txt` 文件。

可以使用 Azure 门户或 Batch SDK 之一下载这些文件。 例如，可以使用 Batch .NET 库中的 [ComputeNode.GetNodeFile](/dotnet/api/microsoft.azure.batch.computenode) 和 [CloudTask.GetNodeFile](/dotnet/api/microsoft.azure.batch.cloudtask) 检索这些文件和其他文件来进行故障排除。

## <a name="task-errors"></a>任务错误

任务错误分为多个类别。

### <a name="pre-processing-errors"></a>预处理错误

如果任务无法启动，则会为任务设置预处理错误。  

如果任务的资源文件已移动、存储帐户不再可用，或者发生其他使文件无法成功复制到节点的问题，则可能会出现预处理错误。

### <a name="file-upload-errors"></a>文件上传错误

如果为任务指定的文件由于某种原因而上传失败，则会为该任务设置文件上传错误。

如果提供的用于访问 Azure 存储的 SAS 无效或未提供写权限，如果存储帐户不再可用，或者如果遇到了另一问题，导致无法从节点成功复制文件，则可能会发生文件上传错误。

### <a name="application-errors"></a>应用程序错误

任务命令行指定的进程也可能会失败。 如果任务执行的进程返回非零退出代码，则将该进程视为失败（请参阅下一部分中的 *任务退出代码* ）。

对于应用程序错误，可以将 Batch 配置为自动重试任务，并最多重试指定的次数。

### <a name="constraint-errors"></a>约束错误

可以设置一个约束来指定作业或任务的最大执行持续期间，即 *maxWallClockTime*。 此约束可用于终止未能继续进行的任务。

如果超出了最长时间，则将任务标记为*已完成*，但退出代码将设置为 `0xC000013A`，*schedulingError* 字段将标记为 `{ category:"ServerError", code="TaskEnded"}`。

## <a name="task-exit-codes"></a>任务退出代码

如前所述，如果任务执行的程序返回非零退出代码，则 Batch 服务会将此任务标记为失败。 当任务执行某个进程时，Batch 将使用进程的返回代码填充任务的退出代码属性。

请务必注意，任务的退出代码不是由 Batch 服务确定， 而是由进程本身或此进程在其上运行的操作系统确定。

## <a name="task-failures-or-interruptions"></a>任务失败或中断

任务偶尔会失败或中断。 任务应用程序本身可能会失败，运行任务的节点可能会重新启动，或者在执行大小调整操作期间可能从池中删除节点（如果池的取消分配策略设置为在不等待任务完成的情况下立即删除节点）。 在所有情况下，任务都可以由 Batch 自动排队，并在另一个节点上执行。

间歇性的问题也有可能会导致任务停止响应，或者花费很长时间才能完成执行。 可为任务设置最长的执行时间间隔。 如果超出最长执行时间间隔，Batch 服务会中断任务应用程序。

## <a name="connect-to-compute-nodes"></a>连接到计算节点

可通过远程登录到计算节点来进一步执行调试和故障排除。 可以使用 Azure 门户下载 Windows 节点的远程桌面协议 (RDP) 文件，并获取 Linux 节点的安全外壳 (SSH) 连接信息。 也可以使用 Batch API（例如，使用 [Batch .NET](/dotnet/api/microsoft.azure.batch.computenode) 或 [Batch Python](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh)）来执行此操作。

> [!IMPORTANT]
> 若要通过 RDP 或 SSH 连接到某个节点，必须先在该节点上创建一个用户。 为此，可以使用 Azure 门户通过 Batch REST API [将用户帐户添加到节点](/rest/api/batchservice/computenode/adduser)、在 Batch .NET 中调用 [ComputeNode.CreateComputeNodeUser](/dotnet/api/microsoft.azure.batch.computenode) 方法，或在 Batch Python 模块中调用 [add_user](batch-linux-nodes.md#connect-to-linux-nodes-using-ssh) 方法。

如需限制或禁用通过 RDP 或 SSH 访问计算节点的功能，请参阅[在 Azure Batch 池中配置或禁用到计算节点的远程访问](pool-endpoint-configuration.md)。

## <a name="troubleshoot-problem-nodes"></a>对有问题的节点进行故障排除

在部分任务失败的情况下，Batch 客户端应用程序或服务可以检查失败任务的元数据来找出行为异常的节点。 池中的每个节点都有一个唯一 ID，运行任务的节点包含在任务元数据中。 识别出“有问题的节点”后，可对其执行多种操作：

- **重新启动节点** ([REST](/rest/api/batchservice/computenode/reboot) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reboot))

    重新启动节点有时可以清除潜在的问题，例如进程停滞或崩溃。 如果池使用启动任务或作业使用作业准备任务，节点重新启动时将执行这些任务。
- **重置映像节点** ([REST](/rest/api/batchservice/computenode/reimage) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.reimage))

    这会在节点上重新安装操作系统。 和重新启动节点一样，在重置映像节点后，便重新执行启动任务和作业准备任务。
- **从池中删除节点** ([REST](/rest/api/batchservice/pool/removenodes) | [.NET](/dotnet/api/microsoft.azure.batch.pooloperations))

    有时必须从池中完全删除节点。
- **禁用节点上的任务调度** ([REST](/rest/api/batchservice/computenode/disablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.disablescheduling))

    这实际上是使节点脱机，以便不再收到任何分配的任务，但允许节点继续运行并保留在池中。 这可让你执行进一步的调查以了解失败原因，却又会不丢失失败任务的数据，并且不让节点造成额外的任务失败。 例如，可以禁用节点上的任务调度，并从远程登录以检查节点的事件日志，或执行其他故障排除操作。 完成调查后，可以启用任务调度 ([REST](/rest/api/batchservice/computenode/enablescheduling) | [.NET](/dotnet/api/microsoft.azure.batch.computenode.enablescheduling)) 使节点重新联机，或者执行上述其中一个其他操作。

> [!IMPORTANT]
> 通过上述操作，你可以指定在执行该操作时如何处理当前正在节点上运行的任务。 例如，在使用 Batch .NET 客户端库的节点上禁用任务调度时，可以指定 [DisableComputeNodeSchedulingOption](/dotnet/api/microsoft.azure.batch.common.disablecomputenodeschedulingoption) 枚举值，以指定是要终止正在运行的任务、重新将任务列入队列以在其他节点上调度，还是允许正在运行的任务先完成再执行操作 (TaskCompletion)  。

## <a name="retry-after-errors"></a>出错后重试

如果操作失败，Batch API 会通知你。 所有操作都包含一个全局重试处理程序，因此都可重试。 最好使用此内置机制。

失败后，应等待一段时间（重试间隔几秒），然后重试。 如果重试次数过于频繁或重试速度过快，重试处理程序将中止。

## <a name="next-steps"></a>后续步骤

- 了解如何[检查池和节点错误](batch-pool-node-error-checking.md)。
- 了解如何[检查作业和任务错误](batch-job-task-error-checking.md)。
- 查看 [Batch 状态和错误代码](/rest/api/batchservice/batch-status-and-error-codes)列表。
