---
title: "Operations Management Suite (OMS) 中的服务映射 | Microsoft Docs"
description: "服务映射是 Operations Management Suite (OMS) 解决方案，可自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。  本文提供了有关在环境中部署服务映射并在各种方案中使用它的详细信息。"
services: operations-management-suite
documentationcenter: 
author: daveirwin1
manager: jwhit
editor: tysonn
ms.assetid: 3ceb84cc-32d7-4a7a-a916-8858ef70c0bd
ms.service: operations-management-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/22/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: d616a8574d2087be66bc10dfdf3bf6f5a14c11fd
ms.lasthandoff: 03/09/2017


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>使用 Operations Management Suite (OMS) 中的服务映射解决方案
服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你如所想一般作为提供重要服务的互连系统查看服务器。  服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。

本指南介绍使用服务映射的详细信息。  有关配置服务映射和载入代理的信息，请参阅[配置 Operations Management Suite (OMS) 中的服务映射解决方案](operations-management-suite-service-map-configure.md)


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>用例：使 IT 进程感知依赖关系

### <a name="discovery"></a>发现
服务映射自动在服务器、进程和第三方服务上生成依赖关系的常见引用映射。  它可发现并映射所有 TCP 依赖关系，从而将意外连接、依赖的远程第三方系统和依赖关系标识到网络的传统深色区域（如 Active Directory）。  服务映射可发现托管系统尝试进行的失败网络连接，帮助标识潜在服务器配置错误、服务中断和网络问题。

### <a name="incident-management"></a>事件管理
服务映射可显示系统的连接方式以及相互的影响，无需再猜测问题隔离。  除了失败的连接，已连接客户端的相关信息还可帮助标识配置错误的负载均衡器、关键服务上的意外或多余负载以及与生产系统通信的开发人员计算机等恶意客户端。  将工作流与 OMS 更改跟踪集成后，还可以查看后端计算机或服务上的更改事件是否能解释事件的根本原因。

### <a name="migration-assurance"></a>迁移保障
服务映射支持有效计划、加快和验证 Azure 迁移，确保迁移所有内容，不会发生意外中断。  你可以发现所有需要一起迁移的互相依赖的系统、访问系统配置和容量并标识运行的系统仍然为用户提供服务还是等待解除授权而非迁移。  移动完成后，你可以检查客户端负载和标识，验证测试系统和客户是否正在连接。  如果子网计划和防火墙定义有问题，服务映射映射中的失败连接将指向需要连接的系统。

### <a name="business-continuity"></a>业务连续性
如果使用 Azure Site Recovery 并需要帮助定义应用程序环境的恢复顺序，服务映射可自动显示系统互相依赖的方式，以便确保恢复计划可靠。  通过选择关键服务器并查看其客户端，你可以标识应仅在关键服务器还原并可用后恢复的前端系统。  相反，通过查看关键服务器的后端依赖关系，标识必须在焦点系统还原之前恢复的系统。

### <a name="patch-management"></a>修补程序管理
服务映射通过显示依赖于你的服务的其他团队和服务器，增强对 OMS 系统更新评估的使用，以便可以在对系统进行修补之前提前通知他们。  服务映射还通过显示你的服务在完成修补和重启后是否可用并已正确连接，来增强 OMS 中的修补程序管理。


## <a name="mapping-overview"></a>映射概述
服务映射代理收集有关已安装 TCP 的服务器上所有 TCP 连接的进程的信息，以及有关每个进程的入站和出站连接的详细信息。  使用服务映射解决方案左侧的计算机列表，可选择具有服务映射代理的计算机在选定时间范围内使其依赖关系可视化。  计算机依赖关系映射侧重于特定计算机，并显示属于该计算机的直接 TCP 客户端或服务器的所有计算机。

![服务映射概述](media/oms-service-map/service-map-overview.png)

可在映射中扩展计算机，以在选定时间范围内显示具有有效网络连接的运行中的进程。  扩展具有服务映射代理的远程计算机以显示进程详细信息时，仅显示与焦点计算机通信的进程。  连接到焦点计算机的无代理前端计算机计数指示在它们所连接到的进程左侧。  如果焦点计算机连接到无代理后端计算机，则服务器端口组中包含该后端服务器以及与同一端口号相连的其他连接。

默认情况下，“服务映射”映射显示过去 10 分钟的依赖关系信息。  使用左上角的时间控件，可以在映射中查询历史时间范围（最多一小时），显示依赖关系在过去（例如发生事件期间或发生更改之前）的出现形式。    服务映射数据在付费工作区中存储 30 天，在免费工作区中存储 7 天。

## <a name="status-badges-and-border-coloring"></a>状态徽章和边框着色
映射中每个服务器的底部可以是状态徽章的列表，用于表示有关服务器的状态信息。  徽章指示其中一个 OMS 解决方案集成中有服务器的一些相关信息。  单击徽章将直接在右侧面板中转到状态的详细信息。  当前可用性状态徽章包括警报、更改、安全性和更新。

根据状态徽章的严重性，计算机节点边框的颜色可以是红色（严重）、黄色（警告）或蓝色（信息）。  该颜色表示任何状态徽章的最严重状态。  灰色边框指示没有当前状态指示器的节点。

![状态徽章](media/oms-service-map/status-badges.png)

## <a name="role-icons"></a>角色图标
某些进程在计算机上充当特定角色：Web 服务器、应用程序服务器、数据库等。服务映射可批注进程和计算机设备，包含的角色图标可帮助以显眼的方式标识进程或服务器扮演的角色。

| 角色图标 | 说明 |
|:--|:--|
| ![Web 服务器](media/oms-service-map/role-web-server.png) | Web 服务器 |
| ![应用服务器](media/oms-service-map/role-application-server.png) | 应用程序服务器 |
| ![数据库服务器](media/oms-service-map/role-database.png) | 数据库服务器 |
| ![LDAP 服务器](media/oms-service-map/role-ldap.png) | LDAP 服务器 |
| ![SMB 服务器](media/oms-service-map/role-smb.png) | SMB 服务器 |

![角色图标](media/oms-service-map/role-icons.png)


## <a name="failed-connections"></a>失败的连接
对于进程和计算机，失败的连接显示在“服务映射”映射中，如果客户端系统无法访问进程或端口，会显示一条红色虚线。  如果已部署服务映射代理的任何系统正尝试失败的连接，将从该系统报告失败的连接。  服务映射通过观察无法建立连接的 TCP 套接字测量失败的连接。  这可能是因为防火墙、客户端或服务器中的配置错误或远程服务不可用。

![失败的连接](media/oms-service-map/failed-connections.png)

了解失败的连接可帮助进行疑难解答、迁移验证、安全分析和了解总体体系结构。  有时失败的连接无害，但它们通常直接指向问题，例如故障转移环境突然无法访问或两个应用程序层在进行云迁移后无法通信。

## <a name="client-groups"></a>客户端组
客户端组是映射上的各个框，表示不具备依赖关系代理的客户端计算机。  单个客户端组表示单个进程的客户端。

![客户端组](media/oms-service-map/client-groups.png)

若要查看客户端组中的服务器 IP 地址，请选择该组。  “属性”面板中将列出该组的内容。

![客户端组属性](media/oms-service-map/client-group-properties.png)

## <a name="server-port-groups"></a>服务器端口组
服务器端口组是表示服务器（不具备依赖关系代理）上的服务器端口的各个框。  框中将列出服务器端口，以及连接到该端口的服务器计数。  展开此框可查看各个服务器和连接。  如果框中仅有一个服务器，则会列出服务器名称或 IP 地址。

![服务器端口组](media/oms-service-map/server-port-groups.png)

## <a name="context-menu"></a>上下文菜单
单击任何服务器右上部的三个点将显示该服务器的上下文菜单。

![失败的连接](media/oms-service-map/context-menu.png)

### <a name="load-server-map"></a>加载服务器映射
加载服务器映射将导航到新映射，其中所选服务器作为新的焦点计算机。

### <a name="showhide-self-links"></a>显示/隐藏自链接
显示自链接将重绘包含任何自链接的服务器节点，这些自链接是表示服务器中的进程的开始和结束的 TCP 连接。  如果显示了自链接，则该菜单将更改为“隐藏自链接”，允许用户切换自链接的绘制。

## <a name="computer-summary"></a>计算机摘要
“计算机摘要”面板包括服务器的操作系统、依赖关系计数以及其他 OMS 解决方案（包括性能指标、更改跟踪、安全、更新等）的各种数据的概述。

![计算机摘要](media/oms-service-map/machine-summary.png)

## <a name="computer-and-process-properties"></a>计算机和进程属性
导航“服务映射”映射时，可选择计算机和进程获取有关其属性的其他上下文。  计算机提供有关 DNS 名称、IPv4 地址、CPU 和内存容量、VM 类型、操作系统版本、上次重启时间及其 OMS 和服务映射代理的 ID 的信息。

![计算机属性](media/oms-service-map/machine-properties.png)

从操作系统元数据中收集有关运行中的进程的进程详细信息，包括进程名称、进程说明、用户名和域（在 Windows 上）、公司名称、产品名称、产品版本、工作目录、命令行和进程启动时间。

![进程属性](media/oms-service-map/process-properties.png)

“进程摘要”面板提供有关该进程的连接性的其他信息，包括其绑定端口，入站和出站连接以及失败的连接。

![进程摘要](media/oms-service-map/process-summary.png)

## <a name="oms-alerts-integration"></a>OMS 警报集成
服务映射与 OMS 警报集成，可显示所选时间范围内所选服务器触发的警报。  如果当前有警报，服务器将显示一个图标，且计算机警报面板将列出警报

![计算机警报面板](media/oms-service-map/machine-alerts.png)

请注意，若要使服务映射能够显示相关警报，必须创建警报规则，以便对特定计算机触发。  创建相应的警报：
- 包含以下子句，以按计算机进行分组：“by Computer interval 1minute”
- 根据公制度量值选择警报

![警报配置](media/oms-service-map/alert-configuration.png)


## <a name="oms-log-events-integration"></a>OMS 日志事件集成
服务映射与日志搜索集成，可显示所选时间范围内所选服务器的所有可用的日志事件。  你可以单击事件计数列表中的任意行，跳转到日志搜索并查看单独的日志事件。

![日志事件](media/oms-service-map/log-events.png)

## <a name="oms-change-tracking-integration"></a>OMS 更改跟踪集成
服务映射将与更改跟踪自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

“计算机更改跟踪”面板显示所有更改（最新更改列在顶部）的列表，以及深入了解日志搜索以获取其他详细信息的链接。
![“计算机更改跟踪”面板](media/oms-service-map/change-tracking.png)

下面是在选择“在 Log Analytics 中显示”后的配置更改事件的深入视图。
![配置更改事件](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>OMS 性能集成
计算机性能面板将显示所选服务器的标准性能指标。  指标包括 CPU 利用率、内存利用率、发送和接收的网络字节数，以及发送和接收网络字节数最多的进程的列表。  注意，要获取网络性能数据，还必须在 OMS 中启用 Wire Data 2.0 解决方案。
![“计算机更改跟踪”面板](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>OMS 安全集成
服务映射将与安全和审核自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

计算机安全面板显示所选服务器的 OMS 安全和审核解决方案的数据。  面板将列出在所选时间范围内服务器的任何未解决的安全问题摘要。  单击任意安全问题将进入“日志搜索”，可深入了解有关安全问题的详细信息。
![“计算机更改跟踪”面板](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>OMS 更新集成
服务映射将与更新管理自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

计算机更新面板显示所选服务器的 OMS 更新管理解决方案的数据。  面板将列出在所选时间范围内服务器的任何缺少更新的摘要。
![“计算机更改跟踪”面板](media/oms-service-map/machine-updates.png)

## <a name="log-analytics-records"></a>Log Analytics 记录
服务映射的计算机和进程清单数据可在 Log Analytics 中[搜索](../log-analytics/log-analytics-log-searches.md)。  这可应用于包括迁移计划、容量分析、发现和临时性能疑难解答在内的方案。

除了在进程或计算机启动或载入服务映射时生成的记录外，还针对每个唯一计算机和进程每小时生成一个记录。  这些记录的属性在下表中列出。  ServiceMapComputer_CL 事件中的字段和值将映射到 ServiceMap ARM API 中计算机资源的字段。  ServiceMapProcess_CL 事件中的字段和值将映射到 ServiceMap ARM API 中进程资源的字段。  ResourceName_s 字段与相应的 ARM 资源中的名称字段匹配。 请注意：随着服务映射功能增加，这些字段可能会更改。


包含内部生成的可用于标识唯一进程和计算机的属性：

- 计算机 - 使用 ResourceId 或 ResourceName_s 唯一标识 OMS 工作区中的计算机。
- 进程 - 使用 ResourceId 唯一标识 OMS 工作区中的进程。 ResourceName_s 在运行该进程的计算机 (MachineResourceName_s) 的上下文中唯一 

由于在给定的时间范围内给定的进程和计算机可能存在多个记录，因此针对同一个计算机或进程的查询可能返回多个记录。 若要仅包括最新记录，请将“|dedup ResourceId”添加到查询。

### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 记录
类型为 **ServiceMapComputer_CL** 的记录包含具有服务映射代理的服务器的清单数据。  这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作区中的计算机的唯一标识符 |
| ResourceName_s | 工作区中的计算机的唯一标识符 |
| ComputerName_s | 计算机 FQDN |
| Ipv4Addresses_s | 服务器的 IPv4 地址列表 |
| Ipv6Addresses_s | 服务器的 IPv6 地址列表 |
| DnsNames_s | DNS 名称的数组 |
| OperatingSystemFamily_s | windows 或 linux |
| OperatingSystemFullName_s | 操作系统全名  |
| Bitness_s | 计算机（32 位）或（64 位）的位数 |
| PhysicalMemory_d | 物理内存（以 MB 为单位） |
| Cpus_d | cpu 数 |
| CpuSpeed_d | cpu 速度（以 MHz 为单位）|
| VirtualizationState_s | “未知”、“物理”、“虚拟”、“虚拟机监控程序” |
| VirtualMachineType_s | “hyperv”、“vmware”，等等。 |
| VirtualMachineNativeMachineId_g | 由虚拟机监控程序分配的 VM ID |
| VirtualMachineName_s | VM 名称 |
| BootTime_t | 启动时间 |



### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 类型记录
类型为 **ServiceMapProcess_CL** 的记录包含具有服务映射代理的服务器上 TCP 连接进程的清单数据。  这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| ResourceId | 工作区中的进程的唯一标识符 |
| ResourceName_s | 进程在运行它的计算机中的唯一标识符|
| MachineResourceName_s | 计算机资源名称 |
| ExecutableName_s | 进程可执行文件名称 |
| StartTime_t | 进程池启动时间 |
| FirstPid_d | 进程池中的第一个 pid |
| Description_s | 进程说明 |
| CompanyName_s | 公司名称 |
| InternalName_s | 内部名称 |
| ProductName_s | 产品名称 |
| ProductVersion_s | 产品版本 |
| FileVersion_s | 文件版本 |
| CommandLine_s | 命令行 |
| ExecutablePath_s | 可执行文件的路径 |
| WorkingDirectory_s | 工作目录 |
| UserName | 执行进程所用的帐户 |
| UserDomain | 在其下执行进程的域 |


## <a name="sample-log-searches"></a>示例日志搜索

### <a name="list-all-known-machines"></a>列出所有已知计算机
Type=ServiceMapComputer_CL | dedup ResourceId

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有托管计算机的物理内存容量。
Type=ServiceMapComputer_CL | select PhysicalMemory_d, ComputerName_s | Dedup ResourceId

### <a name="list-computer-name-dns-ip-and-os"></a>列出计算机名称、DNS、IP 和 OS。
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemFullName_s, DnsNames_s, IPv4Addresses_s  | dedup ResourceId

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令行中查找带有“sql”的所有进程
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-resource-name"></a>按资源名称查找计算机（最新记录）
Type=ServiceMapComputer_CL "m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="find-a-machine-most-recent-record-by-ip-address"></a>按 ip 地址查找计算机（最新记录）
Type=ServiceMapComputer_CL "10.229.243.232" | dedup ResourceId

### <a name="list-all-known-processes-on-a-given-machine"></a>列出给定计算机上的所有已知进程
Type=ServiceMapProcess_CL MachineResourceName_s="m-4b9c93f9-bc37-46df-b43c-899ba829e07b" | dedup ResourceId

### <a name="list-all-computers-running-sql"></a>列出所有运行 SQL 的计算机
Type=ServiceMapComputer_CL ResourceName_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineResourceName_s} | dedup ResourceId | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>在我的数据中心中列出 curl 的所有唯一产品版本
Type=ServiceMapProcess_CL ExecutableName_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>创建运行 CentOS 的所有计算机的计算机组
Type=ServiceMapComputer_CL OperatingSystemFullName_s = \*CentOS\* | Distinct ComputerName_s


## <a name="rest-api"></a>REST API
服务映射中所有服务器、进程和依赖项数据可通过[服务映射 REST API](https://docs.microsoft.com/en-us/rest/api/servicemap/) 获取。


## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据
Microsoft 通过使用服务映射服务，自动收集使用情况和性能数据。 Microsoft 使用此数据提供和改进服务映射服务的质量、安全性和完整性。 数据包括有关你的软件配置的信息（如操作系统和版本），还包括 IP 地址、DNS 名称和工作站名称，以便提供准确高效的疑难解答功能。 我们不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](https://go.microsoft.com/fwlink/?LinkId=512132)。


## <a name="next-steps"></a>后续步骤
- 了解有关 Log Analytics 中的[日志搜索](../log-analytics/log-analytics-log-searches.md)的详细信息，以检索服务映射收集的数据。


## <a name="feedback"></a>反馈
是否有任何关于服务映射或本文档的反馈？  请访问 [User Voice 页面](https://feedback.azure.com/forums/267889-log-analytics/category/184492-service-map)，可在此处推荐功能或对现有建议投票。

