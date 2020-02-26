---
title: 适用于 Windows 上的 SAP NW 的 azure Vm HA 与 Azure NetApp 文件（SMB） |Microsoft Docs
description: 适用于 SAP 应用程序的 azure 虚拟机上的 SAP NetWeaver 的高可用性，适用于 SAP 应用程序的 Azure NetApp 文件（SMB）
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
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/25/2020
ms.locfileid: "77591347"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-windows-with-azure-netapp-filessmb-for-sap-applications"></a>适用于 SAP 应用程序的 azure 虚拟机上的 SAP NetWeaver 的高可用性，适用于 SAP 应用程序的 Azure NetApp 文件（SMB）

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

本文介绍如何在[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)上部署、配置虚拟机、安装群集框架，以及在 Windows vm 上安装高度[可用的 SAP](https://docs.microsoft.com/windows/win32/fileio/microsoft-smb-protocol-and-cifs-protocol-overview) NetWeaver 7.50 系统。  

本文不详细介绍数据库层。 假设已创建 Azure[虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。  

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明[1928533][1928533]，其中包含：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上的 Windows 所需的 SAP 内核版本
* SAP 说明 [2015553][2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2178632][2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [1999351][1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 说明[2287140](https://launchpad.support.sap.com/#/notes/2287140)列出了 SMB 1.x 协议的 sap 支持的 CA 功能的先决条件。
* SAP 说明[2802770](https://launchpad.support.sap.com/#/notes/2802770)针对 Windows 2012 和2016上运行时间缓慢的 SAP transaction AL11 提供了疑难解答信息。
* SAP 说明[1911507](https://launchpad.support.sap.com/#/notes/1911507)中提供了有关 Windows Server 上的文件共享的透明故障转移功能的信息，以及 SMB 3.0 协议。
* SAP 说明[662452](https://launchpad.support.sap.com/#/notes/662452)提供建议（停用8.3 名称生成），以解决数据访问过程中的文件系统性能不佳的问题。
* [在 Azure 上的 SAP ASCS/SCS 实例的 Windows 故障转移群集和文件共享上安装 SAP NetWeaver 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-file-share) 
* [适用于 SAP NetWeaver 的 Azure 虚拟机高可用性体系结构和方案](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-architecture-scenarios)
* [在 ASCS 群集配置中添加探测端口](sap-high-availability-installation-wsfc-file-share.md)
* [在故障转移群集上安装（A） SCS 实例](https://www.sap.com/documents/2017/07/f453332f-c97c-0010-82c7-eda71af511fa.html)
* [为 Azure NetApp 文件创建 SMB 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)
* [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

SAP 开发了可以取代群集共享磁盘的新方法和新方案，用于将 Windows 故障转移群集上的 SAP ASCS/SCS 实例群集化。 可以使用 SMB 文件共享来部署 SAP 全局主机文件，而不是使用群集共享磁盘。 Azure NetApp 文件使用 Active Directory 支持 SMBv3 （连同 NFS）和 NTFS ACL。 Azure NetApp 文件自动可用（因为它是一种 PaaS 服务）。 这些功能使得 Azure NetApp 文件非常适合用于托管 SAP 全局文件共享。  
支持[Azure Active Directory （AD）域服务](https://docs.microsoft.com/azure/active-directory-domain-services/overview)和[Active Directory 域服务（AD DS）](https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) 。 可以将现有 Active Directory 域控制器与 Azure NetApp 文件一起使用。 域控制器可以在 Azure 中作为虚拟机，也可以在本地通过 ExpressRoute 或 S2S VPN。 在本文中，我们将在 Azure VM 中使用域控制器。  
SAP Netweaver central services 的高可用性（HA）需要共享存储。 若要在 Windows 上实现此目的，目前需要构建 SOFS 群集，或使用类似于 SIOS 的群集共享磁盘 s/w。 现在，可以使用共享存储实现 SAP Netweaver HA，并将其部署在 Azure NetApp 文件中。 将 Azure NetApp 文件用于共享存储无需使用 SOFS 或 SIOS。  

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

![包含 SMB 共享的 SAP ASCS/SCS HA 体系结构](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb.png)

SMB 文件共享的先决条件包括：
* SMB 3.0（或更高版本）协议。
* 能够为 Active Directory 用户组和计算机 $ 计算机对象设置 Active Directory 访问控制列表（Acl）。
* 文件共享必须启用 HA 功能。

此参考体系结构中的 SAP 中心服务的共享由 Azure NetApp 文件提供：

![包含 SMB 共享的 SAP ASCS/SCS HA 体系结构](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-detail.png)

## <a name="create-and-mount-smb-volume-for-azure-netapp-files"></a>为 Azure NetApp 文件创建和装载 SMB 卷

执行以下步骤，以便为使用 Azure NetApp 文件做好准备。  

1. 按照以下步骤[注册 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)  
2. 按照[创建 NetApp 帐户](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)中所述的步骤创建 Azure NetApp 帐户  
3. 按照[设置容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)中的说明设置容量池
4. Azure NetApp 文件资源必须位于委托子网中。 按照将[子网委托给 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中的说明创建委托子网。  

> [!IMPORTANT]
> 创建 SMB 卷之前，需要创建 Active Directory 连接。 查看[Active Directory 连接的要求](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#requirements-for-active-directory-connections)。  

5. 如[创建 Active Directory 连接](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#create-an-active-directory-connection)中所述，创建 Active Directory 连接  
6. 按照[添加 smb 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes-smb#add-an-smb-volume)中的说明创建 Smb Azure NETAPP 文件 smb 卷  
7. 在 Windows 虚拟机上装载 SMB 卷。

> [!TIP]
> 可以在[Azure 门户](https://portal.azure.com/#home)中导航到 "Azure netapp 文件" 对象，单击 "**卷**" 边栏选项卡，然后单击 "**装入说明**"，了解有关如何装载 azure netapp 文件卷的说明。  

## <a name="prepare-the-infrastructure-for-sap-ha-by-using-a-windows-failover-cluster"></a>使用 Windows 故障转移群集为 SAP HA 准备基础结构 

1. [设置所需的 DNS IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#b22d7b3b-4343-40ff-a319-097e13f62f9e)  
2. [设置 SAP 虚拟机的静态 IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#84c019fe-8c58-4dac-9e54-173efd4b2c30)。
3. [为 Azure 内部负载均衡器设置静态 IP 地址](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#7a8f3e9b-0624-4051-9e41-b73fff816a9e)。
4. [设置 Azure 内部负载均衡器的默认 ASCS/SCS 负载均衡规则](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#f19bd997-154d-4583-a46e-7f5a69d0153c)。
5. [更改 Azure 内部负载均衡器的 ASCS/SCS 默认负载均衡规则](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#fe0bd8b5-2b43-45e3-8295-80bee5415716)。
6. [将 Windows 虚拟机添加到域](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#e69e9a34-4601-47a3-a41c-d2e11c626c0c)。
7. [在 SAP ASCS/SCS 实例的两个群集节点上添加注册表项](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#661035b2-4d0f-4d31-86f8-dc0a50d78158)
8. [为 SAP ASCS/SCS 实例设置 Windows Server 故障转移群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-shared-disk#0d67f090-7928-43e0-8772-5ccbf8f59aab)
9. 如果使用的是 Windows Server 2016，建议配置[Azure 云见证](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)。


## <a name="install-sap-ascs-instance-on-both-nodes"></a>在两个节点上安装 SAP ASCS 实例

需要 SAP 中的以下软件：
   * SAP 软件预配管理器（SWPM）安装工具版本 SPS25 或更高版本。
   * SAP 内核7.49 或更高版本
   * 如为[群集 SAP ASCS/scs 实例创建虚拟主机名](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#a97ad604-9094-44fe-a364-f89cb39bf097)中所述，为群集 sap ASCS/scs 实例创建虚拟主机名（群集网络名称）。

> [!NOTE]
> 将 SAP NetWeaver 7.40（及更高版本）与 SAP 内核 7.49（及更高版本）配合使用时，即可通过文件共享来群集化 SAP ASCS/SCS 实例。  

### <a name="install-an-ascsscs-instance-on-the-first-ascsscs-cluster-node"></a>在第一个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第一个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到： **Product** > **DBMS** > 安装 > 应用程序服务器 ABAP （或 Java） > 高可用性系统 > 第一个群集节点的 ASCS/SCS 实例。  

2. 在 SWPM 中选择 "**文件共享群集**" 作为群集共享配置。  
3. 出现步骤**SAP 系统群集参数**时，请输入已创建为**文件共享主机名**的 Azure NetApp 文件的主机名。  在此示例中，SMB 共享主机名为**anfsmb-9562**。 

> [!IMPORTANT]
> 如果必备组件检查器导致 SWPM 显示不满足连续可用性功能的条件，则可以按照延迟错误消息中的说明来解决此问题，前提是[尝试访问 Windows 中不再存在的共享文件夹](https://support.microsoft.com/help/2820470/delayed-error-message-when-you-try-to-access-a-shared-folder-that-no-l)。  

> [!TIP]
> 如果必备组件检查器导致 SWPM 显示 "未满足交换大小" 条件，则可以通过导航到我的电脑 > 系统属性 > 性能设置 > 高级 > 虚拟内存 > 更改来调整交换大小。  

4. 使用 PowerShell 配置 SAP 群集资源 `SAP-SID-IP` 探测端口。 在其中一个 SAP ASCS/SCS 群集节点上执行此配置，如[配置探测端口](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-installation-wsfc-shared-disk#10822f4f-32e7-4871-b63a-9b86c76ce761)中所述。

### <a name="install-an-ascsscs-instance-on-the-second-ascsscs-cluster-node"></a>在第二个 ASCS/SCS 群集节点上安装 ASCS/SCS 实例

1. 在第二个群集节点上安装 SAP ASCS/SCS 实例。 启动 SAP SWPM 安装工具，然后导航到**Product** > **DBMS** > 安装 > 应用程序服务器 ABAP （或 Java） > 高可用性系统 > ASCS/SCS 实例 > 附加群集节点。  

### <a name="install-a-dbms-instance-and-sap-application-servers"></a>安装 DBMS 实例和 SAP 应用程序服务器

通过安装以下内容完成 SAP 安装：

   * DBMS 实例  
   * 主 SAP 应用程序服务器  
   * 其他 SAP 应用程序服务器  

## <a name="test-the-sap-ascsscs-instance-failover"></a>测试 SAP ASCS/SCS 实例故障转移 

### <a name="fail-over-from-cluster-node-a-to-cluster-node-b-and-back"></a>从群集节点 A 故障转移到群集节点 B 和备份
在此测试方案中，我们将群集节点 sapascs1 称为节点 A，并将群集节点 sapascs2 称为节点 B。

1. 验证群集资源是否正在节点 A 上运行。 ![图1：在故障转移测试之前，在节点 A 上运行的 Windows Server 故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-1.png)  

2. 重新启动群集节点 A。SAP 群集资源将移到群集节点 B。 ![图2：在故障转移测试后，在节点 B 上运行的 Windows Server 故障转移群集资源](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-2.png)  


## <a name="lock-entry-test"></a>锁定条目测试

1. 验证 SAP 排队复制服务器（ERS）是否处于活动状态  
2. 登录到 SAP 系统，执行 transaction SU01，并在更改模式下打开用户 ID。 这将生成 SAP lock 条目。  
3. 在 SAP 系统中登录时，通过导航到 transaction ST12 来显示锁定条目。  
4. 将 ASCS 资源从群集节点 A 故障转移到群集节点 B。  
5. 验证是否保留了在 SAP ASCS/SCS 群集资源故障转移之前生成的锁定条目。  

![图3：在故障转移测试后保留锁定条目](./media/virtual-machines-shared-sap-high-availability-guide/high-availability-windows-azure-netapp-files-smb-figure-3.png)  

有关详细信息，请参阅[ASCS WITH ERS 中的排队故障转移的疑难解答](https://wiki.scn.sap.com/wiki/display/SI/Troubleshooting+for+Enqueue+Failover+in+ASCS+with+ERS)
## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 了解如何建立高可用性并规划 SAP 灾难恢复 
* Azure 上的 HANA （大型实例），请参阅[azure 上的 SAP HANA （大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅[Azure 虚拟机（vm）上的 SAP HANA 的高可用性][sap-hana-ha]
