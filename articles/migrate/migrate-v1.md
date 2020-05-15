---
title: 使用以前版本的 Azure Migrate
description: 描述如何使用以前版本的 Azure Migrate。
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/19/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 7b02560d1e7b7c34a4d87dbdc468a85362aca4f7
ms.sourcegitcommit: 309a9d26f94ab775673fd4c9a0ffc6caa571f598
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/09/2020
ms.locfileid: "82993806"
---
# <a name="work-with-the-previous-version-of-azure-migrate"></a>使用以前版本的 Azure Migrate

本文提供了有关使用以前版本的 Azure Migrate 的信息。


有两个版本的 Azure Migrate 服务：

- **当前版本**：使用此版本创建 Azure Migrate 项目，发现本地计算机，并安排评估和迁移。 [详细了解](whats-new.md)此版本中的新增功能。
- **以前版本**：如果你使用的是以前版本的 Azure Migrate（仅支持对本地 VMware VM 的评估），则现在应使用当前版本。 如果仍需使用在以前版本中创建的 Azure Migrate 项目，那么以下是可以执行与不可执行的操作：
    - 不能再创建迁移项目。
    - 建议不要执行新的发现。
    - 仍可以访问现有项目。
    - 仍可以运行评估。
    

## <a name="upgrade-between-versions"></a>进行版本之间的升级

无法将以前版本中的项目或组件升级到新版本。 需要[创建新的 Azure Migrate 项目](how-to-add-tool-first-time.md)并向其添加评估和迁移工具。

## <a name="find-projects-from-previous-version"></a>从以前的版本中查找项目

从以前的版本中查找项目的操作如下：

1. 在“Azure 门户”>“所有服务”中搜索并选择“Azure Migrate”   。 
2. Azure Migrate 仪表板上提供了一个通知和一个用于访问旧 Azure Migrate 项目的链接。
3. 单击链接以打开 v1 项目。


## <a name="create-an-assessment"></a>创建评估

在门户中发现 VM 后，可将其进行分组并创建评估。

- 在门户中发现 VM 后，可以立即创建本地评估。
- 在创建基于性能的评估之前，建议等待至少一天，以获得可靠的大小建议。

创建评估的操作如下：

1. 在项目的“概述”页中，单击“+ 创建评估”。  
2. 单击“全部查看”查看评估属性  。
3. 创建组并指定组名称。
4. 选择要添加到该组的计算机。
5. 单击“创建评估”，以创建该组和评估。 
6. 创建评估后，在“概述” > “仪表板”中查看该评估。  
7. 单击“导出评估”，将评估下载为 Excel 文件。 

若要使用最新的性能数据更新现有的评估，可以对评估使用“重新计算”命令来更新它。 

## <a name="review-an-assessment"></a>查看评估 

评估包含三个阶段：

- 评估从适用性分析开始，以确定计算机在 Azure 中是否兼容。
- 调整大小估算。
- 每月成本估算。

如果一台计算机通过了前一个阶段，则只能按顺序进入下一个阶段。 例如，如果一台计算机未通过适用性检查，将被标记为不适合迁移到 Azure，并且不会进行大小调整和成本估算。


### <a name="review-azure-readiness"></a>查看 Azure 迁移就绪性

评估中的 Azure 就绪性视图显示每个 VM 的就绪状态。

**就绪情况** | **State** | **详细信息**
--- | --- | ---
已做好 Azure 迁移准备 | 无兼容性问题。 计算机可按原样迁移到 Azure，它将在 Azure 中启动，并完全支持 Azure。 | 对于准备就绪的 VM，Azure Migrate 会推荐一个适合 Azure 的 VM 大小。
已做好特定条件下的 Azure 迁移准备 | 计算机可能在 Azure 中启动，但 Azure 可能不提供完全支持。 例如，Azure 中不支持具有旧版 Windows Server 的计算机。 | Azure Migrate 说明了就绪情况问题，并提供更正步骤。
尚未做好 Azure 迁移准备 |  VM 不会在 Azure 中启动。 例如，如果 VM 所具有的磁盘大于 4 TB，则不能在 Azure 上托管。 | Azure Migrate 说明了就绪情况问题，并提供更正步骤。
就绪性未知 | Azure Migrate 无法标识 Azure 迁移就绪性，通常是因为数据不可用。


#### <a name="azure-vm-properties"></a>Azure VM 属性
就绪情况考虑了许多 VM 属性，以确定 VM 是否可以在 Azure 中运行。


**属性** | **详细信息** | **就绪情况**
--- | --- | ---
**启动类型** | 支持 BIOS。 不支持 UEFI。 | 如果启动类型为 UEFI，则状态为有条件的就绪。
**核心数** | 计算机核心数 <= Azure VM 支持的最大核心数 (128)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的核心数。<br/>如果在评估设置中指定了舒适因子，则将已利用的内核数乘以此舒适因子。<br/><br/> 如果没有任何性能历史记录，Azure Migrate 将使用已分配的内核数，而不应用舒适因子。 | 如果小于或等于限制，则状态为就绪。
**内存** | 计算机内存大小 <= Azure VM 支持的最大内存（Azure M 系列 Standard_M128m&nbsp;<sup>2</sup> 上为 3892 GB）。 [了解详细信息](https://docs.microsoft.com/azure/virtual-machines/windows/sizes)。<br/><br/> 如果性能历史记录可用，Azure Migrate 会考虑已利用的内存。<br/><br/>如果指定了舒适因子，则将已利用的内存乘以此舒适因子。<br/><br/> 如果没有任何历史记录，将使用已分配的内存，而不应用舒适因子。<br/><br/> | 如果在限制范围内，则状态为就绪。
**存储磁盘** | 分配的磁盘大小必须为 4 TB (4096 GB) 或更小。<br/><br/> 连接到计算机的磁盘（包括操作系统磁盘）数必须为 65 个或更少。 | 如果在限制范围内，则状态为就绪。
**网络** | 连接到计算机的 NIC 数必须为 32 个或更少。 | 如果在限制范围内，则状态为就绪。

#### <a name="guest-operating-system"></a>来宾操作系统

除了 VM 属性，Azure Migrate 还会查看本地 VM 的来宾 OS 以确认该 VM 是否可在 Azure 中运行。

- Azure Migrate 会考虑 vCenter Server 中所指定的 OS。
- 由于 Azure Migrate 执行的发现是基于设备的，它没有办法验证 VM 内部运行的 OS 是否与 vCenter Server 中所指定的 OS 相同。

使用了以下逻辑。

**操作系统** | **详细信息** | **就绪情况**
--- | --- | ---
Windows Server 2016 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2012 R2 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2012 和所有 SP | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2008 R2 和所有 SP | Azure 提供完全支持。| 已做好 Azure 迁移准备
Windows Server 2008（32 位和 64 位） | Azure 提供完全支持。 | 已做好 Azure 迁移准备
Windows Server 2003、2003 R2 | 不支持，需要[自定义支持协议 (CSA)](https://aka.ms/WSosstatement) 才可获取 Azure 支持。 | Azure 有条件的就绪，请考虑在迁移到 Azure 前升级 OS。
Windows 2000、98、95、NT、3.1、MS-DOS | 不支持。 计算机可能在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Windows Client 7、8 和 10 | Azure 仅支持 [Visual Studio 订阅。](https://docs.microsoft.com/azure/virtual-machines/windows/client-images) | 已做好特定条件下的 Azure 迁移准备
Windows 10 专业版桌面 | Azure 提供了对[多租户托管权限](https://docs.microsoft.com/azure/virtual-machines/windows/windows-desktop-multitenant-hosting-deployment)的支持。 | 已做好特定条件下的 Azure 迁移准备
Windows Vista、XP Professional | 不支持。 计算机可能在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前升级 OS。
Linux | Azure 予以认可这些 [Linux 操作系统](../virtual-machines/linux/endorsed-distros.md)。 其他 Linux 操作系统可能在 Azure 中启动，但建议在迁移到 Azure 前将 OS 升级到认可的版本。 | 如果版本受到认可，则为 Azure 已就绪。<br/><br/>如果版本不受认可，则为 Azure 有条件的就绪。
其他操作系统<br/><br/> 例如 Oracle Solaris、Apple Mac 操作系统、FreeBSD 等。 | Azure 不认可这些操作系统。 计算机课在 Azure 中启动，但 Azure 不提供 OS 支持。 | Azure 有条件的就绪，建议在迁移到 Azure 前安装已升级的 OS。  
vCenter Server 中指定为“其他”  的 OS | 在此情况下，Azure Migrate 无法确认 OS。 | 就绪性未知。 确保 VM 内部运行的 OS 在 Azure 中受到支持。
32 位操作系统 | 计算机可在 Azure 中启动，但 Azure 可能不会提供完全支持。 | Azure 有条件的就绪，在迁移到 Azure 前考虑将计算机的 OS 从 32 位 OS 升级到 64 位 OS。


### <a name="review-sizing"></a>查看大小调整

 Azure Migrate 大小建议取决于在评估属性中指定的大小调整条件。

- 如果大小调整条件基于性能，则在提供大小建议时，会考虑 VM（CPU 和内存）及磁盘（IOPS 和吞吐量）的性能历史记录。
- 如果大小调整条件是“作为本地部署”，则 Azure 中的大小建议会根据本地 VM 的大小做出。 磁盘大小调整基于评估属性中指定的“存储”类型（默认为高级磁盘）。 Azure Migrate 不会考虑 VM 和磁盘的性能数据。

### <a name="review-cost-estimates"></a>查看成本估算

成本估算显示在 Azure 中运行 VM 的总计算和存储成本以及每台计算机的详细信息。

- 成本估算是使用针对 VM 计算机及其磁盘和评估属性提供的大小建议估算的。
- 将会对于组中的所有 VM 合计计算和存储的每月估算费用。
- 成本估算适用于将本地 VM 作为 Azure 基础结构即服务 (IaaS) VM 运行。 Azure Migrate 不考虑平台即服务 (PaaS) 或服务型软件 (SaaS) 成本。

### <a name="review-confidence-rating-performance-based-assessment"></a>查看置信度分级（基于性能的评估）

每个基于性能的评估都与置信度分级相关联。

- 置信度分级的范围从一星到五星不等（一星表示分级最低，五星表示分级最高）。
- 为评估分配置信度分级时，会考虑到进行评估计算时所需数据点的可用性。
- 对评估的置信度分级可以用来评估 Azure Migrate 提供的大小建议的可靠性。
- 置信度分级不适用于“按原样”本地评估。

若要基于性能进行大小调整，Azure Migrate 需要以下信息：
- CPU 的使用率数据。
- VM 内存数据。
- 对于每个附加到 VM 的磁盘，它需要磁盘 IOPS 和吞吐量数据。
- 对于每个附加到 VM 的网络适配器，Azure Migrate 需要网络流入/流出量。
- 如果未提供上述任一信息，则大小建议（以及由此得出的置信度分级）可能不可靠。


根据可用数据点的百分比，可能的置信度分级汇总在表中。

**数据点的可用性** | **置信度分级**
--- | ---
0%-20% | 1 星
21%-40% | 2 星
41%-60% | 3 星
61%-80% | 4 星
81%-100% | 5 星


#### <a name="assessment-issues-affecting-confidence-ratings"></a>影响置信度分级的评估问题

由于以下原因，评估时并非所有数据点都可用：

- 未在评估期间分析环境。 例如，如果创建性能持续时间设置为一天的评估，则在对所有数据点启用发现之后，必须等待至少一天才能收集。
- 一些 VM 在进行评估计算期间关闭。 如果任何 VM 停机了一段时间，则 Azure Migrate 无法收集该时段的性能数据。
- 一些 VM 创建于评估计算期间。 例如，如果使用上个月的性能历史记录创建评估，但一周前在环境中创建了多个 VM，则新 VM 的性能历史记录不在整个持续期间内。

> [!NOTE]
> 如果任何评估的置信度分级低于五星，请等待至少一天以便设备对环境进行分析，然后重新计算评估。 如果未基于性能进行大小调整，则大小可能不可靠。 如果不希望重新计算，建议通过更改评估属性，切换为作为本地部署进行大小调整。



## <a name="create-groups-using-dependency-visualization"></a>使用依赖项可视化创建组

除手动创建组之外，还可以使用依赖项可视化创建组。
- 如果希望在运行评估之前通过交叉检查计算机依赖项评估可信度较高的组，通常都会使用此方法。
- 依赖项可视化有助于有效地计划如何迁移到 Azure。 它有助于确保在迁移到 Azure 的过程中不会遗留任何内容，也不会发生意外中断。
- 可以发现所有需要一起迁移的互相依赖的系统，并识别运行中的系统仍然为用户提供服务还是在等待解除授权而非迁移。
- Azure Migrate 使用 Azure Monitor 中的“服务映射”解决方案来实现依赖项可视化。

> [!NOTE]
> 依赖项可视化在 Azure 政府中不可用。

若要设置依赖项可视化，请将 Log Analytics 工作区域 Azure Migrate 项目相关联，在想要可视化依赖项的计算机上安装代理，然后使用依赖项信息创建组。 



### <a name="associate-a-log-analytics-workspace"></a>关联 Log Analytics 工作区

若要使用依赖项可视化，请将 Log Analytics 工作区与迁移项目相关联。 只能在创建迁移项目的同一订阅中创建或附加工作区。

1. 若要将 Log Analytics 工作区附加到项目，请在“概述”>“Essentials”中，单击“需要配置”    。
2. 可以创建新的工作区或附加现有的工作区：
  - 若要创建新的工作区，请指定名称。 在与迁移项目相同的 [Azure 地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)中的区域内创建工作区。
  - 附加现有的工作区时，可以从迁移项目所在订阅中的所有可用工作区进行选择。 只有在[受支持的服务映射区域](../azure-monitor/insights/vminsights-enable-overview.md#prerequisites)中创建的那些工作区才会列出。 若要附加工作区，请确保对该工作区有“读取者”访问权限。

> [!NOTE]
> 你无法更改与迁移项目关联的工作区。

### <a name="download-and-install-vm-agents"></a>下载并安装 VM 代理

配置工作区后，请在要评估的每个本地计算机上下载并安装代理。 此外，如果计算机未连接到 Internet，则需要在计算机上下载并安装 [Log Analytics 网关](../azure-monitor/platform/gateway.md)。

1. 在“概述”  中，单击“管理”   > “计算机”  ，然后选择所需的计算机。
2. 在“依赖项”  列中，单击“安装代理”  。
3. 在要评估的每台 VM 上，从“依赖项”页下载并安装 Microsoft Monitoring Agent (MMA) 和依赖项代理  。
4. 复制工作区 ID 和密钥。 在本地计算机上安装 MMA 时将需要这些信息。

> [!NOTE]
> 若要自动安装代理，可以使用部署工具（例如 Configuration Manager）或合作伙伴工具（如 [Intigua](https://www.intigua.com/intigua-for-azure-migration)，该工具为 Azure Migrate 提供了代理部署解决方案）。


#### <a name="install-the-mma-agent-on-a-windows-machine"></a>在 Windows 计算机上安装 MMA 代理

若要在 Windows 计算机上安装代理：

1. 双击下载的代理。
2. 在“欢迎”页面上，单击“下一步”。   在“许可条款”  页面上，单击“我接受”  以接受许可协议。
3. 在“目标文件夹”  中，保留或修改默认安装文件夹，然后选择“下一步”  。
4. 在“代理安装选项”  中，选择“Azure Log Analytics”   > “下一步”  。
5. 单击“添加”  以添加 Log Analytics 工作区。 粘贴从门户复制的工作区 ID 和密钥。 单击“下一步”。 

可以从命令行或使用自动化方法（如 Configuration Manager）安装代理。 [详细了解](../azure-monitor/platform/log-analytics-agent.md#installation-and-configuration)如何使用这些方法安装 MMA 代理。

#### <a name="install-the-mma-agent-on-a-linux-machine"></a>在 Linux 计算机上安装 MMA 代理

若要在 Linux 计算机上安装代理：

1. 使用 scp/sftp 将相应的捆绑包（x86 或 x64）传输到 Linux 计算机。
2. 使用 --install 参数安装捆绑包。

    ```sudo sh ./omsagent-<version>.universal.x64.sh --install -w <workspace id> -s <workspace key>```

[详细了解 MMA 支持的 Linux 操作系统的列表](https://docs.microsoft.com/azure/log-analytics/log-analytics-concept-hybrid#supported-linux-operating-systems)。

### <a name="install-the-mma-agent-on-a-machine-monitored-by-operations-manager"></a>在受 Operations Manager 监视的计算机上安装 MMA 代理

受 System Center Operations Manager 2012 R2 或更高版本监视的计算机无需安装 MMA 代理。 服务映射与 Operations Manager MMA 集成，以收集必要的依赖项数据。 [了解详细信息](https://docs.microsoft.com/azure/azure-monitor/insights/service-map-scom#prerequisites)。 需要安装依赖项代理。

### <a name="install-the-dependency-agent"></a>安装依赖项代理

1. 若要在 Windows 计算机上安装依赖项代理，请双击安装程序文件，然后按照向导操作。
2. 若要在 Linux 计算机上安装依赖项代理，请使用以下命令安装为根目录：

    ```sh InstallDependencyAgent-Linux64.bin```

- 详细了解针对 Windows 和 Linux 操作系统的 [Dependency agent 支持](../azure-monitor/insights/vminsights-enable-overview.md#supported-operating-systems)。
- [详细了解](../azure-monitor/insights/vminsights-enable-hybrid-cloud.md#installation-script-examples)如何使用脚本来安装依赖项代理。

>[!NOTE]
> 参考的“用于 VM 的 Azure Monitor”一文提供了部署 Dependency agent 的系统先决条件和方法的概述，也适用于服务映射解决方案。

### <a name="create-a-group-with-dependency-mapping"></a>通过依赖项映射创建组

1. 安装代理后，请转到门户并单击“管理”   > “计算机”  。
2. 搜索在其中安装代理的计算机。
3. 计算机的“依赖项”列现在应显示为“查看依赖项”   。 单击该列，查看计算机的依赖项。
4. 计算机的依赖项映射显示以下详细信息：
    - 与计算机的入站（客户端）和出站（服务器）TCP 连接
        - 没有安装 MMA 和依赖项代理的依赖计算机按端口号进行分组。
        - 安装了 MMA 和依赖项代理的依赖计算机显示在单独的框中。
    - 计算机内运行的进程，可以展开每个计算机框查看这些进程
    - 显示包括 FQDN 在内的计算机属性、操作系统和 MAC 地址。 可以单击每个计算机框以查看详细信息。

4. 可通过单击时间范围标签中的持续时间，查看不同持续时间的依赖项。 时间范围默认为 1 小时。 你可以修改时间范围，或指定开始和结束日期以及持续时间。

   > [!NOTE]
   >    支持最长为一小时的时间范围。 使用 Azure Monitor 日志[查询较长持续时间的依赖项数据](https://docs.microsoft.com/azure/migrate/how-to-create-group-machine-dependencies)。

5. 确定想要分成一组的依赖计算机之后，按住 Ctrl 键单击，在映射上选择多台计算机，然后单击“分组计算机”  。
6. 指定组名。 确认 Azure Migrate 已发现依赖计算机。

    > [!NOTE]
    > 如果依赖计算机未被 Azure Migrate 发现，则无法将其添加到组中。 要将此类计算机添加到组中，需要再次在 vCenter Server 的正确范围内运行发现进程，确保 Azure Migrate 发现该计算机。  

7. 如果想要为该组创建评估，请选中相应复选框，为该组创建新的评估。
8. 单击“确定”  以保存组。

创建组后，建议在该组中的所有计算机上安装代理，然后通过可视化整个组的依赖项优化该组。

## <a name="query-dependency-data-from-azure-monitor-logs"></a>查询 Azure Monitor 日志中的依赖项数据

服务映射捕获的依赖项数据可用于在与 Azure Migrate 项目关联的 Log Analytics 工作区中进行查询。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)可在 Azure Monitor 日志中查询的服务映射数据表。 

若要运行 Kusto 查询，请执行以下操作：

1. 安装代理后，请转到门户并单击“概述”  。
2. 在“概述”  中，转到项目的“Essentials”  部分，然后单击“OMS 工作区”  旁边提供的工作区名称。
3. 在“Log Analytics 工作区”页上，单击“常规”   > “日志”  。
4. 编写查询以使用 Azure Monitor 日志收集依赖项数据。 在下一部分查找示例查询。
5. 通过单击“运行”，运行查询。 

[详细了解](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal)如何编写 Kusto 查询。 

### <a name="sample-azure-monitor-logs-queries"></a>Azure Monitor 日志示例查询

以下是可用于提取依赖项数据的示例查询。 可修改查询以提取首选数据点。 [此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#log-analytics-records)提供依赖项数据记录中字段的详尽列表。 更多示例查询，请参阅[此处](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#sample-log-searches)。

#### <a name="summarize-inbound-connections-on-a-set-of-machines"></a>总结一组计算机上的入站连接

针对连接指标的表中的记录 VMConnection 不显示单个物理网络连接。 多个物理网络连接分组到一个逻辑连接中。 [详细了解](https://docs.microsoft.com/azure/azure-monitor/insights/service-map#connections)物理网络连接数据如何聚合到 VMConnection 中的单个逻辑记录中。 

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(LinksEstablished) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```

#### <a name="summarize-volume-of-data-sent-and-received-on-inbound-connections-between-a-set-of-machines"></a>总结一组计算机之间的入站连接上发送与接收的数据量

```
// the machines of interest
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
let StartDateTime = datetime(2019-03-25T00:00:00Z);
let EndDateTime = datetime(2019-03-30T01:00:00Z); 
VMConnection
| where Direction == 'inbound' 
| where TimeGenerated > StartDateTime and TimeGenerated  < EndDateTime
| join kind=inner (ips) on $left.DestinationIp == $right.ips
| summarize sum(BytesSent), sum(BytesReceived) by Computer, Direction, SourceIp, DestinationIp, DestinationPort
```


## <a name="next-steps"></a>后续步骤
[详细了解](migrate-services-overview.md)最新版本的 Azure Migrate。
