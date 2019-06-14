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
ms.date: 09/06/2018
ms.author: hermannd
ms.openlocfilehash: 5091932989849943f00cb71f72378dd17af23a4a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "60204533"
---
# <a name="quickstart-manual-installation-of-single-instance-sap-hana-on-azure-virtual-machines"></a>快速入门：手动安装单实例 SAP HANA Azure 虚拟机上
## <a name="introduction"></a>简介
本指南可帮助你设置单实例 SAP HANA Azure 虚拟机上手动安装 SAP NetWeaver 7.5 和 SAP HANA 1.0 SP12 时。 本指南的重点是如何部署 Azure 上的 SAP HANA。 它不会替代 SAP 文档。 

> [!NOTE]
> 本指南介绍如何将 SAP HANA 部署到 Azure VM 中。 有关如何将 SAP HANA 部署到 HANA 大型实例的信息，请参阅[使用 SAP Azure 虚拟机上](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started)。
 
## <a name="prerequisites"></a>必备组件
本指南假定您熟悉此类基础结构即服务 (IaaS) 基础知识：
 * 如何部署虚拟机 (Vm) 或 Azure 门户或 PowerShell 通过虚拟网络。
 * Azure 跨平台命令行接口 (CLI)，其中包括使用 JavaScript 对象表示法 (JSON) 模板的选项。

本指南还假定您熟悉：
* SAP HANA 和 SAP NetWeaver 以及如何在本地进行安装。
* 如何安装和运行 SAP HANA 和 SAP 应用程序在 Azure 上的新实例。
* 下面的概念和过程关于：
   * 在 Azure 中，其中包括 Azure 虚拟网络规划和 Azure 存储使用情况为 SAP 部署规划。 请参阅[在 Azure 虚拟机的 SAP NetWeaver 规划和实施指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)。
   * Azure 中的部署原则以及部署 VM 的方法。 请参阅[适用于 SAP 的 Azure 虚拟机部署](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)。
   * SAP NetWeaver ABAP SAP 中心服务 (ASCS) /SAP 中心服务 (SCS) 和在 Azure 上的排入队列复制服务器 (ERS) 的高可用性。 请参阅 [Azure VM 上 SAP NetWeaver 的高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide)。
   * 如何提高效率的 Azure 上 ASCS/SCS 多 SID 安装的详细信息。 请参阅[创建 SAP NetWeaver 多 SID 配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-multi-sid)。 
   * 在 Azure 中运行 SAP NetWeaver（基于 Linux 驱动的 VM）的原则。 请参阅[Microsoft Azure SUSE Linux Vm 上运行的 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/suse-quickstart)。 本指南提供了适用于 Linux Azure Vm 中的特定设置。 它还提供有关如何正确地将 Azure 存储磁盘附加到 Linux Vm 的信息。

[IAAS 的 SAP 文档](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)中列出了可用于生产方案的 Azure VM 类型。 对于非生产方案提供了广泛的本机 Azure VM 类型。
有关 VM 配置和操作的详细信息，请参阅[SAP HANA 基础结构配置和 Azure 上的操作](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)。
有关 SAP HANA 高可用性，请参阅[SAP HANA 高可用性的 Azure 虚拟机](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-overview)。

如果你想要获取的 SAP HANA 实例或 S/4HANA 或 BW/4HANA 系统快速部署，请考虑使用[SAP Cloud Appliance Library](https://cal.sap.com)。 您可以找到有关如何部署示例中，通过在 Azure 上，SAP Cloud Appliance Library 的 S/4HANA 系统中的文档[本指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。 你只需是 Azure 订阅，并可使用 SAP Cloud Appliance Library 注册的 SAP 用户。

## <a name="additional-resources"></a>其他资源
### <a name="sap-hana-backup"></a>SAP HANA 备份
有关如何备份 Azure Vm 上的 SAP HANA 数据库的信息，请参阅：
* [Azure 虚拟机上的 SAP HANA 备份指南](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-guide)。
* [在文件级别上的 SAP HANA Azure 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-file-level)。
* [基于存储快照的 SAP HANA 备份](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-backup-storage-snapshots)。

### <a name="sap-cloud-appliance-library"></a>SAP Cloud Appliance Library
若要了解如何使用 SAP Cloud Appliance Library 部署 S/4HANA 或 BW/4HANA，请参阅[部署 SAP S/4HANA 或 Microsoft Azure 上的 BW/4HANA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/cal-s4h)。

### <a name="sap-hana-supported-operating-systems"></a>SAP HANA 支持的操作系统
有关 SAP HANA 支持的操作系统的信息，请参阅[SAP 说明 2235581-SAP HANA:支持的操作系统](https://launchpad.support.sap.com/#/notes/2235581/E)。 Azure VM 仅支持其中的一部分操作系统。 支持使用以下操作系统在 Azure 上部署 SAP HANA： 

* SUSE Linux Enterprise Server 12.x
* Red Hat Enterprise Linux 7.2

如需查看更多有关 SAP HANA 和其他 Linux 操作系统的 SAP 文档，请参阅：

* [SAP 说明 171356:Linux 上的 SAP 软件：常规信息](https://launchpad.support.sap.com/#/notes/1984787)。
* [SAP 说明 1944799:SLES 操作系统安装的 SAP HANA 指导原则](https://go.sap.com/documents/2016/05/e8705aae-717c-0010-82c7-eda71af511fa.html)。
* [SAP 说明 2205917:SAP HANA DB 建议的适用于 SLES 12 for SAP 应用程序的 OS 设置](https://launchpad.support.sap.com/#/notes/2205917/E)。
* [SAP 说明 1391070:Linux UUID 解决方案](https://launchpad.support.sap.com/#/notes/1391070)。
* [SAP 说明 2009879:Red Hat Enterprise Linux (RHEL) 操作系统的 SAP HANA 指导原则](https://launchpad.support.sap.com/#/notes/2009879)。
* [SAP 说明 2292690:SAP HANA DB:建议 OS 设置适用于 RHEL 7](https://launchpad.support.sap.com/#/notes/2292690/E)。

### <a name="sap-monitoring-in-azure"></a>Azure 中的 SAP 监视
有关在 Azure 中 SAP 监视的信息：

* [SAP 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)讨论 SAP 增强型监视与 Azure 上的 Linux Vm。 
* [SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)介绍了与 Linux 上 saposcol 的相关信息。 
* [SAP 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)讨论 Microsoft Azure 上 SAP 关键监控指标。

### <a name="azure-vm-types"></a>Azure VM 类型
Azure VM 类型和用于 SAP HANA 的 SAP 支持的工作负荷方案记录在[SAP 认证 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html)。 

通过了 SAP 的 SAP NetWeaver 和 S/4HANA 应用程序层认证的 azure VM 类型均记录在[SAP 说明 1928533:在 Azure 上的 SAP 应用程序：支持的产品和 Azure VM 类型](https://launchpad.support.sap.com/#/notes/1928533/E)。

> [!NOTE]
> SAP-Linux-Azure 集成仅在 Azure 资源管理器部署模型上受支持，在经典部署模型上不受支持。 

## <a name="manual-installation-of-sap-hana"></a>手动安装 SAP HANA

> [!IMPORTANT]
> 请确保你选择的操作系统经过 SAP 认证的 SAP HANA 上使用的特定 VM 类型。 列表中的 SAP HANA 认证的 VM 类型和 OS 版本中为这些 VM 类型可以中查找[SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)。 请确保单击列出若要获取特定 VM 类型的 SAP HANA 支持的 OS 版本的完整列表的 VM 类型的详细信息。 对于本文档中的示例，我们使用不支持针对 M 系列 Vm 上的 SAP HANA 的 SAP 的 SUSE Linux Enterprise Server (SLES) OS 版本。
>

本指南介绍了在 Azure VM 上手动安装 SAP HANA 的两种不同方法：

* SAP Software Provisioning Manager (SWPM) 用作分布式 NetWeaver 安装在"安装数据库实例"步骤中的一部分。
* 使用 SAP HANA 数据库生命周期管理器 (HDBLCM) 工具，并安装 NetWeaver。

此外可以使用 SWPM 在单个 VM 中安装所有组件，如 SAP HANA、 SAP 应用程序服务器和 ASCS 实例。 在此介绍的步骤[SAP HANA 博客声明](https://blogs.saphana.com/2013/12/31/announcement-sap-hana-and-sap-netweaver-as-abap-deployed-on-one-server-is-generally-available/)。 此选项未介绍在此快速入门指南中，但必须考虑的问题是相同的。

在开始安装之前，我们建议您阅读"准备 Azure Vm 来手动安装 SAP HANA 进行"本指南后面的部分。 这样做有助于防止当仅使用默认 Azure VM 配置时可能会发生的一些基本错误。

## <a name="key-steps-for-sap-hana-installation-when-you-use-sap-swpm"></a>使用 SAP SWPM 时安装 SAP HANA 的主要步骤
本部分列出了当使用 SAP SWPM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 时的主要步骤。 本指南后面部分中的屏幕截图对各个步骤进行了更详细地说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 部署两个带有操作系统按照 Azure 资源管理器模型的 Azure Vm。 此示例使用 SUSE Linux Enterprise Server 和 SLES for SAP Applications 12 SP1。 
3. 将两个 Azure 标准或高级存储磁盘，例如，75-GB 或 500-GB 磁盘附加到应用程序服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的"磁盘设置"部分。
5. 根据大小或吞吐量需求，附加多个磁盘。 然后，创建带区的卷。 在 VM 内的 OS 级别使用逻辑卷管理 (LVM) 或多个设备管理 (mdadm) 工具。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如为所有 SAP 软件使用一个文件系统。 为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是防止被填满根文件系统所必需的。 根文件系统不是很大 Linux Azure Vm 上。 
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数  。
10. 设置 Linux 内核参数根据您使用的 Linux OS 版本。 有关详细信息，请参阅讨论 HANA 和本指南中的"内核参数"部分的 SAP 说明。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在应用服务器 VM 上安装 SAP ASCS 实例。
15. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
16. 安装数据库实例，其中包括 HANA 中，使用 SWPM 在 DB 服务器 VM 上。
17. 在应用程序服务器 VM 上安装主应用程序服务器 (PAS)。
18. 启动 SAP 管理控制台 (SAP MC)。 例如使用 SAP GUI 或 HANA Studio 进行连接。

## <a name="key-steps-for-sap-hana-installation-when-you-use-hdblcm"></a>使用 HDBLCM 时安装 SAP HANA 的主要步骤
本部分列出了使用 SAP HDBLCM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装单实例 SAP HANA 的主要步骤。 本指南中的屏幕截图对各个步骤进行了更详细的说明。

1. 创建包含两个测试 VM 的 Azure 虚拟网络。
2. 部署带有操作系统按照 Azure 资源管理器模型的两个 Azure Vm。 此示例使用 SLES 和 SLES for SAP Applications 12 SP1。
3. 将两个 Azure 标准或高级存储磁盘，例如，75-GB 或 500-GB 磁盘附加到应用服务器 VM。
4. 将高级存储磁盘附加到 HANA DB 服务器 VM。 有关详细信息，请参阅本指南后面的"磁盘设置"部分。
5. 根据大小或吞吐量需求，附加多个磁盘。 使用逻辑卷管理或 mdadm 工具在 VM 内的 OS 级别创建带区的卷。
6. 在附加的磁盘或逻辑卷上创建 XFS 文件系统。
7. 在 OS 级别装载新的 XFS 文件系统。 例如为所有 SAP 软件使用一个文件系统。 为 /sapmnt 目录和备份使用另一个文件系统。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是必要的防止根文件系统填满。 根文件系统不是很大 Linux Azure Vm 上。
8. 在 /etc/hosts 文件中输入测试 VM 的本地 IP 地址。
9. 在 /etc/fstab 文件中输入 nofail 参数  。
10. 设置内核参数根据您使用的 Linux OS 版本。 有关详细信息，请参阅讨论 HANA 和本指南中的"内核参数"部分的 SAP 说明。
11. 添加交换空间。
12. （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
13. 从 SAP Service Marketplace 下载 SAP 软件。
14. 在 HANA DB 服务器 VM 上创建组 ID 为 1001 的组 sapsys。
15. 安装 SAP HANA DB 服务器 VM 上使用 HANA 数据库生命周期管理器。
16. 在应用服务器 VM 上安装 SAP ASCS 实例。
17. 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用程序服务器 VM 是 NFS 服务器。
18. 安装数据库实例，其中包括 HANA 中，使用 SWPM 在 HANA DB 服务器 VM 上。
19. 应用程序服务器 VM 上安装主应用程序服务器。
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
在安装其他软件之前检查 Linux OS 更新和修补程序。 通过安装一个修补程序，可能避免给支持人员的调用。

请确保您使用：
* SUSE Linux Enterprise Server for SAP Applications。
* 适用于 SAP Applications 的 Red Hat Enterprise Linux 或适用于 SAP HANA 的 Red Hat Enterprise Linux。 

如果你尚未这样做，请使用从 Linux 供应商 Linux 订阅注册 OS 部署。 SUSE 已针对 SAP 应用程序的已包含服务，且会自动注册 OS 映像。

下面是如何通过使用适用于 SUSE Linux 检查的可用修补程序的示例**zypper**命令：

 `sudo zypper list-patches`

根据问题种类，可按类别和严重性对修补程序进行分类。 类别的常用的值有： 
- 安全
- 建议
- 可选
- Feature
- Document
- yast

严重性的常用的值有：

- 严重
- 重要说明
- 中等
- 低
- 未指定

zypper 命令仅查找已安装的程序包的所需更新  。 例如，可以使用此命令：

`sudo zypper patch  --category=security,recommended --severity=critical,important`

可添加参数 `--dry-run` 来测试更新，而不实际更新系统。


### <a name="disk-setup"></a>磁盘设置
Azure 上 Linux VM 中的根文件系统具有大小限制。 因此，您需要将额外的磁盘空间附加到要运行 SAP 的 Azure VM。 对于 SAP 应用程序服务器 Azure Vm，Azure 标准存储磁盘的使用可能已足够。 对于 SAP HANA DBMS Azure Vm，Azure 高级存储磁盘用于生产和非生产实现是必需的。

基于[SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)，建议使用以下 Azure 高级存储配置： 

| VM SKU | RAM |  /hana/data and /hana/log <br /> 使用 LVM 或 mdadm 的条带化 | /hana/shared | /root 卷 | /usr/sap |
| --- | --- | --- | --- | --- | --- |
| GS5 | 448 GB | 2 x P30 | 1 x P20 | 1 x P10 | 1 x P10 | 

在建议的磁盘配置中，Azure 高级存储磁盘进行条带化与 LVM 或 mdadm 在同一组上放置 HANA 数据卷和日志卷。 不需要定义任何 RAID 冗余级别，因为 Azure 高级存储会保留三个映像的磁盘的冗余。 

若要确保配置足够的存储，请参阅[SAP HANA TDI 存储要求](https://www.sap.com/documents/2015/03/74cdb554-5a7c-0010-82c7-eda71af511fa.html)并[SAP HANA 服务器安装和更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。 此外应考虑不同 Azure 高级存储磁盘的不同虚拟硬盘 (VHD) 吞吐量卷，如中所述[高性能高级存储和托管的磁盘 Vm](../../windows/disks-types.md)。 

可以将多个高级存储磁盘添加到 HANA DBMS Vm，用于存储数据库或事务日志备份。

用于配置条带化的两个主要工具的详细信息，请参阅：

* [Linux 上配置软件 RAID](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [在 Azure 中的 Linux VM 上配置 LVM](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

有关如何将磁盘附加到运行 Linux 作为来宾 OS 的 Azure Vm 的详细信息，请参阅[将磁盘添加到 Linux VM](../../linux/add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

使用 Azure 高级 Ssd，您可以定义磁盘缓存模式。 对于保存 /hana/data 和 /hana/log 的条带化集，禁用磁盘缓存。 对于其他卷，也就是说，磁盘，将缓存模式设置为**ReadOnly**。

若要找到要用于创建 Vm 的示例 JSON 模板，请参阅[Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)。
vm-simple-sles 模板是基本模板。 它包含具有额外 100-GB 数据磁盘的存储区。 使用此模板为基础。 可将模板调整为特定配置。

> [!NOTE]
> 务必要中所述使用 UUID 附加 Azure 存储磁盘[Microsoft Azure SUSE Linux Vm 上运行 SAP NetWeaver](suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

在测试环境中，两个 Azure 标准存储磁盘被附加到 SAP 应用服务器 VM，如以下屏幕截图中所示。 一个磁盘存储的所有 SAP 软件，例如 NetWeaver 7.5 和 SAP GUI、 SAP HANA 的安装。 第二个磁盘确保有足够的可用空间可供其他要求。 例如，需要属于同一 SAP 布局的所有 Vm 之间共享 /sapmnt 目录，即 SAP 配置文件和备份和测试数据。

![SAP HANA 应用服务器磁盘窗口，其中显示了两个数据磁盘及其大小](./media/hana-get-started/image003.jpg)


### <a name="kernel-parameters"></a>内核参数
SAP HANA 要求使用特定 Linux 内核设置。 这些内核设置不属于标准 Azure 库映像，必须手动设置。 参数可能不同，具体取决于使用 SUSE 还是 Red Hat。 以前，列出的 SAP 说明提供有关这些参数的信息。 屏幕截图中使用了 SUSE Linux 12 SP1。 

SLES for SAP Applications 12 正式发布和 SLES for SAP Applications 12 SP1 还可使用新工具 **-tuned-adm**，替换旧**sapconf**工具。 有一个特殊的 SAP HANA 配置文件可用于 tuned-adm  。 若要优化 SAP HANA 系统，以根用户身份输入以下配置文件：

   `tuned-adm profile sap-hana`

有关 tuned-adm 的详细信息，请参阅[关于 tuned-adm 的 SUSE 文档](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)  。

在以下屏幕截图中，您可以看到如何 **-tuned-adm**更改`transparent_hugepage`和`numa_balancing`值，根据所需的 SAP HANA 设置：

![tuned-adm 工具根据所需的 SAP HANA 设置更改值](./media/hana-get-started/image005.jpg)

若要永久保存 SAP HANA 内核设置，在 SLES 12 上请使用 grub2  。 有关详细信息**grub2**，请参阅[配置文件结构](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)SUSE 文档的部分。

下面的屏幕截图显示了内核设置在配置文件中进行了怎样的更改，然后怎样使用 grub2-mkconfig 进行了编译  ：

![配置文件中已更改并使用 grub2-mkconfig 进行了编译的内核设置](./media/hana-get-started/image006.jpg)

另一种选择是通过 YaST 和“启动加载程序” > “内核参数”设置来更改这些设置   ：

![YaST 启动加载程序中的“内核参数”设置选项卡](./media/hana-get-started/image007.jpg)

### <a name="file-systems"></a>文件系统
以下屏幕截图显示了基于两个附加的 Azure 标准存储磁盘在 SAP 应用服务器 VM 上创建的两个文件系统。 这两个文件系统都是 XFS 类型并装载到 /sapdata 和 /sapsoftware。

它不是必需的这种方式构建您的文件系统。 可以用于构建的磁盘空间的其他选项。 最重要的注意事项是要防止根文件系统的可用空间耗尽。

![在 SAP 应用服务器 VM 上创建的两个文件系统](./media/hana-get-started/image008.jpg)

有关 SAP HANA DB VM，安装数据库期间，当使用 SWPM 使用 SAPinst 并**典型**安装选项时，所有内容安装到 /hana 和 /usr/sap。 SAP HANA 日志备份的默认位置位于 /usr/sap 中。 同样，务必防止根文件系统耗尽存储空间。 请确保在使用 SWPM 安装 SAP HANA 之前没有足够的可用空间 /hana 和 /usr/sap。

有关 SAP HANA 的标准文件系统布局的说明，请参阅[SAP HANA 服务器安装和更新指南](https://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)。

![在 SAP 应用服务器 VM 上创建的额外文件系统](./media/hana-get-started/image009.jpg)

当您在标准 SLES/SLES for SAP Applications 12 Azure 库映像上安装 SAP NetWeaver 时，则显示一条消息，指出没有交换空间，如以下屏幕截图中所示。 若要消除此消息，可通过使用 dd、mkswap 和 swapon 手动添加交换文件    。 若要了解如何操作，请搜索"中的添加交换文件手动[使用 YaST 分区程序](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/sles-for-sap-12-sp1.zip)SUSE 文档的部分。

另一种选择是使用 Linux VM 代理配置交换空间。 有关详细信息，请参阅 [Azure Linux 代理用户指南](../../extensions/agent-linux.md)。

![弹出消息，通知没有交换空间不足](./media/hana-get-started/image010.jpg)


### <a name="the-etchosts-file"></a>/etc/hosts 文件
开始安装 SAP 之前，请确保在 /etc/hosts 文件中包含 SAP VM 的主机名和 IP 地址。 将一个 Azure 虚拟网络中的所有 SAP Vm 都部署。 然后使用内部 IP 地址，如下所示：

![/etc/hosts 文件中列出的 SAP VM 的主机名和 IP 地址](./media/hana-get-started/image011.jpg)

### <a name="the-etcfstab-file"></a>/etc/fstab 文件

最好添加**nofail** fstab 文件的参数。 这样一来，如果出现问题的磁盘，不会停止 VM 在启动过程中响应。 但请记住，可能没有额外的磁盘空间可用，并且进程可能会填满根文件系统。 如果缺少 /hana，则 SAP HANA 不会启动。

![向 fstab 文件添加 nofail 参数](./media/hana-get-started/image000c.jpg)

## <a name="graphical-gnome-desktop-on-sles-12sles-for-sap-applications-12"></a>SLES 12/SLES for SAP Applications 12 上的图形 GNOME 桌面
本部分介绍如何：

* 安装 GNOME 桌面和 xrdp 在 SLES 12/SLES for SAP Applications 12。
* 在 SLES 12/SLES for SAP Applications 12 上使用 Firefox 运行基于 Java 的 SAP MC。

此外可以使用例如 Xterminal 或 VNC，这不本指南中所述的替代项。

### <a name="install-the-gnome-desktop-and-xrdp-on-sles-12sles-for-sap-applications-12"></a>安装 GNOME 桌面和 xrdp 在 SLES 12/SLES for SAP Applications 12
如果您有 Windows 背景，可轻松用于直接在 SAP Linux Vm 图形桌面运行 Firefox、 SAPinst、 SAP GUI、 SAP MC 或 HANA Studio。 然后您可以连接到 VM 通过远程桌面协议 (RDP) 从 Windows 计算机。 具体取决于你如何将图形用户界面添加到生产环境和非生产基于 Linux 的系统的公司策略，你可能想要在服务器上安装 GNOME。 请按照下列步骤在 Azure SLES 12/SLES for SAP Applications 12 VM 安装 GNOME 桌面。

1. 在 PuTTY 窗口中输入以下命令，例如，来安装 GNOME 桌面：

   `zypper in -t pattern gnome-basic`

2. 然后安装 xrdp，以便通过 RDP 连接到虚拟机：

   `zypper in xrdp`

3. 编辑 /etc/sysconfig/windowmanager 并将默认窗口管理器设置为 GNOME：

   `DEFAULT_WM="gnome"`

4. 运行 chkconfig，确保 xrdp 在重新启动后自动启动  ：

   `chkconfig -level 3 xrdp on`

5. 如果您有 RDP 连接发生问题，请尝试重启，例如，从 PuTTY 窗口中：

   `/etc/xrdp/xrdp.sh restart`

6. 如果上一步中提到的 xrdp 重启未成功，请检查 .pid 文件：

   `check /var/run` 

   查找 `xrdp.pid`。 找到后将其删除，然后再次尝试重新启动。

### <a name="start-sap-mc"></a>启动 SAP MC
安装 GNOME 桌面后，请从 Firefox 启动基于 Java 的图形 SAP MC。 如果它在运行 Azure SLES 12/SLES for SAP Applications 12 VM，它可能显示错误。 由于缺少 Java 浏览器插件而发生此错误。

用于启动 SAP MC 的 URL 是 `<server>:5<instance_number>13`。

有关详细信息，请参阅[启动基于 web 的 SAP 管理控制台](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)。

下面的屏幕截图显示了缺少 Java 浏览器插件时显示的错误消息：

![指明缺少 Java 浏览器插件的错误消息](./media/hana-get-started/image013.jpg)

若要解决此问题，其中一种方法是使用 YaST 安装缺少的插件，如下面的屏幕截图所示：

![使用 YaST 安装缺少的插件](./media/hana-get-started/image014.jpg)

时重新输入 SAP 管理控制台 URL 时，系统将要求激活该插件：

![要求激活插件的对话框](./media/hana-get-started/image015.jpg)

可能还会收到关于缺少 javafx.properties 文件的错误消息。 它与 SAP GUI 7.4 的 Oracle Java 1.8 的要求。 有关详细信息，请参阅[SAP 说明 2059429](https://launchpad.support.sap.com/#/notes/2059424)。
IBM Java 版本和随 SLES/SLES for SAP Applications 12 的 openjdk 包不包含所需的 javafx.properties 文件。 解决方法是从 Oracle 下载并安装 Java SE 8。

有关类似的 openSUSE 上 openjdk 问题的信息，请参阅讨论话题 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)。

## <a name="manual-installation-of-sap-hana-swpm"></a>手动安装 SAP HANA：SWPM
在本部分中的屏幕截图系列显示了如何在使用 SAPinst 使用 SWPM 安装 SAP NetWeaver 7.5 和 SAP HANA SP12 的主要步骤。 在 NetWeaver 7.5 安装的一部分，SWPM 还可以将 HANA 数据库安装为单一实例。

在示例测试环境中，我们安装了一个高级业务应用程序编程 (ABAP) 应用服务器。 如以下屏幕截图中所示，我们使用**分布式系统**选项以在 Azure VM 中安装的 ASCS 和主应用程序服务器实例。 我们使用 SAP HANA 作为数据库系统中另一个 Azure VM。

![使用“分布式系统”选项安装的 ASCS 和主应用程序服务器实例](./media/hana-get-started/image012.jpg)

应用服务器 VM 上安装 ASCS 实例后，它是由 SAP 管理控制台中的绿色图标进行标识。 必须与 SAP HANA DB 服务器 VM 共享 /sapmnt 目录，其中包括 SAP 配置文件目录。 DB 安装步骤需要访问此信息。 最好的方法是使用可通过 YaST 进行配置的 NFS。

![显示应用程序服务器使用一个绿色图标的 VM 上安装的 ASCS 实例的 SAP 管理控制台](./media/hana-get-started/image016.jpg)

在应用服务器 VM 上，共享 /sapmnt 目录是通过 NFS 使用**rw**并**no_root_squash**选项。 默认选项为“ro”和“root_squash”，这在安装数据库实例时可能会导致出现问题   。

![通过 NFS 并使用 rw 和 no_root_squash 选项共享 /sapmnt 目录](./media/hana-get-started/image017b.jpg)

SAP HANA DB 服务器 VM 上通过使用如下面的屏幕截图所示，必须配置应用服务器 VM 中的 /sapmnt 共享**NFS 客户端**和 YaST:

![通过使用 NFS 客户端配置的 /sapmnt 共享](./media/hana-get-started/image018b.jpg)

若要执行分布式的 NetWeaver 7.5 安装，即**数据库实例**，登录到 SAP HANA DB 服务器 VM 并启动 SWPM:

![通过登录到 SAP HANA DB 服务器 VM 并启动 SWPM 来安装数据库实例](./media/hana-get-started/image019.jpg)

选择后**典型**安装和安装媒体的路径输入 DB SID、 主机名、 实例编号和 DB 系统管理员密码：

![SAP HANA 数据库系统管理员登录页面](./media/hana-get-started/image035b.jpg)

输入 DBACOCKPIT 架构的密码：

![DBACOCKPIT 架构的密码输入框](./media/hana-get-started/image036b.jpg)

为 SAPABAP1 架构密码输入一个问题：

![为 SAPABAP1 架构密码输入一个问题](./media/hana-get-started/image037b.jpg)

每完成一个任务，DB 安装过程的每个阶段旁边都将显示一个绿色的对号。 将显示消息“执行...数据库实例已完成”。

![包含确认消息的任务已完成窗口](./media/hana-get-started/image023.jpg)

安装成功后，SAP 管理控制台还显示一个绿色图标的 DB 实例。 它显示完整的 SAP HANA 进程，例如 hdbindexserver 和 hdbcompileserver 列表。

![其中包含 SAP HANA 进程列表的 SAP 管理控制台窗口](./media/hana-get-started/image024.jpg)

以下屏幕截图显示了安装 HANA 期间 SWPM 在 /hana/shared 目录下创建的部分文件结构。 由于没有选项用于指定不同的路径，务必使用 SWPM 安装 SAP HANA 安装前在 /hana 目录下的更多的磁盘空间。 此步骤可防止根文件系统，可用空间不足。

![安装 HANA 期间创建的 /hana/shared 目录文件结构](./media/hana-get-started/image025.jpg)

此屏幕截图显示了 /usr/sap 目录的文件结构：

![/usr/sap 目录文件结构](./media/hana-get-started/image026.jpg)

分布式 ABAP 安装的最后一个步骤是安装主应用程序服务器实例：

![ABAP 安装，其中主应用程序服务器实例显示为最后一个步骤](./media/hana-get-started/image027b.jpg)

安装主应用程序服务器实例和 SAP GUI 后，使用**DBA Cockpit**事务确认 SAP HANA 安装，已正确完成：

![确认安装成功的 DBA Cockpit 窗口](./media/hana-get-started/image028b.jpg)

作为最后一步，你可能需要在 SAP 应用服务器 VM 中的第一个安装 HANA Studio。 然后连接到 DB 服务器 VM 运行的 SAP HANA 实例。

![在 SAP 应用服务器 VM 中安装 SAP HANA Studio](./media/hana-get-started/image038b.jpg)

## <a name="manual-installation-of-sap-hana-hdblcm"></a>手动安装 SAP HANA：HDBLCM
除了使用 SWPM 将 SAP HANA 安装为分布式安装的一部分之外，也可以先使用 HDBLCM 独立安装 HANA。 例如，可以安装 SAP NetWeaver 7.5。 此部分的屏幕截图显示了此过程的工作原理。

有关 HANA HDBLCM 工具的详细信息，请参阅：

* [选择您的任务的正确的 SAP HANA HDBLCM](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)。
* [SAP HANA 生命周期管理工具](https://www.tutorialspoint.com/sap_hana_administration/sap_hana_administration_lifecycle_management.htm)。
* [SAP HANA 服务器安装和更新指南](https://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)。

你想要避免出现问题的默认组 ID 设置`\<HANA SID\>adm user`，这由 HDBLCM 工具创建。 定义名为的新组，然后才能安装 SAP HANA 通过 HDBLCM`sapsys`通过使用组 ID `1001`:

![使用组 ID 1001 定义的新组“sapsys”](./media/hana-get-started/image030.jpg)

第一次启动 HDBLCM 时出现简单的开始菜单显示。 选择第 1 项**安装新系统**:

![HDBLCM 启动窗口中的“安装新系统”选项](./media/hana-get-started/image031.jpg)

以下屏幕截图显示了你之前选择的所有主要选项。

> [!IMPORTANT]
> 指定为 HANA 日志和数据卷和安装路径，即在此示例中和 /usr/sap 中的 /hana/shared 的目录不能属于根文件系统。 这些目录属于附加到 VM 的 Azure 数据磁盘。 有关详细信息，请参阅"磁盘设置"部分。 

此方法有助于防止根文件系统耗尽空间。 请注意，HANA 系统管理员具有用户 ID`1005`并且属于`sapsys`组中，id 为`1001`，安装前定义的。

![之前选择的所有主要 SAP HANA 组件的列表](./media/hana-get-started/image032.jpg)

检查`\<HANA SID\>adm user`/etc/passwd 目录中的详细信息。 查找`azdadm`，如以下屏幕截图中所示：

![/etc/passwd 目录中列出的 HANA \<HANA SID\>adm 用户详细信息](./media/hana-get-started/image033.jpg)

使用 HDBLCM 安装 SAP HANA 后，可以在 SAP HANA Studio 中看到文件结构，如下面的屏幕截图所示。 目前尚未提供包括所有 SAP NetWeaver 表的 SAPABAP1 架构。

![SAP HANA Studio 中的 SAP HANA 文件结构](./media/hana-get-started/image034.jpg)

安装 SAP HANA 之后，可以在其上安装 SAP NetWeaver。 下面的屏幕截图中所示，安装是通过 swpm 作为分布式安装执行。 此过程是在上一部分中所述。 当使用 SWPM 安装数据库实例时，则使用 HDBLCM 输入相同的数据。 例如，输入主机名、 HANA SID 和实例编号。 然后，SWPM 将使用现有 HANA 安装并添加更多架构。

![使用 SWPM 执行的分布式安装](./media/hana-get-started/image035b.jpg)

下面的屏幕截图显示了需要输入 DBACOCKPIT 架构相关数据的 SWPM 安装步骤：

![用于输入 DBACOCKPIT 架构数据的 SWPM 安装步骤](./media/hana-get-started/image036b.jpg)

输入 SAPABAP1 架构的相关数据：

![输入 SAPABAP1 架构的相关数据](./media/hana-get-started/image037b.jpg)

完成 SWPM 数据库实例安装后，可以看到在 SAP HANA Studio 中的 SAPABAP1 架构：

![SAP HANA Studio 中的 SAPABAP1 架构](./media/hana-get-started/image038b.jpg)

最后，在 SAP 应用服务器和 SAP GUI 安装完成后，验证使用的 HANA DB 实例**DBA Cockpit**事务：

![使用 DBA Cockpit 事务验证的 HANA DB 实例](./media/hana-get-started/image039b.jpg)


## <a name="sap-software-downloads"></a>SAP 软件下载
可以从 SAP Service Marketplace 下载软件，如以下屏幕截图中所示。

下载适用于 Linux/HANA 的 NetWeaver 7.5：

 ![用于下载 NetWeaver 7.5 SAP 服务安装和升级窗口](./media/hana-get-started/image001.jpg)

下载 HANA SP12 Platform Edition：

 ![下载 HANA SP12 Platform Edition SAP 服务安装和升级窗口](./media/hana-get-started/image002.jpg)

