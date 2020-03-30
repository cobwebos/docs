---
title: Azure 迁移中的疑难评估和依赖项可视化
description: 获取有关 Azure 迁移中的故障排除评估和依赖项可视化的帮助。
ms.service: azure-migrate
ms.topic: troubleshooting
author: musa-57
ms.manager: abhemraj
ms.author: hamusa
ms.date: 01/02/2020
ms.openlocfilehash: 205b52201edb849abab02809b58ff9dc77a32a29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "80127676"
---
# <a name="troubleshoot-assessmentdependency-visualization"></a>排查评估/依赖项可视化问题

本文可帮助您解决 Azure[迁移：服务器评估](migrate-services-overview.md#azure-migrate-server-assessment-tool)的评估和依赖项可视化问题。


## <a name="assessment-readiness-issues"></a>评估准备问题

修复评估就绪问题，如下所示：

**问题** | **修补程序**
--- | ---
引导类型不受支持 | Azure 不支持具有 EFI 启动类型的 VM。 我们建议您在运行迁移之前将引导类型转换为 BIOS。 <br/><br/>可以使用 Azure 迁移服务器迁移来处理此类 VM 的迁移。 它将在迁移期间将 VM 的启动类型转换为 BIOS。
有条件支持的 Windows 操作系统 | 操作系统已过了支持终止日期，并且需要自定义支持协议 （CSA） 来[在 Azure 中提供支持](https://aka.ms/WSosstatement)。 在迁移到 Azure 之前请考虑升级。
不支持的 Windows 操作系统 | Azure 仅支持[选定的 Windows 操作系统版本](https://aka.ms/WSosstatement)。 请考虑在迁移到 Azure 之前升级计算机。
有条件认可的 Linux OS | Azure 仅认可[选定的 Linux 操作系统版本](../virtual-machines/linux/endorsed-distros.md)。 请考虑在迁移到 Azure 之前升级计算机。
未经认可的 Linux OS | 计算机可能在 Azure 中启动，但 Azure 不提供操作系统支持。 在迁移到 Azure 之前，请考虑升级到[支持的 Linux 版本](../virtual-machines/linux/endorsed-distros.md)。
未知操作系统 | VM 的操作系统在 vCenter 服务器中被指定为"其他"。 此行为阻止 Azure 迁移验证 VM 的 Azure 就绪性。 在迁移计算机之前，请确保 Azure[支持](https://aka.ms/azureoslist)操作系统。
不支持的位版本 | 具有 32 位操作系统的 VM 可能会在 Azure 中启动，但我们建议您在迁移到 Azure 之前升级到 64 位。
需要微软可视化工作室订阅 | 计算机正在运行 Windows 客户端操作系统，该操作系统只能通过 Visual Studio 订阅提供支持。
找不到用于所需存储性能的 VM | 计算机所需的存储性能（每秒输入/输出操作 [IOPS] 和吞吐量）超过了 Azure VM 支持。 在迁移之前，减少计算机的存储需求。
找不到用于所需网络性能的 VM | 计算机所需的网络性能（输入/输出）超出了 Azure VM 支持。 减少计算机的网络要求。
在指定位置找不到 VM | 在迁移之前使用不同目标位置。
一个或多个不合适的磁盘 | 连接到 VM 的一个或多个磁盘不符合 Azure 要求。A<br/><br/> Azure 迁移：服务器评估当前不支持超 SSD 磁盘，并根据高级托管磁盘 （32 TB） 的磁盘限制评估磁盘。<br/><br/> 对于连接到 VM 的每个磁盘，请确保磁盘的大小< 64 TB（由超 SSD 磁盘支持）。<br/><br/> 如果不是，请先在迁移到 Azure 之前减小磁盘大小，或者使用 Azure 中的多个磁盘，并将[它们带条](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)，以获得更高的存储限制。 确保 Azure[托管虚拟机磁盘](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#storage-limits)支持每个磁盘所需的性能（IOPS 和吞吐量）。
存在一个或多个不合适网络适配器。 | 在迁移之前从计算机中删除未使用的网络适配器。
磁盘计数超过限制 | 在迁移之前从计算机中删除未使用的磁盘。
磁盘大小超过限制 | Azure 迁移：服务器评估当前不支持超 SSD 磁盘，并根据高级磁盘限制（32 TB）评估磁盘。<br/><br/> 但是，Azure 支持大小高达 64 TB 的磁盘（由超 SSD 磁盘支持）。 在迁移之前将磁盘缩减到小于 64 TB，或者使用 Azure 中的多个磁盘并将[它们条带在一起](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage-performance#disk-striping)以获得更高的存储限制。
指定位置中磁盘不可用 | 在迁移之前请确保磁盘已在目标位置。
不可用于指定冗余的磁盘 | 磁盘应使用在评估设置中定义的冗余存储类型（默认为 LRS）。
由于内部错误，无法确定磁盘的适用性 | 请尝试为组创建一个新评估。
未找到具有所需内核和内存的 VM | Azure 找不到合适的 VM 类型。 在迁移之前请减少本地计算机的内存和内核数。
由于内部错误，无法确定 VM 的适用性 | 请尝试为组创建一个新评估。
由于内部错误，无法确定一个或多个磁盘的适用性 | 请尝试为组创建一个新评估。
由于内部错误，无法确定一个或多个网络适配器的适用性 | 请尝试为组创建一个新评估。

## <a name="linux-vms-are-conditionally-ready"></a>Linux VM 已"有条件地准备就绪"

由于服务器评估中存在已知的漏洞，服务器评估将 Linux VM 标记为"有条件就绪"。

- 该间隙可防止其检测安装在本地 VM 上的 Linux 操作系统的次要版本。
- 例如，对于 RHEL 6.10，当前服务器评估仅检测 RHEL 6 作为操作系统版本。
-  由于 Azure 仅认可 Linux 的特定版本，因此 Linux VM 当前在服务器评估中标记为有条件就绪。
- 通过查看[Azure Linux 支持](https://aka.ms/migrate/selfhost/azureendorseddistros)，可以确定在本地 VM 上运行的 Linux 操作系统是否在 Azure 中得到认可。
-  验证认可分发后，可以忽略此警告。

## <a name="azure-skus-bigger-than-on-premises"></a>Azure SKU 大于本地

Azure 迁移服务器评估可能会根据评估类型建议具有比当前本地分配更多的内核和内存的 Azure VM SKU：


- VM SKU 建议取决于评估属性。
- 这受您在服务器评估中执行的评估类型（*基于性能*）或 *"作为本地"中*的影响。
- 对于基于性能的评估，服务器评估会考虑本地 VM 的利用率数据（CPU、内存、磁盘和网络利用率），以确定本地 VM 的正确目标 VM SKU。 在确定有效利用时，它还增加了一个舒适系数。
- 对于本地大小调整，不考虑性能数据，建议基于本地分配目标 SKU。

为了显示这如何影响建议，我们举一个例子：

我们有一个本地 VM，具有四个内核和八 GB 的内存，CPU 利用率为 50%，内存利用率为 50%，并且指定的舒适系数为 1.3。

-  如果评估**为"本地情况**"，则建议使用具有四个内核和 8 GB 内存的 Azure VM SKU。
- 如果评估基于性能，基于有效 CPU 和内存利用率（4 个内核的 50% = 1.3 = 2.6 个内核和 50% 的 8 GB 内存 = 1.3 = 5.3 GB 内存），则 4 个内核（最近支持的核心计数）和 8 GB 内存（最近支持）最便宜的 VM SKU建议使用内存大小）。
- [详细了解](concepts-assessment-calculation.md#types-of-assessments)评估大小调整。

## <a name="azure-disk-skus-bigger-than-on-premises"></a>Azure 磁盘 SKU 大于本地

Azure 迁移服务器评估可能会建议根据评估类型建立更大的磁盘。
- 服务器评估中的磁盘大小调整取决于两个评估属性：大小调整条件和存储类型。
- 如果大小调整条件**基于性能**，并且存储类型设置为 **"自动**"，则在确定目标磁盘类型（标准硬盘、标准 SSD 或高级磁盘）时，将考虑磁盘的吞吐量值和磁盘的吞吐量值。 然后，建议从磁盘类型中使用磁盘 SKU，并建议考虑本地磁盘的大小要求。
- 如果大小调整条件是基于**性能的**，并且存储类型为**高级**，则建议根据本地磁盘的 IOPS、吞吐量和大小要求在 Azure 中使用高级磁盘 SKU。 当大小调整条件为 **"本地大小做"** 且存储类型为**标准硬盘**、**标准 SSD**或**高级**时，使用相同的逻辑执行磁盘大小调整。

例如，如果您有一个内存为 32 GB 的本地磁盘，但磁盘的聚合读写 IOPS 为 800 IOPS，则服务器评估建议使用高级磁盘（由于 IOPS 要求较高），然后建议提供可支持所需的 IOPS 和大小。 本示例中最接近的匹配项将是 P15（256 GB，1100 IOPS）。 尽管本地磁盘所需的大小为 32 GB，但服务器评估建议使用更大的磁盘，因为本地磁盘的 IOPS 要求很高。

## <a name="utilized-corememory-percentage-missing"></a>缺少已使用的核心/内存百分比

当 Azure 迁移设备无法收集相关本地 VM 的性能数据时，服务器评估报告"已使用缺少的内存百分比"或"内存使用不足百分比"。

- 如果在评估期间关闭 VM，则可能发生这种情况。 关闭 VM 时，设备无法收集其性能数据。
- 如果仅缺少内存计数器，并且您正在尝试评估 Hyper-VM，请检查这些 VM 上是否启用了动态内存。 仅 Hyper-VM 存在一个已知问题，其中 Azure 迁移设备无法为未启用动态内存的 VM 收集内存利用率数据。
- 如果缺少任何性能计数器，Azure 迁移服务器评估将回退到分配的内核和内存，并建议相应的 VM 大小。
- 如果缺少所有性能计数器，请确保满足评估的端口访问要求。 详细了解[VMware、Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#port-access)和[物理](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-physical#port-access)服务器评估的端口访问要求。 [Hyper-V](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#port-access)

## <a name="is-the-operating-system-license-included"></a>是否包括操作系统许可证？

Azure 迁移服务器评估当前仅考虑 Windows 计算机的操作系统许可证成本。 目前不考虑 Linux 计算机的许可成本。

## <a name="how-does-performance-based-sizing-work"></a>基于绩效的尺寸调整如何工作？

服务器评估不断收集本地计算机的性能数据，并使用它来针对 Azure 中的 VM SKU 和磁盘 SKU 提出建议。 [了解如何](concepts-assessment-calculation.md#calculate-sizing-performance-based)收集基于性能的数据。


## <a name="dependency-visualization-in-azure-government"></a>Azure 政府中的依赖项可视化

Azure 迁移取决于依赖项可视化功能的服务映射。 由于服务映射在 Azure 政府中当前不可用，因此此功能在 Azure 政府版中不可用。

## <a name="dependencies-dont-show-after-agent-install"></a>代理安装后不显示依赖项

在本地 VM 上安装依赖项可视化代理后，Azure 迁移通常需要 15-30 分钟才能在门户中显示依赖项。 如果等待超过 30 分钟，请确保 Microsoft 监视代理 （MMA） 可以连接到日志分析工作区。

对于 Windows VM：
1. 在控制面板中，启动 MMA。
2. 在**Microsoft 监视代理属性** > **Azure 日志分析 （OMS） 中**，请确保工作区**的状态**为绿色。
3. 如果状态不是绿色，请尝试删除工作区，然后将其再次添加到 MMA。

    ![MMA 状态](./media/troubleshoot-assessment/mma-properties.png)

对于 Linux VM，请确保 MMA 和依赖项代理的安装命令成功。

## <a name="supported-operating-systems"></a>支持的操作系统

- **彩信代理**：查看支持的[Windows](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)和[Linux](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)操作系统。
- **依赖项代理**：支持的[Windows 和 Linux](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)操作系统。

## <a name="visualize-dependencies-for--hour"></a>可视化 > 小时的依赖项

尽管 Azure 迁移允许您返回上个月的特定日期，但可视化依赖项的最大持续时间为 1 小时。

例如，可以使用依赖项映射中的工期功能查看昨天的依赖项，但只能查看一小时期限。

但是，可以使用 Azure 监视器日志在较长的持续时间内[查询依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

## <a name="visualized-dependencies-for--10-machines"></a>> 10 台计算机的可视化依赖项

在 Azure 迁移服务器评估中，可以可视化具有最多 10 个 VM[的组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 对于较大的组，我们建议您将 VM 拆分为较小的组，以可视化依赖项。

## <a name="machines-show-install-agent"></a>机器显示"安装代理"

将启用依赖项可视化的计算机迁移到 Azure 后，由于以下行为，计算机可能会显示"安装代理"操作，而不是"查看依赖项"：


- 迁移到 Azure 后，本地计算机将关闭，等效 VM 在 Azure 中启动。 这些计算机将获得一个不同的 MAC 地址。
- 机器可能也有不同的 IP 地址，具体取决于您是否保留本地 IP 地址。
- 如果 MAC 地址和 IP 地址都与本地地址不同，则 Azure 迁移不会将本地计算机与任何服务映射依赖项数据相关联。 在这种情况下，它将显示安装代理而不是查看依赖项的选项。
- 测试迁移到 Azure 后，本地计算机将保持按预期打开状态。 Azure 中旋转的等效计算机获取不同的 MAC 地址，并可能获取不同的 IP 地址。 除非阻止从这些计算机传出 Azure 监视器日志流量，否则 Azure 迁移不会将本地计算机与任何服务映射依赖项数据相关联，因此将显示安装代理的选项，而不是查看依赖项。


## <a name="capture-network-traffic"></a>捕获网络流量

收集网络流量日志，如下所示：

1. 登录到 Azure[门户](https://portal.azure.com)。
2. 按 F12 启动开发人员工具。 如果需要，清除导航设置**上的清除条目**。
3. 选择 **"网络**"选项卡，并开始捕获网络流量：
   - 在 Chrome 中，选择“保留日志”****。 记录应自动启动。 红色圆圈表示正在捕获流量。 如果未出现红色圆圈，请选择黑色圆圈以开始。
   - 在 Microsoft 边缘和 Internet 资源管理器中，录制应自动启动。 如果没有，请选择绿色播放按钮。
4. 尝试再现该错误。
5. 在记录过程中遇到错误后，停止记录，并保存一份已记录的活动：
   - 在 Chrome 中，右键单击并选择 **"保存为包含内容的 HAR"。** 此操作压缩日志并将其导出为 .har 文件。
   - 在 Microsoft 边缘或 Internet 资源管理器中，选择 **"导出捕获的流量**"选项。 此操作压缩并导出日志。
6. 选择 **"控制台"** 选项卡以检查是否有任何警告或错误。 保存控制台日志：
   - 在 Chrome 中，右键单击控制台日志中的任意位置。 选择 **"保存为**"以导出日志，然后压缩日志。
   - 在微软边缘或互联网资源管理器中，右键单击错误并选择"**复制所有**"。
7. 关闭“开发人员工具”。

## <a name="next-steps"></a>后续步骤

[创建](how-to-create-assessment.md)或[自定义](how-to-modify-assessment.md)评估。
