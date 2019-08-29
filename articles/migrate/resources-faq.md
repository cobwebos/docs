---
title: Azure Migrate-常见问题 (FAQ) |Microsoft Docs
description: 解答有关 Azure Migrate 的常见问题
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: snehaa
ms.openlocfilehash: af95ad892b62cb5d8bece554d6026525d9279777
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102871"
---
# <a name="azure-migrate-frequently-asked-questions-faq"></a>“Azure Migrate:常见问题 (FAQ)

本文解答了有关 Azure Migrate 的常见问题。 阅读本文后, 如果你有更多的查询, 请将其发布到[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>常规

### <a name="which-azure-geographies-does-azure-migrate-support"></a>哪些 Azure 地理位置 Azure Migrate 支持？

请参阅[VMware 列表](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v 列表](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)。

### <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate 和 Azure Site Recovery 之间的区别是什么？

Azure Migrate 提供了一个集中式中心, 用于开始迁移、执行和跟踪计算机和工作负荷的发现和评估, 并执行和跟踪计算机和工作负荷到 Azure 的迁移。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是一种灾难恢复解决方案。 Azure Migrate Server 迁移使用 Azure Site Recovery 在后端来启用迁移方案, 以便迁移本地计算机。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 设备 (VMware/物理服务器)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 设备如何连接到 Azure？

可以通过 internet 连接, 也可以将 Azure ExpressRoute 用于公共/Microsoft 对等互连。

### <a name="what-are-the-network-connectivity-requirements-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate server 评估和迁移的网络连接要求是什么？

有关启用 Azure Migrate 与 Azure 进行通信所需的 Url 和端口的信息, 请参阅[VMware](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支持矩阵。

### <a name="can-i-harden-the-appliance-vm-that-i-set-up-with-the-template"></a>我是否可以强化在模板中设置的设备 VM？

可以将组件 (例如, 防病毒) 添加到模板, 只要保留 Azure Migrate 设备所需的通信和防火墙规则。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 设备收集哪些数据？

可以[在 Azure Migrate 文档中](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)查看 Azure Migrate 设备收集的数据的详细信息。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析的 VMware 或 Hyper-v 环境是否有任何性能影响？

Azure Migrate 设备会连续分析本地计算机, 以测量 VM 性能数据。 此分析对 Hyper-v/ESXi 主机或 vCenter Server 上的性能几乎没有影响。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>收集的数据存储在何处, 以及有多长时间？

Azure Migrate 设备收集的数据存储在创建迁移项目时选择的 Azure 位置。 数据将安全地存储在 Microsoft 订阅中, 并在删除 Azure Migrate 项目时被删除。

对于依赖项可视化, 如果你在 Vm 上安装代理, 则依赖关系代理收集的数据将存储在美国, 在 Azure 订阅中创建的 Log Analytics 工作区中。 在订阅中删除 Log Analytics 工作区时会删除此数据。 有关详细信息, 请参阅[依赖项可视化](concepts-dependency-visualization.md)。

### <a name="what-volume-of-data-is-uploaded-by-the-azure-migrate-appliance-during-continuous-profiling"></a>在连续分析期间, Azure Migrate 设备会上传哪些数据量？

发送到 Azure Migrate 的数据量因多个参数而异。 为您提供了一种方法: 一个包含10台计算机 (每个计算机包含一个磁盘和一个 NIC) 的 Azure Migrate 项目每天大约 50 MB 发送一次。 此值为近似值。 它会根据 Nic 和磁盘的数据点的数目而发生变化。 (如果计算机、Nic 或磁盘的数量增加, 则发送的数据量将是非线性的。)

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>数据是在静止还是在传输过程中进行加密？

是的。 通过 HTTPS 安全地将元数据发送到 Azure Migrate 服务。 元数据存储在 Microsoft 订阅中的[Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)数据库和[Azure Blob 存储](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中。 静态加密元数据。

依赖关系代理收集的数据也会在传输过程中加密 (安全 HTTPS)。 它存储在订阅的 Log Analytics 工作区中。 它还静态加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 设备如何与 vCenter Server 和 Azure Migrate 服务通信？

设备使用设置设备时提供的凭据连接到 vCenter Server (端口 443)。 它使用 VMware PowerCLI 来查询 vCenter Server 来收集有关 vCenter Server 管理的 Vm 的元数据。 它收集有关 Vm (核心、内存、磁盘、Nic 等) 的配置数据, 以及上个月每个 VM 的性能历史记录。 然后, 收集的元数据将发送到 Azure Migrate 服务器评估 (通过 internet 通过 HTTPS) 进行评估。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>是否可以将同一台设备连接到多个 vCenter Server 实例？

否。 设备与 vCenter Server 之间存在一对一的映射。 如果需要在多个 vCenter Server 实例上发现 Vm, 则需要部署多个设备。


### <a name="i-changed-my-machine-size-can-i-run-the-assessment-again"></a>我更改了计算机的大小。 能否再次运行评估？

Azure Migrate 设备不断地收集有关本地环境的信息。 但评估是本地 Vm 的时间点快照。 如果更改了要评估的 VM 上的设置, 请使用 "重新计算" 选项, 以使用最新更改更新评估。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment-in-azure-migrate-server-assessment"></a>如何在多租户环境中执行 Azure Migrate Server 评估的发现？

对于 VMware, 如果你有一个跨租户共享的环境, 并且你不希望发现另一个租户的订阅中一个租户的 Vm, 请创建 vCenter Server 凭据, 这些凭据只能访问你想要发现的 Vm。 然后在 Azure Migrate 设备中启动发现时使用这些凭据。

对于 Hyper-v, 发现使用 Hyper-v 主机凭据。 如果 Vm 共享相同的 Hyper-v 主机, 当前没有办法分离发现。  

### <a name="how-many-vms-can-i-discover-with-a-single-migration-appliance"></a>单个迁移设备可以发现多少个 Vm？

使用单个迁移设备, 最多可以发现10000个 VMware Vm 和最多5000个 Hyper-v Vm。 如果本地环境中有更多计算机, 请了解如何缩放[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)评估。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 服务器评估

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate 服务器评估是否支持物理服务器评估？

不, Azure Migrate 当前不支持物理服务器评估。

### <a name="does-azure-migrate-need-vcenter-server-to-perform-discovery-in-a-vmware-environment"></a>Azure Migrate 是否需要 vCenter Server 才能在 VMware 环境中执行发现？

是, Azure Migrate 需要 vCenter Server 才能在 VMware 环境中执行发现。 它不支持发现不受 vCenter Server 管理的 ESXi 主机。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 评估和地图工具包之间的区别是什么？

Azure Migrate Server 评估提供了迁移评估, 可帮助迁移到 Azure, 以及评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划 (MAP) 工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助执行其他任务, 例如, 较新版本的 Windows 客户端和服务器操作系统和软件使用跟踪的迁移规划。 对于这些情况，继续使用 MAP Toolkit。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-azure-site-recovery-deployment-planner"></a>Azure Migrate Server 评估与 Azure Site Recovery 部署规划器之间的区别是什么？

Azure Migrate Server 评估是一种迁移规划工具。 Azure Site Recovery 部署规划器是一种灾难恢复规划工具。

**从 VMware/Hyper-v 迁移到 Azure**:如果计划将本地服务器迁移到 Azure, 请使用 Azure Migrate Server 评估进行迁移规划。 它评估本地工作负荷, 并提供指导、见解和工具来帮助你将服务器迁移到 Azure。 准备好迁移计划后, 可以使用 Azure Migrate Server 迁移之类的工具将计算机迁移到 Azure。

**从 VMware/hyper-v 到 Azure 的灾难恢复**:若要通过 Site Recovery 灾难恢复到 Azure, 请使用 Site Recovery 部署规划器进行规划。 Site Recovery 部署规划器对本地环境进行深入的、Site Recovery 特定的评估。 它提供 Site Recovery 的建议, 以实现 Vm 的复制和故障转移等成功灾难操作。

### <a name="does-azure-migrate-support-cost-estimation-for-the-enterprise-agreement-ea-program"></a>Azure Migrate 是否支持企业协议 (EA) 计划的成本估算？

Azure Migrate 当前不支持[企业协议程序](https://azure.microsoft.com/offers/enterprise-agreement-support/)的成本估计。 解决方法是在 "评估属性" 的 "**折扣**" 框中指定 "即**用即付**" 作为 "**产品/服务**", 并手动指定折扣百分比 (适用于订阅):

  ![评估属性](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>作为本地大小调整和基于性能的大小调整之间的区别是什么？

利用按本地大小调整, Azure Migrate 不会考虑 VM 性能数据。 它基于本地配置调整 Vm 的大小。 通过基于性能的大小调整, 大小基于利用率数据。

例如, 请考虑一个具有4个核心和 8 GB 内存的本地 VM, 其 CPU 使用率为 50%, 内存利用率为 50%。 对于此 VM, 本地大小调整建议具有四个核心和 8 GB 内存的 Azure VM SKU。 基于性能的大小建议使用具有两个核心和 4 GB 内存的 VM SKU, 因为会考虑利用率百分比。

同样, 磁盘大小取决于两个评估属性: 大小调整条件和存储类型。 如果大小调整条件是基于性能的并且存储类型为自动, Azure Migrate 将在标识目标磁盘类型 (标准或高级) 时, 考虑磁盘的 IOPS 和吞吐量值。

如果大小调整条件基于性能并且存储类型为 "高级", 则 Azure Migrate 建议使用高级磁盘。 根据本地磁盘的大小选择高级磁盘 SKU。 当大小调整条件为 "按本地大小调整" 并且存储类型为 "标准" 或 "高级" 时, 将使用相同的逻辑来进行磁盘大小调整。

### <a name="what-impact-does-performance-history-and-utilization-percentile-have-on-size-recommendations"></a>性能历史记录和使用率百分比对大小建议有什么影响？

这些属性仅适用于基于性能的大小调整。

Azure Migrate 收集本地计算机的性能历史记录, 并使用它在 Azure 中建议 VM 大小和磁盘类型。

设备会持续分析本地环境, 每隔20秒收集一次实时利用率数据。 该设备汇总了20秒的示例, 每15分钟创建一个数据点。 若要创建数据点, 设备将从所有20秒的示例中选择峰值值。 设备会将此数据点发送到 Azure。

当你在 Azure 中创建评估 (基于性能持续时间和性能历史记录百分比值) 时, Azure Migrate 将计算有效利用率值并将其用于调整大小。

例如, 假设将 "性能持续时间" 设置为一天, 将百分比值设置为 95%。 Azure Migrate 按收集器按升序对过去一天发送的每15分钟的样本点进行排序, 并选取第95百分位值作为有效利用率。

95% 的值可确保忽略任何离群值。 如果使用 99% 百分点值, 则可以包含离群值。 如果要选择该时间段的高峰使用量, 而不想错过任何离群值, 请选择 "99%"。

### <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

利用依赖项可视化, 你可以更自信地评估用于迁移的 Vm 组。 在运行评估之前, 它会交叉检查计算机的依赖关系。 依赖关系可视化可帮助确保在迁移到 Azure 时避免意外中断。 Azure Migrate 使用 Azure Monitor 日志中的服务映射解决方案来启用依赖项可视化。

> [!NOTE]
> 依赖关系可视化在 Azure 政府版中不可用。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>是否需要付费才能使用依赖项可视化？

否。 有关详细信息, 请参阅[Azure Migrate 定价](https://azure.microsoft.com/pricing/details/azure-migrate/)。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>要实现依赖项可视化，是否需要安装任何软件？

若要使用依赖项可视化，你需要在要评估的每台本地计算机上下载并安装代理。

需要在每台计算机上安装以下代理:
- [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果计算机没有 internet 连接, 则需要下载并安装 Log Analytics 网关。

不需要这些代理, 除非使用的是依赖项可视化。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可将现有的工作区用于依赖项可视化？

是的, 你可以将现有工作区附加到迁移项目, 并将其用于依赖项可视化。 有关详细信息, 请参阅[依赖关系可视化](concepts-dependency-visualization.md#how-does-it-work)一文中的 "它的工作原理"。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

不能, 无法导出依赖关系可视化。 但由于 Azure Migrate 使用服务映射进行依赖关系可视化, 因此可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)获取 JSON 格式的依赖项。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>如何自动执行 Microsoft Monitoring Agent (MMA) 和依赖项代理的安装？

使用此[脚本安装代理](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)。 按照这些说明操作, 使用命令行或自动化[来安装 MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 。 对于 MMA, 请使用[此脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了脚本以外, 还可以使用 System Center Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)之类的部署工具来部署代理。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支持哪些操作系统？

- 查看[MMA 支持的 Windows 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)的列表。
- 查看[MMA 支持的 Linux 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)的列表。

### <a name="what-operating-systems-are-supported-by-the-dependency-agent"></a>依赖关系代理支持哪些操作系统？

- 查看[依赖关系代理支持的 Windows 操作系统](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)的列表。
- 查看[依赖关系代理支持的 Linux 操作系统](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)的列表。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>是否可以将 Azure Migrate 中的依赖项可视化到超过一小时？
否。 最多可将依赖项可视化到一小时。 您可以返回到历史记录中的特定日期 (在历史记录中, 每月返回一项), 但可视化效果的最大持续时间是一小时。 例如, 你可以在依赖关系映射中使用时间段来查看昨天的依赖项, 但你只能查看一个小时的窗口。 但是, 可以使用 Azure Monitor 日志来查询更长时间的[依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="can-i-use-dependency-visualization-for-groups-that-contain-more-than-10-vms"></a>对于包含10个以上 Vm 的组是否可以使用依赖项可视化？
可以可视化最多包含10个 Vm 的[组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果有10个以上 Vm 的组, 我们建议将该组拆分为较小的组, 然后将依赖项可视化。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 服务器迁移

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-azure-site-recovery"></a>Azure Migrate Server 迁移和 Azure Site Recovery 之间的区别是什么？

Azure Migrate Server 迁移使用 Site Recovery 复制引擎来迁移 VMware Vm 的基于代理的迁移、迁移 Hyper-v Vm 以及将物理服务器迁移到 Azure。 用于迁移 VMware Vm 的无代理选项在本机内置于服务器迁移中。

## <a name="next-steps"></a>后续步骤
阅读[Azure Migrate 概述](migrate-services-overview.md)。
