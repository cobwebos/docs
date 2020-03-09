---
title: 常见问题-Azure Migrate 中的发现、评估和依赖项分析
description: 获取有关 Azure Migrate 中的发现、评估和依赖关系分析的常见问题的解答。
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 7edc73742b61e4e5e94431c50d14d263bbbea641
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2020
ms.locfileid: "78361819"
---
# <a name="common-questions-about-discovery-assessment-and-dependency-analysis"></a>有关发现、评估和依赖关系分析的常见问题

本文解答了有关 Azure Migrate 中的发现、评估和依赖关系分析的常见问题。 如果你有其他问题，请查看以下文章：

- 有关 Azure Migrate 的[一般问题](resources-faq.md)。
- 关于 Azure Migrate 设备的[问题](common-questions-appliance.md)。
- 有关服务器迁移的[问题](common-questions-server-migration.md)。
- 在[Azure Migrate 论坛](https://aka.ms/AzureMigrateForum)上发布问题



## <a name="how-many-vms-can-i-discover-with-an-appliance"></a>可以使用一个设备发现多少个 Vm？

你最多可以发现10000个 VMware Vm （最多5000个 Hyper-v Vm），并且最多可使用单个设备来250物理服务器。 如果有更多计算机，请查看有关如何缩放[hyper-v](scale-hyper-v-assessment.md)、 [VMware](scale-vmware-assessment.md)和[物理服务器](scale-physical-assessment.md)评估的文章。



## <a name="vm-size-changed-can-i-run-an-assessment-again"></a>VM 大小已更改。 能否再次运行评估？

Azure Migrate 设备不断地收集有关本地计算机的信息，而评估是一个时间点快照。 如果更改了要评估的 VM 上的设置，请使用 "重新计算" 选项，以使用最新更改更新评估。

## <a name="how-do-i-discover-vms-in-a-multitenant-environment"></a>在多租户环境中发现 Vm 如何实现？

- **VMware**：如果环境是在租户之间共享的，并且你不希望在另一个租户的订阅中发现租户的 vm，请创建 vCenter Server 凭据，这些凭据只能访问你想要发现的虚拟机。 然后，在 Azure Migrate 设备中启动发现时使用这些凭据。
- **Hyper-v**：发现使用 hyper-v 主机凭据。 如果 Vm 共享相同的 Hyper-v 主机，当前没有办法分离发现。  


## <a name="do-i-need-vcenter-server"></a>我是否需要 vCenter Server？

是，Azure Migrate 需要 vCenter Server 才能在 VMware 环境中执行发现。 它不支持发现不受 vCenter Server 管理的 ESXi 主机。


## <a name="whats-are-the-sizing-options"></a>大小调整选项有哪些？

利用按本地大小调整，Azure Migrate 不会考虑 VM 性能数据以进行评估。 它基于本地配置评估 VM 大小。 通过基于性能的大小调整，大小基于利用率数据。

- 例如，如果本地 VM 有4个核心，8 GB 的内存为50% 的 CPU 使用率，50% 的内存使用率：
    - 本地大小调整会推荐一个具有四个核心和 8 GB 内存的 Azure VM SKU。
    - 基于性能的大小调整建议使用具有两个核心和 4 GB 内存的 VM SKU，因为会考虑利用率百分比。

- 同样，磁盘大小调整取决于大小标准和存储类型。
    - 如果大小调整条件基于性能并且存储类型为 "自动"，则在标识目标磁盘类型（标准或高级）时，Azure Migrate 将使用该磁盘的 IOPS 和吞吐量值。
    - 如果大小调整条件基于性能并且存储类型为 "高级"，则 Azure Migrate 根据本地磁盘的大小推荐高级磁盘 SKU。 当调整大小为 "本地" 并且存储类型为 "标准" 或 "高级" 时，相同的逻辑也适用于磁盘大小调整。

## <a name="does-performance-historyutilization-impact-sizing"></a>性能历史记录/利用率是否会影响大小？

这些属性仅适用于基于性能的大小调整。

- Azure Migrate 收集本地计算机的性能历史记录，并使用它在 Azure 中建议 VM 大小和磁盘类型。
- 设备会持续分析本地环境，每隔20秒收集一次实时利用率数据。
- 该设备汇总了每15秒的样本，每隔15分钟创建一个数据点。
- 若要创建数据点，设备将从所有20秒的示例中选择峰值值。
- 设备会将此数据点发送到 Azure。

当你在 Azure 中创建基于性能持续时间和性能历史记录百分比值的评估时，Azure Migrate 将计算有效利用率值，并将其用于调整大小。

- 例如，如果将 "性能持续时间" 设置为 "1"，将 "百分比" 值设置为 "95" 百分点值，则 Azure Migrate 会按收集器按升序对最近发送的15分钟的样本点进行排序，并选择第95百分位值作为有效百分比.
- 使用第95百分位值可确保忽略离群值。 如果使用99% 百分点值，则可以包含离群值。 如果要选择该时间段的高峰使用量，而不缺少任何离群值，请选择 "99%"。

## <a name="what-is-dependency-visualization"></a>什么是依赖项可视化？

使用依赖关系可视化可以更自信地评估要迁移的 Vm 组。 依赖关系可视化在运行评估之前交叉检查计算机的依赖关系。 它可帮助确保不会遗留任何内容，从而有助于避免在迁移到 Azure 时出现意外中断。 Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。 [了解更多](concepts-dependency-visualization.md)。

> [!NOTE]
> 依赖关系可视化在 Azure 政府版中不可用。

## <a name="whats-the-difference-between-agent-based-and-agentless"></a>代理和无代理之间的区别是什么？

无代理可视化和基于代理的可视化对象之间的差异在表中进行了总结。

**要求** | **款** | **基于代理**
--- | --- | ---
支持 | 此选项目前为预览版，仅适用于 VMware Vm。 [查看](migrate-support-matrix-vmware.md#agentless-dependency-visualization)支持的操作系统。 | 公开上市（GA）。
代理 | 无需在要交叉检查的计算机上安装代理。 | 要在要分析的每台本地计算机上安装的代理： [Microsoft Monitoring agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)和[依赖项代理](https://docs.microsoft.com/azure/azure-monitor/platform/agents-overview#dependency-agent)。 
先决条件 | [查看](concepts-dependency-visualization.md#agentless-visualization)先决条件和部署要求。 | [查看](concepts-dependency-visualization.md#agent-based-visualization)先决条件和部署要求。
Log Analytics | 不是必需的。 | Azure Migrate 使用[Azure Monitor 日志](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview)中的[服务映射](https://docs.microsoft.com/azure/operations-management-suite/operations-management-suite-service-map)解决方案进行依赖关系可视化。 [了解详细信息](concepts-dependency-visualization.md#agent-based-visualization)。
工作原理 | 捕获启用了依赖关系可视化的计算机上的 TCP 连接数据。 发现后，它会按五分钟的间隔收集数据。 | 计算机上安装的服务映射代理收集有关每个进程的 TCP 进程和入站/出站连接的数据。
数据 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。 | 源计算机服务器名称、进程、应用程序名称。<br/><br/> 目标计算机服务器名称、进程、应用程序名称和端口。<br/><br/> 为 Log Analytics 查询收集和提供连接、延迟和数据传输信息的数目。 
可视化 | 可在一小时到30天内查看单服务器的依赖关系图。 | 单个服务器的依赖关系图。<br/><br/> 仅可在一小时内查看地图。<br/><br/> 一组服务器的依赖关系图。<br/><br/> 在映射视图中添加和删除组中的服务器。
数据导出 | 当前无法以表格格式下载。 | 可以通过 Log Analytics 查询数据。




## <a name="do-i-pay-for-dependency-visualization"></a>我是否需要为依赖关系可视化付费？
No。 [详细了解](https://azure.microsoft.com/pricing/details/azure-migrate/) Azure Migrate 定价。

## <a name="what-do-i-install-for-agent-based-dependency-visualization"></a>对于基于代理的依赖项可视化，我应该安装什么？

若要使用基于代理的依赖项可视化，需要在要评估的每台本地计算机上下载并安装代理。

在每台计算机上安装以下代理：
- [Microsoft Monitoring Agent （MMA）](https://docs.microsoft.com/azure/log-analytics/log-analytics-agent-windows)。
- [依赖关系代理](../azure-monitor/platform/agents-overview.md#dependency-agent)。
- 如果计算机没有 internet 连接，则需要下载并安装 Log Analytics 网关。

你不需要这些代理，除非你使用基于代理的依赖项可视化。

## <a name="can-i-use-an-existing-workspace"></a>是否可以使用现有的的工作区？

是的，对于基于代理的依赖项可视化，你可以将现有工作区附加到迁移项目，并将其用于依赖项可视化。 

## <a name="can-i-export-the-dependency-visualization-report"></a>是否可以导出依赖项可视化报表？

不能，不能导出基于代理的可视化对象中的依赖关系可视化报告。 但 Azure Migrate 使用服务映射，你可以使用[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/machines/listconnections)来检索 JSON 格式的依赖项。

## <a name="can-i-automate-agent-installation"></a>我可以自动安装代理吗？
对于基于代理的依赖项可视化，自动执行以下操作：

- 使用此[脚本安装依赖关系代理](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)。
- 对于 MMA，请按照这些[说明](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)使用命令行或自动化，或使用[此脚本](https://gallery.technet.microsoft.com/scriptcenter/Install-OMS-Agent-with-2c9c99ab)。
- 除了脚本以外，还可以使用部署工具（如 Microsoft Endpoint Configuration Manager 和[Intigua](https://www.intigua.com/getting-started-intigua-for-azure-migration) ）来部署代理。


## <a name="what-operating-systems-does-mma-support"></a>MMA 支持哪些操作系统？

- 查看[MMA 支持的 Windows 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-windows-operating-systems)的列表。
- 查看[MMA 支持的 Linux 操作系统](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)的列表。

## <a name="can-i-visualize-dependencies-for-more-than-an-hour"></a>是否可将依赖项可视化多个小时？
对于基于代理的可视化效果，可将依赖项可视化最多一小时。 您可以返回到历史记录中的特定日期（在历史记录中，每月返回一项），但可视化效果的最大持续时间是一小时。 例如，你可以在依赖关系映射中使用时间段来查看昨天的依赖项，但你只能查看一个小时窗口的依赖项。 但是，可以使用 Azure Monitor 日志来查询更长时间的[依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

对于无代理可视化，你可以查看单个服务器的依赖关系映射，从1小时到30天的持续时间。


## <a name="can-visualize-dependencies-for-groups-of-more-than-10-vms"></a>可以可视化超过10个 Vm 的组的依赖项吗？
可以可视化包含多达10个 Vm 的组的[依赖项](https://docs.microsoft.com/azure/migrate/how-to-create-group-dependencies)。 如果有10个以上 Vm 的组，我们建议将该组拆分为较小的组，然后将这些依赖项可视化。




## <a name="next-steps"></a>后续步骤
阅读[Azure Migrate 概述](migrate-services-overview.md)。
