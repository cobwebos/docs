---
title: Azure Migrate 中的评估和依赖项可视化的疑难解答
description: 在 Azure Migrate 中获取有关评估和依赖项可视化的帮助。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: e9b9e0b27e46c95ca4c2bc3a5c547259bada5fda
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685402"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>排查评估/依赖项可视化问题

本文将帮助你解决与 [Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)有关的评估和依赖项可视化问题。


## <a name="assessment-readiness-issues"></a>评估就绪问题

解决评估准备情况问题，如下所示：

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持具有 EFI 启动类型的虚拟机。 在运行迁移之前，建议将启动类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 Vm 的迁移。 在迁移过程中，它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 操作系统已超过其支持的截止日期，需要自定义支持协议 (CSA) ，以 [支持 Azure 中的支持](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级。
不受支持的 Windows 操作系统 | Azure 仅支持 [所选的 WINDOWS 操作系统版本](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级计算机。
有条件认可的 Linux OS | Azure 予以认可仅 [选择 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级计算机。 有关更多详细信息，请参阅 [此处](https://docs.microsoft.com/azure/migrate/troubleshoot-assessment#linux-vms-are-conditionally-ready-in-an-azure-vm-assessment) 。
未经认可的 Linux OS | 计算机可能会在 Azure 中启动，但 Azure 不提供操作系统支持。 在迁移到 Azure 之前，请考虑升级到 [认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md) 。
未知操作系统 | 在 vCenter Server 中，将 VM 的操作系统指定为 "其他"。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 准备情况。 在迁移计算机之前，请确保 Azure [支持](https://aka.ms/azureoslist) 该操作系统。
不支持的位版本 | 具有32位操作系统的 Vm 可能会在 Azure 中启动，但建议在迁移到 Azure 之前升级到64位。
需要 Microsoft Visual Studio 订阅 | 计算机正在运行 Windows 客户端操作系统，该系统仅通过 Visual Studio 订阅支持。
找不到所需存储性能的 VM | 计算机所需的每秒输入/输出操作数（IOPS）和吞吐量) 的存储性能 (超过了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
找不到所需网络性能的 VM | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定位置找不到 VM | 在迁移之前使用不同目标位置。
一个或多个不合适的磁盘 | 附加到 VM 的一个或多个磁盘不满足 Azure 要求。的<br/><br/> Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并根据高级托管磁盘的磁盘限制 (32 TB) 评估磁盘。<br/><br/> 对于附加到 VM 的每个磁盘，请确保磁盘大小 < 64 TB (超级 SSD 磁盘) 支持。<br/><br/> 如果不是这样，请在迁移到 Azure 之前减小磁盘大小，或者在 Azure 中使用多个磁盘并将它们放在 [一起](../virtual-machines/premium-storage-performance.md#disk-striping) ，以获得更高的存储限制。 请确保 Azure [托管的虚拟机磁盘](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits)支持每个磁盘所需的性能 (IOPS 和吞吐量) 。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并根据高级磁盘限制 (32 TB) 评估磁盘。<br/><br/> 但是，Azure 支持的磁盘大小最高可达 64 TB () 超级 SSD 磁盘。 在迁移之前将磁盘缩减为小于 64 TB，或在 Azure 中使用多个磁盘并将它们放在 [一起](../virtual-machines/premium-storage-performance.md#disk-striping) ，以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误，无法确定磁盘的适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 找不到合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误，无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。
找不到产品/服务币种预订实例的 VM 大小 | 标记为 "不适用" 的计算机，原因是找不到所选 RI、产品/服务和货币组合的 VM 大小。 编辑评估属性以选择有效的组合，并重新计算评估。 
有条件地准备好 Internet 协议 | 仅适用于 Azure VMware 解决方案 (AVS) 评估。 AVS 不支持 IPv6 internet 地址因素。如果检测到你的计算机有 IPv6，请联系 AVS 团队以获取补救指导。

## <a name="suggested-migration-tool-in-import-based-avs-assessment-marked-as-unknown"></a>在基于导入的 AVS 评估中标记为未知的建议迁移工具

对于通过 CSV 文件导入的计算机，和 AVS 评估中的默认迁移工具是未知的。 但对于 VMware 计算机，建议使用 VMware 混合云扩展 (HCX) 解决方案。 [了解详细信息](../azure-vmware/hybrid-cloud-extension-installation.md)。

## <a name="linux-vms-are-conditionally-ready-in-an-azure-vm-assessment"></a>Linux Vm 在 Azure VM 评估中 "有条件地就绪"

对于 VMware 和 Hyper-v Vm，服务器评估会将 Linux Vm 标记为 "有条件就绪"，因为服务器评估中有一个已知的间隔。 

- 该间隔使其无法检测本地 Vm 上安装的 Linux 操作系统的次版本。
- 例如，对于 RHEL 6.10，当前服务器评估只检测到 RHEL 6 作为操作系统版本。 这是因为 vCenter Server ar，Hyper-v 主机不提供 Linux VM 操作系统的内核版本。
-  由于 Azure 予以认可仅特定版本的 Linux，因此 Linux Vm 当前在服务器评估中被标记为有条件准备就绪。
- 可以通过查看 [Azure Linux 支持](https://aka.ms/migrate/selfhost/azureendorseddistros)来确定本地 VM 上运行的 Linux 操作系统是否已在 azure 中认可。
-  验证了认可的分发后，可以忽略此警告。

可以通过在 VMware Vm 上启用 [应用程序发现](./how-to-discover-applications.md) 来解决这种缺口。 服务器评估通过提供的来宾凭据来使用从 VM 中检测到的操作系统。 在 Windows 和 Linux Vm 情况下，此操作系统数据会标识正确的操作系统信息。

## <a name="operating-system-version-not-available"></a>操作系统版本不可用

对于物理服务器，操作系统次要版本信息应该可用。 如果不可用，请联系 Microsoft 支持部门。 对于 VMware 计算机，服务器评估使用在 vCenter Server 中为 VM 指定的操作系统信息。 但 vCenter Server 不提供操作系统的次版本。 若要发现次要版本，需要设置 [应用程序发现](./how-to-discover-applications.md)。 对于 Hyper-v Vm，不支持操作系统次要版本发现。 

## <a name="azure-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>Azure Sku 大于本地的 Azure VM 评估

基于评估类型，Azure Migrate Server 评估可能会推荐 Azure VM Sku，其中包含的内核和内存比当前本地分配更多：

- VM SKU 建议取决于评估属性。
- 这受您在 "服务器评估" 中执行的评估类型的影响： " *基于性能*" 或 *"本地*"。
- 对于基于性能的评估，服务器评估将 (CPU、内存、磁盘和网络利用率) 本地 Vm 的使用率数据，以确定本地 Vm 的正确目标 VM SKU。 在确定有效利用率时，它还添加了舒适因子。
- 对于本地大小调整，不考虑性能数据，建议根据本地分配使用目标 SKU。

为了说明这会如何影响建议，我们举个例子：

我们有一个具有四个核心和 8 GB 内存的本地 VM，其 CPU 50 使用率为50%，内存使用率为%，指定的舒适系数为1.3。

-  如果评估为 **"本地**"，则建议使用具有四个核心和 8 GB 内存的 AZURE VM SKU。
- 如果评估基于有效的 CPU 和内存利用率，则根据有效的 CPU 和内存利用率 (50% 的4个内核 * 1.3 = 2.6 个核心和50% 的 8 GB 内存 * 1.3 = 5.3 =) ，最便宜的四核 VM SKU (最接近支持的核心计数) 和 8 GB 内存 (最接近支持的内存大小) 建议使用。
- [详细了解](concepts-assessment-calculation.md#types-of-assessments) 评估大小。

## <a name="why-is-the-recommended-azure-disk-skus-bigger-than-on-premises-in-an-azure-vm-assessment"></a>为什么建议 Azure 磁盘 Sku 比 Azure VM 评估中的本地更大？

Azure Migrate Server 评估可能会根据评估类型建议更大的磁盘。
- 服务器评估中的磁盘大小调整取决于两个评估属性：大小调整条件和存储类型。
- 如果大小调整条件是 **基于性能**的，并且存储类型设置为 " **自动**"，则在标识目标磁盘类型 (标准 HDD、标准 SSD 或高级) 时，将考虑磁盘的 IOPS 和吞吐量值。 然后建议使用磁盘类型的磁盘 SKU，建议考虑本地磁盘的大小要求。
- 如果大小调整条件 **基于性能**并且存储类型为 " **高级**"，则建议使用 Azure 中的高级磁盘 SKU，根据本地磁盘的 IOPS、吞吐量和大小需求进行推荐。 当大小调整条件为 **本地** 且存储类型为 **标准 HDD**、 **标准 SSD**或 **高级**时，将使用相同的逻辑来执行磁盘大小调整。

例如，如果你有一个具有 32 GB 内存的本地磁盘，但该磁盘的聚合读取和写入 IOPS 为 800 IOPS，则服务器评估建议 (高级磁盘，因为) 的 IOPS 要求较高，然后建议一个可支持所需 IOPS 和大小的磁盘 SKU。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 尽管本地磁盘所需的大小为 32 GB，服务器评估建议使用较大的磁盘，因为本地磁盘的 IOPS 要求较高。

## <a name="why-is-performance-data-missing-for-someall-vms-in-my-assessment-report"></a>为什么评估报告中的某些/所有 VM 缺少性能数据？

对于“基于性能”的评估，当 Azure Migrate 设备无法收集本地 VM 的性能数据时，评估报告导出会显示“PercentageOfCoresUtilizedMissing”或“PercentageOfMemoryUtilizedMissing”。 请检查：

- 是否在创建评估期间启动了 VM
- 如果只有内存计数器缺失，而你尝试评估 Hyper-V VM，则请检查是否在这些 VM 上启用了动态内存。 目前有一个已知问题，由于该问题的存在，Azure Migrate 设备无法收集此类 VM 的内存利用率。
- 如果所有性能计数器都丢失，请确保满足评估的端口访问要求。 详细了解 [VMware](./migrate-support-matrix-vmware.md#port-access-requirements)、 [hyper-v](./migrate-support-matrix-hyper-v.md#port-access) 和 [物理](./migrate-support-matrix-physical.md#port-access) 服务器评估的端口访问要求。
请注意，如果缺少任何性能计数器，则 Azure Migrate：服务器评估会回退到本地分配的核心/内存，并相应地建议一个 VM 大小。

## <a name="why-is-the-confidence-rating-of-my-assessment-low"></a>为什么我的评估的置信度分级较低？

根据计算评估所需的[可用数据点](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#ratings)的百分比，为“基于性能”的评估计算置信度评级。 下面是为什么评估可能会获得较低置信度分级的原因：

- 在创建评估的过程中，你没有对环境进行分析。 例如，如果创建性能持续时间设置为一周的评估，则在对所有数据点启用发现之后，需要等待至少一周才能收集。 如果无法等待这么久，请将性能持续时间缩短，并“重新计算”评估。
 
- 服务器评估无法在评估期内收集部分或全部 VM 的性能数据。 请检查是否已在评估期间启用 VM 且允许通过端口 443 进行出站连接。 对于 Hyper-V VM，如果已启用了动态内存，则内存计数器将缺失，导致置信度分级较低。 请重新计算评估以反映置信度评级的最新更改。 

- 启动服务器评估中的发现之后，基本不再创建 VM。 例如，如果要针对最后一个月的性能历史记录创建评估，但仅仅在一周前，在环境中创建了一些 VM， 在这种情况下，新 VM 的性能数据在整个过程中都不可用，并且置信度分级会较低。

[详细了解](https://docs.microsoft.com/azure/migrate/concepts-assessment-calculation#confidence-ratings-performance-based)置信度分级。

## <a name="is-the-operating-system-license-included-in-an-azure-vm-assessment"></a>Azure VM 评估中是否包含操作系统许可证？

Azure Migrate Server 评估目前只考虑 Windows 计算机的操作系统许可成本。 目前未考虑 Linux 计算机的许可成本。

## <a name="how-does-performance-based-sizing-work-in-an-azure-vm-assessment"></a>基于性能的大小调整如何在 Azure VM 评估中工作？

服务器评估不断收集本地计算机的性能数据，并使用它来针对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 [了解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based) 收集基于性能的数据。

## <a name="why-is-my-assessment-showing-a-warning-that-it-was-created-with-an-invalid-combination-of-reserved-instances-vm-uptime-and-discount-"></a>为什么我的评估显示一个警告，指出它是通过无效的预订实例组合、VM 运行时间和折扣 (% ) 来创建的？
选择 "保留实例" 时，"折扣 (% ) " 和 "VM 运行时间" 属性不适用。 当你的评估创建时使用了这些属性的无效组合时，"编辑" 和 "重新计算" 按钮会被禁用。 请创建新的评估。 [了解详细信息](https://go.microsoft.com/fwlink/?linkid=2131554)。

## <a name="i-do-not-see-performance-data-for-some-network-adapters-on-my-physical-servers"></a>我看不到物理服务器上的某些网络适配器的性能数据

如果物理服务器启用了 Hyper-v 虚拟化，则会发生这种情况。 在这些服务器上，由于产品缺口，Azure Migrate 当前同时发现物理和虚拟网络适配器。 仅捕获虚拟网络适配器上的网络吞吐量。

## <a name="recommended-azure-vm-sku-for-my-physical-server-is-oversized"></a>物理服务器的建议 Azure VM SKU 太大

如果物理服务器启用了 Hyper-v 虚拟化，则会发生这种情况。 在这些服务器上，Azure Migrate 当前同时发现物理和虚拟网络适配器。 因此，不会。 发现的网络适配器的数量高于实际值。 服务器评估选择的 Azure VM 可支持所需数量的网络适配器，这可能会导致超大 VM。 [了解](./concepts-assessment-calculation.md#calculating-sizing) 有关 "否" 的影响的详细信息。 大小上的网络适配器。 这是未来将解决的产品缺口。

## <a name="readiness-category-not-ready-for-my-physical-server"></a>适用于物理服务器的准备情况类别 "未就绪"

如果启用了 Hyper-v 虚拟化的物理服务器，则准备情况类别可能会错误地标记为 "未就绪"。 在这些服务器上，由于产品缺口，Azure Migrate 当前同时发现物理适配器和虚拟适配器。 因此，不会。 发现的网络适配器的数量高于实际值。 在本地和基于性能的评估中，服务器评估将选择可支持所需数量的网络适配器的 Azure VM。 如果发现的网络适配器数量大于32，则最大值为 "否"。 对于 Azure Vm 支持的 Nic，该计算机将被标记为 "未就绪"。  [了解](./concepts-assessment-calculation.md#calculating-sizing) 有关 "否" 的影响的详细信息。 大小的 Nic。


## <a name="number-of-discovered-nics-higher-than-actual-for-physical-servers"></a>已发现的 Nic 数高于物理服务器的实际 Nic 数

如果物理服务器启用了 Hyper-v 虚拟化，则会发生这种情况。 在这些服务器上，Azure Migrate 当前同时发现物理适配器和虚拟适配器。 因此，不会。 发现的 Nic 比实际数量高。

## <a name="dependency-visualization-in-azure-government"></a>Azure 政府版中的依赖项可视化

Azure Migrate 依赖于依赖项可视化功能服务映射。 由于服务映射当前在 Azure 政府版中不可用，因此此功能在 Azure 政府版中不可用。

## <a name="dependencies-dont-show-after-agent-install"></a>代理安装后不显示依赖关系

在本地 Vm 上安装了依赖关系可视化代理后，Azure Migrate 通常需要15-30 分钟的时间才能在门户中显示依赖项。 如果等待超过30分钟，请确保 Microsoft Monitoring Agent (MMA) 可以连接到 Log Analytics 工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
2. 在**Microsoft Monitoring Agent 属性**"  >  **Azure Log Analytics (OMS) **中，确保工作区的**状态**为绿色。
3. 如果状态不是绿色，请尝试删除工作区，并再次将其添加到 MMA。

    ![MMA 状态](./media/troubleshoot-assessment/mma-properties.png)

对于 Linux Vm，请确保 MMA 和依赖关系代理的安装命令已成功。

## <a name="supported-operating-systems"></a>支持的操作系统

- **MMS 代理**：查看支持的 [Windows](../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems)和 [Linux](../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) 操作系统。
- **依赖关系代理**：支持的 [Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems) 操作系统。

## <a name="visualize-dependencies-for--hour"></a>可视化 > 小时的依赖项

使用无代理依赖项分析时，可以可视化依赖项或将其导出到地图中，持续时间最长为30天。

使用基于代理的依赖项分析时，尽管 Azure Migrate 允许您返回到上个月的特定日期，但您可以可视化依赖项的最长持续时间为一小时。 例如，你可以使用 "依赖关系映射" 中的 "持续时间" 功能来查看昨天的依赖项，但你可以仅查看一小时的时间段。 但是，可以使用 Azure Monitor 日志来查询更长时间 [的依赖项数据](./how-to-create-group-machine-dependencies.md) 。

## <a name="visualized-dependencies-for--10-machines"></a>可视化 > 10 计算机的依赖项

在 Azure Migrate 服务器评估中，通过基于代理的依赖项分析，可以可视化最多包含10个 Vm [的组的依赖项](./how-to-create-a-group.md#refine-a-group-with-dependency-mapping) 。 对于更大的组，我们建议将 Vm 拆分为较小的组，以可视化依赖项。


## <a name="machines-show-install-agent"></a>计算机显示 "安装代理"

将启用了依赖关系可视化的计算机迁移到 Azure 后，计算机可能会显示 "安装代理" 操作，而不是 "查看依赖项"，原因如下：


- 迁移到 Azure 后，本地计算机将关闭，并在 Azure 中启动等效 Vm。 这些计算机将获得一个不同的 MAC 地址。
- 计算机还可能具有不同的 IP 地址，具体取决于是否保留了本地 IP 地址。
- 如果 MAC 和 IP 地址与本地不同，Azure Migrate 不会将本地计算机与任何服务映射的依赖关系数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项。
- 在测试迁移到 Azure 后，本地计算机仍按预期方式开启。 在 Azure 中启动的等效计算机获取不同的 MAC 地址，并可能获取不同的 IP 地址。 除非您阻止来自这些计算机的传出 Azure Monitor 日志流量，否则 Azure Migrate 不会将本地计算机与任何服务映射依赖关系数据相关联，因此将显示安装代理的选项，而不是查看依赖项。

## <a name="dependencies-export-csv-shows-unknown-process"></a>依赖项导出 CSV 显示 "未知进程"
在无代理依赖项分析中，会尽力地捕获进程名称。 在某些情况下，尽管将捕获源和目标服务器名称以及目标端口，但在依赖项的两端确定进程名称是不可行的。 在这种情况下，该过程将被标记为 "未知进程"。

## <a name="my-log-analytics-workspace-is-not-listed-when-trying-to-configure-the-workspace-in-server-assessment"></a>尝试在服务器评估中配置工作区时，未列出我的 Log Analytics 工作区
Azure Migrate 目前支持在“美国东部”、“东南亚”和“西欧”区域创建 OMS 工作区。 如果工作区是在任何其他区域的 Azure Migrate 之外创建的，则当前无法与 Azure Migrate 项目关联。


## <a name="capture-network-traffic"></a>捕获网络流量

收集网络流量日志，如下所示：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 按 F12 开始开发人员工具。 如果需要，请清除 "  **在导航上清除条目** " 设置。
3. 选择 " **网络** " 选项卡，开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”****。 记录应自动启动。 红色圆圈表示正在捕获流量。 如果未显示红色圆圈，请选择要开始的黑色圆圈。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果没有，请选择绿色的 "播放" 按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择 " **另存为 HAR 内容**"。 此操作将日志压缩并导出为 har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择 " **导出捕获的流量** " 选项。 此操作将压缩并导出日志。
6. 选择 " **控制台** " 选项卡以检查是否有任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 " **另存为**"、"导出" 和 "压缩日志"。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择 " **全部复制**"。
7. 关闭“开发人员工具”。


## <a name="where-is-the-operating-system-data-in-my-assessment-discovered-from"></a>从何处发现我的评估中的操作系统数据？

- 对于 VMware Vm，默认情况下，它是 vCenter 提供的操作系统数据。 
   - 对于 VMware linux Vm，如果启用了应用程序发现，则从来宾 VM 提取 OS 详细信息。 若要检查评估中的哪些 OS 详细信息，请前往 "发现的服务器" 视图，然后将鼠标悬停在 "操作系统" 列中的值上。 在弹出的文本中，你将能够看到你看到的 OS 数据是从 vCenter 服务器还是使用 VM 凭据从来宾 VM 中收集的。 
   - 对于 Windows Vm，将始终从 vCenter Server 获取操作系统详细信息。
- 对于 Hyper-v Vm，操作系统数据是从 Hyper-v 主机上收集的
- 对于物理服务器，它是从服务器中提取的。

## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md) 或 [自定义](how-to-modify-assessment.md) 评估。
