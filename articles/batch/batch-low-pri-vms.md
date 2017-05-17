---
title: "在经济高效的低优先级 VM 上运行 Azure Batch 工作负荷（预览版）| Microsoft Docs"
description: "了解如何预配低优先级 VM，以降低 Azure Batch 工作负载的成本。"
services: batch
author: mscurrell
manager: timlt
ms.assetid: dc6ba151-1718-468a-b455-2da549225ab2
ms.service: batch
ms.devlang: multiple
ms.topic: article
ms.workload: na
ms.date: 05/05/2017
ms.author: markscu
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 69e26eaf4aedaf17b58091420430ca996c01cf69
ms.contentlocale: zh-cn
ms.lasthandoff: 05/10/2017


---

# <a name="use-low-priority-vms-with-batch-preview"></a>使用 Batch 中的低优先级 VM（预览版）

Azure Batch 提供优先级别虚拟机 (VM) 来降低 Batch 工作负荷的成本。 低优先级 VM 提供大量的经济型计算资源，使新型 Batch 工作负荷成为可能。

低优先级 VM 利用 Azure 中多余的容量。 当你在池中指定低优先级 VM 时，Azure Batch 可以自动使用此多余容量（如果可用）。

使用低优先级 VM 的弊端是，当 Azure 中没有多余的容量时，这些 VM 可能会被取代。 出于此原因，低优先级 VM 最适合用于某些类型的工作负荷。 对于作业完成时间很灵活且工作分布在多个 VM 上的批处理和异步处理工作负荷，可以使用低优先级 VM。

低优先级 VM 的成本远远低于专用 VM。 有关价格详细信息，请参阅 [Batch 定价](https://azure.microsoft.com/pricing/details/batch/)。

> [!IMPORTANT]
> 低优先级 VM 目前以预览版提供，仅适用于在 Batch 中运行的工作负荷。 
>
>

## <a name="use-cases-for-low-priority-vms"></a>低优先级 VM 的用例

根据低优先级 VM 的特征，哪些工作负荷可以和不可以使用它们？ 一般情况下，处理工作负荷很适合使用低优先级 VM，因为作业已分解成多个并行任务，或者许多作业已横向扩展并分散在多个 VM 中。

-   为了最大程度地利用 Azure 中的多余容量，可以横向扩展适当的作业。

-   VM 有时可能不可用或者被取代，导致作业可用的容量减少，或者导致任务中断，需要重新运行。 因此，运行作业所要花费的时间必须灵活。

-   对于包含长时间运行的任务的作业，如果任务被中断，这些作业受到的影响可能更大。 如果长时间运行的任务实施检查点来保存其执行进度，则可以大大降低中断产生的影响。 执行时间较短的任务往往最适合在低优先级 VM 上运行，因为这样可以大大降低中断产生的影响。

-   利用多个 VM 的长时间运行的 MPI 作业不是很适合使用低优先级 VM，因为取代一个 VM 可能会导致需要重新运行整个作业。

适合使用低优先级 VM 的批处理用例示例包括：

-   **开发和测试**：具体而言，开发大规模解决方案时可以实现极大的节省。 所有类型的测试都可以受益，但大规模负载测试和回归测试可以获得极大的好处。

-   **补充按需容量**：低优先级 VM 可用于补充普通的专用 VM - 如果有可用的容量，则作业可以扩展，因而能够以更低的成本、更快的速度完成；如果没有可用的容量，可遵循专用 VM 的基准。

-   **灵活的作业执行时间**：如果作业必须完成的时间比较灵活，则可以容许容量的潜在下降；但是，增加的低优先级 VM 作业往往能够以更低的成本、更快的速度运行。

可以根据作业执行时间的灵活度，通过多种方式将 Batch 池配置为使用低优先级 VM。

-   可以专门在某个池中使用低优先级 VM，Batch 只需恢复任何被占用的容量（如果可用）。 这是执行作业的最节省方法，因为只会使用低优先级 VM。

-   低优先级 VM 可与具有固定基准的专用 VM 结合使用。 专用 VM 的固定数量确保始终能够提供一些容量来保持作业的持续运行。

-   可以动态混用专用 VM 和低先级 VM，以便在廉价的低优先级 VM 可用时专门使用此类 VM，并根据需要扩展昂贵的专用 VM，提供极少量的容量来保持作业的持续运行。

## <a name="batch-support-for-low-priority-vms"></a>低优先级 VM 的 Batch 支持

Azure Batch 提供多种功能来方便你使用低优先级 VM 并从中受益：

-   Batch 池可以包含专用 VM 和低优先级 VM。 创建池时可以指定每种类型的 VM 的数量，或者随时使用显式的大小调整操作或自动缩放来更改现有池的 VM 数量。 作业和任务提交内容可保持不变，不需要考虑到池中的 VM 类型。 此外，还可以让某个池完全使用低优先级 VM 来尽量以最低的成本运行作业，但在容量下降到低于最低阈值时，运转专用 VM 来保持作业的运行。

-   Batch 池会自动确定低优先级 VM 的目标数量。 如果 VM 被取代，Batch 将尝试替换丢失的容量并返回到目标。

-   如果任务被中断，Batch 可检测到这种状态，并自动将任务重新排队，以便重新运行。

> [!NOTE]
> 池分配模式设置为[用户订阅](batch-account-create-portal.md#user-subscription-mode)的 Batch 帐户目前不支持低优先级 VM。
>
>

## <a name="create-and-update-pools"></a>创建和更新池

Batch 池可以包含专用 VM 和低优先级 VM（也称为计算节点）。 可为专用 VM 和低优先级 VM 设置计算节点的目标数量。 节点的目标数量指定要在池中包含的 VM 数量。

例如，若要使用 Azure 云服务 VM 创建包含目标为 5 个专用 VM 和 20 个低优先级 VM 的池，请使用以下代码：

```csharp
CloudPool pool = batchClient.PoolOperations.CreatePool(
    poolId: "cspool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard_D2_v2",
    cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "4") // WS 2012 R2
);
```

若要使用 Azure 虚拟机（在本例中为 Linux VM）创建包含目标为 5 个专用 VM 和 20 个低优先级 VM 的池，请使用以下代码：

```csharp
ImageReference imageRef = new ImageReference(
    publisher: "Canonical",
    offer: "UbuntuServer",
    sku: "16.04.0-LTS",
    version: "latest");

// Create the pool
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration("batch.node.ubuntu 16.04", imageRef);

pool = batchClient.PoolOperations.CreatePool(
    poolId: "vmpool",
    targetDedicatedComputeNodes: 5,
    targetLowPriorityComputeNodes: 20,
    virtualMachineSize: "Standard\_D2\_v2",
    virtualMachineConfiguration: virtualMachineConfiguration);
```

可以获取专用 VM 和低优先级 VM 的当前节点数：

```csharp
int? numDedicated = pool1.CurrentDedicatedComputeNodes;
int? numLowPri = pool1.CurrentLowPriorityComputeNodes;
```

池节点提供一个属性用于指示节点是专用 VM 还是低优先级 VM：

```csharp
bool? isNodeDedicated = poolNode.IsDedicated;
```

当某个池中的一个或多个节点被取代时，对该池执行列出节点操作仍会返回这些节点，当前的低优先级节点数将保持不变，但这些节点的状态将设置为“已取代”。 Batch 会尝试查找替代 VM，如果成功，节点将依次经历“正在创建”和“正在启动”状态，然后可用于执行任务，就像新的节点一样。

## <a name="scale-a-pool-containing-low-priority-vms"></a>缩放包含低优先级 VM 的池

与仅包含专用 VM 的池一样，可以通过调用 Resize 方法或使用自动缩放，来缩放包含低优先级 VM 的池。

池调整大小操作采用可更新 **targetLowPriorityNodes** 值的另一个可选参数：

```csharp
pool.Resize(targetDedicatedComputeNodes: 0, targetLowPriorityComputeNodes: 25);
```

池自动缩放公式支持低优先级 VM，如下所示：

-   可以获取或设置服务定义的变量 **$TargetLowPriorityNodes** 的值。

-   可以获取服务定义的变量 **$CurrentLowPriorityNodes** 的值。

-   可以获取服务定义的变量 **$PreemptedNodeCount** 的值。 
    此变量返回处于已取代状态的节点的数量，并可让你根据不可用的已取代节点数增加或减少专用节点的数量。

## <a name="jobs-and-tasks"></a>作业和任务

作业和任务只需低优先级节点的极少量支持；唯一的支持如下：

-   作业的 JobManagerTask 属性包含新属性 **AllowLowPriorityNode**。 
    如果此属性为 true，则可以在专用或低优先级节点上计划作业管理器任务。 如果此属性为 false，则只会在专用节点上计划作业管理器任务。

-   可对任务应用程序使用一个[环境变量](batch-compute-node-environment-variables.md)，使该应用程序能够确定它是在低优先级节点还是专用节点上运行。 该环境变量为 AZ_BATCH_NODE_IS_DEDICATED。

## <a name="handling-preemption"></a>处理取代

VM 有时会被取代；如果发生这种情况，Batch 将执行以下操作：

-   将已取代的 VM 的状态更新为“已取代”。
-   如果已取代的节点 VM 上有运行中的任务，这些任务将重新排队并重新运行。
-   VM 被有效删除，导致 VM 本地存储的所有数据丢失。
-   池将不断地尝试用完低优先级节点的可用目标数量。 如果找到替代容量，节点将保留其 ID 但会被重新初始化，依次经历“正在创建”和“正在启动”状态，然后供任务计划使用。
-   Azure 门户将以指标形式提供取代计数。

## <a name="next-steps"></a>后续步骤

* 对于准备使用 Batch 的任何人，有必要阅读 [面向开发人员的 Batch 功能概述](batch-api-basics.md)了解基本信息。 本文中包含有关 Batch 服务资源（如池、节点、作业和任务）以及生成你的 Batch 应用程序时可以使用的许多 API 功能的更多详细信息。
* 了解适用于生成批处理解决方案的[批处理 API 和工具](batch-apis-tools.md)。

