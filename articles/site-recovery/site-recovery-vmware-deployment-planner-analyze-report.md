---
title: "适用于 VMware 到 Azure 部署的 Azure Site Recovery Deployment Planner | Microsoft Docs"
description: "本文介绍如何分析适用于 VMware 到 Azure 方案的 Azure Site Recovery 部署规划器的已生成报表。"
services: site-recovery
documentationcenter: 
author: nsoneji
manager: garavd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 03/09/2018
ms.author: nisoneji
ms.openlocfilehash: dce374d85ef32fe1fbfc88502780fa2ad7a5eae4
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/12/2018
---
# <a name="azure-site-recovery-deployment-planner-report"></a>Azure Site Recovery 部署规划器报表
生成的 Microsoft Excel 报表包含以下工作表：
## <a name="on-premises-summary"></a>本地摘要
“本地摘要”工作表概述了分析的 VMware 环境。

![VMware 环境的本地摘要](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**开始日期**和**结束日期**：生成报告时要考虑的分析数据的开始和结束日期。 默认情况下，开始日期是开始分析的日期，结束日期是停止分析的日期。 如果结合这些参数生成报告，可以使用“StartDate”和“EndDate”值。

**分析总天数**：要生成报告的开始和结束日期之间的分析总天数。

**兼容的虚拟机数**：计算了所需网络带宽、所需存储帐户数、Microsoft Azure 核心数、配置服务器与附加进程服务器数的兼容 VM 总数。

**所有兼容虚拟机的磁盘总数**：此数字用作输入之一，确定要在部署中使用的配置服务器和附加进程服务器的数目。

**每个兼容虚拟机的平均磁盘数**：根据所有兼容 VM 计算出的平均磁盘数。

**平均磁盘大小(GB)**：根据所有兼容 VM 计算出的平均磁盘大小。

**所需 RPO (分钟)**：默认恢复点目标，或者在生成报告时为了估算所需带宽为“DesiredRPO”参数传递的值。

**所需带宽(Mbps)**：在生成报告时为了估算可实现 RPO 为“Bandwidth”参数传递的值。

**每日观察到的典型数据变动量(GB)**：在所有分析日期观察到的平均数据变动量。 此数字用作输入之一，确定要在部署中使用的配置服务器和附加进程服务器的数目。

## <a name="recommendations"></a>建议

VMware 到 Azure 报表的建议表根据选定的所需 RPO 提供以下详细信息：

![适用于 VMware 到 Azure 报表的建议](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>分析的数据
![Deployment Planner 中的分析数据视图](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**数据分析期**：在此期间运行了分析。 默认情况下，该工具使用所有分析数据进行计算，除非在生成报告期间使用 StartDate 和 EndDate 选项生成特定时间段的报告。

**服务器名称**：为其生成 VM 报告的 VMware vCenter 或 ESXi 主机的名称或 IP 地址。

**所需 RPO**：部署的恢复点目标。 默认情况下，所需网络带宽是根据 RPO 值为 15、30 和 60 分钟计算的。 根据所做的选择，受影响的值会在工作表中更新。 如果生成报告时使用了 *DesiredRPOinMin* 参数，该值会显示在“所需 RPO”结果中。

### <a name="profiling-overview"></a>分析概述

![Deployment Planner 中的分析结果](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**分析的虚拟机总数**：提供了其分析数据的 VM 总数。 如果 VMListFile 中包含未分析的任何 VM 的名称，则生成报告时不会考虑这些 VM，会将其从分析的 VM 总数中排除。

**兼容的虚拟机**：可以使用 Site Recovery 在 Azure 中保护的 VM 数。 它是计算了所需网络带宽、存储帐户数、Azure 核心数以及配置服务器与附加进程服务器数的兼容 VM 总数。 “兼容的 VM”部分提供了每个兼容的 VM 的详细信息。

**不兼容的虚拟机**：在保护方面与 Site Recovery 不兼容的已分析 VM 数。 “不兼容的 VM”部分说明了不兼容的原因。 如果 VMListFile 包含任何未分析 VM 的名称，则这些 VM 不计入不兼容 VM 的数目。 在“不兼容的 VM”部分的末尾，这些 VM 作为“找不到数据”类型列出。

**所需 RPO**：以分钟为单位的所需恢复点目标。 针对以下三个 RPO 值生成报告：15分钟（默认值）、30 分钟、60 分钟。 会根据你在工作表右上方“所需 RPO”下拉列表中所做的选择来更改报告中的带宽建议。 如果结合某个自定义值使用“-DesiredRPO”参数生成了报告，此自定义值会在“所需 RPO”下拉列表中显示为默认值。

### <a name="required-network-bandwidth-mbps"></a>所需的网络带宽 (Mbps)

![Deployment Planner 中的所需网络带宽](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**为了满足 100% 时间的 RPO：**分配建议的以 Mbps 为单位的带宽，以便满足 100% 时间的所需 RPO。 这种带宽量必须专用于所有兼容 VM 的稳态增量复制，避免任何 RPO 违规。

**为了满足 90% 时间的 RPO**：如果由于宽带价格或任何其他原因而无法设置所需的带宽来满足 100% 时间的所需 RPO，则可选择规格较低但仍可满足 90% 时间的所需 RPO 的带宽设置。 为了帮助用户理解设置这种规格较低的带宽所造成的影响，报告中会根据预期发生的 RPO 违规的数目和持续时间提供一项模拟分析。

**实现的吞吐量：**从运行 GetThroughput 命令的服务器到存储帐户所在的 Microsoft Azure 区域的吞吐量。 此吞吐量数字表示在配置服务器或进程服务器的存储和网络特征与运行该工具的服务器保持相同的前提下，使用 Site Recovery 保护兼容的 VM 时可实现的大致吞吐量水平。

对于复制，应设置建议的带宽以满足 100% 时间的 RPO。 在设置带宽后，如果工具所报告的已实现吞吐量没有增长，请执行以下操作：

1. 查看是否有任何网络服务质量 (QoS) 限制了 Site Recovery 吞吐量。

2. 查看 Site Recovery 保管库是否位于从物理上来说最近的受支持 Microsoft Azure 区域，以尽量降低网络延迟。

3. 检查本地存储特征，确定能否改进硬件（例如，从 HDD 升级到 SSD）。

4. 更改进程服务器中的 Site Recovery 设置，[增大用于复制的网络带宽量](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)。

如果是在其 VM 已经受保护的配置服务器或进程服务器上运行该工具，请运行该工具多次。 已实现的吞吐量数字会更改，具体取决于在该时间点处理的数据更改量。

对于所有企业型 Site Recovery 部署，建议使用 [ExpressRoute](https://aka.ms/expressroute)。

### <a name="required-storage-accounts"></a>所需的存储帐户
下图显示了保护所有兼容 VM 所需的存储帐户（标准和高级）总数。 若要了解适用于每个 VM 的存储帐户，请参阅“VM-存储位置”部分。

![Deployment Planner 中的所需存储帐户](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>所需的 Azure 核心数
此结果是在对所有兼容的 VM 进行故障转移或测试性故障转移之前需要设置的核心总数。 如果订阅中能够使用的核心太少，在执行测试性故障转移或故障转移时，Site Recovery 将无法创建 VM。

![Deployment Planner 中的所需 Azure 核心数](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>所需的本地基础结构
下图是要配置的配置服务器和附加进程服务器的总数，该数目足以保护所有兼容 VM。 根据所支持的[配置服务器大小建议](https://aka.ms/asr-v2a-on-prem-components)，该工具可能会建议使用更多的服务器。 该建议取决于每日变动量和受保护 VM（假定每个 VM 平均有三个磁盘）的最大数目哪个更大，即在配置服务器或附加进程服务器上哪个最先达到。 在“本地摘要”部分可以找到每天总变动量和受保护磁盘总数的详细信息。

![Deployment Planner 中的所需本地基础结构](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>模拟分析
此项分析概述了如果设置较低的带宽，导致只能在 90% 的时间内满足所需 RPO，则在分析期间可能会发生多少项违规。 在任何给定的日期可能会发生一项或多项 RPO 违规。 下图显示了一天的峰值 RPO。
基于此分析，可以确定在使用指定的较低带宽的条件下，是否可以接受所有日期的 RPO 违规数以及每天的峰值 RPO。 如果可以接受，则可为复制分配较低的带宽，否则，应根据建议分配更高的带宽来满足 100% 时间的所需 RPO。

![Deployment Planner 中的模拟分析](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>用于初始复制的建议 VM 批大小
在本部分，我们提供了可以进行并行保护的 VM 数的建议，目的是在 72 小时内使用建议的带宽完成初始复制，该带宽满足所设置的 100% 时间的所需 RPO。 此值是可以配置的值。 若要在生成报告时对其进行更改，请使用“GoalToCompleteIR”参数。

下图显示了一系列带宽值和计算出的 VM 批大小计数，用于根据所有兼容 VM 上检测到的平均 VM 大小，在 72 小时内完成初始复制。

在公共预览版中，报告不会指定应在批中包含的具体 VM。 可以使用“兼容的 VM”部分显示的磁盘大小找出每个 VM 的大小，为批选择 VM，也可以根据已知的工作负荷特征选择 VM。 初始复制的完成时间根据实际 VM 磁盘大小、已用磁盘空间和可用的网络吞吐量按比例变化。

![建议的 VM 批大小](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>成本估算
下图显示的摘要视图包含灾难恢复 (DR) 到所选目标区域的 Azure 时的总估算成本，以及生成报表时所用的指定货币。

![成本估算摘要](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

可以通过此摘要了解在使用 Azure Site Recovery 将所有兼容的 VM 转移到 Azure 进行保护时，需要支付的存储、计算、网络和许可证成本。 计算成本时，只针对兼容的 VM，不针对所有受分析的 VM。  

可以按月或按年查看成本。 详细了解[支持的目标区域](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions)和[支持的货币](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)。

**按组件成本**：总 DR 成本分为四个部分：计算成本、存储成本、网络成本和 Azure Site Recovery 许可证成本。 成本计算基于在复制时和 DR 演练时上述四个部分（计算、存储（高级和标准）、在本地站点和 Azure 之间配置的 ExpressRoute/VPN，以及 Azure Site Recovery 许可证）所对应的使用量。

**按状态成本**：总灾难恢复 (DR) 成本按两种不同的状态（“复制”和“DR 演练”）分类。

**复制成本**：复制期间产生的成本， 涵盖存储成本、网络成本、Azure Site Recovery 许可证成本。

**DR 演练成本**：测试性故障转移期间产生的成本。 Azure Site Recovery 会在测试性故障转移期间启动 VM。 DR 演练成本涵盖正在运行的 VM 的计算和存储成本。

**每月/年的 Azure 存储成本**：表示针对高级和标准存储进行复制和 DR 演练时产生的总存储成本。
可以在[成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)表中查看每个 VM 的详细成本分析。

### <a name="growth-factor-and-percentile-values-used"></a>增长系数和使用的百分位值
工作表底部的此部分显示用于已分析 VM 的所有性能计数器的百分位值（默认为第 95 百分位），以及所有计算中使用的增长系数（默认为 30%）。

![增长系数和使用的百分位值](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>有关用作输入的可用带宽建议

![有关用作输入的可用带宽建议](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

有时，知道无法为 Site Recovery 复制设置 x Mbps 以上的带宽。 在该工具中可以输入可用带宽（生成报告时使用 -Bandwidth 参数），在数分钟内就能获得可实现的 RPO。 使用这个可实现的 RPO 值，可以确定是需要设置额外的带宽，还是可以使用某个灾难恢复解决方案来实现此 RPO。

![500 Mbps 带宽的可实现 RPO](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>VM-存储位置

![VM-存储位置](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**磁盘存储类型**：标准或高级存储帐户，用于复制“要放置的 VM”列中所述的所有相应 VM。

**建议的前缀**：建议的三字符前缀，可用于存储帐户的命名。 可以使用自己的前缀，而该工具的建议则遵循[存储帐户的分区命名约定](https://aka.ms/storage-performance-checklist)。

**建议的帐户名称**：在使用建议的前缀后出现的存储帐户名称。 将尖括号（< 和 >）中的名称替换为自定义输入。

**日志存储帐户**：所有复制日志存储在标准存储帐户中。 对于复制到高级存储帐户的 VM，请设置一个附加的标准存储帐户来进行日志存储。 单个标准日志存储帐户可由多个高级复制存储帐户使用。 复制到标准存储帐户的 VM 为日志使用相同的存储帐户。

**建议的日志帐户名称**：在使用建议的前缀后出现的存储日志帐户名称。 将尖括号（< 和 >）中的名称替换为自定义输入。

**位置摘要**：执行复制和测试性故障转移或故障转移时，存储帐户中 VM 总负载的摘要。 其中包括映射到存储帐户的 VM 总数、放置在此存储帐户中的所有 VM 上发生的读/写 IOPS 总数、写入（复制）IOPS 总数、所有磁盘的总设置大小，以及磁盘总数。

**要放置的虚拟机**：列出了为优化性能和使用而应放置在给定存储帐户中的所有 VM。

## <a name="compatible-vms"></a>兼容的 VM
![包含兼容 VM 的 Excel 电子表格](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**VM 名称**：VM 名称或 IP 地址，生成报告时在 VMListFile 中使用。 此列还列出附加到 VM 的磁盘 (VMDK)。 为了区分使用重复名称或 IP 地址的 vCenter VM，这些名称包含 ESXi 主机名称。 列出的 ESXi 主机是在其中放置了 VM 的主机，该 VM 是在分析期间通过工具发现后放置的。

**VM 兼容性**：值为“是”和“是\*”。  “是\*”针对 VM 适用于 [Azure 高级存储](https://aka.ms/premium-storage-workload)的情况。 在这里，所分析的高变动量或 IOPS 磁盘适合 P20 或 P30 类别，但考虑到磁盘大小，因此将其归入较低的 P10 或 P20 类别。 存储帐户决定了根据大小对磁盘分类时，可将磁盘归入哪种高级存储磁盘类型。 例如：
* <128 GB 为 P10。
* 128 GB 到 256 GB 为 P15
* 256 GB 到 512 GB 为 P20。
* 512 GB 到 1024 GB 为 P30。
* 1025 GB 到 2048 GB 为 P40。
* 2049 GB 到 4095 GB 为 P50。

例如，如果某个磁盘按工作负荷特征应归入 P20 或 P30 类别，但按大小应归入较低的高级存储磁盘类型，则此工具会将该 VM 标记为“是\*”。 该工具还建议根据建议的高级存储磁盘类型更改源磁盘大小，或者在故障转移后更改目标磁盘类型。

**存储类型**：标准或高级。

**建议的前缀**：由三个字符构成的存储帐户前缀。

**存储帐户**：此名称使用建议的存储帐户前缀。

**读/写 IOPS (包括增长系数)**：磁盘上的峰值工作负荷读/写 IOPS（默认为第 95 百分位），包括将来的增长系数（默认为 30%）。 请注意，VM 的读/写 IOPS 总数并非总是该 VM 各个磁盘的读/写 IOPS 之和，因为在分析期间，该 VM 每分钟的读/写 IOPS 峰值是其各个磁盘的读/写 IOPS 之和的峰值。

**以 Mbps 为单位的数据变动量(包括增长系数)**：磁盘上的峰值变动率（默认为第 95 百分位），包括将来的增长系数（默认为 30%）。 请注意，VM 的总数据变动量并非总是 VM 各个磁盘的数据变动量之和，因为 VM 的峰值数据变动量是该 VM 在分析期间每分钟的各个磁盘变动量之和的峰值。

**Azure VM 大小**：适用于此本地 VM 的映射 Azure 云服务虚拟机大小。 映射取决于本地 VM 的内存、磁盘/核心/NIC 数以及读/写 IOPS。 建议在符合所有本地 VM 特征的情况下，始终使用最小的 Azure VM 大小。

**磁盘数**：VM 上的虚拟机磁盘 (VMDK) 总数。

**磁盘大小(GB)**：VM 所有磁盘的总安装大小。 该工具还会显示 VM 中各个磁盘的磁盘大小。

**核心数**：VM 上的 CPU 核心数。

**内存(MB)**：VM 上的 RAM。

**NIC**：VM 上的 NIC 数。

**启动类型**：VM 的启动类型。 它可以是 BIOS 或 EFI。  目前，Azure Site Recovery 支持 Windows Server EFI VM（Windows Server 2012、2012 R2 和 2016），前提是启动盘中的分区数小于 4，引导扇区大小为 512 字节。 Azure Site Recovery 移动服务的版本最低必须为 9.13 才能保护 EFI VM。 EFI VM 仅支持故障转移。 不支持故障回复。  

**OS 类型**：即 VM 的 OS 类型。 可以是 Windows、Linux 或其他，具体取决于创建 VM 时从 VMware vSphere 中选择的模板。  

## <a name="incompatible-vms"></a>不兼容的 VM

![包含不兼容 VM 的 Excel 电子表格
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**VM 名称**：VM 名称或 IP 地址，生成报告时在 VMListFile 中使用。 此列还列出附加到 VM 的 VMDK。 为了区分使用重复名称或 IP 地址的 vCenter VM，这些名称包含 ESXi 主机名称。 列出的 ESXi 主机是在其中放置了 VM 的主机，该 VM 是在分析期间通过工具发现后放置的。

**VM 兼容性**：指示给定的 VM 为何无法与 Site Recovery 兼容使用。 会针对 VM 的每个不兼容磁盘说明原因，而根据已发布的[存储限制](https://aka.ms/azure-storage-scalbility-performance)，这些原因不外乎：

* 磁盘大小超出 4095 GB。 Azure 存储目前不支持大于 4095 GB 的数据磁盘大小。

* OS 磁盘大于 2048 GB。 Azure 存储目前不支持大于 2048 GB 的 OS 磁盘大小。

* VM 总大小（复制 + TFO）超出系统支持的存储帐户大小限制 (35 TB)。 当 VM 中的单个磁盘的性能特征超出系统支持的适用于标准存储的 Azure 或 Site Recovery 最大限制时，通常会表现出这种不兼容性。 如果出现这种情况，则必须将 VM 置于高级存储区域。 但是，高级存储帐户支持的最大大小为 35 TB，并且无法跨多个存储帐户保护单个需要保护的 VM。 另请注意，在受保护 VM 上执行测试性故障转移时，该故障转移运行时所使用的存储帐户与进行复制时所使用的存储帐户相同。 在这种情况下，可以在设置时会磁盘大小加倍，既可进行复制，又可成功地进行测试性故障转移。

* 源 IOPS 超出了每个磁盘支持的存储 IOPS 限制，即 7500。

* 源 IOPS 超出了每个 VM 支持的存储 IOPS 限制，即 80,000。

* 平均数据变动量超出了磁盘支持的 Site Recovery 数据变动量限制：平均 I/O 大小不能超过 10 MB/秒。

* 平均数据变动量超出了 VM 支持的 Site Recovery 数据变动量限制（所有磁盘变动量之和）：平均 I/O 大小不能超过 25 MB/秒。

* VM 中所有磁盘的峰值数据变动量超出了每个 VM 支持的最大 Site Recovery 峰值数据变动量限制，即 54 MB/秒。

* 平均有效写入 IOPS 超出了磁盘支持的 Site Recovery IOPS 限制，即 840。

* 计算出的快照存储超出了支持的快照存储限制，即 10 TB。

* 每日总数据变动量超出了进程服务器支持的每日变动量限制，即 2 TB。


**峰值读/写 IOPS (包括增长系数)**：磁盘上的峰值工作负荷 IOPS（默认为第 95 百分位），包括将来的增长系数（默认为 30%）。 请注意，VM 的读/写 IOPS 总数并非总是该 VM 各个磁盘的读/写 IOPS 之和，因为在分析期间，该 VM 每分钟的读/写 IOPS 峰值是其各个磁盘的读/写 IOPS 之和的峰值。

**以 Mbps 为单位的峰值数据变动量(包括增长系数)**：磁盘上的峰值变动率（默认为第 95 百分位），包括将来的增长系数（默认为 30%）。 请注意，VM 的总数据变动量并非总是 VM 各个磁盘的数据变动量之和，因为 VM 的峰值数据变动量是该 VM 在分析期间每分钟的各个磁盘变动量之和的峰值。

**磁盘数**：VM 上的 VMDK 总数。

**磁盘大小(GB)**：VM 所有磁盘的总安装大小。 该工具还会显示 VM 中各个磁盘的磁盘大小。

**核心数**：VM 上的 CPU 核心数。

**内存(MB)**：VM 上的 RAM 量。

**NIC**：VM 上的 NIC 数。

**启动类型**：VM 的启动类型。 它可以是 BIOS 或 EFI。  目前，Azure Site Recovery 支持 Windows Server EFI VM（Windows Server 2012、2012 R2 和 2016），前提是启动盘中的分区数小于 4，引导扇区大小为 512 字节。 Azure Site Recovery 移动服务的版本最低必须为 9.13 才能保护 EFI VM。 EFI VM 仅支持故障转移。 不支持故障回复。

**OS 类型**：即 VM 的 OS 类型。 可以是 Windows、Linux 或其他，具体取决于创建 VM 时从 VMware vSphere 中选择的模板。

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery 限制
下表提供了 Azure Site Recovery 限制。 这些限制基于我们的测试，但无法涵盖所有可能的应用程序 I/O 组合。 实际结果可能因应用程序 I/O 组合而异。 为获得最佳结果，我们始终建议通过测试性故障转移执行广泛的应用程序测试，获取应用程序的真实性能视图，即使在规划部署后，也应如此。

**复制存储目标** | **平均源磁盘 I/O 大小** |**平均源磁盘数据变动量** | **每天的总源磁盘数据变动量**
---|---|---|---
标准存储 | 8 KB | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 8 KB  | 2 MB/秒 | 每个磁盘 168 GB
高级 P10 或 P15 磁盘 | 16 KB | 4 MB/秒 |  每个磁盘 336 GB
高级 P10 或 P15 磁盘 | 至少 32 KB | 8 MB/秒 | 每个磁盘 672 GB
高级 P20、P30、P40 或 P50 磁盘 | 8 KB    | 5 MB/秒 | 每个磁盘 421 GB
高级 P20、P30、P40 或 P50 磁盘 | 至少 16 KB |10 MB/秒 | 每个磁盘 842 GB

**源数据变动量** | **最大限制**
---|---
每个 VM 的平均数据变动量| 25 MB/秒
VM 上所有磁盘的峰值数据变动量 | 54 MB/秒
进程服务器支持的每日最大数据变动量 | 2 TB

这是在假设存在 30% 的 I/O 重叠的情况下给出的平均数。 Site Recovery 能够根据重叠率、较大的写入大小和实际工作负荷 I/O 行为处理更高的吞吐量。 上述数字假定通常情况下存在大约 5 分钟的积压工作。 也就是说，数据在上传后会在 5 分钟内进行处理并创建恢复点。


## <a name="cost-estimation"></a>成本估算
详细了解[成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)。


## <a name="next-steps"></a>后续步骤
详细了解[成本估算](site-recovery-vmware-deployment-planner-cost-estimation.md)。
