---
title: 成本分析和预算-Azure Batch
description: 了解如何获取成本分析并设置用于运行 Batch 工作负荷的基础计算资源和软件许可证的预算。
services: batch
author: LauraBrenner
manager: evansma
ms.service: batch
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 07/19/2019
ms.author: labrenne
ms.openlocfilehash: 819b5e16f4730e9a1998234288e181772f7c1996
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022709"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 的成本分析和预算

Azure Batch 本身不收取任何费用，只是用于运行批处理工作负荷的基础计算资源和软件许可证。 在高级别上，从池中的虚拟机（Vm）、从 VM 传输数据或存储在云中的任何输入或输出数据产生成本。 让我们看一下批处理的一些关键组件来了解成本来自何处、如何设置池或帐户的预算，以及一些使 Batch 工作负荷更经济高效的方法。

## <a name="batch-resources"></a>Batch 资源

虚拟机是用于批处理的最重要的资源。 使用批处理 Vm 的成本根据类型、数量和使用持续时间进行计算。 VM 计费选项包括即[用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[预订](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前付款）。 根据计算工作负荷，这两种支付方式的优点各不相同，并且这两种支付模型会以不同方式影响帐单。

当使用[应用程序包](batch-application-packages.md)将应用程序部署到 Batch 节点（vm）时，你的应用程序包所使用的 Azure 存储资源会收取费用。 你还将对任何输入或输出文件（如资源文件和其他日志数据）的存储进行计费。 通常，与批处理关联的存储数据成本远远低于计算资源的成本。 使用**VirtualMachineConfiguration**创建的池中的每个 VM 都有一个使用 Azure 托管磁盘的关联 OS 磁盘。 Azure 托管磁盘额外收费，其他磁盘性能层也有不同的成本。

Batch 池使用网络资源。 特别是，对于**VirtualMachineConfiguration**池，使用标准负载均衡器，这需要静态 IP 地址。 Batch 使用的负载均衡器对**用户订阅**帐户可见，但对于**batch 服务**帐户不可见。 标准负载均衡器会对传递给批处理池 Vm 的所有数据产生费用;选择用于从池节点（如 "获取任务/节点文件"）检索数据、任务应用程序包、资源/输出文件和容器映像将产生费用的批处理 Api。

### <a name="additional-services"></a>其他服务

不包括 Vm 和存储的服务可能会考虑批处理帐户的成本。

通常与 Batch 一起使用的其他服务包括：

- Application Insights
- 数据工厂
- Azure 监视器
- 虚拟网络
- 带有图形应用程序的 Vm

根据要用于 Batch 解决方案的服务，可能会产生额外的费用。 请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)来确定每个附加服务的成本。

## <a name="cost-analysis-and-budget-for-a-pool"></a>池的成本分析和预算

通过 Azure 门户，您可以为批处理池或批处理帐户创建预算和支出警报。 预算和警报可用于向利益干系人通知超支任何风险。 支出警报可能会有延迟，并且可能会略微超出预算。 在此示例中，我们将查看单个批处理池的成本分析。

1. 在 Azure 门户中，从左侧导航栏中选择 "**成本管理 + 计费**"。
1. 从 "**我的订阅**" 部分中选择你的订阅
1. 请参阅左侧导航栏的 "**成本管理**" 部分下的 "**成本分析**"，将显示如下所示的视图：
1. 选择 "**添加筛选器**"。 在第一个下拉箭头中，选择**资源**![选择资源筛选器](./media/batch-budget/resource-filter.png)
1. 在第二个下拉箭头中，选择批处理池。 选择该池后，成本分析将类似于以下分析。
    ![池的成本分析](./media/batch-budget/pool-cost-analysis.png)

生成的成本分析显示了池的成本以及导致此成本的资源。 在此示例中，池中使用的 Vm 是成本最高的资源。

若要为池创建预算，请选择 "**预算：无**"，然后选择 "**创建新的预算 >** 。 现在，使用窗口来配置专用于池的预算。

有关配置预算的详细信息，请参阅[创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 构建在 Azure 云服务和 Azure 虚拟机技术基础之上。 选择 "**云服务配置**" 时，会根据云服务定价结构收费。 选择 "**虚拟机配置**" 时，将根据虚拟机定价结构收费。 此页上的示例使用**虚拟机配置**。

## <a name="minimize-cost"></a>降低成本

在很长一段时间内使用多个 Vm 和 Azure 服务可能成本高昂。 幸运的是，有一些服务可帮助减少您的支出，并提供最大程度地提高工作负荷效率的策略。

### <a name="low-priority-virtual-machines"></a>低优先级虚拟机

低优先级 Vm 利用 Azure 中的过剩计算能力，降低 Batch 工作负荷的成本。 当你在池中指定低优先级 Vm 时，Batch 将使用此过剩来运行工作负荷。 使用低优先级 Vm 代替专用 Vm，可节省大量成本。

若要详细了解如何为工作负荷设置低优先级 Vm，请参阅在[Batch 中使用低优先级 vm](batch-low-pri-vms.md)。

### <a name="virtual-machine-os-disk-type"></a>虚拟机 OS 磁盘类型

有多个[VM 操作系统磁盘类型](../virtual-machines/windows/disks-types.md)。 大多数 VM 系列的大小都支持高级和标准存储。 为池选择 "VM 大小" 时，Batch 将配置高级 SSD OS 磁盘。 选择 "非 s" VM 大小后，将使用更便宜的标准 HDD 磁盘类型。 例如，高级 SSD OS 磁盘用于 `Standard_D2s_v3`，标准 HDD OS 磁盘用于 `Standard_D2_v3`。

高级 SSD 操作系统磁盘的开销更高，但性能更高，具有高级磁盘的 Vm 的启动速度要快于带有标准 HDD OS 磁盘的 Vm。 使用 Batch 时，操作系统磁盘通常不会像应用程序和任务文件位于 Vm 临时 SSD 磁盘上那样使用。 因此，在许多情况下，如果指定了 "VM 大小"，则无需为预配的高级 SSD 支付增加成本。

### <a name="reserved-virtual-machine-instances"></a>保留虚拟机实例

如果你想要使用批处理长时间，则可以使用[Azure 保留空间](../cost-management-billing/reservations/save-compute-costs-reservations.md)来节省 vm 的成本。 预订费率明显低于即用即付费率。 不使用保留的虚拟机实例按现用现付费率进行收费。 如果你购买了预订，则会应用预订折扣，你不再按现用现付费率对你收费。

### <a name="automatic-scaling"></a>自动缩放

[自动缩放](batch-automatic-scaling.md)基于当前作业的需求动态缩放 Batch 池中的 vm 数量。 通过根据作业的生存期扩展池，自动缩放可确保 Vm 仅在有作业时才会进行扩展和使用。 如果作业已完成，或者没有作业，Vm 将自动缩小以节省计算资源。 通过缩放，你只需使用所需的资源即可降低 Batch 解决方案的总体成本。

有关自动缩放的详细信息，请参阅[自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解可用于构建和监视批处理解决方案的[批处理 api 和工具](batch-apis-tools.md)。  

- 了解[带有批处理的低优先级 vm](batch-low-pri-vms.md)。
