---
title: 了解 Azure 虚拟机的运行状况 |Microsoft Docs
description: 本文介绍如何了解的 Vm 的虚拟机和 Azure monitor 的基础操作系统的运行状况。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/22/2019
ms.author: magoedte
ms.openlocfilehash: 2bf891f8cfecbb9e78e511dcee7ed1c61c170016
ms.sourcegitcommit: a12b2c2599134e32a910921861d4805e21320159
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/24/2019
ms.locfileid: "67340141"
---
# <a name="understand-the-health-of-your-azure-virtual-machines"></a>了解 Azure 虚拟机的运行状况

Azure 在监视空间，包括针对特定角色或任务的服务，但它不提供深入的运行状况的操作系统 (Os) 托管在 Azure 虚拟机 (Vm) 上的透视。 尽管对不同的条件，可使用 Azure Monitor，但它不旨在模型和表示核心组件的运行状况或 Vm 的总体运行状况。

通过使用 Azure Monitor 的 Vm 运行状况，您可以主动监视的可用性和性能的 Windows 或 Linux 来宾 OS。 运行状况功能使用的模型表示关键组件和它们之间的关系，提供条件，指定如何测量组件运行状况，并检测到不正常状况时发送警报。

查看 Azure VM 与基础操作系统的总体运行状况状态可以观察到从两个角度： 直接从 VM，或从 Azure Monitor 的资源组中的所有 vm。

本文介绍如何快速评估、 调查和解决 Vm 运行状况功能 Azure 监视器在检测到的运行状况问题。

有关配置用于 VM 的 Azure Monitor 的信息，请参阅[启用用于 VM 的 Azure Monitor](vminsights-enable-overview.md)。

## <a name="monitoring-configuration-details"></a>监视配置详细信息

本部分概述了用来监视 Azure Windows 和 Linux Vm 的默认运行状况条件。 所有运行状况条件已预配置为检测到不正常状况时发送警报。

### <a name="windows-vms"></a>Windows VM

- 可用内存 (MB)
- 每次写入的平均磁盘秒数（逻辑磁盘）
- 每次写入的平均磁盘秒数（磁盘）
- 每次读取的平均逻辑磁盘秒数
- 每次传输的平均逻辑磁盘秒数
- 每次读取的平均磁盘秒数
- 每次传输的平均磁盘秒数
- 当前磁盘队列长度（逻辑磁盘）
- 当前磁盘队列长度（磁盘）
- 磁盘空闲时间百分比
- 文件系统错误或损坏
- 逻辑磁盘可用空间 (%) 不足
- 逻辑磁盘可用空间 (MB) 不足
- 逻辑磁盘空闲时间百分比
- 每秒内存页面数
- 读取使用的带宽百分比
- 使用的带宽百分比总计
- 写入使用的带宽百分比
- 使用的已提交内存百分比
- 磁盘空闲时间百分比
- DHCP 客户端服务运行状况
- DNS 客户端服务运行状况
- RPC 服务运行状况
- 服务器服务运行状况
- CPU 利用率百分比总计
- Windows 事件日志服务运行状况
- Windows 防火墙服务运行状况
- Windows 远程管理服务运行状况

### <a name="linux-vms"></a>Linux VM

- 磁盘平均值磁盘秒数/传输
- 磁盘平均值磁盘秒数/读取
- 磁盘平均值磁盘秒数/写入
- 磁盘运行状况
- 逻辑磁盘可用空间
- 逻辑磁盘可用空间百分比
- 逻辑磁盘可用 Inode 百分比
- 网络适配器运行状况
- 处理器时间百分比总计
- 操作系统可用内存 (MB)

## <a name="sign-in-to-the-azure-portal"></a>登录到 Azure 门户

若要登录，请转到[Azure 门户](https://portal.azure.com)。

## <a name="introduction-to-azure-monitor-for-vms-health"></a>Azure Monitor 简介 Vm 运行状况

为单个 VM 或 Vm 组使用运行状况功能之前，务必了解信息的显示方式和可视化对象所表示的含义。

### <a name="view-health-directly-from-a-vm"></a>直接从 VM 查看运行状况

若要查看 Azure VM 的运行状况，请选择**Insights （预览版）** VM 的左窗格中。 在 VM insights 页**运行状况**选项卡是默认情况下打开并显示 VM 的运行状况视图。

![用于 VM 的 Azure Monitor 针对所选 Azure 虚拟机显示的运行状况概述](./media/vminsights-health/vminsights-directvm-health.png)

在中**运行状况**选项卡上，在**来宾 VM 运行状况**，表显示了 VM 的运行状况状态和不正常的组件引发的 VM 运行状况警报总数。

有关详细信息，请参阅[警报](#alerts)。

下表介绍了为 VM 定义的运行状况状态：

|图标 |运行状况 |含义 |
|-----|-------------|---------------|
| |Healthy |虚拟机位于定义的运行状况条件。 此状态指示检测到的任何问题，且 VM 工作正常。 与父汇总监视器，运行状况汇总，并且反映子的最佳和最差状态。|
| |严重 |状态未定义的运行状况条件，，表明其中一个内或未检测到更为重要的问题。 必须解决这些问题，以恢复正常功能。 使用父汇总监视器的运行状况状态汇总，并且反映子的最佳和最差状态。|
| |警告 |状态是为定义的运行状况条件，其中一个表示警告状态和其他指示严重状态 （可以配置三个运行状况状态阈值），或当非关键问题可以导致严重问题，如果在两个阈值之间无法解析。 与父汇总监视器，如果一个或多个子级处于警告状态，父级将反映警告状态。 如果一个子处于严重状态和处于警告状态的另一个子项，父汇总将显示为关键的运行状况状态。|
| |Unknown |有几个原因，无法计算状态。 以下部分提供了更多详细信息和可能的解决方案。 |

未知运行状况状态可能造成以下问题：

- 重新配置代理和报表复制到工作区不再适用于 Vm 的 Azure 监视器已启用时指定。 若要配置代理向工作区，请参阅报告[添加或删除工作区](../platform/agent-manage.md#adding-or-removing-a-workspace)。
- VM 已删除。
- 已删除的 Vm 与 Azure 监视器关联的工作区。 如果有顶级支持权益，你可以恢复该工作区。 转到[顶级](https://premier.microsoft.com/)并打开支持请求。
- 已删除的解决方案依赖项。 若要重新启用 Log Analytics 工作区中的 ServiceMap 和 InfrastructureInsights 解决方案，使用重新安装这些解决方案[Azure 资源管理器模板](vminsights-enable-at-scale-powershell.md#install-the-servicemap-and-infrastructureinsights-solutions)。 或者，使用开始选项卡中找到的配置工作区选项。
- VM 已关闭。
- Azure VM 服务不可用，或正在执行维护。
- 在工作区[每日数据或保留期限制](../platform/manage-cost-storage.md)已满足。

选择**查看运行状况诊断**以打开一个页面，显示的 VM 的所有组件关联的运行状况条件、 状态更改和检测到监视组件与 VM 相关的其他问题。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

在中**运行状况**部分中，表显示了性能监视的运行状况条件的组件的运行状况汇总。 这些组件包括**CPU**，**内存**，**磁盘**，以及**网络**。 选择组件打开一个页面，其中列出了所有监视的条件，然后该组件的运行状况状态。

当从运行 Windows 的 Azure VM 访问运行状况时下, 显示前五个核心 Windows 服务的运行状况状态**核心服务运行状况**。 选择的任何服务打开一个页面，其中列出了该组件以及其运行状况监视的运行状况条件。

选择运行状况条件的名称将打开属性窗格。 在此窗格中，你可以查看配置详细信息，包括如果运行状况条件有对应的 Azure Monitor 警报。

有关详细信息，请参阅[运行状况诊断和使用运行状况条件](#health-diagnostics)。

### <a name="aggregate-vm-perspective"></a>聚合 VM 透视

若要查看资源组中的所有 Vm 运行状况收集，请选择**Azure Monitor**从导航列表在门户中，，然后选择**虚拟机 （预览版）** 。

![Azure Monitor 中的 VM 见解监视视图](./media/vminsights-health/vminsights-aggregate-health.png)

在中**订阅**并**资源组**与组，以查看其报告的运行状况状态的下拉列表中，选择适当的资源组包含 Vm 相关。 您的选择仅适用于运行状况功能并不会延续到**性能**或**映射**选项卡。

**运行状况**选项卡提供以下信息：

* 多少个 Vm 处于严重或不正常状态，而不是多少都正常工作，或未提交数据 （也称为的状态为未知）。
* 哪些和多少通过 OS 的 Vm 要报告的不正常状态。
* 由于检测到与处理器、 磁盘、 内存或网络适配器，分类按运行状况状态的问题，多少个 Vm 不正常。
* 多少个 Vm 不正常，这是使用核心操作系统服务，按运行状况检测到问题的。

上**运行状况**选项卡上，可以标识检测到监视的 VM，并查看警报详细信息和关联的知识文章的运行状况条件的关键问题。 这些文章可帮助诊断并修正问题。 选择任一严重性可打开按该严重性筛选的“[所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)”页。

“按操作系统列出的 VM 分发版”列表根据 Windows 版本或 Linux 分发版及其版本显示 VM。  在每个 OS 类别中，Vm 会细分进一步基于 VM 的运行状况。

![VM 见解：虚拟机分发版透视图](./media/vminsights-health/vminsights-vmdistribution-by-os.png)

选择任何列，包括**VM 计数**，**严重**，**警告**，**正常**，或**未知**。 查看筛选结果中的列表**虚拟机**匹配所选列的页。

例如，若要查看所有运行 Red Hat Enterprise Linux 版本 7.5 的 Vm，选择**VM 计数**该 OS 和它的值将列出匹配该筛选器和其当前的运行状况状态的 Vm。

![Red Hat Linux VM 的汇总信息示例](./media/vminsights-health/vminsights-rollup-vm-rehl-01.png)

在中**虚拟机**页上，如果您选择的列下的 VM 名称**VM 名称**，将转到**VM 实例**页。 此页提供警报和影响所选的 VM 的运行状况条件问题的更多详细信息。 通过选择筛选运行状况状态详细信息**运行状况状态**页后，可以看到哪些组件是不正常的左上角的图标。 此外可以查看引发的警报的严重性分类的不正常的组件的 VM 运行状况警报。

从**VM 列表**视图中，选择要打开的 VM 名称**运行状况**页上为该 VM，同样如如果所选**Insights （预览版）** 从直接的 VM。

![所选 Azure 虚拟机的 VM 见解](./media/vminsights-health/vminsights-directvm-health.png)

**Insights （预览版）** 页显示汇总运行状况状态的 VM 和警报。 此运行状况状态表示 VM 运行状况警报时的运行状况状态从正常变为不正常，引发基于条件的严重性进行分类。 选择**关键条件中的 Vm**严重运行状况状态中的一个或多个 Vm 的列表会打开一个页面。

选择一个 Vm 显示的运行状况状态**运行状况诊断**VM 视图中的。 在此视图中，您可以确定哪些运行状况条件专用于反映将运行状况状态问题。 当**运行状况诊断**页打开时，它显示 VM 的所有组件以及与当前的运行状况状态及其关联的运行状况条件。

有关详细信息，请参阅[运行状况诊断](#health-diagnostics)。

选择“查看所有运行状况条件”会打开一个页面，其中显示了适用于此功能的所有运行状况条件的列表。  可根据以下选项进一步筛选信息：

* **类型**。 有三种类型的运行状况条件以评估条件并汇总被监视的 VM 的总体运行状况状态：
    - **单元**。 测量 VM 的某些方面。 此运行状况条件类型可能会检查性能计数器来确定组件的性能，运行一个脚本来执行综合事务，或监视用于指示错误的事件。 默认情况下，筛选器设置为单位。
    - **依赖项**。 提供了不同的实体之间的运行状况汇总。 此运行状况条件允许实体依赖于另一种类型的实体所依赖的成功的操作的运行状况的运行状况。
    - **聚合**。 提供了类似的运行状况条件合并运行状况状态。 通常聚合运行状况条件下配置单元和依赖关系的运行状况标准。 除了针对面向某个实体的许多不同运行状况条件提供更好的一般性组织方式以外，聚合运行状况条件还为不同类别的实体提供唯一的运行状况。

* **类别**。 用于报告目的的类似条件进行分组的运行状况条件的类型。 这些类别**可用性**并**性能**。

若要查看哪些实例运行不正常，请选择下的值**不正常的组件**列。 在此页中，一个表将列出处于严重运行状况状态的组件。

## <a name="health-diagnostics"></a>运行状况诊断

**运行状况诊断**页面允许您直观显示 VM 的运行状况模型。 此页列出所有 VM 组件、 关联的运行状况条件、 状态更改和监视与 VM 相关的组件由标识其他重大问题。

![VM 的“运行状况诊断”页示例](./media/vminsights-health/health-diagnostics-page-01.png)

使用以下方法启动运行状况诊断：

* 通过从聚合 VM 角度 Azure Monitor 中的所有 Vm 的汇总运行状况状态：

    1. 上**运行状况**页上，选择的图标**严重**，**警告**，**正常**，或**未知**部分下的运行状况**来宾 VM 运行状况**。
    2. 请转到列出匹配该筛选的类别的所有 Vm 的页。
    3. 选择中的值**运行状况状态**列来打开应用范围限定为该 VM 的运行状况诊断。

* 通过 Azure Monitor 中的聚合 VM 透视的操作系统。 在“VM 分发版”下，选择任何一个列值会打开“虚拟机”页，并在表中返回与筛选的类别匹配的列表。   选择下的值**运行状况状态**列所选的 VM 打开运行状况诊断。
 
* 从来宾 Vm 的 Azure 监视器上的 VM**运行状况**选项卡上，通过选择**查看运行状况诊断**。

运行状况诊断运行状况将信息组织到两个类别： 可用性和性能。
 
而无需在两个类别上筛选，可以查看所有组件，如逻辑磁盘、 CPU 和等等，为定义的运行状况条件。 这些视图可以采用的条件，或通过选择时，通过这两种类别筛选结果的全部视图**可用性**或**性能**。

此外，旁边看到的条件类别**运行状况条件**列。 如果条件不匹配所选的类别，一条消息指出**没有可用于所选类别的运行状况条件**将出现在**运行状况条件**列。

运行状况条件的状态由定义四种类型之一：**关键**，**警告**，**正常**，并且**未知**。 前三列是可配置性，这意味着您可以修改直接在监视器的阈值**运行状况条件**配置窗格。 这也是可以通过使用 Azure Monitor REST API[更新监视操作](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。 **未知**不是可配置并保留针对特定方案。

**运行状况诊断**页包含三个主要部分：

* 组件模型
* 运行状况条件
* 状态更改

![“运行状况诊断”页包含的部分](./media/vminsights-health/health-diagnostics-page-02.png)

### <a name="component-model"></a>组件模型

中的最左列**运行状况诊断**页面**组件模型**。 所有组件，与 VM 相关联，该列及其当前运行状况状态中都显示。

在以下示例中，已发现的组件是**磁盘**，**逻辑磁盘**，**处理器**，**内存**，和**操作系统**。 可在此列中发现并显示这些组件的多个实例。

例如下, 图显示了 VM 具有两个实例的逻辑磁盘**c:** 并**d:** ，处于正常状态是：

![运行状况诊断中显示的示例组件模型](./media/vminsights-health/health-diagnostics-page-component.png)

### <a name="health-criteria"></a>运行状况条件

运行状况诊断页中的中心列是**运行状况条件**。 为 VM 定义的运行状况模型在分层树中显示。 VM 的运行状况模型包括单元和聚合运行状况条件。

![运行状况诊断中显示的示例运行状况条件](./media/vminsights-health/health-diagnostics-page-healthcriteria.png)

运行状况标准测量运行状况的监视的实例，可以为阈值的值，状态为实体中，依次类推。 运行状况条件具有两个或三个可配置的运行状况状态阈值，如前面所述。 在任何时刻，运行状况条件可以是中的唯一一种其潜在状态。

运行状况模型定义条件，以确定运行状况的总体目标和目标组件。 条件的层次结构所示**运行状况条件**部分**运行状况诊断**页。

运行状况汇总策略是聚合运行状况条件的配置的一部分 (默认值设置为**最差的**)。 您可以找到一组默认的运行状况条件中的此功能的一部分运行[监视配置的详细信息](#monitoring-configuration-details)本文的部分。

此外可以使用 Azure Monitor REST API[监视器实例列表，按资源](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitorinstances/listbyresource)若要获取所有运行状况条件的列表。 此条件包括针对 Azure VM 资源运行的配置详细信息。

**单元**运行状况条件类型可以通过选择右侧的省略号链接来修改其配置。 选择**显示详细信息**以打开配置窗格。

![配置运行状况条件的示例](./media/vminsights-health/health-diagnostics-vm-example-02.png)

所选的运行状况条件，如果您使用示例的配置窗格中**平均磁盘秒每次写入**，可以将阈值配置使用不同的数字值。 它是二态监视器，这意味着它可以仅从更改**正常**到**警告**。

其他运行状况条件有时使用三种状态，可在其中配置警告和严重运行状况状态阈值的值。 您还可以通过使用 Azure Monitor REST API 修改阈值[监视器配置](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/monitors/update)。

>[!NOTE]
>将运行状况条件配置更改应用于一个实例，将它们应用到所有受监视的实例中。 例如，如果您选择**磁盘-1 d:** ，然后修改**平均磁盘秒每次写入**阈值，此更改适用于发现和监视 VM 上的所有实例。


![配置单元监视器的运行状况条件的示例](./media/vminsights-health/health-diagnostics-criteria-config-01.png)

如果你想要了解有关运行状况条件的详细信息，我们提供了知识库文章来帮助你确定问题、 原因和解决方法。 选择**查看信息**在页上，若要查看相关的知识库文章。

若要查看 Azure Monitor 中包含的 Vm 运行状况的所有知识库文章，请参阅[Azure 监视器运行状况知识文档](https://docs.microsoft.com/azure/monitoring/infrastructure-health/)。

### <a name="state-changes"></a>状态更改

最右侧的栏**运行状况诊断**页面**状态更改**。 此列列出了与中所选的运行状况条件相关联的所有状态更改**运行状况条件**部分中或如果 VM 已从所选 VM 的状态更改**组件模型**或**运行状况条件**表的列。

![运行状况诊断中显示的状态更改示例](./media/vminsights-health/health-diagnostics-page-statechanges.png)

以下部分显示运行状况条件状态和关联的时间。 此信息显示在列顶部的最新状态。

### <a name="association-of-component-model-health-criteria-and-state-changes-columns"></a>组件模型、 运行状况条件和状态更改的列的关联

这三个列彼此互相链接。 当选择中的实例**组件模型**列中，**运行状况条件**列的筛选到该组件视图。 相应地，**状态更改**所选的运行状况条件的更新列。

![选择受监视实例和结果的示例](./media/vminsights-health/health-diagnostics-vm-example-01.png)

例如，如果您选择*磁盘-1 的 d:* 下的列表**组件模型**，**运行状况条件**筛选*磁盘-1 D:* ，和**状态更改**状态更改的可用性基于所示*磁盘-1 的 d:* 。

若要查看更新后的健康状态，可以刷新运行状况诊断页通过选择**刷新**链接。 如果基于预定义的轮询间隔对运行状况条件的运行状况做了更新，则此任务可以避免等待显示最新运行状况。 **运行状况条件状态**是筛选器，可以将范围限制基于所选的运行状况状态的结果：正常、 警告、 严重、 未知，和全部。 **上次更新时间**在右上角的时间表示的上次刷新运行状况诊断页。

## <a name="alerts"></a>警报

Azure Vm 运行状况监视器与集成[Azure 警报](../../azure-monitor/platform/alerts-overview.md)。 它会发出警报时预定义的条件，当检测到，将更改从正常运行状态为不正常状态。 警报按严重性，严重性 0 严重性级别 4 中，通过使用 0 作为最高级别的严重性级别从分类。

警报与不相关的操作组，以触发警报时通知你。 订阅所有者必须通过中的步骤来配置通知[配置警报](#configure-alerts)部分。

按严重性分类的 VM 运行状况通知的总数已接入**运行状况**下的仪表板**警报**部分。 选择警报总数或者对应于某个严重性级别的编号时，“警报”页将会打开并列出与所选内容匹配的所有警报。 

例如，如果您选择对应的行**严重性级别 1**，您将看到以下视图：

![所有严重性级别 1 警报的示例](./media/vminsights-health/vminsights-sev1-alerts-01.png)

**的所有警报**页不作用域仅显示警报匹配你的选择。 它还通过筛选**资源类型**以显示仅引发的 VM 资源的运行状况警报。 这种格式反映在警报列表中，列下**目标资源**，其中会显示 Azure VM 所引发的警报时的不正常条件已满足。

不应包含在此视图中的其他资源类型或服务的警报。 这些警报包括基于日志查询或通常从默认的 Azure Monitor 查看的指标警报的日志警报[的所有警报](../../azure-monitor/platform/alerts-overview.md#all-alerts-page)页。

可以通过在页面顶部的下拉列表菜单中选择值来筛选此视图。

|列 |描述 |
|-------|------------|
|订阅 |选择 Azure 订阅。 只有选定订阅中的警报才会包含在视图中。 |
|资源组 |选择单个资源组。 只有包含选定资源组中的目标的警报才会包含在视图中。 |
|资源类型 |选择一个或多个资源类型。 默认情况下，仅选择目标虚拟机的警报并将其列在此视图中  。 仅在指定资源组后，才显示此列。 |
|Resource |选择资源。 只有包含该资源（作为目标）的警报才会包含在视图中。 仅后未指定资源类型，此列才可用。 |
|Severity |选择警报严重性，或选择“所有”以包含所有严重性的警报。  |
|监视条件 |如果它们已触发或解析系统，如果条件不再处于活动状态，请选择监视条件的筛选器警报。 或者，选择**所有**包括的所有条件的警报。 |
|警报状态 |选择警报的状态，**新建**，**确认**，**已关闭**，或者**所有**包括的所有状态的警报。 |
|监视服务 |选择一个服务，或选择“所有”以包含所有服务。  此功能支持仅从 VM Insights 警报。|
|时间范围| 只有在选定时间范围内触发的警报才会包含在该视图中。 支持的值为过去 1 小时、过去 24 小时、过去 7 天和过去 30 天。 |

选择一个警报，当**警报详细信息**显示页。 此页提供警报的详细信息，并允许您以更改其状态。

要详细了解如何管理警报，请参阅[使用 Azure Monitor 创建、查看和管理警报](../../azure-monitor/platform/alerts-metric.md)。

>[!NOTE]
>创建新警报基于运行状况条件或修改现有运行状况警报规则在 Azure Monitor 中的从门户目前不支持。


![所选警报的警报详细信息窗格](./media/vminsights-health/alert-details-pane-01.png)

可以通过选择它们，然后选择更改一个或多个警报的警报状态**更改状态**从**的所有警报**左上角中的页。 选择一种状态上**更改警报状态**窗格中，添加中更改的描述**注释**字段，然后再选择**确定**提交所做的更改。 当对信息进行验证，并应用所做的更改时，下面跟踪进度**通知**菜单中。

### <a name="configure-alerts"></a>配置警报
无法从 Azure 门户来管理某些警报管理任务。 必须使用执行这些任务[Azure Monitor REST API](https://docs.microsoft.com/rest/api/monitor/microsoft.workloadmonitor/components)。 具体而言：

- 启用或禁用运行状况条件的警报
- 设置运行状况条件警报通知

每个示例使用[ARMClient](https://github.com/projectkudu/armclient)在 Windows 计算机上。 如果您不熟悉此方法，请参阅[使用 ARMClient](../platform/rest-api-walkthrough.md#use-armclient)。

#### <a name="enable-or-disable-an-alert-rule"></a>启用或禁用警报规则

若要启用或禁用特定的运行状况条件，该属性时的警报**alertGeneration**必须具有的值修改**禁用**或**已启用**。

若要识别*monitorId*特定的运行状况条件，下面的示例演示如何查询条件值**LogicalDisk\Avg Disk 秒 Per Transfer**:

1. 在终端窗口中，键入 **armclient.exe login**。 这样做会提示你登录到 Azure。

2. 输入以下命令检索所有活动的特定 VM 上的运行状况条件并确定的值*monitorId*属性：

    ```
    armclient GET "subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/monitors?api-version=2018-08-31-preview”
    ```

    下面的示例演示的输出*armclient GET*命令。 记下的值*MonitorId*。 此值是必需的下一步，我们必须在此处指定的运行状况条件 ID 并修改其属性，以创建警报。

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

4. 输入在步骤 2 中用于验证的属性值设置为 GET 命令**禁用**。

#### <a name="associate-an-action-group-with-health-criteria"></a>将操作组与运行状况条件相关联

不正常的运行状况条件从生成警报时，azure Vm 运行状况监视器支持 SMS 和电子邮件通知。 若要配置通知，请注意已配置的操作组发送短信或电子邮件通知的名称。

>[!NOTE]
>必须针对你想要接收的通知每个受监视 VM 执行此操作。 它不能应用于资源组中的所有 Vm。

1. 在终端窗口中，输入*armclient.exe login*。 这样做会提示你登录到 Azure。

2. 输入以下命令以将操作组关联的警报规则：
 
    ```
    $payload = "{'properties':{'ActionGroupResourceIds':['/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/microsoft.insights/actionGroups/actiongroupName']}}"
    armclient PUT https://management.azure.com/subscriptions/subscriptionId/resourceGroups/resourcegroupName/providers/Microsoft.Compute/virtualMachines/vmName/providers/Microsoft.WorkloadMonitor/notificationSettings/default?api-version=2018-08-31-preview $payload
    ```

3. 若要验证的属性的值**actionGroupResourceIds**已成功更新，请输入以下命令：

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

- 若要标识限制和 VM 的总体性能，请参阅[查看 Azure VM 性能](vminsights-performance.md)。
- 若要了解有关发现的应用程序依赖关系，请参阅[查看 Azure 监视的 Vm 映射](vminsights-maps.md)。
