---
title: 将 Azure 上的 SAP HANA （大型实例）迁移到 Azure 虚拟机 |Microsoft Docs
description: 如何将 Azure 上的 SAP HANA 迁移到 Azure 虚拟机
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: gwallace
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/11/2020
ms.author: saghorpa
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2dd01bf60104939fcf1f9bd1ccec0e7d72710041
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2020
ms.locfileid: "77154915"
---
# <a name="sap-hana-on-azure-large-instance-migration-to-azure-virtual-machines"></a>Azure 大型实例上的 SAP HANA 迁移到 Azure 虚拟机
本文介绍了可能的 Azure 大型实例部署方案，并提供了最小化转换停机时间的规划和迁移方法

## <a name="overview"></a>概述
由于发布了2016年9月 SAP HANA （！）的 Azure 大型实例的公告，许多客户已将此硬件作为服务用于其内存中计算平台。  最近几年来，Azure VM 可伸缩性扩展与 HANA 扩展部署模型的支持结合在一起，超过了大多数企业客户的 ERP 数据库容量需求。  我们开始向客户介绍如何将其 SAP HANA 工作负荷从物理服务器迁移到 Azure Vm。
本指南不是一种循序渐进的配置文档，而是介绍了常见的部署模型，并提供了规划、迁移建议，以便为准备最大限度地减少转换停机时间准备必要的注意事项。

## <a name="assumptions"></a>假设
本文作出以下假设：
- 唯一的兴趣是从 Hana 大型实例（B-HLI）迁移到 Azure VM，而无需进行大量的软件升级或修补。 这些小版本的更新包括使用最新的操作系统版本，或显式声明为相关 SAP 说明支持的 HANA 版本。 
- 所有更新/升级活动（如果需要）在迁移之前或之后执行。  例如，SAP HANA MCOS 转换为 MDC 部署。 
- 最少停机时间的迁移方法是 SAP HANA 系统复制。 其他迁移方法不属于本文档的范围。
- 这适用于 Rev3 和 Rev4 Sku。
- 在迁移过程中，HANA 部署体系结构主要保持不变。  也就是说，具有单实例灾难恢复的系统在目标位置保持不变。
- 客户已经查看并了解目标（点对点）体系结构的服务级别协议（SLA）。 
- 由于在 HLIs 与 Vm 之间的商业术语方面存在差异，客户应该监视其 Vm 的使用情况，以便进行成本管理。
- 客户理解并承认，是一种专用计算平台。 但是，Vm 在共享的基础结构上运行，这是安全的，并且与其他租户隔离。
- 客户已验证目标 Vm 是否支持预期的体系结构。 若要查看 SAP HANA 部署的所有受支持的 VM Sku，请查看[SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。
- 客户已验证设计和迁移计划。
- 规划灾难恢复节点和主站点。  在迁移后，客户将无法对 Vm 上运行的主站点使用 "B-HLI DR" 节点。
- 客户根据企业可恢复性和合规性要求将所需的备份文件复制到目标 Vm。 这是为了涵盖过渡期内的时点恢复需要。
- 对于 HSR HA，客户需要为[SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)和[RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)按照 SAP HANA ha 指南设置和配置 STONITH 设备。  它不像 $ B-HLI 案例那样预先配置。
- 此迁移方法不涵盖包含 Optane 配置的 B-HLI Sku。

## <a name="deployment-scenarios"></a>部署方案
下表总结了与 B-HLI 客户相同的部署模型。  可以将迁移到 Azure Vm 以实现所有的一种情况。  几种情况下，可能需要进行细微的体系结构修改才能获得当前 Azure 服务产品的优势。

| 方案 ID | B-HLI 方案 | 是否逐字迁移到 VM？ | 备注 |
| --- | --- | --- | --- |
| 1 | [具有一个 SID 的单个节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-one-sid) | 是 | - |
| 2 | [具有 MCOS 的单个节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-mcos) | 是 | - |
| 3 | [使用存储复制进行灾难恢复的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-storage-replication) | 否 | 存储复制不适用于 Azure 虚拟平台，将当前 DR 解决方案改为 HSR 或备份/还原 |
| 4 | [使用存储复制的单节点，含 DR （多用途）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-multipurpose-using-storage-replication) | 否 | 存储复制不适用于 Azure 虚拟平台，将当前 DR 解决方案改为 HSR 或备份/还原 |
| 5 | [HSR with STONITH 实现高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#hsr-with-stonith-for-high-availability) | 是 | 目标 Vm 没有预先配置的 SBD。  选择并部署 STONITH 解决方案。  可能的选项： Azure 防护代理（ [RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-pacemaker)、 [SLES](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker)支持）、SBD |
| 6 | [HA 与 HSR，灾难恢复与存储复制](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-with-hsr-and-dr-with-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原的 DR 需求 |
| 7 | [主机自动故障转移（1 + 1）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#host-auto-failover-11) | 是 | 将和用于 Azure Vm 的共享存储 |
| 8 | [带备用的横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-standby) | 是 | BW/4HANA，M128s，M416s，M416ms Vm，只使用和进行存储 |
| 9 | [无备用的横向扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-without-standby) | 是 | 使用 M128s、M416s、M416ms Vm 的 BW/4HANA （使用或不使用和进行存储） |
| 10 | [使用存储复制进行 DR 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-storage-replication) | 否 | 将存储复制替换为 HSR 或备份/还原的 DR 需求 |
| 11 | [使用 HSR 进行灾难恢复的单节点](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-with-dr-using-hsr) | 是 | - |
| 12 | [单节点 HSR 到 DR （成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#single-node-hsr-to-dr-cost-optimized) | 是 | - |
| 13 | [HA 和 DR with HSR](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr) | 是 | - |
| 14 | [HA 和 DR with HSR （成本优化）](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#high-availability-and-disaster-recovery-with-hsr-cost-optimized) | 是 | - |
| 15 | [使用 HSR 通过 DR 扩展](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-supported-scenario#scale-out-with-dr-using-hsr) | 是 | 带 M128s 的 BW/4HANA。 M416s、M416ms Vm （使用或不使用和进行存储） |


## <a name="source-hli-planning"></a>源（B-HLI）规划
在载入 B-HLI 服务器时，Microsoft 服务管理和客户都完成了计算、网络、存储和操作系统特定的设置，以便运行 SAP HANA 数据库。  迁移到 Azure VM 需要进行类似的规划。

### <a name="sap-hana-housekeeping"></a>SAP HANA 内务处理 
在迁移 HANA 数据库之前，最好是整理数据库内容，使不需要的、过时的数据或过时的日志不会迁移到新数据库。  内务处理一般涉及到删除或存档旧数据、过期数据或非活动数据。  因此，应在非生产系统中测试这些 "数据清理" 操作，以在生产使用之前验证其数据剪裁有效性。

### <a name="allow-network-connectivity-for-new-vms-and-or-virtual-network"></a>允许新 Vm 和虚拟网络的网络连接 
在客户的[SAP HANA （大型实例）网络体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture)一文中介绍了从 Azure vnet 建立的网络连接。 而且，网络流量路由是以 "在 Azure 中路由" 一节中概述的方式完成的。
1. 为了将新的 VM 设置为迁移的 HSR 目标，如果新的 Azure VM 放置在具有 IP 地址范围的现有虚拟网络中，而该虚拟网络已具有连接到 B-HLI 节点的权限，则不需要进行进一步的
2. 如果新的 Azure VM 放置在新的虚拟网络（可能位于另一个区域）中，并且对等互连使用现有的虚拟网络，则可以使用 ExpressRoute 服务密钥和来自原机配置的资源 ID 来允许访问此新的虚拟网络 IP 范围。  与 Microsoft 服务管理协调，使虚拟网络能够实现连接。  注意：若要最大程度地减少应用程序层和数据库层之间的网络延迟，应用程序层和数据库层都必须位于同一虚拟网络上。  

### <a name="existing-app-layer-availability-set-availability-zones-and-proximity-placement-group"></a>现有应用层可用性集、可用性区域和邻近度布局组
为了满足特定的服务级别目标，当前部署模型已完成。  在此移动中，请确保目标基础结构能够满足或超出设定目标。  
更可能的是，客户 SAP 应用程序服务器放置在可用性集中。  如果当前的部署服务级别令人满意， 
- 如果通过名称解析来替换 SAP HANA 服务器是通过交换 DB 主机名的 Ip 来完成的，则无需进一步操作。  
- 如果未使用 PPG，请确保将所有应用程序和数据库服务器放在同一区域，以最大程度地减少网络延迟。
- 如果你使用的是 PPG，请参阅本文档目标规划、可用性集、可用性区域和邻近性放置组（PPG）中的部分。

### <a name="storage-replication-discontinuance-process-if-used"></a>存储复制 discontinuance 过程（如果使用）
如果将存储复制用作 DR 解决方案，则在 SAP 应用程序关闭并且最后一个 SAP HANA 目录、日志文件、数据备份到远程存储卷后，复制将需要终止（非计划）。  此操作是一种预防性策略，如果在物理到 Azure VM 转换的过程中发生灾难，我们将在灾难恢复过程中有一个数据库。

### <a name="data-backups-preservation-consideration"></a>数据备份保留注意事项
在 Azure VM 上切换到 SAP HANA 后，如果需要，则在的所有基于快照的数据或日志备份都无法轻松访问或恢复到 VM。 对于早期过渡期，在基于 Azure 的备份构建足够的历史记录以满足时间点恢复要求之前，我们建议你在转换前，还需要在  将这些备份复制到新的 SAP HANA VM 可访问的 Azure 存储帐户。 除了备份 B-HLI 内容外，还可以在需要回滚的情况下随时访问 SAP 布局的完整备份，这也是一种非常明智的做法。

### <a name="adjusting-system-monitoring"></a>调整系统监视 
客户使用多种不同的工具来监视和发送其 SAP 环境内系统的警报通知。  此项只是一种简单的调用，用于在添加新 VM 时执行适当的操作，其中包含用于监视的任何调整，并根据需要更新警报通知收件人。

### <a name="microsoft-operations-team-involvement"></a>Microsoft 操作团队参与 
从现有的 Azure 门户库中打开一个票证。  创建支持票证后，支持工程师将通过电子邮件与你联系。  

### <a name="engage-microsoft-account-team"></a>与 Microsoft 帐户团队接洽
将迁移计划于 B-HLI 合同的周年周年续订时间，以最大程度地减少计算资源上支出的不必要情况。 若要停止 "B-HLI" 边栏选项卡，需协调合同终止和设备实际的关机。

## <a name="destination-planning"></a>目标规划
建立一个新的基础结构来取代现有的基础结构，一定要确保新的添加功能适用于大型方案。  下面是结论的一些要点。

### <a name="resource-availability-in-the-target-region"></a>目标区域中的资源可用性 
当前的 SAP 应用程序服务器的部署区域通常与关联的 HLIs 有关。  但是，HLIs 的位置比可用的 Azure 区域少。  在从物理速度迁移到 Azure VM 时，它为性能优化提供了 "微调" 所有相关服务的邻近距离。  在执行此操作时，需要考虑的一个重要事项是确保所选区域具有相关资源。  例如，出于高可用性考虑，某些 VM 系列或 Azure 区域的产品/服务的可用性。

### <a name="virtual-network"></a>虚拟网络 
基础结构架构师需要选择是在现有的 SAP 应用程序虚拟网络内运行新的 HANA 数据库还是创建新的。  主要的决定因素是 SAP 环境的当前网络布局。  例如，在部署基础结构发生更改的情况下，从一个区域到两个区域的部署，并利用 PPG。 此可用性增强功能对体系结构进行了更改。 有关详细信息，请参阅 Azure PPG 一文，[了解如何通过 SAP 应用程序实现最佳网络延迟](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)。   

### <a name="security"></a>安全性
无论新 SAP HANA VM 是否在新的或现有的 vnet/子网上登录，它都表示需要保护的新业务关键服务。  对于这一新的服务类别，符合公司信息安全策略的适当访问控制应进行评估和部署。 

### <a name="vm-sizing-recommendation"></a>VM 大小建议
此迁移也是用于正确调整 HANA 计算引擎规模的机会。  可以结合使用 HANA[系统视图](https://help.sap.com/viewer/7c78579ce9b14a669c1f3295b0d8ca16/Cloud/3859e48180bb4cf8a207e15cf25a7e57.html)和 hana Studio 来了解系统资源消耗，这允许适当调整大小以提高支出效率。

### <a name="storage"></a>存储 
存储性能是影响 SAP 应用程序用户体验的因素之一。  基于给定 VM SKU，最小存储布局建议[SAP HANA Azure 虚拟机存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)发布。 建议查看这些最低规格，并与现有的速度系统统计信息进行比较，以确保新 HANA VM 的 IO 容量和性能充足。

如果为新 HANA VM 及其关联的服务配置 PPG，请提交支持票证以检查并确保存储和 HANA VM 的归置。  
由于你的备份解决方案可能需要更改，因此还应再次对存储成本进行更改，以避免操作开支惊喜。 

### <a name="storage-replication-for-disaster-recovery"></a>用于灾难恢复的存储复制
在，为 HANA 数据库的灾难恢复复制提供了存储复制作为默认选项。 此功能不是 Azure VM 的默认选项。 请考虑 HSR、备份/还原或其他受支持的解决方案，满足你的业务需求。

### <a name="availability-sets-availability-zones-and-proximity-placement-groups"></a>可用性集、可用性区域和邻近性放置组 
考虑尽量减少应用程序层与 SAP HANA 之间的距离以使网络延迟降到最低，应将新的数据库 VM 和当前 SAP 应用程序服务器置于邻近位置组（PPG）中。 请参阅[邻近的放置组](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-proximity-placement-scenarios)，了解 Azure 可用性集和可用性区域如何与 PPG for SAP 部署配合使用。
如果目标 HANA 系统的成员部署在多个 Azure 区域中，则客户应清楚地了解所选区域的延迟配置文件。 SAP 系统组件的位置对于 SAP 应用程序和数据库之间的最近距离是最佳的。  利用公共域[可用性区域延迟测试工具](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test)，可以更轻松地进行测量。  


### <a name="backup-strategy"></a>备份策略
许多客户已使用第三方备份解决方案进行 SAP HANA。  在这种情况下，只需配置其他受保护的 VM 和 HANA 数据库。  如果在迁移后停止了计算机，则当前的进度备份作业将处于非计划计划。
虚拟机上的 SAP HANA 的 Azure 备份现已正式发布。  有关详细信息，请参阅以下链接：[备份](https://docs.microsoft.com/azure/backup/backup-azure-sap-hana-database)、[还原](https://docs.microsoft.com/azure/backup/sap-hana-db-restore)、[管理](https://docs.microsoft.com/azure/backup/sap-hana-db-manage)Azure vm 中的 SAP HANA 备份。

### <a name="dr-strategy"></a>DR 策略
如果服务级别目标的恢复时间比简单备份到 blob 存储以及就地还原或部署到新 VM 的时间长，则这是最简单且最便宜的 DR 策略。  
例如，通常通过 HSR 完成 HANA DR 的大型实例平台;在 Azure VM 上，HSR 也是最自然的本机 SAP HANA DR 解决方案。  无论源部署是否为单一实例，使用或不使用自动故障转移的群集或多节点扩展 HANA，都需要在 DR 区域中提供源基础结构的目标 HSR 副本。 在从主机到 VM 的迁移完成后，将建立此 HSR 目标 DR 副本。  DR HANA 实例将作为辅助复制站点注册到 VM 实例上的主 SAP HANA。  

### <a name="sap-application-server-connectivity-destination-change"></a>SAP 应用程序服务器连接目标更改
HSR 迁移方法会生成新的 HANA DB 主机，因此，应用程序层有一个新的数据库主机名，需要修改 SAP 配置文件以反映新的主机名。  如果开关通过名称解析来保留主机名，则无需更改配置文件。

### <a name="operating-system"></a>操作系统
虽然和 VM 的操作系统映像处于相同的发布级别，但在 SLES 11 SP4 中是不完全相同。 客户必须在 B-HLI 验证所需的包、修补程序、修补程序、内核和安全修补程序，以便可以将这些修补程序安装到目标 VM 的操作系统上。  此外，客户通常会在 SAP HSR 的目标 VM 上配置更新的 OS 版本。  每个[SAP 说明 2763388](https://launchpad.support.sap.com/#/notes/2763388)支持。

### <a name="new-sap-license-request"></a>新的 SAP 许可证请求
为新的 HANA 系统请求新的 SAP 许可证的简单调用现在已迁移到 Vm。

### <a name="service-level-agreement-sla-differences"></a>服务级别协议（SLA）差异
作者想要了解如何与 Azure 虚拟机之间的可用性 SLA 产生差别。  例如，聚集 HLIs HA 对提供99.99% 的可用性。 若要获得相同的 SLA，必须在可用性区域中部署 Vm。 [本文](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/)介绍了与关联的部署体系结构的可用性，以便客户可以相应地规划目标基础结构。


## <a name="migration-strategy"></a>迁移策略
在本文档中，我们只介绍了 HANA 系统复制方法，用于从 B-HLI 迁移到 Azure VM。  依赖于部署的目标存储解决方案，此过程稍有不同。 高级步骤如下所述。

### <a name="vm-with-premiumultra-disks-for-data"></a>包含数据的高级/超磁盘的 VM
对于使用高级或 hyper-v 部署的 Vm，标准 SAP HANA 系统复制配置适用，可用于设置 HSR。  引用的[SAP 帮助文章](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.04/099caa1959ce4b3fa1144562fa09e163.html)提供了在两个系统之间设置系统复制、接管辅助系统、故障回复到主系统和禁用系统复制所涉及的步骤的概述。  出于迁移的目的，我们只需要在《迁址源 ' 步骤上进行设置、接管和禁用系统复制。  

### <a name="vm-with-anf-for-data-and-log-volumes"></a>用于数据和日志卷的带和的 VM
在高级别中，需要将完整数据和日志卷的最新的 B-HLI 存储快照复制到 Azure 存储中，以供目标 HANA VM 进行访问和恢复。  可以通过任何本机 Linux 复制工具完成复制过程。  

>[!Important]
> 复制和数据传输可能需要几个小时，具体取决于 HANA 数据库大小和网络带宽。  大容量复制过程应在主 HANA DB 停机时间之前进行。

### <a name="mcos-to-mdc-conversion"></a>MCOS 到 MDC 转换
一个系统（MCOS）部署模型中的多个组件是由我们的一位客户选择的。  动机是绕过更早 SAP HANA 版本的多数据库容器（MDC）存储快照限制。  在 MCOS 模型中，多个独立的 SAP HANA 实例堆积在一只一层边栏选项卡中。  使用 HSR 进行迁移将会正常运行，从而使多个 HANA Vm 每个都有一个租户 DB。  这种最终状态使得与客户可能已使的环境更繁忙。  使用 SAP HANA 2.0 默认部署作为 MDC，一种可行的替代方法是在 HSR 迁移后执行[HANA 租户移动](https://launchpad.support.sap.com/#/notes/2472478)。  此过程将这些独立 HANA 数据库合并到单个 HANA 容器中的 cotenants 中。  

### <a name="application-layer-consideration"></a>应用层注意事项
数据库服务器被视为 SAP 系统的中心。  所有应用程序服务器都应位于 SAP HANA DB 附近。  在某些情况下，如果需要 PPG 的新使用，请将现有应用程序服务器重新定位到可能需要 HANA VM 的 PPG。  如果你的部署模板非常方便，则生成新的应用程序服务器可能会更容易。  
如果现有的应用程序服务器和新的 HANA VM 处于最佳位置，则不需要构建新的应用程序服务器，除非需要额外的容量。  
如果构建新基础结构以提高服务可用性，则现有应用程序服务器可能会变得不必要，应关闭并删除。
如果目标 VM 主机名发生了变化，并且不同于 B-HLI 主机名，则需要对 SAP 应用程序服务器配置文件进行调整，使之指向新的主机。  如果只更改了 HANA DB IP 地址，则需要进行 DNS 记录更新，以将传入连接连接到新的 HANA VM。

### <a name="acceptance-test"></a>验收测试
尽管从 B-HLI 到 VM 的迁移对数据库内容不做任何重要的更改，但与异类迁移相比，我们仍建议验证新设置的主要功能和性能方面。  

### <a name="cutover-plan"></a>切换计划
虽然这种迁移是直接进行的，但它还是需要解除现有 DB 的授权。  如果需要回退，请仔细规划保留源系统及其关联的内容和备份映像。  良好的规划确实会提供 speedier 的反转。   


## <a name="post-migration"></a>迁移之后
在我们安全地分离了任何与 B-HLI 相关的服务或连接，以确保保留数据完整性之前，不会完成迁移作业。  同时关闭不必要的服务。  本部分将介绍一些最重要的事项。

### <a name="decommissioning-the-hli"></a>停止，
成功将 HANA DB 迁移到 Azure VM 后，不应在 B-HLI 数据库上进行生产业务事务。  但是，将启动并运行的时间段等于其本地备份数据保留时段是一种安全的做法，如有必要，请确保 speedier 的数据恢复。  只有在 ""  除了技术任务外，客户在其最大程度上，应合同形式与 Microsoft 进行一项工作承诺。  客户应联系其 Microsoft 代表，以完成过程中的工作。

### <a name="remove-any-proxy-ex-iptables-bigip-configured-for-hli"></a>删除为 B-HLI 配置的任何代理（ex： Iptables、BIGIP） 
如果使用类似于 IPTables 的代理服务来将本地流量路由到 B-HLI，则在成功迁移到 VM 后，不再需要该服务。  但是，只要在 "退役" 主题中讨论了 ""，则可以保留此连接服务。  此服务可以在 "完成" 边栏选项卡完全停止后关闭。 

### <a name="remove-global-reach-for-hli"></a>删除 Global Reach 
Global Reach 通常用于将客户的 ExpressRoute 网关与的 expressroute 网关连接起来，从而使客户的本地流量可以直接访问 B-HLI 租户，而无需使用代理服务。  与 IPTables 代理服务一样，应将 GlobalReach 保留到 ""。

### <a name="operating-system-subscription--movereuse"></a>操作系统订阅-移动/重复使用
当 VM 服务器考验，并且在服务器上停止使用时，可以替换或重复使用 OS 订阅，以避免对 OS 许可证进行双重支付。



## <a name="next-steps"></a>后续步骤
请参阅以下文章：
- [在 Azure 上 SAP HANA 基础结构配置和操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
- [Azure 上的 SAP 工作负荷：规划和部署清单](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-deployment-checklist)。
