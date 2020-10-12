---
title: 检查池和节点错误
description: 本文介绍了可能会发生的后台操作、要检查的错误，以及在创建池和节点时如何避免这些错误。
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: how-to
ms.openlocfilehash: 519b357e4e5fde30221f7dc804bb848ecec9704c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "85979911"
---
# <a name="check-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理 Azure Batch 池时，某些操作会立即发生。 但是，某些操作是异步的，并且在后台运行，需要几分钟才能完成。

检测即时发生的失败操作非常简单，因为会立即通过 API、CLI 或 UI 返回任何错误。

本文介绍可能针对池和池节点执行的后台操作。 它指定检测和避免失败的方式。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或重设现有池大小时，将指定目标节点数。  创建或调整大小操作会立即完成，但新节点的实际分配或现有节点的删除操作可能需要几分钟才能完成。  指定[创建](/rest/api/batchservice/pool/add)或[重设大小](/rest/api/batchservice/pool/resize) API 中的重设大小超时。 如果在调整大小超时期间 Batch 无法获取目标节点数，则池将进入稳定状态并报告调整大小错误。

最新评估的 [ResizeError](/rest/api/batchservice/pool/get#resizeerror) 属性会列出发生的错误。

调整大小错误的常见原因包括：

- 调整大小超时过短
  - 大多数情况下，15 分钟的默认超时足以分配或删除池节点。
  - 如果正在分配大量的节点，建议将重设大小超时设置为 30 分钟。 例如，从 Azure 市场映像将大小重设为 1,000 个节点以上，或从自定义 VM 映像将大小重设为 300 个节点以上。
- 核心配额不足
  - 批处理帐户具有的可跨所有池分配的核心数有限。 达到该配额后，Batch 就会停止分配节点。 [可以增加](./batch-quota-limit.md)核心配额，使 Batch 可以分配更多节点。
- 当[池处于虚拟网络](./batch-virtual-network.md)，子网 IP 不足
  - 虚拟网络子网必须具有足够的未分配的 IP 地址才能分配给每个请求的池节点。 否则，无法创建节点。
- 当[池处于虚拟网络](./batch-virtual-network.md)，资源不足
  - 建议在与批处理帐户相同的订阅中创建资源（例如负载均衡器、公共 IP和网络安全组）。 请检查这些资源的订阅配额是否足够。
- 具有自定义 VM 映像的大型池
  - 使用自定义 VM 映像的大型池可能需要更长的时间进行分配，并且可能发生重大小超时。  有关限制和配置的建议，请参阅[使用共享映像库创建池](batch-sig-images.md)。

### <a name="automatic-scaling-failures"></a>自动缩放失败

还可以将 Azure Batch 设置为自动缩放池中的节点数。 定义[池的自动缩放公式](./batch-automatic-scaling.md)的参数。 Batch 服务使用公式定期评估池中的节点数，并设置新的目标数。 可能会出现以下类型的问题：

- 自动缩放评估失败。
- 生成的重设大小操作失败并超时。
- 自动缩放公式导致节点目标值不正确的问题。 重设大小要么适用要么超时。

可使用 [autoScaleRun](/rest/api/batchservice/pool/get#autoscalerun) 属性获取关于最近一次自动缩放评估的信息。 此属性报告评估时间、值和结果以及任何性能错误。

[池重设大小完成事件](./batch-pool-resize-complete-event.md)捕获所有评估的相关信息。

### <a name="delete"></a>删除

删除包含节点的池时，Batch 首先会删除节点。 然后再删除池对象。 可能需要等待数分钟才会删除池节点。

Batch 在删除过程中将[池状态](/rest/api/batchservice/pool/get#poolstate)设置为“删除中”。 调用应用程序可以使用“state”和“stateTransitionTime”属性来检测池删除时间是否过长 。

## <a name="pool-compute-node-errors"></a>池计算节点错误

即使当 Batch 成功分配池中的节点时，各种问题也可能会导致某些节点运行状况不佳且无法运行任务。 这些节点仍会产生费用，因此，请务必检测问题，以免为不能使用的节点付费。 除了常见的节点错误外，了解当前[作业状态](/rest/api/batchservice/job/get#jobstate)也有助于排除故障。

### <a name="start-task-failures"></a>开始任务失败

建议为池指定一个可选的[开始任务](/rest/api/batchservice/pool/add#starttask)。 与任何任务一样，可以使用从存储器中下载的命令行和资源文件。 开启后，即可为每个节点运行开始任务。 waitForSuccess 属性指定 Batch 是否等待开始任务成功完成后才计划节点的任何任务。

如果已将节点配置为等待开始任务成功完成，但开始任务失败，该如何操作？ 在这种情况下，该节点将无法使用，但仍会产生费用。

可以使用顶级 [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务是否失败。

如果将“waitForSuccess”设置为“true”，则失败的开始任务还会导致 Batch 将节点[状态](/rest/api/batchservice/computenode/get#computenodestate)设置为“starttaskfailed”  。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，请检查 stdout、stderr 和任何其他特定于任务的日志文件。

开始任务必须是可重入的，因为开始任务可能会在同一个节点上运行多次；开始任务将在节点重置映像或重新启动时运行。 在极少数情况下，当事件导致节点重新启动（在此过程中某个操作系统或临时磁盘重置映像）时，开始任务会运行。 由于 Batch 开始任务（像所有 Batch 任务一样）是从临时磁盘运行的，因此，通常不会出现问题，但当开始任务将应用程序安装到操作系统磁盘而将其他数据保留在临时磁盘中时，在这些情况下可能会出现问题，因为事项不同步。如果同时使用这两个磁盘，请采取相应措施保护应用程序。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以为池指定一个或多个应用程序包。 Batch 将指定的包文件下载到每个节点，并在节点开始之后、计划任务之前解压缩文件。 通常将开始任务命令行与应用程序包结合使用。 例如，将文件复制到其他文件或运行安装程序。

节点 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性报告未能下载和解压缩应用程序包；节点状态设置为“不可用”。。

### <a name="container-download-failure"></a>容器下载失败

你可以在池上指定一个或多个容器引用。 Batch 会将指定的容器下载到各个节点。 节点 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性报告了容器下载失败，并将节点状态设置为“不可用”。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于多种原因，Azure Batch 可能将[节点状态](/rest/api/batchservice/computenode/get#computenodestate)设置为“不可用”。 将节点状态设置为“不可用”之后，任务无法计划到节点中，但节点仍会产生费用。

节点处于“不可用”状态，但没有 [errors](/rest/api/batchservice/computenode/get#computenodeerror)，这表示 Batch 无法与 VM 进行通信。 在这种情况下，Batch 始终会尝试恢复 VM。 Batch 不会自动尝试恢复无法安装应用程序包或容器的 VM，即使其状态为“不可用”。

如果 Batch 可以确定原因，则节点 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告该原因。

“不可用”节点的其他原因示例：

- 自定义 VM 映像无效。 例如，未正确准备映像。

- 由于基础结构故障或低级别的升级，移动了 VM。 Batch 会恢复节点。

- 将 VM 映像部署到了不支持它的硬件上。 例如，尝试在 [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM 上运行 CentOS HPC 映像。

- VM 处于 [Azure 虚拟网络](batch-virtual-network.md)中，而流量已被阻止进入主要端口。

- VM 处于虚拟网络中，但到 Azure 存储的出站流量已被阻止。

- VM 处于具有客户 DNS 配置的虚拟网络中，DNS 服务器无法解析 Azure 存储。

### <a name="node-agent-log-files"></a>节点代理日志文件

每个池节点上运行的 Batch 代理进程可以提供日志文件，如需就池节点问题联系支持部门，这些文件可能会有所帮助。 可以通过 Azure 门户、Batch Explorer 或 [API](/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。 它可用于上传和保存日志文件。 之后，可以删除节点或池以节省运行节点的费用。

### <a name="node-disk-full"></a>节点磁盘已满

Batch 将池节点 VM 的临时驱动器用于存储作业文件、任务文件和共享文件。

- 应用程序包文件
- 任务资源文件
- 下载到了某个 Batch 文件夹的特定于应用程序的文件
- 每次执行任务应用程序所用的 Stdout 和 stderr 文件
- 特定于应用程序的输出文件

其中一些文件仅在创建池节点时写入一次，如池应用程序包或池开始任务资源文件。 即使只在创建节点时写入一次，但如果这些文件太大，它们也可能将临时驱动器填充满。

针对在节点上运行的每个任务，会写出其他文件，如 stdout 和 stderr。 如果在同一节点上运行大量任务，并且/或者任务文件太大，则它们可能会将临时驱动器填充满。

临时驱动器的大小取决于 VM 大小。 选择 VM 大小时需考虑的一个因素是，确保临时驱动器具有足够的空间。

- 在 Azure 门户中添加池时，可以查看 VM 大小的完整列表，其中有一个“资源磁盘大小”列。
- 介绍各种 VM 大小的文章都有包含“临时存储”列的表：例如[计算优化的 VM 大小](../virtual-machines/sizes-compute.md)

针对每个任务写出的文件，可以针对每个任务指定保留期，确定在自动清理这些任务文件之前将其保留多长时间。 保留期可以缩短，以降低存储要求。


如果临时磁盘空间不足（或空间即将不足），节点将转到[不可用](/rest/api/batchservice/computenode/get#computenodestate)状态，并将报告一个节点错误，指出磁盘已满。

### <a name="what-to-do-when-a-disk-is-full"></a>磁盘已满时要执行的操作

确定磁盘已满的原因：如果你不确定哪些内容占用了节点的空间，建议远程进入节点并手动调查空间占用情况。 此外，也可以使用 [ 列表文件 API](/rest/api/batchservice/file/listfromcomputenode) 检查 Batch 托管文件夹中的文件（如任务输出）。 请注意，此 API 只列出 Batch 托管目录中的文件，如果任务在其他位置创建了文件，此 API 无法显示这些文件。

请确保已从节点检索到所需的任何数据，或已将其上传到持久存储。 解决磁盘已满问题的所有缓解措施都涉及到删除数据来释放空间。

### <a name="recovering-the-node"></a>恢复节点

1. 如果池是 [C.loudServiceConfiguration](/rest/api/batchservice/pool/add#cloudserviceconfiguration) 池，则可以通过 [Batch 重置映像 API](/rest/api/batchservice/computenode/reimage) 来重置节点的映像。这会清理整个磁盘。 [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration) 池目前不支持重置映像。

2. 如果池是 [VirtualMachineConfiguration](/rest/api/batchservice/pool/add#virtualmachineconfiguration)，则可以使用[删除节点 API](/rest/api/batchservice/pool/removenodes)，从池中删除节点。 然后，你可以再次扩展池，使用新节点替换有问题的节点。

3.  删除已完成的旧作业或其任务数据仍位于节点上的已完成的旧任务。 可以查看节点上的 [RecentTasks 集合](/rest/api/batchservice/computenode/get#taskinformation)，也可以查看[节点上的文件](/rest/api/batchservice/file/listfromcomputenode)，以了解哪些作业/任务数据在节点上。 删除作业时，将会删除作业中的所有任务，而且删除作业中的任务都会触发删除节点上的任务目录中的数据，从而释放空间。 释放足够的空间后，重新启动该节点，它应该会从“不可用”状态重新进入“空闲”状态。

## <a name="next-steps"></a>后续步骤

请检查是否将应用程序设置为实施全面错误检查，尤其是异步操作。 它对于及时检测和诊断问题至关重要。
