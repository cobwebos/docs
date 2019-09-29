---
title: 检查池和节点错误 - Azure Batch
description: 创建池和节点时要检查的错误以及如何避免错误
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 3c8e189e84e0a467125995b3e2d633c285eb7367
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350065"
---
# <a name="check-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理 Azure Batch 池时，某些操作会立即发生。 但是，某些操作是异步的且在后台运行，需要数分钟才能完成。

检测即时发生的失败操作非常简单，因为会立即通过 API、CLI 或 UI 返回任何错误。

本文介绍可能针对池和池节点执行的后台操作。 它指定检测和避免失败的方式。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或重设现有池大小时，将指定目标节点数。  创建或重设大小操作是即时完成的，但实际分配新节点或删除现有节点可能需要几分钟时间。  指定[创建](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[重设大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中的重设大小超时。 如果 Batch 在大小调整超时期限内无法获取目标节点数，池会进入稳定状态并报告重设大小错误。

最近执行的评估的 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 属性会列出发生的错误。

重设大小错误的常见原因包括：

- 调整大小超时过短
  - 大多数情况下，15 分钟的默认超时足以分配或删除池节点。
  - 如果正在分配大量的节点，建议将重设大小超时设置为 30 分钟。 例如，从 Azure 市场映像将大小重设为 1,000 个节点以上，或从自定义 VM 映像将大小重设为 300 个节点以上。
- 核心配额不足
  - 批处理帐户具有的可跨所有池分配的核心数有限。 达到该配额后，Batch 就会停止分配节点。 [可以增加](https://docs.microsoft.com/azure/batch/batch-quota-limit)核心配额，使 Batch 可以分配更多节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，子网 IP 不足
  - 虚拟网络子网必须具有足够的未分配的 IP 地址才能分配给每个请求的池节点。 否则，无法创建节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，资源不足
  - 建议在与批处理帐户相同的订阅中创建资源（例如负载均衡器、公共 IP和网络安全组）。 请检查这些资源的订阅配额是否足够。
- 具有自定义 VM 映像的大型池
  - 使用自定义 VM 映像的大型池可能需要更长的时间进行分配，并且可能发生重大小超时。  有关限制和配置的建议，请参阅[使用共享映像库创建池](batch-sig-images.md)。

### <a name="automatic-scaling-failures"></a>自动缩放失败

还可以将 Azure Batch 设置为自动缩放池中的节点数。 定义[池的自动缩放公式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)的参数。 Batch 服务使用公式定期评估池中的节点数，并设置新的目标数。 可能会出现以下类型的问题：

- 自动缩放评估失败。
- 生成的重设大小操作失败并超时。
- 自动缩放公式导致节点目标值不正确的问题。 重设大小要么适用要么超时。

可使用 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 属性获取关于最近一次自动缩放评估的信息。 此属性报告评估时间、值和结果以及任何性能错误。

[池重设大小完成事件](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)捕获所有评估的相关信息。

### <a name="delete"></a>DELETE

删除包含节点的池时，Batch 首先会删除节点。 然后再删除池对象。 可能需要等待数分钟才会删除池节点。

Batch 在删除过程中将[池状态](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)设置为“删除中”。 调用应用程序可以使用“state”和“stateTransitionTime”属性来检测池删除时间是否过长。

## <a name="pool-compute-node-errors"></a>池计算节点错误

即使 Batch 在池中成功分配了节点，各种问题仍可能会导致某些节点不正常，无法运行任务。 这些节点仍会产生费用，因此务必要检测问题，避免不能使用的节点产生费用。 除了常见的节点错误外，知道当前[作业状态](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)对于故障排除非常有用。

### <a name="start-task-failures"></a>启动任务失败

建议为池指定一个可选的[开始任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。 与任何任务一样，可以使用从存储器中下载的命令行和资源文件。 开启后，即可为每个节点运行开始任务。 waitForSuccess 属性指定 Batch 是否等待开始任务成功完成后才计划节点的任何任务。

如果已将节点配置为等待开始任务成功完成，但开始任务失败，该如何操作？ 在这种情况下，该节点将不可用，但仍会产生费用。

可以使用顶级 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务是否失败。

如果已将 **waitForSuccess** 设置为 **true**，启动任务失败还会导致 Batch 将节点[状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为 **starttaskfailed**。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，请检查 stdout、stderr 和任何其他特定于任务的日志文件。

启动任务必须可重入，因为启动任务可能会在同一节点上多次运行；节点重置映像或重启时，启动任务就会运行。 在罕见情况下，启动任务会在某个事件导致节点重启后运行，此时某个操作系统或临时磁盘已重置映像，而另一个并没有这样做。 由于 Batch 启动任务（与所有 Batch 任务一样）从临时磁盘运行，因此通常这不是一个问题。但在某些情况下，启动任务将应用程序安装到操作系统磁盘，而将其他数据保留在临时磁盘上，这就可能会导致问题，因为会出现不同步现象。如果使用两种磁盘，请对应用程序进行相应的保护。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以为池指定一个或多个应用程序包。 在启动节点之后，在对任务进行计划之前，Batch 会将指定的包文件下载到每个节点并解压缩这些文件。 通常将开始任务命令行与应用程序包结合使用。 例如，将文件复制到其他文件或运行安装程序。

节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告下载和解压缩应用程序包失败；节点状态会设置为 **unusable**。

### <a name="container-download-failure"></a>容器下载失败

可以在池上指定一个或多个容器引用。 Batch 可将指定的容器下载到每个节点。 节点的 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性报告容器下载失败，并将节点状态设置为“不可用”。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于多种原因，Azure Batch 可能将[节点状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为“不可用”。 将节点状态设置为“不可用”之后，任务无法计划到节点中，但节点仍会产生费用。

节点处于“unusable”状态但没有 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)，这意味着 Batch 无法与 VM 通信。 在这种情况下，Batch 始终尝试恢复 VM。 Batch 不会自动尝试恢复无法安装应用程序包或容器的 VM，即使这些 VM 的状态为“unusable”。

如果 Batch 可以确定原因，则节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告该原因。

“不可用”节点的其他原因示例：

- 自定义 VM 映像无效。 例如，未正确准备映像。

- 由于基础结构故障或低级别的升级，移动了 VM。 Batch 会恢复节点。

- VM 映像已经部署在不受支持的硬件上。 例如，尝试在 [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) VM 上运行 CentOS HPC 映像。

- VM 位于 [Azure 虚拟网络](batch-virtual-network.md)中，并且流量已被阻止，无法发送到关键端口。

- VM 位于虚拟网络中，但流向 Azure 存储的出站流量被阻止。

- VM 位于使用客户 DNS 配置的虚拟网络中，DNS 服务器不能解析 Azure 存储。

### <a name="node-agent-log-files"></a>节点代理日志文件

每个池节点上运行的 Batch 代理进程可以提供日志文件。如果你需要就池节点问题联系支持人员，这些日志文件可能很有帮助。 可以通过 Azure 门户、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。 它可用于上传和保存日志文件。 之后，可以删除节点或池以节省运行节点的费用。

### <a name="node-disk-full"></a>节点磁盘已满

批处理用于作业文件、任务文件和共享文件的池节点 VM 的临时驱动器。

- 应用程序包文件
- 任务资源文件
- 特定于应用程序的文件下载到某个批处理文件夹
- 每次执行任务应用程序的 Stdout 和 stderr 文件
- 特定于应用程序的输出文件

其中一些文件仅在创建池节点后写入一次，例如池应用程序包或池启动任务资源文件。 即使在创建节点时只写入一次，如果这些文件太大，它们也可能填充临时驱动器。

对于在节点上运行的每个任务（如 stdout 和 stderr），会写出其他文件。 如果在同一节点上运行大量任务，并且/或任务文件太大，则可以填充临时驱动器。

临时驱动器的大小取决于 VM 大小。 选择 VM 大小时需考虑的一个因素是确保临时驱动器具有足够的空间。

- 在添加池时 Azure 门户中，可显示 VM 大小的完整列表，并且存在 "资源磁盘大小" 列。
- 描述所有 VM 大小的文章都有包含 "临时存储" 列的表;例如，[计算优化的 VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

对于每个任务编写的文件，可以为每个任务指定保留时间，以确定任务文件在自动清理之前保留多长时间。 可以缩短保留时间以降低存储要求。

如果临时磁盘空间已满，则节点当前将停止正在运行的任务。 以后会报告[节点错误](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)。


## <a name="next-steps"></a>后续步骤

请检查是否将应用程序设置为实施全面错误检查，尤其是异步操作。 它对于及时检测和诊断问题至关重要。
