---
title: 检查批处理池和节点错误
description: 创建池和节点时要检查的错误以及如何避免错误
services: batch
author: mscurrell
ms.author: markscu
ms.date: 9/25/2018
ms.topic: conceptual
ms.openlocfilehash: bc09089fa9984e9042166938da19499afca21509
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/28/2018
ms.locfileid: "47435816"
---
# <a name="checking-for-pool-and-node-errors"></a>检查池和节点错误

创建和管理批处理池时，会立即执行一些操作，而非即时异步操作将在后台运行，可能需要几分钟才能完成。

检测即时发生的失败操作非常简单，因为将立即通过 API、CLI 或 UI 返回任何错误。

本文介绍可在池和池节点发生的后台操作 - 它指定如何检测故障，以及如何避免故障。

## <a name="pool-errors"></a>池错误

### <a name="resize-timeout-or-failure"></a>调整大小超时或失败

创建新池或调整现有池大小时，将指定目标节点数。  该操作将立即完成，但新节点的实际分配或删除现有节点将在后台进行，可能需要几分钟的时间。  调整大小超时在[创建](https://docs.microsoft.com/rest/api/batchservice/pool/add)或[调整](https://docs.microsoft.com/rest/api/batchservice/pool/resize) API 大小中指定 - 如果不能在调整大小超时时间段内获得目标节点数，该操作将停止，同时池会进入稳定状态并附带调整大小错误。

调整大小超时将由 [ResizeError](https://docs.microsoft.com/rest/api/batchservice/pool/get#resizeerror) 属性报告，以进行最后一次评估，其中列出了出现的一个或多个错误。

调整大小超时的常见原因包括：
- 调整大小超时过短
  - 默认超时为 15 分钟，通常对于分配或删除池节点已足够。
  - 当在池创建或调整大小过程中分配大量节点（市场映像中超过 1000 个节点，或自定义映像中超过300 个节点），建议使用 30 分钟的超时。
- 核心配额不足
  - 批处理帐户具有跨所有池分配的核心数配额。  一旦达到该配额，批处理将不会分配节点。  [可以增加](https://docs.microsoft.com/azure/batch/batch-quota-limit)核心配额以启用更多要分配的节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，子网 IP 不足
  - 虚拟网络子网必须具有足够的未分配 IP 地址以分配到请求的所有池节点，否则无法创建节点。
- 当[池处于虚拟网络](https://docs.microsoft.com/azure/batch/batch-virtual-network)，资源不足
  - 诸如负载均衡器、公共 IP 和 NSG 之类的资源在用于创建批处理帐户的订阅中创建。  这些资源的订阅配额必须足够。
- 对大型池使用自定义 VM 映像
  - 使用自定义映像的大型池可能需要更长的时间进行分配，并且可能发生调整大小超时。  在一篇[特定文章](https://docs.microsoft.com/azure/batch/batch-custom-images)中提供了限制和配置建议。 

### <a name="auto-scale-failures"></a>自动缩放失败

无需在池创建或调整大小中显式设置池的目标节点数，可以自动缩放池中的节点数。  [可为池创建自动缩放公式](https://docs.microsoft.com/azure/batch/batch-automatic-scaling)，将在一个常规的可配置时间间隔内进行评估，以设置池的目标节点数。  可能会发生并检测到以下类型问题：

- 自动缩放评估可能失败。
- 生成的调整大小操作可能会失败和超时。
- 自动缩放公式可能会出现问题，从而导致节点目标值不正确，同时出现调整大小工作或超时。

可以使用 [autoScaleRun](https://docs.microsoft.com/rest/api/batchservice/pool/get#autoscalerun) 属性获取有关最后一次自动缩放评估的信息，该属性报告评估时间、评估值和结果，以及执行评估的任何错误。

所有评估的相关信息将由[池调整大小完成事件](https://docs.microsoft.com/azure/batch/batch-pool-resize-complete-event)自动捕获。

### <a name="delete"></a>删除

假设池中有节点，则池删除操作会导致先删除节点，然后再删除池对象本身。  可能需要等待数分钟才会删除池节点。

[池状态](https://docs.microsoft.com/rest/api/batchservice/pool/get#poolstate)将在删除过程中设置为“正在删除”。  调用应用程序可以通过使用“state”和“stateTransitionTime”属性来检测池删除时间是否过长。

## <a name="pool-compute-node-errors"></a>池计算节点错误

可以在一个池中成功分配节点，但各种问题可以会导致节点运行不正常且无法使用。  在池中分配节点后，它们会产生费用，因此务必要检测问题，以避免支付不能使用的节点。

### <a name="start-task-failure"></a>开始任务失败

可为池指定一个可选的[开始任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)。  与任何任务一样，可以指定从存储器中下载命令行和资源文件。  开始任务是为池指定的，但在每个节点上运行，一旦启动每个节点，开始任务将运行。  [开始任务](https://docs.microsoft.com/rest/api/batchservice/pool/add#starttask)的另一个属性，“waitForSuccess”，指定批处理是否应等待开始任务成功完成，再将任何任务调度到节点。

如果开始任务失败，并且开始任务配置指定要等待任务成功完成，该节点将不可用，并仍会产生费用。

可以使用顶级 [startTaskInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#starttaskinformation) 节点属性的 [result](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskexecutionresult) 和 [failureInfo](https://docs.microsoft.com/rest/api/batchservice/computenode/get#taskfailureinformation) 属性来检测开始任务失败。

失败的开始任务还会导致节点[状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)设置为“starttaskfailed”，但仅限“waitForSuccess”设置为“true”的情况。

与任何任务一样，开始任务失败可能有许多原因。  若要进行故障排除，应检查 stdout、stderr 和任何其他特定于任务的日志文件。

### <a name="application-package-download-failure"></a>应用程序包下载失败

可以选择性地为一个池指定一个或多个应用程序包，其中指定的包文件被下载到每个节点，并在节点启动后但在调度任务前解压缩。  例如，通常将开始任务命令行与应用程序包结合使用，以便将文件复制到另一个位置或运行安装程序。

未能下载和解压缩应用程序包将由节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性报告。  节点状态将设置为“不可用”。

### <a name="node-in-unusable-state"></a>处于“不可用”状态的节点

由于许多原因，[节点状态](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodestate)可能设置为“不可用”。  当“不可用”时，无法向节点调度任务，但该节点仍将产生费用。

批处理将始终尝试恢复不可用节点，但恢复可能可行也可能不可行，具体取决于原因。

如果可以确定原因，它将由节点 [errors](https://docs.microsoft.com/rest/api/batchservice/computenode/get#computenodeerror) 属性报告。

“不可用”节点的一些其他原因示例：

- 例如，自定义映像无效；未正确准备。
- 基础结构故障或低级别升级导致基础 VM 移动；批处理将恢复节点。

### <a name="node-agent-log-files"></a>节点代理日志文件

如果需要就池节点问题联系支持人员，则可以获取在每个池节点上运行的批处理代理进程的日志文件。  可以通过 Azure 门户、Batch Explorer 或 [API](https://docs.microsoft.com/rest/api/batchservice/computenode/uploadbatchservicelogs) 上传某个节点的日志文件。  上传和保存日志文件可能非常有用，因为可以删除节点或池，以节省运行节点的成本。

## <a name="next-steps"></a>后续步骤

请确保应用程序已实现全面的错误检查，尤其是对于异步操作，以便可以快速检测和诊断问题。
