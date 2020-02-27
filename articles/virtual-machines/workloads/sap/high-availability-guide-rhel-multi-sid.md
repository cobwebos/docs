---
title: RHEL 上的 SAP NW 的 Azure Vm 高可用性多 SID 指南 |Microsoft Docs
description: Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/26/2020
ms.author: radeltch
ms.openlocfilehash: 1c52e7e30ac02b14356284f0506824b5a7d0188a
ms.sourcegitcommit: 96dc60c7eb4f210cacc78de88c9527f302f141a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2020
ms.locfileid: "77652043"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-red-hat-enterprise-linux-for-sap-applications-multi-sid-guide"></a>适用于 SAP 应用程序的 Azure Red Hat Enterprise Linux Vm 上的 SAP NetWeaver 高可用性多 SID 指南

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=storage&regions=all
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2002167]: https://launchpad.support.sap.com/#/notes/2002167
[2009879]: https://launchpad.support.sap.com/#/notes/2009879
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[template-multisid-xscs]:https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fsap-3-tier-marketplace-image-multi-sid-xscs-md%2Fazuredeploy.json

[sap-hana-ha]:sap-hana-high-availability-rhel.md
[glusterfs-ha]:high-availability-guide-rhel-glusterfs.md

本文介绍如何使用适用于 SAP 应用程序的 Red Hat Enterprise Linux 在 Azure Vm 的两个节点群集中部署多个 SAP NetWeaver 高度可用系统（即多 SID）。  

在示例配置中，安装命令等是三个 SAP NetWeaver 7.50 系统部署在单个双节点高可用性群集中。 SAP 系统 Sid 包括：
* **NW1**： ASCS 实例编号**00**和虚拟主机名**msnw1ascs**;ERS 实例编号**02** ，虚拟主机名为**msnw1ers**。  
* **NW2**： ASCS 实例编号**10**和虚拟主机名**msnw2ascs**;ERS 实例编号**12** ，虚拟主机名为**msnw2ers**。  
* **NW3**： ASCS 实例编号**20**和虚拟主机名**msnw3ascs**;ERS 实例编号**22**且虚拟主机名为**msnw3ers**。  

本文不涉及 SAP NFS 共享的数据库层和部署。 在本文的示例中，我们将使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)volume **sapMSID** for NFS 共享，前提是该卷已部署。 我们也假设，Azure NetApp 文件卷是用 NFSv3 协议部署的，并且 SAP 系统 NW1、NW2 和 NW3 的 ASCS 和 ERS 实例的群集资源存在以下文件路径：  

* volume sapMSID （nfs://10.42.0.4/sapmnt<b>NW1</b>）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW1</b>ascs）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW1</b>sys）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW1</b>ers）
* volume sapMSID （nfs://10.42.0.4/sapmnt<b>NW2</b>）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW2</b>ascs）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW2</b>sys）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW2</b>ers）
* volume sapMSID （nfs://10.42.0.4/sapmnt<b>NW3</b>）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW3</b>ascs）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW3</b>sys）
* volume sapMSID （nfs://10.42.0.4/usrsap<b>NW3</b>ers）

在开始之前，请先参阅以下 SAP 说明和文章：

* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* [Azure NetApp 文件文档][anf-azure-doc]
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2002167] 包含适用于 Red Hat Enterprise Linux 的建议 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP Netweaver in pacemaker cluster](https://access.redhat.com/articles/3150081)（Pacemaker 群集中的 SAP NetWeaver）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuring ASCS/ERS for SAP Netweaver with standalone resources in RHEL 7.5](https://access.redhat.com/articles/3569681)（使用 RHEL 7.5 中的独立资源为 SAP NetWeaver 配置 ASCS/ERS）
  * [在 RHEL 上的 Pacemaker 中，通过独立的排队服务器2（ENSA2）配置 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
* [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

在发生故障转移时，必须调整加入群集的虚拟机的大小，使其能够运行所有资源。 在多 SID 高可用性群集中，每个 SAP SID 都可以相互独立地进行故障转移。  

为了实现高可用性，SAP NetWeaver 需要高度可用的共享。 在本文档中，我们介绍了部署在[Azure NetApp 文件 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上的 SAP 共享的示例。 还可以在高度可用的[GlusterFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)上托管共享，多个 SAP 系统可以使用这些共享。  

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-rhel/ha-rhel-multi-sid.png)

> [!IMPORTANT]
> 在 Azure Vm 中，支持将 SAP ASCS/ERS 与 Red Hat Linux 作为来宾操作系统进行多 SID 群集，限制为同一群集上的**五个**sap sid。 每个新 SID 都增加了复杂性。 **不支持**在同一个群集中混合使用 SAP 排队复制服务器1和排队复制服务器2。 多 SID 群集介绍了如何在一个 Pacemaker 群集中安装具有不同 Sid 的多个 SAP ASCS/ERS 实例。 目前仅支持 ASCS/ERS 的多 SID 群集。  

> [!TIP]
> SAP ASCS/ERS 的多 SID 群集是复杂性更高的解决方案。 实现起来更为复杂。 执行维护活动（如 OS 修补）时，它还涉及更高的管理工作量。 在开始实际实施之前，请花些时间仔细规划部署和所有涉及的组件，如 Vm、NFS 装载、Vip、负载平衡器配置等。  

SAP NetWeaver ASCS、SAP NetWeaver SCS 和 SAP NetWeaver ERS 使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 建议使用[标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。  

以下列表显示了此多 SID 群集示例（包含三个 SAP 系统）的（A） SCS 和 ERS 负载均衡器的配置。 每个 Sid 的每个 ASCS 和 ERS 实例需要单独的前端 IP、运行状况探测和负载均衡规则。 将属于 ASCS/ASCS 群集的所有 Vm 分配给单个 ILB 的后端池。  

### <a name="ascs"></a>(A)SCS

* 前端配置
  * NW1 的 IP 地址：10.3.1.50
  * NW2 的 IP 地址：10.3.1.52
  * NW3 的 IP 地址：10.3.1.54

* 探测端口
  * 端口 620<strong>&lt;nr&gt;</strong>，因此对于 NW1、NW2 和 NW3 探测端口 620**00**，620**10**和 620**20**
* 负载均衡规则-为每个实例创建一个规则，即 NW1/ASCS、NW2/ASCS 和 NW3/ASCS。
  * 如果使用标准负载均衡器，请选择 " **HA 端口**"
  * 如果使用基本负载均衡器，则为以下端口创建负载均衡规则
    * 32<strong>nr&lt; TCP&gt;</strong>
    * 36<strong>nr&lt; TCP&gt;</strong>
    * 39<strong>nr&lt; TCP&gt;</strong>
    * 81<strong>nr&lt; TCP&gt;</strong>
    * 5<strong>nr&lt;13 TCP&gt;</strong>
    * 5<strong>nr&lt;14 TCP&gt;</strong>
    * 5<strong>nr&lt;16 TCP&gt;</strong>

### <a name="ers"></a>ERS

* 前端配置
  * NW1 10.3.1.51 的 IP 地址
  * NW2 10.3.1.53 的 IP 地址
  * NW3 10.3.1.55 的 IP 地址

* 探测端口
  * 端口 621<strong>&lt;nr&gt;</strong>，因此对于 NW1、NW2 和 N3 探测端口 621**02**，621**12** ，621**22**
* 负载均衡规则-为每个实例创建一个规则，即 NW1/ERS、NW2/ERS 和 NW3/ERS。
  * 如果使用标准负载均衡器，请选择 " **HA 端口**"
  * 如果使用基本负载均衡器，则为以下端口创建负载均衡规则
    * 32<strong>nr&lt; TCP&gt;</strong>
    * 33<strong>nr&lt; TCP&gt;</strong>
    * 5<strong>nr&lt;13 TCP&gt;</strong>
    * 5<strong>nr&lt;14 TCP&gt;</strong>
    * 5<strong>nr&lt;16 TCP&gt;</strong>

* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口

> [!Note]
> 如果没有公共 IP 地址的 Vm 放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，则不会有出站 internet 连接，除非执行其他配置以允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅[使用 Azure 标准负载均衡器在 SAP 高可用性方案中的虚拟机的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

> [!IMPORTANT]
> 不要在 azure 负载均衡器后面的 Azure Vm 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数**tcp_timestamps**设置为**0**。 有关详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

## <a name="sap-shares"></a>SAP 共享

SAP NetWeaver 要求传输、配置文件目录等共享存储。 对于高度可用的 SAP 系统，具有高度可用的共享很重要。 需要确定 SAP 共享的体系结构。 一种选择是在[Azure NetApp 文件 NFS 卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)上部署共享。  对于 Azure NetApp 文件，你将获得 SAP NFS 共享的内置高可用性。

另一种做法是在[Azure vm Red Hat Enterprise Linux 上构建 GlusterFS，用于 SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)，可在多个 SAP 系统之间共享。 

## <a name="deploy-the-first-sap-system-in-the-cluster"></a>在群集中部署第一个 SAP 系统

现在，你已决定 SAP 共享的体系结构，请按照相应的文档在群集中部署第一个 SAP 系统。

* 如果使用 Azure NetApp 文件 NFS 卷，请[在 Azure 虚拟机上使用适用于 sap 应用程序的 Azure Netapp 文件 Red Hat Enterprise Linux 的 Azure vm 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)  
* 如果使用的是 GlusterFS 群集，请遵循[Azure vm 上的 GlusterFS Red Hat Enterprise Linux FOR SAP NetWeaver](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)。  

以上列出的文档将指导你完成准备所需的基础结构、构建群集、为运行 SAP 应用程序准备 OS 的步骤。  

> [!TIP]
> 在将其他 SAP Sid 添加到群集之前，请始终测试群集的故障转移功能。 这样，在将其他 SAP 系统的复杂性添加到群集之前，就可以了解群集功能的工作原理。   

## <a name="deploy-additional-sap-systems-in-the-cluster"></a>在群集中部署其他 SAP 系统

在此示例中，我们假定已在群集中部署了系统**NW1** 。 我们将演示如何在群集 SAP 系统**NW2**和**NW3**中进行部署。 

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

### <a name="prerequisites"></a>必备条件 

> [!IMPORTANT]
> 在按照说明在群集中部署其他 SAP 系统之前，请按照说明在群集中部署第一个 SAP 系统，因为在第一次部署系统的过程中，只需要执行一些步骤。  

本文档假定：
* Pacemaker 群集已配置且正在运行。  
* 至少一个 SAP 系统（ASCS/ERS 实例）已部署并且正在群集中运行。  
* 已测试群集故障转移功能。  
* 部署所有 SAP 系统的 NFS 共享。  

### <a name="prepare-for-sap-netweaver-installation"></a>准备 SAP NetWeaver 安装

1. 按照说明[通过 Azure 门户手动部署 Azure 负载均衡器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#deploy-linux-manually-via-azure-portal)，将新部署的系统（即**NW2**、 **NW3**）的配置添加到现有的 azure 负载均衡器。 调整配置的 IP 地址、运行状况探测端口和负载均衡规则。  

2. **[A]** 为其他 SAP 系统设置名称解析。 你可以使用 DNS 服务器，或修改所有节点上的 `/etc/hosts`。 此示例演示如何使用 `/etc/hosts` 文件。  根据你的环境调整 IP 地址和主机名。 

    ```
    sudo vi /etc/hosts
    # IP address of the load balancer frontend configuration for NW2 ASCS
    10.3.1.52 msnw2ascs
    # IP address of the load balancer frontend configuration for NW3 ASCS
    10.3.1.54 msnw3ascs
    # IP address of the load balancer frontend configuration for NW2 ERS
    10.3.1.53 msnw2ers
    # IP address of the load balancer frontend configuration for NW3 ERS
    10.3.1.55 msnw3ers
   ```

3. **[A]** 为要部署到群集的其他**NW2**和**NW3** SAP 系统创建共享目录。 

    ```
    sudo mkdir -p /sapmnt/NW2
    sudo mkdir -p /usr/sap/NW2/SYS
    sudo mkdir -p /usr/sap/NW2/ASCS10
    sudo mkdir -p /usr/sap/NW2/ERS12
    sudo mkdir -p /sapmnt/NW3
    sudo mkdir -p /usr/sap/NW3/SYS
    sudo mkdir -p /usr/sap/NW3/ASCS20
    sudo mkdir -p /usr/sap/NW3/ERS22

    
    sudo chattr +i /sapmnt/NW2
    sudo chattr +i /usr/sap/NW2/SYS
    sudo chattr +i /usr/sap/NW2/ASCS10
    sudo chattr +i /usr/sap/NW2/ERS12
    sudo chattr +i /sapmnt/NW3
    sudo chattr +i /usr/sap/NW3/SYS
    sudo chattr +i /usr/sap/NW3/ASCS20
    sudo chattr +i /usr/sap/NW3/ERS22
   ```

4. **[A]** 为要部署到群集的其他 sap 系统添加/Sapmnt/SID 和/usr/sap/SID/SYS 文件系统的装载条目。 在此示例中， **NW2**和**NW3**。  

   针对要部署到群集的其他 SAP 系统，用文件系统更新文件 `/etc/fstab`。  

   * 如果使用 Azure NetApp 文件，请按照[此处](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#prepare-for-sap-netweaver-installation)的说明进行操作  
   * 如果使用 GlusterFS 群集，请按照[此处](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel#prepare-for-sap-netweaver-installation)的说明进行操作  

### <a name="install-ascs--ers"></a>安装 ASCS/ERS

1. 为要部署到群集的其他 SAP 系统的 ASCS 实例创建虚拟 IP 和运行状况探测群集资源。 此处所示的示例针对的是**NW2**和**NW3** ASCS，在 Azure NetApp 文件卷上使用带有 NFSv3 协议的 NFS。  

    ```
    sudo pcs resource create fs_NW2_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ascs' \
    directory='/usr/sap/NW2/ASCS10' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_ASCS

    sudo pcs resource create vip_NW2_ASCS IPaddr2 \
    ip=10.3.1.52 cidr_netmask=24 \
     --group g-NW2_ASCS
    
    sudo pcs resource create nc_NW2_ASCS azure-lb port=62010 \
     --group g-NW2_ASCS

    sudo pcs resource create fs_NW3_ASCS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ascs' \
    directory='/usr/sap/NW3/ASCS20' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
    --group g-NW3_ASCS

    sudo pcs resource create vip_NW3_ASCS IPaddr2 \
    ip=10.3.1.54 cidr_netmask=24 \
    --group g-NW3_ASCS

    sudo pcs resource create nc_NW3_ASCS azure-lb port=62020 \
    --group g-NW3_ASCS
    ```

   请确保群集状态为 "正常"，并且所有资源均已启动。 资源在哪个节点上运行并不重要。  

2. [1] 安装 SAP NetWeaver ASCS  

   使用映射到 ASCS 的负载均衡器前端配置的 IP 地址的虚拟主机，以 root 身份安装 SAP NetWeaver ASCS。 例如，对于 system **NW2**，虚拟主机名为<b>msnw2ascs</b>、 <b>10.3.1.52</b>和用于探测负载均衡器的实例号，例如<b>10</b>。 对于 system **NW3**，虚拟主机名为<b>msnw3ascs</b>、 <b>10.3.1.54</b>和用于探测负载均衡器的实例号，例如<b>20</b>。 记下在哪个群集节点上安装了每个 SAP SID 的 ASCS。  

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 可以使用参数 SAPINST_USE_HOSTNAME 使用虚拟主机名安装 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   如果安装无法在/usr/sap/**SID**/ASCS**instance #** 中创建子文件夹，请尝试将所有者设置为**SID**ADM 并将组设置为 ASCS**实例 #** 的 sapsys，然后重试。

3. **[1]** 为要部署到群集的其他 SAP 系统的 ERS 实例创建虚拟 IP 和运行状况探测群集资源。 此处所示的示例针对的是**NW2**和**NW3** ERS，在 Azure NetApp 文件卷上使用带有 NFSv3 协议的 NFS。  

    ```
    sudo pcs resource create fs_NW2_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW2ers' \
    directory='/usr/sap/NW2/ERS12' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW2_AERS

    sudo pcs resource create vip_NW2_AERS IPaddr2 \
    ip=10.3.1.53 cidr_netmask=24 \
     --group g-NW2_AERS

    sudo pcs resource create nc_NW2_AERS azure-lb port=62112 \
     --group g-NW2_AERS

    sudo pcs resource create fs_NW3_AERS Filesystem device='10.42.0.4:/sapMSIDR/usrsapNW3ers' \
    directory='/usr/sap/NW3/ERS22' fstype='nfs' force_unmount=safe \
    op start interval=0 timeout=60 op stop interval=0 timeout=120 op monitor interval=200 timeout=40 \
     --group g-NW3_AERS

    sudo pcs resource create vip_NW3_AERS IPaddr2 \
    ip=10.3.1.55 cidr_netmask=24 \
     --group g-NW3_AERS

    sudo pcs resource create nc_NW3_AERS azure-lb port=62122 \
     --group g-NW3_AERS
   ```

   请确保群集状态为 "正常"，并且所有资源均已启动。  

   接下来，请确保新创建的 ERS 组的资源在群集节点上运行，这与安装了同一 SAP 系统的 ASCS 实例的群集节点相反。  例如，如果 `rhelmsscl1`上安装了 NW2 ASCS，请确保 NW2 ERS 组正在 `rhelmsscl2`上运行。  可以通过为组中的其中一个群集资源运行以下命令，将 NW2 ERS 组迁移到 `rhelmsscl2`： 

    ```
      pcs resource move fs_NW2_AERS rhelmsscl2
    ```

4. [2] 安装 SAP Netweaver ERS

   使用映射到 ERS 的负载均衡器前端配置的 IP 地址的虚拟主机名，在另一个节点上以 root 身份安装 SAP NetWeaver ERS。 例如，对于 system **NW2**，虚拟主机名为<b>msnw2ers</b>、 <b>10.3.1.53</b>和用于探测负载均衡器的实例号，例如<b>12</b>。 对于 system **NW3**，为用于探测负载均衡器的虚拟主机名<b>msnw3ers</b>、 <b>10.3.1.55</b>和实例编号（例如<b>22</b>）。 

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 可以使用参数 SAPINST_USE_HOSTNAME 使用虚拟主机名安装 SAP。  

    ```
    # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again
    sudo firewall-cmd --zone=public --add-port=4237/tcp
    sudo swpm/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=virtual_hostname
    ```

   > [!NOTE]
   > 使用 SWPM SP 20 PL 05 或更高版本。 较低版本不会正确设置权限，安装将失败。

   如果安装无法在/usr/sap/**NW2**/ERS**实例 #** 中创建**子文件夹，** 请尝试将 "所有者" 设置为 " **sid**adm"，将 "组" 设置为 "sapsys"，然后重试。

   如果需要将新部署的 SAP 系统的 ERS 组迁移到另一个群集节点，请不要忘记删除 ERS 组的位置约束。 可以通过运行以下命令来删除约束（该示例是针对 SAP systems **NW2**和**NW3**提供的）。 请确保删除在命令中用于移动 ERS 群集组的同一资源的临时约束。

    ```
      pcs resource clear fs_NW2_AERS
      pcs resource clear fs_NW3_AERS
    ```

5. **[1]** 改编新安装的 SAP 系统的 ASCS/SCS 和 ERS 实例配置文件。 下面显示的示例适用于 NW2。 你将需要为添加到群集中的所有 SAP 实例调整 ASCS/SCS 和 ERS 配置文件。  
 
   * ASCS/SCS 配置文件

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs
   
      # Change the restart command to a start command
      #Restart_Program_01 = local $(_EN) pf=$(_PF)
      Start_Program_01 = local $(_EN) pf=$(_PF)

      # Add the keep alive parameter
      enque/encni/set_so_keepalive = true
      ```

   * ERS 配置文件

      ```
      sudo vi /sapmnt/NW2/profile/NW2_ERS12_msnw2ers
   
      # Change the restart command to a start command
      #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
      Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
      # remove Autostart from ERS profile
      # Autostart = 1
      ```

6. **[A]** 更新 /usr/sap/sapservices 文件

   若要防止 sapinit 启动脚本启动实例，必须从 `/usr/sap/sapservices` 文件中注释掉 Pacemaker 管理的所有实例。  下面显示的示例适用于 SAP 系统**NW2**和**NW3**。  

   ```
    # On the node where ASCS was installed, comment out the line for the ASCS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ASCS10/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ASCS10/exe/sapstartsrv pf=/usr/sap/NW2/SYS/profile/NW2_ASCS10_msnw2ascs -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ASCS20/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ASCS20/exe/sapstartsrv pf=/usr/sap/NW3/SYS/profile/NW3_ASCS20_msnw3ascs -D -u nw3adm

    # On the node where ERS was installed, comment out the line for the ERS instacnes
    #LD_LIBRARY_PATH=/usr/sap/NW2/ERS12/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW2/ERS12/exe/sapstartsrv pf=/usr/sap/NW2/ERS12/profile/NW2_ERS12_msnw2ers -D -u nw2adm
    #LD_LIBRARY_PATH=/usr/sap/NW3/ERS22/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/NW3/ERS22/exe/sapstartsrv pf=/usr/sap/NW3/ERS22/profile/NW3_ERS22_msnw3ers -D -u nw3adm
   ```

7. **[1]** 创建新安装的 sap 系统的 sap 群集资源。  

   如果使用 "排队服务器1体系结构" （ENSA1），请按如下所示定义 SAP systems **NW2**和**NW3**的资源：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint location rsc_sap_NW2_ASCS10 rule score=2000 runs_ers_NW2 eq 1
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint location rsc_sap_NW3_ASCS20 rule score=2000 runs_ers_NW3 eq 1
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   SAP 在 SAP NW 7.52 中引入了对排队服务器2（包括复制）的支持。 从 ABAP 平台1809开始，默认情况下会安装排队服务器2。 有关排队服务器2支持，请参阅 SAP 说明[2630416](https://launchpad.support.sap.com/#/notes/2630416) 。
   如果使用 "排队服务器2体系结构" （[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），请按如下所示定义 SAP systems **NW2**和**NW3**的资源：

    ```
     sudo pcs property set maintenance-mode=true

    sudo pcs resource create rsc_sap_NW2_ASCS10 SAPInstance \
    InstanceName=NW2_ASCS10_msnw2ascs START_PROFILE="/sapmnt/NW2/profile/NW2_ASCS10_msnw2ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_ASCS

    sudo pcs resource create rsc_sap_NW2_ERS12 SAPInstance \
    InstanceName=NW2_ERS12_msnw2ers START_PROFILE="/sapmnt/NW2/profile/NW2_ERS12_msnw2ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW2_AERS

    sudo pcs constraint colocation add g-NW2_AERS with g-NW2_ASCS -5000
    sudo pcs constraint order g-NW2_ASCS then g-NW2_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW2_ASCS then stop g-NW2_AERS symmetrical=false

    sudo pcs resource create rsc_sap_NW3_ASCS20 SAPInstance \
    InstanceName=NW3_ASCS20_msnw3ascs START_PROFILE="/sapmnt/NW3/profile/NW3_ASCS20_msnw3ascs" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 failure-timeout=60 \
    op monitor interval=20 on-fail=restart timeout=60 \
    op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_ASCS

    sudo pcs resource create rsc_sap_NW3_ERS22 SAPInstance \
    InstanceName=NW3_ERS22_msnw3ers START_PROFILE="/sapmnt/NW3/profile/NW2_ERS22_msnw3ers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    op monitor interval=20 on-fail=restart timeout=60 op start interval=0 timeout=600 op stop interval=0 timeout=600 \
     --group g-NW3_AERS

    sudo pcs constraint colocation add g-NW3_AERS with g-NW3_ASCS -5000
    sudo pcs constraint order g-NW3_ASCS then g-NW3_AERS kind=Optional symmetrical=false
    sudo pcs constraint order start g-NW3_ASCS then stop g-NW3_AERS symmetrical=false

    sudo pcs property set maintenance-mode=false
    ```

   如果从较旧版本升级并切换到排队服务器2，请参阅 SAP 说明[2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   > [!NOTE]
   > 上述配置中的超时只是示例，可能需要适应特定的 SAP 设置。 

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。
   以下示例显示了在 SAP 系统**NW2**和**NW3**添加到群集后，群集资源的状态。 

    ```
     sudo pcs status

    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    ```

8. **[A]** 为两个节点上的 ASCS 和 ERS 添加防火墙规则。  以下示例显示了适用于 SAP 系统**NW2**和**NW3**的防火墙规则。  

   ```
    # NW2 - ASCS
    sudo firewall-cmd --zone=public --add-port=62010/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62010/tcp
    sudo firewall-cmd --zone=public --add-port=3210/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3210/tcp
    sudo firewall-cmd --zone=public --add-port=3610/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3610/tcp
    sudo firewall-cmd --zone=public --add-port=3910/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3910/tcp
    sudo firewall-cmd --zone=public --add-port=8110/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8110/tcp
    sudo firewall-cmd --zone=public --add-port=51013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51013/tcp
    sudo firewall-cmd --zone=public --add-port=51014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51014/tcp
    sudo firewall-cmd --zone=public --add-port=51016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51016/tcp
    # NW2 - ERS
    sudo firewall-cmd --zone=public --add-port=62112/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62112/tcp
    sudo firewall-cmd --zone=public --add-port=3312/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3312/tcp
    sudo firewall-cmd --zone=public --add-port=51213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51213/tcp
    sudo firewall-cmd --zone=public --add-port=51214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51214/tcp
    sudo firewall-cmd --zone=public --add-port=51216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=51216/tcp
    # NW3 - ASCS
    sudo firewall-cmd --zone=public --add-port=62020/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62020/tcp
    sudo firewall-cmd --zone=public --add-port=3220/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3220/tcp
    sudo firewall-cmd --zone=public --add-port=3620/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3620/tcp
    sudo firewall-cmd --zone=public --add-port=3920/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3920/tcp
    sudo firewall-cmd --zone=public --add-port=8120/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=8120/tcp
    sudo firewall-cmd --zone=public --add-port=52013/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52013/tcp
    sudo firewall-cmd --zone=public --add-port=52014/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52014/tcp
    sudo firewall-cmd --zone=public --add-port=52016/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52016/tcp
    # NW3 - ERS
    sudo firewall-cmd --zone=public --add-port=62122/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=62122/tcp
    sudo firewall-cmd --zone=public --add-port=3322/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=3322/tcp
    sudo firewall-cmd --zone=public --add-port=52213/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52213/tcp
    sudo firewall-cmd --zone=public --add-port=52214/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52214/tcp
    sudo firewall-cmd --zone=public --add-port=52216/tcp --permanent
    sudo firewall-cmd --zone=public --add-port=52216/tcp
   ```

### <a name="proceed-with-the-sap-installation"></a>继续进行 SAP 安装 

通过以下方式完成 SAP 安装：

* [准备 SAP NetWeaver 应用程序服务器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#2d6008b0-685d-426c-b59e-6cd281fd45d7)
* [安装 DBMS 实例](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#install-database)
* [安装主 SAP 应用程序服务器](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files#sap-netweaver-application-server-installation)
* 安装一个或多个其他 SAP 应用程序实例

## <a name="test-the-multi-sid-cluster-setup"></a>测试多 SID 群集设置

以下测试是 Red Hat 的最佳实践指南中的测试用例的子集。 为了方便起见，已将其包含在内。 有关群集测试的完整列表，请参阅以下文档：

* 如果使用 Azure NetApp 文件 NFS 卷，请遵循[适用于 sap 应用程序的 Azure Netapp 文件的 Azure vm 高可用性 Sap NetWeaver ON RHEL](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-netapp-files)
* 如果使用高可用 `GlusterFS`，请遵循[RHEL FOR sap 应用程序上的 Sap NetWeaver 的 Azure vm 高可用性](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)。  

请始终阅读 Red Hat 最佳实践指南，并执行可能已添加的所有其他测试。  
所呈现的测试位于两个节点，多 SID 群集中安装了三个 SAP 系统。  

1. 手动迁移 ASCS 实例。 该示例演示了如何迁移 SAP system NW3 的 ASCS 实例。

   开始测试之前的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   以 root 身份运行以下命令，以迁移 NW3 ASCS 实例。

   ```
    pcs resource move rsc_sap_NW3_ASCS200
    # Clear temporary migration constraints
    pcs resource clear rsc_sap_NW3_ASCS20

    # Remove failed actions for the ERS that occurred as part of the migration
    pcs resource cleanup rsc_sap_NW3_ERS22
   ```

   测试之后的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

1. 模拟节点故障

   开始测试之前的资源状态：

   ```
    Online: [ rhelmsscl1 rhelmsscl2 ]

    Full list of resources:

    rsc_st_azure   (stonith:fence_azure_arm):      Started rhelmsscl1
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
   ```

   在节点上以 root 身份运行以下命令，其中至少有一个 ASCS 实例正在运行。 在此示例中，我们在 `rhelmsscl1`上执行了命令，其中 NW1、NW2 和 NW3 的 ASCS 实例正在运行。  

   ```
   echo c > /proc/sysrq-trigger
   ```

   已崩溃的测试后和节点之后的状态将再次开始，应如下所示。

   ```
    Full list of resources:

    rsc_st_azure    (stonith:fence_azure_arm):      Started rhelmsscl2
    Resource Group: g-NW1_ASCS
        fs_NW1_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW1_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW1_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW1_ASCS00 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW1_AERS
        fs_NW1_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW1_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW1_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW1_ERS02  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW2_ASCS
        fs_NW2_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW2_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW2_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW2_ASCS10 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW2_AERS
        fs_NW2_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW2_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW2_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW2_ERS12  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
    Resource Group: g-NW3_ASCS
        fs_NW3_ASCS        (ocf::heartbeat:Filesystem):    Started rhelmsscl2
        vip_NW3_ASCS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl2
        nc_NW3_ASCS        (ocf::heartbeat:azure-lb):      Started rhelmsscl2
        rsc_sap_NW3_ASCS20 (ocf::heartbeat:SAPInstance):   Started rhelmsscl2
    Resource Group: g-NW3_AERS
        fs_NW3_AERS        (ocf::heartbeat:Filesystem):    Started rhelmsscl1
        vip_NW3_AERS       (ocf::heartbeat:IPaddr2):       Started rhelmsscl1
        nc_NW3_AERS        (ocf::heartbeat:azure-lb):      Started rhelmsscl1
        rsc_sap_NW3_ERS22  (ocf::heartbeat:SAPInstance):   Started rhelmsscl1
   ```

   如果有失败资源的消息，请清除失败资源的状态。 例如：

   ```
   pcs resource cleanup rsc_sap_NW1_ERS02
   ```

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅[Azure 虚拟机（vm）上的 SAP HANA 的高可用性][sap-hana-ha]
