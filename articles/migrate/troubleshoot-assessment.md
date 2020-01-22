---
title: Azure Migrate 中的评估和依赖项可视化的疑难解答
description: 在 Azure Migrate 中获取有关评估和依赖项可视化的帮助。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: fb1e32d9f67febb09eadfb5d31221db33504eb05
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289469"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>排查评估/依赖项可视化问题

本文将帮助你解决与[Azure Migrate：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)有关的评估和依赖项可视化问题。


## <a name="assessment-readiness-issues"></a>评估就绪问题

解决评估准备情况问题，如下所示：

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持具有 EFI 启动类型的虚拟机。 在运行迁移之前，建议将启动类型转换为 BIOS。 <br/><br/>你可以使用 Azure Migrate 服务器迁移来处理此类 Vm 的迁移。 在迁移过程中，它会将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 操作系统已超过其支持的截止日期，需要自定义支持协议（CSA）以[支持 Azure 中的支持](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级。
不受支持的 Windows 操作系统 | Azure 仅支持[所选的 WINDOWS 操作系统版本](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级计算机。
有条件认可的 Linux OS | Azure 予以认可仅[选择 LINUX 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级计算机。
未经认可的 Linux OS | 计算机可能会在 Azure 中启动，但 Azure 不提供操作系统支持。 在迁移到 Azure 之前，请考虑升级到[认可的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | 在 vCenter Server 中，将 VM 的操作系统指定为 "其他"。 此行为会阻止 Azure Migrate 验证 VM 的 Azure 准备情况。 在迁移计算机之前，请确保 Azure[支持](https://aka.ms/azureoslist)该操作系统。
不支持的位版本 | 具有32位操作系统的 Vm 可能会在 Azure 中启动，但建议在迁移到 Azure 之前升级到64位。
需要 Microsoft Visual Studio 订阅 | 计算机正在运行 Windows 客户端操作系统，该系统仅通过 Visual Studio 订阅支持。
找不到所需存储性能的 VM | 计算机所需的存储性能（每秒输入/输出操作数 [IOPS] 和吞吐量）超过了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
找不到所需网络性能的 VM | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定位置找不到 VM | 在迁移之前使用不同目标位置。
一个或多个不合适的磁盘 | 附加到 VM 的一个或多个磁盘不满足 Azure 要求。的<br/><br/> Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并根据高级托管磁盘（32 TB）的磁盘限制来评估磁盘。<br/><br/> 对于附加到 VM 的每个磁盘，请确保磁盘大小为 < 64 TB （超级 SSD 磁盘支持）。<br/><br/> 如果不是这样，请在迁移到 Azure 之前减小磁盘大小，或者在 Azure 中使用多个磁盘并将它们放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以获得更高的存储限制。 确保 Azure[托管的虚拟机磁盘](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)支持每个磁盘所需的性能（IOPS 和吞吐量）。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | Azure Migrate：服务器评估目前不支持超级 SSD 磁盘，并基于高级磁盘限制（32 TB）评估磁盘。<br/><br/> 但是，Azure 支持最大为 64 TB 大小的磁盘（超级 SSD 磁盘支持）。 在迁移之前将磁盘缩减为小于 64 TB，或在 Azure 中使用多个磁盘并将它们放在[一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误，无法确定磁盘的适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 找不到合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误，无法确定 VM 适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于出现内部错误，无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。

## <a name="linux-vms-are-conditionally-ready"></a>Linux Vm 的 "有条件准备"

服务器评估将 Linux Vm 标记为 "有条件就绪"，因为服务器评估中有一个已知的间隔。

- 该间隔使其无法检测本地 Vm 上安装的 Linux 操作系统的次版本。
- 例如，对于 RHEL 6.10，当前服务器评估只检测到 RHEL 6 作为操作系统版本。
-  由于 Azure 予以认可仅特定版本的 Linux，因此 Linux Vm 当前在服务器评估中被标记为有条件准备就绪。
- 可以通过查看[Azure Linux 支持](https://aka.ms/migrate/selfhost/azureendorseddistros)来确定本地 VM 上运行的 Linux 操作系统是否已在 azure 中认可。
-  验证了认可的分发后，可以忽略此警告。

## <a name="azure-skus-bigger-than-on-premises"></a>Azure Sku 大于本地

基于评估类型，Azure Migrate Server 评估可能会推荐 Azure VM Sku，其中包含的内核和内存比当前本地分配更多：


- VM SKU 建议取决于评估属性。
- 这受您在 "服务器评估" 中执行的评估类型的影响： "*基于性能*" 或 *"本地*"。
- 对于基于性能的评估，服务器评估会考虑本地 Vm 的使用率数据（CPU、内存、磁盘和网络使用率），以确定本地 Vm 的正确目标 VM SKU。 它还在确定有效利用率时增加了舒适的因素。
- 对于本地大小调整，不考虑性能数据，建议根据本地分配使用目标 SKU。

为了说明这会如何影响建议，我们举个例子：

我们有一个具有四个核心和 8 GB 内存的本地 VM，其 CPU 50 使用率为50%，内存使用率为%，指定的舒适系数为1.3。

-  如果评估为 **"本地**"，则建议使用具有四个核心和 8 GB 内存的 AZURE VM SKU。
- 如果评估基于性能，则基于有效的 CPU 和内存利用率（4个内核的 50% * 1.3 = 2.6 个核心和 8 GB 内存的 50% * 1.3 = 5.3 = = =）、四个核心的最便宜 VM SKU （最接近支持的核心数）和 8 GB 内存（最接近支持内存大小）。
- [详细了解](concepts-assessment-calculation.md#assessments-in-server-assessment)评估大小。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure 磁盘 Sku 大于本地

Azure Migrate Server 评估可能会根据评估类型建议更大的磁盘。
- 服务器评估中的磁盘大小调整取决于两个评估属性：大小调整条件和存储类型。
- 如果大小调整条件是**基于性能**的，并且存储类型设置为 "**自动**"，则在标识目标磁盘类型（标准 HDD、标准 SSD 或高级）时，将考虑磁盘的 IOPS 和吞吐量值。 然后建议使用磁盘类型的磁盘 SKU，建议考虑本地磁盘的大小要求。
- 如果大小调整条件**基于性能**并且存储类型为 "**高级**"，则建议使用 Azure 中的高级磁盘 SKU，根据本地磁盘的 IOPS、吞吐量和大小需求进行推荐。 当大小调整条件为**本地**且存储类型为**标准 HDD**、**标准 SSD**或**高级**时，将使用相同的逻辑来执行磁盘大小调整。

例如，如果你有一个具有 32 GB 内存的本地磁盘，但该磁盘的聚合读取和写入 IOPS 为 800 IOPS，则服务器评估建议使用高级磁盘（由于 IOPS 要求较高），然后建议一个可支持 r 的磁盘 SKUe) IOPS 和大小。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 尽管本地磁盘所需的大小为 32 GB，服务器评估建议使用较大的磁盘，因为本地磁盘的 IOPS 要求较高。

## <a name="utilized-corememory-percentage-missing"></a>已使用的核心/内存百分比丢失

当 Azure Migrate 设备无法收集相关本地 Vm 的性能数据时，服务器评估将报告 "PercentageOfCoresUtilizedMissing" 或 "PercentageOfMemoryUtilizedMissing"。

- 如果 Vm 在评估期间关闭，则会发生这种情况。 当 VM 关闭时，它无法收集 VM 的性能数据。
- 如果仅缺少内存计数器，而你要尝试评估 Hyper-v Vm，请检查是否已在这些 Vm 上启用动态内存。 Hyper-v Vm 仅有一个已知问题，在这种情况下，Azure Migrate 设备无法为未启用动态内存的 Vm 收集内存使用率数据。
- 如果缺少任何性能计数器，Azure Migrate 服务器评估将回退到分配的内核和内存，并建议相应的 VM 大小。
- 如果所有性能计数器都丢失，请确保满足评估的端口访问要求。 详细了解[VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access)、 [hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access)和[物理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)服务器评估的端口访问要求。

## <a name="is-the-operating-system-license-included"></a>是否包含操作系统许可证？

Azure Migrate Server 评估目前只考虑 Windows 计算机的操作系统许可成本。 目前未考虑 Linux 计算机的许可成本。

## <a name="how-does-performance-based-sizing-work"></a>基于性能的大小调整如何工作？

服务器评估不断收集本地计算机的性能数据，并使用它来针对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 [了解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集基于性能的数据。


## <a name="dependency-visualization-in-azure-government"></a>Azure 政府版中的依赖项可视化

Azure Migrate 依赖于依赖项可视化功能服务映射。 由于服务映射当前在 Azure 政府版中不可用，因此此功能在 Azure 政府版中不可用。

## <a name="dependencies-dont-show-after-agent-install"></a>代理安装后不显示依赖关系

在本地 Vm 上安装了依赖关系可视化代理后，Azure Migrate 通常需要15-30 分钟的时间才能在门户中显示依赖项。 如果等待超过30分钟，请确保 Microsoft Monitoring Agent （MMA）可以连接到 Log Analytics 工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
2. 在 > **Azure Log Analytics （OMS）** **Microsoft Monitoring Agent 属性**中，确保工作区的**状态**为绿色。
3. 如果状态不是绿色，请尝试删除工作区，并再次将其添加到 MMA。

    ![MMA 状态](./media/troubleshoot-assessment/mma-properties.png)

对于 Linux Vm，请确保 MMA 和依赖关系代理的安装命令已成功。

## <a name="supported-operating-systems"></a>支持的操作系统

- **MMS 代理**：查看支持的[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)和[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)操作系统。
- **依赖关系代理**：支持的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)操作系统。

## <a name="visualize-dependencies-for--hour"></a>可视化 > 小时的依赖项

尽管 Azure Migrate 允许您返回到上个月的特定日期，但您可以对依赖项进行可视化的最长持续时间为一小时。

例如，你可以使用 "依赖关系映射" 中的 "持续时间" 功能来查看昨天的依赖项，但你可以仅查看一小时的时间段。

但是，可以使用 Azure Monitor 日志来查询更长时间[的依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="visualized-dependencies-for--10-machines"></a>可视化 > 10 计算机的依赖项

在 Azure Migrate 服务器评估中，可以可视化最多包含10个 Vm[的组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 对于更大的组，我们建议将 Vm 拆分为较小的组，以可视化依赖项。

## <a name="machines-show-install-agent"></a>计算机显示 "安装代理"

将启用了依赖关系可视化的计算机迁移到 Azure 后，计算机可能会显示 "安装代理" 操作，而不是 "查看依赖项"，原因如下：


- 迁移到 Azure 后，本地计算机将关闭，并在 Azure 中启动等效 Vm。 这些计算机将获得一个不同的 MAC 地址。
- 计算机还可能具有不同的 IP 地址，具体取决于是否保留了本地 IP 地址。
- 如果 MAC 和 IP 地址与本地不同，Azure Migrate 不会将本地计算机与任何服务映射的依赖关系数据相关联。 在这种情况下，它将显示安装代理的选项，而不是查看依赖项。
- 在测试迁移到 Azure 后，本地计算机仍按预期方式开启。 在 Azure 中启动的等效计算机获取不同的 MAC 地址，并可能获取不同的 IP 地址。 除非您阻止来自这些计算机的传出 Azure Monitor 日志流量，否则 Azure Migrate 不会将本地计算机与任何服务映射依赖关系数据相关联，因此将显示安装代理的选项，而不是查看依赖项。


## <a name="capture-network-traffic"></a>捕获网络流量

收集网络流量日志，如下所示：

1. 登录 [Azure 门户](https://portal.azure.com)。
2. 按 F12 开始开发人员工具。 如果需要，请清除 "**在导航上清除条目**" 设置。
3. 选择 "**网络**" 选项卡，开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”。 记录应自动启动。 红色圆圈表示正在捕获流量。 如果未显示红色圆圈，请选择要开始的黑色圆圈。
   - 在 Microsoft Edge 和 Internet Explorer 中，记录应自动启动。 如果没有，请选择绿色的 "播放" 按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择 "**另存为 HAR 内容**"。 此操作将日志压缩并导出为 har 文件。
   - 在 Microsoft Edge 或 Internet Explorer 中，选择 "**导出捕获的流量**" 选项。 此操作将压缩并导出日志。
6. 选择 "**控制台**" 选项卡以检查是否有任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 "**另存为**"、"导出" 和 "压缩日志"。
   - 在 Microsoft Edge 或 Internet Explorer 中，右键单击错误并选择 "**全部复制**"。
7. 关闭“开发人员工具”。

## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md)或[自定义](how-to-modify-assessment.md)评估。
