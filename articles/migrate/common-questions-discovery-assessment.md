---
title: 有关 Azure 迁移中的发现、评估和依赖关系分析的问题
description: 获取有关 Azure 迁移中发现、评估和依赖项分析的常见问题的解答。
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: b4b2a50bc88768d46c82f6bce73447dc901e5dfd
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/21/2020
ms.locfileid: "81681902"
---
# <a name="discovery-assessment-and-dependency-analysis---common-questions"></a>发现、评估和依赖分析 - 常见问题

本文回答了有关 Azure 迁移中的发现、评估和依赖项分析的常见问题。 如果您有其他问题，请检查以下资源：

- 有关 Azure 迁移的[一般问题](resources-faq.md)
- 有关[Azure 迁移设备的问题](common-questions-appliance.md)
- 有关[服务器迁移](common-questions-server-migration.md)的问题
- 在[Azure 迁移论坛](https://aka.ms/AzureMigrateForum)中回答问题


## <a name="what-geographies-are-supported-for-discovery-and-assessment-with-azure-migrate"></a>Azure 迁移支持哪些地理位置用于发现和评估？

查看[公共](migrate-support-matrix.md#supported-geographies-public-cloud)和政府[云](migrate-support-matrix.md#supported-geographies-azure-government)的支持地理位置。


## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>使用设备可以发现多少个 VM？

通过使用单个设备，您可以发现多达 10，000 个 VMware VM、多达 5，000 台 Hyper-VM 和多达 250 台物理服务器。 如果您有更多计算机，请阅读有关缩放[Hyper-V 评估](scale-hyper-v-assessment.md)、[缩放 VMware 评估](scale-vmware-assessment.md)或[缩放物理服务器评估](scale-physical-assessment.md)。

## <a name="i-cant-see-some-vm-types-in-azure-government"></a>我在 Azure 政府中看不到某些 VM 类型

支持评估和迁移的 VM 类型取决于 Azure 政府位置的可用性。 您可以在 Azure 政府中[查看和比较](https://azure.microsoft.com/global-infrastructure/services/?regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia&products=virtual-machines)VM 类型。


## <a name="the-size-of-my-vm-changed-can-i-run-an-assessment-again"></a>我的 VM 大小已更改。 我可以再次进行评估吗？

Azure 迁移设备不断收集有关本地环境的信息。  评估是本地 VM 的时间点快照。 如果更改要评估的 VM 上的设置，请使用重新计算选项使用最新更改更新评估。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>如何在多租户环境中发现 VM？

- **VMware**：如果环境在租户之间共享，并且您不希望在另一个租户的订阅中发现租户的 VM，请创建 VMware vCenter Server 凭据，该凭据只能访问要发现的 VM。 然后，在 Azure 迁移设备中开始发现时使用这些凭据。
- **Hyper-V**： 发现使用超 V 主机凭据。 如果 VM 共享同一个 Hyper-V 主机，则当前无法分离发现。  

## <a name="do-i-need-vcenter-server"></a>我需要 vCenter 服务器吗？

是的，Azure 迁移需要 VMware 环境中的 vCenter 服务器来执行发现。 Azure 迁移不支持发现 vCenter 服务器未管理的 ESXi 主机。

## <a name="what-are-the-sizing-options"></a>大小调整选项是什么？

使用本地大小调整时，Azure 迁移不考虑 VM 性能数据进行评估。 Azure 迁移根据本地配置评估 VM 大小。 使用基于性能的尺寸调整，大小调整基于利用率数据。

例如，如果本地 VM 具有四个内核和 8 GB 内存，CPU 利用率为 50%，内存利用率为 50%：
- 作为本地大小调整将建议具有四个内核和 8 GB 内存的 Azure VM SKU。
- 基于性能的尺寸调整将建议具有两个内核和 4 GB 内存的 VM SKU，因为考虑了利用率百分比。

同样，磁盘大小调整取决于大小调整条件和存储类型：
- 如果大小调整条件基于性能，并且存储类型是自动的，则 Azure 迁移在标识目标磁盘类型（标准磁盘或高级磁盘）时，会考虑磁盘的 IOPS 和吞吐量值。
- 如果大小调整条件基于性能，并且存储类型为高级，则 Azure 迁移建议根据本地磁盘的大小使用高级磁盘 SKU。 当大小调整为本地大小调整且存储类型为"标准"或"高级"时，相同的逻辑应用于磁盘大小调整。

## <a name="does-performance-history-and-utilization-affect-sizing"></a>性能历史记录和利用率是否影响大小调整？

是，性能历史记录和利用率会影响 Azure 迁移中大小调整。

### <a name="performance-history"></a>性能历史记录

对于仅针对基于性能的大小调整，Azure 迁移会收集本地计算机的性能历史记录，然后使用它在 Azure 中推荐 VM 大小和磁盘类型：

1. 设备持续对本地环境进行配置文件，以便每 20 秒收集一次实时利用率数据。
1. 产品将收集的 20 秒样本汇总一次，并使用它们每 15 分钟创建一个数据点。
1. 要创建数据点，设备将从所有 20 秒的样本中选择峰值。
1. 设备将数据点发送到 Azure。

### <a name="utilization"></a>利用

在 Azure 中创建评估时，根据性能持续时间和设置的性能历史记录百分位数值，Azure 迁移将计算有效利用率值，然后使用它进行大小调整。

例如，如果将性能持续时间设置为一天，百分位数值设置为第 95 百分位，Azure 迁移会按升序对收集器过去一天发送的 15 分钟采样点进行排序。 它选择第 95 个百分位值作为有效利用。

使用第 95 个百分位值可确保忽略异常值。 如果 Azure 迁移使用第 99 个百分位数，则可能包含异常值。 要选择期间的峰值使用值而不错过任何异常值，将 Azure 迁移设置为使用第 99 个百分位数。

## <a name="how-are-import-based-assessments-different-from-assessments-with-discovery-source-as-appliance"></a>基于导入的评估与使用发现源作为设备的评估有什么不同？

基于导入的评估是使用 CSV 文件导入 Azure 迁移的计算机创建的评估。 导入只需四个字段：服务器名称、内核、内存和操作系统。 以下是需要注意的一些事项： 
 - 在基于导入的对引导类型参数的评估中，就绪条件不太严格。 如果未提供引导类型，则假定计算机具有 BIOS 启动类型，并且计算机未标记为 **"有条件就绪**"。 在使用发现源作为设备进行的评估中，如果缺少启动类型，就绪状态将标记为 **"有条件就绪**"。 此就绪性计算差异是因为，在基于导入的评估完成时，用户可能没有有关迁移规划早期阶段计算机的所有信息。 
 - 基于性能的导入评估使用用户提供的利用率值进行正确的大小调整计算。 由于利用率值由用户提供，因此在评估属性中禁用**性能历史记录**和**百分位数利用率**选项。 在使用发现源作为设备进行评估时，所选百分位数值将从设备收集的性能数据中选取。

## <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

依赖项可视化可帮助您更自信地评估要迁移的 VM 组。 依赖项可视化在运行评估之前交叉检查计算机依赖项。 它有助于确保不会留下任何内容，并且有助于避免迁移到 Azure 时意外中断。 Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md)。

> [!NOTE]
> Azure 政府中不提供基于代理的依赖项分析。 您可以使用无代理依赖项分析

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>基于代理和无代理之间的区别是什么？

表中总结了无代理可视化和基于代理的可视化之间的区别。

**要求** | **无代理** | **基于代理**
--- | --- | ---
支持 | 此选项当前处于预览状态，仅适用于 VMware VM。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-analysis-requirements)支持的操作系统。 | 在一般可用性 （GA） 中。
代理 | 无需在要交叉检查的计算机上安装代理。 | 要安装在要分析的每个本地计算机上的代理[：Microsoft 监视代理 （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[依赖项代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
先决条件 | [查看](concepts-dependency-visualization.md#agentless-analysis)先决条件和部署要求。 | [查看](concepts-dependency-visualization.md#agent-based-analysis)先决条件和部署要求。
Log Analytics | 非必需。 | Azure 迁移使用[Azure 监视器日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案进行依赖项可视化。 [了解详细信息](concepts-dependency-visualization.md#agent-based-analysis)。
工作原理 | 在启用依赖项可视化的计算机上捕获 TCP 连接数据。 发现后，它每隔五分钟收集数据。 | 安装在计算机上的服务映射代理收集有关每个进程的 TCP 进程和入站/出站连接的数据。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 收集连接数、延迟和数据传输信息，并可用于日志分析查询。 
可视化 | 单个服务器的依赖项映射可在 1 小时到 30 天的持续时间内查看。 | 单个服务器的依赖项映射。<br/><br/> 地图只能查看一个多小时。<br/><br/> 一组服务器的依赖项映射。<br/><br/> 从地图视图添加和删除组中的服务器。
数据导出 | 当前无法以表格格式下载。 | 数据可以通过日志分析进行查询。

## <a name="do-i-pay-for-dependency-visualization"></a>我为依赖项可视化付费吗？

不是。 了解有关[Azure 迁移定价](https://azure.microsoft.com/pricing/details/azure-migrate/)的更多详细信息。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>对于基于代理的依赖项可视化，我应安装什么？

要使用基于代理的依赖项可视化，请在要评估的每个本地计算机上下载和安装代理：

- [微软监控代理（MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)
- [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)
- 如果您有没有互联网连接的计算机，请下载并安装日志分析网关。

仅当使用基于代理的依赖项可视化效果时，才需要这些代理。

## <a name="can-i-use-an-existing-workspace"></a>是否可以使用现有的的工作区？

是的，对于基于代理的依赖项可视化，您可以将现有工作区附加到迁移项目，并将其用于依赖项可视化。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

否，无法导出基于代理的可视化中的依赖项可视化报表。 但是，Azure 迁移使用服务映射，您可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)以 JSON 格式检索依赖项。

## <a name="can-i-automate-agent-installation"></a>我可以自动安装代理吗？

对于基于代理的依赖项可视化：

- 使用[脚本安装依赖项代理](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。
- 对于 MMA，[请使用命令行或自动化](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)，或使用[脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除了脚本之外，您还可以使用部署工具（如 Microsoft 终结点配置管理器和[Intigua）](https://www.intigua.com/getting-started-intigua-for-azure-migration)来部署代理。

## <a name="what-operating-systems-does-mma-support"></a>MMA 支持哪些操作系统？

- 查看 MMA[支持的 Windows 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)的列表。
- 查看 MMA[支持的 Linux 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)的列表。

## <a name="can-i-visualize-dependencies-for-more-than-one-hour"></a>我可以可视化依赖项超过一小时吗？

对于基于代理的可视化，您可以可视化依赖项长达一个小时。 您可以追溯到历史记录中的特定日期长达一个月，但可视化的最大持续时间为 1 小时。 例如，可以使用依赖项映射中的工期来查看昨天的依赖项，但只能查看一小时窗口的依赖项。 但是，可以使用 Azure 监视器日志[查询依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)的时间较长。

对于无代理可视化，您可以查看单个服务器的依赖项映射，持续时间为 1 小时到 30 天。

## <a name="can-i-visualize-dependencies-for-groups-of-more-than-10-vms"></a>是否可以可视化超过 10 个 VM 的组的依赖项？

您可以可视化最多具有 10 个 VM 的组[的依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果组具有 10 个以上 VM，我们建议您将组拆分为较小的组，然后可视化依赖项。

## <a name="next-steps"></a>后续步骤

阅读[Azure 迁移概述](migrate-services-overview.md)。
