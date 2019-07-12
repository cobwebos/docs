---
title: Azure Migrate - 常见问题解答 (FAQ) | Microsoft Docs
description: 解答有关 Azure Migrate 的常见问题
author: snehaamicrosoft
ms.service: azure-migrate
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: snehaa
ms.openlocfilehash: 08a0312f12b3daab8b7f5e88da118b5bcbeb2f4c
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807331"
---
# <a name="azure-migrate---frequently-asked-questions-faq"></a>Azure Migrate - 常见问题解答 (FAQ)

本文包含有关 Azure Migrate 的常见问题。 如果在阅读本文之后还有其他问题，请在 [Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)中提问。

## <a name="general"></a>常规

### <a name="which-azure-geographies-are-supported-by-azure-migrate"></a>Azure Migrate 支持哪些 Azure 地域？
Azure Migrate 当前支持地理区域可以在其中创建 Azure Migrate 项目的数。 即使仅可以在这些区域中创建项目，你仍然可以评估其他目标位置的计算机。 项目地域仅用于存储已发现的元数据。

**Geography** | **元数据存储位置**Azure 政府 |美国弗吉尼亚州政府亚洲 |亚洲东南部或亚洲东部欧洲 |欧洲北部或欧洲西部大不列颠及北爱尔兰联合王国 |英国南部或英国西部美国 |美国中部或美国西部 2

### <a name="how-is-azure-migrate-different-from-azure-site-recovery"></a>Azure Migrate 与 Azure Site Recovery 有何不同？

Azure Migrate 提供了可帮助你发现、 评估和将计算机和工作负荷迁移到 Azure 的工具。 [Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/migrate-tutorial-on-premises-azure)是灾难恢复解决方案。 这两个服务共享某些组件。

## <a name="azure-migrate-appliance-vmwarephysical-servers"></a>Azure Migrate 设备 （VMware/物理服务器）

### <a name="how-does-the-azure-migrate-appliance-connect-to-azure"></a>Azure Migrate 设备如何连接到 Azure？

连接可以是通过 internet，也可以使用 ExpressRoute 公共对等互连。

### <a name="what-network-connectivity-requirements-are-needed-for-azure-migrate-server-assessment-and-migration"></a>所需的 Azure 迁移服务器评估和迁移哪些网络连接要求

有关与 Azure 通信的 Url 和端口所需的 Azure Migrate，查看[VMWare](migrate-support-matrix-vmware.md)并[HYPER-V](migrate-support-matrix-hyper-v.md)支持矩阵。

### <a name="can-i-harden-the-appliance-vmware-vm-i-set-up-with-the-ova-template"></a>可以强制执行设备设置使用 OVA 模板的 VMware VM？

其他组件 （例如防病毒） 可以添加到 OVA 模板上，只要 Azure Migrate 重新为设备所需的通信和防火墙规则。   

### <a name="to-harden-the-azure-migrate-appliance-what-are-the-recommended-antivirus-av-exclusions"></a>若要强化 Azure Migrate 设备，你们建议从防病毒软件 (AV) 中排除哪些项？

您需要从设备上扫描中排除以下文件夹：

- 包含有关在 Azure Migrate 服务二进制文件的文件夹。 排除所有子文件夹。
- %ProgramFiles%\ProfilerService  
- Azure Migrate Web 应用程序。 排除所有子文件夹。
- %SystemDrive%\inetpub\wwwroot
- 数据库和日志文件在本地缓存。 在 Azure Migrate 服务需要读/写访问到此文件夹。
  - %SystemDrive%\Profiler

### <a name="what-data-is-collected-by-azure-migrate"></a>Azure Migrate 收集哪些数据？

Azure Migrate 设备收集的本地 Vm，包括元数据：

**VM 的配置数据**
- VM 显示名称（在 vCenter 上）
- VM 库存路径（vCenter 中的主机/群集/文件夹）
- IP 地址
- MAC 地址
- 操作系统
- 核心数、磁盘数、NIC 数
- 内存大小、磁盘大小

**VM 的性能数据**
- CPU 使用率
- 内存使用率
- 对于附加到 VM 的每个磁盘：
  - 磁盘读取吞吐量
  - 磁盘写入吞吐量
  - 每秒的磁盘读取操作次数
  - 每秒的磁盘写入操作次数
- 对于附加到 VM 的每个网络适配器：
  - 进网络
  - 出网络

Tn 此外，如果部署依赖项映射依赖关系映射代理收集信息，包括计算机 FQDN、 操作系统、 IP 地址和 MAC 地址，该 VM，并传入/传出 TCP 连接，在 VM 内运行的进程。 此发现是可选的才使用启用发现的依赖项映射。

### <a name="is-there-any-performance-impact-on-the-analyzed-esxi-host-environment"></a>已分析的 ESXi 主机环境上是否有任何性能影响？

与性能数据的持续分析，Azure Migrate 设备配置文件的本地计算机来度量 VM 的性能数据。 此性能的影响几乎为零的 ESXi 主机之外，以及在 vCenter 服务器上。

### <a name="where-is-the-collected-data-stored-and-for-how-long"></a>收集的数据存储在何处，存储多久？

Azure Migrate 设备收集的数据存储在创建迁移项目时指定的 Azure 位置。 数据安全地存储在 Microsoft 订阅，并在删除时删除 Azure Migrate 项目。

对于依赖项可视化，如果在虚拟机上安装代理依赖关系代理收集的数据存储在美国，在 Azure 订阅中创建的 Log Analytics 工作区中。 在订阅中删除 Log Analytics 工作区时会删除此数据。 [了解详细信息](concepts-dependency-visualization.md)。

### <a name="what-is-the-volume-of-data-uploaded-by-azure-migrate-during-continuous-profiling"></a>什么是持续分析期间，Azure migrate 上传的数据卷？

根据多个参数的数据发送到 Azure Migrate 量而有所不同。 为提供一个表示数字，使用 10 个计算机 （每个与一个磁盘上，一个 NIC），Azure Migrate 项目，将发送大约 50 MB / 天。 这是一个近似值，更改基于数据点数目的 Nic 和磁盘 （发送的数据是非线性如果机、 Nic 或磁盘的数量增加）。

### <a name="is-the-data-encrypted-at-rest-and-in-transit"></a>是加密的数据的静态和传输中？

两个是。 安全地发送到 Azure Migrate 服务通过 internet 通过 https 进行元数据。 元数据存储在[Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/database-encryption-at-rest)，然后在[Azure blob 存储](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)Microsoft 订阅中是静态加密。

依赖关系代理收集的数据也是在加密的传输 (安全的 HTTPS)，并存储在用户订阅中的 Log Analytics 工作区中。 它也是静态加密。

### <a name="how-does-the-azure-migrate-appliance-communicate-with-the-vcenter-server-and-the-azure-migrate-service"></a>Azure Migrate 设备与 vCenter Server 和 Azure Migrate 服务如何通信？

设备连接到 vCenter 服务器 （端口 443） 使用设置设备时提供的凭据。 它将查询 vCenter 服务器使用 VMware PowerCLI，收集有关 Vm 由 vCenter Server 托管的元数据。 它的上个月每个 VM 的性能历史记录以及收集有关虚拟机 （核心、 内存、 磁盘、 NIC 等），这两个配置数据。 然后将收集的元数据发送到 Azure Migrate Server 评估 （通过 HTTPS 通过 internet) 进行评估。 

### <a name="can-i-connect-the-same-appliance-to-multiple-vcenter-servers"></a>可以在同一设备连接到多个 vCenter 服务器？

是的使用单个设备，Azure Migrate 发现多个 vCenter 服务器，但不是能同时。 需要一个接一个运行发现。

### <a name="is-the-ova-template-used-by-site-recovery-integrated-with-the-ova-used-by-azure-migrate"></a>Site Recovery 使用的 OVA 模板是否与 Azure Migrate 使用的 OVA 集成？

当前没有集成。 Site Recovery 中的 .OVA 模板用于为 VMware VM/物理服务器复制设置 Site Recovery 配置服务器。 。使用 Azure migrate OVA 用于发现由 vCenter 服务器管理，适用于评估和迁移目的管理的 VMware Vm。

### <a name="i-changed-my-machine-size-can-i-rerun-an-assessment"></a>我更改了计算机的大小。 可以重新运行评估？
Azure Migrate 设备持续收集有关在本地环境的信息。 但是，评估是在本地 Vm 的时间点快照。 如果更改想要评估的 VM 上的设置，请使用重新计算选项与最新更改更新评估。

### <a name="how-can-i-discover-a-multi-tenant-environment-in-azure-migrate"></a>如何在 Azure Migrate 中发现多租户环境？

对于 VMware，如果必须在租户间共享的环境，并且不想要发现的另一个租户的订阅中的一个租户的 Vm，创建 vCenter 服务器凭据，仅有权你想要发现这些 Vm。 然后在 Azure Migrate 装置中发现在启动时使用的凭据。

对于 HYPER-V，此发现将使用的 HYPER-V 主机的凭据，如果 Vm 共享相同的 HYPER-V 主机，目前无法分离发现。  

### <a name="how-many-vms-can-be-discovered-using-a-single-migration-appliance"></a>可以使用单一迁移设备发现多少 Vm？

可以发现最多 10,000 的 VMware Vm 和多达 5,000 的 HYPER-V Vm 使用单一迁移设备。  如果您有更多的计算机的本地环境中，了解如何缩放[HYPER-V](scale-hyper-v-assessment.md)并[VMware](scale-vmware-assessment.md)评估。


## <a name="azure-migrate-server-assessment"></a>Azure 迁移：Server 评估

### <a name="does-azure-migrate-server-assessment-support-assessment-of-physical-servers"></a>Azure 不会迁移：服务器评估支持物理服务器的评估？

否，Azure Migrate 目前不支持物理服务器的评估。 

### <a name="does-azure-migrate-need-vcenter-server-to-discover-a-vmware-environment"></a>Azure Migrate 是否需要使用 vCenter Server 来发现 VMware 环境？

是的 Azure Migrate 需要 vCenter 服务器，以发现 VMware 环境。 它不支持不由 vCenter 服务器托管的 ESXi 主机的发现。

### <a name="whats-the-difference-between-using-azure-migrate-server-assessment-and-the-map-toolkit"></a>使用 Azure Migrate 之间的区别是什么：Server 评估和 Map 工具包？

Azure 迁移：服务器评估提供了迁移评估，以帮助进行迁移准备情况和评估工作负荷适合迁移到 Azure。 [Microsoft 评估与规划 (MAP) 工具包](https://www.microsoft.com/download/details.aspx?id=7826)具有其他功能，如迁移规划的较新版本的 Windows 客户端和服务器操作系统和软件使用情况跟踪。 对于这些情况，继续使用 MAP Toolkit。

### <a name="how-is-azure-migrate-server-assessment-different-from-azure-site-recovery-deployment-planner"></a>如何为 Azure Migrate:从 Azure Site Recovery 部署规划器不同的服务器评估？

Azure 迁移：服务器评估是迁移规划工具。 Azure Site Recovery 部署规划器是计划工具的灾难恢复。

- **从 VMware/HYPER-V 到 Azure 的迁移**:如果您想要将你的本地服务器迁移到 Azure，请使用 Azure Migrate:针对迁移规划服务器评估工具。 该工具会评估本地工作负荷，并提供指南、 见解和机制，以帮助您迁移到 Azure。 迁移计划准备就绪后，你可以使用 Azure Migrate 等工具：服务器迁移，将计算机迁移到 Azure。
- **从 VMware/Hyper-V 到 Azure 的灾难恢复**：对于到 Azure 中使用 Site Recovery 的灾难恢复，灾难恢复规划使用 Site Recovery 部署规划器。 Site Recovery 部署规划器会在本地环境的深入，特定于站点恢复的评估。 它提供了所需的 Site Recovery 对成功的灾难操作，例如复制和故障转移的 Vm 的建议。 

### <a name="does-azure-migrate-support-enterprise-agreement-ea-based-cost-estimation"></a>Azure Migrate 支持企业协议 EA 基于成本估计？

Azure Migrate 目前不支持的成本估算[企业协议的产品/服务](https://azure.microsoft.com/offers/enterprise-agreement-support/)。 解决方法是指定为产品/服务，即用即付和手动评估属性的折扣字段中指定的折扣百分比 （适用于订阅）。

  ![折扣](./media/resources-faq/discount.png)

### <a name="whats-the-difference-between-as-on-premises-sizing-and-performance-based-sizing"></a>为本地大小调整和基于性能的大小之间的区别是什么？

- 在作为本地大小调整，Azure Migrate 不会考虑 VM 的性能数据。 它会根据在本地配置的 Vm 调整大小。 -在基于性能的大小调整大小调整基于利用率数据。
- 例如，如果在本地 VM 具有 4 个核心和 8 GB 内存的 50%的 CPU 利用率和 50%的内存利用率，按本地大小调整建议了 Azure VM SKU 具有 4 个核心和 8 GB 内存。 基于性能的大小调整，但是，建议 VM SKU 的 2 个核心和 4 GB，因为被视为利用率百分比。
- 同样，磁盘大小调整取决于两个评估属性的大小调整条件和存储类型。
= 如果大小调整条件是基于性能和存储类型为自动，确定目标磁盘类型 （标准或高级） 时，会考虑磁盘的 IOPS 和吞吐量值。
- 如果大小调整条件是基于性能的并且存储类型为高级版，建议的高级磁盘。 所选 SKU 的高级磁盘基于本地磁盘的大小。 使用相同的逻辑来执行操作时的大小调整条件为作为本地大小调整，且存储类型为标准或高级磁盘大小调整。

### <a name="what-impact-does-performance-history-and-percentile-utilization-have-on-the-size-recommendations"></a>性能历史记录和利用百分率对大小建议有什么影响？

这些属性仅适用于基于性能的大小调整。

- Azure Migrate 收集的本地计算机的性能历史记录，并使用它来建议在 Azure 中的 VM 大小和磁盘类型。
- 设备持续配置文件的本地环境以收集每隔 20 秒的实时利用率数据。 设备对时长 20 秒的样本进行汇总，每隔 15 分钟创建单个数据点。 为了创建单个数据点，设备从所有时长 20 秒的样本中选择峰值并将其发送到 Azure。
- 当你在 Azure （基于性能持续时间和性能历史记录的百分位值） 中创建一个评估时，Azure Migrate 计算有效使用率值，并使用它进行大小调整。
- 例如，如果设置为一天，将性能时段和百分位数为 95 百分位，Azure Migrate 使用 15 分钟的示例点发送的最后一天的收集器，对其按升序进行排序和选择为第 95 百分位值有效的使用率。
- 第 95 百分位值可确保正在忽略任何使用达 99%的情况下可能出现的异常消息。 如果希望选取期间的峰值利用率并且不希望错过任何离群值，则应当选择第 99 百分位值。

### <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

依赖项可视化，可更有信心地评估迁移的 Vm 组。 它交叉检查计算机依赖项运行评估之前。 依赖项可视化有助于确保执行任何操作处于后面，并避免意外的中断时将迁移到 Azure。 Azure Migrate 利用 Azure Monitor 日志，以启用依赖项可视化效果中的服务映射解决方案。

> [!NOTE]
> 依赖项可视化功能在 Azure 政府中不可用。

### <a name="do-i-need-to-pay-to-use-the-dependency-visualization-feature"></a>是否需要支付依赖项可视化功能的使用费？

否。 [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。

### <a name="do-i-need-to-install-anything-for-dependency-visualization"></a>要实现依赖项可视化，是否需要安装任何软件？

若要使用依赖项可视化，你需要在要评估的每台本地计算机上下载并安装代理。

- 需要在每台计算机上安装 [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- 需要在每台计算机上安装[依赖项代理](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure)。
- 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 Log Analytics 网关。

除非您使用的依赖项可视化效果，则不需要这些代理。

### <a name="can-i-use-an-existing-workspace-for-dependency-visualization"></a>是否可将现有的工作区用于依赖项可视化？

是的可以将现有的工作区附加到迁移项目，并将它们的依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md#how-does-it-work)。

### <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

否，不能导出依赖项可视化。 但是，由于 Azure Migrate 使用服务映射进行依赖项可视化，因此可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections) 获取 JSON 格式的依赖项。

### <a name="how-can-i-automate-the-installation-of-microsoft-monitoring-agent-mma-and-dependency-agent"></a>如何才能自动安装 Microsoft Monitoring Agent (MMA) 和依赖项代理？

[使用此脚本](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#installation-script-examples)安装的代理。 [请遵循以下说明](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent#install-and-configure-agent)安装 MMA 使用命令行或自动化。 适用于 MMA，利用[此脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。

除了脚本，你可以使用部署工具等 System Center Configuration Manager 中， [Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration)等部署代理。

### <a name="what-operating-systems-are-supported-by-mma"></a>MMA 支持哪些操作系统？

- [查看](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)MMA 支持的 Windows 操作系统的列表。
- [查看]https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems) MMA 支持的 Linux 操作系统的列表。

### <a name="what-are-the-operating-systems-supported-by-the-dependency-agent"></a>通过依赖关系代理支持的操作系统有哪些？

[查看](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-windows-operating-systems)通过依赖关系代理支持的 Windows 操作系统。
[查看](https://docs.microsoft.com/azure/monitoring/monitoring-service-map-configure#supported-linux-operating-systems)依赖关系代理支持的 Linux 操作系统的列表。

### <a name="can-i-visualize-dependencies-in-azure-migrate-for-more-than-an-hour"></a>可以为多个一小时可视化中 Azure Migrate 的依赖关系？
不可以，你可以可视化依赖项的最多一小时。 可以返回到某一特定日期中的历史记录，最后一个月内，最多，但可视化效果的最大持续时间为一小时。 例如，可以使用依赖项映射中的持续时间来查看针对昨天，依赖项，但只可以为一小时窗口查看它。 但是，可以使用 Azure Monitor 日志传输到[查询依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)通过更长的时间。

### <a name="is-dependency-visualization-supported-for-groups-with-more-than-ten-vms"></a>支持具有 10 个以上的 Vm 的组依赖项可视化效果？
你可以[可视化组依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)与最多十个 Vm。 如果必须具有 10 个以上的 Vm 的组，我们建议您将拆分为较小的组中的组，然后可视化的依赖项。

## <a name="azure-migrate-server-migration"></a>Azure 迁移：服务器迁移

### <a name="how-is-azure-migrate-server-migration-different-from-azure-site-recovery"></a>如何为 Azure Migrate:Azure Site Recovery 从不同的服务器迁移？

Azure 迁移：服务器迁移利用基于代理的迁移的服务器到 Azure Site Recovery 的复制引擎。
## <a name="next-steps"></a>后续步骤
阅读 [Azure Migrate 概述](migrate-services-overview.md)
