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
ms.date: 11/18/2016
ms.author: daseidma;bwren;dairwin
translationtype: Human Translation
ms.sourcegitcommit: cbed591d15daf8060f13d0e9b009d65c85d256aa
ms.openlocfilehash: c5cd77dc53c36c4ad6c41eb876e24b4077d2007c


---

# <a name="using-service-map-solution-in-operations-management-suite-oms"></a>使用 Operations Management Suite (OMS) 中的服务映射解决方案
![警报管理图标](media/oms-service-map/icon.png) 服务映射自动发现 Windows 和 Linux 系统上的应用程序组件并映射服务之间的通信。 它允许你如所想一般作为提供重要服务的互连系统查看服务器。  服务映射显示任何 TCP 连接的体系结构中服务器、进程和端口之间的连接，只需安装代理，无需任何其他配置。

本指南介绍使用服务映射的详细信息。  有关配置服务映射和载入代理的信息，请参阅[配置 Operations Management Suite (OMS) 中的服务映射解决方案](operations-management-suite-service-map-configure.md)


## <a name="use-cases-make-your-it-processes-dependency-aware"></a>用例：使你的 IT 进程依赖关系具有感知

### <a name="discovery"></a>发现
服务映射自动在服务器、进程和第三方服务上生成依赖关系的常见引用映射。  它可发现并映射所有 TCP 依赖关系，从而将意外连接、所依赖的远程第三方系统和依赖关系标识到网络的传统深色区域（如 DNS 和 AD）。  服务映射可发现托管系统尝试进行的失败网络连接，帮助标识潜在服务器配置错误、服务中断和网络问题。

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

可在映射中扩展计算机，以在选定时间范围内显示具有有效网络连接的运行中的进程。  扩展具有服务映射代理的远程计算机以显示进程详细信息时，仅显示与焦点计算机通信的进程。  连接到焦点计算机的无代理前端计算机计数指示在它们所连接到的进程左侧。  如果焦点计算机无需代理即可连接到后端计算机，该后端将使用映射中的一个节点表示，可扩展该节点以显示焦点计算机要通信的单独端口和服务。

默认情况下，“服务映射”映射显示过去 10 分钟的依赖关系信息。  使用左上角的时间控件，可以在映射中查询历史时间范围（最多一小时），显示依赖关系在过去（例如发生事件期间或发生更改之前）的出现形式。    服务映射数据在付费工作区中存储 30 天，在免费工作区中存储 7 天。

![具有选定计算机属性的计算机映射](media/oms-service-map/machine-map.png)

## <a name="failed-connections"></a>失败的连接数
对于进程和计算机，失败的连接显示在“服务映射”映射中，如果客户端系统无法访问进程或端口，会显示一条红色虚线。  如果已部署服务映射代理的任何系统正尝试失败的连接，将从该系统报告失败的连接。  服务映射通过观察无法建立连接的 TCP 套接字测量失败的连接。  这可能是因为防火墙、客户端或服务器中的配置错误或远程服务不可用。

![失败的连接](media/oms-service-map/failed-connections.png)

了解失败的连接可帮助进行疑难解答、迁移验证、安全分析和了解总体体系结构。  有时失败的连接无害，但它们通常直接指向问题，例如故障转移环境突然无法访问或两个应用程序层在进行云迁移后无法通信。

## <a name="computer-and-process-properties"></a>计算机和进程属性
导航“服务映射”映射时，可选择计算机和进程获取有关其属性的其他上下文。  计算机提供有关 DNS 名称、IPv4 地址、CPU 和内存容量、VM 类型、操作系统版本、上次重启时间及其 OMS 和服务映射代理的 ID 的信息。

![计算机属性](media/oms-service-map/machine-properties.png)

从操作系统元数据中收集有关运行中的进程的进程详细信息，包括进程名称、进程说明、用户名和域（在 Windows 上）、公司名称、产品名称、产品版本、工作目录、命令行和进程启动时间。

![进程属性](media/oms-service-map/process-properties.png)

“进程摘要”面板提供有关该进程的连接性的其他信息，包括其绑定端口，入站和出站连接以及失败的连接。

![进程摘要](media/oms-service-map/process-summary.png)

## <a name="computer-summary"></a>计算机摘要
“计算机摘要”面板包括服务器的操作系统、依赖关系计数以及其他 OMS 解决方案（包括性能指标、更改跟踪、安全、更新等）的各种数据的概述。

![计算机摘要](media/oms-service-map/machine-summary.png)

## <a name="oms-change-tracking-integration"></a>OMS 更改跟踪集成
服务映射将与更改跟踪自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

“计算机更改跟踪”面板显示所有更改（最新更改列在顶部）的列表，以及深入了解日志搜索以获取其他详细信息的链接。
![“计算机更改跟踪”面板](media/oms-service-map/change-tracking.png)

下面是在选择“在 Log Analytics 中显示”后的配置更改事件的深入视图。
![配置更改事件](media/oms-service-map/configuration-change-event.png)


## <a name="oms-performance-integration"></a>OMS 性能集成
计算机性能面板将显示所选服务器的标准性能指标。  指标包括 CPU 利用率、内存利用率、发送和接收的网络字节数，以及发送和接收网络字节数最多的进程的列表。
![“计算机更改跟踪”面板](media/oms-service-map/machine-performance.png)


## <a name="oms-security-integration"></a>OMS 安全集成
服务映射将与安全和审核自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

计算机安全面板显示所选服务器的 OMS 安全和审核解决方案的数据。  面板将列出在所选时间范围内服务器的任何未解决的安全问题摘要。  单击任意安全问题将进入“日志搜索”，可深入了解有关安全问题的详细信息。
![“计算机更改跟踪”面板](media/oms-service-map/machine-security.png)


## <a name="oms-updates-integration"></a>OMS 更新集成
服务映射将与更新管理自动集成，前提是这两个解决方案已启用并在 OMS 工作区中配置。

计算机更新面板显示所选服务器的 OMS 更新管理解决方案的数据。  面板将列出在所选时间范围内服务器的任何缺少更新的摘要。
![“计算机更改跟踪”面板](media/oms-service-map/machine-updates.png)


## <a name="oms-alert-integration"></a>OMS 警报集成
服务映射与 OMS 警报集成，可显示所选时间范围内所选服务器触发的警报。  如果当前有警报，服务器将显示一个图标，且计算机警报面板将列出警报

![计算机警报面板](media/oms-service-map/machine-alerts.png)

请注意，若要使服务映射能够显示相关警报，必须创建警报规则，以便对特定计算机触发。  创建相应的警报：
- 包含以下子句，以按计算机进行分组：“by Computer interval 1minute”
- 根据公制度量值选择警报

![警报配置](media/oms-service-map/alert-configuration.png)


## <a name="log-analytics-records"></a>Log Analytics 记录
服务映射的计算机和进程清单数据可在 Log Analytics 中[搜索](../log-analytics/log-analytics-log-searches.md)。  这可应用于包括迁移计划、容量分析、发现和临时性能疑难解答在内的方案。

除了在唯一进程或计算机启动或载入服务映射时生成的记录外，还针对每个计算机和进程每小时生成一个记录。  这些记录的属性在下表中列出。

包含内部生成的可用于标识唯一进程和计算机的属性：

- PersistentKey_s 由进程配置唯一定义，例如命令行和用户 ID。  它特定于给定计算机，但可以在计算机之间重复。
- ProcessId_s 和 ComputerId_s 在服务映射模型中全局唯一。



### <a name="servicemapcomputercl-records"></a>ServiceMapComputer_CL 记录
类型为 **ServiceMapComputer_CL** 的记录包含具有服务映射代理的服务器的清单数据。  这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapComputer_CL* |
| SourceSystem | *OpsManager* |
| ComputerName_s | Windows 或 Linux 计算机名称 |
| CPUSpeed_d | CPU 速度（以 MHz 为单位） |
| DnsNames_s | 此计算机的所有 DNS 名称的列表 |
| IPv4s_s | 此计算机使用的所有 IPv4 地址的列表 |
| IPv6s_s | 此计算机使用的所有 IPv6 地址的列表。  （服务映射标识 IPv6 地址，但不会发现 IPv6 依赖关系。） |
| Is64Bit_b | true 或 false，取决于操作系统类型 |
| MachineId_s | OMS 工作区上唯一的内部 GUID  |
| OperatingSystemFamily_s | Windows 或 Linux |
| OperatingSystemVersion_s | 操作系统版本的长字符串 |
| TimeGenerated | 创建记录的日期和时间。 |
| TotalCPUs_d | CPU 内核数 |
| TotalPhysicalMemory_d | 内存容量（以 MB 为单位） |
| VirtualMachine_b | true 或 false，取决于操作系统是否为 VM 来宾 |
| VirtualMachineID_g | Hyper-V VM ID |
| VirtualMachineName_g | Hyper-V VM 名称 |
| VirtualMachineType_s | Hyperv、Vmware、Xen、Kvm、Ldom、Lpar、Virtualpc |


### <a name="servicemapprocesscl-type-records"></a>ServiceMapProcess_CL 类型记录
类型为 **ServiceMapProcess_CL** 的记录包含具有服务映射代理的服务器上 TCP 连接进程的清单数据。  这些记录的属性在下表中列出：

| 属性 | 说明 |
|:--|:--|
| 类型 | *ServiceMapProcess_CL* |
| SourceSystem | *OpsManager* |
| CommandLine_s | 进程的完整命令行 |
| CompanyName_s | 公司名称（来自 Windows PE 或 Linux RPM） |
| Description_s | 进程长说明（来自 Windows PE 或 Linux RPM） |
| FileVersion_s | 可执行文件版本（来自 Windows PE，仅限 Windows） |
| FirstPid_d | 操作系统进程 ID |
| InternalName_s | 可执行文件的内部名称（来自 Windows PE，仅限 Windows） |
| MachineId_s | OMS 工作区上唯一的内部 GUID  |
| Name_s | 进程可执行文件名称 |
| Path_s | 进程可执行文件的文件系统路径 |
| PersistentKey_s | 此计算机内唯一的内部 GUID |
| PoolId_d | 用于根据相似命令行聚合进程的内部 ID。 |
| ProcessId_s | OMS 工作区上唯一的内部 GUID  |
| ProductName_s | 产品名称字符串（来自 Windows PE 或 Linux RPM） |
| ProductVersion_s | 产品版本字符串（来自 Windows PE 或 Linux RPM） |
| StartTime_t | 本地计算机时钟上的进程启动时间 |
| TimeGenerated | 创建记录的日期和时间。 |
| UserDomain_s | 进程所有者的域（仅限 Windows） |
| UserName_s | 进程所有者的名称（仅限 Windows） |
| WorkingDirectory_s | 进程工作目录 |


## <a name="sample-log-searches"></a>示例日志搜索

### <a name="list-the-physical-memory-capacity-of-all-managed-computers"></a>列出所有托管计算机的物理内存容量。
Type=ServiceMapComputer_CL | select TotalPhysicalMemory_d, ComputerName_s | Dedup ComputerName_s

### <a name="list-computer-name-dns-ip-and-os-version"></a>列出计算机名称、DNS、IP 和操作系统版本。
Type=ServiceMapComputer_CL | select ComputerName_s, OperatingSystemVersion_s, DnsNames_s, IPv4s_s  | dedup ComputerName_s

### <a name="find-all-processes-with-sql-in-the-command-line"></a>在命令行中查找带有“sql”的所有进程
Type=ServiceMapProcess_CL CommandLine_s = \*sql\* | dedup ProcessId_s

### <a name="after-viewing-event-data-for-given-process-use-its-machine-id-to-retrieve-the-computers-name"></a>查看给定进程的事件数据之后，使用其计算机 ID 检索计算机名称
Type=ServiceMapComputer_CL "m!m-9bb187fa-e522-5f73-66d2-211164dc4e2b" | Distinct ComputerName_s

### <a name="list-all-computers-running-sql"></a>列出所有运行 SQL 的计算机
Type=ServiceMapComputer_CL MachineId_s IN {Type=ServiceMapProcess_CL \*sql\* | Distinct MachineId_s} | Distinct ComputerName_s

### <a name="list-of-all-unique-product-versions-of-curl-in-my-datacenter"></a>在我的数据中心中列出 curl 的所有唯一产品版本
Type=ServiceMapProcess_CL Name_s=curl | Distinct ProductVersion_s

### <a name="create-a-computer-group-of-all-computers-running-centos"></a>创建运行 CentOS 的所有计算机的计算机组
Type=ServiceMapComputer_CL OperatingSystemVersion_s = "CentOS" | Distinct ComputerName_s



## <a name="diagnostic-and-usage-data"></a>诊断和使用情况数据
Microsoft 通过使用服务映射服务，自动收集使用情况和性能数据。 Microsoft 使用此数据提供和改进服务映射服务的质量、安全性和完整性。 数据包括有关你的软件配置的信息（如操作系统和版本），还包括 IP 地址、DNS 名称和工作站名称，以便提供准确高效的疑难解答功能。 我们不收集姓名、地址或其他联系信息。

有关数据收集和使用的详细信息，请参阅 [Microsoft Online Services 隐私声明](hhttps://go.microsoft.com/fwlink/?LinkId=512132)。



## <a name="next-steps"></a>后续步骤
- 了解有关 Log Analytics 中的[日志搜索](../log-analytics/log-analytics-log-searches.md)的详细信息，以检索服务映射收集的数据。



<!--HONumber=Nov16_HO4-->


