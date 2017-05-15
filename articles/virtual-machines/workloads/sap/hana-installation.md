---
title: "在 Azure SAP HANA（大型实例）上安装 SAP HANA | Microsoft Docs"
description: "如何在 Azure SAP HANA（大型实例）上安装 SAP HANA。"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 12/01/2016
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 54b5b8d0040dc30651a98b3f0d02f5374bf2f873
ms.openlocfilehash: b791be369016dd52d95ec727e46fd8b554c09047
ms.contentlocale: zh-cn
ms.lasthandoff: 04/28/2017


---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何在 Azure 上安装和配置 SAP HANA（大型实例）

SAP HANA 的安装由你负责。可以在接管新的 Azure 上的 SAP HANA（大型实例）服务器以及在 Azure VNet 与 HANA 大型实例单元之间建立连接后启动该活动。 请注意，根据 SAP 政策，SAP HANA 的安装必须由经过认证的 SAP HANA 安装人员（已通过 SAP 技术认证专员 - SAP HANA 安装认证考试）或经过 SAP 认证的系统集成商 (SI) 执行。

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>接收 HANA 大型实例单元后的前几个步骤

接收 HANA 大型实例并与该实例建立访问和连接后的**第一个步骤**是将该实例的 OS 注册到 OS 提供程序。 这包括在需要事先部署的 SUSE SMT 实例中注册 SUSE Linux OS。 或者，需要将 RedHat OS 注册到所要连接的 Red Hat 订阅管理器中。 另请参阅此[文档](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的备注。 以后若要修补 OS，也需要执行此步骤。 此任务由客户负责。 

**第二个步骤**是检查特定 OS 发行版/版本的新修补程序和修复程序。 检查 HANA 大型实例的修补级别是否处于最新状态。 根据 OS 修补程序/版本的时间以及对 Microsoft 可以部署的映像的更改，有时可能未包含最新的修补程序。 因此，在接管 HANA 大型实例单元并将 OS 安装注册到 Linux 分发服务器后，必须执行一个步骤来检查与安全性、功能、可用性和性能相关的修补程序是否由特定的 Linux 供应商发布，以及是否需要应用这些修补程序。

**第三个步骤**是查看有关在特定 OS 发行版/版本上安装和配置 SAP HANA 的 SAP 说明。 由于建议会不断发生变化，或者与各种安装方案相关的 SAP 说明或配置会发生更改，因此，Microsoft 不一定总能完美配置 HANA 大型实例单元。 客户必须阅读 SAP 说明（最起码包括下面列出的说明），检查所需的 OS 发行版/版本配置，并应用配置设置（如果尚未这样做）。

具体而言，请检查以下参数并将其最终调整为：

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

从 SLES12 SP1 和 RHEL 7.2 开始，必须在 /etc/sysctl.d 目录中的某个配置文件内设置这些参数。 例如，必须创建名为 91-NetApp-HANA.conf 的配置文件。 对于旧版 SLES 和 RHEL，必须在 in/etc/sysctl.conf 中设置这些参数。

对于所有 RHEL 版本以及 SLES12 和更高版本，必须在 in/etc/modprobe.d/sunrpc-local.conf 中设置 
- sunrpc.tcp_slot_table_entries = 128

参数。 如果该文件不存在，必须先通过添加以下条目创建该文件： 
- options sunrpc tcp_max_slot_table_entries=128

**第四个步骤**是检查 HANA 大型实例单元的系统时间。 将部署一个带有系统时区的、表示 HANA 大型实例戳记所在 Azure 区域位置的实例。 你可以任意更改自己拥有的实例的系统时间或时区。 执行该步骤并在租户中排列更多实例可以防止今后需要调整新交付的实例的时区。 在接管后，Microsoft 运营部门并不了解你在实例上设置的系统时区。 因此，新部署的实例可能不会在与切换到的实例相同的时区中设置。 因此，客户需要负责检查接管后的实例的时区，并根据需要调整。 

**第五个步骤**是检查 etc/hosts。 接管刀片服务器后，它们将会根据不同的用途而分配有不同的 IP 地址（参阅下一部分）。 请检查 etc/hosts。 如果将单元添加到了现有的租户，请不要料想会使用以前交付的系统的 IP 地址正确维护新部署的系统的 etc/hosts。 因此，客户需负责检查设置是否正确，使新部署的实例能够与租户中以前部署的单元交互并解析其名称。 

## <a name="networking"></a>网络
假设你已遵循以下文档中有关设计 Azure VNet 并将其连接到 HANA 大型实例的建议：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

在单个单位的网络方面，有些详细信息值得注意。 每个 HANA 大型实例单元附带两个或三个 IP 地址，这些地址已分配到该单元的两个或三个 NIC 端口。 HANA 横向扩展配置和 HANA 系统复制方案中使用三个 IP 地址。 分配给单元 NIC 的一个 IP 地址位于 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的服务器 IP 池外部。

分配有两个 IP 地址的单元的分布形式应如下所示：

- 应该为 eth0.xx 分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 此 IP 地址将用于在 OS 的 /etc/hosts 中进行维护。
- 应该为 eth1.xx 分配一个用来与 NFS 通信的 IP 地址。 因此，**不**需要在 etc/hosts 中维护这些地址即可在租户中允许实例间的通信流量。

HANA 系统复制或分配有两个 IP 地址的 HANA 横向扩展刀片服务器配置不适合用于部署。 如果你已只分配了两个 IP 地址，并想要部署这种方案，请联系 Microsoft 服务管理部门，让他们帮忙在第三个 VLAN 中分配第三个 IP 地址。 对于在三个 NIC 端口上分配了三个 IP 地址的 HANA 大型实例单位，请注意以下事项：

- 应该为 eth0.xx 分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 因此，此 IP 地址不会用于在 OS 的 /etc/hosts 中进行维护。
- 应该为 eth1.xx 分配一个用来与 NFS 存储通信的 IP 地址。 因此，不应在 etc/hosts 中维护此类地址。
- 应在 etc/hosts 中以独占方式维护 eth2.xx，以便在不同的实例之间通信。 在横向扩展 HANA 配置中需要维护的 IP 地址也是 HANA 用于节点间配置的 IP 地址。



## <a name="storage"></a>存储

Azure SAP HANA（大型实例）的存储布局可通过 Azure SAP HANA 服务管理按 SAP 建议的最佳实践进行配置，详见 [SAP HANA Storage Requirements](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)（SAP HANA 存储要求）白皮书。 在阅读文档和查看 HANA 大型实例单元时，你将会认识到，单元随附了一个庞大的 HANA/data 数据卷，以及一个HANA/log/backup 卷。 使用这么大的 HANA/data 卷的原因在于，我们为客户提供的存储快照使用完全相同的磁盘卷。 这意味着，执行的存储快照越大，所需的空间就越多。 HANA/log/backup 卷不被认为是存储数据库备份的卷，它用作 HANA 事务日志的备份卷。 在将来的存储快照自助服务版本中，我们会专门在此特定卷中包含更频繁的快照；如果你需要启用 HANA 大型实例基础结构提供的灾难恢复功能，我们还会在灾难恢复站点中包含更频率的复制内容。请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的详细信息。 

除了提供的存储以外，你还可以购买更多存储容量，增量为 1 TB。 可以将此附加存储作为新卷添加到 HANA 大型实例。

在 Microsoft 服务管理部门的帮助下登记期间，客户需指定 <SID>ADM 用户和 sapsys 组的用户 ID (UID) 和组 ID (GID)（例如：1000,500）。在安装 SAP HANA 系统期间，必须使用与此相同的值。

大型实例戳中的存储控制器和节点将同步到 NTP 服务器。 针对 NTP 服务器同步 Azure SAP HANA（大型实例）单元和 Azure VM 以后，在 Azure 或大型实例戳中的基础结构和计算单元之间就不会出现显著的时间偏移。

若要优化 SAP HANA，使其不超过使用的存储，还应设置以下 SAP HANA 配置参数：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
对于 SAP HANA 1.0 SPS12 或更低版本，可根据 [SAP 说明 #2267798 - SAP HANA 数据库的配置](https://launchpad.support.sap.com/#/notes/2267798)中所述，在安装 SAP HANA 数据库期间设置这些参数

也可以在安装 SAP HANA 数据库之后使用 hdbparam 框架配置这些参数。 

在 SAP HANA 2.0 中，hdbparam 框架已弃用。 因此，必须使用 SQL 命令设置这些参数。 有关详细信息，请参阅 [SAP 说明 #2399079：HANA 2 中已弃用 hdbparam](https://launchpad.support.sap.com/#/notes/2399079)。


## <a name="operating-system"></a>操作系统

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) 是针对 Azure SAP HANA（大型实例）安装的 Linux 分发。 此特定分发提供特定于 SAP 的&quot;现成&quot;功能（包括在 SLES 上高效运行 SAP 所需的预设参数）。

请参阅 SUSE 网站上的[资源库/白皮书](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)以及 SAP 社区网络 (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（基于 SUSE 的 SAP），了解多种与部署 SLES SAP HANA 相关的有用资源（包括设置高可用性、特定于 SAP 操作的安全强化等）。

其他与 SLES 相关的有用链接：

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（SUSE Linux 站点上的 SAP HANA）
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)（SAP 最佳实践：排队复制 - 基于 SUSE Linux Enterprise 12 的 SAP NetWeaver）
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)（ClamSAP - 适用于 SAP 的 SLES 病毒防护）（包括 SLES 12 for SAP Applications）

适用于在 SLES 12 SP1 上实现 SAP HANA 的 SAP 支持说明：

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)（SAP 支持说明 1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指南）
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)（SAP 支持说明 2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的 OS 设置）
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 1984787 - SUSE Linux Enterprise Server 12：安装说明）
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 171356 - Linux 上的 SAP 软件：常规信息）
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 1391070 - Linux UUID 解决方案）

## <a name="time-synchronization"></a>时间同步

SAP 对组成 SAP 系统的各组件的时间差异非常敏感。 如果你已使用过 SAP (Basis) 很长时间，则可能熟悉错误标题为 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 短转储，因为当不同服务器或 VM 的系统时间偏移过大时，就会出现这些短转储。

就 Azure SAP HANA（大型实例）来说，在 Azure 中进行的时间同步不适用于大型实例戳中的计算单元。 这种同步不适用于在 Azure 中（VM 上）以本机方式运行的 SAP 应用程序，因为 Azure 可确保正确同步系统的时间。 因此，必须设置单独的时间服务器，供在 Azure VM 上运行的 SAP 应用程序服务器以及在 HANA 大型实例上运行的 SAP HANA 数据库实例使用。 大型实例戳中的存储基础结构与 NTP 服务器进行时间同步。



