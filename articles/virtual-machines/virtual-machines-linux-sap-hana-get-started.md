---
title: "快速入门指南：在 Azure VM 上手动安装单实例 SAP HANA | Microsoft 文档"
description: "在 Azure VM 上手动安装单实例 SAP HANA 的快速入门指南"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: c51a2a06-6e97-429b-a346-b433a785c9f0
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
translationtype: Human Translation
ms.sourcegitcommit: d8fe055ec2912b4bf8ba2c86ba63335cc6ac2600
ms.openlocfilehash: 0683008470ade23abc5cf6c76ff945abdf303617


---
# <a name="quick-start-guide-manual-installation-of-single-instance-sap-hana-on-azure-vms"></a>快速入门指南：在 Azure VM 上手动安装单实例 SAP HANA
## <a name="introduction"></a>介绍
当手动安装 SAP NetWeaver 7.5 和 SAP HANA SP12 时，本快速入门指南可帮助你在 Azure 虚拟机 (VM) 上设置单实例 SAP HANA 原型或演示系统。

本指南假定你熟悉如下 Azure IaaS 基础知识：
 * 如何通过 Azure 门户或 PowerShell 部署虚拟机或虚拟网络。
 * Azure 跨平台命令行工具 (CLI)，包括用来使用 JavaScript 对象通知 (JSON) 模板的选项。

本指南还假定你熟悉 SAP HANA 和 SAP NetWeaver，以及如何在本地安装它们。

此外，你应当留意本指南末尾的“一般信息”部分中提到的 SAP Azure 文档。

因为本指南的内容仅限于非生产系统，因此未涵盖诸如高可用性 (HA)、备份、灾难恢复 (DR)、高性能或特殊安全注意事项之类的主题。

SAP-Linux-Azure 仅在 Azure Resource Manager 部署模型上受支持，在经典部署模型上不受支持。 因此，我们通过 Azure Resource Manager 模型执行了一个使用两个虚拟机的示例设置来完成一个分布式 SAP NetWeaver 安装。 有关 Resource Manager 的详细信息，请参阅本指南末尾的“一般信息”部分。

对于示例安装，我们使用了以下两个测试 VM：

* hana-appsrv (type DS3)，用于承载 NetWeaver 7.5 ABAP SAP Central Services (ASCS) 实例 + PAS
* hana-dbsrv（类型 GS4），用于承载 HANA SP12

这两个 VM 属于同一个 Azure 虚拟网络 (azure-hana-test-vnet)，并且它们中的 OS 都是 SLES 12 SP1。

自 2016 年 7 月起，只有 Azure VM 类型 GS5 上的 OLAP (BW) 生产系统完全支持 SAP HANA。 对于测试目的，如果不需要官方 SAP 支持，可以使用较小的类型，例如 GS4。 对于 Azure 上的 SAP HANA，应始终使用 Azure 高级存储来保存 HANA 数据和日志文件（请参阅本指南下文中的“磁盘设置”部分）。 有关 Azure 支持哪些 SAP 产品的详细信息，请参阅本指南末尾的“一般信息”部分。

本指南介绍了在 Azure VM 上手动安装 SAP HANA 的两种不同方法：

* 在“安装数据库实例”步骤中将 SAP Software Provisioning Manager (SWPM) 用作分布式 NetWeaver 安装的一部分。
* 使用 HANA 生命周期管理工具 hdblcm，然后安装 NetWeaver。

此外，还可以使用 SWPM，并在单个 VM 中安装所有组件（SAP HANA、SAP 应用服务器、ASCS 实例、SAP GUI）。 该选项未在本指南中介绍，但必须注意的一些事项是相同的。

在开始安装之前，请务必阅读“为手动安装 SAP HANA 准备 Azure VM”，它紧跟在两个 SAP HANA 安装清单之后。 这样做有助于防止当仅使用默认 Azure VM 配置时可能会发生的一些基本错误。

## <a name="checklist-for-sap-hana-installation-using-sap-swpm"></a>使用 SAP SWPM 安装 SAP HANA 时的清单
本部分列出了当使用 SAP SWPM 执行分布式 SAP NetWeaver 7.5 安装时，手动安装用于演示或原型制作用途的单实例 SAP HANA 时的主要步骤。 本指南将借助屏幕截图更详细地解释各个事项。

* 创建包含两个测试 VM 的 Azure 虚拟网络。
* 按照 Azure Resource Manager 模型部署装有 OS SLES 12 SP1 的两个 Azure VM
* 将两个标准存储磁盘（例如 75GB 或 500GB 磁盘）附加到应用服务器 VM。
* 将四个磁盘附加到 HANA DB 服务器 VM：两个标准存储磁盘（与用于应用服务器 VM 的那些磁盘一样）和两个高级存储磁盘（例如两个 512-GB 磁盘）。
* 根据大小或吞吐量需求，附加多个磁盘，并使用逻辑卷管理 (LVM) 或多设备管理实用工具 (mdadm) 在 VM 内在 OS 级别创建带区卷。
* 在附加的磁盘/逻辑卷上创建 XFS 文件系统。
* 在 OS 级别装载新的 XFS 文件系统。 使用一个文件系统来保存所有 SAP 软件，使用另一个文件系统来保存诸如 /sapmnt 之类的目录（也许还包括备份）。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是必要的，它可以防止根文件系统（在 Linux Azure VM 上不是很大）被填满。
* 在 /etc/hosts 中输入测试 VM 的本地 IP 地址。
* 在 /etc/fstab 中输入 nofail 参数。
* 根据 HANA-SLES-12 SAP 说明设置内核参数。 有关详细信息，请参阅“内核参数”部分。
* 添加交换空间。
* （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
* 从 SAP 服务应用商店下载 SAP 软件。
* 在应用服务器 VM 上安装 SAP ASCS 实例。
* 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用服务器 VM 是 NFS 服务器。
* 使用 SWPM 在 DB 服务器 VM 上安装数据库实例（包括 HANA）。
* 在应用服务器 VM 上安装主应用程序服务器 (PAS)。
* 启动 SAP 管理控制台 (MC) 并与诸如 SAP GUI/HANA Studio 之类的程序进行连接。

## <a name="checklist-for-sap-hana-installation-using-hdblcm"></a>使用 hdblcm 安装 SAP HANA 时的清单
本部分列出了当使用 SAP hdblcm 执行分布式 SAP NetWeaver 7.5 安装时，手动安装用于演示或原型制作用途的单实例 SAP HANA 时的主要步骤。 本指南将借助屏幕截图更详细地解释各个事项。

* 创建包含两个测试 VM 的 Azure 虚拟网络。
* 按照 Azure Resource Manager 模型部署装有 OS SLES 12 SP1 的两个 Azure VM
* 将两个标准存储磁盘（例如 75GB 或 500GB 磁盘）附加到应用服务器 VM。
* 将四个磁盘附加到 HANA DB 服务器 VM：两个标准存储磁盘（与用于应用服务器 VM 的那些磁盘一样）和两个高级存储磁盘（例如两个 512-GB 磁盘）。
* 根据大小或吞吐量需求，附加多个磁盘，并使用逻辑卷管理 (LVM) 或多设备管理实用工具 (mdadm) 在 VM 内在 OS 级别创建带区卷。
* 在附加的磁盘/逻辑卷上创建 XFS 文件系统。
* 在 OS 级别装载新的 XFS 文件系统。 使用一个文件系统来保存所有 SAP 软件，使用另一个文件系统来保存诸如 /sapmnt 之类的目录（也许还包括备份）。 在 SAP HANA DB 服务器上，在高级存储磁盘上将 XFS 文件系统装载为 /hana 和 /usr/sap。 此过程是必要的，它有助于防止根文件系统（在 Linux Azure VM 上不是很大）被填满。
* 在 /etc/hosts 中输入测试 VM 的本地 IP 地址。
* 在 /etc/fstab 中输入 nofail 参数。
* 根据 HANA-SLES-12 SAP 说明设置内核参数。 有关详细信息，请参阅“内核参数”部分。
* 添加交换空间。
* （可选）在测试 VM 上安装图形桌面。 否则，请使用远程 SAPinst 安装。
* 从 SAP 服务应用商店下载 SAP 软件。
* 在 HANA DB 服务器 VM 上创建组 ID 为 1001 的组“sapsys”。
* 使用 HANA 数据库生命周期管理器 (HDBLCM) 在 DB 服务器 VM 上安装 SAP HANA。
* 在应用服务器 VM 上安装 SAP ASCS 实例。
* 使用 NFS 在测试 VM 之间共享 /sapmnt 目录。 应用服务器 VM 是 NFS 服务器。
* 使用 SWPM 在 HANA DB 服务器 VM 上安装数据库实例（包括 HANA）。
* 在应用服务器 VM 上安装主应用程序服务器 (PAS)。
* 启动 SAP MC 并通过 SAP GUI/HANA Studio 进行连接。

## <a name="prepare-azure-vms-for-a-manual-installation-of-sap-hana"></a>为手动安装 SAP HANA 准备 Azure VM
本部分涵盖了以下五个主题：

* 磁盘设置
* 内核参数
* 文件系统
* /etc/hosts
* /etc/fstab

### <a name="disk-setup"></a>磁盘设置
Azure 上 Linux VM 中的根文件系统大小有限制。 因此，有必要为运行 SAP 的 VM 附加额外的磁盘空间。 如果 SAP 应用服务器 VM 用于单纯的原型/演示环境中，则使用 Azure 标准存储磁盘即可。 对于 SAP HANA DB 数据和日志文件，请使用 Azure 高级存储磁盘，即使是在非生产布局中也是如此。

有关详细信息，请参阅[如何向 Linux VM 附加磁盘](virtual-machines-linux-add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

对于 Azure 磁盘缓存，对于要用来存储 HANA 事务日志的磁盘，请输入“无”。 对于 HANA 数据文件，可以使用读取缓存。 因为 HANA 是内存中数据库，所以，处于 Azure 磁盘级别的读取缓存可以在多大程度上提高性能（例如启动 HANA 并将磁盘中的数据读入内存）将取决于总体使用模式。

有关详细信息，请参阅[高级存储：适用于 Azure 虚拟机工作负荷的高性能存储](../storage/storage-premium-storage.md)。

要查找用于创建 VM 的示例 JSON 模板，请转到 [Azure 快速入门模板](https://github.com/Azure/azure-quickstart-templates)。
“101-vm-simple-linux”模板显示了一个基本模板，它包括一个具有额外的 100-GB 数据磁盘的存储节。

要获取有关如何使用 PowerShell 或 CLI 查找 SUSE 映像的信息，以及了解通过使用 UUID 附加磁盘的重要性，请参阅[在 Microsoft Azure SUSE Linux VM 上运行 SAP NetWeaver](virtual-machines-linux-sap-on-suse-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

根据系统大小和吞吐量需求，可能需要附加多个磁盘（而不是一个），并且以后可以在 OS 级别创建跨磁盘的带区集。 你将创建一个跨多个 Azure 磁盘的带区集，有两个原因：

* 可以增大吞吐量。
* 你需要大于 1 TB 的单个文件系统，而当前 Azure 磁盘大小限制为 1 TB（截至 2016 年 7 月）。

有关用于配置带区的两个主要工具的详细信息，请参阅以下文章：

* [在 Linux 上配置软件 RAID](virtual-machines-linux-configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [在 Azure 中的 Linux VM 上配置 LVM](virtual-machines-linux-configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

在测试环境中，有两个 Azure 标准存储磁盘已附加到 SAP 应用服务器 VM，如以下屏幕截图中所示。 一个磁盘存储着可供安装的所有 SAP 软件（即 NetWeaver 7.5、SAP GUI、SAP HANA，等等）。 第二个磁盘确保有足够的可用空间可用来满足额外的需求（例如，备份和测试数据），并且确保在属于同一 SAP 布局的所有 VM 之间共享 /sapmnt 目录（即 SAP 配置文件）。

![SAP HANA 应用服务器磁盘窗口，其中显示了两个数据磁盘及其大小](./media/virtual-machines-linux-sap-hana-get-started/image003.jpg)

与应用服务器 VM 方案不同，四个磁盘已连接到 SAP HANA 服务器 VM，如以下屏幕截图中所示。 两个磁盘用来存储 SAP 软件（还可以使用 NFS 共享 SAP 软件磁盘）并确保有足够的可用空间可用（例如用于备份）。 另外的两个磁盘是 Azure 高级存储磁盘，用来存储 SAP HANA 数据和日志文件以及 /usr/sap 目录。

![SAP HANA 应用服务器磁盘窗口，其中显示了四个数据磁盘及其大小](./media/virtual-machines-linux-sap-hana-get-started/image004.jpg)

### <a name="kernel-parameters"></a>内核参数
SAP HANA 要求使用不属于标准 Azure 库映像的特定 Linux 内核设置，并且必须手动指定这些设置。 以下 SAP 说明介绍了适用于 SLES 12/SLES for SAP Applications 12 的建议 OS 设置：[SAP 说明 2205917](https://launchpad.support.sap.com/#/notes/2205917)。

有关与在 SLES 上运行 SAP HANA 相关的其他页面缓存主题，请参阅 [SUSE Linux Enterprise Server for SAP Applications 12 SP1 Guide](https://www.suse.com/documentation/sles_for_sap/singlehtml/sles_for_sap_guide/sles_for_sap_guide.html#sec.s4s.configure.page-cache)（SUSE Linux Enterprise Server for SAP Applications 12 SP1 指南）中的“6.1 Kernel: Page-Cache Limit”（6.1 内核：页面缓存限制）。

另一个 SAP 说明介绍了页面缓存限制：[SAP 说明 1557506](https://launchpad.support.sap.com/#/notes/1557506)。

SLES 12 中的一个新工具取代了旧的 sapconf 实用工具。 此新工具名为 tuned-adm，并且有一个特殊的 SAP HANA 配置文件可供它使用。 有关 tuned-adm 的详细信息，请参阅以下文章：

* [有关 tuned-adm 配置文件 sap-hana 的 SLES 文档。](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html)
* [有关 tuned-adm 配置文件 sap-hana 的 SLES 文档：“6.2 Tuning Systems for SAP Workloads with tuned-adm”（6.2 使用 tuned-adm 针对 SAP 工作负荷优化系统）一章。](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf)

在下面的屏幕截图中，可以看到 tuned-adm 如何根据所需的 SAP HANA 设置更改了 transparent_hugepage 和 numa_balancing 值。

![tuned-adm 工具根据所需的 SAP HANA 设置更改值](./media/virtual-machines-linux-sap-hana-get-started/image005.jpg)

若要永久保存 SAP HANA 内核设置，在 SLES 12 上请使用 grub2。 有关 grub2 的详细信息，请转到 [SUSE 文档的“Configuration File Structure”部分](https://www.suse.com/documentation/sled-12/book_sle_admin/data/sec_grub2_file_structure.html)。

以下屏幕截图显示了内核设置在配置文件中进行了怎样的更改，然后怎样使用 grub2-mkconfig 进行了编译。

![配置文件中已更改并使用 grub2-mkconfig 进行了编译的内核设置](./media/virtual-machines-linux-sap-hana-get-started/image006.jpg)

另一种选择是通过 Yast 和“启动加载程序” > “内核参数”设置来更改这些设置。

![YaST 启动加载程序中的“内核参数”设置选项卡](./media/virtual-machines-linux-sap-hana-get-started/image007.jpg)

### <a name="file-systems"></a>文件系统
以下屏幕截图显示了基于两个附加的 Azure 标准存储磁盘在 SAP 应用服务器 VM 上创建的两个文件系统。 两个文件系统都是 XFS 类型的并装载到 /sapdata 和 /sapsoftware。

并非必须按照此方式安排文件系统的结构。 还可以通过其他方式来安排磁盘空间的结构。 最重要的注意事项是要防止根文件系统的可用空间耗尽。

![在 SAP 应用服务器 VM 上创建的两个文件系统](./media/virtual-machines-linux-sap-hana-get-started/image008.jpg)

对于 SAP HANA DB VM，必须知道，在安装数据库期间，当使用 SAPinst (SWPM) 和简单的“典型”安装选项时，默认情况下所有项都将安装到 /hana 和 /usr/sap 中。 SAP HANA 日志备份的默认设置位于 /usr/sap 中。 同样，防止根文件系统耗尽存储空间也非常重要，在使用 SWPM 安装 SAP HANA 之前，请确保 /hana 和 /usr/sap 中有足够的可用空间。

有关 SAP HANA 的标准文件系统布局的说明，请参阅 [SAP HANA Server Installation and Update Guide](http://help.sap.com/saphelp_hanaplatform/helpdata/en/4c/24d332a37b4a3caad3e634f9900a45/frameset.htm)（SAP HANA 服务器安装和更新指南）。
![在 SAP 应用服务器 VM 上创建的额外文件系统](./media/virtual-machines-linux-sap-hana-get-started/image009.jpg)

在标准 SLES 12 Azure 库映像中安装 SAP NetWeaver 时，将显示一条消息，指出没有交换空间。 若要消除此消息，可以通过使用 dd、mkswap 和 swapon 手动添加交换文件。 若要了解如何操作，请在 [SUSE 文档的“Using the YaST Partitioner”（使用 YaST 分区程序）部分](https://www.suse.com/documentation/sled-12/book_sle_deployment/data/sec_yast2_i_y2_part_expert.html)中搜索“Adding a Swap File Manually”（手动添加交换文件）。

另一种选择是使用 Linux VM 代理配置交换空间。 有关详细信息，请参阅 [Azure Linux Agent User Guide](virtual-machines-linux-agent-user-guide.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（Azure Linux 代理用户指南）。

![通知交换空间不足的弹出消息](./media/virtual-machines-linux-sap-hana-get-started/image010.jpg)


### <a name="etchosts"></a>/etc/hosts
在开始安装 SAP 之前，另一个要注意的重要事项是在 /etc/hosts 文件中包含 SAP VM 的主机名和 IP 地址。 将所有 SAP VM 部署在一个 Azure 虚拟网络中，然后使用内部 IP 地址。

![/etc/hosts 文件中列出的 SAP VM 的主机名和 IP 地址](./media/virtual-machines-linux-sap-hana-get-started/image011.jpg)

### <a name="etcfstab"></a>/etc/fstab

在测试阶段，向 fstab 添加 nofail 参数是个不错的主意。 如果磁盘出错，VM 将仍可启动，而不会在启动过程中挂起。 但请密切注意，因为，在此方案中，可能没有额外的磁盘空间可用，并且进程可能会填满根文件系统。 如果缺少 /hana，则 SAP HANA 不会启动。

![向 fstab 添加 nofail 参数](./media/virtual-machines-linux-sap-hana-get-started/image000c.jpg)

## <a name="install-graphical-gnome-desktop-on-sles-12"></a>在 SLES 12 上安装图形 Gnome 桌面
本部分涵盖了以下主题：

* 在 SLES 12 上安装 Gnome 桌面和 xrdp
* 在 SLES 12 上使用 Firefox 运行基于 Java 的 SAP MC

还可以使用诸如 Xterminal 或 VNC 之类的替代方法，但是截至 2016 年 9 月，本指南仅介绍了 xrdp。

### <a name="installing-gnome-desktop-and-xrdp-on-sles-12"></a>在 SLES 12 上安装 Gnome 桌面和 xrdp
如果你有 Microsoft Windows 背景，则可以直接在 SAP Linux VM 中轻松使用图形桌面来运行 Firefox、Sapinst、SAP GUI、SAP MC 或 HANA Studio，以及从 Windows 计算机通过 RDP 连接到 VM。 虽然此过程可能不适用于生产数据库服务器，但适用于单纯的原型/演示环境。 下面是在 Azure SLES 12 VM 上安装 Gnome 桌面的步骤：

输入以下命令来安装 Gnome 桌面（例如在 putty 窗口中）：

`zypper in -t pattern gnome-basic`

然后安装 xrdp，以便通过 RDP 连接到 VM：

`zypper in xrdp`

编辑 /etc/sysconfig/windowmanager 并将默认 Windows 管理器设置为 Gnome：

`DEFAULT_WM="gnome"`

运行 chkconfig，确保 xrdp 在重新启动后自动启动：

`chkconfig -level 3 xrdp on`

如果 RDP 连接发生问题，请尝试重新启动（也许可以从 putty 窗口）：

`/etc/xrdp/xrdp.sh restart`

如果上述 xrdp 重新启动不起作用，请检查是否存在 .pid 文件，如果存在，请将其删除：

`check /var/run` 并查找 `xrdp.pid`

将其删除，然后再次尝试重新启动。

### <a name="sap-mc"></a>SAP MC
安装 Gnome 桌面后，从在 Azure SLES 12 VM 中运行的 Firefox 启动图形化的基于 Java 的 SAP MC 可能会因为缺少 Java 浏览器插件而显示错误。

用于启动 SAP MC 的 URL 是 <server>:5<instance_number>13。

有关详细信息，请参阅 [Starting the Web-Based SAP Management Console](https://help.sap.com/saphelp_nwce10/helpdata/en/48/6b7c6178dc4f93e10000000a42189d/frameset.htm)（启动基于 Web 的 SAP 管理控制台）。

下面的屏幕截图显示了当缺少 Java 浏览器插件时显示的错误消息。

![指明缺少 Java 浏览器插件的错误消息](./media/virtual-machines-linux-sap-hana-get-started/image013.jpg)

若要解决此问题，其中一种方法是使用 YaST 安装缺少的插件，如下面的屏幕截图所示。

![使用 YaST 安装缺少的插件](./media/virtual-machines-linux-sap-hana-get-started/image014.jpg)

再次输入 SAP 管理控制台 URL 会显示一个对话框，要求你激活该插件。

![要求激活插件的对话框](./media/virtual-machines-linux-sap-hana-get-started/image015.jpg)

另一个可能发生的问题是出现关于缺少 javafx.properties 文件的错误消息。 该错误可能与 Java 1.8 的安装有关，该软件是 SAP GUI 7.4 必需的。 在 YaST 中看到的 IBM Java 版本未包括此文件。 解决方法是从 Oracle 下载 Java。 有关此问题的详细信息，请参阅 [SAPGui 7.4 Java for openSUSE 42.1 Leap](https://scn.sap.com/thread/3908306)（适用于 openSUSE 42.1 Leap 的 SAPGui 7.4 Java）。

## <a name="install-sap-hana-manually-by-using-swpm-as-part-of-a-netweaver-75-installation"></a>在 NetWeaver 7.5 安装过程中使用 SWPM 手动安装 SAP HANA
本部分中的屏幕截图系列显示了当使用 SWPM (SAPinst) 时用于安装 SAP NetWeaver 7.5 和 SAP HANA SP12 的主要步骤。 在 NetWeaver 7.5 安装过程中，SWPM 还可以将 HANA 数据库安装为单一实例。

在示例测试环境中，我们仅安装了一个高级业务应用程序编程 (ABAP) 应用服务器。 如下面的屏幕截图中所示，我们使用了“分布式系统”选项来将 ASCS 和主应用程序服务器实例安装在一个 Azure VM 中，将 SAP HANA 作为数据库系统安装在另一个 Azure VM 中。

![使用“分布式系统”选项安装的 ASCS 和主应用程序服务器实例](./media/virtual-machines-linux-sap-hana-get-started/image012.jpg)

在将 ASCS 实例安装到应用服务器 VM 并且在 SAP 管理控制台中将其设置为“绿色”之后，必须与 SAP HANA DB 服务器 VM 共享 /sapmnt 目录（包括 SAP 配置文件目录）。 DB 安装步骤需要访问此信息。 最好的方法是使用可通过 YaST 进行配置的 NFS。

![SAP 管理控制台，其中显示了在应用服务器 VM 上安装的并设置为“绿色”的 ASCS 实例](./media/virtual-machines-linux-sap-hana-get-started/image016.jpg)

在应用服务器 VM 上，应通过 NFS 并使用 **rw** 和 **no_root_squash** 选项来共享 /sapmnt 目录。 默认选项为“ro”和“root_squash”，这在安装数据库实例时可能会导致出现问题。

![通过 NFS 并使用“rw”和“no_root_squash”选项共享 /sapmnt 目录](./media/virtual-machines-linux-sap-hana-get-started/image017b.jpg)

如下一屏幕截图所示，必须使用 **NFS 客户端**（借助 YaST）在 SAP HANA DB 服务器 VM 上配置应用服务器 VM 中的 /sapmnt 共享。

![使用“NFS 客户端”配置的 /sapmnt 共享](./media/virtual-machines-linux-sap-hana-get-started/image018b.jpg)

若要执行分布式 NetWeaver 7.5 安装**数据库实例**，如以下屏幕截图中所示，请登录到 SAP HANA DB 服务器 VM 并启动 SWPM。

![通过登录到 SAP HANA DB 服务器 VM 并启动 SWPM 来安装数据库实例](./media/virtual-machines-linux-sap-hana-get-started/image019.jpg)

在选择“典型”安装和安装媒体的路径后，输入 DB SID、主机名、实例编号和 DB 系统管理员密码。

![SAP HANA 数据库系统管理员登录页面](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

输入 DBACOCKPIT 架构的密码。

![DBACOCKPIT 架构的密码输入框](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

为 SAPABAP1 架构密码输入一个问题。

![为 SAPABAP1 架构密码输入一个问题](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

在任务完成后，DB 安装过程的每个阶段旁边都将显示一个绿色的对号。 一个消息框窗口将显示一条消息，消息内容为“...数据库实例的执行已完成。”

![包含确认消息的任务已完成窗口](./media/virtual-machines-linux-sap-hana-get-started/image023.jpg)

安装成功后，SAP 管理控制台应该也会将 DB 实例显示为“绿色”并显示完整的 SAP HANA 进程列表（hdbindexserver、hdbcompileserver，等等）。

![其中包含 SAP HANA 进程列表的 SAP 管理控制台窗口](./media/virtual-machines-linux-sap-hana-get-started/image024.jpg)

以下屏幕截图显示了 SWPM 在安装 HANA 期间在 /hana/shared 下创建的部分文件结构。 因为没有用来指定其他路径的选项，因此 ，在使用 SWPM 安装 SAP HANA 之前，请在 /hana 下装载额外的磁盘空间以防止根文件系统耗尽可用空间。

![在安装 HANA 期间创建的 /hana/shared 文件结构](./media/virtual-machines-linux-sap-hana-get-started/image025.jpg)

此屏幕截图显示了 /usr/sap 目录的文件结构。

![/usr/sap 目录文件结构](./media/virtual-machines-linux-sap-hana-get-started/image026.jpg)

分布式 ABAP 安装的最后一个步骤是“主应用程序服务器实例”。

![其中将“主应用程序服务器实例”显示为最后一个步骤的 ABAP 安装](./media/virtual-machines-linux-sap-hana-get-started/image027b.jpg)

安装主应用程序服务器实例和 SAP GUI 后，使用事务“dbacockpit”确认 SAP HANA 安装是否已正确完成。

![确认安装成功的 DBA Cockpit 窗口](./media/virtual-machines-linux-sap-hana-get-started/image028b.jpg)

在最后一个步骤中，你可能希望首先在 SAP 应用服务器 VM 中安装 SAP HANA Studio，然后连接到在 DB 服务器 VM 上运行的 SAP HANA 实例。

![在 SAP 应用服务器 VM 中安装 SAP HANA Studio](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

## <a name="install-sap-hana-manually-by-using-the-hana-lifecycle-management-tool-hdblcm"></a>使用 HANA 生命周期管理工具 (hdblcm) 手动安装 SAP HANA
除了使用 SWPM 将 SAP HANA 安装为分布式安装的一部分之外，也可以先使用 hdblcm 独立安装 HANA。 之后，可以安装诸如 SAP NetWeaver 7.5 之类的软件。 下面的屏幕截图显示了此过程的工作原理。

下面是有关 HANA hdblcm 工具的三个信息来源：

* [Choosing the Correct SAP HANA HDBLCM for Your Task](https://help.sap.com/saphelp_hanaplatform/helpdata/en/68/5cff570bb745d48c0ab6d50123ca60/content.htm)（为任务选择正确的 SAP HANA HDBLCM）
* [SAP HANA Lifecycle Management Tools](http://saphanatutorial.com/sap-hana-lifecycle-management-tools/)（SAP HANA 生命周期管理工具）
* [SAP HANA Server Installation and Update Guide](http://help.sap.com/hana/SAP_HANA_Server_Installation_Guide_en.pdf)（SAP HANA 服务器安装和更新指南）

为了避免 \<HANA SID\>adm 用户（由 hdblcm 工具创建）的默认组 ID 设置发生问题，应该在使用 hdblcm 安装 SAP HANA 之前，定义一个名为“sapsys”、组 ID 为 1001 的新组。

![使用组 ID 1001 定义的新组“sapsys”](./media/virtual-machines-linux-sap-hana-get-started/image030.jpg)

首次启动 hdblcm 时，会显示一个简单的启动菜单。 选择第 1 项“安装新系统”，如以下屏幕截图中所示。

![hdblcm 启动窗口中的“安装新系统”选项](./media/virtual-machines-linux-sap-hana-get-started/image031.jpg)

以下屏幕截图显示了你之前选择的所有主要选项。

> [!IMPORTANT]
> 为 HANA 日志和数据卷指定的目录以及安装路径（在本例中为 /hana/shared）和 /usr/sap 不应当是根文件系统的一部分。 这些目录属于附加到 VM 的 Azure 数据磁盘，如 Azure VM 设置部分所述。 此方法将有助于避免根文件系统耗尽空间的风险。 此外还可以看到，HANA 管理用户的用户 ID 为 1005，并且属于在安装前定义的 sapsys 组（ID 为 1001）。

![之前选择的所有主要 SAP HANA 组件的列表](./media/virtual-machines-linux-sap-hana-get-started/image032.jpg)

可以在 /etc/passwd 中检查 HANA \<HANA SID\>adm（在以下屏幕截图中为 azdadm）用户的详细信息。

![/etc/passwd 中列出的 HANA \<HANA SID\>adm 用户详细信息](./media/virtual-machines-linux-sap-hana-get-started/image033.jpg)

使用 hdblcm 安装 SAP HANA 后，可以在 SAP HANA Studio 中看到文件结构。 目前尚未提供包括所有 SAP NetWeaver 表的 SAPABAP1 架构。

![SAP HANA Studio 中的 SAP HANA 文件结构](./media/virtual-machines-linux-sap-hana-get-started/image034.jpg)

安装 SAP HANA 之后，可以在其上安装 SAP NetWeaver。 如此屏幕截图中所示，安装是通过 SWPM 作为“分布式安装”执行的（如上一部分所述）。 使用 SWPM 安装数据库实例时，你将输入与使用 hdblcm 时相同的数据（例如主机名、HANA SID 和实例编号）。 然后，SWPM 将使用现有 HANA 安装并添加更多架构。

![使用 SWPM 执行的分布式安装](./media/virtual-machines-linux-sap-hana-get-started/image035b.jpg)

以下屏幕截图显示了用于输入 DBACOCKPIT 架构相关数据的 SWPM 安装步骤。

![用于输入 DBACOCKPIT 架构数据的 SWPM 安装步骤](./media/virtual-machines-linux-sap-hana-get-started/image036b.jpg)

输入 SAPABAP1 架构的相关数据。

![输入 SAPABAP1 架构的相关数据](./media/virtual-machines-linux-sap-hana-get-started/image037b.jpg)

完成 SWPM 数据库实例安装后，可以在 SAP HANA Studio 中看到 SAPABAP1 架构。

![SAP HANA Studio 中的 SAPABAP1 架构](./media/virtual-machines-linux-sap-hana-get-started/image038b.jpg)

最后，在 SAP 应用服务器和 SAP GUI 安装完成后，可以使用“dbacockpit”事务来验证 HANA DB 实例。

![使用“dbacockpit”事务验证的 HANA DB 实例](./media/virtual-machines-linux-sap-hana-get-started/image039b.jpg)

## <a name="about-sap-azure-certifications-and-running-sap-hana-on-azure"></a>关于 SAP Azure 认证和在 Azure 上运行 SAP HANA
有关详细信息，请参阅以下文档：
* 有关在经典模式下使用 Windows OS 在 Azure 上运行 SAP 的一般 SAP Azure 信息：[Using SAP on Windows virtual machines in Azure](virtual-machines-windows-classic-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)（在 Azure 中的 Windows 虚拟机上使用 SAP）。
* 有关供客户使用的现有 SAP 模板的信息：[Azure Quickstart Templates for SAP](https://blogs.msdn.microsoft.com/saponsqlserver/2016/05/16/azure-quickstart-templates-for-sap/)（适用于 SAP 的 Azure 快速启动模板）。
* 有关在 Azure Resource Manager 模型中使用 Linux OS 在 Azure 上运行 SAP 的一般 SAP Azure 信息：[Using SAP on Linux virtual machines (VMs)](virtual-machines-linux-sap-get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（在 Linux 虚拟机 (VM) 上使用 SAP）
* 已认证的 SAP HANA 硬件目录，其中列出了生产环境支持的 Azure VM 类型：[Certified SAP HANA® Hardware Directory](https://global.sap.com/community/ebook/2014-09-02-hana-hardware/enEN/iaas.html)（已认证的 SAP HANA® 硬件目录）。
* 有关虚拟机大小（尤其是对于 Linux 工作负荷）的信息：[Sizes for virtual machines in Azure](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（Azure 中虚拟机的大小）。
* 以下 SAP 说明，其中列出了 Azure 上所有受支持的 SAP 产品以及 SAP 支持的 Azure VM 类型：[SAP 说明 1928533](https://launchpad.support.sap.com/#/notes/1928533/E)。
* 有关对 Azure 上的 Linux VM 进行 SAP“增强型监视”的 SAP 说明：[SAP 说明 2191498](https://launchpad.support.sap.com/#/notes/2191498/E)。
* 适用于 Azure“大型实例”的 SAP HANA 产品。 务必要了解，此信息与在 Azure VM 上运行 SAP HANA 无关。 它与混合环境有关，在混合环境中，SAP 应用服务器在 Azure VM 中运行，但 SAP HANA 在裸机服务器上运行：[SAP 说明 2316233](https://launchpad.support.sap.com/#/notes/2316233/E)。
* 其中包含 Linux 上的 SAPOSCOL 的相关信息的 SAP 说明：[SAP 说明 1102124](https://launchpad.support.sap.com/#/notes/1102124/E)。
* Microsoft Azure 上的 SAP 的关键监视度量值：[SAP 说明 2178632](https://launchpad.support.sap.com/#/notes/2178632/E)。
* 有关 Azure Resource Manager 的信息：[Azure Resource Manager overview](../azure-resource-manager/resource-group-overview.md)（Azure Resource Manager 概述）。
* 有关使用模板部署 Linux VM 的信息：[Deploy and manage virtual machines by using Azure Resource Manager templates and the Azure CLI](virtual-machines-linux-cli-deploy-templates.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)（使用 Azure Resource Manager 模板与 Azure CLI 来部署和管理虚拟机）。
* Azure Resource Manager 部署模型与经典部署模型之间的比较：[Azure Resource Manager vs. classic deployment: Understand deployment models and the state of your resources](../azure-resource-manager/resource-manager-deployment-model.md)（Azure Resource Manager 与经典部署：了解资源的部署模型和状态）。

## <a name="sap-software-downloads"></a>SAP 软件下载
可以从 SAP 服务应用商店下载软件，如以下屏幕截图中所示。

* 下载适用于 Linux/HANA 的 NetWeaver 7.5：

 ![用于下载 NetWeaver 7.5 的 SAP 服务安装和升级窗口](./media/virtual-machines-linux-sap-hana-get-started/image001.jpg)
* 下载 HANA SP12 Platform Edition：

 ![用于下载 HANA SP12 Platform Edition 的 SAP 服务安装和升级窗口](./media/virtual-machines-linux-sap-hana-get-started/image002.jpg)



<!--HONumber=Dec16_HO3-->


