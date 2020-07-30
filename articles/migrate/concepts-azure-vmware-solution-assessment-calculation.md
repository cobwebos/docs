---
title: Azure Migrate 中的 AVS 评估计算 |Microsoft Docs
description: 提供 Azure Migrate 服务中的 AVS 评估计算的概述。
author: rashi-ms
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 06/25/2020
ms.author: mahain
ms.openlocfilehash: 84798dbcd158b62ce6714bf73494a9e85bf932a8
ms.sourcegitcommit: 5b8fb60a5ded05c5b7281094d18cf8ae15cb1d55
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/29/2020
ms.locfileid: "87387942"
---
# <a name="avs-assessments-in-azure-migrate-server-assessment"></a>Azure Migrate 中的 AVS 评估：服务器评估

[Azure Migrate](migrate-services-overview.md)提供了一个中心中心来跟踪你的本地应用和工作负荷的发现、评估和迁移。 它还跟踪 Azure 的私有和公有云实例。 该中心提供用于评估和迁移的 Azure Migrate 工具，以及第三方独立软件供应商（ISV）产品。

服务器评估是 Azure Migrate 中的一种工具，用于评估要迁移到 Azure IaaS 虚拟机和 Azure VMware 解决方案（AVS）的本地服务器。 本文提供了有关如何计算 Azure VMware 解决方案（AVS）评估的信息。

> [!NOTE]
> Azure VMware 解决方案（AVS）评估目前处于预览阶段，只能为 VMware Vm 创建。

## <a name="types-of-assessments"></a>评估类型

使用服务器评估创建的评估是数据的时间点快照。 使用“Azure Migrate: 服务器评估”可以运行两种类型的评估。

**评估类型** | **详细信息**
--- | --- 
**Azure VM** | 将本地服务器迁移到 Azure 虚拟机的评估。 <br/><br/> 可以使用此评估类型评估本地[VMware vm](how-to-set-up-appliance-vmware.md)、 [hyper-v vm](how-to-set-up-appliance-hyper-v.md)和[物理服务器](how-to-set-up-appliance-physical.md)，以便迁移到 Azure。[了解更多](concepts-assessment-calculation.md)
**Azure VMware 解决方案 (AVS)** | 将本地服务器迁移到 [Azure VMware 解决方案 (AVS)](../azure-vmware/introduction.md) 的评估。 <br/><br/> 可使用此评估类型评估要迁移到 Azure VMware 解决方案 (AVS) 的本地 [VMware VM](how-to-set-up-appliance-vmware.md)。[了解详细信息](concepts-azure-vmware-solution-assessment-calculation.md)

服务器评估中的 Azure VMware 解决方案（AVS）评估提供两个大小调整条件选项：

**评估** | **详细信息** | **数据**
--- | --- | ---
**基于性能** | 基于本地 Vm 的收集的性能数据进行评估。 | **推荐的节点大小**：基于 CPU 和内存使用率数据，以及你为评估选择的节点类型、存储类型和 FTT 设置。
**本地** | 基于本地大小的评估。 | **推荐的节点大小**：根据本地 VM 大小以及你为评估选择的节点类型、存储类型和 FTT 设置。

## <a name="how-do-i-run-an-assessment"></a>如何实现运行评估？

可以通过多种方式来运行评估。

- 使用轻型 Azure Migrate 设备收集的服务器元数据来评估计算机。 设备会发现本地计算机。 然后，它将计算机元数据和性能数据发送到 Azure Migrate。
- 使用以逗号分隔值（CSV）格式导入的服务器元数据来评估计算机。

## <a name="how-do-i-assess-with-the-appliance"></a>如何实现与设备进行评估？

如果要部署 Azure Migrate 设备来发现本地服务器，请执行以下步骤：

1. 将 Azure 和本地环境设置为使用服务器评估。
2. 对于第一个评估，请创建一个 Azure 项目并向其添加服务器评估工具。
3. 部署轻型 Azure Migrate 设备。 设备持续发现本地计算机，并将计算机元数据和性能数据发送到 Azure Migrate。 将设备部署为 VM。 无需在要评估的计算机上安装任何内容。

设备开始发现计算机后，你可以将你想要评估的计算机收集到一个组中，并使用评估类型 " **Azure VMware 解决方案" （AVS）** 为该组运行评估。

按照[此处](how-to-create-azure-vmware-solution-assessment.md)的步骤创建第一个 Azure VMware 解决方案（AVS）评估。

## <a name="how-do-i-assess-with-imported-data"></a>如何实现评估导入的数据？

如果要使用 CSV 文件评估服务器，则无需设备。 请改为执行以下步骤：

1. 设置 Azure 以使用服务器评估。
2. 对于第一个评估，请创建一个 Azure 项目并向其添加服务器评估工具。
3. 下载 CSV 模板并向其中添加服务器数据。
4. 将模板导入到服务器评估中。
5. 发现通过导入添加的服务器，将其收集到一个组中，并为具有评估类型 " **Azure VMware 解决方案（AVS）**" 的组运行评估。

## <a name="what-data-does-the-appliance-collect"></a>设备会收集哪些数据？

如果使用 Azure Migrate 设备进行评估，请了解为[VMware](migrate-appliance.md#collected-data---vmware)收集的元数据和性能数据。

## <a name="how-does-the-appliance-calculate-performance-data"></a>设备如何计算性能数据？

如果使用设备进行发现，则它会使用以下步骤收集计算设置的性能数据：

1. 设备将收集实时示例点。

    - **VMware vm**：每隔20秒收集一个示例点。

2. 设备将示例点组合起来，每10分钟创建一个数据点。 为了创建数据点，设备从所有示例中选择最大值。 然后，它将数据点发送到 Azure。
3. 服务器评估存储上个月的10分钟数据点。
4. 创建评估时，服务器评估会确定要用于合理精简的相应数据点。 标识基于*性能历史记录*和*百分比利用率*的百分位值。

    - 例如，如果性能历史记录为一周，百分比利用率为95%，则服务器评估会对最后一周的10分钟示例点进行排序。 它将按升序排序，并选取合理精简的第95百分位值。
    - 95% 的值可以确保忽略任何离群值，如果选择了99% 百分点值，则可能会包含这些离群值。
    - 如果要选择该时间段的高峰使用量，而不想错过任何离群值，请选择99% 百分位以实现百分比利用率。

5. 此值与舒适因素相乘，以获取设备收集的这些指标的有效性能利用率数据：

    - CPU 使用率
    - RAM 利用率
    - 磁盘 IOPS （读取和写入）
    - 磁盘吞吐量（读取和写入）
    - 网络吞吐量（传入和传出）

收集以下性能数据，但不在 AVS 评估的大小建议中使用：
  - 附加到 VM 的每个磁盘的磁盘 IOPS 和吞吐量数据。
  - 为附加到 VM 的每个网络适配器处理基于性能的大小调整的网络 i/o。

## <a name="how-are-avs-assessments-calculated"></a>AVS 评估是如何计算的？

服务器评估使用本地计算机的元数据和性能数据来计算评估。 如果部署 Azure Migrate 设备，则评估将使用设备收集的数据。 但如果运行使用 CSV 文件导入的评估，则需要提供用于计算的元数据。

这三个阶段会发生计算：

1. **计算 Azure Vmware 解决方案（AVS）就绪**状态：本地 vm 是否适合迁移到 Azure vmware 解决方案（avs）。
2. **计算每个节点的 AVS 节点数和利用率**：在所有节点上运行 vm 所需的 avs 节点数以及预计的 CPU、内存和存储利用率。
3. **每月成本估计**：运行本地 vm 的所有 Azure VMware 解决方案（AVS）节点的每月预估成本。

计算按前面的顺序排列。 仅当计算机通过前一个阶段时，计算机才会移动到后面的阶段。 例如，如果服务器未通过 AVS 就绪阶段，则会将其标记为不适用于 Azure。 不会对该服务器执行大小调整和成本计算

## <a name="whats-in-an-azure-vmware-solution-avs-assessment"></a>Azure VMware 解决方案（AVS）评估中有哪些？

服务器评估中的 AVS 评估包含以下是：


| **属性** | **详细信息** 
| - | - 
| **目标位置** | 指定要迁移到的 AVS 私有云位置。<br/><br/> 服务器评估中的 AVS 评估目前支持以下目标区域：美国东部、西欧、美国西部。 
| **存储类型** | 指定要在 AVS 中使用的存储引擎。<br/><br/> AVS 评估只支持使用 vSAN 作为默认存储类型。 
**预订实例（RIs）** | 此属性可帮助你在 AVS 中指定保留实例。 当前 AVS 节点不支持 RIs。 
**节点类型** | 指定用于映射本地 Vm 的[AVS 节点类型](../azure-vmware/concepts-private-clouds-clusters.md)。 默认节点类型为 AV36。 <br/><br/> Azure Migrate 将建议将 Vm 迁移到 AVS 所需的节点数。 
**FTT 设置，RAID 级别** | 指定适用于容忍和 Raid 组合的失败。 选择的 FTT 选项与本地 VM 磁盘要求一起决定了 AVS 中所需的总 vSAN 存储空间。 
**“大小调整”条件** | 设置要用于 AVS 的虚拟机*大小*的条件。 你可以选择*基于性能*的大小调整，也可以选择*在本地*进行调整，而无需考虑性能历史记录。 
**性能历史记录** | 设置评估计算机性能数据时要考虑的持续时间。 仅当大小调整条件*基于性能*时，此属性才适用。 
**百分位使用率** | 指定要用于调整右大小的性能样本集的百分位数值。 此属性仅当调整大小基于性能时才适用。
**舒适因子** | 在评估期间，Azure Migrate 服务器评估会考虑使用缓冲区（舒适因素）。 该缓冲应用到 VM 的机器使用率数据（CPU、内存、磁盘和网络）上。 舒适因子考虑到季节性使用特点、短期性能历史记录，以及未来使用量可能会增加等问题。<br/><br/> 例如，一个使用率为 20% 的 10 核 VM 通常相当于一个 2 核 VM。 但是，如果舒适因子为 2.0x，则结果就变成一个 4 核 VM。 
**产品/服务** | 显示您注册的[Azure 产品/服务](https://azure.microsoft.com/support/legal/offer-details/)。 Azure Migrate 会进行相应的成本估算。
**货币** | 显示帐户的计费货币。 
**折扣 (%)** | 列出你在 Azure 产品/服务上收到的任何特定于订阅的折扣。 默认设置是 0%。 
**Azure 混合权益** | 指定您是否具有软件保障并且有资格使用[Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-use-benefit/)。 尽管由于基于节点的价格而不影响 Azure VMware 解决方案定价，但客户仍然可以使用 Azure 混合权益在 AVS 中应用本地操作系统许可证（基于 Microsoft）。 其他软件操作系统供应商必须提供其自己的许可条款，例如 RHEL。 
**vCPU 超额订阅** | 指定绑定到 AVS 节点中一个物理内核的虚拟核心数的比率。 计算中的默认值为 4 vCPU：1个物理核心 in AVS。 <br/><br/> API 用户可以将此值设置为整数。 请注意，vCPU 超额 > 4:1 可能会影响工作负载，具体取决于其 CPU 使用率。 


## <a name="azure-vmware-solution-avs-suitability-analysis"></a>Azure VMware 解决方案（AVS）适用性分析

服务器评估中的 AVS 评估通过查看计算机属性来评估每个本地 Vm 的 AVS 的适用性。 它还将每个已评估的计算机分配给以下适用性类别之一：

- **适用于 AVS**：计算机可以按原样迁移到 AZURE （AVS），无需进行任何更改。 它将在具有完全 AVS 支持的 AVS 中开始。
- **准备就绪**： vm 可能会在当前 vSphere 版本中出现兼容性问题，还可能需要 VMware 工具和或其他设置，然后才能在 AVS 中实现 VM 的完整功能。
- **尚未准备好用于 avs**： VM 将不会在 avs 中启动。 例如，如果本地 VMware VM 连接了外部设备（例如 cd-rom），则 VMware VMotion 操作将失败（如果使用 VMware VMotion）。
- **准备情况未知**： Azure Migrate 无法确定计算机的就绪性，因为从本地环境收集的元数据不足。

服务器评估检查计算机属性以确定本地计算机的 Azure 准备情况。

### <a name="machine-properties"></a>计算机属性

服务器评估查看本地 VM 的以下属性，以确定它是否可在 Azure VMware 解决方案（AVS）上运行。


| **属性** | **详细信息** | **AVS 就绪状态** 
| - | - | - 
| **Internet 协议** | AVS 目前不支持 IPv6 Internet 寻址。<br/><br/> 如果检测到计算机使用 IPv6，请与你当地的 MSFT AVS GBB 团队联系，以获取修正指南。| 有条件地准备好 Internet 协议


### <a name="guest-operating-system"></a>来宾操作系统

目前，AVS 评估不会在适用性分析过程中检查操作系统。 在本地 Vm 上运行的所有操作系统都可能在 Azure VMware 解决方案（AVS）上运行。

服务器评估连同 VM 属性一起查看计算机的来宾操作系统，以确定它是否可在 Azure 上运行。


## <a name="sizing"></a>调整大小

将计算机标记为可供 AVS 使用后，服务器评估中的 AVS 评估会提供节点大小建议，这涉及到识别适当的本地 VM 要求并找到所需的 AVS 节点的总数。 根据指定的评估属性，这些建议会有所不同。

- 如果评估使用*基于性能的大小调整*，Azure Migrate 会考虑计算机的性能历史记录，为 AVS 做出适当的大小调整建议。 如果已过度分配本地 VM，但利用率较低，并且你想要将虚拟机的大小调整到 AVS 以节省成本，则此方法特别有用。 此方法可帮助你在迁移过程中优化大小。
- 如果你不想考虑 VM 大小的性能数据，并想要将本地计算机原样地用于 AVS，则可以将调整大小标准设置为 *"本地*"。 然后，服务器评估将基于本地配置调整 Vm 大小，而无需考虑利用率数据。 


### <a name="ftt-sizing-parameters"></a>FTT 大小调整参数

AVS 中使用的存储引擎为 vSAN。 vSAN 存储策略为虚拟机定义存储要求。 这些策略保证了 VM 所需的服务级别，因为它们可确定如何将存储分配给 VM。 可用的 FTT-Raid 组合如下： 

**允许的故障数 (FTT)** | **RAID 配置** | **需要的最少主机数** | **大小调整注意事项**
--- | --- | --- | --- 
1 | RAID-1（镜像） | 3 | 100GB VM 将使用 200GB。
1 | RAID-5（擦除编码） | 4 | 100GB VM 将使用 133.33 GB
2 | RAID-1（镜像） | 5 | 100GB VM 将使用 300GB。
2 | RAID-6（擦除编码） | 6 | 100GB VM 将使用 150GB。
3 | RAID-1（镜像） | 7 | 100GB VM 将使用 400GB。

### <a name="performance-based-sizing"></a>基于性能的大小

对于基于性能的大小调整，服务器评估从附加到 VM 的磁盘开始，后跟网络适配器。 然后，它将 VM 要求映射到 AVS 的相应无节点。 Azure Migrate 设备对本地环境进行配置，以便收集 CPU、内存、磁盘和网络适配器的性能数据。

**性能数据收集步骤：**

1. 对于 VMware Vm，Azure Migrate 设备将按每隔20秒的时间间隔收集一个实时示例点。 
2. 设备汇总每10分钟收集的样本点，并将最近10分钟的最大值发送到服务器评估。
3. 服务器评估存储最近一个月的所有10分钟示例点。 然后，根据为*性能历史记录*和*百分比利用率*指定的评估属性，它将标识用于适当调整大小的适当数据点。 例如，如果将性能历史记录设置为1天，百分比利用率为95%，则服务器评估使用最近一天10分钟的示例点，按升序对它们进行排序，并选取第95百分位值进行适当调整。
4. 此值与舒适系数相乘，可获取设备收集的每个指标（CPU 使用率、内存使用率、磁盘 IOPS （读取和写入）、磁盘吞吐量（读取和写入）和网络吞吐量（传入和传出）的有效性能利用率数据。

确定有效利用率值后，将按如下所示处理存储、网络和计算大小调整。

**存储大小调整**： Azure Migrate 使用总本地 VM 磁盘空间作为计算参数来确定 AVS vSAN 存储要求以及客户选择的 FTT 设置。 FTT-可容忍的故障以及每个 FTT 的最少节点数选项将确定所需的总 vSAN 存储与 VM 磁盘要求组合在一起。

**网络规模**：对于 AVS 评估，服务器评估目前不考虑任何网络设置。

**计算大小调整**：计算存储要求后，服务器评估会考虑 CPU 和内存要求，以确定 AVS 基于节点类型所需的节点数。

- 根据大小调整条件，服务器评估将查看基于性能的 VM 数据或本地 VM 配置。 舒适系数设置允许指定群集的增长因子。 目前，默认情况下会启用超线程，因此 36 个核心节点将具有 72 个 vCore。 结合每台物理计算机使用 4 个 vCore 和不超过 80% 利用率的 VMware 标准，来确定每个群集的 CPU 阈值，以便在不影响群集可用性的情况下进行维护或故障处理。 当前没有可用于更改超额订阅值的替代，我们可能会在将来的版本中使用它。

### <a name="as-on-premises-sizing"></a>按本地大小调整

如果使用*作为本地大小调整*，则服务器评估不会考虑 vm 和磁盘的性能历史记录。 相反，它会根据本地分配的大小分配 AVS 节点。 默认存储类型为在 AVS 中的 vSAN。

## <a name="confidence-ratings"></a>置信度评级

Azure Migrate 中的每个基于性能的评估都与一个（最低）到5星（最高）的置信度分级相关联。

- 为评估分配置信度时，会考虑到进行评估计算时所需数据点的可用性。
- 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 置信度评级不适用于*作为本地*评估。
- 对于基于性能的大小调整，服务器评估中的 AVS 评估需要 CPU 和 VM 内存的利用率数据。 收集以下数据，但不在 AVS 的大小建议中使用：
  - 附加到 VM 的每个磁盘的磁盘 IOPS 和吞吐量数据。
  - 为附加到 VM 的每个网络适配器处理基于性能的大小调整的网络 i/o。

  如果在 vCenter Server 中有任何使用不足，则大小建议可能不可靠。

根据可用的数据点数百分比，评估的置信度分级如下所示。


| **数据点的可用性** | **置信度分级** |
| - | - |
| 0-20% | 1 星 |
| 21-40% | 2 星 |
| 41-60% | 3 星 |
| 61-80% | 4 星 |
| 81-100% | 5 星 |

### <a name="low-confidence-ratings"></a>低置信度分级

下面是评估降低置信度的几个原因：

- 你未在创建评估的持续时间内分析环境。 例如，如果你创建了 "性能持续时间" 设置为一天的评估，则必须在开始发现所有数据点后等待至少一天。
- 一些 VM 在进行评估计算期间关闭。 如果任何 Vm 在某个持续时间内关闭，服务器评估将无法收集该时间段的性能数据。
- 某些 Vm 是在计算评估期间创建的。 例如，如果你为上个月的性能历史记录创建了评估，但某些 Vm 只是在一周前的环境中创建的，则新 Vm 的性能历史记录不会存在于完整的持续时间内。

> [!NOTE]
> 如果任何评估的置信度小于五星级，我们建议你等待至少一天的时间让设备对环境进行配置，然后重新计算评估。 如果不这样做，则基于性能的大小调整可能不可靠。 在这种情况下，我们建议你将评估切换为本地大小调整。

## <a name="monthly-cost-estimation"></a>每月成本估计

调整大小建议完成后，Azure Migrate 通过将节点价格所需的 AVS 节点数相乘，来计算在 AVS 中运行本地工作负荷的总成本。 每个 VM 的成本是通过将总成本除以评估中的 Vm 数来计算的。 
- 计算采用所需的节点数、节点类型和位置。
- 它在所有节点上聚合成本，以计算每月总成本。
- 成本以在评估设置中指定的币种显示。

由于 Azure VMware 解决方案（AVS）的定价是每个节点的，因此总成本不会产生计算成本和存储成本分布。 [了解详细信息](../azure-vmware/introduction.md)

请注意，由于 Azure VMware 解决方案（AVS）处于预览阶段，评估中的节点价格是预览版价格。 请联系你的本地 MSFT AVS GBB 团队以获得指导。

## <a name="migration-tool-guidance"></a>迁移工具指南

在 Azure VMware 解决方案 (AVS) 评估的 Azure 迁移就绪性报告中，可以看到以下推荐工具： 
- **VMWARE HCX 或 Enterprise**：对于 vmware 计算机，Vmware 混合云扩展（HCX）解决方案是将本地工作负荷迁移到 Azure VMware 解决方案（AVS）私有云的建议迁移工具。 [了解详细信息](../azure-vmware/hybrid-cloud-extension-installation.md)。
- **未知**：对于通过 CSV 文件导入的计算机，默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展（HCX）解决方案。

## <a name="next-steps"></a>后续步骤

创建适用于[AVS VMware vm](how-to-create-azure-vmware-solution-assessment.md)的评估。
