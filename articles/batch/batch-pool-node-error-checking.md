---
title: 检查池和节点错误
description: 本文介绍在创建池和节点时可能发生的后台操作、要检查的错误以及如何避免这些错误。
author: mscurrell
ms.author: markscu
ms.date: 08/23/2019
ms.topic: conceptual
ms.openlocfilehash: 5051b9c536ded50e77fb75515c16daba884d5d24
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115901"
---
# <a name="check-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理 Azure Batch 池时，某些操作是即时发生的。 但是，某些操作是异步的且在后台运行，需要数分钟才能完成。

检测即时发生的失败操作非常简单，因为 API、CLI 或 UI 会立即返回任何错误。

本文介绍可对池和池节点执行的后台操作， 其中说明了如何检测和避免错误。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或调整现有池大小时，需指定目标节点数。  创建或重设大小操作是即时完成的，但实际分配新节点或删除现有节点可能需要几分钟时间。  在 [create](https://docs.microsoft.com/rest/api/batchservice/pool/add) 或 [resize](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中指定大小调整超时。 如果 Batch 在大小调整超时期限内无法获取目标节点数，池会进入稳定状态并报告重设大小错误。

最近执行的评估的 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 属性会列出发生的错误。

重设大小错误的常见原因包括：

- 调整大小超时过短
  - 在大多数情况下，默认超时 15 分钟已足够长，在此期限内可以完成池节点的分配或删除。
  - 若要分配大量的节点，我们建议将大小调整超时设置为 30 分钟。 例如，要在 Azure 市场映像中将大小调整为 1,000 个以上的节点，或者在自定义 VM 映像中将大小调整为 300 个以上的节点时。
- 核心配额不足
  - Batch 帐户在所有池中可分配的核心数有限制。 一旦达到该配额，Batch 就会停止分配节点。 [可以提高](https://docs.microsoft.com/azure/batch/batch-quota-limit)核心配额，使 Batch 能够分配更多的节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，子网 IP 不足
  - 虚拟网络子网必须能够提供可分配到每个请求的池节点的、尚未分配的足够 IP 地址。 否则无法创建节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，资源不足
  - 你可能会在 Batch 帐户所在的同一订阅中创建负载均衡器、公共 IP 和网络安全组等资源。 请检查这些资源的订阅配额是否足够。
- 使用自定义 VM 映像的大型池
  - 使用自定义 VM 映像的大型池可能需要更长的时间进行分配，并且可能发生大小调整超时。  有关限制和配置的建议，请参阅[使用共享映像库创建池](batch-sig-images.md)。

### <a name="automatic-scaling-failures"></a>自动缩放功能

也可以将 Azure Batch 设置为自动缩放池中的节点数。 定义[池自动缩放公式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)的参数。 Batch 服务使用该公式定期评估池中的节点数，并设置新的目标数。 可能会发生以下类型问题：

- 自动缩放评估失败。
- 生成的大小调整操作失败和超时。
- 自动缩放公式的问题导致节点目标值不正确。 大小调整操作要么正常运行，要么超时。

可以使用 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 属性获取有关上次自动缩放评估的信息。 此属性将报告评估时间、值和结果，以及任何性能错误。

[池大小调整完成事件](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)捕获有关所有评估的信息。

### <a name="delete"></a>Delete

当你删除包含节点的池时，Batch 首先会删除节点， 然后再删除池对象本身。 可能需要等待数分钟才会删除池节点。

在删除过程中，Batch 会将[池状态](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)设置为 **deleting**。 调用方应用程序可以使用 **state** 和 **stateTransitionTime** 属性来检测池删除时间是否过长。

## <a name="pool-compute-node-errors"></a>池计算节点错误

即使 Batch 在池中成功分配了节点，各种问题仍可能会导致某些节点不正常，无法运行任务。 这些节点仍会产生费用，因此务必要检测问题，避免不能使用的节点产生费用。 除了常见的节点错误外，知道当前[作业状态](/rest/api/batchservice/job/get#jobstate)对于故障排除非常有用。

### <a name="start-task-failures"></a>启动任务失败

建议为池指定一个可选的[开始任务](/rest/api/batchservice/pool/add#starttask)。 与任何任务一样，可以使用从存储器中下载的命令行和资源文件。 开启后，即可为每个节点运行开始任务。 waitForSuccess 属性指定 Batch 是否等待开始任务成功完成后才计划节点的任何任务****。

如果已将节点配置为等待开始任务成功完成，但开始任务失败，该如何操作？ 在这种情况下，该节点将不可用，但仍会产生费用。

可以使用顶级 [startTaskInfo](/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务是否失败。

如果已将 **waitForSuccess** 设置为 **true**，启动任务失败还会导致 Batch 将节点[状态](/rest/api/batchservice/computenode/get#computenodestate)设置为 **starttaskfailed**。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，请检查 stdout、stderr 和任何其他特定于任务的日志文件。

启动任务必须可重入，因为启动任务可能会在同一节点上多次运行；节点重置映像或重启时，启动任务就会运行。 在罕见情况下，启动任务会在某个事件导致节点重启后运行，此时某个操作系统或临时磁盘已重置映像，而另一个并没有这样做。 由于批处理启动任务（如所有批处理任务）是从临时磁盘运行的，因此通常不会出现问题，但在某些情况下，启动任务是将应用程序安装到操作系统磁盘并在临时磁盘上保留其他数据，这可能会导致问题，因为它不同步。如果同时使用这两个磁盘，请相应地保护应用程序。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以为池指定一个或多个应用程序包。 在启动节点之后，在对任务进行计划之前，Batch 会将指定的包文件下载到每个节点并解压缩这些文件。 通常将开始任务命令行与应用程序包结合使用。 例如，将文件复制到其他文件或运行安装程序。

节点 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告下载和解压缩应用程序包失败；节点状态会设置为 **unusable**。

### <a name="container-download-failure"></a>容器下载失败

可以在池上指定一个或多个容器引用。 Batch 可将指定的容器下载到每个节点。 节点的 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性报告容器下载失败，并将节点状态设置为“不可用”****。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于多种原因，Azure Batch 可能将[节点状态](/rest/api/batchservice/computenode/get#computenodestate)设置为“不可用”****。 将节点状态设置为“不可用”之后，任务无法计划到节点中，但节点仍会产生费用****。

节点处于“unusable”状态但**** 没有 [errors](/rest/api/batchservice/computenode/get#computenodeerror)，这意味着 Batch 无法与 VM 通信。 在这种情况下，Batch 始终尝试恢复 VM。 Batch 不会自动尝试恢复无法安装应用程序包或容器的 VM，即使这些 VM 的状态为“unusable”****。

如果 Batch 可以确定原因，则节点 [errors](/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告该原因。

“不可用”节点的其他原因示例****：

- 自定义 VM 映像无效。 例如，未正确准备映像。

- 由于基础结构故障或低级别的升级，移动了 VM。 Batch 会恢复节点。

- VM 映像已经部署在不受支持的硬件上。 例如，尝试在 [Standard_D1_v2](../virtual-machines/dv2-dsv2-series.md) VM 上运行 CentOS HPC 映像。

- VM 位于 [Azure 虚拟网络](batch-virtual-network.md)中，并且流量已被阻止，无法发送到关键端口。

- VM 位于虚拟网络中，但流向 Azure 存储的出站流量被阻止。

- VM 位于使用客户 DNS 配置的虚拟网络中，DNS 服务器不能解析 Azure 存储。

### <a name="node-agent-log-files"></a>节点代理日志文件

每个池节点上运行的 Batch 代理进程可以提供日志文件。如果你需要就池节点问题联系支持人员，这些日志文件可能很有帮助。 可以通过 Azure 门户、Batch Explorer 或 [API](/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。 它可用于上传和保存日志文件。 之后，可以删除节点或池以节省运行节点的费用。

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
- 描述所有 VM 大小的文章都有包含 "临时存储" 列的表;例如，[计算优化的 VM 大小](/azure/virtual-machines/windows/sizes-compute)

对于每个任务编写的文件，可以为每个任务指定保留时间，以确定任务文件在自动清理之前保留多长时间。 可以缩短保留时间以降低存储要求。


如果临时磁盘空间不足（或非常接近空间不足），则节点将进入 "[不可用](/rest/api/batchservice/computenode/get#computenodestate)" 状态，并将报告节点错误，指出磁盘已满。

### <a name="what-to-do-when-a-disk-is-full"></a>磁盘已满时要执行的操作

确定磁盘已满的原因：如果不能确定节点上占据空间的原因，则建议远程进入节点并在空间已消失的位置手动调查。 你还可以使用[批处理列表文件 API](https://docs.microsoft.com/rest/api/batchservice/file/listfromcomputenode)来检查批处理托管文件夹（例如，任务输出）中的文件。 请注意，此 API 只列出批处理托管目录中的文件，如果任务在其他位置创建文件，则不会看到这些文件。

请确保已从节点检索到所需的任何数据或将其上载到持久存储。 磁盘完全问题的所有缓解措施都涉及到删除数据以释放空间。

### <a name="recovering-the-node"></a>恢复节点

1. 如果池是[loudServiceConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#cloudserviceconfiguration)池，则可以通过[批处理重新映像 API](https://docs.microsoft.com/rest/api/batchservice/computenode/reimage)重新映像该节点。这会清理整个磁盘。 [VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)池目前不支持重映像。

2. 如果池是[VirtualMachineConfiguration](https://docs.microsoft.com/rest/api/batchservice/pool/add#virtualmachineconfiguration)，则可以使用 "[删除节点" API](https://docs.microsoft.com/rest/api/batchservice/pool/removenodes)从池中删除节点。 然后，你可以再次增加池以将错误节点替换为新节点。

3.  删除已完成的旧作业或任务数据仍位于节点上的旧已完成任务。 有关作业/任务数据位于节点上的提示，可以在节点上的[RecentTasks 集合](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskinformation)中查看，也可以在[节点上的文件](https://docs.microsoft.com//rest/api/batchservice/file/listfromcomputenode)中查看。 删除作业将删除作业中的所有任务，删除作业中的任务会触发要删除的节点上的任务目录中的数据，从而释放空间。 释放足够的空间后，重新启动该节点，并再次进入 "不可用" 状态并再次进入 "空闲" 状态。

## <a name="next-steps"></a>后续步骤

请检查是否将应用程序设置为实施全面错误检查，尤其是异步操作。 它对于及时检测和诊断问题至关重要。
