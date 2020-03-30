---
title: 成本分析和预算 - Azure Batch
description: 了解如何进行成本分析，并为用于运行 Batch 工作负荷的基础计算资源和软件许可证设置预算。
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "77022709"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 的成本分析和预算

Azure Batch 本身不会产生任何费用，而只有用于运行 Batch 工作负荷的底层计算资源和软件授权才产生费用。 从较高层面讲，成本是由池中的虚拟机 (VM)、从 VM 进行的数据传输，或云中存储的任何输入或输出数据产生的。 让我们看一下 Batch 的一些关键组件，以了解成本的起源、如何针对池或帐户设置预算，以及使 Batch 工作负荷更具成本效益的一些方法。

## <a name="batch-resources"></a>Batch 资源

虚拟机是用于 Batch 处理的最重要资源。 Batch 中的 VM 使用成本是根据类型、数量和使用持续时间计算的。 VM 计费选项包括即[用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[预订](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前付款）。 这两个支付选项根据计算工作负荷而各有优势，这两种支付模式将对帐单产生不同的影响。

如果使用[应用程序包](batch-application-packages.md)将应用程序部署到 Batch 节点 (VM)，系统还会对应用程序包使用的 Azure 存储资源收费。 你还要支付任何输入或输出文件（例如资源文件和其他日志数据）的存储费。 一般情况下，与 Batch 关联的存储数据成本远远低于计算资源的成本。 使用 **VirtualMachineConfiguration** 在池中创建的每个 VM 都有一个使用 Azure 托管磁盘的关联 OS 磁盘。 Azure 托管磁盘会产生额外的成本，其他磁盘性能层也有不同的成本。

Batch 池使用网络资源。 具体而言，对于 **VirtualMachineConfiguration** 池，将使用标准负载均衡器，这需要配置静态 IP 地址。 Batch 使用的负载均衡器对**用户订阅**帐户可见，但对 **Batch 服务**帐户不可见。 传入和传出 Batch 池 VM 的所有数据会产生标准负载均衡器费用；从池节点检索数据的某些 Batch API（例如“获取任务/节点文件”）、任务应用程序包、资源/输出文件和容器映像会产生费用。

### <a name="additional-services"></a>其他服务

不包含 VM 和存储的服务可能会影响 Batch 帐户的成本。

经常与 Batch 配合使用的其他服务可能包括：

- Application Insights
- 数据工厂
- Azure Monitor
- 虚拟网络
- 包含图形应用程序的 VM

根据在 Batch 解决方案中使用的服务，可能会产生额外的费用。 请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)来确定每个附加服务的成本。

## <a name="cost-analysis-and-budget-for-a-pool"></a>池的成本分析和预算

通过 Azure 门户可以针对 Batch 池或 Batch 帐户创建预算和支出警报。 存在任何透支风险时，预算和警报可用于通知利益干系人。 支出警报可能会有所延迟，并可能存在略微超出预算的情况。 在本示例中，我们将查看单个 Batch 池的成本分析。

1. 在 Azure 门户的左侧导航栏中，选择“成本管理 + 计费”。****
1. 在“我的订阅”部分选择你的订阅。****
1. 转到左侧导航栏中“成本管理”部分下的“成本分析”，此时会显示如下所示的视图：********
1. 选择“添加筛选器”。**** 在第一个下拉列表中，选择 **"资源**![选择资源筛选器"](./media/batch-budget/resource-filter.png)
1. 在第二个下拉列表中选择 Batch 池。 选择池后，成本分析将类似于以下分析。
    ![池的成本分析](./media/batch-budget/pool-cost-analysis.png)

生成的成本分析将显示此成本涉及的池和资源的成本。 在本示例中，池中使用的 VM 是成本最高的资源。

若要为池创建预算，请依次选择“预算: 无”、“创建新预算 >”。******** 现在，使用窗口专门为池配置预算。

有关配置预算的详细信息，请参阅[创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 构建在 Azure 云服务和 Azure 虚拟机技术基础之上。 如果选择“云服务配置”****，系统会根据云服务定价结构收费。 如果选择“虚拟机配置”****，系统会根据虚拟机定价结构收费。 此页上的示例使用“虚拟机配置”。****

## <a name="minimize-cost"></a>尽量降低成本

长时间使用多个 VM 和 Azure 服务可能会产生很高的成本。 幸运的是，可以借助某些服务来降低支出，并可以使用某些策略来最大程度地提高工作负荷的效率。

### <a name="low-priority-virtual-machines"></a>低优先级虚拟机

低优先级 VM 利用 Azure 中的盈余计算容量来降低 Batch 工作负荷的成本。 在池中指定低优先级 VM 时，Batch 将使用此盈余容量来运行工作负荷。 使用低优先级 VM 代替专用 VM 可以大幅节省成本。

[在 Batch 中使用低优先级 VM](batch-low-pri-vms.md) 详细介绍了如何为工作负荷设置低优先级 VM。

### <a name="virtual-machine-os-disk-type"></a>虚拟机 OS 磁盘类型

有多种 [VM OS 磁盘类型](../virtual-machines/windows/disks-types.md)。 大多数 VM 系列大小都支持高级和标准存储。 为池选择“s”VM 大小时，Batch 将配置高级 SSD OS 磁盘。 选择“非 s”VM 大小时，将使用更经济的标准 HDD 磁盘类型。 例如，将高级 SSD OS 磁盘用于 `Standard_D2s_v3`，将标准 HDD OS 磁盘用于 `Standard_D2_v3`。

高级 SSD OS 磁盘的成本更高，但性能更高；使用高级磁盘的 VM 的启动速度比使用标准 HDD OS 磁盘的 VM 略快一些。 在 Batch 中，OS 磁盘通常不会频繁使用，因为应用程序和任务文件位于 VM 的临时 SSD 磁盘中。 因此，在许多情况下，如果指定了“s”VM 大小，则无需为预配的高级 SSD 支付更高的成本。

### <a name="reserved-virtual-machine-instances"></a>虚拟机预留实例

如果打算长时间使用 Batch，则可以通过使用[Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)来节省 VM 的成本。 预留费率明显低于即用即付费率。 在不预留的情况下使用虚拟机实例将按即用即付费率收费。 如果你购买了预留实例，则会应用预留折扣，你不再需要按即用即付费率付费。

### <a name="automatic-scaling"></a>自动缩放

[自动缩放](batch-automatic-scaling.md)根据当前作业的需求动态缩放 Batch 池中的 VM 数量。 自动缩放根据作业的生存期缩放池，确保仅在需要执行某个作业时，才扩展和使用 VM。 如果该作业已完成或者没有任何作业，则 VM 会自动缩减以节省计算资源。 缩放可让你仅使用所需的资源，从而降低 Batch 解决方案的总体成本。

有关自动缩放的详细信息，请参阅[自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解可用于构建及监视 Batch 解决方案的 [Batch API 和工具](batch-apis-tools.md)。  

- 了解 [Batch 中的低优先级 VM](batch-low-pri-vms.md)。
