---
title: 成本分析和预算
description: 了解如何进行成本分析，并为用于运行 Batch 工作负荷的基础计算资源和软件许可证设置预算。
ms.topic: how-to
ms.date: 07/19/2019
ms.openlocfilehash: 50ca1ecfd0a973ff39dabfcb62175ce820d0a0d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "88654236"
---
# <a name="cost-analysis-and-budgets-for-azure-batch"></a>Azure Batch 成本分析和预算

Azure Batch 本身是免费的，只有用于运行 Batch 工作负荷的基础计算资源和软件许可证是收费的。 概括来讲，成本来自池中的虚拟机 (VM)、从 VM 进行的数据传输或在云中存储的任何输入或输出数据。 接下来将介绍 Batch 的一些关键组件，以了解成本从何而来、如何为池或帐户设置预算，以及让 Batch 工作负荷更经济高效的一些技术。

## <a name="batch-resources"></a>Batch 资源

虚拟机是用于 Batch 处理的最重要资源。 Batch 的 VM 使用成本是根据类型、数量和使用持续时间计算的。 VM 计费选项包括[即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/)或[预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)（提前支付）。 这两种支付选项的优势因计算工作负荷而异，而且这两种付款模型对帐单的影响也不同。

使用[应用包](batch-application-packages.md)将应用部署到 Batch 节点 (VM) 时，还需要为应用包使用的 Azure 存储资源付费。 你还要为任何输入或输出文件（如资源文件和其他日志数据）的存储支付费用。 通常，与 Batch 关联的存储数据的成本要比计算资源的成本低得多。 池中使用 VirtualMachineConfiguration 创建的每个 VM 都有一个使用 Azure 托管磁盘的关联 OS 磁盘。 Azure 托管磁盘有额外的成本，其他磁盘性能层也有不同的成本。

Batch 池使用网络资源。 具体而言，对于 VirtualMachineConfiguration 池，使用了需要静态 IP 地址的标准负载均衡器。 Batch 使用的负载均衡器对“用户订阅”帐户可见，但对“Batch 服务”帐户不可见。 传入和传出 Batch 池 VM 的所有数据都会让标准负载均衡器产生费用；选择从池节点检索数据的 Batch API（如“获取任务/节点文件”）、任务应用包、资源/输出文件和容器映像会产生费用。

### <a name="additional-services"></a>其他服务

在计算 Batch 帐户的成本时，可能需要考虑不包括 VM 和存储的服务这一因素。

通常用于 Batch 的其他服务可能包括：

- Application Insights
- 数据工厂
- Azure Monitor
- 虚拟网络
- 带有图形应用的 VM

根据要将哪些服务用于 Batch 解决方案，可能会产生额外的费用。 若要确定每个附加服务的成本，请参阅[定价计算器](https://azure.microsoft.com/pricing/calculator/)。

## <a name="cost-analysis-and-budget-for-a-pool"></a>池的成本分析和预算

通过 Azure 门户，可以为一个或多个 Batch 池或 Batch 帐户创建预算和支出警报。 预算和警报可用于通知利益干系人存在任何超支风险。 支出警报可能会有延迟，导致稍微超出预算。 在此示例中，我们将查看各个 Batch 池的成本分析。

1. 在 Azure 门户的左侧导航栏中，选择“成本管理 + 计费”。
1. 在“我的订阅”部分中，选择你的订阅
1. 转到左侧导航栏的“成本管理”部分下的“成本分析”，此时会看到如下视图：
1. 选择“添加筛选器”。 在第一个下拉列表中，选择“资源” ![选择“资源”筛选器](./media/batch-budget/resource-filter.png)
1. 在第二个下拉列表中，选择 Batch 池。 在选择池后，就会看到类似于下面的成本分析。
    ![池的成本分析](./media/batch-budget/pool-cost-analysis.png)

生成的成本分析显示了池的成本，以及此成本来自哪些资源。 在此示例中，池中使用的 VM 是成本最高的资源。

若要为池创建预算，请依次选择“预算:无”和“新建预算 >”。 现在，使用此窗口来为池专门配置预算。

若要详细了解如何配置预算，请参阅[创建和管理 Azure 预算](../cost-management-billing/costs/tutorial-acm-create-budgets.md)。

> [!NOTE]
> Azure Batch 构建在 Azure 云服务和 Azure 虚拟机技术基础之上。 如果选择“云服务配置”，将根据云服务定价结构付费。 如果选择“虚拟机配置”，将根据虚拟机定价结构付费。 此页上的示例使用“虚拟机配置”。

## <a name="minimize-cost"></a>最大限度地降低成本

长时间使用多个 VM 和 Azure 服务的成本可能会很高。 幸运的是，有一些服务可以帮助你减少开支，也有一些策略可以帮助你最大限度地提升工作负荷的效率。

### <a name="low-priority-virtual-machines"></a>低优先级虚拟机

低优先级 VM 通过利用 Azure 中的盈余计算容量来降低 Batch 工作负荷的成本。 当你在池中指定低优先级 VM 时，Batch 使用此盈余来运行工作负荷。 使用低优先级 VM 来代替专用 VM 可以节省大量成本。

若要详细了解如何为工作负荷设置低优先级 VM，请参阅[对 Batch 使用低优先级 VM](batch-low-pri-vms.md)。

### <a name="virtual-machine-os-disk-type"></a>虚拟机 OS 磁盘类型

有多种 [VM OS 磁盘类型](../virtual-machines/disks-types.md)。 大多数 VM 系列的大小都同时支持高级存储和标准存储。 如果你为池选择 VM 大小“S”，Batch 会配置高级 SSD OS 磁盘。 如果选择的是 VM 大小“非 S”，则会使用更便宜的标准 HDD 磁盘类型。 例如，高级 SSD OS 磁盘用于 `Standard_D2s_v3`，标准 HDD OS 磁盘用于 `Standard_D2_v3`。

高级 SSD OS 磁盘更昂贵，但具有更高的性能，并且包含高级磁盘的 VM 的启动速度比包含标准 HDD OS 磁盘的 VM 稍微快一些。 使用 Batch 时，通常不会经常使用 OS 磁盘，因为应用和任务文件位于 VM 临时 SSD 盘中。 因此，在许多情况下，对于在指定了 VM 大小“S”时预配的高级 SSD，无需支付增加的成本。

### <a name="reserved-virtual-machine-instances"></a>虚拟机预留实例

若要长时间使用 Batch，可以通过对工作负荷使用 [Azure 预留](../cost-management-billing/reservations/save-compute-costs-reservations.md)来节省 VM 成本。 预留费率比即用即付费率低得多。 所用不含预留的虚拟机实例按即用即付费率进行收费。 如果你购买了预留，就会应用预留折扣，不再按即用即付费率向你收费。

### <a name="automatic-scaling"></a>自动缩放

[自动缩放](batch-automatic-scaling.md)根据当前作业的需求来动态缩放 Batch 池中的 VM 数。 通过根据作业生存期来缩放池，自动缩放可确保 VM 仅在有作业需要执行时才进行纵向扩展和使用。 当作业完成或没有作业时，VM 就会自动纵向缩减来节省计算资源。 借助缩放，可以只使用所需的资源，从而降低 Batch 解决方案的总体成本。

若要详细了解自动缩放，请参阅[自动缩放 Azure Batch 池中的计算节点](batch-automatic-scaling.md)。

## <a name="next-steps"></a>后续步骤

- 详细了解可用于生成和监视 Batch 解决方案的 [Batch API 和工具](batch-apis-tools.md)。  

- 了解如何[对 Batch 使用低优先级 VM](batch-low-pri-vms.md)。
