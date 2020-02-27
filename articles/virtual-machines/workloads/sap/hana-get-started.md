---
title: 快速入门：在 Azure 虚拟机上手动安装单实例 SAP HANA | Microsoft Docs
description: 有关在 Azure 虚拟机上手动安装单实例 SAP HANA 的快速入门指南
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 0090ffe977dee3e493d726c9eb4d151bcbeb503f
ms.sourcegitcommit: f15f548aaead27b76f64d73224e8f6a1a0fc2262
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2020
ms.locfileid: "77617250"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>快速入门：在 Azure 虚拟机上手动安装单实例 SAP HANA
## <a name="introduction"></a>简介
本指南可帮助你在 Azure 虚拟机上设置单实例 SAP HANA，并手动安装 SAP NetWeaver 7.5 和 SAP HANA 1.0 SP12。 本指南的重点是如何在 Azure 上部署 SAP HANA。 它不会取代 SAP 文档。 

> [!NOTE]
> 本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关如何将 SAP HANA 部署到 HANA 大型实例的信息，请参阅[在 Azure 虚拟机上使用 SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。
 
## <a name="prerequisites"></a>系统必备
本指南假定你熟悉此类基础结构即服务（IaaS）基础知识，如下所示：
 * 如何通过 Azure 门户或 PowerShell 部署虚拟机（Vm）或虚拟网络。
 * Azure 跨平台命令行接口（CLI），其中包括使用 JavaScript 对象表示法（JSON）模板的选项。

本指南还假定你熟悉以下内容：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 如何在 Azure 上安装和运行 SAP HANA 和 SAP 应用程序实例。
* 下面的概念和过程关于：
   * 规划 Azure 上的 SAP 部署，包括 Azure 虚拟网络规划和 Azure 存储使用情况。 请参阅[Azure 虚拟机上的 SAP NetWeaver-规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅[适用于 SAP 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。
   * Azure 上的 SAP NetWeaver ABAP SAP 中心服务（ASCS）、SAP 中心服务（SCS）和排队复制服务器（ERS）的高可用性。 请参阅 [Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)。
   * 详细介绍如何在 Azure 上的 ASCS/SCS 安装中提高效率。 请参阅[创建 SAP NetWeaver 多 SID 配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)。 
   * 在 Azure 中运行 SAP NetWeaver（基于 Linux 驱动的 VM）的原则。 请参阅[在 MICROSOFT AZURE SUSE Linux vm 上运行 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)。 本指南提供 Azure Vm 中 Linux 的特定设置。 它还提供了有关如何正确地将 Azure 存储磁盘附加到 Linux Vm 的信息。

[IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中列出了可用于生产方案的 Azure VM 类型。 对于非生产方案，可以使用一系列更广泛的本机 Azure VM 类型。
有关 VM 配置和操作的详细信息，请参阅[SAP HANA 基础结构配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
有关 SAP HANA 高可用性，请参阅[Azure 虚拟机 SAP HANA 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。

如果希望快速部署 SAP HANA 实例或 S/4HANA 或 BW/4HANA 系统，请考虑使用[SAP 云设备库](https://cal.sap.com)。 例如，在[本指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)中，可以找到有关如何在 Azure 上通过 SAP 云设备库部署 S/4HANA 系统的文档。 你只需一个 Azure 订阅和一个可以注册到 SAP 云设备库的 SAP 用户。

## <a name="additional-resources"></a>其他资源
### <a name="sap-hana-backup"></a>SAP HANA 备份
有关如何在 Azure Vm 上备份 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上 SAP HANA 的备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)。
* [在文件级别上 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)。
* [SAP HANA 基于存储快照的备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)。

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
有关如何使用 SAP 云设备库部署 S/4HANA 或 BW/4HANA 的信息，请参阅[Microsoft Azure 上的部署 Sap S/4HANA 或 bw/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 支持的操作系统
有关 SAP HANA 支持的操作系统的信息，请参阅 [SAP 说明 2235581-SAP HANA：](https://launchpad.support.sap.com/#/notes/2235581/E)支持的操作系统。 Azure VM 仅支持其中的一部分操作系统。 支持使用以下操作系统在 Azure 上部署 SAP HANA： 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

如需查看更多有关 SAP HANA 和其他 Linux 操作系统的 SAP 文档，请参阅：

* [SAP 说明171356：Linux 上的 SAP 软件：](https://launchpad.support.sap.com/#/notes/1984787)一般信息。
* [SAP 说明1944799：SAP HANA SLES 操作系统安装](http://service.sap.com/sap/support/notes/1944799)的准则。
* [SAP 说明2205917：SAP HANA DB 推荐适用于 SLES 12 for SAP 应用程序的 OS 设置](https://launchpad.support.sap.com/#/notes/2205917/E)。
* [SAP 说明1391070：Linux UUID 解决方案](https://launchpad.support.sap.com/#/notes/1391070)。
* [SAP 说明2009879：Red Hat Enterprise Linux （RHEL）操作系统](https://launchpad.support.sap.com/#/notes/2009879)SAP HANA 准则。
* [SAP 说明2292690：SAP HANA DB：RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)的建议 OS 设置。

### <a name="sap-monitoring-in-azure"></a>Azure 中的 SAP 监视
有关 Azure 中的 SAP 监视的信息：

* [Sap 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)讨论了 Azure 上的 Linux VM 的 sap 增强型监视。 
* [SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)讨论了有关 Linux 上的 SAPOSCOL 的信息。 
* [Sap 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)讨论 Microsoft Azure 上的 sap 的关键监视指标。

### <a name="azure-vm-types"></a>Azure VM 类型
适用于 SAP HANA 的 Azure VM 类型和 SAP 支持的工作负荷方案记录在[SAP 认证 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中。 

Sap 中的 sap NetWeaver 或 S/4HANA 应用程序层认证的 Azure VM 类型已记录在 SAP 说明 1928533 [中：Azure 上的 SAP 应用程序：](https://launchpad.support.sap.com/#/notes/1928533/E)支持的产品和 Azure VM 类型。

> [!NOTE]
> SAP-Linux-Azure 集成仅在 Azure 资源管理器部署模型上受支持，在经典部署模型上不受支持。 

## <a name="manual-installation-of-sap-hana"></a>手动安装 SAP HANA

> [!IMPORTANT]
> 请确保所选择的 OS 是针对所使用的特定 VM 类型的 SAP HANA SAP 认证的。 可在[SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查找这些 vm 类型的 SAP HANA 认证的 vm 类型和 OS 版本的列表。 请确保单击列出的 VM 类型的详细信息，以获取特定 VM 类型 SAP HANA 支持的 OS 版本的完整列表。 对于本文档中的示例，我们使用了不受 SAP 用于 M 系列 Vm SAP HANA 的 SUSE Linux Enterprise Server （SLES） OS 版本。
>

本指南介绍了在 Azure VM 上手动安装 SAP HANA 的两种不同方法：

* 在 "安装数据库实例" 步骤中将 SAP 软件预配管理器（SWPM）用作分布式 NetWeaver 安装的一部分。
* 使用 SAP HANA 数据库生命周期管理器（HDBLCM）工具，然后安装 NetWeaver。

还可以使用 SWPM 在单个 VM 中安装所有组件，例如 SAP HANA、SAP 应用程序服务器和 ASCS 实例。 此[SAP HANA 博客公告](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)中介绍了这些步骤。 此选项未在本快速入门指南中介绍，但你必须考虑的问题是相同的。

在开始安装之前，建议阅读本指南后面的 "准备 Azure Vm 以便手动安装 SAP HANA" 部分。 这样做有助于防止当仅使用默认 Azure VM 配置时可能会发生的一些基本错误。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>使用 SAP SWPM 时安装 SAP HANA 的主要步骤
本部分列出了当使用 SAP SWPM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 时的主要步骤。 本指南后面部分中的屏幕截图对各个步骤进行了更详细地说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 根据 Azure 资源管理器模型部署两个带有操作系统的 Azure Vm。 此示例使用 SUSE Linux Enterprise Server 和 SLES for SAP Applications 12 SP1。 
3. 将两个 Azure 标准或高级存储磁盘（例如，75-GB 或 500-GB 磁盘）附加到应用程序服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的 "磁盘设置" 部分。
5. 根据大小或吞吐量需求，附加多个磁盘。 然后创建带区卷。 在 VM 内的 OS 级别使用逻辑卷管理（LVM）或多设备管理（mdadm）工具。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如为所有 SAP 软件使用一个文件系统。 为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 要防止根文件系统填满，此过程是必需的。 根文件系统在 Linux Azure Vm 上不太大。 
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数。
10. 根据你使用的 Linux OS 版本设置 Linux 内核参数。 有关详细信息，请参阅本指南中讨论 HANA 的 SAP 说明和 "内核参数" 部分。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在应用服务器 VM 上安装 SAP ASCS 实例。
15. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
16. 在 DB 服务器 VM 上使用 SWPM 安装包含 HANA 的数据库实例。
17. 在应用程序服务器 VM 上安装主应用程序服务器 (PAS)。
18. 启动 SAP 管理控制台 (SAP MC)。 例如使用 SAP GUI 或 HANA Studio 进行连接。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>使用 HDBLCM 时安装 SAP HANA 的主要步骤
本部分列出了使用 SAP HDBLCM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 的主要步骤。 本指南中的屏幕截图对各个步骤进行了更详细的说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 根据 Azure 资源管理器模型部署两个带有操作系统的 Azure Vm。 此示例使用 SLES 和 SLES for SAP Applications 12 SP1。
3. 将两个 Azure 标准或高级存储磁盘（例如，75-GB 或 500-GB 磁盘）附加到应用服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的 "磁盘设置" 部分。
5. 根据大小或吞吐量需求，附加多个磁盘。 使用逻辑卷管理或 VM 内的 OS 级别的 mdadm 工具创建带区卷。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如为所有 SAP 软件使用一个文件系统。 为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是帮助防止根文件系统填满的必要操作。 根文件系统在 Linux Azure Vm 上不太大。
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数。
10. 根据所使用的 Linux OS 版本设置内核参数。 有关详细信息，请参阅本指南中讨论 HANA 的 SAP 说明和 "内核参数" 部分。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在 HANA DB 服务器 VM 上创建组 ID 为 1001 的组 sapsys。
15. 使用 HANA 数据库生命周期管理器在 DB 服务器 VM 上安装 SAP HANA。
16. 在应用服务器 VM 上安装 SAP ASCS 实例。
17. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
18. 在 HANA DB 服务器 VM 上使用 SWPM 安装包含 HANA 的数据库实例。
19. 在应用程序服务器 VM 上安装主应用程序服务器。
20. 启动 SAP MC。 通过 SAP GUI 或 HANA Studio 进行连接。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>为手动安装 SAP HANA 准备 Azure VM
本部分涵盖了以下主题：

* OS 更新
* 磁盘设置
* 内核参数
* 文件系统
* /etc/hosts 文件
* /etc/fstab 文件

### <a name="os-updates"></a>OS 更新
在安装其他软件之前，请检查是否有 Linux OS 更新和修补程序。 通过安装修补程序，你可以避免对支持人员的呼叫。

请确保使用：
* SUSE Linux Enterprise Server for SAP Applications。
* 适用于 SAP Applications 的 Red Hat Enterprise Linux 或适用于 SAP HANA 的 Red Hat Enterprise Linux。 

如果尚未这样做，请向 Linux 供应商提供的 Linux 订阅注册 OS 部署。 SUSE 包含适用于 SAP 应用程序的操作系统映像，这些应用程序已经包含服务，并会自动注册。

下面的示例演示如何使用**zypper**命令检查 SUSE Linux 的可用修补程序：

 `sudo zypper list-patches`

根据问题种类，可按类别和严重性对修补程序进行分类。 类别的常用值包括： 
- 安全性
- 建议
- 可选
- 功能
- Document
- Yast

严重性的常用值有：

- 关键
- 重要提示
- 中等
- 低
- “未指定”

zypper 命令仅查找已安装的程序包的所需更新。 例如，可以使用以下命令：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

可添加参数 `--dry-run` 来测试更新，而不实际更新系统。


### <a name="disk-setup"></a>磁盘设置
Azure 上 Linux VM 中的根文件系统具有大小限制。 因此，需要将额外的磁盘空间附加到 Azure VM 以运行 SAP。 对于 SAP 应用程序服务器 Azure Vm，使用 Azure 标准存储磁盘可能已经足够。 对于 SAP HANA DBMS Azure Vm，必须将 Azure 高级存储磁盘用于生产和非生产实现。

根据[SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建议以下 Azure 高级存储配置： 

| VM SKU | RAM |  /hana/data and /hana/log <br /> 用 LVM 或 mdadm 进行条带化 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建议的磁盘配置中，HANA 数据卷和日志卷位于用 LVM 或 mdadm 进行条带化的同一组 Azure 高级存储磁盘上。 不需要定义任何 RAID 冗余级别，因为 Azure 高级存储会保留磁盘的三个映像以实现冗余。 

若要确保配置足够的存储，请参阅[SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)和[SAP HANA 服务器安装和更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。 还应考虑不同 Azure 高级存储磁盘的不同虚拟硬盘（VHD）吞吐量卷，如[vm 的高性能高级存储和托管磁盘](../../windows/disks-types.md)中所述。 

可以将更多高级存储磁盘添加到 HANA DBMS Vm，以存储数据库备份或事务日志备份。

有关用于配置条带化的两个主要工具的详细信息，请参阅：

* [在 Linux 上配置软件 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [在 Azure 中的 Linux VM 上配置 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

有关如何将磁盘附加到运行 Linux 的 Azure Vm 的详细信息，请参阅[将磁盘添加到 LINUX VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

借助 Azure premium Ssd，可以定义磁盘缓存模式。 对于包含/hana/data 和/hana/log 的条带集，禁用磁盘缓存。 对于其他卷（即磁盘），请将缓存模式设置为**ReadOnly**。

若要查找用于创建 Vm 的示例 JSON 模板，请参阅[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)。
vm-simple-sles 模板是基本模板。 它包含具有额外 100-GB 数据磁盘的存储区。 使用此模板作为基础。 可将模板调整为特定配置。

> [!NOTE]
> 如在[MICROSOFT AZURE SUSE Linux vm 上运行 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)中所述，使用 UUID 附加 Azure 存储磁盘是很重要的。

在测试环境中，将两个 Azure 标准存储磁盘附加到 SAP 应用服务器 VM，如以下屏幕截图所示。 一个磁盘存储所有 SAP 软件，如安装的 NetWeaver 7.5、SAP GUI 和 SAP HANA。 第二个磁盘确保有足够的可用空间来满足其他要求。 例如，备份和测试数据以及/sapmnt 目录（即 SAP 配置文件）需要在属于同一 SAP 布局的所有 Vm 之间共享。

![SAP HANA 应用服务器磁盘窗口，其中显示了两个数据磁盘及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>内核参数
SAP HANA 要求特定的 Linux 内核设置。 这些内核设置不是标准 Azure 库映像的一部分，必须手动设置。 参数可能不同，具体取决于使用 SUSE 还是 Red Hat。 前面列出的 SAP 说明会给出这些参数的相关信息。 屏幕截图中使用了 SUSE Linux 12 SP1。 

SLES for SAP Applications 12 general 可用性和 SLES for SAP Applications 12 SP1 提供了一个新的工具，用于**优化**旧的**sapconf**工具。 有一个特殊的 SAP HANA 配置文件可用于 tuned-adm。 若要优化 SAP HANA 的系统，请以 root 用户身份输入以下配置文件：

   `tuned-adm profile sap-hana`

有关 tuned-adm 的详细信息，请参阅[关于 tuned-adm 的 SUSE 文档](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)。

在下面的屏幕截图中，可以根据所需的 SAP HANA 设置，查看**优化的 adm**如何更改 `transparent_hugepage` 和 `numa_balancing` 值：

![tuned-adm 工具根据所需的 SAP HANA 设置更改值](./media/hana-get-started/image005.jpg)

若要永久保存 SAP HANA 内核设置，在 SLES 12 上请使用 grub2。 有关**grub2**的详细信息，请参阅 SUSE 文档的 "[配置文件结构](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)" 一节。

下面的屏幕截图显示了内核设置在配置文件中进行了怎样的更改，然后怎样使用 grub2-mkconfig 进行了编译：

![配置文件中已更改并使用 grub2-mkconfig 进行了编译的内核设置](./media/hana-get-started/image006.jpg)

另一种选择是通过 YaST 和“启动加载程序” > “内核参数”设置来更改这些设置：

![YaST 启动加载程序中的“内核参数”设置选项卡](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>文件系统
以下屏幕截图显示了基于两个附加的 Azure 标准存储磁盘在 SAP 应用服务器 VM 上创建的两个文件系统。 这两个文件系统都是 XFS 类型，并装载到/sapdata 和/sapsoftware。

这并不是以这种方式构造文件系统所必需的。 你还可以使用其他选项来构造磁盘空间。 最重要的注意事项是要防止根文件系统的可用空间耗尽。

![在 SAP 应用服务器 VM 上创建的两个文件系统](./media/hana-get-started/image008.jpg)

对于 SAP HANA DB VM，在数据库安装过程中，当你将 SAPinst 与 SWPM 和**典型**安装选项一起使用时，所有内容都将安装在/hana 和/usr/sap 下。 SAP HANA 日志备份的默认位置位于 /usr/sap 中。 同样，阻止根文件系统耗尽存储空间非常重要。 在使用 SWPM 安装 SAP HANA 之前，请确保 "/hana" 和 "/usr/sap" 下有足够的可用空间。

有关 SAP HANA 的标准文件系统布局的说明，请参阅[SAP HANA 服务器安装和更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。

![在 SAP 应用服务器 VM 上创建的额外文件系统](./media/hana-get-started/image009.jpg)

在标准 SLES/SLES for SAP Applications 12 Azure 库映像上安装 SAP NetWeaver 时，会显示一条消息，指出没有交换空间，如以下屏幕截图所示。 若要消除此消息，可通过使用 dd、mkswap 和 swapon 手动添加交换文件。 若要了解如何操作，请在 SUSE 文档的[使用 YaST 分区](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)程序部分中搜索 "手动添加交换文件"。

另一种选择是使用 Linux VM 代理配置交换空间。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../../extensions/agent-linux.md)。

![建议没有足够的交换空间的弹出消息](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 文件
开始安装 SAP 之前，请确保在 /etc/hosts 文件中包含 SAP VM 的主机名和 IP 地址。 将所有 SAP Vm 部署在一个 Azure 虚拟网络中。 然后使用内部 IP 地址，如下所示：

![/etc/hosts 文件中列出的 SAP VM 的主机名和 IP 地址](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 文件

将**nofail**参数添加到 fstab 文件会很有帮助。 这样一来，如果磁盘出错，VM 就不会在启动过程中停止响应。 但请记住，可能没有额外的磁盘空间可用，并且进程可能会填满根文件系统。 如果缺少 /hana，则 SAP HANA 不会启动。

![向 fstab 文件添加 nofail 参数](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 上的图形 GNOME 桌面
本部分介绍如何：

* 在 SLES 12/SLES for SAP Applications 12 上安装 GNOME 桌面和 xrdp。
* 在 SLES 12/SLES for SAP Applications 12 上使用 Firefox 运行基于 Java 的 SAP MC。

你还可以使用其他方法，如本指南中未介绍的 Xterminal 或 VNC。

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>在 SLES 12/SLES for SAP Applications 12 上安装 GNOME 桌面和 xrdp
如果你有 Windows 背景，则可以轻松地在 SAP Linux Vm 中轻松使用图形桌面来运行 Firefox、SAPinst、SAP GUI、SAP MC 或 HANA Studio。 然后，可以通过 Windows 计算机上的远程桌面协议（RDP）连接到 VM。 根据你的公司策略，将 Gui 添加到生产和非生产基于 Linux 的系统，你可能需要在服务器上安装 GNOME。 按照以下步骤在 Azure SLES 12/SLES for SAP Applications 12 VM 上安装 GNOME 桌面。

1. 在 PuTTY 窗口中输入以下命令以安装 GNOME 桌面：

   `zypper in -t pattern gnome-basic`

2. 然后安装 xrdp，以便通过 RDP 连接到虚拟机：

   `zypper in xrdp`

3. 编辑 /etc/sysconfig/windowmanager 并将默认窗口管理器设置为 GNOME：

   `DEFAULT_WM="gnome"`

4. 运行 chkconfig，确保 xrdp 在重新启动后自动启动：

   `chkconfig -level 3 xrdp on`

5. 如果 RDP 连接发生问题，请尝试重新启动，例如从 PuTTY 窗口重新启动：

   `/etc/xrdp/xrdp.sh restart`

6. 如果上一步中提到的 xrdp 重启未成功，请检查 .pid 文件：

   `check /var/run` 

   查找 `xrdp.pid`。 找到后将其删除，然后再次尝试重新启动。

### <a name="start-sap-mc"></a>启动 SAP MC
安装 GNOME 桌面后，从 Firefox 启动基于 Java 的图形 SAP MC。 如果它在 Azure SLES 12/SLES for SAP Applications 12 VM 中运行，则它可能会显示错误。 发生此错误的原因是缺少 Java 浏览器插件。

用于启动 SAP MC 的 URL 是 `<server>:5<instance_number>13`。

有关详细信息，请参阅[启动基于 web 的 SAP 管理控制台](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)。

以下屏幕截图显示了在缺少 Java 浏览器插件时显示的错误消息：

![指明缺少 Java 浏览器插件的错误消息](./media/hana-get-started/image013.jpg)

若要解决此问题，其中一种方法是使用 YaST 安装缺少的插件，如下面的屏幕截图所示：

![使用 YaST 安装缺少的插件](./media/hana-get-started/image014.jpg)

当你重新输入 SAP 管理控制台 URL 时，系统将要求你激活插件：

![要求激活插件的对话框](./media/hana-get-started/image015.jpg)

可能还会收到关于缺少 javafx.properties 文件的错误消息。 它与 Oracle Java 1.8 for SAP GUI 7.4 的要求相关。 有关详细信息，请参阅[SAP 说明 2059429](https://launchpad.support.sap.com/#/notes/2059424)。
IBM Java 版本和与 SLES/SLES for SAP Applications 一起提供的 openjdk 包均不包括所需的 javafx.properties 文件。 解决方法是从 Oracle 下载并安装 Java SE 8。

有关类似的 openSUSE 上 openjdk 问题的信息，请参阅讨论话题 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)。

## <a name="manual-installation-of-sap-hana-swpm"></a>手动安装 SAP HANA：SWPM
本部分中的一系列屏幕截图显示了在将 SWPM 与 SAPinst 结合使用时如何安装 SAP NetWeaver 7.5 和 SAP HANA SP12 的关键步骤。 作为 NetWeaver 7.5 安装的一部分，SWPM 还可以将 HANA 数据库安装为单一实例。

在示例测试环境中，我们安装了一个高级业务应用程序编程（ABAP）应用服务器。 如以下屏幕截图所示，我们使用了**分布式系统**选项在一个 Azure VM 中安装 ASCS 和主应用程序服务器实例。 我们使用 SAP HANA 作为另一个 Azure VM 中的数据库系统。

![使用“分布式系统”选项安装的 ASCS 和主应用程序服务器实例](./media/hana-get-started/image012.jpg)

在应用服务器 VM 上安装 ASCS 实例后，它会在 SAP 管理控制台中通过绿色图标进行标识。 /Sapmnt 目录（包括 SAP 配置文件目录）必须与 SAP HANA DB 服务器 VM 共享。 DB 安装步骤需要访问此信息。 最好的方法是使用可通过 YaST 进行配置的 NFS。

![SAP 管理控制台显示了使用绿色图标在应用服务器 VM 上安装的 ASCS 实例](./media/hana-get-started/image016.jpg)

在应用服务器 VM 上，使用 " **rw** " 和 " **no_root_squash** " 选项通过 NFS 共享/sapmnt 目录。 默认选项为“ro”和“root_squash”，这在安装数据库实例时可能会导致出现问题。

![通过 NFS 并使用 rw 和 no_root_squash 选项共享 /sapmnt 目录](./media/hana-get-started/image017b.jpg)

如以下屏幕截图所示，必须使用**NFS 客户端**和 YaST 在 SAP HANA DB 服务器 vm 上配置应用服务器 vm 中的/sapmnt 共享：

![使用 NFS 客户端配置的/sapmnt 共享](./media/hana-get-started/image018b.jpg)

若要执行分布式 NetWeaver 7.5 安装，即**数据库实例**，请登录到 SAP HANA DB 服务器 VM 并启动 SWPM：

![通过登录到 SAP HANA DB 服务器 VM 并启动 SWPM 来安装数据库实例](./media/hana-get-started/image019.jpg)

选择 "**典型**" 安装和安装媒体的路径后，输入 db SID、主机名、实例编号和 DB 系统管理员密码：

![SAP HANA 数据库系统管理员登录页面](./media/hana-get-started/image035b.jpg)

输入 DBACOCKPIT 架构的密码：

![DBACOCKPIT 架构的密码输入框](./media/hana-get-started/image036b.jpg)

为 SAPABAP1 架构密码输入一个问题：

![为 SAPABAP1 架构密码输入一个问题](./media/hana-get-started/image037b.jpg)

每完成一个任务，DB 安装过程的每个阶段旁边都将显示一个绿色的对号。 将显示消息“执行...数据库实例已完成”。

![包含确认消息的任务已完成窗口](./media/hana-get-started/image023.jpg)

成功安装后，SAP 管理控制台还会显示带有绿色图标的数据库实例。 它显示 SAP HANA 进程的完整列表，如 hdbindexserver 和 hdbcompileserver。

![其中包含 SAP HANA 进程列表的 SAP 管理控制台窗口](./media/hana-get-started/image024.jpg)

以下屏幕截图显示了安装 HANA 期间 SWPM 在 /hana/shared 目录下创建的部分文件结构。 由于没有指定其他路径的选项，因此，在使用 SWPM 安装 SAP HANA 之前，请务必在/hana 目录下装载更多的磁盘空间。 此步骤可防止根文件系统耗尽可用空间。

![安装 HANA 期间创建的 /hana/shared 目录文件结构](./media/hana-get-started/image025.jpg)

此屏幕截图显示了 /usr/sap 目录的文件结构：

![/usr/sap 目录文件结构](./media/hana-get-started/image026.jpg)

分布式 ABAP 安装的最后一个步骤是安装主应用程序服务器实例：

![ABAP 安装，其中主应用程序服务器实例显示为最后一个步骤](./media/hana-get-started/image027b.jpg)

安装主应用程序服务器实例和 SAP GUI 后，使用**DBA 考核**事务确认 SAP HANA 安装是否已正确完成：

![确认安装成功的 DBA Cockpit 窗口](./media/hana-get-started/image028b.jpg)

作为最后一步，你可能需要首先在 SAP 应用服务器 VM 中安装 HANA Studio。 然后连接到在 DB 服务器 VM 上运行的 SAP HANA 实例。

![在 SAP 应用服务器 VM 中安装 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>手动安装 SAP HANA：HDBLCM
除了使用 SWPM 将 SAP HANA 安装为分布式安装的一部分之外，也可以先使用 HDBLCM 独立安装 HANA。 例如，可以安装 SAP NetWeaver 7.5。 此部分的屏幕截图显示了此过程的工作原理。

有关 HANA HDBLCM 工具的详细信息，请参阅：

* [为任务选择正确的 SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)。
* [SAP HANA 生命周期管理工具](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)。
* [SAP HANA server 安装和更新指南](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)。

你想要避免 `\<HANA SID\>adm user`的默认组 ID 设置的问题，该设置由 HDBLCM 工具创建。 通过 HDBLCM 安装 SAP HANA 之前，请使用 "组 ID" `1001`定义名为 `sapsys` 的新组：

![使用组 ID 1001 定义的新组“sapsys”](./media/hana-get-started/image030.jpg)

首次启动 HDBLCM 时，将显示一个简单的 "开始" 菜单。 选择第1项 "**安装新系统**"：

![HDBLCM 启动窗口中的“安装新系统”选项](./media/hana-get-started/image031.jpg)

以下屏幕截图显示了你之前选择的所有主要选项。

> [!IMPORTANT]
> 为 HANA 日志和数据卷命名的目录以及安装路径（在本示例中为/hana/shared）和/usr/sap 不能是根文件系统的一部分。 这些目录属于附加到 VM 的 Azure 数据磁盘。 有关详细信息，请参阅 "磁盘设置" 部分。 

此方法有助于防止根文件系统耗尽空间。 请注意，HANA 系统管理员具有 `1005` 的用户 ID，并且是在安装之前定义的 ID 为 `1001`的 `sapsys` 组的一部分。

![之前选择的所有主要 SAP HANA 组件的列表](./media/hana-get-started/image032.jpg)

检查/etc/passwd 目录中的 `\<HANA SID\>adm user` 详细信息。 查找 `azdadm`，如以下屏幕截图所示：

![/etc/passwd 目录中列出的 HANA \<HANA SID\>adm 用户详细信息](./media/hana-get-started/image033.jpg)

使用 HDBLCM 安装 SAP HANA 后，可以在 SAP HANA Studio 中看到文件结构，如下面的屏幕截图所示。 目前尚未提供包括所有 SAP NetWeaver 表的 SAPABAP1 架构。

![SAP HANA Studio 中的 SAP HANA 文件结构](./media/hana-get-started/image034.jpg)

安装 SAP HANA 之后，可以在其上安装 SAP NetWeaver。 如以下屏幕截图所示，安装是通过使用 SWPM 作为分布式安装执行的。 上一部分介绍了此过程。 使用 SWPM 安装数据库实例时，请使用 HDBLCM 输入相同的数据。 例如，输入主机名、HANA SID 和实例编号。 然后，SWPM 将使用现有 HANA 安装并添加更多架构。

![使用 SWPM 执行的分布式安装](./media/hana-get-started/image035b.jpg)

下面的屏幕截图显示了需要输入 DBACOCKPIT 架构相关数据的 SWPM 安装步骤：

![用于输入 DBACOCKPIT 架构数据的 SWPM 安装步骤](./media/hana-get-started/image036b.jpg)

输入 SAPABAP1 架构的相关数据：

![输入 SAPABAP1 架构的相关数据](./media/hana-get-started/image037b.jpg)

SWPM 数据库实例安装完成后，可以在 SAP HANA Studio 中看到 SAPABAP1 架构：

![SAP HANA Studio 中的 SAPABAP1 架构](./media/hana-get-started/image038b.jpg)

最后，在 SAP 应用服务器和 SAP GUI 安装完成后，使用**DBA 考核**事务验证 HANA DB 实例：

![使用 DBA Cockpit 事务验证的 HANA DB 实例](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 软件下载
可以从 SAP Service Marketplace 下载软件，如以下屏幕截图中所示。

下载适用于 Linux/HANA 的 NetWeaver 7.5：

 ![用于下载 NetWeaver 7.5 的 SAP 服务安装和升级窗口](./media/hana-get-started/image001.jpg)

下载 HANA SP12 Platform Edition：

 ![用于下载 HANA SP12 Platform Edition 的 SAP 服务安装和升级窗口](./media/hana-get-started/image002.jpg)

