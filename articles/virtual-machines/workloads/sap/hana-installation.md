---
title: 在 Azure SAP HANA（大型实例）上安装 SAP HANA | Microsoft Docs
description: 如何在 Azure SAP HANA（大型实例）上安装 SAP HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/04/2018
ms.author: rclaus
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0747bd5dc147639167f352dea46f7e4a1d43227d
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2018
ms.locfileid: "34763437"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何在 Azure 上安装和配置 SAP HANA（大型实例）

下面介绍了在阅读本指南之前要了解的一些重要定义。 在 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中，我们引入了两类不同的 HANA 大型实例单元，其中包括：

- 称为“I 类”SKU 的 S72、S72m、S144、S144m、S192、S192m 和 S192xm。
- 称为“II 类”SKU 的 S384、S384m、S384xm、S384xxm、S576m、S576xm、S768m、S768xm 和 S960m。

将在整个 HANA 大型实例文档中使用类说明符，最终用于根据 HANA 大型实例 SKU 指代不同的功能和要求。

我们还经常用到其他定义，如下所述：
- **大型实例模具：** 已通过 SAP HANA TDI 认证的硬件基础结构堆栈，专门用于在 Azure 中运行 SAP HANA 实例。
- **Azure 上的 SAP HANA（大型实例）：** 用于在通过 SAP HANA TDI 认证的、部署在不同 Azure 区域中的大型实例模具中的硬件上运行 HANA 实例的产品的官方名称。 本技术部署指南中广泛使用的相关术语“HANA 大型实例”是“Azure 上的 SAP HANA（大型实例）”的简称。


用户有责任安装 SAP HANA，可以在切换 Azure 上的 SAP HANA（大型实例）新服务器后启动活动。 也可以在连接 Azure VNet 与 HANA 大型实例单元后启动活动。 

> [!Note]
> 根据 SAP 策略，必须由获得 SAP HANA 安装认证的人员安装 SAP HANA。 安装人员必须通过 SAP 技术认证初级考试、SAP HANA 安装认证考试，或由 SAP 认证的系统集成商 (SI) 安装。

再次查看（尤其是在计划安装 HANA 2.0 时）[SAP Support Note #2235581 - SAP HANA: Supported Operating Systems](https://launchpad.support.sap.com/#/notes/2235581/E)（SAP 支持说明 #2235581 - SAP HANA：支持的操作系统），确保已决定安装的 SAP HANA 版本支持该操作系统。 将会发现，HANA 2.0 支持的 OS 比 HANA 1.0 支持的 OS 更为有限。 

## <a name="first-steps-after-receiving-the-hana-large-instance-units"></a>接收 HANA 大型实例单元后的前几个步骤

收到 HANA 大型实例且建立与实例的访问和连接通道后的第一步是，向 OS 提供程序注册 OS 实例。 在这一步中，请在需要部署到 Azure VM 的 SUSE SMT 实例中注册 SUSE Linux OS。 HANA 大型实例单元可以连接到此 SMT 实例（请参阅本文档的后面部分）。 或者，需要将 RedHat OS 注册到所要连接的 Red Hat 订阅管理器中。 另请参阅此[文档](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的备注。 若要修补 OS，也需要执行这一步。 此任务由客户负责。 对于 SUSE，请在[此处](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)查找有关安装和配置 SMT 的文档。

**第二个步骤**是检查特定 OS 发行版/版本的新修补程序和修复程序。 检查 HANA 大型实例的修补级别是否处于最新状态。 有时，可能未包含最新修补程序，具体视 OS 修补程序/版本的时间安排，以及 Microsoft 可以部署的映像的更改而定。 因此，在接管 HANA 大型实例单元后，必须检查与安全性、功能、可用性和性能相关的修补程序是否已发布，同时检查由特定 Linux 供应商发布的修补程序，以及是否需要应用这些修补程序。

**第三个步骤**是查看有关在特定 OS 发行版/版本上安装和配置 SAP HANA 的 SAP 说明。 由于建议会不断发生变化，或者与各种安装方案相关的 SAP 说明或配置会发生更改，因此，Microsoft 不一定总能完美配置 HANA 大型实例单元。 因此，作为客户，必须阅读与确切 Linux 发行版上的 SAP HANA 相关的 SAP 说明。 还要检查 OS 发行版/版本的必要配置，并应用尚未应用的配置设置。

具体而言，请检查下面的参数，最终将它们调整为如下所示：

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

**第四个步骤**是检查 HANA 大型实例单元的系统时间。 部署实例时使用的是系统时区，表示 HANA 大型实例标记所在 Azure 区域的位置。 可以任意更改自己拥有的实例的系统时间或时区。 执行该步骤并在租户中排列更多实例可以防止今后需要调整新交付的实例的时区。 在接管后，Microsoft 运营部门并不了解你在实例上设置的系统时区。 因此，新部署的实例可能不会在与切换到的实例相同的时区中设置。 因此，客户需要负责检查接管后的实例的时区，并根据需要调整。 

**第五个步骤**是检查 etc/hosts。 接管刀片服务器后，它们会根据不同的用途而分配有不同的 IP 地址（参阅下一部分）。 检查 etc/hosts 文件。 如果将单元添加到了现有的租户，请不要料想会使用以前交付的系统的 IP 地址正确维护新部署的系统的 etc/hosts。 因此，客户需负责检查设置是否正确，使新部署的实例能够与租户中以前部署的单元交互并解析其名称。 

## <a name="networking"></a>网络
假设已遵循以下文档中的建议，这些文档介绍了如何设计 Azure VNet，并将这些 VNet 连接到 HANA 大型实例：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA（大型实例）的基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

在单个单位的网络方面，有些详细信息值得注意。 每个 HANA 大型实例单元附带两个或三个 IP 地址，这些地址已分配到该单元的两个或三个 NIC 端口。 HANA 横向扩展配置和 HANA 系统复制方案中使用三个 IP 地址。 分配给单元 NIC 的一个 IP 地址位于 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的服务器 IP 池外部。

分配有两个 IP 地址的单元的分布形式应如下所示：

- 应该为 eth0.xx 分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 此 IP 地址用于在 OS 的 /etc/hosts 中进行维护。
- 应该为 eth1.xx 分配一个用来与 NFS 通信的 IP 地址。 因此，**不**需要在 etc/hosts 中维护这些地址即可在租户中允许实例间的通信流量。

对于 HANA 系统复制或 HANA 横向扩展部署用例，不适合使用分配有两个 IP 地址的刀片式服务器配置。 如果只分配了两个 IP 地址，且希望部署此类配置，请联系 Azure 上的 SAP HANA 服务管理部门，让其在第三个 VLAN 中分配第三个 IP 地址。 对于在三个 NIC 端口上分配了三个 IP 地址的 HANA 大型实例单元，请注意以下使用规则：

- 应该为 eth0.xx 分配一个位于提交给 Microsoft 的服务器 IP 池地址范围以外的 IP 地址。 因此，此 IP 地址不会用于在 OS 的 /etc/hosts 中进行维护。
- 应该为 eth1.xx 分配一个用来与 NFS 存储通信的 IP 地址。 因此，不应在 etc/hosts 中维护此类地址。
- 应在 etc/hosts 中以独占方式维护 eth2.xx，以便在不同的实例之间通信。 在横向扩展 HANA 配置中，也需要将这些 IP 地址作为 HANA 用于节点间配置的 IP 地址进行维护。



## <a name="storage"></a>存储

Azure 上的 SAP HANA 服务管理部门可遵循 [SAP HANA 存储要求](http://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮书中的 SAP 建议指南，配置 Azure 上的 SAP HANA（大型实例）的存储布局。 有关包含不同 HANA 大型实例 SKU 的各卷的粗略大小，请参阅 [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

下表列出了存储卷的命名约定：

| 存储用途 | 装入名 | 卷名 | 
| --- | --- | ---|
| HANA 数据 | /hana/data/SID/mnt0000<m> | 存储 IP：/hana_data_SID_mnt00001_tenant_vol |
| HANA 日志 | /hana/log/SID/mnt0000<m> | 存储 IP：/hana_log_SID_mnt00001_tenant_vol |
| HANA 日志备份 | /hana/log/backups | 存储 IP：/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共享 | /hana/shared/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

其中，SID 代表 HANA 实例系统 ID 

租户代表在部署租户时在内部枚举操作。

可以看到，HANA 共享和 usr/sap 使用同一个卷。 装入点的命名法确实包括 HANA 实例系统 ID 和装入编号。 在纵向扩展部署中，只有一个装入，如 mnt00001。 而在横向扩展部署中，装入数量与辅助角色和主节点一样多。 对于横向扩展环境，数据、日志、日志备份卷都会共享并附加到横向扩展配置中的每个节点。 对于运行多个 SAP 实例的配置，将创建一组不同的卷，并将其附加到 HANA 大型实例单元。

阅读本文档并了解 HANA 大型实例单元时，将会发现，单元随附相当大的 HANA/data 磁盘卷，并且还有 HANA/log/backup 卷。 HANA/data 卷之所以这么大是因为，我们为客户提供的存储快照使用的是同一磁盘卷。 也就是说，如果存储快照更多，分配的存储卷中的快照占用的空间就更多。 HANA/log/backup 卷并不是用于放置数据库备份的卷。 它的大小适合用作 HANA 事务日志备份的备份卷。 在今后发布的存储快照自助服务版本中，这一特定卷将能包含更频繁生成的快照。 而且，这样一来，还可以更频繁地复制到灾难恢复站点，前提是希望启用 HANA 大型实例基础结构提供的灾难恢复功能。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

除了提供的存储以外，还可以购买更多存储容量，增量为 1 TB。 可以将此附加存储作为新卷添加到 HANA 大型实例。

在 Azure 上的 SAP HANA 服务管理部门的帮助下进行载入期间，客户指定 sidadm 用户和 sapsys 组的用户 ID (UID) 和组 ID (GID)（例如：1000,500）。在安装 SAP HANA 系统期间，必须使用与此相同的值。 若要在一个单元上部署多个 HANA 实例，将获得多组卷（每个实例一组卷）。 因此，需要在部署时定义：

- 不同 HANA 实例的 SID（sidadm 由此派生）。
- 不同 HANA 实例的内存大小。 这是因为每个实例的内存大小定义的是各组卷的卷大小。

根据存储提供程序的建议，为所有已装载卷（不包括启动 LUN）配置以下装入选项：

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

这些装入点在 /etc/ fstab 中进行配置，如下图所示：

![HANA 大型实例单元中已装载卷的 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 大型实例单元上的命令 df -h 的输出如下所示：

![HANA 大型实例单元中已装载卷的 fstab](./media/hana-installation/image2_df_output.PNG)


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

根据 [SAP 支持说明 #1999997 - FAQ：SAP HANA 内存](https://launchpad.support.sap.com/#/notes/1999997/E)，交付的 OS 映像的交换空间设置为 2GB。 任何所需的不同设置需要由作为客户的你进行设置。

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/hana) 是针对 Azure SAP HANA（大型实例）安装的 Linux 分发。 此特定分发提供特定于 SAP 的&quot;现成&quot;功能（包括在 SLES 上高效运行 SAP 所需的预设参数）。

请参阅 SUSE 网站上的[资源库/白皮书](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)以及 SAP 社区网络 (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（基于 SUSE 的 SAP），了解多种与部署 SLES SAP HANA 相关的有用资源（包括设置高可用性、特定于 SAP 操作的安全强化等）。

与 SUSE 上的 SAP 相关的其他实用链接：

- [SAP HANA on SUSE Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（SUSE Linux 站点上的 SAP HANA）
- [Best Practice for SAP: Enqueue Replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/docrepcontent/container.jsp?containerId=9113)（SAP 最佳实践：排队复制 - 基于 SUSE Linux Enterprise 12 的 SAP NetWeaver）。
- [ClamSAP – SLES Virus Protection for SAP](http://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)（ClamSAP - 适用于 SAP 的 SLES 病毒防护）（包括 SLES 12 for SAP Applications）。

适用于在 SLES 12 上实现 SAP HANA 的 SAP 支持说明：

- [SAP Support Note #1944799 – SAP HANA Guidelines for SLES Operating System Installation](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)（SAP 支持说明 #1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指南）。
- [SAP Support Note #2205917 – SAP HANA DB Recommended OS Settings for SLES 12 for SAP Applications](https://launchpad.support.sap.com/#/notes/2205917/E)（SAP 支持说明 #2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的操作系统设置）。
- [SAP Support Note #1984787 – SUSE Linux Enterprise Server 12:  Installation Notes](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #1984787 - SUSE Linux Enterprise Server 12：安装说明）。
- [SAP Support Note #171356 – SAP Software on Linux:  General Information](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #171356 - Linux 上的 SAP 软件：常规信息）。
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）。

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) 是用于在 HANA 大型实例上运行 SAP HANA 的另一个产品。 现在有 RHEL 6.7 和 7.2 版本可用。 请注意，与仅支持 RHEL 7.2 及更高版本的本机 Azure VM 不同，HANA 大型实例还支持 RHEL 6.7。 但是我们建议使用 RHEL 7.x 版本。

其他与基于 Red Hat 的 SAP 相关的有用链接：
- [SAP HANA on Red Hat Linux Site](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)（“基于 Red Hat Linux 的 SAP HANA”站点）。

适用于在 Red Hat 上实现 SAP HANA 的 SAP 支持说明：

- [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879/E)（SAP 支持说明 #2009879 - Red Hat Enterprise Linux (RHEL) 操作系统的 SAP HANA 指南）。
- [SAP Support Note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议操作系统设置）。
- [SAP Support Note #2247020 - SAP HANA DB: Recommended OS settings for RHEL 6.7](https://launchpad.support.sap.com/#/notes/2247020)（SAP 支持说明 #2247020 - SAP HANA DB：RHEL 6.7 的建议操作系统设置）。
- [SAP Support Note #1391070 – Linux UUID Solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）。
- [SAP Support Note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351)（SAP 支持说明 #2228351 - Linux：基于 RHEL 6 或 SLES 11 的 SAP HANA Database SPS 11 修订版 110（或更高版本））。
- [SAP Support Note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)（SAP 支持说明 #2397039 - 常见问题解答：基于 RHEL 的 SAP）。
- [SAP Support Note #1496410 - Red Hat Enterprise Linux 6.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/1496410)（SAP 支持说明 #1496410 - Red Hat Enterprise Linux 6.x：安装和升级）。
- [SAP Support Note #2002167- Red Hat Enterprise Linux 7.x: Installation and Upgrade](https://launchpad.support.sap.com/#/notes/2002167)（SAP 支持说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级）。

## <a name="time-synchronization"></a>时间同步

如果 SAP 应用程序是在 SAP NetWeaver 体系结构基础之上生成，则对 SAP 系统各组成部分的时间差异非常敏感。 可能会对错误标题为 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 短转储非常熟悉，因为当不同服务器或 VM 的系统时间差异过大时，就会出现这些短转储。

就 Azure SAP HANA（大型实例）来说，在 Azure 中进行的时间同步不适用于大型实例戳中的计算单元。 这种同步不适用于在本机 Azure VM 中运行的 SAP 应用程序，因为 Azure 可确保正确同步系统时间。 因此，必须设置单独的时间服务器，供在 Azure VM 上运行的 SAP 应用程序服务器以及在 HANA 大型实例上运行的 SAP HANA 数据库实例使用。 大型实例戳中的存储基础结构与 NTP 服务器进行时间同步。

## <a name="setting-up-smt-server-for-suse-linux"></a>为 SUSE Linux 安装 SMT 服务器
SAP HANA 大型实例不直接连接 Internet。 因此，无法直接向 OS 提供程序注册此类单元，也无法直接下载并应用修补程序。 如果是 SUSE Linux，一种解决方案是在 Azure VM 中安装 SMT 服务器。 而 Azure VM 需要托管在连接到 HANA 大型实例的 Azure VNet 中。 通过此类 SMT 服务器，HANA 大型实例单元可以注册并下载修补程序。 

SUSE 在[用于 SLES 12 SP2 的订阅管理工具](https://www.suse.com/documentation/sles-12/pdfdoc/book_smt/book_smt.pdf)中提供了更多指南。 

作为完成 HANA 大型实例任务的 SMT 服务器的安装前提条件，将需要：

- 连接到 HANA 大型实例 ER 线路的 Azure VNet。
- 与组织相关联的 SUSE 帐户。 而组织则需要拥有有效的 SUSE 订阅。

### <a name="installation-of-smt-server-on-azure-vm"></a>在 Azure VM 上安装 SMT 服务器

在这一步中，将在 Azure VM 中安装 SMT 服务器。 第一步是登录 [SUSE 客户中心](https://scc.suse.com/)

登录后，依次转到“组织”-->“组织凭据”。 在此部分中，应能找到安装 SMT 服务器所需的凭据。

第三步是在 Azure VNet 中安装 SUSE Linux VM。 若要部署此 VM，请获取 Azure 的 SLES 12 SP2 库映像。 在部署过程中，请勿定义 DNS 名称，也不要使用此屏幕截图中的静态 IP 地址

![SMT 服务器的 VM 部署](./media/hana-installation/image3_vm_deployment.png)

部署的 VM 为较小 VM，在地址为 10.34.1.4 的 Azure VNet 中获取内部 IP 地址。 VM 名称为 smtserver。 安装完成后，检查了与 HANA 大型实例单元的连接。 可能需要在 Azure VM 的 etc/hosts 中配置 HANA 大型实例单元解析，具体视名称解析的组织方式而定。 向用于保留修补程序的 VM 额外添加磁盘。 启动盘本身可能太小。 在此示例中，磁盘装载到 /srv/www/htdocs，如下面的屏幕截图所示。 100GB 磁盘应该就足够了。

![SMT 服务器的 VM 部署](./media/hana-installation/image4_additional_disk_on_smtserver.PNG)

登录 HANA 大型实例单元，维护 /etc/hosts，并检查能否到达应通过网络运行 SMT 服务器的 Azure VM。

成功完成此检查后，需要登录应运行 SMT 服务器的 Azure VM。 若要使用 putty 登录 VM，需要在 bash 窗口中按以下顺序运行命令：

```
cd ~
echo "export NCURSES_NO_UTF8_ACS=1" >> .bashrc
```

运行这些命令后，重启 bash 以激活设置。 然后，启动 YAST。

在 YAST 中，转到“软件维护”并搜索 smt。 选择“smt”后会自动切换到“yast2-smt”，如下所示

![YAST 中的 SMT](./media/hana-installation/image5_smt_in_yast.PNG)


接受 smtserver 上的安装选择。 安装完成后，立即转到 SMT 服务器配置，并输入之前从 SUSE 客户中心获取的组织凭据。 此外，还请输入 Azure VM 主机名，作为 SMT 服务器 URL。 在此示例中，它是 https://smtserver，如下图所示。

![SMT 服务器配置](./media/hana-installation/image6_configuration_of_smtserver1.png)

接下来，需要测试与 SUSE 客户中心的连接是否有效。 如下图所示，在此示例中，连接确实有效。

![测试与 SUSE 客户中心的连接](./media/hana-installation/image7_test_connect.png)

在 SMT 安装程序启动后，需要输入数据库密码。 由于这是新安装，因此需要定义数据库密码，如下图所示。

![定义数据库密码](./media/hana-installation/image8_define_db_passwd.PNG)

下一次交互是在证书创建完成时。 浏览对话框（如下图所示），应该能够继续执行步骤。

![创建 SMT 服务器证书](./media/hana-installation/image9_certificate_creation.PNG)

配置的最后一步“运行同步检查”可能需要几分钟的时间才能完成。 安装和配置 SMT 服务器后，应该会在装入点 /srv/www/htdocs 下找到目录存储库，并能在存储库下找到一些子目录。 

运行以下命令，重启 SMT 服务器及其相关服务。

```
rcsmt restart
systemctl restart smt.service
systemctl restart apache2
```

### <a name="download-of-packages-onto-smt-server"></a>将程序包下载到 SMT 服务器上

重启所有服务后，使用 YAST 在“SMT 管理”中选择相应程序包。 程序包的选择依据是 HANA 大型实例服务器的 OS 映像，而不是运行 SMT 服务器的 VM 的 SLES 发行版或版本。 程序包选择屏幕示例如下所示。

![选择程序包](./media/hana-installation/image10_select_packages.PNG)

选择完程序包后，需要开始将选择的程序包首次复制到安装的 SMT 服务器。 在 shell 中使用命令 smt-mirror 触发此次复制，如下所示


![将程序包下载到 SMT 服务器](./media/hana-installation/image11_download_packages.PNG)

如上所示，应将程序包复制到装入点 /srv/www/htdocs 下创建的目录。 此过程可能需要一段时间才能完成。 可能需要一个小时或更长时间，具体视选择的程序包数量而定。
此过程完成之后，需要继续安装 SMT 客户端。 

### <a name="set-up-the-smt-client-on-hana-large-instance-units"></a>在 HANA 大型实例单元上安装 SMT 客户端

在此示例中，客户端为 HANA 大型实例单元。 SMT 服务器安装程序将脚本 clientSetup4SMT.sh 复制到了 Azure VM 中。 将此脚本复制到要连接到 SMT 服务器的 HANA 大型实例单元中。 使用 -h 选项启动此脚本，并将 SMT 服务器名称作为参数传递给它。 在此示例中，名称为 smtserver。

![配置 SMT 客户端](./media/hana-installation/image12_configure_client.PNG)

可能会遇到以下情况：客户端从服务器成功加载证书，但无法进行注册，如下所示。

![客户端注册失败](./media/hana-installation/image13_registration_failed.PNG)

如果无法注册，请阅读这篇 [SUSE 支持文档](https://www.suse.com/de-de/support/kb/doc/?id=7006024)，并执行其中所述步骤。

> [!IMPORTANT] 
> 需要提供 VM 名称（在此示例中为 smtserver）作为服务器名称，无需提供完全限定的域名。 只需提供 VM 名称即可。 

执行完这些步骤后，需要在 HANA 大型实例单元上运行以下命令

```
SUSEConnect –cleanup
```

> [!Note] 
> 在我们的测试中，始终都需要在这一步后等待几分钟。 如果在采取 SUSE 文章中介绍的纠正措施后直接运行 clientSetup4SMT.sh，最后会看到指明证书尚未生效的消息。 通常需要先等待 5-10 分钟，再运行 clientSetup4SMT.sh，这样就可以成功完成客户端配置。

如果遇到需要根据 SUSE 文章中的步骤进行修复的问题，必须在 HANA 大型实例单元上再次重启 clientSetup4SMT.sh。 现在，应该可以成功完成了，如下所示。

![客户端注册成功](./media/hana-installation/image14_finish_client_config.PNG)

在这一步中，将 HANA 大型实例单元的 SMT 客户端配置为，连接在 Azure VM 中安装的 SMT 服务器。 现在可以使用“zypper up”或“zypper in”，将 OS 修补程序安装到 HANA 大型实例或安装其他程序包。 不言而喻，只能获取之前在 SMT 服务器上下载的修补程序。


## <a name="example-of-an-sap-hana-installation-on-hana-large-instances"></a>HANA 大型实例上的 SAP HANA 安装示例
此部分介绍了如何在 HANA 大型实例单元上安装 SAP HANA。 初始状态如下：

- 向 Microsoft 提供了部署 SAP HANA 大型实例所需的全部数据。
- 从 Microsoft 收到了 SAP HANA 大型实例。
- 创建了连接到本地网络的 Azure VNet。
- 将 HANA 大型实例的 ExpressRotue 线路与同一 Azure VNet 相连。
- 安装了用作 HANA 大型实例的跳转盒的 Azure VM。
- 确保可以从跳转盒连接到 HANA 大型实例单元，反之亦然。
- 已检查是否安装了所有必要的程序包和修补程序。
- 阅读了有关在要使用的 OS 上安装 HANA 的 SAP 说明和文档，并确保 OS 版本支持选择的 HANA 版本。

接下来需要完成的步骤是，将 HANA 安装程序包下载到跳转盒 VM（在此示例中，是在 Windows OS 上运行），将程序包复制到 HANA 大型实例单元，并执行安装程序。

### <a name="download-of-the-sap-hana-installation-bits"></a>下载 SAP HANA 安装位包
由于 HANA 大型实例单元不直接连接 Internet，因此无法直接将安装程序包从 SAP 下载到 HANA 大型实例 VM。 若要解决不直接连接 Internet 的问题，需要使用跳转盒。 将程序包下载到跳转盒 VM。

若要下载 HANA 安装程序包，需要以 SAP S 用户或其他用户身份下载，以便可以访问 SAP市场。 登录后，请依次转到下列屏幕：

转到 [SAP Service Marketplace](https://support.sap.com/en/index.html) &gt; 单击“下载软件”&gt;“安装和升级”&gt; 使用按字母顺序排列的索引 &gt; 在“H”下选择“SAP HANA 平台版本”&gt;“SAP HANA 平台版 2.0”&gt;“安装”&gt; 下载以下文件

![下载 HANA 安装程序包](./media/hana-installation/image16_download_hana.PNG)

在此示例中，我们下载了 SAP HANA 2.0 安装程序包。 在 Azure 跳转盒 VM 中，将自解压存档展开到目录中，如下所示。

![解压缩 HANA 安装程序包](./media/hana-installation/image17_extract_hana.PNG)

解压缩存档期间，将解压缩创建的目录（在此示例中为 51052030）复制到 HANA 大型实例单元的 /hana/shared 卷中的已创建目录。

> [!Important]
> 请勿将安装程序包复制到根或启动 LUN 中，这是因为空间有限，并且其他进程也需要使用安装程序包。


### <a name="install-sap-hana-on-the-hana-large-instance-unit"></a>在 HANA 大型实例单元上安装 SAP HANA
若要安装 SAP HANA，需要以用户根身份登录。 只有当根拥有足够权限时，才能安装 SAP HANA。
首先，需要设置对复制到 /hana/shared 的目录的权限。 需要设置如下权限

```
chmod –R 744 <Installation bits folder>
```

若要使用图形安装程序安装 SAP HANA，需要在 HANA 大型实例上安装 gtk2 包。 运行以下命令，检查是否已安装此包

```
rpm –qa | grep gtk2
```

在后续步骤中，我们将展示 SAP HANA 安装程序的图形用户界面。 接下来，依次转到安装目录和子目录 HDB_LCM_LINUX_X86_64。 开始

```
./hdblcmgui 
```
从此目录入手。 现在将通过一系列屏幕逐步介绍如何安装，需要在这些屏幕中提供安装数据。 在此示例中，我们将安装 SAP HANA 数据库服务器和 SAP HANA 客户端组件。 因此，我们选择的是“SAP HANA 数据库”，如下所示

![选择安装 HANA](./media/hana-installation/image18_hana_selection.PNG)

在下一屏幕中，选择“安装新系统”选项

![选择新安装 HANA](./media/hana-installation/image19_select_new.PNG)

执行这一步后，需要在可以另外安装到 SAP HANA 数据库服务器的其他几个组件之间进行选择。

![选择其他 HANA 组件](./media/hana-installation/image20_select_components.PNG)

在本文档中，我们选择了 SAP HANA 客户端和 SAP HANA Studio。 我们还安装了纵向扩展实例。 因此，在下一屏幕中，需要选中“单主机系统” 

![选择安装纵向扩展实例](./media/hana-installation/image21_single_host.PNG)

在下一屏幕中，需要提供一些数据

![提供 SAP HANA SID](./media/hana-installation/image22_provide_sid.PNG)

> [!Important]
> 提供的 HANA 系统 ID (SID) 必须与在订购 HANA 大型实例部署时向 Microsoft 提供的数据一致。 选择不同的 SID 会导致安装失败，原因是不同卷带来的访问权限问题

使用 /hana/shared 目录作为安装目录。 在下一步中，需要提供 HANA 数据文件和 HANA 日志文件的位置


![提供 HANA 日志位置](./media/hana-installation/image23_provide_log.PNG)

> [!Note]
> 应将装入点（其中包含在此屏幕的上一选择屏幕中选择的 SID）随附的卷定义为数据和日志文件。 如果 SID 与在前面的屏幕中键入的内容不一致，请返回并将 SID 调整为装入点上的值。

在下一步中，检查主机名并进行最终更正。 

![检查主机名](./media/hana-installation/image24_review_host_name.PNG)

在下一步中，还需要检索在订购 HANA 大型实例部署时向 Microsoft 提供的数据。 

![提供系统用户 UID 和 GID](./media/hana-installation/image25_provide_guid.PNG)

> [!Important]
> 提供的系统用户 ID 和用户组 ID 必须与在订购单元部署时向 Microsoft 提供的数据一致。 如果提供的 ID 不完全相同，将无法在 HANA 大型实例单元上安装 SAP HANA。

在接下来的两个屏幕（本文档未显示）中，需要提供 SAP HANA 数据库的系统用户密码和 sapadm 用户密码（用于作为 SAP HANA 数据库实例的一部分安装的 SAP 主机代理）。

定义密码后，便会看到确认屏幕。 检查列出的所有数据，并继续安装。 此时，系统会显示安装进度屏幕，如下所示

![查看安装进度](./media/hana-installation/image27_show_progress.PNG)

安装完成后，应该会看到下图

![安装完成](./media/hana-installation/image28_install_finished.PNG)

此时，SAP HANA 实例应该已创建完成，可以开始使用了。 应该能够从 SAP HANA Studio 连接到它。 此外，还请务必检查最新 SAP HANA 修补程序，并应用这些修补程序。
























































 







 




