---
title: 检查池和节点错误 - Azure Batch
description: 创建池和节点时要检查的错误以及如何避免错误
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: 4e1e645c25d2f1e49e222e39ecd719a414e1404e
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/27/2018
ms.locfileid: "53790463"
---
# <a name="check-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理 Azure Batch 池时，某些操作会立即发生。 但是，某些操作是异步的，并且在后台运行。 这些操作需要几分钟才能完成。

检测即时发生的失败操作非常简单，因为会立即通过 API、CLI 或 UI 返回任何错误。

本文介绍可能针对池和池节点执行的后台操作。 它指定检测和避免失败的方式。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或重设现有池大小时，将指定目标节点数。  操作会立即完成，但新节点的实际分配或现有节点的删除操作可能需要几分钟才能完成。  指定[创建](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[重设大小](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 中的重设大小超时。 如果 Batch 无法获取重设大小超时期间的目标节点数，则它会停止操作。 池会进入稳定状态并报告重设大小错误。

最新评估的 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 属性报告重设大小超时，并列出发生的任何错误。

调整大小超时的常见原因包括：

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

### <a name="delete"></a>删除

删除包含节点的池时，Batch 首先会删除节点。 然后再删除池对象。 可能需要等待数分钟才会删除池节点。

Batch 在删除过程中将[池状态](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)设置为“删除中”。 调用应用程序可以使用“state”和“stateTransitionTime”属性来检测池删除时间是否过长。

## <a name="pool-compute-node-errors"></a>池计算节点错误

即使当 Batch 成功分配池中的节点时，各种问题也可能导致某些节点运行状况不佳且不可用。 这些节点会产生费用。 请务必检测问题，以免为不可用的节点付费。

### <a name="start-task-failure"></a>开始任务失败

建议为池指定一个可选的[开始任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。 与任何任务一样，可以使用从存储器中下载的命令行和资源文件。 开启后，即可为每个节点运行开始任务。 waitForSuccess 属性指定 Batch 是否等待开始任务成功完成后才计划节点的任何任务。

如果已将节点配置为等待开始任务成功完成，但开始任务失败，该如何操作？ 在此情况下，节点不可用，但仍会产生费用。

可以使用顶级 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务是否失败。

如果将“waitForSuccess”设置为“true”，则失败的开始任务还会导致 Batch 将节点[状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为“starttaskfailed”。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，请检查 stdout、stderr 和任何其他特定于任务的日志文件。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以为池指定一个或多个应用程序包。 Batch 将指定的包文件下载到每个节点，并在节点开始之后、计划任务之前解压缩文件。 通常将开始任务命令行与应用程序包结合使用。 例如，将文件复制到其他文件或运行安装程序。

节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性报告未能下载和解压缩应用程序包。 Batch 将节点状态设置为“不可用”。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于多种原因，Azure Batch 可能将[节点状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为“不可用”。 将节点状态设置为“不可用”之后，任务无法计划到节点中，但节点仍会产生费用。

Batch 将始终尝试恢复不可用节点，但恢复可能可行也可能不可行，具体取决于何种原因。

如果 Batch 可以确定原因，则节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性会报告该原因。

“不可用”节点的其他原因示例：

- 自定义 VM 映像无效。 例如，未正确准备映像。
- 由于基础结构故障或低级别的升级，移动了 VM。 Batch 会恢复节点。

### <a name="node-agent-log-files"></a>节点代理日志文件

如需联系技术支持部门解决池节点问题，每个池节点上运行的 Batch 代理进程可以提供可能有帮助的日志文件。 可以通过 Azure 门户、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。 它可用于上传和保存日志文件。 之后，可以删除节点或池以节省运行节点的费用。

## <a name="next-steps"></a>后续步骤

请检查是否将应用程序设置为实施全面错误检查，尤其是异步操作。 它对于及时检测和诊断问题至关重要。
