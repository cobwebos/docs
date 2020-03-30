---
title: 在 Azure（大型实例）上将 SAP HANA 迁移到 Azure 虚拟机*微软文档
description: 如何将 Azure 上的 SAP HANA（大型实例）迁移到 Azure 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: bentrin
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: bentrin
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fd1267711871b3e55f1a6229e46ae27b360322f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77617040"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 大型实例上的 SAP HANA 迁移到 Azure 虚拟机
本文介绍了可能的 Azure 大型实例部署方案，并提供规划和迁移方法，并最大限度地减少过渡停机时间

## <a name="overview"></a>概述
自 2016 年 9 月宣布 SAP HANA （HLI） Azure 大型实例以来，许多客户已使用此硬件作为其内存计算平台的服务产品。  近年来，Azure VM 大小扩展加上对 HANA 横向扩展部署的支持，已超过大多数企业客户的 ERP 数据库容量需求。  我们开始看到客户表示有兴趣将其 SAP HANA 工作负载从物理服务器迁移到 Azure VM。
本指南不是分步配置文档。 它描述了常见的部署模型，并提供规划和迁移建议。  目的是指出必要的准备注意事项，以尽量减少过渡停机时间。

## <a name="assumptions"></a>假设
本文作出以下假设：
- 唯一考虑的兴趣是同质的 HANA 数据库计算服务从 Hana 大型实例 （HLI） 迁移到 Azure VM，而无需进行重要的软件升级或修补。 这些次要更新包括使用较新的操作系统版本或 HANA 版本，明确说明相关 SAP 说明支持。 
- 所有更新/升级活动都需要在迁移之前或之后完成。  例如，SAP HANA MCOS 转换为 MDC 部署。 
- 提供最少停机时间的迁移方法是 SAP HANA 系统复制。 其他迁移方法不是本文档范围的一部分。
- 本指南适用于 HLI 的 Rev3 和 Rev4 SKU。
- HANA 部署体系结构在迁移期间主要保持不变。  也就是说，具有单个实例 DR 的系统将在目标上保持不变。
- 客户已经查看并理解目标（即将成为）体系结构的服务级别协议 （SLA）。 
- HLIs 和 VM 之间的商业术语是不同的。 客户应监控其 VM 的使用以进行成本管理。
- 客户了解 HLI 是专用的计算平台，而 VM 则在共享但隔离的基础结构上运行。
- 客户已验证目标 VM 支持您预期的体系结构。 要查看所有支持的 VM SKU 认证的 SAP HANA 部署，请参阅[SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客户已验证了设计和迁移计划。
- 计划灾难恢复 VM 以及主站点。  迁移后，客户不能将 HLI 用作在 VM 上运行的主站点的 DR 节点。
- 客户根据业务可恢复性和合规性要求将所需的备份文件复制到目标 VM。 借助 VM 可访问的备份，它允许在过渡期间进行时间点恢复。
- 对于 HSR HA，客户需要根据 SAP HANA HA 指南为[SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)和[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)设置和配置 STONITH 设备。  它不像 HLI 大小写那样预配置。
- 此迁移方法不包括具有 Optane 配置的 HLI SKU。

## <a name="deployment-scenarios"></a>部署方案
下表汇总了 HLI 客户的常见部署模型。  可以迁移到 Azure VM 的所有 HLI 方案。  为了从可用的补充 Azure 服务中获益，可能需要进行细微的体系结构更改。

| 方案 ID | HLI 方案 | 逐字迁移到 VM？ | 备注 |
| --- | --- | --- | --- |
| 1 | [具有一个 SID 的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | 是 | - |
| 2 | [带 MCOS 的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | 是 | - |
| 3 | [使用存储复制的 DR 的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 否 | Azure 虚拟平台不提供存储复制，将当前 DR 解决方案更改为 HSR 或备份/还原 |
| 4 | [使用存储复制的具有 DR（多用途）的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 否 | Azure 虚拟平台不提供存储复制，将当前 DR 解决方案更改为 HSR 或备份/还原 |
| 5 | [具有 STONITH 的 HSR，提供高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | 是 | 没有针对目标 VM 的预配置 SBD。  选择并部署 STONITH 解决方案。  可能的选项：Azure 屏蔽代理（支持两个[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker) [、SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)）SBD |
| 6 | [HA 带 HSR，DR 带存储复制](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原，以满足 DR 需求 |
| 7 | [主机自动故障转移 (1+1)](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | 是 | 将 ANF 与 Azure VM 共享存储 |
| 8 | [使用备用节点的横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | 是 | BW/4HANA，带 M128s、M416s、M416ms VM，仅用于存储 |
| 9 | [不使用备用节点的横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | 是 | BW/4HANA 带 M128、M416、M416ms VM（无论是否使用 ANF 进行存储） |
| 10 | [使用存储复制使用 DR 横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原，以满足 DR 需求 |
| 11 | [使用 HSR 使用 DR 的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [单个节点 HSR 到 DR（成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [带 HSR 的医管局和 DR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [具有 HSR 的 HA 和 DR（成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 使用 DR 横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | 是 | BW/4HANA，带M128。 M416s，M416ms VM（无论是否使用 ANF 进行存储） |


## <a name="source-hli-planning"></a>源 （HLI） 规划
在 HLI 服务器中，Microsoft 服务管理和客户都进行了用于运行 SAP HANA 数据库的计算、网络、存储和操作系统特定设置的规划。  需要进行类似的规划才能迁移到 Azure VM。

### <a name="sap-hana-housekeeping"></a>SAP HANA 内务管理 
清理数据库内容是一种很好的操作实践，这样不需要的、过时的数据或陈旧的日志不会迁移到新数据库。  内务管理通常涉及删除或存档旧数据、过期数据或非活动数据。  这些"数据卫生"操作应在非生产系统中进行测试，以在生产使用之前验证其数据缩减有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允许新 VM 和 虚拟网络进行网络连接 
在客户的 HLI 部署中，网络已基于文章[SAP HANA（大型实例）网络体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)中描述的信息进行设置。 此外，网络流量路由以"Azure 中路由"部分中概述的方式完成。
- 在将新 VM 设置为迁移目标时，如果将其放置在现有虚拟网络中，其 IP 地址范围已允许连接到 HLI，则无需进一步的连接更新。
- 如果新的 Azure VM 放置在新的 Microsoft Azure 虚拟网络中，可能位于另一个区域中，并与现有虚拟网络对等，则原始 HLI 预配中的 ExpressRoute 服务密钥和资源 ID 可用于允许访问此新虚拟虚拟网络 IP 范围。  与 Microsoft 服务管理协调，使虚拟网络能够连接到 HLI 连接。  注意：为了最小化应用程序层和数据库层之间的网络延迟，应用程序层和数据库层必须位于同一虚拟网络上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group-ppg"></a>现有应用图层可用性集、可用性区域和邻近放置组 （PPG）
当前部署模型是为了满足某些服务级别目标。  在此移动中，确保目标基础结构达到或超过设定的目标。  
更有可能的是，客户 SAP 应用程序服务器被放置在可用性集中。  如果当前部署服务级别令人满意，并且 
- 如果目标 VM 假定 HLI 逻辑名称的主机名，则更新指向 VM IP 的域名服务 （DNS） 地址解析将工作，而无需更新任何 SAP 配置文件
- 如果不使用 PPG，请确保将所有应用程序和数据库服务器放在同一个区域中，以尽量减少网络延迟。
- 如果您使用的是 PPG，请参阅本文档的部分："目标规划、可用性集、可用性区域和邻近放置组 （PPG）"。""。

### <a name="storage-replication-discontinuance-process-if-used"></a>存储复制中断过程（如果使用）
如果存储复制用作 DR 解决方案，则应在 SAP 应用程序关闭后终止（计划外）。  此外，最后一个 SAP HANA 目录、日志文件和数据备份已复制到远程 DR HLI 存储卷上。  这样做是一种预防措施，以防在物理服务器到 Azure VM 转换期间发生灾难。

### <a name="data-backups-preservation-consideration"></a>数据备份保存注意事项
在 Azure VM 上共享到 SAP HANA 后，HLI 上的所有基于快照的数据或日志备份都不容易访问或恢复，如果需要，VM 也不容易访问或恢复。 在早期过渡期间，在基于 Azure 的备份构建足够的历史记录以满足时间点恢复要求之前，我们建议在切换前几天或数周之前，除 HLI 上的快照外，还获取文件级别备份。  将这些备份复制到 Azure 存储帐户，以便由新的 SAP HANA VM 访问。
除了备份 HLI 内容外，在需要回滚时，最好能够轻松访问 SAP 环境的完整备份。

### <a name="adjusting-system-monitoring"></a>调整系统监控 
客户使用许多不同的工具来监视和发送 SAP 环境中系统的警报通知。  此项目只是适当操作的呼出，用于合并用于监视和更新警报通知收件人（如果需要）的更改。

### <a name="microsoft-operations-team-involvement"></a>微软运营团队参与 
根据现有的 HLI 实例从 Azure 门户打开票证。  创建支持票证后，支持工程师将通过电子邮件与您联系。  

### <a name="engage-microsoft-account-team"></a>参与微软客户团队
在 HLI 合同的周年续订时间附近规划迁移，以尽量减少计算资源不必要的超支。 要停用 HLI 刀片，需要协调合同终止和装置的实际关闭。

## <a name="destination-planning"></a>目的地规划
建立一个新的基础设施，取代现有的基础设施，值得思考，以确保新的增加将适合大计划的事情。  以下是思考的一些要点。

### <a name="resource-availability-in-the-target-region"></a>目标区域的资源可用性 
当前 SAP 应用程序服务器的部署区域通常与关联的 HLIs 非常接近。  但是，与可用 Azure 区域相比，HLI 提供的位置更少。  将物理 HLI 迁移到 Azure VM 时，也是"微调"所有相关服务距离以进行性能优化的好时机。  在这样做的同时，一个关键考虑是确保选定的区域拥有所有必要的资源。  例如，某些 VM 系列的可用性或 Azure 区域的可用性设置。

### <a name="virtual-network"></a>虚拟网络 
客户需要选择是在现有虚拟网络中运行新的 HANA 数据库，还是创建新的 HANA 数据库。  主要决定因素是 SAP 环境的当前网络布局。  此外，当基础结构从一个区域部署到双区部署并使用 PPG 时，它强制实施体系结构更改。 有关详细信息，请参阅有关 Azure [PPG 的文章，了解 SAP 应用程序的最佳网络延迟](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)。   

### <a name="security"></a>安全性
无论是新的 SAP HANA VM 在新的 vnet/子网上着陆，它都代表着一种新的业务关键型服务，需要保护。  应针对这种新的服务类别评估和部署符合公司信息安全策略的访问控制。

### <a name="vm-sizing-recommendation"></a>VM 大小调整建议
此迁移也是正确调整 HANA 计算引擎大小的机会。  我们可以与 HANA Studio 结合使用 HANA[系统视图](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)来了解系统资源消耗，从而允许正确调整大小以提高支出效率。

### <a name="storage"></a>存储 
存储性能是影响 SAP 应用程序用户体验的因素之一。  基于给定的 VM SKU，有最少的存储布局发布[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)。 我们建议查看这些最低规格，并对现有 HLI 系统统计信息进行比较，以确保新 HANA VM 有足够的 IO 容量和性能。

如果为新 HANA VM 及其关联的分单元配置 PPG，请提交支持票证以检查和确保存储和 VM 的同地位置。 由于备份解决方案可能需要更改，因此还应重新查看存储成本，以避免运营支出意外。

### <a name="storage-replication-for-disaster-recovery"></a>用于灾难恢复的存储复制
使用 HLI 时，存储复制作为灾难恢复的默认选项提供。 此功能不是 Azure VM 上的 SAP HANA 的默认选项。 考虑 HSR、备份/恢复或其他支持的解决方案，满足您的业务需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性集、可用性区域和邻近放置组 
为了缩短应用程序层和 SAP HANA 之间的距离，以将网络延迟降至最低，应将新的数据库 VM 和当前的 SAP 应用程序服务器置于 PPG 中。 请参阅[邻近放置组](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，了解 Azure 可用性集和可用性区域如何使用 PPG 进行 SAP 部署。
如果目标 HANA 系统的成员部署在多个 Azure 区域中，客户应清楚地了解所选区域的延迟配置文件。 SAP 系统组件的放置对于 SAP 应用程序和数据库之间的近距是最佳选择。  公域[可用性区域延迟测试工具](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)有助于简化测量。  


### <a name="backup-strategy"></a>备份策略
许多客户已经在 HLI 上使用 SAP HANA 的第三方备份解决方案。  在这种情况下，只需要配置其他受保护的 VM 和 HANA 数据库。  如果计算机在迁移后正在停用，则现在可以计划正在进行的 HLI 备份作业。
VM 上的 SAP HANA 的 Azure 备份现在通常可用。  有关以下链接的详细信息，请参阅以下链接：在 Azure VM 中[备份](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)、[还原](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)[、管理](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)SAP HANA 备份。

### <a name="dr-strategy"></a>DR 战略
如果服务级别目标适应较长的恢复时间，则对 Blob 存储进行简单备份，并就地还原或还原到新 VM 是最简单、成本最低的 DR 策略。  
与 HANA DR 通常使用 HSR 完成的大型实例平台类似;在 Azure VM 上，HSR 也是最自然和本机 SAP HANA DR 解决方案。  无论源部署是单实例部署还是群集部署，DR 区域都需要源基础结构的副本。
此 DR 副本将在主 HLI 到 VM 迁移完成后进行配置。  DR HANA 数据库将作为辅助复制站点注册到 VM 实例上的主要 SAP HANA。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 应用程序服务器连接目标更改
HSR 迁移产生了新的 HANA 数据库主机，因此应用程序层的新数据库主机名需要修改 SAP 配置文件以反映新的主机名。  如果切换是通过保留主机名的名称解析完成的，则不需要更改配置文件。

### <a name="operating-system"></a>操作系统
例如，HLI 和 VM 的操作系统映像（尽管位于同一版本级别）并不相同。 客户必须在 HLI 上验证所需的包、热修复程序、修补程序、内核和安全修补程序，才能在目标上安装相同的包。  支持使用 HSR 将旧操作系统复制到具有较新操作系统版本的 VM 上。  通过查看[SAP 注释 2763388](https://launchpad.support.sap.com/#/notes/2763388)验证特定支持的版本。

### <a name="new-sap-license-request"></a>新的 SAP 许可证请求
一个简单的呼出，以请求新的SAP许可证的新HANA系统，因为它已迁移到VM。

### <a name="service-level-agreement-sla-differences"></a>服务级别协议 （SLA） 差异
作者喜欢指出 HLI 和 Azure VM 之间的可用性 SLA 差异。  例如，群集 HLIs HA 对提供 99.99% 的可用性。 要实现相同的 SLA，必须在可用性区域中部署 VM。 [本文](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)介绍具有相关部署体系结构的可用性，以便客户可以相应地规划其目标基础结构。


## <a name="migration-strategy"></a>迁移策略
在本文中，我们仅介绍从 HLI 迁移到 Azure VM 的 HANA 系统复制方法。  取决于部署的目标存储解决方案，该过程略有不同。 下面将介绍高级步骤。

### <a name="vm-with-premiumultra-disks-for-data"></a>具有高级/超磁盘数据的 VM
对于使用高级磁盘或超磁盘部署的 VM，标准 SAP HANA 系统复制配置适用于设置 HSR。  [SAP 帮助文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)概述了设置系统复制、接管辅助系统、故障回主系统以及禁用系统复制所涉及的步骤。  出于迁移的目的，我们只需要设置、接管和禁用复制步骤。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>用于数据和日志卷的 ANF 的 VM
在高级别上，需要将完整数据和日志卷的最新 HLI 存储快照复制到 Azure 存储，目标 HANA VM 可访问和恢复这些快照。  复制过程可以使用任何本机 Linux 复制工具完成。  

> [!IMPORTANT]
> 复制和数据传输可能需要数小时，具体取决于 HANA 数据库大小和网络带宽。  复制过程的大部分应在主要 HANA DB 停机之前完成。

### <a name="mcos-to-mdc-conversion"></a>MCOS 到 MDC 转换
一个系统中的多个组件 （MCOS） 部署模型被我们的一些 HLI 客户使用。  其动机是规避早期 SAP HANA 版本的多个数据库容器 （MDC） 存储快照限制。  在 MCOS 模型中，多个独立的 SAP HANA 实例堆叠在一个 HLI 刀片中。  将 HSR 用于迁移可以正常工作，但会导致多个 HANA VM，每个租户数据库都有一个租户数据库。  这种终端状态使环境比客户可能习惯的更繁忙。  当 SAP HANA 2.0 默认部署为 MDC 时，一个可行的替代方法是在 HSR 迁移后执行[HANA 租户移动](https://launchpad.support.sap.com/#/notes/2472478)。  此过程将这些独立的 HANA 数据库"合并"为单个 HANA 容器中的共同租户。  

### <a name="application-layer-consideration"></a>应用层考虑
数据库服务器被视为 SAP 系统的中心。  所有应用程序服务器都应位于 SAP HANA 数据库附近。  在某些情况下，当需要使用 PPG 时，可能需要将现有应用程序服务器重新定位到可能需要 HANA VM 的 PPG 上。  如果您已经方便了部署模板，则构建新的应用程序服务器可能更容易。  
如果现有应用程序服务器和新的 HANA VM 处于最佳位置，则无需构建新的应用程序服务器，除非需要额外的容量。  
如果构建新的基础结构以提高服务可用性，则现有应用程序服务器可能变得没有必要，应关闭并删除。
如果目标 VM 主机名已更改，并且与 HLI 主机名不同，则需要调整 SAP 应用程序服务器配置文件以指向新主机。  如果仅更改了 HANA DB IP 地址，则需要 DNS 记录更新才能引导传入连接到新的 HANA VM。

### <a name="acceptance-test"></a>验收测试
尽管与异构迁移相比，从 HLI 迁移到 VM 不会对数据库内容进行实质性更改，但我们仍建议验证新设置的关键功能和性能方面。  

### <a name="cutover-plan"></a>截止计划
尽管这种迁移是直接的，但它涉及现有 DB 的停用。  如果有必要回退，仔细规划保留源系统及其相关内容和备份映像至关重要。  良好的规划确实提供了更快的逆转。   


## <a name="post-migration"></a>迁移之后
迁移作业不会完成，直到我们安全地分离任何与 HLI 相关的服务或连接，以确保数据完整性得到保留。  此外，关闭不必要的服务。  本节会调用一些最高级的项目。

### <a name="decommissioning-the-hli"></a>HLI 退役
成功将 HANA DB 迁移到 Azure VM 后，请确保 HLI DB 上没有运行任何高效的业务事务。  但是，保持 HLI 运行一段时间等于其本地备份保留窗口是一种安全的做法，确保在需要时加快恢复速度。  只有到那时，HLI 刀片才能停用。  客户应通过联系其 Microsoft 代表来合同完成与 Microsoft 的 HLI 承诺。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>删除为 HLI 配置的任何代理（例如：Iptables、BIGIP） 
如果 IPTables 等代理服务用于路由本地流量到 HLI 和从 HLI 路由，则在成功迁移到 VM 后不再需要它。  但是，只要 HLI 刀片仍然处于待命状态，此连接服务应保留。  只有在 HLI 刀片完全停用后关闭服务。

### <a name="remove-global-reach-for-hli"></a>删除 HLI 的全球覆盖范围 
全球覆盖用于将客户的快速路由网关与 HLI ExpressRoute 网关连接起来。  它允许客户的本地流量直接到达 HLI 租户，而无需使用代理服务。  迁移后没有 HLI 单元，则不再需要此连接。  与 IPTables 代理服务的情况一样，GlobalReach 也应保留，直到 HLI 刀片完全停用。

### <a name="operating-system-subscription--movereuse"></a>操作系统订阅 = 移动/重用
当 VM 服务器已启动且 HLI 刀片已停用时，可以替换或重复使用操作系统订阅，以避免操作系统许可证的双重支付。



## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [SAP HANA 基础结构配置和在 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
- [Azure 上的 SAP 工作负载：规划和部署检查表](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)。
