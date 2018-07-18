---
title: 快速入门：在 Azure 虚拟机上手动安装单实例 SAP HANA | Microsoft Docs
description: 有关在 Azure 虚拟机上手动安装单实例 SAP HANA 的快速入门指南
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 14cdb2d3e433da38913ffa29b3b150bdb264278b
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2018
ms.locfileid: "34658700"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>快速入门：在 Azure VM 上手动安装单实例 SAP HANA
## <a name="introduction"></a>介绍
手动安装 SAP NetWeaver 7.5 和 SAP HANA 1.0 SP12 时，本入门指南可指导用户在 Azure 虚拟机 (VM) 上设置单实例 SAP HANA。 本指南重点介绍在 Azure 上部署 SAP HANA。 它不能替代 SAP 文档。 

>[!Note]
>本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关将 SAP HANA 部署到 HANA 大型实例的信息，请参阅[使用 Azure 虚拟机 (VM) 上的 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。
 
## <a name="prerequisites"></a>先决条件
本指南假定使用者熟悉如下基础结构即服务 (IaaS) 基础知识：
 * 如何通过 Azure 门户或 PowerShell 部署虚拟机或虚拟网络。
 * Azure 跨平台命令行接口 (CLI)，包括用来使用 JavaScript 对象表示法 (JSON) 模板的选项。

本指南还假定使用者熟悉以下内容：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 在 Azure 上安装和操作 SAP HANA 和 SAP 应用程序实例。
* 下面的概念和过程关于：
   * 规划 Azure 上的 SAP 部署，包括 Azure 虚拟网络规划和 Azure 存储使用。 请参阅 [Azure 虚拟机 (VM) 上的 SAP NetWeaver - 规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅[适用于 SAP 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。
   * Azure 上 SAP NetWeaver ASCS（ABAP SAP 中心服务）、SCS（SAP 中心服务）和 ERS（估计的收货结算）的高可用性。 请参阅 [Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)。
   * 有关如何提高 Azure 上 ASCS/SCS 多 SID 安装的利用效率的详细信息。 请参阅[创建 SAP NetWeaver 多 SID 配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)。 
   * 在 Azure 中运行 SAP NetWeaver（基于 Linux 驱动的 VM）的原则。 请参阅[在 Microsoft Azure SUSE Linux VM 上运行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)。 本指南介绍 Azure VM 中适用于 Linux 的具体设置，还将详细介绍如何正确地将 Azure 存储磁盘附加到 Linux VM。

目前，Azure VM 只通过了 SAP 的 SAP HANA 纵向扩展配置的认证。 暂时不支持包含 SAP HANA 工作负荷的横向扩展配置。 对于纵向扩展配置中的 SAP HANA 高可用性，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability)。

如果希望快速部署 SAP HANA 实例、S/4HANA 或 BW/4HANA 系统，则应考虑使用 [SAP Cloud Appliance Library](http://cal.sap.com)。 可在[本指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)中找到部署的相关文档，例如如何在 Azure 中通过 SAP CAL 部署 S/4HANA 系统。 只需具有 Azure 订阅，以及可通过 SAP Cloud Appliance Library 注册的 SAP 用户，即可完成操作。

## <a name="additional-resources"></a>其他资源
### <a name="sap-hana-backup"></a>SAP HANA 备份
有关在 Azure VM 上备份 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)
* [文件级别的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)
* [基于存储快照的 SAP HANA 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
有关使用 SAP Cloud Appliance Library 部署 S/4HANA 或 BW/4HANA 的信息，请参阅 [在 Microsoft Azure 上部署 SAP S/4HANA 或 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h) 。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 支持的操作系统
有关 SAP HANA 支持的操作系统的信息，请参阅 [SAP 支持说明 #2235581 - SAP HANA：支持的操作系统](https://launchpad.support.sap.com/#/notes/2235581/E)。 Azure VM 仅支持其中的一部分操作系统。 支持使用以下操作系统在 Azure 上部署 SAP HANA： 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

如需查看更多有关 SAP HANA 和其他 Linux 操作系统的 SAP 文档，请参阅：

* [SAP 支持说明 #171356 - Linux 上的 SAP 软件：常规信息](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP 支持说明 #1944799 - 适用于 SLES 操作系统安装的 SAP HANA 指南](http://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)
* [SAP 支持说明 #2205917 - SAP HANA DB 建议的适用于 SLES 12 for SAP Applications 的 OS 设置](https://launchpad.support.sap.com/#/notes/2205917/E)
* [SAP 支持说明 #1984787 - SUSE Linux Enterprise Server 12：安装说明](https://launchpad.support.sap.com/#/notes/1984787)
* [SAP 支持说明 #1391070 - Linux UUID 解决方案](https://launchpad.support.sap.com/#/notes/1391070)
* [SAP Support Note #2009879 - SAP HANA Guidelines for Red Hat Enterprise Linux (RHEL) Operating System](https://launchpad.support.sap.com/#/notes/2009879)（SAP 支持说明 #2009879 - Red Hat Enterprise Linux (RHEL) 操作系统的 SAP HANA 指南）
* [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)（2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）

### <a name="sap-monitoring-in-azure"></a>Azure 中的 SAP 监视
有关 Azure 中 SAP 监视的信息，请参阅：

* [SAP 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)。 本说明讨论对 Azure 上的 Linux VM 进行 SAP“增强型监视”。 
* [SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)。 本说明讨论 Linux 上的 SAPOSCOL 的相关信息。 
* [SAP 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)。 本说明讨论 Microsoft Azure 上的 SAP 关键监视指标。

### <a name="azure-vm-types"></a>Azure VM 类型
Azure VM 类型和配合 SAP HANA 使用的 SAP 支持的工作负荷方案记录在 [SAP 认证 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中。 

有关通过 SAP 的 SAP NetWeaver 或 S/4HANA 应用程序层认证的 Azure VM 类型，请参阅 [SAP 说明 1928533 - Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533/E)中的介绍。

>[!Note]
>SAP-Linux-Azure 集成仅在 Azure 资源管理器部署模型上受支持，在经典部署模型上不受支持。 

## <a name="manual-installation-of-sap-hana"></a>手动安装 SAP HANA
本指南介绍了在 Azure VM 上手动安装 SAP HANA 的两种不同方法：

* 在“安装数据库实例”步骤中将 SAP Software Provisioning Manager (SWPM) 用作分布式 NetWeaver 安装的一部分
* 使用 SAP HANA 数据库生命周期管理工具 HDBLCM，然后安装 NetWeaver

此外，还可以使用 SWPM 在单个 VM 上安装所有组件（SAP HANA、SAP 应用程序服务器和 ASCS 实例），如此 [SAP HANA 博客声明](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)中所述。 本快速入门指南中不介绍此选项，但须考虑的问题是相同的。

开始安装前，建议阅读本指南后面的“为手动安装 SAP HANA 准备 Azure VM”部分。 这样做有助于防止当仅使用默认 Azure VM 配置时可能会发生的一些基本错误。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>使用 SAP SWPM 时安装 SAP HANA 的主要步骤
本部分列出了当使用 SAP SWPM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 时的主要步骤。 本指南后面部分中的屏幕截图对各个步骤进行了更详细地说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 按照 Azure 资源管理器模型部署两个带有操作系统的 Azure VM（在本示例中为 SUSE Linux Enterprise Server (SLES) 和 SLES for SAP Applications 12 SP1）。
3. 将两个 Azure 标准或高级存储磁盘（例如 75-GB 或 500-GB 磁盘）附加到应用程序服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的“磁盘设置”部分。
5. 根据大小或吞吐量需求，附加多个磁盘，然后使用逻辑卷管理或多设备管理工具 (MDADM) 在 VM 内的 OS 级别创建带区卷。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如为所有 SAP 软件使用一个文件系统。 为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是必要的，它可以防止根文件系统（在 Linux Azure VM 上不是很大）被填满。
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数。
10. 根据使用的 Linux OS 版本设置 Linux 内核参数。 有关详细信息，请参阅本指南中探讨 HANA 的相应 SAP 说明以及“内核参数”部分。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在应用服务器 VM 上安装 SAP ASCS 实例。
15. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
16. 使用 SWPM 在 DB 服务器 VM 上安装数据库实例（包括 HANA）。
17. 在应用程序服务器 VM 上安装主应用程序服务器 (PAS)。
18. 启动 SAP 管理控制台 (SAP MC)。 例如使用 SAP GUI 或 HANA Studio 进行连接。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>使用 HDBLCM 时安装 SAP HANA 的主要步骤
本部分列出了使用 SAP HDBLCM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 的主要步骤。 本指南中的屏幕截图对各个步骤进行了更详细的说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 按照 Azure 资源管理器模型部署两个带有操作系统的 Azure VM（在本示例中为 SLES 和 SLES for SAP Applications 12 SP1）。
3. 将两个 Azure 标准或高级存储磁盘（例如 75-GB 或 500-GB 磁盘）附加到应用服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的“磁盘设置”部分。
5. 根据大小或吞吐量需求，附加多个磁盘，然后使用逻辑卷管理或多设备管理工具 (MDADM) 在 VM 内的 OS 级别创建带区卷。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如，为所有 SAP 软件使用一个文件系统，为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是必要的，它有助于防止根文件系统（在 Linux Azure VM 上不是很大）被填满。
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数。
10. 根据使用的 Linux OS 版本设置内核参数。 有关详细信息，请参阅本指南中探讨 HANA 的相应 SAP 说明以及“内核参数”部分。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在 HANA DB 服务器 VM 上创建组 ID 为 1001 的组 sapsys。
15. 使用 HANA 数据库生命周期管理器 (HDBLCM) 在 DB 服务器 VM 上安装 SAP HANA。
16. 在应用服务器 VM 上安装 SAP ASCS 实例。
17. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
18. 使用 SWPM 在 HANA DB 服务器 VM 上安装数据库实例（包括 HANA）。
19. 在应用程序服务器 VM 上安装主应用程序服务器 (PAS)。
20. 启动 SAP MC。 通过 SAP GUI 或 HANA Studio 进行连接。

## <a name="preparing-azure-vms-for-a-manual-installation-of-sap-hana"></a>为手动安装 SAP HANA 准备 Azure VM
本部分涵盖了以下主题：

* OS 更新
* 磁盘设置
* 内核参数
* 文件系统
* /etc/hosts 文件
* /etc/fstab 文件

### <a name="os-updates"></a>OS 更新
在安装其他软件前检查 Linux OS 更新和修补程序。 或许可通过安装一个修补程序解决问题，而无需求助支持人员。

确保使用：
* SUSE Linux Enterprise Server for SAP Applications。
* 适用于 SAP Applications 的 Red Hat Enterprise Linux 或适用于 SAP HANA 的 Red Hat Enterprise Linux。 

如果尚未这样做，请使用从 Linux 供应商获得的 Linux 订阅注册 OS 部署。 请注意，SUSE 的适用于 SAP 应用程序的某些 OS 映像已包含服务且会自动注册。

以下是通过使用 zypper 命令检查 SUSE Linux 的可用修补程序的示例：

 `sudo zypper list-patches`

根据问题种类，可按类别和严重性对修补程序进行分类。 类别的常用值有：“安全”、“建议”、“可选”、“功能”、“文档”或“yast”。
严重性的常用值有：“严重”、“重要”、“中等”、“低”或“未指定”。

zypper 命令仅查找已安装的程序包的所需更新。 例如，可使用此命令：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

可添加参数 `--dry-run` 来测试更新，而不实际更新系统。


### <a name="disk-setup"></a>磁盘设置
Azure 上 Linux VM 中的根文件系统具有大小限制。 因此，有必要为运行 SAP 的 Azure VM 附加额外的磁盘空间。 对于 SAP 应用程序服务器 Azure VM，使用 Azure 标准存储磁盘可能就已足够。 但是，对于 SAP HANA DBMS Azure VM，必须将 Azure 高级存储磁盘用于生产和非生产实现。

根据 [SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建议使用以下 Azure 高级存储存储配置： 

| VM SKU | RAM |  /hana/data and /hana/log <br /> 使用 LVM 或 MDADM 进行条带化 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建议的磁盘配置中，将 HANA 数据卷和日志卷放置在使用 LVM 或 MDADM 进行条带化的同一组 Azure 高级存储磁盘上。 不需要定义任何 RAID 冗余级别，因为 Azure 高级存储会保留磁盘的三个映像用作冗余。 若要确保配置足够的存储，请参阅 [SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)和 [SAP HANA 服务器安装和更新指南](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。 此外应考虑不同 Azure 高级存储磁盘的不同虚拟硬盘 (VHD) 吞吐量卷，如 [VM 的高性能高级存储和托管磁盘](https://docs.microsoft.com/azure/storage/storage-premium-storage)中所述。 

可将更多的高级存储磁盘添加到 HANA DBMS VM，用于存储数据库或事务日志备份。

有关用于配置带区的两个主要工具的详细信息，请参阅以下文章：

* [在 Linux 上配置软件 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [在 Azure 中的 Linux VM 上配置 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

有关将磁盘附加到将 Linux 作为来宾 OS 运行 的 Azure VM 的详细信息，请参阅[将磁盘附加到 Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

Azure 高级存储允许定义磁盘缓存模式。 对于保存 /hana/data 和 /hana/log 的条带化集，应禁用磁盘缓存。 对于其他卷（磁盘），缓存模式都应设置为“ReadOnly”。

有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../../windows/premium-storage.md)。

要查找用于创建 VM 的示例 JSON 模板，请转到 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)。
vm-simple-sles 模板是基本模板。 它包含具有额外 100-GB 数据磁盘的存储区。 此模板可以用作基类。 可将模板调整为特定配置。

>[!Note]
>务必根据[运行 Microsoft Azure SUSE Linux VM 上的 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) 中所述，使用 UUID 附加 Azure 存储磁盘。

在测试环境中，有两个 Azure 标准存储磁盘已附加到 SAP 应用服务器 VM，如以下屏幕截图中所示。 一个磁盘存储着可供安装的所有 SAP 软件（包括 NetWeaver 7.5、SAP GUI、SAP HANA）。 第二个磁盘确保有足够的可用空间可用来满足额外的需求（例如，备份和测试数据），并且确保在属于同一 SAP 布局的所有 VM 之间共享 /sapmnt 目录（即 SAP 配置文件）。

![SAP HANA 应用服务器磁盘窗口，其中显示了两个数据磁盘及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>内核参数
SAP HANA 要求使用不属于标准 Azure 库映像的特定 Linux 内核设置，并且必须手动指定这些设置。 参数可能不同，具体取决于使用 SUSE 还是 Red Hat。 前面列出的 SAP 说明提供了这些参数的相关信息。 屏幕截图中使用了 SUSE Linux 12 SP1。 

SLES for SAP Applications 12 GA 和 SLES for SAP Applications 12 SP1 具有替换旧 sapconf 工具的新工具 tuned-adm。 有一个特殊的 SAP HANA 配置文件可用于 tuned-adm。 若要优化 SAP HANA 的系统，请以根用户身份输入以下内容：

   `tuned-adm profile sap-hana`

有关 tuned-adm 的详细信息，请参阅[关于 tuned-adm 的 SUSE 文档](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

在下面的屏幕截图中，可以看到 tuned-adm 如何根据所需的 SAP HANA 设置更改了 `transparent_hugepage` 和 `numa_balancing` 值。

![tuned-adm 工具根据所需的 SAP HANA 设置更改值](./media/hana-get-started/image005.jpg)

若要永久保存 SAP HANA 内核设置，在 SLES 12 上请使用 grub2。 有关 grub2 的详细信息，请转到 SUSE 文档的[配置文件结构](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)部分。

下面的屏幕截图显示了内核设置在配置文件中进行了怎样的更改，然后怎样使用 grub2-mkconfig 进行了编译：

![配置文件中已更改并使用 grub2-mkconfig 进行了编译的内核设置](./media/hana-get-started/image006.jpg)

另一种选择是通过 YaST 和“启动加载程序” > “内核参数”设置来更改这些设置：

![YaST 启动加载程序中的“内核参数”设置选项卡](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>文件系统
以下屏幕截图显示了基于两个附加的 Azure 标准存储磁盘在 SAP 应用服务器 VM 上创建的两个文件系统。 两个文件系统都属于 XFS 类型并装载到 /sapdata 和 /sapsoftware。

并非必须按照此方式安排文件系统的结构。 还可以通过其他方式来安排磁盘空间的结构。 最重要的注意事项是要防止根文件系统的可用空间耗尽。

![在 SAP 应用服务器 VM 上创建的两个文件系统](./media/hana-get-started/image008.jpg)

对于 SAP HANA DB VM，在数据库安装期间，当使用 SAPinst (SWPM) 和“典型”安装选项时，所有项都将安装到 /hana 和 /usr/sap 中。 SAP HANA 日志备份的默认位置位于 /usr/sap 中。 同样，防止根文件系统耗尽存储空间也非常重要，在使用 SWPM 安装 SAP HANA 之前，请确保 /hana 和 /usr/sap 中有足够的可用空间。

有关 SAP HANA 的标准文件系统布局的说明，请参阅 [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)（SAP HANA 服务器安装和更新指南）。

![在 SAP 应用服务器 VM 上创建的额外文件系统](./media/hana-get-started/image009.jpg)

在标准 SLES/SLES for SAP Applications 12 Azure 库映像中安装 SAP NetWeaver 时，将显示一条消息，指出没有交换空间，如下面的屏幕截图所示。 若要消除此消息，可通过使用 dd、mkswap 和 swapon 手动添加交换文件。 若要了解如何操作，请在 SUSE 文档的[使用 YaST 分区程序](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)部分中搜索“手动添加交换文件”。

另一种选择是使用 Linux VM 代理配置交换空间。 有关详细信息，请参阅 [Azure Linux Agent User Guide](../../extensions/agent-linux.md)（Azure Linux 代理用户指南）。

![通知交换空间不足的弹出消息](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 文件
开始安装 SAP 之前，请确保在 /etc/hosts 文件中包含 SAP VM 的主机名和 IP 地址。 将所有 SAP VM 部署在一个 Azure 虚拟网络中，然后使用内部 IP 地址，如下所示：

![/etc/hosts 文件中列出的 SAP VM 的主机名和 IP 地址](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 文件

一个有用的做法是向 fstab 文件添加 nofail 参数。 此操作后，如果磁盘发生错误，VM 将不会在启动过程中挂起。 但请记住，可能没有额外的磁盘空间可用，并且进程可能会填满根文件系统。 如果缺少 /hana，则 SAP HANA 不会启动。

![向 fstab 文件添加 nofail 参数](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 上的图形 GNOME 桌面
本部分涵盖了以下主题：

* 在 SLES 12/SLES for SAP Applications 12 上安装 GNOME 桌面和 xrdp
* 在 SLES 12/SLES for SAP Applications 12 上使用 Firefox 运行基于 Java 的 SAP MC

还可使用替代项，例如 Xterminal 或 VNC（本指南不提供介绍）。

### <a name="installing-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>在 SLES 12/SLES for SAP Applications 12 上安装 GNOME 桌面和 xrdp
如果你熟悉 Windows，则可以直接在 SAP Linux VM 中轻松使用图形桌面来运行 Firefox、SAPinst、SAP GUI、SAP MC 或 HANA Studio，以及从 Windows 计算机通过远程桌面协议 (RDP) 连接到 VM。 根据公司关于将图形用户界面添加到基于 Linux 的生产和非生产系统的政策，可能需要在服务器上安装 GNOME。 若要在 Azure SLES 12/SAP Applications 12 VM 上安装 GNOME 桌面：

1. 输入以下命令来安装 GNOME 桌面（例如在 PuTTY 窗口中）：

   `zypper in -t pattern gnome-basic`

2. 然后安装 xrdp，以便通过 RDP 连接到虚拟机：

   `zypper in xrdp`

3. 编辑 /etc/sysconfig/windowmanager 并将默认窗口管理器设置为 GNOME：

   `DEFAULT_WM="gnome"`

4. 运行 chkconfig，确保 xrdp 在重新启动后自动启动：

   `chkconfig -level 3 xrdp on`

5. 如果 RDP 连接发生问题，请尝试重新启动（例如从 PuTTY 窗口）：

   `/etc/xrdp/xrdp.sh restart`

6. 如果上一步中提到的 xrdp 重启未成功，请检查 .pid 文件：

   `check /var/run` 

   查找 `xrdp.pid`。 找到后将其删除，然后再次尝试重新启动。

### <a name="starting-sap-mc"></a>启动 SAP MC
安装 GNOME 桌面后，在 Azure SLES 12/SLES for SAP Applications 12 VM 中运行的 Firefox 中启动图形化的基于 Java 的 SAP MC 时，可能会因为缺少 Java 浏览器插件而显示错误。

用于启动 SAP MC 的 URL 是 `<server>:5<instance_number>13`。

有关详细信息，请参阅 [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)（启动基于 Web 的 SAP 管理控制台）。

下面的屏幕截图显示了缺少 Java 浏览器插件时显示的错误消息：

![指明缺少 Java 浏览器插件的错误消息](./media/hana-get-started/image013.jpg)

若要解决此问题，其中一种方法是使用 YaST 安装缺少的插件，如下面的屏幕截图所示：

![使用 YaST 安装缺少的插件](./media/hana-get-started/image014.jpg)

重新输入 SAP 管理控制台 URL 时，将显示一条指示激活该插件的消息：

![要求激活插件的对话框](./media/hana-get-started/image015.jpg)

可能还会收到关于缺少 javafx.properties 文件的错误消息。 这与用于 SAP GUI 7.4 的 Oracle Java 1.8 的要求有关。 （请参阅 [SAP 说明 2059429](https://launchpad.support.sap.com/#/notes/2059424)。）IBM Java 版本和 SLES/SLES for SAP Applications 12 提供的 openjdk 包均不包括所需的 javafx.properties 文件。 解决方法是从 Oracle 下载并安装 Java SE 8。

有关类似的 openSUSE 上 openjdk 问题的信息，请参阅讨论话题 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)。

## <a name="manual-installation-of-sap-hana-swpm"></a>手动安装 SAP HANA：SWPM
本部分中的屏幕截图系列显示了当使用 SWPM (SAPinst) 时用于安装 SAP NetWeaver 7.5 和 SAP HANA SP12 的主要步骤。 在 NetWeaver 7.5 安装过程中，SWPM 还可以将 HANA 数据库安装为单一实例。

在示例测试环境中，我们仅安装了一个高级业务应用程序编程 (ABAP) 应用服务器。 如下面的屏幕截图所示，我们使用了“分布式系统”选项来将 ASCS 和主应用程序服务器实例安装在一个 Azure VM 中，将 SAP HANA 作为数据库系统安装在另一个 Azure VM 中。

![使用“分布式系统”选项安装的 ASCS 和主应用程序服务器实例](./media/hana-get-started/image012.jpg)

在将 ASCS 实例安装到应用服务器 VM 并且在 SAP 管理控制台中将其设置为“绿色”（如下面的屏幕截图所示）后，必须与 SAP HANA DB 服务器 VM 共享 /sapmnt 目录（包括 SAP 配置文件目录）。 DB 安装步骤需要访问此信息。 最好的方法是使用可通过 YaST 进行配置的 NFS。

![SAP 管理控制台，其中显示了在应用服务器 VM 上安装的并设置为“绿色”的 ASCS 实例](./media/hana-get-started/image016.jpg)

在应用服务器 VM 上，应通过 NFS 并使用 **rw** 和 **no_root_squash** 选项来共享 /sapmnt 目录。 默认选项为“ro”和“root_squash”，这在安装数据库实例时可能会导致出现问题。

![通过 NFS 并使用 rw 和 no_root_squash 选项共享 /sapmnt 目录](./media/hana-get-started/image017b.jpg)

如下一屏幕截图所示，必须使用“NFS 客户端”（和 YaST）在 SAP HANA DB 服务器 VM 上配置应用服务器 VM 中的 /sapmnt 共享。

![使用 NFS 客户端配置的 /sapmnt 共享](./media/hana-get-started/image018b.jpg)

若要执行分布式 NetWeaver 7.5 安装（“数据库实例”），如以下屏幕截图中所示，请登录到 SAP HANA DB 服务器 VM 并启动 SWPM。

![通过登录到 SAP HANA DB 服务器 VM 并启动 SWPM 来安装数据库实例](./media/hana-get-started/image019.jpg)

选择“典型”安装和安装媒体的路径后，输入 DB SID、主机名、实例编号和 DB 系统管理员密码。

![SAP HANA 数据库系统管理员登录页面](./media/hana-get-started/image035b.jpg)

输入 DBACOCKPIT 架构的密码：

![DBACOCKPIT 架构的密码输入框](./media/hana-get-started/image036b.jpg)

为 SAPABAP1 架构密码输入一个问题：

![为 SAPABAP1 架构密码输入一个问题](./media/hana-get-started/image037b.jpg)

每完成一个任务，DB 安装过程的每个阶段旁边都将显示一个绿色的对号。 将显示消息“执行...数据库实例已完成”。

![包含确认消息的任务已完成窗口](./media/hana-get-started/image023.jpg)

安装成功后，SAP 管理控制台应该也会将 DB 实例显示为“绿色”，并显示完整的 SAP HANA 进程列表（hdbindexserver、hdbcompileserver，等等）。

![其中包含 SAP HANA 进程列表的 SAP 管理控制台窗口](./media/hana-get-started/image024.jpg)

以下屏幕截图显示了安装 HANA 期间 SWPM 在 /hana/shared 目录下创建的部分文件结构。 因为没有用来指定其他路径的选项，因此，在使用 SWPM 安装 SAP HANA 前，务必在 /hana 目录下装载额外的磁盘空间。 这能防止根文件系统耗尽可用空间。

![安装 HANA 期间创建的 /hana/shared 目录文件结构](./media/hana-get-started/image025.jpg)

此屏幕截图显示了 /usr/sap 目录的文件结构：

![/usr/sap 目录文件结构](./media/hana-get-started/image026.jpg)

分布式 ABAP 安装的最后一个步骤是安装主应用程序服务器实例：

![ABAP 安装，其中主应用程序服务器实例显示为最后一个步骤](./media/hana-get-started/image027b.jpg)

安装主应用程序服务器实例和 SAP GUI 后，使用 DBA Cockpit 事务确认 SAP HANA 安装是否已正确完成：

![确认安装成功的 DBA Cockpit 窗口](./media/hana-get-started/image028b.jpg)

在最后一个步骤中，你可能希望首先在 SAP 应用服务器 VM 中安装 HANA Studio，然后连接到在 DB 服务器 VM 上运行的 SAP HANA 实例：

![在 SAP 应用服务器 VM 中安装 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>手动安装 SAP HANA：HDBLCM
除了使用 SWPM 将 SAP HANA 安装为分布式安装的一部分之外，也可以先使用 HDBLCM 独立安装 HANA。 例如，可以安装 SAP NetWeaver 7.5。 此部分的屏幕截图显示了此过程的工作原理。

有关 HANA HDBLCM 工具的详细信息，请参阅：

* [Choosing the Correct SAP HANA HDBLCM for Your Task](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)（为任务选择正确的 SAP HANA HDBLCM）
* [SAP HANA Lifecycle Management Tools](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)（SAP HANA 生命周期管理工具）
* [SAP HANA Server Installation and Update Guide](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)（SAP HANA 服务器安装和更新指南）

为了避免 `\<HANA SID\>adm user`（由 HDBLCM 工具创建）的默认组 ID 设置发生问题，请在通过 HDBLCM 安装 SAP HANA 之前，定义一个名为 `sapsys`、组 ID 为 `1001` 的新组：

![使用组 ID 1001 定义的新组“sapsys”](./media/hana-get-started/image030.jpg)

首次启动 HDBLCM 时，会显示一个简单的启动菜单。 选择第 1 项“安装新系统”，如下面的屏幕截图所示：

![HDBLCM 启动窗口中的“安装新系统”选项](./media/hana-get-started/image031.jpg)

以下屏幕截图显示了你之前选择的所有主要选项。

> [!IMPORTANT]
> 为 HANA 日志和数据卷指定的目录以及安装路径（在本例中为 /hana/shared）和 /usr/sap 不应当是根文件系统的一部分。 这些目录属于附加到 VM 的 Azure 数据磁盘（如“磁盘设置”部分所述）。 此方法有助于防止根文件系统耗尽空间。 在下面的屏幕截图中，可以看到 HANA 系统管理员具有用户 ID `1005`，它是安装前定义的 `sapsys` 组 (ID `1001`) 的一部分。

![之前选择的所有主要 SAP HANA 组件的列表](./media/hana-get-started/image032.jpg)

可在 /etc/passwd 目录中查看 `\<HANA SID\>adm user`（下面屏幕截图中的 `azdadm`）的详细信息：

![/etc/passwd 目录中列出的 HANA \<HANA SID\>adm 用户详细信息](./media/hana-get-started/image033.jpg)

使用 HDBLCM 安装 SAP HANA 后，可以在 SAP HANA Studio 中看到文件结构，如下面的屏幕截图所示。 目前尚未提供包括所有 SAP NetWeaver 表的 SAPABAP1 架构。

![SAP HANA Studio 中的 SAP HANA 文件结构](./media/hana-get-started/image034.jpg)

安装 SAP HANA 之后，可以在其上安装 SAP NetWeaver。 如下面的屏幕截图所示，安装是通过 SWPM 作为分布式安装执行的（如上一部分所述）。 使用 SWPM 安装数据库实例时，请输入与使用 HDBLCM 时相同的数据（例如主机名、HANA SID 和实例编号）。 然后，SWPM 将使用现有 HANA 安装并添加更多架构。

![使用 SWPM 执行的分布式安装](./media/hana-get-started/image035b.jpg)

下面的屏幕截图显示了需要输入 DBACOCKPIT 架构相关数据的 SWPM 安装步骤：

![用于输入 DBACOCKPIT 架构数据的 SWPM 安装步骤](./media/hana-get-started/image036b.jpg)

输入 SAPABAP1 架构的相关数据：

![输入 SAPABAP1 架构的相关数据](./media/hana-get-started/image037b.jpg)

完成 SWPM 数据库实例安装后，可在 SAP HANA Studio 中看到 SAPABAP1 架构：

![SAP HANA Studio 中的 SAPABAP1 架构](./media/hana-get-started/image038b.jpg)

最后，在 SAP 应用服务器和 SAP GUI 安装完成后，可以使用 DBA Cockpit 事务验证 HANA DB 实例：

![使用 DBA Cockpit 事务验证的 HANA DB 实例](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 软件下载
可以从 SAP Service Marketplace 下载软件，如以下屏幕截图中所示。

下载适用于 Linux/HANA 的 NetWeaver 7.5：

 ![用于下载 NetWeaver 7.5 的 SAP 服务安装和升级窗口](./media/hana-get-started/image001.jpg)

下载 HANA SP12 Platform Edition：

 ![用于下载 HANA SP12 Platform Edition 的 SAP 服务安装和升级窗口](./media/hana-get-started/image002.jpg)

