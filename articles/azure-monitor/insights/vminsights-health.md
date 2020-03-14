---
title: 了解 Azure 虚拟机的运行状况 |Microsoft Docs
description: 本文介绍如何通过用于 VM 的 Azure Monitor 了解虚拟机和基础操作系统的运行状况。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 11/14/2019
ms.openlocfilehash: 3cecb04a4f299051860c45425f0fc4e13c3722ea
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/13/2020
ms.locfileid: "79275121"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>了解 Azure 虚拟机的运行状况

Azure 包括监视空间中特定角色或任务的服务，但它不提供在 Azure 虚拟机（Vm）上托管的操作系统（OSs）的深入运行状况。 虽然你可以将 Azure Monitor 用于不同的条件，但它并不用于为核心组件的运行状况建模，也不能表示 Vm 的总体运行状况。

通过使用用于 VM 的 Azure Monitor 运行状况，你可以主动监视 Windows 或 Linux 来宾操作系统的可用性和性能。 运行状况功能使用表示关键组件及其关系的模型，提供指定如何测量组件运行状况的条件，并在检测到不正常情况时发送警报。

查看 Azure VM 和基础操作系统的总体运行状况状态时，可以从以下两个方面进行观察：直接从 VM 或资源组中的所有 Vm Azure Monitor。

本文介绍了如何在用于 VM 的 Azure Monitor 运行状况功能检测到运行状况问题时对其进行快速评估、调查和解决。

有关配置用于 VM 的 Azure Monitor 的信息，请参阅[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

>[!NOTE]
>我们最近根据我们从公共预览版客户收到的反馈，宣布了对运行状况功能所[做的更改](https://azure.microsoft.com/updates/updates-to-azure-monitor-for-virtual-machines-preview-before-general-availability-release/
)。 考虑到我们将进行的更改数，我们将停止为新客户提供运行状况功能。 现有客户可继续使用运行状况功能。 有关更多详细信息，请参阅我们的[公开上市常见问题解答](vminsights-ga-release-faq.md)。 

## <a name="monitoring-configuration-details"></a>监视配置详细信息

本部分概述了用于监视 Azure Windows 和 Linux Vm 的默认运行状况条件。 所有运行状况条件都预配置为在检测到不正常的情况时发送警报。

| 监视器名称 | 频率（分钟） | Lookback 持续时间（分钟） | 操作员 | 阈值 | 警报状态 | 严重性 | 工作负荷类别 | 
|--------------|-----------|----------|----------|-----------|----------------|----------|-------------------|
| 逻辑磁盘联机 | 5 | 15 | <> | 1（true） | 严重 | Sev1 | Linux | 
| 逻辑磁盘可用空间 | 5 | 15 | < | 200 MB （警告）<br> 100 MB （严重） | 警告 | Sev1<br> Sev2 | Linux | 
| 逻辑磁盘可用 Inode 百分比 | 5 | 15 | < | 5% | 严重 | Sev1 | Linux | 
| 逻辑磁盘可用空间百分比 | 5 | 15 | < | 5% | 严重 | Sev1 | Linux | 
| 网络适配器状态 | 5 | 15 | <> | 1（true） | 警告 | Sev2 | Linux | 
| Operating System Available Megabytes Memory | 5 | 10 | < | 2.5 MB | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Read | 5 | 25 | > | 0.05s | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Transfer | 5 | 25 | > | 0.05s | 严重 | Sev1 | Linux | 
| 磁盘 Avg. Disk sec/Write | 5 | 25 | > | 0.05s | 严重 | Sev1 | Linux | 
| 磁盘状态 | 5 | 25 | <> | 1（true） | 严重 | Sev1 | Linux | 
| 操作系统处理器时间百分比总计 | 5 | 10 | >= | 95% | 严重 | Sev1 | Linux | 
| CPU 利用率百分比总计 | 5 | 10 | >= | 95% | 严重 | Sev1 | Windows | 
| 文件系统错误或损坏 | 60 | 60 | <> | 4 | 严重 | Sev1 | Windows | 
| 每次读取的平均逻辑磁盘秒数 | 1 | 15 | > | 0.04s | 警告 | Sev2 | Windows | 
| 每次传输的平均逻辑磁盘秒数 | 1 | 15 | > | 0.04s | 警告 | Sev2 | Windows | 
| 每次写入逻辑磁盘的平均时间（逻辑磁盘） | 1 | 15 | > | 0.04s | 警告 | Sev2 | Windows | 
| 当前磁盘队列长度（逻辑磁盘） | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 逻辑磁盘可用空间（MB） | 15 | 60 | > | 500 MB 警告<br> 300 MB 关键 | 严重 | Sev1<br> Sev2 | Windows | 
| 逻辑磁盘可用空间（%） | 15 | 60 | > | 10% 警告<br> 5% 严重 | 严重 | Sev1<br> Sev2 | Windows |
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
| 可用系统页表项 | 5 | 10 | <= | 5000 | 严重 | Sev1 | Windows | 
| 每秒内存页面数 | 5 | 10 | >= | 5000/秒 | 警告 | Sev1 | Windows | 
| 使用的已提交内存百分比 | 5 | 10 | > | 80% | 严重 | Sev1 | Windows | 
| 每次传输的平均磁盘秒数 | 1 | 15 | > | 0.04s | 警告 | Sev2 | Windows | 
| 每次写入磁盘的平均时间 | 1 | 15 | > | 0.04s | 警告 | Sev2 | Windows | 
| Current Disk Queue Length | 5 | 60 | >= | 32 | 警告 | Sev2 | Windows | 
| 磁盘空闲时间百分比 | 5 | 60 | >= | 20% | 警告 | Sev2 | Windows | 

>[!NOTE]
>Lookback Duration 表示 "查看" 窗口检查指标值的频率，如过去五分钟。  

>[!NOTE]
>Frequency 表示指标警报检查是否满足条件的频率，例如每分钟一次。  这是运行状况标准的执行速率，lookback 是计算运行状况条件的持续时间。 例如，如果条件的**CPU 利用率**大于95% 且频率为5分钟，并且在15分钟内保持大于95% （3个连续的评估周期），则运行状况条件将会进行评估，并将状态更新为严重严重性。

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

若要登录，请前往[Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>用于 VM 的 Azure Monitor 运行状况简介

在将运行状况功能用于单个 VM 或 Vm 组之前，请务必了解信息的显示方式和视觉对象表示的内容。

### <a name="view-health-directly-from-a-vm"></a>直接从 VM 查看运行状况

若要查看 Azure VM 的运行状况，请在 VM 的左窗格中选择 " **Insights （预览版）** "。 在 VM insights 页面上，默认情况下，"**运行状况**" 选项卡处于打开状态，并显示 VM 的运行状况视图。

![用于 VM 的 Azure Monitor 针对所选 Azure 虚拟机显示的运行状况概述](./media/vminsights-health/vminsights-directvm-health-01.png)

在 "**来宾 VM 运行状况**" 部分中，表显示了 vm 的运行状况标准监视的性能组件的运行状况汇总，以及由不正常组件引发的 vm 运行状况警报总数。 这些组件包括**CPU**、**内存**、**磁盘**和**网络**。 展开 "来宾 VM 运行状况" 旁的 v 形，查看其组件的运行状况。

![选择的 Azure 虚拟机用于 VM 的 Azure Monitor 组件运行状态](./media/vminsights-health/vminsights-directvm-health-02.png)

选择组件旁边的状态将在所选组件的上下文中打开运行状况诊断体验。 它显示该组件的状态组合，描述用于计算其运行状况的运行状况条件。 有关详细信息，请参阅[运行状况诊断和使用运行状况条件](#health-diagnostics)。 有关警报的详细信息，请参阅[警报](#alerts)。

下表介绍了为 VM 定义的运行状况状态：

|图标 |运行状况 |含义 |
|-----|-------------|---------------|
| |正常 |VM 在定义的运行状况条件内。 此状态指示未检测到任何问题，并且 VM 工作正常。 使用父汇总监视器时，运行状况将汇总，并反映子级的最佳或最坏情况状态。|
| |严重 |状态不在定义的运行状况条件内，表示检测到一个或多个关键问题。 要恢复正常功能，必须解决这些问题。 使用父汇总监视器时，运行状况状态将汇总，并反映子级的最佳或最坏情况状态。|
| |警告 |定义的运行状况条件的两个阈值之间的状态为，其中一个指示警告状态，另一个指示严重状态（可以配置三个运行状况状态阈值），或者当非关键问题在以下情况下可能导致严重问题时：未解析. 使用父汇总监视器时，如果一个或多个子级处于警告状态，则父对象将反映警告状态。 如果一个子级处于严重状态，而另一个子级处于警告状态，则父汇总会将运行状况状态显示为 "严重"。|
| |未知 |由于多种原因，无法计算状态。 以下部分提供了其他详细信息和可能的解决方案。 |

未知的运行状况状态可能是由以下问题引起的：

- 代理已重新配置，不再向启用用于 VM 的 Azure Monitor 时所指定的工作区报告。 若要将代理配置为向工作区报告，请参阅[添加或删除工作区](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- 已删除 VM。
- 与用于 VM 的 Azure Monitor 关联的工作区已删除。 如果你具有顶级支持权益，你可以恢复工作区。 请参阅 "[顶级](https://premier.microsoft.com/)" 并打开支持请求。
- 解决方案依赖项已删除。 若要在 Log Analytics 工作区中重新启用 ServiceMap 和 InfrastructureInsights 解决方案，请使用[Azure 资源管理器模板](vminsights-enable-at-scale-powershell.md#install-the-servicemap-solution)重新安装 ServiceMap 解决方案。 若要重新安装 InfastructureInsights 解决方案，请 vminsights@microsoft.com电子邮件。 
- VM 已关闭。
- Azure VM 服务不可用或正在进行维护。
- 满足了工作区[每日数据或保持限制](../platform/manage-cost-storage.md)。

选择 "**查看运行状况诊断**" 以打开一个页面，其中显示了 vm 的所有组件、关联的运行状况条件、状态更改以及通过监视与 VM 相关的组件检测到的其他问题。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

在 "**运行状况**" 部分中，表显示按运行状况标准监视的性能组件的运行状况汇总。 这些组件包括**CPU**、**内存**、**磁盘**和**网络**。 选择某个组件将打开一个页面，其中列出了该组件的所有监视标准和运行状况状态。

从运行 Windows 的 Azure VM 访问运行状况时，前五个核心 Windows 服务的运行状况状态显示在 "**核心服务运行状况**" 下。 选择任何服务将打开一个页面，其中列出了该组件的运行状况标准监视及其运行状况状态。

选择运行状况条件的名称将打开属性窗格。 在此窗格中，可以查看配置详细信息，包括运行状况条件是否有对应的 Azure Monitor 警报。

有关详细信息，请参阅[运行状况诊断和使用运行状况条件](#health-diagnostics)。

### <a name="aggregate-vm-perspective"></a>聚合 VM 透视

若要查看资源组中所有 Vm 的运行状况收集，请从门户的导航列表中选择 " **Azure Monitor** "，然后选择 "**虚拟机（预览版）** "。

![Azure Monitor 中的 VM 见解监视视图](./media/vminsights-health/vminsights-aggregate-health.png)

在 "**订阅**和**资源组**" 下拉列表中，选择包含与组相关的 vm 的相应资源组，以查看其报告的运行状况状态。 你的选择仅适用于运行状况功能，且不会执行**性能**或**地图**选项卡。

"**运行状况**" 选项卡提供以下信息：

* 处于严重或不正常状态的 Vm 数，与运行状况或未提交数据的 Vm 数（称为未知状态）。
* 操作系统以及多少 Vm 正在报告不正常状态。
* 由于检测到由运行状况状态分类的处理器、磁盘、内存或网络适配器的问题，导致了多少 Vm 不正常。
* 由于核心操作系统服务检测到问题而导致的虚拟机不正常，由运行状况状态分类。

在 "**运行状况**" 选项卡上，可以确定监视 VM 的运行状况条件检测到的关键问题，并查看警报详细信息和关联的知识库文章。 这些文章可帮助诊断和解决问题。 选择任一严重性可打开按该严重性筛选的“[所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)”页。

“按操作系统列出的 VM 分发版”列表根据 Windows 版本或 Linux 分发版及其版本显示 VM。 在每个 OS 类别中，Vm 会根据 VM 的运行状况进一步分解。

![VM 见解：虚拟机分发版透视图](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

选择包括 " **VM 计数**"、 **"严重**"、"**警告**"、"**正常**" 或 "**未知**" 的列。 在 "**虚拟机**" 页中查看与所选列相匹配的筛选结果的列表。

例如，若要查看运行 Red Hat Enterprise Linux 版本7.5 的所有 Vm，请选择该操作系统的 " **VM 计数**" 值，并列出与该筛选器匹配的 vm 及其当前运行状况状态。

![Red Hat Linux VM 的汇总信息示例](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

单击 "**显示运行状况**" 复选框，将为表中筛选结果返回运行状况状态。  

![Red Hat Linux Vm 的示例运行状况状态](./media/vminsights-health/vminsights-rollup-vm-rehl-02.png)

对于列表中的任何一项，可以单击相应的运行状况诊断，以启动运行状况诊断，其中显示了如何评估所选 VM 的运行状况。 

在 "**虚拟机**" 页上，如果在 " **vm 名称**" 列下选择了 vm 的名称，则会定向到 " **vm 实例**" 页。 此页详细介绍了影响所选 VM 的警报和健康状况问题。 通过选择页面左上角的 "**运行状况状态**" 图标来筛选运行状况状态详细信息，以查看哪些组件不正常。 你还可以查看按警报严重性分类的不正常组件引发的 VM 运行状况警报。

在 " **vm 列表**" 视图中，选择要打开该 Vm 的**运行状况**页面的 vm 的名称，就像直接从 VM 选择了 " **Insights （预览版）** " 一样。

![所选 Azure 虚拟机的 VM 见解](./media/vminsights-health/vminsights-directvm-health.png)

Azure Monitor "页中的"**虚拟机（预览）** "显示 VM 和警报的汇总运行状况状态。 此运行状况状态按严重性进行分类，这表示在运行状况状态从正常更改为不正常时引发的 VM 运行状况警报基于条件。 **在 "严重情况" 中选择 "vm"** 会打开一个页面，其中包含一个或多个处于严重运行状况状态的 vm。

选择其中一个 Vm 的运行状况状态将显示 VM 的**运行状况诊断**视图。 在此视图中，你可以确定哪些运行状况条件反映了运行状况状态问题。 当 "**运行状况诊断**" 页打开时，将显示所有 VM 组件及其关联的运行状况条件和当前运行状况状态。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

选择“查看所有运行状况条件”会打开一个页面，其中显示了适用于此功能的所有运行状况条件的列表。 可根据以下选项进一步筛选信息：

* **Type**。 有三种类型的运行状况条件可用于评估条件并汇总监视的 VM 的总体运行状况状态：
    - **单元**。 度量 VM 的某个方面。 此运行状况条件类型可能会检查性能计数器以确定组件的性能，运行脚本以执行综合事务，或者监视表明错误的事件。 默认情况下，筛选器设置为 "单位"。
    - **依赖项**。 提供不同实体之间的运行状况汇总。 此运行状况标准允许实体的运行状况依赖于它所依赖的其他类型的实体的运行状况，以实现成功操作。
    - **聚合**。 提供类似运行状况条件的组合运行状况状态。 单元和依赖项的运行状况标准通常在聚合运行状况条件下进行配置。 除了针对面向某个实体的许多不同运行状况条件提供更好的一般性组织方式以外，聚合运行状况条件还为不同类别的实体提供唯一的运行状况。

* **类别**。 用于出于报告目的对相似条件进行分组的运行状况条件的类型。 这些类别是**可用性**和**性能**。

若要查看不正常的实例，请在 "不**正常的组件**" 列下选择一个值。 在此页中，表列出了处于严重运行状况状态的组件。

## <a name="health-diagnostics"></a>运行状况诊断

"**运行状况诊断**" 页可让你直观显示 VM 的运行状况模型。 此页列出了与 VM 相关的受监视组件识别的所有 VM 组件、相关的运行状况条件、状态更改以及其他重大问题。

![VM 的“运行状况诊断”页示例](./media/vminsights-health/health-diagnostics-page-01.png)

使用以下方法启动运行状况诊断：

* 在 Azure Monitor 中，按聚合 VM 透视汇总所有 Vm 的运行状况状态：

    1. 在 "**运行状况**" 页上，在 "**来宾 VM 运行状况**" 部分中选择 "**严重**"、"**警告**"、"**正常**" 或 "**未知**" 运行状况。
    2. 中转到列出与筛选类别匹配的所有 Vm 的页面。
    3. 选择 "**运行状况状态**" 列中的值，打开范围为该虚拟机的运行状况诊断。

* 在 Azure Monitor 中，按 OS 聚合 VM 的方式。 在“VM 分发版”下，选择任何一个列值会打开“虚拟机”页，并在表中返回与筛选的类别匹配的列表。 选择 "**运行状况状态**" 列下的值会打开所选 VM 的运行状况诊断。
 
* 在 "用于 VM 的 Azure Monitor**运行状况**" 选项卡上的 "来宾 VM" 中，选择 "**查看运行状况诊断**"。

运行状况诊断将运行状况信息分为两类：可用性和性能。
 
可以查看为组件定义的所有运行状况标准（如逻辑磁盘、CPU 等），而无需对这两种类别进行筛选。 这些视图可以是所有条件视图，也可以是在选择 "**可用性**" 或 "**性能**" 时通过两个类别筛选结果。

此外，还可以在 "**运行状况条件**" 列旁查看条件类别。 如果条件不匹配所选的类别 **，则会**出现一条消息，指出**没有可用于所选类别的运行状况条件**。

运行状况条件的状态由以下四种类型之一定义： "**严重**"、"**警告**"、"**正常**" 和 "**未知**"。 前三个是可配置的，这意味着你可以直接在 "**运行状况条件**" 配置窗格中修改监视器的阈值。 还可以通过使用 Azure Monitor REST API[更新监视器操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)来实现此操作。 "**未知**" 不可配置，并保留用于特定方案。

**运行状况诊断**页包含三个主要部分：

* 组件模型
* 运行状况条件
* 状态更改

![“运行状况诊断”页包含的部分](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>组件模型

**运行状况诊断**页中最左侧的列是**组件模型**。 与 VM 关联的所有组件都会显示在此列及其当前运行状况状态。

在以下示例中，发现的组件是**磁盘**、**逻辑磁盘**、**处理器**、**内存**和**操作系统**。 可在此列中发现并显示这些组件的多个实例。

例如，下图显示 VM 包含两个逻辑磁盘实例： **C：** 和**D：** ，它们处于正常状态：

![运行状况诊断中显示的示例组件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>运行状况条件

"运行状况诊断" 页中的 "中心" 列是**运行状况条件**。 为 VM 定义的运行状况模型在分层树中显示。 VM 的运行状况模型包括单元和聚合运行状况条件。

![运行状况诊断中显示的示例运行状况条件](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

运行状况条件测量被监视实例的运行状况，这可能是阈值、实体的状态等。 运行状况条件有两个或三个可配置的运行状况状态阈值，如前文所述。 在任何时候，运行状况标准只能处于其一种潜在状态。

运行状况模型定义确定目标的整体目标和组件的运行状况的条件。 标准层次结构显示在 "**运行状况诊断**" 页的 "**运行状况条件**" 部分中。

运行状况汇总策略是聚合运行状况条件（默认设置为 "**最差-** "）的配置的一部分。 在本文的 "[监视配置详细信息](#monitoring-configuration-details)" 部分中，可以找到作为此功能的一部分运行的默认运行状况标准集。

你还可以使用 Azure Monitor REST API[按资源列出的监视器实例列表](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)来获取所有运行状况条件的列表。 此条件包括针对 Azure VM 资源运行的配置详细信息。

通过选择右侧的省略号链接，可以修改**单元**状况条件类型的配置。 选择 "**显示详细信息**" 以打开 "配置" 窗格。

![配置运行状况条件的示例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

在 "配置" 窗格中选择的运行状况条件，如果使用**每次写入磁盘的平均时间**，则可以使用不同的数值来配置阈值。 这是一个双状态监视器，这意味着它只能从**正常**状态更改为**警告**。

其他运行状况标准有时会使用三种状态，您可以在其中配置警告和严重运行状况阈值的值。 还可以通过使用 Azure Monitor REST API[监视器配置](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)来修改阈值。

>[!NOTE]
>将运行状况条件配置更改应用于一个实例会将其应用到所有监视的实例。 例如，如果选择 "**磁盘 1 D：** "，然后修改 "**每次写入磁盘的平均时间**" 阈值，则更改将应用到 VM 上发现和监视的所有实例。


![配置单元监视器的运行状况条件的示例](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

如果要了解有关运行状况条件的详细信息，我们提供了知识库文章来帮助你确定问题、原因和解决方法。 选择页面上的 "**查看信息**" 以查看相关的知识库文章。

若要查看用于 VM 的 Azure Monitor 运行状况附带的所有知识库文章，请参阅[Azure Monitor 运行状况知识文档](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)。

### <a name="state-changes"></a>状态更改

**运行状况诊断**页的最右侧的列是**状态更改**。 此列列出与**运行状况条件**部分中选定的运行状况条件相关联的所有状态更改，或者如果已从表的 "**组件模型**" 或 "**运行状况条件**" 列中选择了 vm，则为 vm 的状态更改。

![运行状况诊断中显示的状态更改示例](./media/vminsights-health/health-diagnostics-page-statechanges.png)

以下部分显示了运行状况标准状态和关联的时间。 此信息显示列顶部的最新状态。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>组件模型、运行状况条件和状态更改列的关联

这三个列彼此互相链接。 选择 "**组件模型**" 列中的实例时，"**运行状况条件**" 列将筛选为该组件视图。 相应地，**状态更改**列根据所选的运行状况条件进行更新。

![选择受监视实例和结果的示例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

例如，如果从 "**组件模型**" 下的列表中选择 "*磁盘-1 D：* "，则 "**运行状况条件**筛选为*磁盘-1d：* " 和 "**状态更改**" 显示基于*磁盘 1 D：* 的可用性的状态更改。

若要查看更新的运行状况状态，可以通过选择 "**刷新**" 链接来刷新 "运行状况诊断" 页。 如果基于预定义的轮询间隔对运行状况条件的运行状况做了更新，则此任务可以避免等待显示最新运行状况。 **运行状况条件状态**是一个筛选器，可让你根据选定的运行状况状态对结果进行限定： "正常"、"警告"、"严重"、"未知" 和 "全部"。 右上角的 "**上次更新**时间" 表示刷新运行状况诊断页的最后时间。

## <a name="alerts"></a>警报

用于 VM 的 Azure Monitor 运行状况与[Azure 警报](../../azure-monitor/platform/alerts-overview.md)集成。 检测到预定义的条件时，它会发出警报，从正常状态更改为不正常状态。 警报按严重性分类，从严重性0到严重性4，严重性0作为最高级别。

警报不与操作组相关联，以便在触发警报时通知您。 订阅范围内具有所有者角色的用户必须按照[配置警报](#configure-alerts)部分中的步骤配置通知。

"**警报**" 部分下面的 "**运行状况**" 仪表板上提供了按严重性分类的 VM 运行状况警报总数。 选择警报总数或者对应于某个严重性级别的编号时，“警报”页将会打开并列出与所选内容匹配的所有警报。

例如，如果选择对应于**严重性 level 1**的行，则会看到以下视图：

![所有严重性级别 1 警报的示例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

"**所有警报**" 页的作用域不仅显示与你的选择匹配的警报。 它也按**资源类型**进行筛选，仅显示 VM 资源引发的运行状况警报。 此格式反映在警报列表中的 "**目标资源**" 列下，其中显示 Azure VM 在满足不正常情况时发出的警报。

来自其他资源类型或服务的警报不应包含在此视图中。 这些警报包括日志警报，它们基于从默认 Azure Monitor[所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)"页中通常会查看的日志查询或指标警报。

您可以通过在页面顶部的下拉菜单中选择值来筛选此视图。

|列 |说明 |
|-------|------------|
|订阅 |选择 Azure 订阅。 只有选定订阅中的警报才会包含在视图中。 |
|资源组 |选择单个资源组。 只有包含选定资源组中的目标的警报才会包含在视图中。 |
|资源类型 |选择一个或多个资源类型。 默认情况下，仅选择目标虚拟机的警报并将其列在此视图中。 仅在指定资源组后，才显示此列。 |
|资源 |选择资源。 只有包含该资源（作为目标）的警报才会包含在视图中。 只有在指定了资源类型后，此列才可用。 |
|严重性 |选择警报严重性，或选择“所有”以包含所有严重性的警报。 |
|监视条件 |如果条件不再处于活动状态，请选择监视条件以筛选警报（如果它们已由系统触发或解决）。 或者选择 "**全部**" 以包括所有条件的警报。 |
|警报状态 |选择 "警报状态"、"**新建**"、"**确认**"、"**已关闭**" 或 "**全部**" 以包含所有状态的警报。 |
|监视服务 |选择一个服务，或选择“所有”以包含所有服务。 此功能仅支持来自 VM Insights 的警报。|
|时间范围| 只有在选定时间范围内触发的警报才会包含在该视图中。 支持的值为过去 1 小时、过去 24 小时、过去 7 天和过去 30 天。 |

选择警报时，将显示 "**警报详细信息**" 页。 此页提供有关警报的详细信息，并允许您更改其状态。

要详细了解如何管理警报，请参阅[使用 Azure Monitor 创建、查看和管理警报](../../azure-monitor/platform/alerts-metric.md)。

>[!NOTE]
>当前不支持基于运行状况条件创建新警报，或从门户 Azure Monitor 修改现有的运行状况警报规则。


![所选警报的警报详细信息窗格](./media/vminsights-health/alert-details-pane-01.png)

您可以通过选择一个或多个警报来更改警报状态，然后从左上角的 "**所有警报**" 页中选择 "**更改状态**"。 在 "**更改警报状态**" 窗格中选择一个状态，在 "**注释**" 字段中添加更改说明，然后选择 **"确定"** 提交更改。 验证信息并应用所做的更改后，请在菜单中的 "**通知**" 下跟踪进度。

### <a name="configure-alerts"></a>配置警报
无法从 Azure 门户管理某些警报管理任务。 必须通过使用[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)来执行这些任务。 具体来说：

- 启用或禁用运行状况条件的警报
- 设置运行状况条件警报的通知

每个示例在 Windows 计算机上使用[ARMClient](https://github.com/projectkudu/armclient) 。 如果你不熟悉此方法，请参阅[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。

#### <a name="enable-or-disable-an-alert-rule"></a>启用或禁用警报规则

若要为特定的运行状况条件启用或禁用警报，则必须使用 "**已禁用**" 或 "**已启用**" 值修改 " **alertGeneration** " 属性。

为了识别特定运行状况条件的*monitorId* ，下面的示例演示了如何针对**每个传输的标准 logicaldisk\avg disk 磁盘时间**查询该值：

1. 在终端窗口中，键入 **armclient.exe login**。 这样做会提示你登录到 Azure。

2. 输入以下命令以检索特定 VM 上所有活动的运行状况标准并标识*monitorId*属性的值：

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview"
    ```

    下面的示例显示了*ARMCLIENT GET*命令的输出。 记下*MonitorId*的值。 下一步需要此值，必须指定运行状况条件的 ID，并修改其属性以创建警报。

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

3. 输入以下命令来修改*alertGeneration*属性：

    ```
    armclient patch subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors/Microsoft_LogicalDisk_AvgDiskSecPerTransfer?api-version=2018-08-31-preview "{'properties':{'alertGeneration':'Disabled'}}"
    ```   

4. 输入步骤2中使用的 GET 命令，以验证是否已将属性值设置为 "**已禁用**"。

#### <a name="associate-an-action-group-with-health-criteria"></a>将操作组与运行状况条件关联

当警报是从不正常的运行状况条件生成时，用于 VM 的 Azure Monitor 运行状况支持 SMS 和电子邮件通知。 若要配置通知，请记下配置的操作组的名称，以发送短信或电子邮件通知。

>[!NOTE]
>对于要接收通知的每个受监视的 VM，必须执行此操作。 它不适用于资源组中的所有 Vm。

1. 在终端窗口中，输入 " *armclient" 登录名*。 这样做会提示你登录到 Azure。

2. 输入以下命令，将操作组与警报规则相关联：
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要验证是否已成功更新**actionGroupResourceIds**属性的值，请输入以下命令：

    ```
    armclient GET "subscriptions/subscriptionName/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings?api-version=2018-08-31-preview"
    ```

    输出应类似于以下条件：
    
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

- 若要了解已发现的应用程序依赖关系，请参阅[查看用于 VM 的 Azure Monitor 映射](vminsights-maps.md)。
