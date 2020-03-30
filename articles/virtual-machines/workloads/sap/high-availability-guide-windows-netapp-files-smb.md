---
title: Azure VM HA 用于具有 Azure NetApp 文件 （SMB） 的 Windows 上的 SAP NW。微软文档
description: 适用于 SAP 应用程序的 Windows 上 Azure VM 上 SAP NetWeaver 的高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 5e514964-c907-4324-b659-16dd825f6f87
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/29/2019
ms.author: radeltch
ms.openlocfilehash: b41db629c5308348f632b3dc51c75822ba361c60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "77591347"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>适用于 SAP 应用程序的 Windows 上 Azure VM 上 SAP NetWeaver 的高可用性

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]:https://launchpad.support.sap.com/#/notes/2205917
[1944799]:https://launchpad.support.sap.com/#/notes/1944799
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json
[template-converged]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-converged-md%2Fazuredeploy.json
[template-file-server]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-file-server-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md

本文介绍如何使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)上的[SMB](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview)在 Windows VM 上部署、配置虚拟机、安装群集框架和在 Windows VM 上安装高可用性的 SAP NetWeaver 7.50 系统。  

本文未详细介绍数据库层。 我们假定 Azure[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)已创建。  

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 注释[1928533][1928533]， 包含：  
  * 支持部署 SAP 软件的 Azure VM 大小列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * 微软 Azure 上 Windows 所需的 SAP 内核版本
* SAP 说明 [2015553][2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2178632][2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [1999351][1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 注释[2287140](https://launchpad.support.sap.com/#/notes/2287140)列出了 SMB 3.x 协议支持 SAP 的 CA 功能的先决条件。
* SAP Note [2802770](https://launchpad.support.sap.com/#/notes/2802770)在 Windows 2012 和 2016 上为运行缓慢的 SAP 事务 AL11 提供了故障排除信息。
* SAP Note [1911507](https://launchpad.support.sap.com/#/notes/1911507)具有有关 Windows 服务器上具有 SMB 3.0 协议的文件共享的透明故障转移功能的信息。
* SAP Note [662452](https://launchpad.support.sap.com/#/notes/662452)建议（停用 8.3 名称生成）以解决数据访问期间文件系统性能不佳/错误的问题。
* [在 Windows 故障转移群集上安装 SAP NetWeaver 高可用性，在 Azure 上安装适用于 SAP ASCS/SCS 实例的文件共享](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [在 ASCS 群集配置中添加探测端口](sap-high-availability-installation-wsfc-file-share.md)
* [在故障转移群集上安装 （A） SCS 实例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [创建用于 Azure NetApp 文件的 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [使用 Azure NetApp 文件在 Microsoft Azure 上使用 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

SAP 开发了可以取代群集共享磁盘的新方法和新方案，用于将 Windows 故障转移群集上的 SAP ASCS/SCS 实例群集化。 可以使用 SMB 文件共享来部署 SAP 全局主机文件，而不是使用群集共享磁盘。 Azure NetApp 文件支持 SMBv3（以及 NFS）和使用活动目录的 NTFS ACL。 Azure NetApp 文件自动高度可用（因为它是 PaaS 服务）。 这些功能使 Azure NetApp 文件非常适合托管 SAP 全局的 SMB 文件共享。  
支持[Azure 活动目录 （AD） 域服务和](https://docs.microsoft.com/azure/active-directory-domain-services/overview)[活动目录域服务 （AD DS）。](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 您可以将现有的活动目录域控制器与 Azure NetApp 文件一起使用。 域控制器可以作为虚拟机在 Azure 中，也可以通过 ExpressRoute 或 S2S VPN在内部。 在本文中，我们将在 Azure VM 中使用域控制器。  
SAP Netweaver 中心服务的高可用性 （HA） 需要共享存储。 为了在 Windows 上实现这一点，到目前为止，必须构建 SOFS 群集或使用群集共享磁盘，如 SIOS。 现在，可以使用部署在 Azure NetApp 文件上的共享存储来实现 SAP Netweaver HA。 将 Azure NetApp 文件用于共享存储无需 SOFS 或 SIOS。  

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

![SAP ASCS/SCS HA 架构，具有 SMB 共享](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 文件共享的先决条件是：
* SMB 3.0（或更高版本）协议。
* 能够为活动目录用户组和计算机计算机对象设置活动目录访问控制列表 （ACL）。
* 文件共享必须启用 HA。

此参考体系结构中的 SAP 中央服务的共享由 Azure NetApp 文件提供：

![SAP ASCS/SCS HA 架构，具有 SMB 共享](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建和装载 SMB 卷

执行以下步骤，作为使用 Azure NetApp 文件的准备。  

1. 按照步骤注册[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. 按照[创建 NetApp 帐户](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)中描述的步骤创建 Azure NetApp 帐户  
3. 按照[设置容量池中的说明设置容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)
4. Azure NetApp 文件资源必须驻留在委派的子网中。 按照[将子网委派到 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的说明来创建委派的子网。  

> [!IMPORTANT]
> 在创建 SMB 卷之前，您需要创建活动目录连接。 查看[活动目录连接的要求](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)。  

5. 创建活动目录连接，如[创建活动目录连接中](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)所述  
6. 按照[添加 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)中的说明创建 SMB Azure NetApp 文件 SMB 卷  
7. 将 SMB 卷安装到 Windows 虚拟机上。

> [!TIP]
> 如果在[Azure 门户](https://portal.azure.com/#home)中导航到 Azure NetApp 文件对象，请单击 **"卷"** 边栏选项卡，然后**装载说明**，则可以找到有关如何装载 Azure NetApp 文件卷的说明。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>使用 Windows 故障转移群集为 SAP HA 准备基础结构 

1. [设置所需的 DNS IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [为 SAP 虚拟机设置静态 IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)。
3. [为 Azure 内部负载均衡器设置静态 IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)。
4. [为 Azure 内部负载均衡器设置默认 ASCS/SCS 负载均衡规则](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)。
5. [更改 Azure 内部负载均衡器的 ASCS/SCS 默认负载平衡规则](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
6. [将 Windows 虚拟机添加到域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
7. [在 SAP ASCS/SCS 实例的两个群集节点上添加注册表项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [为 SAP ASCS/SCS 实例设置 Windows 服务器故障转移群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. 如果使用 Windows 服务器 2016，我们建议您配置 Azure[云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>在两个节点上安装 SAP ASCS 实例

您需要 SAP 提供以下软件：
   * SAP 软件配置管理器 （SWPM） 安装工具版本 SPS25 或更高版本。
   * SAP 内核 7.49 或更高版本
   * 为群集 SAP ASCS/SCS 实例创建虚拟主机名（群集网络名称），如[为群集 SAP ASCS/SCS 实例创建虚拟主机名](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)中所述。

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>在第一个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第一个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到：**产品** > **DBMS** >安装>应用程序服务器 ABAP（或 Java）>高可用性系统> ASCS/SCS 实例>第一个群集节点。  

2. 选择**文件共享群集**作为 SWPM 中的群集共享配置。  
3. 当在步骤**SAP 系统群集参数**中提示时，输入已创建为**文件共享主机名**的 Azure NetApp 文件 SMB 共享的主机名。  在此示例中，SMB 共享主机名为**afsmb-9562**。 

> [!IMPORTANT]
> 如果 SWPM 中的先决条件检查器结果显示未满足连续可用性功能条件，[则可以在尝试访问 Windows 中不再存在的共享文件夹时，按照延迟错误消息](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)中的说明进行寻址。  

> [!TIP]
> 如果 SWPM 中的"先决条件检查器结果"显示未满足交换大小条件，则可以通过导航到"我的计算机>系统属性>性能设置>高级>虚拟内存>更改来调整 SWAP 大小。  

4. 使用 PowerShell 配置`SAP-SID-IP`SAP 群集资源（探测端口）。 在 SAP ASCS/SCS 群集节点之一上执行此配置，如[配置探测端口](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)中所述。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>在第二个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第二个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到**产品** > **DBMS** >安装>应用程序服务器 ABAP（或 Java） >高可用性系统> ASCS/SCS 实例>其他群集节点。  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>安装 DBMS 实例和 SAP 应用程序服务器

通过安装：

   * DBMS 实例  
   * 主 SAP 应用程序服务器  
   * 附加的 SAP 应用程序服务器  

## <a name="test-the-sap-ascsscs-instance-failover"></a>测试 SAP ASCS/SCS 实例故障转移 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>故障从群集节点 A 故障转移到群集节点 B 和返回
在此测试方案中，我们将群集节点 sapcs1 称为节点 A，将群集节点 sapcs2 称为节点 B。

1. 验证群集资源是否在节点 A 上运行。![图 1：在故障转移测试之前在节点 A 上运行的 Windows Server 故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 重新启动群集节点 A。SAP 群集资源将移动到群集节点 B。![图 2：故障转移测试后在节点 B 上运行的 Windows 服务器故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>锁定输入测试

1.验证 SAP 队列复制服务器 （ERS） 是否处于活动状态  
2. 登录到 SAP 系统，执行事务 SU01 并在更改模式下打开用户 ID。 这将生成 SAP 锁条目。  
3. 当您登录 SAP 系统时，通过导航到事务 ST12 来显示锁条目。  
4. 将 ASCS 资源从群集节点 A 故障转移到群集节点 B。  
5. 验证是否保留 SAP ASCS/SCS 群集资源故障转移之前生成的锁条目。  

![图 3：故障转移测试后保留锁条目](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

有关详细信息，请参阅使用[ERS 在 ASCS 中解决队列故障转移的疑难解答](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 了解如何建立高可用性并规划 SAP 的灾难恢复 
* 在 Azure 上（大型实例）上的 HANA，请参阅[Azure 上的 SAP HANA（大型实例）高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何在 Azure VM 上建立 SAP HANA 高可用性以及规划灾难恢复，请参阅 [Azure 虚拟机 (VM) 上的 SAP HANA 高可用性][sap-hana-ha]
