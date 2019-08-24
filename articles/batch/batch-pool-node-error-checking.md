---
title: 检查池和节点错误 - Azure Batch
description: 创建池和节点时要检查的错误以及如何避免错误
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: d115b7d56609b95f2ea10b3fee2f8900102b94e4
ms.sourcegitcommit: dcf3e03ef228fcbdaf0c83ae1ec2ba996a4b1892
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2019
ms.locfileid: "70012472"
---
# <a name="check-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理 Azure Batch 池时，某些操作会立即发生。 但是, 有些操作是异步的, 并在后台运行, 需要几分钟才能完成。

检测即时发生的失败操作非常简单，因为会立即通过 API、CLI 或 UI 返回任何错误。

本文介绍可能针对池和池节点执行的后台操作。 它指定检测和避免失败的方式。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或重设现有池大小时，将指定目标节点数。  创建或调整大小操作将立即完成, 但新节点的实际分配或现有节点的删除操作可能需要几分钟的时间。  指定[创建](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[重设大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中的重设大小超时。 如果在调整超时期限内批处理无法获取目标节点数, 则池将进入稳定状态并报告调整大小错误。

最新计算的[ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror)属性列出发生的错误。

大小调整错误的常见原因包括:

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
  - 使用自定义 VM 映像的大型池可能需要更长的时间进行分配，并且可能发生重大小超时。  要了解关于限制和配置的建议，请参阅[使用自定义映像创建虚拟机池](https://docs.microsoft.com/azure/batch/batch-custom-images)。

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

即使批处理成功分配池中的节点, 不同的问题也会导致某些节点运行不正常, 并且无法运行任务。 这些节点仍会产生费用, 因此, 检测问题以避免为不能使用的节点付费, 这一点很重要。 除了常见的节点错误外, 知道当前[作业状态](https://docs.microsoft.com/rest/api/batchservice/job/get#jobstate)对于故障排除非常有用。

### <a name="start-task-failures"></a>启动任务失败

建议为池指定一个可选的[开始任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。 与任何任务一样，可以使用从存储器中下载的命令行和资源文件。 开启后，即可为每个节点运行开始任务。 waitForSuccess 属性指定 Batch 是否等待开始任务成功完成后才计划节点的任何任务。

如果已将节点配置为等待开始任务成功完成，但开始任务失败，该如何操作？ 在这种情况下, 该节点将无法使用, 但仍会产生费用。

可以使用顶级 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务是否失败。

如果**waitForSuccess**设置为**true**, 则失败的启动任务还会导致批处理将节点[状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为**starttaskfailed** 。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，请检查 stdout、stderr 和任何其他特定于任务的日志文件。

启动任务必须是可重入的, 因为启动任务可以在同一个节点上多次运行;启动任务在重置映像或重新启动节点时运行。 在极少数情况下, 当某个事件导致节点重新启动 (其中一个操作系统或临时磁盘重置映像) 时, 启动任务将运行。 由于批处理启动任务 (如所有批处理任务) 从临时磁盘运行, 这通常不是问题, 但在某些情况下, 启动任务将应用程序安装到操作系统磁盘并在临时磁盘上保留其他数据, 这可能导致问题, 因为它不同步。如果同时使用这两个磁盘, 请相应地保护应用程序。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以为池指定一个或多个应用程序包。 Batch 会将指定的包文件下载到每个节点, 并在节点启动之后但在计划任务之前 uncompresses 文件。 通常将开始任务命令行与应用程序包结合使用。 例如，将文件复制到其他文件或运行安装程序。

节点[错误](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)属性报告下载和取消压缩应用程序包失败;节点状态设置为 "**不可用**"。

### <a name="container-download-failure"></a>容器下载失败

可以在池上指定一个或多个容器引用。 Batch 可将指定的容器下载到每个节点。 节点的 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性报告容器下载失败，并将节点状态设置为“不可用”。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于多种原因，Azure Batch 可能将[节点状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为“不可用”。 将节点状态设置为“不可用”之后，任务无法计划到节点中，但节点仍会产生费用。

处于**不可用**状态但没有[错误](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)的节点意味着批处理无法与 VM 通信。 在这种情况下，Batch 始终尝试恢复 VM。 Batch 不会自动尝试恢复无法安装应用程序包或容器的 Vm, 即使它们的状态不可**用**。

如果 Batch 可以确定原因，则节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告该原因。

“不可用”节点的其他原因示例：

- 自定义 VM 映像无效。 例如，未正确准备映像。

- 由于基础结构故障或低级别的升级，移动了 VM。 Batch 会恢复节点。

- VM 映像已经部署在不受支持的硬件上。 例如，尝试在 [Standard_D1_v2](../virtual-machines/linux/sizes-general.md#dv2-series) VM 上运行 CentOS HPC 映像。

- VM 位于 [Azure 虚拟网络](batch-virtual-network.md)中，并且流量已被阻止，无法发送到关键端口。

- Vm 位于虚拟网络中, 但会阻止到 Azure 存储的出站流量。

- Vm 位于具有客户 DNS 配置的虚拟网络中, DNS 服务器无法解析 Azure 存储。

### <a name="node-agent-log-files"></a>节点代理日志文件

如果需要联系支持部门节点问题, 则在每个池节点上运行的批处理代理进程可能会提供日志文件。 可以通过 Azure 门户、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。 它可用于上传和保存日志文件。 之后，可以删除节点或池以节省运行节点的费用。

### <a name="node-disk-full"></a>节点磁盘已满

批处理用于作业文件、任务文件和共享文件的池节点 VM 的临时驱动器。

- 应用程序包文件
- 任务资源文件
- 特定于应用程序的文件下载到某个批处理文件夹
- 每次执行任务应用程序的 Stdout 和 stderr 文件
- 特定于应用程序的输出文件

其中一些文件仅在创建池节点后写入一次, 例如池应用程序包或池启动任务资源文件。 即使在创建节点时只写入一次, 如果这些文件太大, 它们也可能填充临时驱动器。

对于在节点上运行的每个任务 (如 stdout 和 stderr), 会写出其他文件。 如果在同一节点上运行大量任务, 并且/或任务文件太大, 则可以填充临时驱动器。

临时驱动器的大小取决于 VM 大小。 选择 VM 大小时需考虑的一个因素是确保临时驱动器具有足够的空间。

- 在添加池时 Azure 门户中, 可显示 VM 大小的完整列表, 并且存在 "资源磁盘大小" 列。
- 描述所有 VM 大小的文章都有包含 "临时存储" 列的表;例如,[计算优化的 VM 大小](https://docs.microsoft.com/azure/virtual-machines/windows/sizes-compute)

对于每个任务编写的文件, 可以为每个任务指定保留时间, 以确定任务文件在自动清理之前保留多长时间。 可以缩短保留时间以降低存储要求。

如果临时磁盘空间已满, 则节点当前将停止正在运行的任务。 以后会报告[节点错误](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror)。


## <a name="next-steps"></a>后续步骤

请检查是否将应用程序设置为实施全面错误检查，尤其是异步操作。 它对于及时检测和诊断问题至关重要。
