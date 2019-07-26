---
title: Azure Migrate - 常见问题解答 (FAQ) | Microsoft Docs
description: 解答有关 Azure Migrate 的常见问题
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: snehaa
ms.openlocfilehash: 0708502087ae6880d9559cf17f0ba9982b2ba040
ms.sourcegitcommit: 57a7d4f67635212f5bf0c56e58fd87c8ec366f2c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68372485"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - 常见问题解答 (FAQ)

本文包含有关 Azure Migrate 的常见问题。 如果在阅读本文之后还有其他问题，请在 [Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中提问。

## <a name="general"></a>常规

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支持哪些 Azure 地域？

可在[此处找到 VMware](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)列表, 并在[此处找到 hyper-v](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)。

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate 与 Azure Site Recovery 有何不同？

Azure Migrate 提供了一个集中式中心, 用于启动、执行和跟踪计算机和工作负荷到 Azure 的发现、评估和迁移。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是一种灾难恢复解决方案。 Azure Migrate Server 迁移利用后端 Azure Site Recovery, 为本地计算机的迁移方案启用迁移方案。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 设备 (VMware/物理服务器)

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 设备如何连接到 Azure？

可以通过 internet 连接, 也可以将 ExpressRoute 用于公共/Microsoft 对等互连。

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate 服务器评估和迁移需要哪些网络连接要求

有关 Azure Migrate 与 Azure 进行通信所需的 Url 和端口, 请查看[VMWare](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支持列表。

### <a name="can-i-harden-the-appliance-vm-i-set-up-with-the-template"></a>是否可以强化通过模板设置的设备 VM？

可以将其他组件 (例如防病毒软件) 添加到模板中, 只要 Azure Migrate 设备所需的通信和防火墙规则保持原样。   

### <a name="what-data-is-collected-by-azure-migrate-appliance"></a>Azure Migrate 设备收集哪些数据？

可在[此处](https://docs.microsoft.com/azure/migrate/migrate-appliance#collected-performance-data-vmware)详细了解 Azure Migrate 设备收集的数据。

### <a name="is-there-any-performance-impact-on-the-analyzed-vmware-or-hyper-v-environment"></a>分析的 VMware 或 Hyper-v 环境是否有任何性能影响？

通过连续分析性能数据, Azure Migrate 设备配置本地计算机以测量 VM 性能数据。 对于 Hyper-v/ESXi 主机和 vCenter Server, 这几乎不会对性能产生任何影响。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>收集的数据存储在何处，存储多久？

Azure Migrate 设备收集的数据存储在创建迁移项目时指定的 Azure 位置。 数据将安全地存储在 Microsoft 订阅中, 并在删除 Azure Migrate 项目时删除。

对于依赖项可视化, 如果你在 Vm 上安装代理, 则依赖关系代理收集的数据将存储在美国, 在 Azure 订阅中创建的 Log Analytics 工作区中。 在订阅中删除 Log Analytics 工作区时会删除此数据。 [了解详细信息](concepts-dependency-visualization.md)。

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-appliance-during-continuous-profiling"></a>在连续分析期间 Azure Migrate 设备上传的数据量是多少？

发送到 Azure Migrate 的数据量因多个参数而异。 为提供一个指示号, 其中包含10台计算机 (每个计算机包含一个磁盘和一个 NIC) 的 Azure Migrate 项目, 每天大约 50 MB 发送一次。 这是一个近似值, 此值根据 Nic 和磁盘的数据点的数量变化 (如果计算机、Nic 或磁盘的数量增加, 则发送的数据是非线性的)。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>数据是静态加密的还是传输中的？

对于两者都是。 通过 https 安全地将元数据发送到 Azure Migrate 服务。 元数据存储在 Microsoft 订阅中的[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)和[Azure blob 存储](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)中, 并以静态加密。

依赖关系代理收集的数据也会在传输过程中加密 (安全 HTTPS), 并存储在用户订阅中的 Log Analytics 工作区中。 它还静态加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 设备如何与 vCenter Server 和 Azure Migrate 服务通信？

设备使用设置设备时提供的凭据连接到 vCenter Server (端口 443)。 它使用 VMware PowerCLI 查询 vCenter Server, 收集有关 vCenter Server 管理的 Vm 的元数据。 它收集有关 Vm (核心、内存、磁盘、NIC 等) 的配置数据, 以及上个月每个 VM 的性能历史记录。 然后, 收集的元数据将发送到 Azure Migrate 服务器评估 (通过 internet 通过 HTTPS) 进行评估。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>是否可以将同一台设备连接到多个 vCenter 服务器？

是的, 可以使用单个 Azure Migrate 设备来发现多个 vCenter 服务器, 但不能同时使用。 你需要再运行一次发现。

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>我更改了计算机的大小。 能否重新运行评估？

Azure Migrate 设备不断地收集有关本地环境的信息。 但是, 评估是本地 Vm 的时间点快照。 如果更改了要评估的 VM 上的设置, 请使用 "重新计算" 选项, 以使用最新更改更新评估。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate-server-assessment"></a>如何在 Azure Migrate 服务器评估中发现多租户环境？

对于 VMware, 如果你有一个在租户之间共享的环境, 并且你不希望发现另一个租户的订阅中一个租户的 Vm, 请创建 vCenter Server 凭据, 只需访问要发现的 Vm 即可。 然后, 在 Azure Migrate 设备中开始例行关闭发现时使用凭据。

对于 Hyper-v, 发现使用 Hyper-v 主机凭据。如果 Vm 共享相同的 Hyper-v 主机, 则当前没有办法分隔发现。  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>使用单个迁移设备可以发现多少个 Vm？

使用单个迁移设备, 最多可以发现10000个 VMware Vm 和最多5000个 Hyper-v Vm。  如果本地环境中有更多计算机, 请了解如何缩放[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)评估。

## <a name="azure-migrate-server-assessment"></a>“Azure Migrate:服务器评估”评估 VMware VM

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure Migrate:服务器评估支持物理服务器的评估？

不, Azure Migrate 当前不支持物理服务器评估。

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate 是否需要使用 vCenter Server 来发现 VMware 环境？

是的, Azure Migrate 需要 vCenter Server 发现 VMware 环境。 它不支持发现不受 vCenter Server 管理的 ESXi 主机。

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>使用 Azure Migrate 之间有何区别:服务器评估和地图工具包？

“Azure Migrate:服务器评估提供迁移评估来帮助迁移准备情况, 并评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划 (MAP) 工具包](https://www.microsoft.com/download/details.aspx?id=7826)具有其他功能, 如针对较新版本的 Windows 客户端和服务器操作系统的迁移规划以及软件使用跟踪。 对于这些情况，继续使用 MAP Toolkit。

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>如何 Azure Migrate:服务器评估不同于 Azure Site Recovery 部署规划器？

“Azure Migrate:服务器评估是一种迁移规划工具。 Azure Site Recovery 部署规划器是一种灾难恢复规划工具。

- **从 VMware/Hyper-v 迁移到 Azure**:如果打算将本地服务器迁移到 Azure, 请使用 Azure Migrate:用于迁移规划的服务器评估工具。 该工具可评估本地工作负荷, 并提供指导、见解和机制来帮助迁移到 Azure。 准备好迁移计划后, 可以使用 Azure Migrate 的工具:服务器迁移, 将计算机迁移到 Azure。
- **从 VMware/Hyper-V 到 Azure 的灾难恢复**：对于使用 Site Recovery 的到 Azure 的灾难恢复, 请使用 Site Recovery 部署规划器进行灾难恢复计划。 Site Recovery 部署规划器对本地环境进行深入的、Site Recovery 特定的评估。 它提供 Site Recovery 的建议, 以实现成功的灾难操作, 如复制和 Vm 故障转移。

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate 是否支持基于企业协议 (EA) 的成本估算？

Azure Migrate 当前不支持[企业协议提议](https://azure.microsoft.com/offers/enterprise-agreement-support/)的成本估算。 解决方法是指定即用即付产品/服务, 并手动指定评估属性的 "折扣" 字段中的折扣百分比 (适用于订阅)。

  ![折扣](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>作为本地大小调整和基于性能的大小调整之间的区别是什么？

- 在中, 作为本地大小调整, Azure Migrate 不考虑 VM 性能数据。 它基于本地配置调整 Vm 的大小。 -基于性能的大小调整时, 大小基于利用率数据。
- 例如, 如果本地 VM 有4个核心和 8 GB 的内存, 其中包含 50% 的 CPU 使用率和 50% 的内存利用率, 作为本地大小, 建议使用具有四个核心和 8 GB 内存的 Azure VM SKU。 但基于性能的大小调整建议使用两个核心和 4 GB 的 VM SKU, 因为会考虑利用率百分比。
- 同样, 磁盘大小取决于两个评估属性-大小调整条件和存储类型。
= 如果大小调整条件是基于性能的并且存储类型为自动, 则在标识目标磁盘类型 (标准或高级) 时, 考虑磁盘的 IOPS 和吞吐量值。
- 如果大小调整条件基于性能并且存储类型为 "高级", 则建议使用高级磁盘。 根据本地磁盘的大小选择高级磁盘 SKU。 当大小调整条件为 "按本地大小调整" 并且存储类型为 "标准" 或 "高级" 时, 将使用相同的逻辑来执行磁盘大小调整。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>性能历史记录和利用百分率对大小建议有什么影响？

这些属性仅适用于基于性能的大小调整。

- Azure Migrate 收集本地计算机的性能历史记录, 并使用它在 Azure 中建议 VM 大小和磁盘类型。
- 设备会持续分析本地环境, 每隔20秒收集一次实时利用率数据。 设备对时长 20 秒的样本进行汇总，每隔 15 分钟创建单个数据点。 为了创建单个数据点，设备从所有时长 20 秒的样本中选择峰值并将其发送到 Azure。
- 当你在 Azure 中创建评估 (基于性能持续时间和性能历史记录百分比值) 时, Azure Migrate 将计算有效利用率值, 并将其用于调整大小。
- 例如, 如果将性能持续时间设置为一天, 百分比值设置为 95%, 则 Azure Migrate 使用收集器为最后一天发送的15分钟的样本点, 按升序对它们进行排序, 并选取第95百分位值作为有效利用率。
- 95% 的值可确保忽略任何离群值, 如果使用 99% 的百分点值, 可能会出现这种情况。 如果希望选取期间的峰值利用率并且不希望错过任何离群值，则应当选择第 99 百分位值。

### <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

利用依赖项可视化, 你可以更自信地评估用于迁移的 Vm 组。 在运行评估之前, 它会交叉检查计算机的依赖关系。 依赖关系可视化可帮助确保不会遗留任何内容, 并避免在迁移到 Azure 时出现意外中断。 Azure Migrate 利用 Azure Monitor 日志中的服务映射解决方案来启用依赖项可视化。

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>是否需要支付依赖项可视化功能的使用费？

否。 [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>要实现依赖项可视化，是否需要安装任何软件？

若要使用依赖项可视化，你需要在要评估的每台本地计算机上下载并安装代理。

- 需要在每台计算机上安装 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- 需要在每台计算机上安装[依赖项代理](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。
- 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 Log Analytics 网关。

不需要这些代理, 除非使用的是依赖项可视化。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可将现有的工作区用于依赖项可视化？

是的, 你可以将现有工作区附加到迁移项目, 并利用它进行依赖关系可视化。 [了解详细信息](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

不能, 无法导出依赖关系可视化。 但是，由于 Azure Migrate 使用服务映射进行依赖项可视化，因此可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 获取 JSON 格式的依赖项。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>如何才能自动安装 Microsoft Monitoring Agent (MMA) 和依赖项代理？

[使用此脚本](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)安装代理。 [按照这些说明](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)操作, 使用命令行或自动安装 MMA。 对于 MMA, 请利用[此脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了脚本以外, 还可以使用部署工具 (例如 System Center Configuration Manager、 [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等) 来部署代理。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支持哪些操作系统？

- [查看](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)MMA 支持的 Windows 操作系统的列表。
- [查看](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)MMA 支持的 Linux 操作系统的列表。

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>依赖关系代理支持哪些操作系统？

[查看](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)依赖关系代理支持的 Windows 操作系统。
[查看](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)依赖关系代理支持的 Linux 操作系统的列表。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>是否可以将 Azure Migrate 中的依赖项可视化到超过一小时？
不可以, 你可以将依赖项可视化最多一小时。 您可以返回到历史记录中的特定日期 (直到上个月), 但可视化效果的最大持续时间是一小时。 例如, 你可以在依赖关系映射中使用时间段来查看昨天的依赖项, 但只能在一个小时的时段内查看依赖项。 但是, 可以使用 Azure Monitor 日志来查询更长时间的[依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-10-vms"></a>包含 10 个以上 VM 的组是否支持依赖项可视化？
可以可视化最多为10个 Vm[的组的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果有10个以上 Vm 的组, 我们建议将该组拆分为较小的组, 然后可视化依赖项。

## <a name="azure-migrate-server-migration"></a>“Azure Migrate:Server 迁移

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>如何 Azure Migrate:服务器迁移不同于 Azure Site Recovery？

“Azure Migrate:服务器迁移利用 Site Recovery 的复制引擎, 对 VMware Vm 进行基于代理的迁移, 迁移 Hyper-v Vm, 并将物理服务器迁移到 Azure。 用于迁移 VMware Vm 的无代理选项在本地构建的服务器迁移中。

## <a name="next-steps"></a>后续步骤
阅读 [Azure Migrate 概述](migrate-services-overview.md)
