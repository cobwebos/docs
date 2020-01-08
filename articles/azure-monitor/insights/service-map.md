---
title: 使用 Azure 中的服务映射解决方案 | Microsoft Docs
description: 服务映射是 Azure 中的解决方案，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 本文提供了有关在环境中部署服务映射并在各种方案中使用它的详细信息。
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/24/2019
ms.openlocfilehash: d4fd443959604f1a50dffbcb646bbe66fa159f8d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75402599"
---
# <a name="using-service-map-solution-in-azure"></a>使用 Azure 中的服务映射解决方案

服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 使用服务映射，可按所需方式查看服务器：将其视为提供重要服务的互连系统。 服务映射显示 TCP 连接的任何体系结构中服务器之间的连接、进程、入站和出站连接延迟及端口，只需安装代理，无需任何其他配置。

本指南介绍载入和使用服务映射的详细信息。 有关配置此解决方案的必备组件的信息，请参阅[启用用于 VM 的 Azure Monitor 概述](vminsights-enable-overview.md#prerequisites)。 总而言之，需要以下各项：

* 用于启用此解决方案的 Log Analytics 工作区。

* 安装在 Windows 计算机或 Linux 服务器上的 Log Analytics 代理配置为报告启用了解决方案的同一工作区。

* 安装在 Windows 计算机或 Linux 服务器上的依赖关系代理。

>[!NOTE]
>如果已部署服务映射，现在还可以在面向 VM 的 Azure Monitor 中查看映射，其中包括监视 VM 运行状况和性能的附加功能。 若要了解详细信息，请参阅[面向 VM 的 Azure Monitor 概述](../../azure-monitor/insights/vminsights-overview.md)。 若要了解服务映射解决方案与用于 VM 的 Azure Monitor 地图功能之间的差异，请参阅以下[常见问题解答](vminsights-faq.md#how-is-azure-monitor-for-vms-map-feature-different-from-service-map)。

## <a name="sign-in-to-azure"></a>登录 Azure

在 [https://portal.azure.com](https://portal.azure.com) 中登录 Azure 门户。

## <a name="enable-service-map"></a>启用服务映射

1. 从[Azure marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.ServiceMapOMS?tab=Overview)或使用[从解决方案库添加监视解决方案](solutions.md)中所述的过程，启用服务映射解决方案。
1. 在[Windows 上安装依赖关系代理](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-windows)，或在要从中获取数据的每台计算机上[安装 Linux 上的依赖关系](vminsights-enable-hybrid-cloud.md#install-the-dependency-agent-on-linux)代理。 依赖关系代理可以监视与直接邻居之间的连接，因此不需要在每台计算机上都具有代理。

在 Azure 门户从 Log Analytics 工作区访问服务映射，并选择左窗格中的“解决方案”选项。<br><br> ![选择工作区中的“解决方案”选项](./media/service-map/select-solution-from-workspace.png)。<br> 从解决方案列表中选择“ServiceMap(workspaceName)”，并在服务映射解决方案概述页面单击“服务映射摘要”标题。<br><br> ![服务映射摘要标题](./media/service-map/service-map-summary-tile.png)。

## <a name="use-cases-make-your-it-processes-dependency-aware"></a>用例：使 IT 进程感知依赖关系

### <a name="discovery"></a>发现

服务映射自动在服务器、进程和第三方服务上生成依赖关系的常见引用映射。 它可发现并映射所有 TCP 依赖关系，从而将意外连接、依赖的远程第三方系统和依赖关系标识到网络的传统深色区域（如 Active Directory）。 服务映射可发现托管系统尝试进行的失败网络连接，帮助标识潜在服务器配置错误、服务中断和网络问题。

### <a name="incident-management"></a>事件管理

服务映射可显示系统的连接方式以及相互的影响，无需再猜测问题隔离。 除了标识失败的连接，它还帮助标识配置错误的负载均衡器、关键服务上的意外或多余负载以及与生产系统通信的开发人员计算机等恶意客户端。 通过将集成的工作流与更改跟踪配合使用，还可以查看后端计算机或服务上的更改事件是否能解释事件的根本原因。

### <a name="migration-assurance"></a>迁移保障

使用服务映射可以有效地计划、加快和验证 Azure 迁移，帮助确保迁移所有内容，不会发生意外中断。 可以发现所有需要一起迁移的互相依赖的系统、访问系统配置和容量，并识别运行中的系统仍然为用户提供服务还是在等待解除授权而非迁移。 移动完成后，可以检查客户端负载和标识，验证测试系统和客户是否正在连接。 如果子网计划和防火墙定义有问题，“服务映射”映射中的失败连接将指向需要连接的系统。

### <a name="business-continuity"></a>业务连续性

如果使用 Azure Site Recovery 并需要帮助定义应用程序环境的恢复顺序，服务映射可自动显示系统互相依赖的方式，以便确保恢复计划可靠。 通过选择关键服务器或组并查看其客户端，可以标识要在该服务器还原并可用后恢复的前端系统。 相反，通过查看关键服务器的后端依赖关系，可以标识要在焦点系统还原之前恢复的系统。

### <a name="patch-management"></a>修补程序管理

“服务映射”通过显示依赖于服务的其他团队和服务器，增强对系统更新评估的使用，以便在对系统进行修补之前通知他们。 服务映射还通过显示服务在完成修补和重启后是否可用并已正确连接，来增强修补程序管理。

## <a name="mapping-overview"></a>映射概述

“服务映射”代理收集有关已安装 TCP 的服务器上所有 TCP 连接的进程的信息，以及有关每个进程的入站和出站连接的详细信息。

从左侧窗格的列表中，可选择具有服务映射代理的计算机或组，在指定时间范围内使其依赖关系可视化。 计算机依赖关系映射侧重于特定计算机，并显示属于该计算机的直接 TCP 客户端或服务器的所有计算机。  计算机组映射显示多组服务器及其依赖关系。

![服务映射概述](media/service-map/service-map-overview.png)

可在映射中扩展计算机，以在选定时间范围内显示具有有效网络连接的运行中的进程组和进程。 展开具有服务映射代理的远程计算机以显示进程详细信息时，仅显示与焦点计算机通信的进程。 连接到焦点计算机的无代理前端计算机计数显示在它们所连接到的进程左侧。 如果焦点计算机连接到无代理的后端计算机，则服务器端口组中包含该后端服务器以及与同一端口号相连的其他连接。

默认情况下，“服务映射”映射显示过去 30 分钟的依赖关系信息。 使用左上角的时间控件，可在映射中查询历史时间范围（最多一小时），显示依赖关系在过去（例如，发生事件期间或发生更改之前）的出现形式。 服务映射数据在付费工作区中存储 30 天，在免费工作区中存储 7 天。

## <a name="status-badges-and-border-coloring"></a>状态徽章和边框着色

映射中每个服务器的底部可以是状态徽章的列表，用于表示有关服务器的状态信息。 徽章指示其中一个解决方案集成中有服务器的一些相关信息。 单击徽章，直接转到右侧窗格中的状态详细信息。 当前可用状态徽章包括警报、服务台、更改、安全性和更新。

根据状态徽章的严重性，计算机节点边框的颜色可以是红色（严重）、黄色（警告）或蓝色（信息）。 该颜色表示任何状态徽章的最严重状态。 灰色边框指示没有状态指示器的节点。

![状态徽章](media/service-map/status-badges.png)

## <a name="process-groups"></a>进程组

进程组将与常用产品或服务关联的进程合并到一个进程组中。  展开计算机节点后，将显示独立的进程以及进程组。  如果进程组中某个进程的入站和出站连接失败，则整个进程组的连接都将显示为失败。

## <a name="machine-groups"></a>计算机组

使用计算机组可以查看以一组服务器为中心的映射，而不只是某个服务器的映射，这样，通过一个映射就可以查看某个多层应用程序或服务器群集的所有成员。

用户可选择哪些服务器属于一个组，并选择该组的名称。  然后可以选择查看该组的所有进程和连接，或者仅查看与该组其他成员直接相关的进程和连接。

![计算机组](media/service-map/machine-group.png)

### <a name="creating-a-machine-group"></a>创建计算机组

若要创建组，请在“计算机”列表中选择所需的一个或多个计算机，然后单击“添加到组”。

![创建组](media/service-map/machine-groups-create.png)

在这里，可以选择“新建”，并为组指定名称。

![为组命名](media/service-map/machine-groups-name.png)

>[!NOTE]
>计算机组限制为 10 个服务器。

### <a name="viewing-a-group"></a>查看组

创建一些组后，便可以选择“组”选项卡查看它们。

![“组”选项卡](media/service-map/machine-groups-tab.png)

然后选择组名称，查看该计算机组的映射。
![计算机组](media/service-map/machine-group.png) 属于该组的计算机在映射中以白色框出。

展开组将列出构成计算机组的计算机。

![计算机组 计算机](media/service-map/machine-groups-machines.png)

### <a name="filter-by-processes"></a>按进程筛选

可以在以下两种映射视图之间切换：显示组中的所有进程和连接，以及仅显示与计算机组直接相关的进程和连接。  默认视图是显示所有进程。  可以单击映射上方的筛选器图标更改视图。

![筛选组](media/service-map/machine-groups-filter.png)

如果选择“所有进程”，映射将包括组中每个计算机上的所有进程和连接。

![计算机组 所有进程](media/service-map/machine-groups-all.png)

如果将视图更改为仅显示“与组连接的进程”，映射将缩小为仅包括与组中的其他计算机直接相关的进程和连接，从而创建一个简化的视图。

![计算机组 筛选后的进程](media/service-map/machine-groups-filtered.png)
 
### <a name="adding-machines-to-a-group"></a>将计算机添加到组

若要将计算机添加到现有组，请选中所需计算机旁边的框，单击“添加到组”。  然后选择要将计算机添加到的组。
 
### <a name="removing-machines-from-a-group"></a>从组中删除计算机

在“组”列表中，展开组名称以列出计算机组中的计算机。  然后，单击要删除的计算机旁边的省略号菜单，选择“删除”。

![从组中删除计算机](media/service-map/machine-groups-remove.png)

### <a name="removing-or-renaming-a-group"></a>删除或重命名组

单击“组”列表中组名称旁边的省略号菜单。

![计算机组 菜单](media/service-map/machine-groups-menu.png)


## <a name="role-icons"></a>角色图标

某些进程在计算机上充当特定角色：Web 服务器、应用程序服务器、数据库等。 “服务映射”可批注进程和计算机设备，包含的角色图标有助于以显眼的方式标识进程或服务器扮演的角色。

| 角色图标 | Description |
|:--|:--|
| ![Web 服务器](media/service-map/role-web-server.png) | Web 服务器 |
| ![应用服务器](media/service-map/role-application-server.png) | 应用程序服务器 |
| ![数据库服务器](media/service-map/role-database.png) | 数据库服务器 |
| ![LDAP 服务器](media/service-map/role-ldap.png) | LDAP 服务器 |
| ![SMB 服务器](media/service-map/role-smb.png) | SMB 服务器 |

![角色图标](media/service-map/role-icons.png)


## <a name="failed-connections"></a>失败的连接

对于进程和计算机，失败的连接显示在“服务映射”映射中，并显示一条红色虚线，指示客户端系统无法访问进程或端口。 如果已部署服务映射代理的任何系统正尝试失败的连接，将从该系统报告失败的连接。 服务映射通过观察无法建立连接的 TCP 套接字测量此过程。 出现失败连接可能是因为防火墙、客户端或服务器中的配置错误或远程服务不可用。

![失败的连接](media/service-map/failed-connections.png)

了解失败的连接可帮助进行疑难解答、迁移验证、安全分析和了解总体体系结构。 有时失败的连接无害，但它们通常直接指向某个问题，例如故障转移环境突然无法访问，或两个应用程序层在进行云迁移后无法通信。

## <a name="client-groups"></a>客户端组

客户端组是映射上的各个框，表示不具备依赖关系代理的客户端计算机。 单个客户端组表示单个进程或计算机的客户端。

![客户端组](media/service-map/client-groups.png)

若要查看客户端组中的服务器 IP 地址，请选择该组。 “客户端组属性”窗格中会列出该组的内容。

![客户端组属性](media/service-map/client-group-properties.png)

## <a name="server-port-groups"></a>服务器端口组

服务器端口组是表示服务器（不具备依赖关系代理）上的服务器端口的各个框。 框中包含服务器端口，以及连接到该端口的服务器计数。 展开此框可查看各个服务器和连接。 如果框中仅有一个服务器，则会列出该服务器的名称或 IP 地址。

![服务器端口组](media/service-map/server-port-groups.png)

## <a name="context-menu"></a>上下文菜单

单击任意服务器右上方的省略号 (...)，显示该服务器的上下文菜单。

![失败的连接](media/service-map/context-menu.png)

### <a name="load-server-map"></a>加载服务器映射

单击“加载服务器映射”，转到新映射，其中所选服务器作为新的焦点计算机。

### <a name="show-self-links"></a>显示自链接

单击“显示自链接”，重绘包含任何自链接的服务器节点，这些自链接是表示服务器中进程的开始和结束的 TCP 连接。 如果显示了自链接，则该菜单命令更改为“隐藏自链接”，以便隐藏自链接。

## <a name="computer-summary"></a>计算机摘要

“计算机摘要”窗格包括服务器的操作系统、依赖关系计数以及来自其他解决方案的各种数据的概述。 此类数据包括性能指标、服务台票证、更改跟踪、安全性和更新。

![“计算机摘要”窗格](media/service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>计算机和进程属性

导航“服务映射”映射时，可选择计算机和进程获取有关其属性的其他上下文。 计算机提供有关 DNS 名称、IPv4 地址、CPU 和内存容量、VM 类型、操作系统和版本、上次重启时间及其 OMS 和服务映射代理的 ID 的信息。

![“计算机属性”窗格](media/service-map/machine-properties.png)

可以从操作系统元数据中收集有关运行中的进程的进程详细信息，包括进程名称、进程说明、用户名和域（在 Windows 上）、公司名称、产品名称、产品版本、工作目录、命令行和进程启动时间。

![“进程属性”窗格](media/service-map/process-properties.png)

“进程摘要”窗格提供有关该进程的连接性的其他信息，包括其绑定端口、入站和出站连接以及失败的连接。

![“进程摘要”窗格](media/service-map/process-summary.png)

## <a name="alerts-integration"></a>警报集成

服务映射与 Azure 警报集成，可显示所选时间范围内所选服务器触发的警报。 如果当前有警报，服务器会显示一个图标，且“计算机警报”窗格会列出警报。

![“计算机警报”窗格](media/service-map/machine-alerts.png)

若要使服务映射能够显示相关警报，请创建对特定计算机触发的警报规则。 创建相应的警报：
- 包含一个子句，以按计算机分组（例如“by Computer interval 1 minute”）。
- 根据指标度量值选择警报。

## <a name="log-events-integration"></a>日志事件集成

服务映射与日志搜索集成，可显示所选时间范围内所选服务器的所有可用的日志事件。 可单击事件计数列表中的任意行，跳转到“日志搜索”并查看单独的日志事件。

![“计算机日志事件”窗格](media/service-map/log-events.png)

## <a name="service-desk-integration"></a>服务台集成

当在 Log Analytics 工作区中启用并配置了这两个解决方案时，服务映射会自动与 IT Service Management Connector 集成。 服务映射中的集成标记为“服务台”。 有关详细信息，请参阅[使用 IT Service Management Connector 集中管理 ITSM 工作项](https://docs.microsoft.com/azure/log-analytics/log-analytics-itsmc-overview)。

“计算机服务台”窗格列出所选时间范围内选定服务器的所有 IT Service Management 事件。 如果当前存在项，服务器会显示一个图标，且“计算机服务台”窗格会列出存在的项。

![“计算机服务台”窗格](media/service-map/service-desk.png)

若要打开连接的 ITSM 解决方案中的项，请单击“查看工作项”。

若要在日志搜索中查看项的详细信息，请单击“在日志搜索中显示”。
连接指标将写入到 Log Analytics 的两个新表中 

## <a name="change-tracking-integration"></a>更改跟踪集成

当在 Log Analytics 工作区中启用并配置了这两个解决方案时，服务映射会自动与更改跟踪集成。

“计算机更改跟踪”窗格列出所有更改（最新更改列在顶部），以及一个向下钻取到日志搜索以获取其他详细信息的链接。

![“计算机更改跟踪”窗格](media/service-map/change-tracking.png)

下图是在选择“在 Log Analytics 中显示”后可能显示的 ConfigurationChange 事件的详细视图。

![ConfigurationChange 事件](media/service-map/configuration-change-event-01.png)

## <a name="performance-integration"></a>性能集成

“计算机性能”窗格显示所选服务器的标准性能指标。 指标包括 CPU 利用率、内存利用率、发送和接收的网络字节数，以及发送和接收网络字节数最多的进程的列表。

![“计算机性能”窗格](media/service-map/machine-performance.png)

若要查看性能数据，就可能需要[启用相应的 Log Analytics 性能计数器](https://docs.microsoft.com/azure/log-analytics/log-analytics-data-sources-performance-counters)。  要启用的计数器：

Windows：
- Processor(*)\\% Processor Time
- Memory\\% Committed Bytes In Use
- Network Adapter(*)\\Bytes Sent/sec
- Network Adapter(*)\\Bytes Received/sec

Linux：
- Processor(*)\\% Processor Time
- Memory(*)\\% Used Memory
- Network Adapter(*)\\Bytes Sent/sec
- Network Adapter(*)\\Bytes Received/sec

若要获取网络性能数据，还必须在工作区中启用 Wire Data 2.0 解决方案。
 
## <a name="security-integration"></a>安全集成

当在 Log Analytics 工作区中启用并配置了这两个解决方案时，服务映射会自动与安全和审核集成。

“计算机安全”面板显示所选服务器的安全和审核解决方案的数据。 该窗格列出所选时间范围内服务器的所有未解决安全问题的摘要。 单击任意安全问题，向下钻取到日志搜索，了解有关安全问题的详细信息。

![“计算机安全性”窗格](media/service-map/machine-security.png)

## <a name="updates-integration"></a>更新集成

当在 Log Analytics 工作区中启用并配置了这两个解决方案时，服务映射会自动与更新管理集成。

“计算机更新”面板显示所选服务器的更新管理解决方案的数据。 该窗格列出所选时间范围内服务器缺少的所有更新的摘要。

![“计算机更改跟踪”窗格](media/service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics 记录

服务映射的计算机和进程清单数据可在 Log Analytics 中[搜索](../../azure-monitor/log-query/log-query-overview.md)。 此数据可应用于包括迁移计划、容量分析、发现和按需性能故障排除在内的方案。

除了在进程或计算机启动或载入服务映射时生成的记录外，还针对每个唯一计算机和进程每小时生成一条记录。 这些记录的属性在下表中列出。 ServiceMapComputer_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中计算机资源的字段。 ServiceMapProcess_CL 事件中的字段和值映射到 ServiceMap Azure 资源管理器 API 中进程资源的字段。 ResourceName_s 字段与相应的 Azure Resource Manager 资源中的名称字段匹配。 

>[!NOTE]
>随着服务映射功能增加，这些字段可能会更改。

包含内部生成的可用于标识唯一进程和计算机的属性：

- 计算机：使用 *ResourceId* 或 *ResourceName_s* 唯一标识 Log Analytics 工作区中的计算机。
- 进程：使用 *ResourceId* 唯一标识 Log Analytics 工作区中的进程。 *ResourceName_s* 在运行该进程的计算机 (MachineResourceName_s) 的上下文中唯一 

由于在指定的时间范围内，指定的进程和计算机可能存在多条记录，因此针对同一个计算机或进程的查询可能返回多条记录。 若要仅添加最新记录，请在查询中添加“| dedup ResourceId”。

### <a name="connections"></a>连接

连接指标将写入到 Log Analytics 的新表 VMConnection 中。 此表提供有关计算机的连接（入站和出站）的信息。 还可以通过 API 公开连接指标。使用这些 API 可以获取某个时间范围内的特定指标。  在侦听套接字上执行 *accept* 命令后生成的 TCP 连接是入站连接，针对给定的 IP 和端口执行 *connect* 命令后建立的 TCP 连接是出站连接。 连接方向由 Direction 属性表示，可将其设置为 **inbound** 或 **outbound**。 

这些表中的记录是基于依赖项代理报告的数据生成的。 每条记录表示一分钟时间间隔内观测到的结果。 TimeGenerated 属性表示时间间隔的开始时间。 每条记录包含用于识别相应实体（即连接或端口）以及与该实体关联的指标的信息。 目前，只会报告使用“基于 IPv4 的 TCP”发生的网络活动。

为了控制成本和复杂性，连接记录不会显示单个物理网络连接。 多个物理网络连接分组到一个逻辑连接中，然后在相应的表中反映该逻辑连接。  这意味着，*VMConnection* 表中的记录表示逻辑分组，而不是观测到的单个物理连接。 在给定的一分钟时间间隔内共享以下属性相同值的物理网络连接将聚合到 *VMConnection* 中的单个逻辑记录内。 

| 属性 | Description |
|:--|:--|
| `Direction` |连接方向，值为 *inbound* 或 *outbound* |
| `Machine` |计算机 FQDN |
| `Process` |进程或进程组的标识，状态为正在启动/接受连接 |
| `SourceIp` |源的 IP 地址 |
| `DestinationIp` |目标的 IP 地址 |
| `DestinationPort` |目标的端口号 |
| `Protocol` |用于连接的协议。  值为 *tcp*。 |

为了帮助你权衡分组造成的影响，以下记录属性中提供了有关分组的物理连接数的信息：

| 属性 | Description |
|:--|:--|
| `LinksEstablished` |在报告时间范围内建立的物理网络连接数 |
| `LinksTerminated` |在报告时间范围内终止的物理网络连接数 |
| `LinksFailed` |在报告时间范围内失败的物理网络连接数 此信息目前仅适用于出站连接。 |
| `LinksLive` |在报告时间范围结束时打开的物理网络连接数|

#### <a name="metrics"></a>度量值

除了连接计数指标以外，以下记录属性中还包含了有关在给定逻辑连接或网络端口上发送和接收的数据量的信息：

| 属性 | Description |
|:--|:--|
| `BytesSent` |在报告时间范围内发送的字节总数 |
| `BytesReceived` |在报告时间范围内接收的字节总数 |
| `Responses` |在报告时间范围内观测到的响应数。 
| `ResponseTimeMax` |在报告时间范围内观测到的最大响应时间（毫秒）。  如果无值，则该属性为空。|
| `ResponseTimeMin` |在报告时间范围内观测到的最小响应时间（毫秒）。  如果无值，则该属性为空。|
| `ResponseTimeSum` |在报告时间范围内观测到的所有响应时间的和（毫秒）。  如果无值，则该属性为空|

报告的第三种数据类型是响应时间 - 调用方花费了多长时间来等待通过连接发送请求进行处理，并收到远程终结点的响应。 报告的响应时间是底层应用程序协议的真实响应时间的估算值。 它是基于物理网络连接的源与目标端之间的数据流观测结果，使用试探法计算出来的。 从概念上讲，它是请求的最后一个字节离开发送方的时间，与发送方收到响应的最后一个字节的时间的差。 这两个时间戳用于描述给定物理连接上的请求和响应事件。 两者的差表示单个请求的响应时间。 

在此功能的第一个版本中，我们的算法是求近似值，根据给定网络连接所用的实际应用程序协议，其成功度各不相同。 例如，当前做法非常适合基于请求-响应的协议（例如 HTTP (S)），但不适合单向协议或基于消息队列的协议。

考虑的几个要点：

1. 如果进程在相同的 IP 地址上接受连接，但通过多个网络接口接受连接，则为每个接口单独报告一条记录。 
2. 带通配符 IP 的记录不包含任何活动。 包含此类记录的目的是表示在计算机上为入站流量开放了某个端口这一事实。
3. 为了降低详细程度和数据量，存在带有特定 IP 地址的匹配记录（适用于相同的进程、端口和协议）时，将省略带通配符 IP 的记录。 省略了通配符 IP 记录后，具有特定 IP 地址的 IsWildcardBind 记录属性将设置为“True”，表示已通过报告计算机的每个接口公开了该端口。
4. 仅在特定接口上绑定的端口的 IsWildcardBind 设置为“False”。

#### <a name="naming-and-classification"></a>命名和分类

为提供方便，RemoteIp 属性中包含了连接的远程端的 IP 地址。 对于入站连接，RemoteIp 与 SourceIp 相同；对于出站连接，RemoteIp 与 DestinationIp 相同。 RemoteDnsCanonicalNames 属性表示计算机针对 RemoteIp 报告的 DNS 规范名称。 RemoteDnsQuestions 和 RemoteClassification 属性保留供将来使用。 

#### <a name="geolocation"></a>地理位置

*VMConnection* 还包含以下记录属性中每个连接记录的远程端的地理位置信息： 

| 属性 | Description |
|:--|:--|
| `RemoteCountry` |托管 RemoteIp 的国家/地区的名称。  例如 *United States* |
| `RemoteLatitude` |地理位置的纬度。  例如 *47.68* |
| `RemoteLongitude` |地理位置的经度。  例如 *-122.12* |

#### <a name="malicious-ip"></a>恶意 IP

将会根据一组 IP 检查 *VMConnection* 表中的每个 RemoteIp 属性，以识别已知的恶意活动。 如果 RemoteIp 识别为恶意，则会在以下记录属性中填充以下属性（如果未将该 IP 视为恶意，则这些属性为空）：

| 属性 | Description |
|:--|:--|
| `MaliciousIp` |RemoteIp 地址 |
| `IndicatorThreadType` |检测到的威胁标志是以下值之一：Botnet、C2、CryptoMining、Darknet、DDos、MaliciousUrl、Malware、Phishing、Proxy、PUA 和 Watchlist。   |
| `Description` |观察到的威胁说明。 |
| `TLPLevel` |交通信号灯协议 (TLP) 级别是以下定义值之一：White、Green、Amber 和 Red。 |
| `Confidence` |值介于 0 和 100 之间。 |
| `Severity` |值介于 0 和 5 之间，其中 5 表示最严重，0 表示毫不严重。 默认值为 3。  |
| `FirstReportedDateTime` |提供程序第一次报告指标。 |
| `LastReportedDateTime` |Interflow 最后一次看到指标。 |
| `IsActive` |使用值 True 或 False 指明是否停用标志。 |
| `ReportReferenceLink` |与给定可观测结果相关的报告的链接。 |
| `AdditionalInformation` |提供观测到的威胁的其他信息（若有）。 |

### <a name="servicemapcomputer_cl-records"></a>ServiceMapComputer_CL 记录

类型为 *ServiceMapComputer_CL* 的记录包含具有服务映射代理的服务器的清单数据。 这些记录的属性在下表中列出：

| 属性 | Description |
|:--|:--|
| `Type` | *ServiceMapComputer_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | 工作区中计算机的唯一标识符 |
| `ResourceName_s` | 工作区中计算机的唯一标识符 |
| `ComputerName_s` | 计算机 FQDN |
| `Ipv4Addresses_s` | 服务器的 IPv4 地址列表 |
| `Ipv6Addresses_s` | 服务器的 IPv6 地址列表 |
| `DnsNames_s` | DNS 名称的数组 |
| `OperatingSystemFamily_s` | Windows 或 Linux |
| `OperatingSystemFullName_s` | 操作系统的全名  |
| `Bitness_s` | 计算机的位数（32 位或 64 位）  |
| `PhysicalMemory_d` | 物理内存（以 MB 为单位） |
| `Cpus_d` | CPU 数 |
| `CpuSpeed_d` | CPU 速度（以 MHz 为单位）|
| `VirtualizationState_s` | *未知*、*物理*、*虚拟*、*虚拟机监控程序* |
| `VirtualMachineType_s` | *hyperv*、*vmware* 等等 |
| `VirtualMachineNativeMachineId_g` | 由虚拟机监控程序分配的 VM ID |
| `VirtualMachineName_s` | VM 的名称 |
| `BootTime_t` | 引导时间 |

### <a name="servicemapprocess_cl-type-records"></a>ServiceMapProcess_CL 类型记录

类型为 *ServiceMapProcess_CL* 的记录包含具有服务映射代理的服务器上 TCP 连接进程的清单数据。 这些记录的属性在下表中列出：

| 属性 | Description |
|:--|:--|
| `Type` | *ServiceMapProcess_CL* |
| `SourceSystem` | *OpsManager* |
| `ResourceId` | 工作区中进程的唯一标识符 |
| `ResourceName_s` | 进程在运行它的计算机中的唯一标识符|
| `MachineResourceName_s` | 计算机的资源名称 |
| `ExecutableName_s` | 进程可执行文件的名称 |
| `StartTime_t` | 进程池启动时间 |
| `FirstPid_d` | 进程池中的第一个 PID |
| `Description_s` | 进程说明 |
| `CompanyName_s` | 公司名称 |
| `InternalName_s` | 内部名称 |
| `ProductName_s` | 产品名称 |
| `ProductVersion_s` | 产品版本 |
| `FileVersion_s` | 文件版本 |
| `CommandLine_s` | 命令行 |
| `ExecutablePath _s` | 可执行文件的路径 |
| `WorkingDirectory_s` | 工作目录 |
| `UserName` | 执行进程所用的帐户 |
| `UserDomain` | 执行进程所在的域 |

## <a name="sample-log-searches"></a>示例日志搜索

### <a name="list-all-known-machines"></a>列出所有已知计算机

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有托管计算机的物理内存容量。

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project PhysicalMemory_d, ComputerName_s

### <a name="list-computer-name-dns-ip-and-os"></a>列出计算机名称、DNS、IP 和 OS。

ServiceMapComputer_CL | summarize arg_max(TimeGenerated, *) by ResourceId | project ComputerName_s, OperatingSystemFullName_s, DnsNames_s, Ipv4Addresses_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令行中查找带有“sql”的所有进程

ServiceMapProcess_CL | where CommandLine_s contains_cs "sql" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>按资源名称查找计算机（最新记录）

search in (ServiceMapComputer_CL) "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>按 IP 地址查找计算机（最新记录）

search in (ServiceMapComputer_CL) "10.229.243.232" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-known-processes-on-a-specified-machine"></a>列出指定计算机上的所有已知进程

ServiceMapProcess_CL | where MachineResourceName_s == "m-559dbcd8-3130-454d-8d1d-f624e57961bc" | summarize arg_max(TimeGenerated, *) by ResourceId

### <a name="list-all-computers-running-sql"></a>列出所有运行 SQL 的计算机

ServiceMapComputer_CL | where ResourceName_s in ((search in (ServiceMapProcess_CL) "\*sql\*" | distinct MachineResourceName_s)) | distinct ComputerName_s

### <a name="list-all-unique-product-versions-of-curl-in-my-datacenter"></a>在我的数据中心列出 curl 的所有唯一产品版本

ServiceMapProcess_CL | where ExecutableName_s == "curl" | distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>创建由运行 CentOS 的所有计算机组成的计算机组

ServiceMapComputer_CL | where OperatingSystemFullName_s contains_cs "CentOS" | distinct ComputerName_s

### <a name="summarize-the-outbound-connections-from-a-group-of-machines"></a>汇总一组计算机的出站连接

```
// the machines of interest
let machines = datatable(m: string) ["m-82412a7a-6a32-45a9-a8d6-538354224a25"];
// map of ip to monitored machine in the environment
let ips=materialize(ServiceMapComputer_CL
| summarize ips=makeset(todynamic(Ipv4Addresses_s)) by MonitoredMachine=ResourceName_s
| mvexpand ips to typeof(string));
// all connections to/from the machines of interest
let out=materialize(VMConnection
| where Machine in (machines)
| summarize arg_max(TimeGenerated, *) by ConnectionId);
// connections to localhost augmented with RemoteMachine
let local=out
| where RemoteIp startswith "127."
| project ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=Machine;
// connections not to localhost augmented with RemoteMachine
let remote=materialize(out
| where RemoteIp !startswith "127."
| join kind=leftouter (ips) on $left.RemoteIp == $right.ips
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine=MonitoredMachine);
// the remote machines to/from which we have connections
let remoteMachines = remote | summarize by RemoteMachine;
// all augmented connections
(local)
| union (remote)
//Take all outbound records but only inbound records that come from either //unmonitored machines or monitored machines not in the set for which we are computing dependencies.
| where Direction == 'outbound' or (Direction == 'inbound' and RemoteMachine !in (machines))
| summarize by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol, RemoteIp, RemoteMachine
// identify the remote port
| extend RemotePort=iff(Direction == 'outbound', DestinationPort, 0)
// construct the join key we'll use to find a matching port
| extend JoinKey=strcat_delim(':', RemoteMachine, RemoteIp, RemotePort, Protocol)
// find a matching port
| join kind=leftouter (VMBoundPort 
| where Machine in (remoteMachines) 
| summarize arg_max(TimeGenerated, *) by PortId 
| extend JoinKey=strcat_delim(':', Machine, Ip, Port, Protocol)) on JoinKey
// aggregate the remote information
| summarize Remote=makeset(iff(isempty(RemoteMachine), todynamic('{}'), pack('Machine', RemoteMachine, 'Process', Process1, 'ProcessName', ProcessName1))) by ConnectionId, Direction, Machine, Process, ProcessName, SourceIp, DestinationIp, DestinationPort, Protocol
```

## <a name="rest-api"></a>REST API

“服务映射”中的所有服务器、进程和依赖项数据均可通过[服务映射 REST API](https://docs.microsoft.com/rest/api/servicemap/) 获取。

## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据

Microsoft 通过使用服务映射服务，自动收集使用情况和性能数据。 Microsoft 使用此数据提供和改进服务映射服务的质量、安全性和完整性。 数据包括有关软件配置的信息（如操作系统和版本）、IP 地址、DNS 名称和工作站名称，以便提供准确高效的故障排除功能。 Microsoft 不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。

## <a name="next-steps"></a>后续步骤

详细了解 Log Analytics 中的[日志搜索](../../azure-monitor/log-query/log-query-overview.md)，以检索服务映射收集的数据。

## <a name="troubleshooting"></a>故障排除

如果安装或运行服务映射时遇到任何问题，可通过本部分内容获得帮助。 如果仍然无法解决问题，请联系 Microsoft 支持部门。

### <a name="dependency-agent-installation-problems"></a>Dependency Agent 安装问题

#### <a name="installer-prompts-for-a-reboot"></a>安装程序提示重新启动
依赖关系代理*通常*不需要在安装或删除时重新启动。 在极少数的某些情况下，Windows Server 需要重启才能继续安装。 这种情况发生在依赖项，通常C++是由于锁定了文件而需要重新启动。

#### <a name="message-unable-to-install-dependency-agent-visual-studio-runtime-libraries-failed-to-install-code--code_number-appears"></a>将显示“无法安装 Dependency Agent: Visual Studio 运行时库安装失败 (code = [code_number])”消息

Microsoft Dependency Agent 基于 Microsoft Visual Studio 运行时库。 如果安装库时出现问题，将收到一条消息。 

运行时库安装程序在 %LOCALAPPDATA%\temp 文件夹中创建日志。 文件是 `dd_vcredist_arch_yyyymmddhhmmss.log`的 *，其中，* `x86` 或 `amd64`， *yyyymmddhhmmss*是创建日志时的日期和时间（24小时制）。 该日志提供有关阻止安装的问题的详细信息。

首先安装[最新的运行时库](https://support.microsoft.com/help/2977003/the-latest-supported-visual-c-downloads)可能会很有用。

下表列出了代码号和建议的解决方法。

| 代码 | Description | 分辨率 |
|:--|:--|:--|
| 0x17 | 库安装程序需要尚未安装的 Windows 更新。 | 查看最新的库安装程序日志。<br><br>如果对 `Windows8.1-KB2999226-x64.msu` 的引用后跟一行 `Error 0x80240017: Failed to execute MSU package,` 则没有安装 KB2999226 的先决条件。 按照[Windows 的通用 C 运行时中](https://support.microsoft.com/kb/2999226)的先决条件部分中的说明进行操作。 可能需要运行 Windows 更新并重新启动多次，才能安装好必备组件。<br><br>再次运行 Microsoft Dependency Agent 安装程序。 |

### <a name="post-installation-issues"></a>安装后的问题

#### <a name="server-doesnt-appear-in-service-map"></a>服务映射中不显示服务器

如果依赖关系代理安装已成功，但在服务映射解决方案中看不到计算机：
* Dependency Agent 是否已安装成功？ 可通过检查是否已安装并运行服务来验证这一点。<br><br>
**Windows**：查找名为 " **Microsoft 依赖关系代理**" 的服务。
**Linux**：查找正在运行的进程**microsoft 依赖关系代理**。

* 你是否处于[Log Analytics 免费层](https://azure.microsoft.com/pricing/details/monitor/)？ 免费计划最多允许五个独特的服务映射机。 任何后续计算机不会出现在服务映射中，即使之前的五个不再发送数据。

* 服务器是否发送日志和性能数据来 Azure Monitor 日志？ 请参阅 Azure Monitor\Logs 并为你的计算机运行以下查询： 

    ```kusto
    Usage | where Computer == "admdemo-appsvr" | summarize sum(Quantity), any(QuantityUnit) by DataType
    ```

结果中是否有多种不同的事件？ 是否为最新数据？ 如果是这样，则 Log Analytics 代理正在正常运行，并且与工作区通信。 如果没有，请检查计算机上的代理：[用于 Windows 的 Log Analytics 代理故障排除](../platform/agent-windows-troubleshoot.md)或[用于 Linux 的 Log Analytics 代理故障排除](../platform/agent-linux-troubleshoot.md)。

#### <a name="server-appears-in-service-map-but-has-no-processes"></a>服务器会在服务映射中显示，但没有任何进程

如果你在服务映射中看到你的计算机，但它没有进程或连接数据，则表明依赖关系代理已安装并正在运行，但内核驱动程序未加载。 

检查 `C:\Program Files\Microsoft Dependency Agent\logs\wrapper.log file` （Windows）或 `/var/opt/microsoft/dependency-agent/log/service.log file` （Linux）。 文件的最后几行应指出为何未加载内核。 例如，如果更新内核，则内核在 Linux 上可能不受支持。

## <a name="feedback"></a>反馈

是否有任何关于服务映射或本文档的反馈？  请访问 [User Voice 页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。
