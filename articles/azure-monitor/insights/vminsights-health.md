---
title: 了解 Azure 虚拟机的运行状况 | Microsoft Docs
description: 本文介绍如何使用用于 VM 的 Azure Monitor 来了解虚拟机和基础操作系统的运行状况。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: mgoedtel
ms.author: magoedte
ms.date: 11/14/2019
ms.openlocfilehash: 5fd5295e52f0fef5e1432fdb2f81d2ba0e1717e8
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74109763"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>了解 Azure 虚拟机的运行状况

Azure 包含监视空间中特定角色或任务的服务，但不提供 Azure 虚拟机 (VM) 上托管的操作系统 (OS) 的深入运行状况透视图。 尽管我们可以使用 Azure Monitor 来监视不同的状况，但该工具并不能为核心组件的运行状况或 VM 的总体运行状况建模，或呈现这些信息。

但是，使用 VM 的 Azure Monitor 运行状况功能，可以主动监视 Windows 或 Linux 来宾 OS 的可用性和性能。 运行状态功能使用一个代表关键组件及其关系的模型，提供用于指定如何度量组件运行状况的条件，并在检测到不正常状况时发送警报。

可以通过两个透视图查看 Azure VM 和基础 OS 的总体运行状况：直接通过某个 VM 进行观察，或者在资源组中的所有 VM 上通过 Azure Monitor 进行观察。

本文介绍如何快速评估、调查和解决用于 VM 的 Azure Monitor 检测到的运行状况问题。

有关配置适用于 VM 的 Azure Monitor 的信息，请参阅[启用适用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

>[!NOTE]
>我们最近根据我们从公共预览版客户收到的反馈，宣布了对运行状况功能所[做的更改](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)。 考虑到我们将进行的更改数，我们将停止为新客户提供运行状况功能。 现有客户可继续使用运行状况功能。 有关更多详细信息，请参阅我们的[公开上市常见问题解答](vminsights-ga-release-faq.md)。 

## <a name="monitoring-configuration-details"></a>监视配置详细信息

本部分概述监视 Azure Windows 和 Linux VM 所依据的默认运行状况条件。 所有运行状况条件预配置为在检测到不正常状况时发送警报。

| 监视器名称 | 频率（分钟） | 回看持续时间（分钟） | 运算符 | 阈值 | 状态警报 | 严重性 | 工作负荷类别 | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| 联机逻辑磁盘 | 5 | 15 | <> | 1 (true) | 严重 | Sev1 | Linux | 
| 逻辑磁盘可用空间 | 5 | 15 | < | 200 MB（警告）<br> 100 MB（严重） | 警告 | Sev1<br> Sev2 | Linux | 
| 逻辑磁盘可用 Inode 百分比 | 5 | 15 | < | 5% | 严重 | Sev1 | Linux | 
| 逻辑磁盘可用空间百分比 | 5 | 15 | < | 5% | 严重 | Sev1 | Linux | 
| 网络适配器状态 | 5 | 15 | <> | 1 (true) | 警告 | Sev2 | Linux | 
| 操作系统可用内存 (MB) | 5 | 10 | < | 2.5 MB | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Read | 5 | 25 | > | 0.05 秒 | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Transfer | 5 | 25 | > | 0.05 秒 | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Write | 5 | 25 | > | 0.05 秒 | 严重 | Sev1 | Linux | 
| 磁盘状态 | 5 | 25 | <> | 1 (true) | 严重 | Sev1 | Linux | 
| 操作系统总处理器时间百分比 | 5 | 10 | >= | 95% | 严重 | Sev1 | Linux | 
| CPU 利用率百分比总计 | 5 | 10 | >= | 95% | 严重 | Sev1 | Windows | 
| 文件系统错误或损坏 | 60 | 60 | <> | 4 | 严重 | Sev1 | Windows | 
| 每次读取的平均逻辑磁盘秒数 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次传输的平均逻辑磁盘秒数 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次写入的平均逻辑磁盘秒数（逻辑磁盘） | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 当前磁盘队列长度（逻辑磁盘） | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 逻辑磁盘可用空间 (MB) | 15 | 60 | > | 500 MB（警告）<br> 300 MB（严重） | 严重 | Sev1<br> Sev2 | Windows | 
| 逻辑磁盘可用空间 (%) | 15 | 60 | > | 10%（警告）<br> 5%（严重） | 严重 | Sev1<br> Sev2 | Windows |
| 逻辑磁盘空闲时间百分比 | 15 | 360 | <= | 20% | 警告 | Sev2 | Windows | 
| 读取使用的带宽百分比 | 5 | 60 | >= | 60% | 警告 | Sev2 | Windows | 
| 使用的带宽百分比总计 | 5 | 60 | >= | 75% | 警告 | Sev2 | Windows | 
| 写入使用的带宽百分比 | 5 | 60 | >= | 60% | 警告 | Sev2 | Windows | 
| DHCP 客户端服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| DNS 客户端服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| Windows 事件日志服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| Windows 防火墙服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| RPC 服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| 服务器服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| Windows 远程管理服务运行状况 | 5 | 12 | <> | 4（正在运行） | 严重 | Sev1 | Windows | 
| 可用内存 (MB) | 5 | 10 | < | 100 MB | 严重 | Sev1 | Windows | 
| 可用系统页表条目 | 5 | 10 | <= | 5000 | 严重 | Sev1 | Windows | 
| 每秒内存页面数 | 5 | 10 | >= | 5000/秒 | 警告 | Sev1 | Windows | 
| 使用的已提交内存百分比 | 5 | 10 | > | 80% | 严重 | Sev1 | Windows | 
| 每次传输的平均磁盘秒数 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 每次写入的平均磁盘秒数 | 1 | 15 | > | 0.04 秒 | 警告 | Sev2 | Windows | 
| 当前的磁盘队列长度 | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 磁盘空闲时间百分比 | 5 | 60 | >= | 20% | 警告 | Sev2 | Windows | 

>[!NOTE]
>“回看持续时间”表示回看窗口检查指标值的频率，例如过去五分钟内。  

>[!NOTE]
>“频率”表示指标警报检查是否满足条件的频率，例如每隔一分钟。  它是执行运行状况标准的速率，而回溯是评估运行状况标准的持续时间。 例如，运行状况标准正在评估条件 **CPU 利用率**是否在频率为 5 分钟的情况下大于 95%，并且在 15 分钟内保持大于95%（连续 3 个评估周期），则状态将更新为“严重”严重性（如果尚不是）。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

若要登录，请转到 [Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>用于 VM 的 Azure Monitor 运行状况简介

在针对单个虚拟机或 VM 组使用运行状况功能之前，必须了解信息的显示方式，以及要显示哪些可视化效果。

### <a name="view-health-directly-from-a-vm"></a>直接从 VM 查看运行状况

若要查看 Azure VM 的运行状况，请在 VM 的左窗格中选择“见解(预览版)”。 VM 见解页上默认会打开“运行状况”选项卡，其中显示了 VM 的运行状况视图。

![用于 VM 的 Azure Monitor 针对所选 Azure 虚拟机显示的运行状况概述](./media/vminsights-health/vminsights-directvm-health-01.png)

“来宾 VM 运行状况”部分的表显示了按 VM 的运行状况条件监视的性能组件的运行状况汇总，以及由不正常组件引发的 VM 运行状况警报总数。 这些组件包括“CPU”、“内存”、“磁盘”和“网络”。 展开“来宾 VM 运行状况”旁边的 V 形图标，查看其组件的运行状况。

![所选 Azure 虚拟机的用于 VM 的 Azure Monitor 组件的运行状况状态](./media/vminsights-health/vminsights-directvm-health-02.png)

选择组件旁边的状态会在所选组件的上下文中打开运行状况诊断体验。 它会显示该组件的状态组成，介绍使用了哪些运行状况条件来计算其运行状况。 有关详细信息，请参阅[运行状况诊断和使用运行状况条件](#health-diagnostics)。 有关警报的详细信息，请参阅[警报](#alerts)。

下表介绍了为 VM 定义的运行状况状态：

|图标 |运行状况 |含义 |
|-----|-------------|---------------|
| |正常 |VM 符合定义的运行状况条件。 此状态表示未检测到问题，VM 正常运行。 对于父汇总监视器，运行状况将会汇总，并反映子级的最佳和最差状态。|
| |严重 |该状态不在定义的运行状况条件范围内，表示检测到一个或多个严重问题。 必须解决这些问题才能恢复正常功能。 对于父汇总监视器，运行状况将会汇总，并反映子级的最佳和最差状态。|
| |警告 |此状态介于定义的运行状况条件的两个阈值之间，其中一个阈值指示“警告”状态，另一个指示“严重”状态（可以配置三个运行状况状态阈值），或者检测到非严重性问题，若不解决可能会导致严重问题。 对于父汇总监视器，如果一个或多个子级处于警告状态，则父级将反映“警告”状态。 如果一个子级处于“严重”状态，另一个子级处于“警告”状态，则父汇总将显示“严重”运行状况。|
| |未知 |出于多种原因，无法计算状态。 以下部分提供了更多详细信息和可能的解决方法。 |

“未知”运行状况可能是由以下问题造成的：

- 代理已重新配置，不再向启用用于 VM 的 Azure Monitor 时指定的工作区报告。 若要将代理配置为向该工作区报告，请参阅[添加或删除工作区](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- VM 已删除。
- 与用于 VM 的 Azure Monitor 关联的工作区已删除。 如果你享有顶级支持权益，可以恢复该工作区。 转到 [顶级支持](https://premier.microsoft.com/) 并提出支持请求。
- 解决方案依赖项已删除。 若要在 Log Analytics 工作区中重新启用 ServiceMap 和 InfrastructureInsights 解决方案，请使用 [Azure 资源管理器模板](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)重新安装 ServiceMap 解决方案。 若要重新安装 InfastructureInsights 解决方案，请 vminsights@microsoft.com电子邮件。 
- VM 已关闭。
- Azure VM 服务不可用，或正在执行维护。
- 已达到工作区的[每日数据或保留期限制](../platform/manage-cost-storage.md)。

选择“查看运行状况诊断”打开一个页面，其中显示了 VM 的所有组件、关联的运行状况条件、状态更改，以及 VM 相关的监视组件检测到的其他问题。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

“运行状况”部分中的表格显示了运行状况条件监视的性能组件的总体运行状况。 这些组件包括“CPU”、“内存”、“磁盘”和“网络”。 选择一个组件会打开一个页面，其中列出了该组件的所有运行状况条件和运行状况。

从运行 Windows 的 Azure VM 访问“运行状况”时，“核心服务运行状况”下面会显示前五个核心 Windows 服务的运行状况。 选择任一服务会打开一个页面，其中列出了该组件的运行状况条件监视信息及其运行状况。

单击运行状况条件的名称会打开属性窗格。 在此窗格中可以查看配置详细信息，包括运行状况条件是否有相应的 Azure Monitor 警报。

有关详细信息，请参阅[运行状况诊断和使用运行状况条件](#health-diagnostics)。

### <a name="aggregate-vm-perspective"></a>聚合 VM 透视图

若要查看资源组中所有 VM 的运行状况集合，请在门户上的导航列表中选择“Azure Monitor”，然后选择“虚拟机(预览)”。

![Azure Monitor 中的 VM 见解监视视图](./media/vminsights-health/vminsights-aggregate-health.png)

在“订阅”和“资源组”下拉列表中，选择包含组相关 VM 的相应资源组，以查看其报告的运行状况。 你的选择仅适用于运行状况功能，并不会传播到“性能”或“映射”选项卡。

“运行状况”选项卡提供以下信息：

* 有多少个 VM 处于严重或不正常状态，有多少个 VM 处于正常状态或未提交数据（称为未知状态）。
* 有哪些以及多少个 VM（按 OS 列出）正在报告不正常状态。
* 有多少个 VM 由于检测到处理器、磁盘、内存或网络适配器的问题而显示为不正常状态（按运行状况分类）。
* 有多少个 VM 由于检测到核心 OS 服务的问题而显示为不正常状态（按运行状况分类）。

在“运行状况”选项卡上，可以识别监视 VM 的运行状况条件检测到的严重问题，以及查看警报详细信息和相关的知识库文章。 这些文章可以帮助诊断和修正问题。 选择任一严重性可打开按该严重性筛选的“[所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)”页。

“按操作系统列出的 VM 分发版”列表根据 Windows 版本或 Linux 分发版及其版本显示 VM。 在每个 OS 类别中，VM 根据 VM 的运行状况进一步划分。

![VM 见解：虚拟机分发版透视图](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

选择任一列，包括“VM 计数”、“严重”、“警告”、“正常”或“未知”。 在“虚拟机”页中查看与所选列匹配的筛选结果列表。

例如，若要查看所有运行 Red Hat Enterprise Linux 版本 7.5 的 VM，可以选择该 OS 对应的“VM 计数”值，随即会列出与该筛选器匹配的 VM 及其当前运行状况。

![Red Hat Linux VM 的汇总信息示例](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

单击“显示运行状况”复选框，就会在表中返回已筛选结果的运行状况状态。  

![Red Hat Linux VM 的运行状况状态示例](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

对于列表中的任何项，均可单击相应的运行状况状态来启动运行状况诊断，后者会显示如何评估所选 VM 的运行状况。 

在“虚拟机”页中，如果选择“VM 名称”列下的某个 VM 的名称，则会定向到“VM 实例”页。 此页提供影响所选 VM 的警报和运行状况条件问题的更多详细信息。 选择页面左上角的“运行状况”图标筛选运行状况详细信息，以查看哪些组件不正常。 还可以查看不正常组件引发的 VM 运行状况警报（已按警报严重性分类）。

在“VM 列表”视图中，选择某个 VM 的名称会打开该 VM 的“运行状况”页，与直接在 VM 中选择“见解(预览版)”时所看到的页面相同。

![所选 Azure 虚拟机的 VM 见解](./media/vminsights-health/vminsights-directvm-health.png)

“Azure Monitor 中的虚拟机(预览版)”页显示 VM 的运行状况汇总状态和警报。 此运行状况按严重性分类，表示运行状况从正常更改为不正常时根据条件引发的 VM 运行状况警报。 选择“出现严重状况的 VM”会打开一个页面，其中列出了处于严重运行状况的一个或多个 VM。

选择某个 VM 的运行状况会显示该 VM 的“运行状况诊断”视图。 在此视图中可以确定哪个运行状况条件反映了运行状况问题。 当“运行状况诊断”页打开时，它会显示所有 VM 组件，以及这些组件的关联运行状况条件和当前运行状况。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

选择“查看所有运行状况条件”会打开一个页面，其中显示了适用于此功能的所有运行状况条件的列表。 可根据以下选项进一步筛选信息：

* **类型**。 有三种类型的运行状况条件可以评估状况，并汇总受监视 VM 的总体运行状况。
    - **单元**。 度量 VM 的某个方面。 此运行状况条件类型可以检查性能计数器以确定组件的性能、运行脚本来执行综合事务，或者监视指示出错的事件。 默认情况下，筛选器设置为单元。
    - **依赖项**。 提供不同实体之间的运行状况汇总。 此运行状况条件允许一个实体的运行状况依赖于另一种实体的运行状况来成功完成操作。
    - **聚合**。 提供类似运行状况条件的合并运行状况。 单元和依赖项运行状况条件通常在聚合运行状况条件下配置。 除了针对面向某个实体的许多不同运行状况条件提供更好的一般性组织方式以外，聚合运行状况条件还为不同类别的实体提供唯一的运行状况。

* **类别**。 用于将类似的条件分组，以实现报告目的的运行状况条件类型。 这些类别包括“可用性”和“性能”。

若要查看哪些实例不正常，请选择“不正常组件”列下的某个值。 此页中的表格列出了处于严重运行状况的组件。

## <a name="health-diagnostics"></a>运行状况诊断

在“运行状况诊断”页中，可将 VM 的运行状况模型可视化。 此页列出所有 VM 组件、关联的运行状况条件、状态更改，以及与 VM 相关的受监视组件识别到的其他重大问题。

![VM 的“运行状况诊断”页示例](./media/vminsights-health/health-diagnostics-page-01.png)

使用以下方法启动运行状况诊断：

* 通过 Azure Monitor 的聚合 VM 透视图中显示的所有 VM 的汇总运行状况：

    1. 在“运行状况”页上，选择“来宾 VM 运行状况”部分下的“严重”、“警告”、“正常”或“未知”运行状况。
    2. 转到列出了所有与该筛选类别匹配的 VM 的页面。
    3. 选择“运行状况”列中的值打开限定为该 VM 的运行状况诊断。

* 通过 Azure Monitor 的聚合 VM 透视图中的 OS 启动诊断。 在“VM 分发版”下，选择任何一个列值会打开“虚拟机”页，并在表中返回与筛选的类别匹配的列表。 选择“运行状况状态”列下的值会打开所选 VM 的运行状况诊断。
 
* 在 Azure Monitor 的 VM“运行状况”选项卡上的来宾 VM 中，选择“查看运行状况诊断”。

运行状况诊断将运行状况信息组织成两个类别：可用性和性能。
 
无需根据这两个类别进行筛选，即可查看针对所有组件（例如逻辑磁盘、CPU 等）定义的运行状况条件。 这些视图可能会包含在一个统一的条件视图中，或者，可以在选择“可用性”或“性能”时按类别筛选结果，来查看这些视图。

此外，“运行状况条件”列的旁边也会显示条件类别。 如果条件与所选的类别不匹配，“运行状况条件”列中会显示一条消息，指出“所选的类别没有可用的运行状况条件”。

运行状况条件的状态由以下四种类型之一定义： "**严重**"、"**警告**"、"**正常**" 和 "**未知**"。 前三种类型是可配置的，意味着，可以直接在“运行状况条件”配置窗格中修改监视器的阈值。 也可以使用 Azure Monitor REST API [更新监视器操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)进行修改。 “未知”状态不可配置，保留用于特定场景。

“运行状况诊断”页包含三个主要部分：

* 组件模型
* 运行状况条件
* 状态更改

![“运行状况诊断”页包含的部分](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>组件模型

“运行状况诊断”页的最左侧列是“组件模型”。 与 VM 关联的所有组件及其当前运行状况状态显示在此列中。

在以下示例中，已发现的组件是“磁盘”、“逻辑磁盘”、“处理器”、“内存”和“操作系统”。 可在此列中发现并显示这些组件的多个实例。

例如，下图显示 VM 有两个逻辑磁盘实例（**C:** 和 **D:** ），它们均处于正常状态。

![运行状况诊断中显示的示例组件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>运行状况条件

“运行状况诊断”页的中间列是“运行状况条件”。 为 VM 定义的运行状况模型在分层树中显示。 VM 的运行状况模型包括单元和聚合运行状况条件。

![运行状况诊断中显示的示例运行状况条件](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

运行状况条件度量受监视实例的运行状况，这可能是某个阈值、实体状态等。 如前所述，运行状况条件具有两个或三个可配置的运行状况状态阈值。 在任意时间，运行状况条件只能处于一种潜在状态。

运行状况模型定义条件来确定整个目标以及目标组件的运行状况。 “运行状况诊断”页的“运行状况条件”部分显示了条件的层次结构。

运行状况汇总策略是聚合运行状况条件配置的一部分（默认值设置为“最差”）。 可以在本文的[监视配置详细信息](#monitoring-configuration-details)部分找到作为此功能的一部分运行的默认运行状况条件集。

也可以使用 Azure Monitor REST API [按资源列出的监视器实例列表](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)获取所有运行状况条件的列表。 此条件包括针对 Azure VM 资源运行的配置详细信息。

选择最右侧的省略号链接可以修改“单元”运行状况条件类型的配置。 选择“显示详细信息”打开配置窗格。

![配置运行状况条件的示例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

在所选的运行状况条件的配置窗格中，使用示例“每次写入的平均磁盘秒数”，可以使用不同数值来配置其阈值。 它是一个双状态监视器，意味着它只能从“正常”更改为“警告”。

其他运行状况条件有时使用三种状态，在这种情况下，可以配置警告和严重运行状况状态阈值。 也可以使用 Azure Monitor REST API [监视器配置](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)修改阈值。

>[!NOTE]
>将运行状况条件配置更改应用到一个实例，即可将其应用到所有受监视的实例。 例如，如果选择“磁盘 -1 D:”并修改“每次写入的平均磁盘秒数”阈值，则更改会应用到在 VM 上发现和监视的所有实例。


![配置单元监视器的运行状况条件的示例](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

若要详细了解运行状况条件，请参阅知识文章，其中可帮助你识别问题、原因和解决方法。 在页面上选择“查看信息”可以查看相关的知识文章。

若要查看用于 VM 的 Azure Monitor 运行状况相关的所有知识文章，请参阅 [Azure Monitor 运行状况知识文档](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)。

### <a name="state-changes"></a>状态更改

“运行状况诊断”页中最右侧的列是“状态更改”。 此列列出了与在“运行状况条件”部分选择的运行状况条件相关联的所有状态更改，或者从表的“组件模型”或“运行状况条件”列中选择的某个 VM 的状态更改。

![运行状况诊断中显示的状态更改示例](./media/vminsights-health/health-diagnostics-page-statechanges.png)

以下部分显示运行状况条件的状态和关联的时间。 该列顶部的信息显示最新状态。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>“组件模型”、“运行状况条件”和“状态更改”列的关联

这三个列彼此互相链接。 在“组件模型”列中选择某个实例时，“运行状况条件”列会根据该组件视图进行筛选。 “状态更改”列会相应地根据所选运行状况条件进行更新。

![选择受监视实例和结果的示例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

例如，如果在“组件模型”下面的列表中选择“磁盘 - 1 D:”，则“运行状况条件”将筛选为“磁盘 - 1 D:”，“状态更改”会根据“磁盘 - 1 D:”的可用性显示状态更改。

若要查看更新的运行状况状态，可以选择“刷新”链接来刷新“运行状况诊断”页。 如果基于预定义的轮询间隔对运行状况条件的运行状况做了更新，则此任务可以避免等待显示最新运行状况。 **运行状况条件状态**是一个筛选器，可让你根据选定的运行状况状态对结果进行限定： "正常"、"警告"、"严重"、"未知" 和 "全部"。 右上角的“上次更新时间”表示上次刷新“运行状况诊断”页的时间。

## <a name="alerts"></a>警报

用于 VM 的 Azure Monitor 运行状况功能与 [Azure 警报](../../azure-monitor/platform/alerts-overview.md)相集成。 当预定义的条件在检测到从正常状态更改为不正常状态时，它会引发警报。 警报按严重性分类 - 严重性 0 到 4，严重性 0 表示最高严重性级别。

警报不与操作组相关联，在触发警报时会发出通知。 在订阅范围内具有“所有者”角色的用户必须按照[配置警报](#configure-alerts)部分中的步骤配置通知。

“运行状况”仪表板的“警报”部分下显示了按严重性分类的 VM 运行状况警报总数。 选择警报总数或者对应于某个严重性级别的编号时，“警报”页将会打开并列出与所选内容匹配的所有警报。

例如，如果选择了对应于“严重性级别 1”的行，则会看到以下视图：

![所有严重性级别 1 警报的示例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

“所有警报”页的显示范围不局限于与你的选择匹配的警报。 它还会按“资源类型”筛选，以便仅显示 VM 资源引发的运行状况警报。 此格式反映在警报列表中的“目标资源”列下，其中显示了符合不正常条件时引发了警报的 Azure VM。

其他资源类型或服务发出的警报不会包含在此视图中。 这些警报包括日志警报，而这些警报基于通常会在默认的 Azure Monitor [所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page) 页中显示的日志查询或指标警报。

可以通过选择页面顶部的下拉菜单中的值，来对此视图进行筛选。

|列 |说明 |
|-------|------------|
|订阅 |选择 Azure 订阅。 只有选定订阅中的警报才会包含在视图中。 |
|资源组 |选择单个资源组。 只有包含选定资源组中的目标的警报才会包含在视图中。 |
|资源类型 |选择一个或多个资源类型。 默认情况下，仅选择目标虚拟机的警报并将其列在此视图中。 仅在指定资源组后，才显示此列。 |
|资源 |选择资源。 只有包含该资源（作为目标）的警报才会包含在视图中。 仅在指定资源类型后，才显示此列。 |
|严重性 |选择警报严重性，或选择“所有”以包含所有严重性的警报。 |
|监视条件 |选择一个监视条件可以筛选系统激发的警报；如果该条件不再有效，则可以筛选系统已解决的警报。 选择“所有”会包含所有条件的警报。 |
|警报状态 |选择一种警报状态（“新”、“确认”、“已关闭”），或选择“所有”以包括所有状态的警报。 |
|监视服务 |选择一个服务，或选择“所有”以包含所有服务。 此功能仅支持来自 VM 见解的警报。|
|时间范围| 只有在选定时间范围内触发的警报才会包含在该视图中。 支持的值为过去 1 小时、过去 24 小时、过去 7 天和过去 30 天。 |

选择某个警报时，会显示“警报详细信息”页。 此页提供警报详细信息，并可在其中更改警报的状态。

要详细了解如何管理警报，请参阅[使用 Azure Monitor 创建、查看和管理警报](../../azure-monitor/platform/alerts-metric.md)。

>[!NOTE]
>目前，暂不支持根据运行状况条件创建新警报，或通过门户在 Azure Monitor 中修改现有运行状况警报规则。


![所选警报的警报详细信息窗格](./media/vminsights-health/alert-details-pane-01.png)

还可以更改一个或多个警报的警报状态，方法是选择这些警报，然后在“所有警报”页的左上角选择“更改状态”。 在“更改警报状态”窗格中选择一种状态，在“注释”字段中添加更改操作的说明，然后选择“确定”提交更改。 在验证信息和应用更改时，可在菜单中的“通知”下面跟踪操作进度。

### <a name="configure-alerts"></a>配置警报
无法通过 Azure 门户管理某些警报管理任务。 必须使用 [Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components) 执行这些任务。 具体而言：

- 启用或禁用运行状况条件的警报
- 针对运行状况条件警报设置通知

每个示例使用 Windows 计算机上的 [ARMClient](https://github.com/projectkudu/armclient)。 如果你不熟悉此方法，请参阅[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。

#### <a name="enable-or-disable-an-alert-rule"></a>启用或禁用警报规则

若要启用或禁用特定运行状况条件的警报，必须使用 **Disabled** 或 **Enabled** 值修改 **alertGeneration** 属性。

若要识别特定运行状况条件的 *monitorId*，请查看以下示例，其中演示了如何查询条件 **LogicalDisk\Avg Disk Seconds Per Transfer** 的该值：

1. 在终端窗口中，键入 **armclient.exe login**。 此时，系统会提示你登录到 Azure。

2. 输入以下命令检索特定 VM 上处于活动状态的所有运行状况条件，并确定 *monitorId* 属性的值：

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    以下示例显示了 *armclient GET* 命令的输出。 记下 *MonitorId* 的值。 执行下一步骤时需要使用此值，其中，我们必须指定运行状况条件的 ID 并修改其属性，以创建警报。

    ```
    "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/monitors/ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "name": "ComponentTypeId='LogicalDisk',MonitorId='Microsoft_LogicalDisk_AvgDiskSecPerRead'",
      "type": "Microsoft.WorkloadMonitor/virtualMachines/monitors"
    },
    {
      "properties": {
        "description": "Monitor the performance counter LogicalDisk\\Avg Disk Sec Per Transfer",
        "monitorId": "Microsoft_LogicalDisk_AvgDiskSecPerTransfer",
        "monitorName": "Microsoft.LogicalDisk.AvgDiskSecPerTransfer",
        "monitorDisplayName": "Average Logical Disk Seconds Per Transfer",
        "parentMonitorName": null,
        "parentMonitorDisplayName": null,
        "monitorType": "Unit",
        "monitorCategory": "PerformanceHealth",
        "componentTypeId": "LogicalDisk",
        "componentTypeName": "LogicalDisk",
        "componentTypeDisplayName": "Logical Disk",
        "monitorState": "Enabled",
        "criteria": [
          {
            "healthState": "Warning",
            "comparisonOperator": "GreaterThan",
            "threshold": 0.1
          }
        ],
        "alertGeneration": "Enabled",
        "frequency": 1,
        "lookbackDuration": 17,
        "documentationURL": "https://aka.ms/Ahcs1r",
        "configurable": true,
        "signalType": "Metrics",
        "signalName": "VMHealth_Avg. Logical Disk sec/Transfer"
      },
      "etag": null,
    ```

3. 输入以下命令修改 *alertGeneration* 属性：

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 输入步骤 2 中使用的 GET 命令，以验证属性值是否设置为 **Disabled**。

#### <a name="associate-an-action-group-with-health-criteria"></a>将操作组与运行状况条件相关联

用于 VM 的 Azure Monitor 运行状况功能支持当不正常运行状况条件生成警报时发送短信和电子邮件通知。 若要配置通知，请记下配置用于发送短信或电子邮件通知的操作组的名称。

>[!NOTE]
>必须针对要接收其通知的每个受监视 VM 执行此操作。 不需要对资源组中的所有 VM 执行此操作。

1. 在终端窗口中，输入 *armclient.exe login*。 此时，系统会提示你登录到 Azure。

2. 输入以下命令将操作组关联到警报规则：
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要验证是否已成功更新 **actionGroupResourceIds** 属性的值，请输入以下命令：

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    输出应如以下条件所示：
    
    ```
    {
      "value": [
        {
          "properties": {
            "actionGroupResourceIds": [
              "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourceGroups/Lab/providers/microsoft.insights/actionGroups/Lab-IT%20Ops%20Notify"
            ]
          },
          "etag": null,
          "id": "/subscriptions/a7f23fdb-e626-4f95-89aa-3a360a90861e/resourcegroups/Lab/providers/Microsoft.Compute/virtualMachines/SVR01/providers/Microsoft.WorkloadMonitor/notificationSettings/default",
          "name": "notificationSettings/default",
          "type": "Microsoft.WorkloadMonitor/virtualMachines/notificationSettings"
        }
      ],
      "nextLink": null
    }
    ```

## <a name="next-steps"></a>后续步骤

- 若要确定限制和总体 VM 性能，请参阅[查看 AZURE Vm 性能](vminsights-performance.md)。

- 若要了解已发现的应用程序依赖项，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
