---
title: 适用于 Hyper-V 到 Azure 部署的 Azure Site Recovery 部署规划器 | Microsoft Docs
description: 本文介绍如何分析适用于 Hyper-V 到 Azure 方案的 Azure Site Recovery 部署规划器的已生成报表。
services: site-recovery
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: nisoneji
ms.openlocfilehash: 78350f8e0ab86c7c87c321397bd4e4a4849368b6
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/09/2018
ms.locfileid: "37918843"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>分析 Azure Site Recovery 部署规划器报表
本文介绍 Azure Site Recovery 部署规划器针对 Hyper-V 到 Azure 方案生成的 Excel 报表中包含的工作表。

## <a name="on-premises-summary"></a>本地摘要
本地摘要工作表概述了分析的 Hyper-V 环境。

![本地摘要](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**开始日期**和**结束日期**：生成报告时要考虑的分析数据的开始和结束日期。 默认情况下，开始日期是开始分析的日期，结束日期是停止分析的日期。 如果报表是使用这些参数生成的，此信息可以是“StartDate”和“EndDate”值。

**分析总天数**：要生成报告的开始和结束日期之间的分析总天数。

**兼容的虚拟机数**：计算了所需网络带宽、所需存储帐户数、Azure 核心数的兼容 VM 总数。

**所有兼容虚拟机的磁盘总数**：所有兼容 VM 的磁盘总数。

**每个兼容虚拟机的平均磁盘数**：根据所有兼容 VM 计算出的平均磁盘数。

**平均磁盘大小(GB)**：根据所有兼容 VM 计算出的平均磁盘大小。

**所需 RPO (分钟)**：默认恢复点目标，或者在生成报告时为了估算所需带宽为“DesiredRPO”参数传递的值。

**所需带宽(Mbps)**：在生成报告时为了估算可实现恢复点目标 (RPO) 为“Bandwidth”参数传递的值。

**每日观察到的典型数据变动量(GB)**：在所有分析日期观察到的平均数据变动量。

## <a name="recommendations"></a>建议 
Hyper-V 到 Azure 报表的建议表根据选定的所需 RPO 提供以下详细信息：

![适用于 Hyper-V 到 Azure 报表的建议](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>配置文件数据
![配置文件数据](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**数据分析期**：在此期间运行了分析。 默认情况下，此工具在计算中包括所有分析的数据。 如果在报表生成过程中使用了 StartDate 和 EndDate 选项，则会生成特定期间的报表。 

**分析的 Hyper-V 服务器数**：其 VM 的报表已生成的 Hyper-V 服务器数。 选择该数字即可查看 Hyper-V 服务器的名称。 将会打开“本地存储要求”表，其中显示了所有服务器及其存储要求。 

**所需 RPO**：部署的恢复点目标。 默认情况下，所需网络带宽是根据 RPO 值为 15、30 和 60 分钟计算的。 根据所做的选择，受影响的值会在工作表中更新。 如果生成报表时使用了 DesiredRPOinMin 参数，该值会显示在“所需 RPO”结果中。

### <a name="profiling-overview"></a>分析概述
![分析概述](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**分析的虚拟机总数**：提供了其分析数据的 VM 总数。 如果 VMListFile 中包含未分析的任何 VM 的名称，则生成报表时不会考虑这些 VM，会将其从分析的 VM 总数中排除。

**兼容的虚拟机数**：可以使用 Azure Site Recovery 在 Azure 中保护的 VM 数。 它是计算了所需网络带宽、存储帐户数、Azure 核心数的兼容 VM 总数。 “兼容的 VM”部分提供了每个兼容的 VM 的详细信息。

**不兼容的虚拟机**：在保护方面与 Site Recovery 不兼容的已分析 VM 数。 “不兼容的 VM”部分说明了不兼容的原因。 如果 VMListFile 包含任何未分析 VM 的名称，则这些 VM 不计入不兼容 VM 的数目。 在“不兼容的 VM”部分的末尾，这些 VM 作为“找不到数据”类型列出。

**所需 RPO**：以分钟为单位的所需恢复点目标。 针对以下三个 RPO 值生成报告：15分钟（默认值）、30 分钟、60 分钟。 会根据你在工作表右上方“所需 RPO”下拉列表中所做的选择来更改报表中的带宽建议。 如果结合某个自定义值使用 -DesiredRPO 参数生成了报表，此自定义值会在“所需 RPO”下拉列表中显示为默认值。

### <a name="required-network-bandwidth-mbps"></a>所需的网络带宽 (Mbps)
![所需的网络带宽](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**为了满足 100% 时间的 RPO**：分配建议的以 Mbps 为单位的带宽，以便满足 100% 时间的所需 RPO。 这种带宽量必须专用于所有兼容 VM 的稳态增量复制，避免任何 RPO 违规。

**为了满足 90% 时间的 RPO**：也许因为带宽定价或其他原因，无法设置所需带宽来满足 100% 时间的所需 RPO。 如果属于这种情况，可以使用较低的带宽设置来满足 90% 时间的所需 RPO。 为了帮助用户理解设置这种规格较低的带宽所造成的影响，报告中会根据预期发生的 RPO 违规的数目和持续时间提供一项模拟分析。

**实现的吞吐量**：从运行 GetThroughput 命令的服务器到存储帐户所在的 Azure 区域的吞吐量。 此吞吐量数字表示使用 Site Recovery 保护兼容的 VM 时可以实现的吞吐量水平（估计值）。 Hyper-V 服务器的存储和网络特征必须与运行该工具的服务器的此类特征保持相同。

对于所有企业型 Site Recovery 部署，建议使用 [ExpressRoute](https://aka.ms/expressroute)。

### <a name="required-storage-accounts"></a>所需的存储帐户
下图显示了保护所有兼容 VM 所需的存储帐户（标准和高级）总数。 若要了解适用于每个 VM 的存储帐户，请参阅“VM-存储位置”部分。

![所需的 Azure 存储帐户](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>所需的 Azure 核心数
此结果是在对所有兼容的 VM 进行故障转移或测试性故障转移之前需要设置的核心总数。 如果订阅中能够使用的核心太少，在执行测试性故障转移或故障转移时，Site Recovery 将无法创建 VM。

![所需的 Azure 核心数](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>其他本地存储要求

在 Hyper-V 服务器上成功地进行初始复制和增量复制所需的总可用存储，可确保 VM 复制不会导致任何会对生产应用程序造成负面影响的停机。 [本地存储要求](#on-premises-storage-requirement)中提供了每个卷要求的详细信息。 

若要了解为何复制时需要可用空间，请参阅[本地存储要求](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication)部分。

![本地存储要求和复制频率](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>最大复制频率
若要在复制时实现所需的 RPO，必须设置建议的最大复制频率。 默认值为 5 分钟。 若要实现更好的 RPO，可将复制频率设置为 30 秒。

### <a name="what-if-analysis"></a>模拟分析
![模拟分析](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) 此项分析概述了如果设置较低的带宽，导致只能在 90% 的时间内满足所需 RPO，则在分析期间可能会发生多少项违规。 在任何给定的日期可能会发生一项或多项 RPO 违规。 下图显示了一天的峰值 RPO。 基于此分析，可以确定在使用指定的较低带宽的条件下，是否可以接受所有日期的 RPO 违规数以及每天的峰值 RPO。 如果可以接受，则可为复制设置较低的带宽。 如果不可接受，请根据建议分配较高的带宽，以便满足 100% 时间的所需 RPO。 

### <a name="recommendation-for-successful-initial-replication"></a>成功进行初始复制的建议
此部分讨论了需要对 VM 进行保护的批数，以及成功完成初始复制 (IR) 所需的最小带宽。 

![IR 批处理](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

必须按给定的批顺序对 VM 进行保护。 每一批都有一个特定的 VM 列表。 第 1 批 VM 必须在第 2 批 VM 之前受到保护， 第 2 批 VM 必须在第 3 批 VM 之前受到保护，依此类推。 完成第 1 批 VM 的初始复制以后，即可启用对第 2 批 VM 的复制。 类似地，完成第 2 批 VM 的初始复制以后，即可启用对第 3 批 VM 的复制，依此类推。 

如果不遵从此批顺序，则可能没有进行初始复制所需的足够带宽供随后受到保护的 VM 使用。 结果就是，VM 无法完成初始复制，或者一些受保护的 VM 可能会进入重新同步模式。 所选 RPO 表的 IR 批处理提供了应包括在每一批中的具体 VM 的详细信息。

上图显示了按给定批顺序对各个批进行初始复制和增量复制时的带宽分布情况。 对第一批 VM 进行保护时，全部带宽可以用于初始复制。 完成第一批的初始复制以后，部分带宽需要用于增量复制。 剩余带宽可供第二批 VM 的初始复制使用。 

第 2 批的条形表示第 1 批 VM 需要的增量复制带宽和可供第 2 批 VM 进行初始复制的带宽。 同样，第 3 批的条形表示前面的批次（第 1 批和第 2 批 VM）需要的增量复制带宽和可供第 3 批进行初始复制的带宽，依此类推。 所有批次的初始复制完成以后，最后一个条形表示所有受保护 VM 进行增量复制所需的带宽。

**为何需要初始复制批处理？**
初始复制的完成时间取决于 VM 磁盘大小、已用磁盘空间和可用网络吞吐量。 所选 RPO 表的 IR 批处理中提供了详细信息。

### <a name="cost-estimation"></a>成本估算
下图显示的摘要视图包含灾难恢复 (DR) 到所选目标区域的 Azure 时的总估算成本，以及生成报表时所用的指定货币。

![成本估算摘要](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

可以通过此摘要了解在使用 Site Recovery 将所有兼容的 VM 转移到 Azure 进行保护时，需要支付的存储、计算、网络和许可证成本。 计算成本时，只针对兼容的 VM，不针对所有受分析的 VM。 
 
可以按月或按年查看成本。 详细了解[支持的目标区域](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)和[支持的货币](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies)。

**按组件成本**：总 DR 成本分为四个部分：计算成本、存储成本、网络成本和 Site Recovery 许可证成本。 成本计算取决于在复制和 DR 演练时相关资源的使用情况。 用于计算的资源包括计算、存储（高级和标准）、在本地站点和 Azure 之间配置的 ExpressRoute/VPN、Site Recovery 许可证。

**按状态成本**：总灾难恢复成本按两种不同的状态（“复制”和“DR 演练”）分类。 

**复制成本**：复制期间产生的成本， 涵盖存储成本、网络成本、Site Recovery 许可证成本。 

**DR 演练成本**：测试性故障转移期间产生的成本。 Site Recovery 会在测试性故障转移期间启动 VM。 DR 演练成本涵盖正在运行的 VM 的计算和存储成本。 

**每月/年的 Azure 存储成本**：条形图表示针对高级和标准存储进行复制和 DR 演练时产生的总存储成本。 可以在[成本估算](hyper-v-deployment-planner-cost-estimation.md)表中查看每个 VM 的详细成本分析。

### <a name="growth-factor-and-percentile-values-used"></a>增长系数和使用的百分位值
工作表底部的此部分显示用于已分析 VM 的所有性能计数器的百分位值（默认为第 95 百分位）， 以及所有计算中使用的增长系数（默认为 30%）。

![增长系数和使用的百分位值](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>有关用作输入的可用带宽建议
![带宽输入分析概述](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

有时，你知道无法为 Site Recovery 复制设置 x Mbps 以上的带宽。 可以使用该工具输入可用带宽（生成报表时使用 -Bandwidth 参数），在数分钟内获得可实现的 RPO。 使用这个可实现的 RPO 值，可以确定是需要预配额外的带宽，还是可以使用某个灾难恢复解决方案来实现此 RPO。

![可实现的 RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>VM-存储位置建议 
![VM-存储位置](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**磁盘存储类型**：标准或高级存储帐户，用于复制“要放置的 VM”列中所述的所有相应 VM。

**建议的前缀**：建议的三字符前缀，可用于存储帐户的命名。 可以使用自己的前缀，而该工具的建议则遵循[存储帐户的分区命名约定](https://aka.ms/storage-performance-checklist)。

**建议的帐户名称**：在使用建议的前缀后出现的存储帐户名称。 将尖括号（< 和 >）中的名称替换为自定义输入。

**日志存储帐户**：所有复制日志存储在标准存储帐户中。 对于复制到高级存储帐户的 VM，请设置一个附加的标准存储帐户来进行日志存储。 单个标准日志存储帐户可由多个高级复制存储帐户使用。 复制到标准存储帐户的 VM 为日志使用相同的存储帐户。

**建议的日志帐户名称**：在使用建议的前缀后出现的存储日志帐户名称。 将尖括号（< 和 >）中的名称替换为自定义输入。

**位置摘要**：执行复制和测试性故障转移或故障转移时，存储帐户中 VM 总负载的摘要。 摘要包括：

* 映射到存储帐户的 VM 的总数。 
* 放置在此存储帐户中的所有 VM 的总读/写 IOPS。
* 总写入（复制）IOPS。
* 跨所有磁盘的总安装大小。
* 磁盘总数。

**要放置的 VM**：列出了为优化性能和使用而应放置在给定存储帐户中的所有 VM。

## <a name="compatible-vms"></a>兼容的 VM
由 Site Recovery 部署规划器生成的 Excel 报表在“兼容的 VM”工作表中提供了所有兼容 VM 的详细信息。

![兼容的 VM](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**VM 名称**：生成报表时在 VMListFile 中使用的 VM 名称。 此列还列出附加到 VM 的磁盘 (VHD)。 这些名称包括 Hyper-V 主机名，其中的 VM 是此工具在分析期间发现它们时放置的。

**VM 兼容性**：值为“是”和“是\*”。  **是**\*针对 VM 适用于 [Azure 高级存储](https://aka.ms/premium-storage-workload)的情况。 在这里，所分析的高变动量磁盘或 IOPS 磁盘适合的高级磁盘大小大于映射到磁盘的大小。 存储帐户决定了根据大小对磁盘分类时，可将磁盘归入哪种高级存储磁盘类型： 
* <128 GB 为 P10。
* 128 GB 到 256 GB 为 P15。
* 256 GB 到 512 GB 为 P20。
* 512 GB 到 1,024 GB 为 P30。
* 1,025 GB 到 2,048 GB 为 P40。
* 2,049 GB 到 4,095 GB 为 P50。

例如，如果某个磁盘按工作负荷特征应归入 P20 或 P30 类别，但按大小应归入较低的高级存储磁盘类型，则此工具会将该 VM 标记为“是\*”。 该工具还建议根据建议的高级存储磁盘类型更改源磁盘大小，或者在故障转移后更改目标磁盘类型。

**存储类型**：标准或高级。

**建议的前缀**：由三个字符构成的存储帐户前缀。

**存储帐户**：此名称使用建议的存储帐户前缀。

**峰值读/写 IOPS (包括增长系数)**：磁盘上的峰值工作负荷读/写 IOPS（默认为第 95 百分位），以及将来的增长系数（默认为 30%）。 VM 的总读/写 IOPS 并非总是 VM 的单个磁盘的读/写 IOPS 之和。 VM 的峰值读/写 IOPS 是单个磁盘在分析期间的每分钟读/写 IOPS 之和的峰值。

**以 MB/秒为单位的峰值数据变动量(包括增长系数)**：磁盘上的峰值变动率（默认为第 95 百分位），以及将来的增长系数（默认为 30%）。 VM 的总数据变动量并非总是 VM 的单个磁盘的数据变动量之和。 VM 的峰值数据变动量是单个磁盘在分析期间的每分钟变动量之和的峰值。

**Azure VM 大小**：适用于此本地 VM 的映射 Azure 云服务 VM 大小。 映射取决于本地 VM 的内存、磁盘/核心/NIC 数以及读/写 IOPS。 建议在符合所有本地 VM 特征的情况下，始终使用最小的 Azure VM 大小。

**磁盘数**：VM 上的虚拟机磁盘 (VHD) 总数。

**磁盘大小(GB)**：VM 所有磁盘的总大小。 该工具还会显示 VM 中各个磁盘的磁盘大小。

**核心数**：VM 上的 CPU 核心数。

**内存(MB)**：VM 上的 RAM。

**NIC**：VM 上的 NIC 数。

**启动类型**：VM 的启动类型。 它可以是 BIOS 或 EFI。

## <a name="incompatible-vms"></a>不兼容的 VM
由 Site Recovery 部署规划器生成的 Excel 报表在“不兼容的 VM”工作表中提供了所有不兼容 VM 的详细信息。

![不兼容的 VM](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**VM 名称**：生成报表时在 VMListFile 中使用的 VM 名称。 此列还列出附加到 VM 的磁盘 (VHD)。 这些名称包括 Hyper-V 主机名，其中的 VM 是此工具在分析期间发现它们时放置的。

**VM 兼容性**：指示给定的 VM 为何无法与 Site Recovery 兼容使用。 会针对 VM 的每个不兼容磁盘说明原因，而根据已发布的[存储限制](https://aka.ms/azure-storage-scalbility-performance)，这些原因不外乎：

* 磁盘大小大于 4,095 GB。 Azure 存储目前不支持大于 4,095 GB 的数据磁盘大小。

* 第 1 代（BIOS 启动类型）VM 的 OS 磁盘大于 2,047 GB。 Site Recovery 不允许第 1 代 VM 的 OS 磁盘大小超出 2,047 GB。

* 第 2 代（EFI 启动类型）VM 的 OS 磁盘大于 300 GB。 Site Recovery 不允许第 2 代 VM 的 OS 磁盘大小超出 300 GB。

* 不允许 VM 名称包含任何下述字符：“” [] `。 此工具无法获取名称中包含任何此类字符的 VM 的已分析数据。 

* 一个 VHD 由至少两个 VM 共享。 Azure 不支持具有共享 VHD 的 VM。

* 不支持具有虚拟光纤信道的 VM。 Site Recovery 不支持具有虚拟光纤信道的 VM。

* Hyper-V 群集不包含复制代理。 如果未为 Hyper-V 群集配置 Hyper-V 副本代理，Site Recovery 不支持该群集中的 VM。

* VM 并非高度可用。 Site Recovery 不支持其 VHD 存储在本地磁盘而非群集磁盘上的 Hyper-V 群集节点的 VM。 

* VM 总大小（复制 + 测试性故障转移）超出系统支持的高级存储帐户大小限制 (35 TB)。 当 VM 中的单个磁盘的性能特征超出系统支持的适用于标准存储的 Azure 或 Site Recovery 最大限制时，通常会表现出这种不兼容性。 如果出现这种情况，则必须将 VM 置于高级存储区域。 但是，高级存储帐户最大可以为 35 TB。 不能跨多个存储帐户对单个受保护的 VM 进行保护。 

    在受保护 VM 上执行测试性故障转移时，如果为测试性故障转移配置了非托管磁盘，则该故障转移运行时所使用的存储帐户与进行复制时所使用的存储帐户相同。 在这种情况下，需要的额外存储空间量与进行复制时需要的相同。 这样可确保在进行复制的同时成功地进行测试性故障转移。 为测试性故障转移配置托管磁盘时，不需考虑为测试性故障转移 VM 提供额外的空间。

* 源 IOPS 超出了每个磁盘支持的存储 IOPS 限制，即 7,500。

* 源 IOPS 超出了每个 VM 支持的存储 IOPS 限制，即 80,000。

* 源 VM 平均数据变动量超出了支持的 Site Recovery 数据变动量限制，即平均 I/O 大小不能超过 10 MB/秒。

* 源 VM 平均有效写入 IOPS 超出了支持的 Site Recovery IOPS 限制，即 840。

* 计算出的快照存储超出了支持的快照存储限制，即 10 TB。

**峰值读/写 IOPS (包括增长系数)**：磁盘上的峰值工作负荷 IOPS（默认为第 95 百分位），以及将来的增长系数（默认为 30%）。 VM 的总读/写 IOPS 并非总是 VM 的单个磁盘的读/写 IOPS 之和。 VM 的峰值读/写 IOPS 是单个磁盘在分析期间的每分钟读/写 IOPS 之和的峰值。

**峰值数据变动量(MB/秒，包括增长系数)**：磁盘上的峰值变动率（默认为第 95 百分位），以及将来的增长系数（默认为 30%）。 请注意，VM 的总数据变动量并非总是 VM 的单个磁盘的数据变动量之和。 VM 的峰值数据变动量是单个磁盘在分析期间的每分钟变动量之和的峰值。

**磁盘数**：VM 上的 VHD 总数。

**磁盘大小(GB)**：VM 所有磁盘的总安装大小。 该工具还会显示 VM 中各个磁盘的磁盘大小。

**核心数**：VM 上的 CPU 核心数。

**内存(MB)**：VM 上的 RAM 量。

**NIC**：VM 上的 NIC 数。

**启动类型**：VM 的启动类型。 它可以是 BIOS 或 EFI。

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供了 Site Recovery 限制。 这些限制基于测试，但无法涵盖所有可能的应用程序 I/O 组合。 实际结果可能因应用程序 I/O 组合而异。 为获得最佳结果，请通过测试性故障转移执行广泛的应用程序测试，获取应用程序的真实性能视图，即使在规划部署后，也应如此。
 
**复制存储目标** | **源 VM 平均 I/O 大小** |**源 VM 平均数据变动量** | **每天的总源 VM 数据变动量**
---|---|---|---
标准存储 | 8 KB | 每个 VM 2 MB/秒 | 每个 VM 168 GB
高级存储 | 8 KB  | 每个 VM 5 MB/秒 | 每个 VM 421 GB
高级存储 | 16 KB 或更高| 每个 VM 10 MB/秒 | 每个 VM 842 GB

这些限制是在假设存在 30% 的 I/O 重叠的情况下给出的平均数。 Site Recovery 能够根据重叠率、较大的写入大小和实际工作负荷 I/O 行为处理更高的吞吐量。 上述数字假定通常情况下存在大约 5 分钟的积压工作。 也就是说，数据在上传后会在 5 分钟内进行处理并创建恢复点。

## <a name="on-premises-storage-requirement"></a>本地存储要求

工作表提供了 Hyper-V 服务器（VHD 驻留在其中）每个卷的总可用存储空间要求，这是成功进行初始复制和增量复制的前提。 在启用复制之前，请在卷上添加必需的存储空间，确保复制不会导致任何会对生产应用程序造成负面影响的停机。 

  Site Recovery 部署规划器根据用于复制的 VHD 大小和网络带宽来确定最佳存储空间要求。

![本地存储要求](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>复制时为何需要 Hyper-V 服务器上存在可用空间？
* 启用 VM 复制时，Site Recovery 会拍摄 VM 的每个 VHD 的快照，以便进行初始复制。 当初始复制正在进行时，应用程序会将新的更改写入磁盘。 Site Recovery 在日志文件中跟踪这些增量更改，因此需要额外的存储空间。 在初始复制完成之前，日志文件存储在本地。 

    如果没有可供日志文件和快照 (AVHDX) 使用的足够空间，复制会进入重新同步模式，永远也不会完成。 在最坏的情况下，需提供 100% 的 VHD 大小额外可用空间来完成初始复制。
* 初始复制完成后，增量复制开始。 Site Recovery 在日志文件中跟踪这些增量更改，而日志文件则存储在 VM 的 VHD 驻留的卷上。 这些日志文件按配置的复制频率复制到 Azure。 日志文件需要一定的时间才能复制到 Azure，具体取决于可用的网络带宽。 

    如果没有足够的可用空间来存储日志文件，复制会暂停。 然后，VM 的复制状态变为“需要重新同步”。
* 如果网络带宽不够，无法将日志文件及时推送到 Azure 中，则日志文件会堆积在卷上。 在最坏的情况下，当日志文件大小增加到 VHD 大小的 50% 时，VM 复制会变为“需要重新同步”状态。 在最坏的情况下，需提供 50% 的 VHD 大小额外可用空间来完成增量复制。

**Hyper-V 主机**：已分析的 Hyper-V 服务器的列表。 如果某个服务器是 Hyper-V 群集的一部分，则会将所有群集节点组合在一起。

**卷(VHD 路径)**：VHD/VHDX 所在的 Hyper-V 主机的每个卷。 

**可用空间(GB)**：卷上的可用空间。

**卷上需要的总存储空间(GB)**：成功进行初始复制和增量复制所需要的在卷上可用的总存储空间。 

**需要在卷上预配才能成功进行复制的总额外存储(GB)**：此项建议的总额外空间必须在卷上预配好，然后才能成功进行初始复制和增量复制。

## <a name="initial-replication-batching"></a>初始复制批处理 

### <a name="why-do-i-need-initial-replication-batching"></a>为何需要初始复制批处理？
如果同时对所有 VM 进行保护，则可用存储要求要高得多。 如果没有足够的存储可用，则 VM 的复制会进入重新同步模式。 另外，若要让所有 VM 一起成功地完成初始复制，网络带宽要求会高得多。 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>针对所选 RPO 进行的初始复制批处理
以下工作表提供了需要进行 IR 的每个批次的详细信息视图。 将会为每个 RPO 创建单独的 IR 批处理工作表。 

按照每个卷的本地存储要求建议操作以后，需要复制的主要信息是可以并行进行保护的 VM 的列表。 这些 VM 按批组合在一起，可以有多个批次。 请按给定的批顺序对 VM 进行保护。 首先保护第 1 批 VM。 完成初始复制后，保护第 2 批 VM，依此类推。 可以从此工作表中获取批次和相应 VM 的列表。 

![IR 批处理详细信息](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![其他 IR 批处理详细信息](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>每一批都提供以下信息 
**Hyper-V 主机**：要保护的 VM 的 Hyper-V 主机。

**虚拟机**：要保护的 VM。 

**注释**：如果需要对 VM 的特定卷执行操作，则在此处提供注释。 例如，如果卷上没有足够的可用空间，则注释会要求“添加额外的存储来保护此 VM”。

**卷(VHD 路径)**：VM 的 VHD 所驻留的卷名称。 

**卷上的可用空间(GB)**：VM 的卷上的可用磁盘空间。 在计算卷上的可用空间时，会考虑由以前批次的 VM（其 VHD 位于同一卷上）用来进行增量复制的磁盘空间。 

例如，VM1、VM2 和 VM3 驻留在路径为 E:\VHDpath 的卷上。 在复制前，卷上的可用空间为 500 GB。 VM1 属于第 1 批，VM2 属于第 2 批，VM3 属于第 3 批。 VM1 的可用空间为 500 GB。 VM2 的可用空间为 500 – VM1 进行增量复制所需的磁盘空间。 如果 VM1 需要 300 GB 的空间进行增量复制，则 VM2 的可用空间为 500 GB – 300 GB = 200 GB。 同样，假设 VM2 需要 300 GB 进行增量复制， 则 VM3 的可用空间为 200 GB - 300 GB = -100 GB。

**卷上需要用于初始复制的存储(GB)**：VM 的卷上需要用于初始复制的存储空间。

**卷上需要用于增量复制的存储(GB)**：VM 的卷上需要用于增量复制的存储空间。

**在资源不足情况下为了避免复制失败而需要的额外存储(GB)**：卷上 VM 所需的额外存储空间。 这就是进行初始复制和增量复制的最高存储空间要求减去卷上的可用空间。

**进行初始复制所需的最小带宽(Mbps)**：VM 进行初始复制所需的最小带宽。

**进行增量复制所需的最小带宽(Mbps)**：VM 进行增量复制所需的最小带宽。

### <a name="network-utilization-details-for-each-batch"></a>每个批次的网络使用率详细信息 
每个批次表都提供了该批次的网络使用率摘要。

**可用于此批次的带宽**：在考虑以前批次的增量复制带宽因素后可供此批次使用的带宽。

**可用于此批次的初始复制的大致带宽**：可用于此批次的 VM 初始复制的带宽。 

**此批次的增量复制所用的大致带宽**：此批次的 VM 增量复制所需的带宽。 

**估计用于此批次的初始复制时间(HH:MM)**：估计的初始复制时间，采用“时:分”格式。



## <a name="next-steps"></a>后续步骤
详细了解[成本估算](hyper-v-deployment-planner-cost-estimation.md)。
