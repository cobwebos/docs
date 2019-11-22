---
title: 有关 Azure Migrate 的常见问题
description: 获取有关 Azure Migrate 服务的常见问题解答。
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: snehaa
ms.openlocfilehash: dc7dff0119ec849b447754ae54a45911038f6c48
ms.sourcegitcommit: e50a39eb97a0b52ce35fd7b1cf16c7a9091d5a2a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2019
ms.locfileid: "74284469"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate：常见问题

本文解答了有关 Azure Migrate 的常见问题。 阅读本文后，如果你有更多的查询，请将其发布到[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)。

## <a name="general"></a>常规

### <a name="which-azure-geographies-are-supported"></a>支持哪些 Azure 区域？

查看适用于[VMWARE vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-vmware#azure-migrate-projects)和[hyper-v vm](https://docs.microsoft.com/azure/migrate/migrate-support-matrix-hyper-v#azure-migrate-projects)的 Azure Migrate 支持的地理位置。

### <a name="whats-the-difference-between-azure-migrate-and-site-recovery"></a>Azure Migrate 和 Site Recovery 之间的区别是什么？

Azure Migrate 提供了一个集中式中心来管理和跟踪本地计算机和工作负荷到 Azure 的发现、评估和迁移。 [Azure Site Recovery](../site-recovery/site-recovery-overview.md)是一种灾难恢复解决方案。 Azure Migrate：服务器迁移工具使用一些后端 Site Recovery 功能来迁移某些本地计算机。

### <a name="how-do-i-delete-an-azure-migrate-project"></a>如何实现删除 Azure Migrate 项目？

按照[这些说明](how-to-delete-project.md)操作来删除项目。 [查看](how-to-delete-project.md#created-resources)在 Azure Migrate 项目中发现、评估和迁移计算机与工作负荷时创建的资源。


## <a name="azure-migrate-appliance"></a>Azure Migrate 设备

### <a name="how-does-the-appliance-connect-to-azure"></a>设备如何连接到 Azure？

可以通过 internet 连接，也可以将 Azure ExpressRoute 用于公共/Microsoft 对等互连。

### <a name="what-network-connectivity-is-needed-for-azure-migrate-server-assessment-and-migration"></a>Azure Migrate 服务器评估和迁移需要哪些网络连接？

有关 Azure Migrate 与 Azure 进行通信所需的 Url 和端口的信息，请参阅[VMware](migrate-support-matrix-vmware.md)和[hyper-v](migrate-support-matrix-hyper-v.md)支持列表。

### <a name="can-i-harden-the-appliance-vm-created-with-the-template"></a>能否强化通过模板创建的设备 VM？

可以将组件（例如，防病毒）添加到模板，只要保留 Azure Migrate 设备所需的通信和防火墙规则。

### <a name="what-data-is-collected-by-the-azure-migrate-appliance"></a>Azure Migrate 设备收集哪些数据？

查看设备收集的数据，如下所示：
- VMware Vm 的[性能数据](migrate-appliance.md#collected-performance-data-vmware)和[元](migrate-appliance.md#collected-metadata-vmware)数据。
- Hyper-v Vm 的[性能数据](migrate-appliance.md#collected-performance-data-hyper-v)和[元数据](migrate-appliance.md#collected-metadata-hyper-v)。


### <a name="does-appliance-analysis-impact-performance"></a>设备分析是否会影响性能？

Azure Migrate 设备会连续分析本地计算机，以测量 VM 性能数据。 此分析对 Hyper-v/ESXi 主机或 vCenter Server 上的性能几乎没有影响。

### <a name="where-and-how-long-is-collected-data-stored"></a>收集数据的位置和时间是多少？

Azure Migrate 设备收集的数据存储在创建迁移项目时选择的 Azure 位置。 数据将安全地存储在 Microsoft 订阅中，并在删除 Azure Migrate 项目时删除。

对于依赖项可视化，收集的数据存储在美国中，在 Azure 订阅中创建的 Log Analytics 工作区中。 在订阅中删除 Log Analytics 工作区时会删除此数据。 [详细了解](concepts-dependency-visualization.md)依赖关系可视化。

### <a name="what-volume-of-data-is-uploaded-during-continuous-profiling"></a>连续分析期间上传了多少数据？

发送到 Azure Migrate 的数据量因多个参数而异。 为了让你了解卷，每个 Azure Migrate 包含10台计算机的项目（每个都有一个磁盘，一个 NIC）发送大约 50 MB。 此值为近似值，并根据 Nic 和磁盘的数据点数量进行了更改。 如果计算机、Nic 或磁盘的数量增加，则发送的数据增加是非线性的。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>数据是静态加密数据还是传输数据？

是的。
- 元数据通过 HTTPS 安全地发送到 Azure Migrate 服务。
- 元数据存储在[Azure Cosmos 数据库](../cosmos-db/database-encryption-at-rest.md)数据库和 Microsoft 订阅中的[azure Blob 存储](../storage/common/storage-service-encryption.md)中。 元数据是静态加密的。
- 依赖关系分析的数据也会在传输中加密（安全 HTTPS）。 它存储在订阅的 Log Analytics 工作区中。 它还静态加密。

### <a name="how-does-the-appliance-communicate-with-vcenter-server-and-azure-migrate"></a>设备如何与 vCenter Server 和 Azure Migrate 通信？

1. 设备使用设置设备时提供的凭据连接到 vCenter Server （端口443）。
2. 设备使用 VMware PowerCLI 来查询 vCenter Server，以收集 vCenter Server 管理的 Vm 的元数据。 它收集有关过去个月内每个 VM 的 Vm （核心、内存、磁盘、Nic）和性能历史记录的配置数据。
3. 然后，收集的元数据将发送到 Azure Migrate：服务器评估（通过 internet 通过 HTTPS）进行评估。

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-server-instances"></a>是否可以将同一台设备连接到多个 vCenter Server 实例？

不。 设备与 vCenter Server 之间存在一对一的映射。 若要发现多个 vCenter Server 实例上的 Vm，需要部署多个设备。


### <a name="machine-size-changed-can-i-run-the-assessment-again"></a>计算机大小已更改。 能否再次运行评估？

Azure Migrate 设备不断地收集有关本地环境的信息。 但评估是本地 Vm 的时间点快照。 如果更改了要评估的 VM 上的设置，请使用 "重新计算" 选项，以使用最新更改更新评估。

### <a name="how-can-i-perform-discovery-in-a-multitenant-environment"></a>如何在多租户环境中执行发现？

- 对于 VMware，如果你的环境在租户之间共享，并且你不希望发现另一个租户的订阅中一个租户的 Vm，请创建 vCenter Server 凭据，这些凭据只能访问你想要发现的 Vm。 然后，在 Azure Migrate 设备中启动发现时使用这些凭据。
- 对于 Hyper-v，发现使用 Hyper-v 主机凭据。 如果 Vm 共享相同的 Hyper-v 主机，当前没有办法分离发现。  

### <a name="how-many-vms-can-i-discover-with-a-single-appliance"></a>使用单个设备可以发现多少个 Vm？

使用单个迁移设备，最多可以发现10000个 VMware Vm 和最多5000个 Hyper-v Vm。 如果本地环境中有更多计算机，请了解如何缩放[hyper-v](scale-hyper-v-assessment.md)和[VMware](scale-vmware-assessment.md)评估。

### <a name="can-i-delete-the-azure-migrate-appliance-from-the-project"></a>是否可以从项目中删除 Azure Migrate 设备？

当前不支持从项目中删除设备。

- 删除设备的唯一方法是删除包含与设备关联的 Azure Migrate 项目的资源组。
- 但是，删除资源组也会删除其他已注册的设备、发现的清单、评估以及与资源组中的项目相关联的所有其他 Azure 组件。

## <a name="azure-migrate-server-assessment"></a>Azure Migrate 服务器评估


### <a name="does-azure-migrate-need-vcenter-server-for-vmware-vm-discovery"></a>对于 VMWare VM 发现 Azure Migrate 是否需要 vCenter Server？

是，Azure Migrate 需要 vCenter Server 才能在 VMware 环境中执行发现。 它不支持发现不受 vCenter Server 管理的 ESXi 主机。

### <a name="whats-the-difference-between-azure-migrate-server-assessment-and-the-map-toolkit"></a>Azure Migrate Server 评估和地图工具包之间的区别是什么？

服务器评估提供评估以帮助迁移准备情况，并评估要迁移到 Azure 的工作负荷。 [Microsoft 评估和计划（MAP）工具包](https://www.microsoft.com/download/details.aspx?id=7826)可帮助执行其他任务，包括针对较新版本的 Windows 客户端/服务器操作系统和软件使用跟踪的迁移规划。 对于这些方案，请继续使用地图工具包。

### <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>服务器评估与 Site Recovery 部署规划器之间的区别是什么？

服务器评估是一种迁移规划工具。 Site Recovery 部署规划器是一种灾难恢复规划工具。

- **规划本地迁移到 azure**：如果计划将本地服务器迁移到 azure，请使用服务器评估进行迁移规划。 它会评估本地工作负荷，并提供指导和工具来帮助你进行迁移。 迁移计划准备就绪后，可以使用工具（包括 Azure Migrate 服务器迁移）将计算机迁移到 Azure。
- **规划 azure 到 azure 的灾难恢复**：如果计划使用 Site Recovery 设置从本地到 Azure 的灾难恢复，请使用 Site Recovery 部署规划器。 部署规划器提供针对灾难恢复目的的本地环境的深入、Site Recovery 特定的评估。 它提供有关灾难恢复的建议，例如复制和故障转移。

### <a name="whats-the-difference-between-as-on-premises-and-performance-based-sizing"></a>作为本地和基于性能的大小调整有什么区别？

利用按本地大小调整，Azure Migrate 不会考虑 VM 性能数据以进行评估。 它基于本地配置评估 VM 大小。 通过基于性能的大小调整，大小基于利用率数据。

- 例如，如果本地 VM 有4个核心，8 GB 的内存为50% 的 CPU 使用率和50% 的内存使用率，则会发生以下情况：
    - 本地大小调整会推荐一个具有四个核心和 8 GB 内存的 Azure VM SKU。
    - 基于性能的大小调整建议使用具有两个核心和 4 GB 内存的 VM SKU，因为会考虑利用率百分比。

- 同样，磁盘大小取决于两个评估属性：大小调整条件和存储类型。
    - 如果大小调整条件基于性能并且存储类型为 "自动"，则在标识目标磁盘类型（标准或高级）时，Azure Migrate 将使用该磁盘的 IOPS 和吞吐量值。
    - 如果大小调整条件基于性能并且存储类型为 "高级"，则 Azure Migrate 根据本地磁盘的大小推荐高级磁盘 SKU。 当调整大小为 "本地" 并且存储类型为 "标准" 或 "高级" 时，相同的逻辑也适用于磁盘大小调整。

### <a name="does-performance-history-and-utilization-percentile-impact-size-recommendations"></a>性能历史记录和利用率百分比是否会影响大小建议？

这些属性仅适用于基于性能的大小调整。

- Azure Migrate 收集本地计算机的性能历史记录，并使用它在 Azure 中建议 VM 大小和磁盘类型。
- 设备会持续分析本地环境，每隔20秒收集一次实时利用率数据。
- 该设备汇总了每15秒的样本，每隔15分钟创建一个数据点。
- 若要创建数据点，设备将从所有20秒的示例中选择峰值值。
- 设备会将此数据点发送到 Azure。

当你在 Azure 中创建基于性能持续时间和性能历史记录百分比值的评估时，Azure Migrate 将计算有效利用率值，并将其用于调整大小。

- 例如，如果将 "性能持续时间" 设置为 "1"，将 "百分比" 值设置为 "95" 百分点值，则 Azure Migrate 会按收集器按升序对最近发送的15分钟的样本点进行排序，并选择第95百分位值作为有效百分比.
- 使用第95百分位值可确保忽略离群值。 如果使用99% 百分点值，则可以包含离群值。 如果要选择该时间段的高峰使用量，而不缺少任何离群值，请选择 "99%"。

## <a name="server-assessment---dependency-visualization"></a>服务器评估-依赖项可视化


### <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

使用依赖关系可视化可以更自信地评估要迁移的 Vm 组。 依赖关系可视化在运行评估之前交叉检查计算机的依赖关系。 它可帮助确保不会遗留任何内容，从而有助于避免在迁移到 Azure 时出现意外中断。 Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。 [了解更多](concepts-dependency-visualization.md)。

> [!NOTE]
> 依赖关系可视化在 Azure 政府版中不可用。

### <a name="do-i-need-to-pay-to-use-dependency-visualization"></a>是否需要付费才能使用依赖项可视化？
不。 [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>要实现依赖项可视化，是否需要安装任何软件？

若要使用依赖项可视化，你需要在要评估的每台本地计算机上下载并安装代理。

需要在每台计算机上安装以下代理：
- [Microsoft Monitoring Agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果计算机没有 internet 连接，则需要下载并安装 Log Analytics 网关。

不需要这些代理，除非使用的是依赖项可视化。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可将现有的工作区用于依赖项可视化？

是的，你可以将现有工作区附加到迁移项目，并将其用于依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

不能，无法导出依赖关系可视化。 但 Azure Migrate 使用服务映射，你可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)来检索 JSON 格式的依赖项。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-the-dependency-agent"></a>如何自动执行 Microsoft Monitoring Agent （MMA）和依赖项代理的安装？

使用此[脚本安装依赖关系代理](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。 按照这些说明操作，使用命令行或自动化[来安装 MMA](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent) 。 对于 MMA，请使用[此脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了脚本以外，还可以使用 System Center Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)之类的部署工具来部署代理。


### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支持哪些操作系统？

- 查看[MMA 支持的 Windows 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)的列表。
- 查看[MMA 支持的 Linux 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)的列表。

### <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>是否可将依赖项可视化多个小时？
不。 最多可将依赖项可视化到一小时。 您可以返回到历史记录中的特定日期（在历史记录中，每月返回一项），但可视化效果的最大持续时间是一小时。 例如，你可以在依赖关系映射中使用时间段来查看昨天的依赖项，但你只能查看一个小时窗口的依赖项。 但是，可以使用 Azure Monitor 日志来查询更长时间的[依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

### <a name="can-i-use-dependency-visualization-for-groups-of-more-than-10-vms"></a>能否对超过10个 Vm 的组使用依赖项可视化？
可以可视化包含多达10个 Vm 的组的[依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果有10个以上 Vm 的组，我们建议将该组拆分为较小的组，然后将这些依赖项可视化。

## <a name="azure-migrate-server-migration"></a>Azure Migrate 服务器迁移

### <a name="whats-the-difference-between-azure-migrate-server-migration-and-site-recovery"></a>Azure Migrate Server 迁移和 Site Recovery 之间的区别是什么？

- 本地 VMware Vm 的无代理迁移在 Azure Migrate 服务器迁移内处于本机模式。
- 对于 Hyper-v Vm、物理服务器和基于代理的 VMware Vm 的迁移，Azure Migrate 服务器迁移使用 Azure Site Recovery 复制引擎。


## <a name="next-steps"></a>后续步骤
阅读[Azure Migrate 概述](migrate-services-overview.md)。
