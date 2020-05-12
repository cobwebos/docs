---
title: 在 Azure SAP HANA（大型实例）上安装 SAP HANA | Microsoft Docs
description: 如何在 Azure 上的 SAP HANA （大型实例）上安装 SAP HANA。
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/16/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4db072cf881c936db6721845e7823082388515b0
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83117115"
---
# <a name="how-to-install-and-configure-sap-hana-large-instances-on-azure"></a>如何安装和配置 Azure 上的 SAP HANA（大型实例）

在阅读本文之前，请熟悉 [HANA 大型实例常用术语](hana-know-terms.md)和 [HANA 大型实例 SKU](hana-available-skus.md)。

SAP HANA 的安装由你负责。 可以在 Azure 虚拟网络与 HANA 大型实例单元之间建立连接之后再开始安装新的 Azure 上的 SAP HANA（大型实例）。 

> [!Note]
> 按照 SAP 策略，SAP HANA 的安装必须由经过认证的 SAP 技术关联考试、SAP HANA 安装认证考试，或者是经过 SAP 认证的系统集成商（SI）的人员执行。

规划 HANA 2.0 的安装时，请参阅 [SAP 支持说明 #2235581 - SAP HANA：支持的操作系统](https://launchpad.support.sap.com/#/notes/2235581/E)，确保所要安装的 SAP HANA 版本支持该 OS。 HANA 2.0 支持的 OS 比 HANA 1.0 支持的 OS 限制性更强。 还需要检查是否为此已发布[列表](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)上的特定 b-hli 单元列出了你感兴趣的操作系统版本。 单击该单元可获取该设备支持的操作系统列表的完整详细信息。 

在开始安装 HANA 之前，请先验证以下内容：
- [HLI 单元](#validate-the-hana-large-instance-units)
- [操作系统配置](#operating-system)
- [网络配置](#networking)
- [存储配置](#storage)


## <a name="validate-the-hana-large-instance-units"></a>验证 HANA 大型实例单元

从 Microsoft 收到 HANA 大型实例单元后，请验证以下设置并根据需要进行调整。

接收 HANA 大型实例并建立与实例的访问和连接的**第一步**是签入 Azure 门户是否将实例显示为正确的 SKU 和操作系统。 [通过 Azure 门户读取 AZURE HANA 大型实例控制](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-li-portal)，以执行检查所需的步骤。

接收 HANA 大型实例并建立与实例的访问和连接后，**第二步**是将实例的 os 注册到 os 提供程序。 此步骤在要部署到 Azure VM 的 SUSE SMT 实例中注册 SUSE Linux OS。 

HANA 大型实例单元可以连接到此 SMT 实例。 （有关详细信息，请参阅[如何为 SUSE Linux 设置 SMT 服务器](hana-setup-smt.md)）。 或者，需要在所要连接的 Red Hat 订阅管理器中注册 Red Hat OS。 有关详细信息，请参阅[什么是 Azure 上的 SAP HANA（大型实例）？](https://docs.microsoft.com/azure/virtual-machines/linux/sap-hana-overview-architecture?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中的备注。 

此步骤对于修补操作系统（客户的责任）是必需的。 对于 SUSE，可在有关 [SMT 安装](https://www.suse.com/documentation/sles-12/book_smt/data/smt_installation.html)的页面中找到有关安装和配置 SMT 的文档。

**第三步**是检查特定 OS 发行版/版本的新修补程序和修补程序。 验证 HANA 大型实例的修补级别是否处于最新状态。 有时可能未包含最新修补程序。 在接管 HANA 大型实例单元后，必须检查是否需要应用修补程序。

**第四步**是查看相关的 SAP 说明，以便在特定 OS 版本/版本上安装和配置 SAP HANA。 由于建议会不断发生变化，或者与各种安装方案相关的 SAP 说明或配置会发生更改，因此，Microsoft 不一定总能完美配置 HANA 大型实例单元。 

因此，作为客户，必须阅读与确切 Linux 发行版上的 SAP HANA 相关的 SAP 说明。 还要检查 OS 发行版/版本的配置，并应用尚未应用的配置设置。

具体而言，请检查以下参数，最终将其调整为：

- net.core.rmem_max = 16777216
- net.core.wmem_max = 16777216
- net.core.rmem_default = 16777216
- net.core.wmem_default = 16777216
- net.core.optmem_max = 16777216
- net.ipv4.tcp_rmem = 65536 16777216 16777216
- net.ipv4.tcp_wmem = 65536 16777216 16777216

从 SLES12 SP1 和 RHEL 7.2 开始，必须在 /etc/sysctl.d 目录中的某个配置文件内设置这些参数。 例如，必须创建名为 91-NetApp-HANA.conf 的配置文件。 对于旧版 SLES 和 RHEL，必须在 in/etc/sysctl.conf 中设置这些参数。

对于从 RHEL 6.3 开始的所有 RHEL 版本，请记住： 
- 必须在 in/etc/modprobe.d/sunrpc-local.conf 中设置 sunrpc.tcp_slot_table_entries = 128 参数。 如果该文件不存在，则需要首先通过添加条目来创建它： 
    - options sunrpc tcp_max_slot_table_entries=128

**第五步**是检查 HANA 大型实例单元的系统时间。 部署实例时使用的是系统时区。 此时区表示 HANA 大型实例阵列所在 Azure 区域的位置。 可以更改自己拥有的实例的系统时间或时区。 

如果在租户中订购了更多的实例，则需要调整新交付的实例的时区。 在交接后，Microsoft 并不了解你在实例上设置的系统时区。 因此，新部署的实例可能不会在与切换到的实例相同的时区中设置。 在必要时，客户需负责调整交接后的实例的时区。 

**第六步**是检查 etc/hosts。 交接刀片服务器后，会根据不同的用途为它们分配不同的 IP 地址。 检查 etc/hosts 文件。 如果将单元添加到了现有租户，请不要料想会使用以前交付的系统的 IP 地址正确维护新部署的系统的 etc/hosts。 客户需负责确保新部署的实例能够与租户中以前部署的单元交互并解析其名称。 


## <a name="operating-system"></a>操作系统

根据 [SAP 支持说明 #1999997 - 常见问题解答：SAP HANA 内存](https://launchpad.support.sap.com/#/notes/1999997/E)，交付的 OS 映像的交换空间设置为 2GB。 如果客户需要不同的设置，则必须自行设置。

[SUSE Linux Enterprise Server 12 SP1 for SAP Applications](https://www.suse.com/products/sles-for-sap/download/) 是为 Azure SAP HANA（大型实例）安装的 Linux 分发版。 此特定分发版提供特定于 SAP 的现成功能（包括在 SLES 上高效运行 SAP 所需的预设参数）。

请参阅 SUSE 网站上的[资源库/白皮书](https://www.suse.com/products/sles-for-sap/resource-library#white-papers)以及 SAP 社区网络 (SCN) 上的 [SAP on SUSE](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)（基于 SUSE 的 SAP），了解多种与部署 SLES SAP HANA 相关的有用资源（包括设置高可用性、特定于 SAP 操作的安全强化等）。

下面是与 SAP on SUSE 相关的其他有用链接：

- [SAP HANA on SUSE Linux 站点](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+SUSE)
- [Best practices for SAP: Enqueue replication – SAP NetWeaver on SUSE Linux Enterprise 12](https://www.suse.com/media/guide/SLES4SAP-NetWeaver-ha-guide-EnqRepl-12_color_en.pdf)（SAP 最佳做法：排队复制 - SAP NetWeaver on SUSE Linux Enterprise 12）
- [ClamSAP – SLES virus protection for SAP](https://scn.sap.com/community/linux/blog/2014/04/14/clamsap--suse-linux-enterprise-server-integrates-virus-protection-for-sap)（ClamSAP - 适用于 SAP 的 SLES 病毒防护）（包括 SLES 12 for SAP Applications）

下面是适用于实施 SAP HANA on SLES 12 的 SAP 支持说明：

- [SAP support note #1944799 – SAP HANA guidelines for SLES operating system installation](http://service.sap.com/sap/support/notes/1944799)（SAP 支持说明 #1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指导原则）
- [SAP support note #2205917 – SAP HANA DB recommended OS settings for SLES 12 for SAP applications](https://launchpad.support.sap.com/#/notes/2205917/E)（SAP 支持说明 #2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的 OS 设置）
- [SAP support note #1984787 – SUSE Linux Enterprise Server 12:  installation notes](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #1984787 - SUSE Linux Enterprise Server 12：安装说明）
- [SAP support note #171356 – SAP software on Linux:  General information](https://launchpad.support.sap.com/#/notes/1984787)（SAP 支持说明 #171356 - Linux 上的 SAP 软件：常规信息）
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）

[Red Hat Enterprise Linux for SAP HANA](https://www.redhat.com/en/resources/red-hat-enterprise-linux-sap-hana) 是用于在 HANA 大型实例上运行 SAP HANA 的另一个产品。 RHEL 7.2 和7.3 版本可用且受支持。 

下面是与 SAP on Red Hat 相关的其他有用链接：
- [SAP HANA on Red Hat Linux 站点](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+on+Red+Hat)。

下面是适用于实施 SAP HANA on Red Hat 的 SAP 支持说明：

- [SAP support note #2009879 - SAP HANA guidelines for Red Hat Enterprise Linux (RHEL) operating system](https://launchpad.support.sap.com/#/notes/2009879/E)（SAP 支持说明 #2009879 - 适用于 Red Hat Enterprise Linux (RHEL) 操作系统的 SAP HANA 指导原则）
- [SAP support note #2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（SAP 支持说明 #2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
- [SAP support note #1391070 – Linux UUID solutions](https://launchpad.support.sap.com/#/notes/1391070)（SAP 支持说明 #1391070 - Linux UUID 解决方案）
- [SAP support note #2228351 - Linux: SAP HANA Database SPS 11 revision 110 (or higher) on RHEL 6 or SLES 11](https://launchpad.support.sap.com/#/notes/2228351)（SAP 支持说明 #2228351 - Linux：RHEL 6 或 SLES 11 上的 SAP HANA Database SPS 11 修订版 110（或更高版本））
- [SAP support note #2397039 - FAQ: SAP on RHEL](https://launchpad.support.sap.com/#/notes/2397039)（SAP 支持说明 #2397039 - 常见问题解答：SAP on RHEL）
- [SAP support note #2002167 - Red Hat Enterprise Linux 7.x: Installation and upgrade](https://launchpad.support.sap.com/#/notes/2002167)（SAP 支持说明 #2002167 - Red Hat Enterprise Linux 7.x：安装和升级）

### <a name="time-synchronization"></a>时间同步

在 SAP NetWeaver 体系结构基础之上生成的 SAP 应用程序对构成 SAP 系统的组件的时差非常敏感。 可能经常会出现标题为 ZDATE\_LARGE\_TIME\_DIFF 的 SAP ABAP 简短转储。 这是因为，当不同服务器或 VM 的系统时差过大时，就会出现这些简短转储。

就 Azure SAP HANA（大型实例）而言，在 Azure 中进行的时间同步不适用于大型实例阵列中的计算单元。 这种同步不适用于在本机 Azure VM 中运行的 SAP 应用程序，因为 Azure 可确保正确同步系统时间。 

因此，必须设置单独的时间服务器，供在 Azure VM 上运行的 SAP 应用程序服务器以及在 HANA 大型实例上运行的 SAP HANA 数据库实例使用。 大型实例阵列中的存储基础结构与 NTP 服务器进行时间同步。


## <a name="networking"></a>网络
假设已遵循以下文档中的建议设计了 Azure 虚拟网络并将这些虚拟网络连接到了 HANA 大型实例：

- [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)
- [Azure 上的 SAP HANA （大型实例）基础结构和连接](hana-overview-infrastructure-connectivity.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

在单个单元的网络方面，有些详细信息值得注意。 每个 HANA 大型实例单元附带两个或三个 IP 地址，这些地址已分配到两个或三个 NIC 端口。 HANA 横向扩展配置和 HANA 系统复制方案中使用三个 IP 地址。 分配给单元 NIC 的一个 IP 地址位于 [Azure 上的 SAP HANA（大型实例）概述和体系结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture)中所述的服务器 IP 池外部。

有关体系结构以太网的详细信息，请参阅 [HLI 支持的方案](hana-supported-scenario.md)。

## <a name="storage"></a>存储

Azure 上的 SAP HANA （大型实例）的存储布局是通过 Azure 上的 SAP HANA Azure `service management` 建议指导原则配置的。 [SAP HANA 存储要求](https://go.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)白皮书中阐述了这些指导原则。 

有关包含不同 HANA 大型实例 SKU 的各卷的粗略大小，请参阅 [Azure 上的 SAP HANA（大型实例）概述和体系结构](hana-overview-architecture.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

下表列出了存储卷的命名约定：

| 存储用途 | 装入点名称 | 卷名 | 
| --- | --- | ---|
| HANA 数据 | /hana/data/SID/mnt0000 \< m> | 存储 IP：/hana_data_SID_mnt00001_tenant_vol |
| HANA 日志 | /hana/log/SID/mnt0000 \< m> | 存储 IP：/hana_log_SID_mnt00001_tenant_vol |
| HANA 日志备份 | /hana/log/backups | 存储 IP：/hana_log_backups_SID_mnt00001_tenant_vol |
| HANA 共享 | /hana/shared/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/shared |
| usr/sap | /usr/sap/SID | 存储 IP：/hana_shared_SID_mnt00001_tenant_vol/usr_sap |

*SID* 表示 HANA 实例系统 ID。 

*Tenant* 表示部署租户时的操作的内部枚举。

HANA usr/sap 共享同一个卷。 装入点的命名法包括 HANA 实例系统 ID 和装入编号。 纵向扩展部署中只有一个装入点，例如 mnt00001。 另一方面，在横向扩展部署中，装入点数量与工作节点和主节点一样多。 

对于横向扩展环境，数据、日志和日志备份卷都会共享并附加到横向扩展配置中的每个节点。 对于运行多个 SAP 实例的配置，将创建一组不同的卷，并将其附加到 HANA 大型实例单元。 有关方案存储布局的详细信息，请参阅 [HLI 支持的方案](hana-supported-scenario.md)。

查看 HANA 大型实例单元时，你会发现，单元随附了相当大的 HANA/data 磁盘卷，并且还有一个 HANA/log/backup 卷。 我们提供这么大的 HANA/data 卷的原因是，为客户提供的存储快照使用同一个磁盘卷。 执行的存储快照越多，分配的存储卷中的快照占用的空间就越多。 

HANA/log/backup 卷并非旨在用作数据库备份的卷。 它的大小适合用作 HANA 事务日志备份的备份卷。 有关详细信息，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 

除了提供的存储以外，还可以购买更多存储容量，增量为 1-TB。 可以将此附加存储作为新卷添加到 HANA 大型实例。

使用 Azure 上的 SAP HANA 在载入期间 `service management` ，客户将为 sidadm 用户和 sapsys 组指定用户 ID （UID）和组 ID （GID）（例如：1000500）。 在安装 SAP HANA 系统期间，必须使用与此相同的值。 由于要在一个单元上部署多个 HANA 实例，因此将获得多个卷集（每个实例有一个集）。 因此，需要在部署时定义：

- 不同 HANA 实例的 SID（sidadm 由此派生）。
- 不同 HANA 实例的内存大小。 每个实例的内存大小定义各个卷集的卷大小。

根据存储提供程序的建议，为所有已装载卷（不包括启动 LUN）配置以下装入选项：

- nfs    rw, vers=4, hard, timeo=600, rsize=1048576, wsize=1048576, intr, noatime, lock 0 0

这些装入点在 /etc/ fstab 中进行配置，如下图所示：

![HANA 大型实例单元中已装载卷的 fstab](./media/hana-installation/image1_fstab.PNG)

S72m HANA 大型实例单元上的命令 df -h 的输出如下所示：

![HANA 大型实例单元中已装载卷的 fstab](./media/hana-installation/image2_df_output.PNG)


大型实例戳中的存储控制器和节点将同步到 NTP 服务器。 针对 NTP 服务器同步 Azure SAP HANA（大型实例）单元和 Azure VM 之后，在 Azure 或大型实例阵列中的基础结构与计算单元之间就不会出现明显的时间偏差。

若要优化 SAP HANA，使其不超过使用的存储，请设置以下 SAP HANA 配置参数：

- max_parallel_io_requests 128
- async_read_submit on
- async_write_submit_active on
- async_write_submit_blocks all
 
对于 SAP HANA 1.0 SPS12 或更低版本，可根据 [SAP 说明 #2267798 - SAP HANA 数据库的配置](https://launchpad.support.sap.com/#/notes/2267798)中所述，在安装 SAP HANA 数据库期间设置这些参数

也可以在安装 SAP HANA 数据库之后使用 hdbparam 框架配置这些参数。 

HANA 大型实例中使用的存储具有文件大小限制。 [大小限制为](https://docs.netapp.com/ontap-9/index.jsp?topic=%2Fcom.netapp.doc.dot-cm-vsmg%2FGUID-AA1419CF-50AB-41FF-A73C-C401741C847C.html)每个文件 16 TB。 不同于 EXT3 文件系统中的文件大小限制，HANA 不会隐式识别 HANA 大型实例存储所强制执行的存储限制。 因此，当达到16TB 的文件大小限制时，HANA 不会自动创建新的数据文件。 由于 HANA 尝试将文件增长到超过 16 TB，因此，HANA 将报告错误，并且索引服务器将在结尾崩溃。

> [!IMPORTANT]
> 为了防止 HANA 尝试将数据文件增长到 HANA 大型实例存储的 16 TB 文件大小限制之外，需要在 SAP HANA 的 global.asa 配置文件中设置以下参数
> 
> - datavolume_striping = true
> - datavolume_striping_size_gb = 15000
> - 另请参阅 SAP 说明[#2400005](https://launchpad.support.sap.com/#/notes/2400005)
> - 请注意 SAP 说明[#2631285](https://launchpad.support.sap.com/#/notes/2631285)


在 SAP HANA 2.0 中，hdbparam 框架已弃用。 因此，必须使用 SQL 命令设置这些参数。 有关详细信息，请参阅 [SAP 说明 #2399079：HANA 2 中已弃用 hdbparam](https://launchpad.support.sap.com/#/notes/2399079)。

请参阅 [HLI 支持的方案](hana-supported-scenario.md)，详细了解体系结构的存储布局。


**后续步骤**

- 参阅 [HLI 上的 HANA 安装](hana-example-installation.md)










































 







 




