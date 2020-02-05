---
title: Azure 虚拟机上的 azure 虚拟机高可用性 Azure NetApp 文件 SUSE Linux Enterprise Server 上的 SAP NetWeaver |Microsoft Docs
description: 适用于 sap NetWeaver 的高可用性指南，适用于 SAP 应用程序的 Azure NetApp 文件 SUSE Linux Enterprise Server
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
ms.date: 02/03/2020
ms.author: radeltch
ms.openlocfilehash: 1a413ce55604ef8b5c3219e8de466fcc23d41bac
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2020
ms.locfileid: "76990935"
---
# <a name="high-availability-for-sap-netweaver-on-azure-vms-on-suse-linux-enterprise-server-with-azure-netapp-files-for-sap-applications"></a>Azure Vm 上的 SAP NetWeaver 高可用性，适用于 SAP 应用程序的 Azure NetApp 文件 SUSE Linux Enterprise Server

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

本文介绍如何使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)部署虚拟机、配置虚拟机、安装群集框架，以及安装高可用性 SAP NetWeaver 7.50 系统。
在示例配置和安装命令等中，ASCS 实例为 number 00，ERS 实例编号01，主应用程序实例（PAS）为02，应用程序实例（.AAS）为03。 使用 SAP 系统 ID QAS。 

本文介绍如何通过 Azure NetApp 文件实现 SAP NetWeaver 应用程序的高可用性。 本文不详细介绍数据库层。

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533][1928533]，其中包含：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553][2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2205917][2205917] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的推荐 OS 设置
* SAP 说明 [1944799][1944799] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的 SAP HANA 准则
* SAP 说明 [2178632][2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498][2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692][2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1984787][1984787] 包含有关 SUSE Linux Enterprise Server 12 的一般信息。
* SAP 说明 [1999351][1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 社区 WIKI] （ https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳实践指南][suse-ha-guide]本指南包含在本地设置 Netweaver HA 和 SAP HANA 系统复制所需的所有信息。 请使用上述指南作为常规基准。 它们提供更多详细信息。
* [SUSE 高可用性扩展 12 SP3 发行说明][suse-ha-12sp3-relnotes]
* [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

SAP Netweaver central services 的高可用性（HA）需要共享存储。
若要在 SUSE Linux 上实现此目的，需要构建单独的高可用性 NFS 群集。 

现在，可以使用共享存储实现 SAP Netweaver HA，并将其部署在 Azure NetApp 文件中。 使用共享存储的 Azure NetApp 文件无需额外的[NFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)。 SAP Netweaver central services （ASCS/SCS）的 HA 仍需要 Pacemaker。


![SAP NetWeaver 高可用性概述](./media/high-availability-guide-suse-anf/high-availability-guide-suse-anf.PNG)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP HANA 数据库使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要使用虚拟 IP 地址的[负载均衡器](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)。 建议使用[标准负载均衡器](https://docs.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)。 以下列表显示 (A)SCS 和 ERS 负载均衡器的配置。

> [!IMPORTANT]
> **不支持**将 SAP ASCS/ERS 的多 SID 群集与 SUSE Linux 作为 Azure vm 中的来宾操作系统。 多 SID 群集介绍在一个 Pacemaker 群集中安装具有不同 Sid 的多个 SAP ASCS/ERS 实例


### <a name="ascs"></a>(A)SCS

* 前端配置
  * IP 地址10.1.1.20
* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口
* 探测端口
  * 端口 620&lt;nr&gt;
* 负载均衡规则
  * 如果使用标准负载均衡器，请选择 " **HA 端口**"
  * 如果使用基本负载均衡器，则为以下端口创建负载均衡规则
    * 32&lt;nr&gt; TCP
    * 36&lt;nr&gt; TCP
    * 39&lt;nr&gt; TCP
    * 81&lt;nr&gt; TCP
    * 5&lt;nr&gt;13 TCP
    * 5&lt;nr&gt;14 TCP
    * 5&lt;nr&gt;16 TCP

### <a name="ers"></a>ERS

* 前端配置
  * IP 地址10.1.1.21
* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口
* 探测端口
  * 端口 621&lt;nr&gt;
* 负载均衡规则
  * 如果使用标准负载均衡器，请选择 " **HA 端口**"
  * 如果使用基本负载均衡器，则为以下端口创建负载均衡规则
    * 32&lt;nr&gt; TCP
    * 33&lt;nr&gt; TCP
    * 5&lt;nr&gt;13 TCP
    * 5&lt;nr&gt;14 TCP
    * 5&lt;nr&gt;16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

SAP NetWeaver 要求对传输和配置文件目录使用共享存储。  在继续安装 Azure NetApp 文件基础结构之前，请先熟悉[Azure Netapp 文件文档][anf-azure-doc]。 检查所选的 Azure 区域是否提供 Azure NetApp 文件。 以下链接显示了 azure 区域的 azure NetApp 文件的可用性： azure [netapp 的 Azure Netapp 文件可用性][anf-avail-matrix]。

Azure NetApp 文件在多个[azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 在部署 Azure NetApp 文件之前，请在[注册 azure][anf-register]netapp 文件说明后，请求加入 Azure netapp 文件。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

这些步骤假定你已部署[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 文件资源和将在其中装入 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。  

1. 如果尚未执行此操作，请请求[加入 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 按照[创建 Netapp 帐户的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)，在所选的 Azure 区域中创建 netapp 帐户。  
3. 按照[如何设置 Azure Netapp 文件容量池的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)设置 Azure netapp 文件容量池。  
本文中介绍的 SAP Netweaver 体系结构使用了单个 Azure NetApp 文件容量池、高级 SKU。 对于 Azure 上的 SAP Netweaver 应用程序工作负载，我们建议 Azure NetApp 文件高级 SKU。  

4. 按照[说明将子网委托给 Azure Netapp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中所述，将子网委托给 azure netapp 文件。  

5. 按照[说明为 Azure Netapp 文件创建卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)，部署 Azure netapp 文件卷。 在指定的 Azure NetApp 文件[子网](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中，或者位于对等互连 Azure 虚拟网络中。 例如，sapmnt<b>QAS</b>、usrsap<b>QAS</b>等。是卷名称<b>，sapmnt QAS</b>，Usrsap<b>QAS</b>等，是 Azure NetApp 文件卷的 filepaths。  

   1. volume sapmnt<b>QAS</b> （nfs://10.1.0.4/sapmnt<b>QAS</b>）
   2. volume usrsap<b>QAS</b> （nfs://10.1.0.4/usrsap<b>QAS</b>）
   3. volume usrsap<b>QAS</b>sys （nfs://10.1.0.5/usrsap<b>QAS</b>sys）
   4. volume usrsap<b>QAS</b>ers （nfs://10.1.0.4/usrsap<b>QAS</b>ers）
   5. 卷事务（nfs://10.1.0.4/trans）
   6. volume usrsap<b>QAS</b>pas （nfs://10.1.0.5/usrsap<b>QAS</b>pas）
   7. volume usrsap<b>QAS</b>.aas （nfs://10.1.0.4/usrsap<b>QAS</b>.aas）
   
在此示例中，我们使用了适用于所有 SAP Netweaver 文件系统的 Azure NetApp 文件来演示如何使用 Azure NetApp 文件。 不需要通过 NFS 装载的 SAP 文件系统也可以部署为[Azure 磁盘存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。 在此示例中， <b>-e</b>必须位于 Azure NetApp 文件上，并且<b>f-g</b> （即/usr/sap/<b>QAS</b>/d<b>02</b>，/Usr/sap/<b>QAS</b>/d<b>03</b>）可以部署为 azure 磁盘存储。 

### <a name="important-considerations"></a>重要注意事项

考虑在 SUSE 高可用性体系结构上用于 SAP Netweaver 的 Azure NetApp 文件时，请注意以下重要事项：

- 最小容量池为 4 TiB。 容量池大小可以增加1个 TiB。
- 最小卷为 100 GiB
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机都必须位于同一个 Azure 虚拟网络中，或位于同一区域中的[对等互连虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 现在支持通过同一区域中的 VNET 对等互连进行 Azure NetApp 文件访问。 目前尚不支持通过全局对等互连进行 Azure NetApp 访问。
- 所选虚拟网络必须具有子网，并委托给 Azure NetApp 文件。
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：可控制允许的客户端、访问类型（读取 & 写入、只读等）。 
- Azure NetApp 文件功能尚不能识别区域。 目前，azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域中潜在的延迟影响。 
- Azure NetApp 文件卷可部署为 NFSv3 或 NFSv 4.1 卷。 SAP 应用程序层（ASCS/ERS、SAP 应用程序服务器）支持这两种协议。 

## <a name="deploy-linux-vms-manually-via-azure-portal"></a>通过 Azure 门户手动部署 Linux Vm

首先，需要创建 Azure NetApp 文件卷。 部署 Vm。 之后，创建一个负载均衡器并使用后端池中的虚拟机。

1. 创建资源组。
1. 创建虚拟网络
1. 为 ASCS 创建可用性集  
   设置最大更新域
1. 创建虚拟机 1  
   至少使用使用 SLES4SAP 12 SP3，在此示例中，将使用使用 SLES4SAP 12 SP3 映像  
   选择以前为 ASCS 创建的可用性集  
1. 创建虚拟机 2  
   至少使用使用 SLES4SAP 12 SP3，在此示例中，将使用使用 SLES4SAP 12 SP3 映像  
   选择以前为 ASCS 创建的可用性集  
1. 为 SAP 应用程序实例（PAS、.AAS）创建可用性集    
   设置最大更新域
1. 创建虚拟机3  
   至少使用使用 SLES4SAP 12 SP3，在此示例中，将使用使用 SLES4SAP 12 SP3 映像  
   选择以前为 PAS/.AAS 创建的可用性集   
1. 创建虚拟机4  
   至少使用使用 SLES4SAP 12 SP3，在此示例中，将使用使用 SLES4SAP 12 SP3 映像  
   选择以前为 PAS/.AAS 创建的可用性集  

## <a name="disable-id-mapping-if-using-nfsv41"></a>禁用 ID 映射（如果使用 NFSv 4.1）

本部分中的说明仅适用于使用带有 NFSv 4.1 协议的 Azure NetApp 文件的卷。 在所有 Vm 上执行配置，其中将装载 Azure NetApp 文件 NFSv 4.1 卷。  

1. 验证 NFS 域设置。 请确保将该域配置为默认的 Azure NetApp 文件域，即 **`defaultv4iddomain.com`** ，并且映射设置为 "无**人**"。  

    > [!IMPORTANT]
    > 请确保在 VM `/etc/idmapd.conf` 上设置 NFS 域，使其与 Azure NetApp 文件上的默认域配置匹配： **`defaultv4iddomain.com`** 。 如果 NFS 客户端（即 VM）上的域配置与 NFS 服务器（即 Azure NetApp 配置）之间存在不匹配的情况，则在 Vm 上装载的 Azure NetApp 卷上的文件权限将显示为 `nobody`。  

    <pre><code>
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>defaultv4iddomain.com</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 验证 `nfs4_disable_idmapping`。 它应设置为**Y**。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 由于已为内核/驱动程序保留访问权限，因此无法在/sys/modules 下手动创建目录。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.1.0.4:/sapmnt/<b>qas</b> /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>


## <a name="setting-up-ascs"></a>设置 (A)SCS

在此示例中，资源是通过[Azure 门户](https://portal.azure.com/#home)手动部署的。

### <a name="deploy-azure-load-balancer-manually-via-azure-portal"></a>通过 Azure 门户手动部署 Azure 负载均衡器

首先，需要创建 Azure NetApp 文件卷。 部署 Vm。 之后，创建一个负载均衡器并使用后端池中的虚拟机。

1. 创建负载平衡器（内部、标准）：  
   1. 创建前端 IP 地址
      1. ASCS 的 IP 地址10.1.1.20
         1. 打开负载均衡器，选择前端 IP 池，并单击“添加”
         1. 输入新前端 IP 池的名称（例如 "前端" **。QAS.ASCS**）
         1. 将 "分配" 设置为 "静态" 并输入 IP 地址（例如**10.1.1.20**）
         1. 单击“确定”
      1. ASCS ERS 的 IP 地址10.1.1.21
         * 重复上面的 "a" 中的步骤，创建 ERS 的 IP 地址（例如**10.1.1.21**和**前端。QAS.ERS**）
   1. 创建后端池
      1. 为 ASCS 创建后端池
         1. 打开负载均衡器，单击后端池，并单击“添加”
         1. 输入新后端池的名称（例如**后端。QAS**）
         1. 单击“添加虚拟机”。
         1. 选择虚拟机
         1. 选择（A） SCS 群集的虚拟机及其 IP 地址。
         1. 单击“添加”
   1. 创建运行状况探测
      1. ASCS 的端口 620**00**
         1. 打开负载均衡器，选择运行状况探测，并单击“添加”
         1. 输入新运行状况探测的名称（例如**运行状况）。QAS.ASCS**）
         1. 选择 TCP 作为协议，选择端口 620**00**，将“间隔”保留为 5，将“不正常阈值”保留为 2
         1. 单击“确定”
      1. ASCS ERS 的端口 621**01**
            * 重复上述 "c" 下的步骤，为 ERS 创建运行状况探测（例如 621**01**和**health。QAS.ERS**）
   1. 负载均衡规则
      1. 为 ASCS 创建后端池
         1. 打开负载均衡器，选择 "负载均衡规则"，并单击 "添加"
         1. 输入新负载均衡器规则的名称（例如**lb。QAS.ASCS**）
         1. 选择之前创建的 ASCS、后端池和运行状况探测的前端 IP 地址（例如 "**前端"。QAS.ASCS**、**后端。QAS**和**运行状况。QAS.ASCS**）
         1. 选择**HA 端口**
         1. 将空闲超时增大到 30 分钟
         1. **确保启用浮动 IP**
         1. 单击“确定”
         * 重复上述步骤来为 ERS 创建负载均衡规则（例如**lb。QAS.ERS**）
1. 或者，如果方案需要基本负载均衡器（内部），请执行以下步骤：  
   1. 创建前端 IP 地址
      1. ASCS 的 IP 地址10.1.1.20
         1. 打开负载均衡器，选择前端 IP 池，并单击“添加”
         1. 输入新前端 IP 池的名称（例如 "前端" **。QAS.ASCS**）
         1. 将 "分配" 设置为 "静态" 并输入 IP 地址（例如**10.1.1.20**）
         1. 单击“确定”
      1. ASCS ERS 的 IP 地址10.1.1.21
         * 重复上面的 "a" 中的步骤，创建 ERS 的 IP 地址（例如**10.1.1.21**和**前端。QAS.ERS**）
   1. 创建后端池
      1. 为 ASCS 创建后端池
         1. 打开负载均衡器，单击后端池，并单击“添加”
         1. 输入新后端池的名称（例如**后端。QAS**）
         1. 单击“添加虚拟机”。
         1. 选择之前为 ASCS 创建的可用性集 
         1. 选择 (A)SCS 群集的虚拟机
         1. 单击“确定”
   1. 创建运行状况探测
      1. ASCS 的端口 620**00**
         1. 打开负载均衡器，选择运行状况探测，并单击“添加”
         1. 输入新运行状况探测的名称（例如**运行状况）。QAS.ASCS**）
         1. 选择 TCP 作为协议，选择端口 620**00**，将“间隔”保留为 5，将“不正常阈值”保留为 2
         1. 单击“确定”
      1. ASCS ERS 的端口 621**01**
            * 重复上述 "c" 下的步骤，为 ERS 创建运行状况探测（例如 621**01**和**health。QAS.ERS**）
   1. 负载均衡规则
      1. ASCS 的 32**00** TCP
         1. 打开负载均衡器，选择 "负载均衡规则"，并单击 "添加"
         1. 输入新负载均衡器规则的名称（例如**lb。QAS.ASCS**）
         1. 选择之前创建的 ASCS、后端池和运行状况探测的前端 IP 地址（例如 "**前端"。QAS.ASCS**）
         1. 将协议保留为“TCP”，输入端口 **3200**
         1. 将空闲超时增大到 30 分钟
         1. **确保启用浮动 IP**
         1. 单击“确定”
      1. ASCS 的其他端口
         * 对于端口 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 和 TCP ASCS
      1. ASCS ERS 的其他端口
         * 为 ASCS ERS 的端口 33**01**、5**01**13、5**01**14、5**01**16 和 TCP 重复上述步骤。

      > [!Note]
      > 如果没有公共 IP 地址的 Vm 放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，则不会有出站 internet 连接，除非执行其他配置以允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅[使用 Azure 标准负载均衡器在 SAP 高可用性方案中的虚拟机的公共终结点连接](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-standard-load-balancer-outbound-connections)。  

      > [!IMPORTANT]
      > 不要在 azure 负载均衡器后面的 Azure Vm 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数**tcp_timestamps**设置为**0**。 有关详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>创建 Pacemaker 群集

遵循[在 Azure 中的 SUSE Linux Enterprise Server 上设置 Pacemaker](high-availability-guide-suse-pacemaker.md) 中的步骤为此 (A)SCS 服务器创建一个基本 Pacemaker 群集。

### <a name="installation"></a>安装

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. **[A]** 安装 SUSE 连接器

   <pre><code>sudo zypper install sap-suse-cluster-connector
   </code></pre>

   > [!NOTE]
   > 使用主机名中的短划线的已知问题与包**sap-** 3.1.1 的版本已修复。 如果将群集节点用于主机名中的短划线，请确保至少使用版本3.1.1 的 "包 sap-群集-连接器"。 否则群集无法正常使用。 

   请确保安装 SAP SUSE 群集连接器的新版本。 旧版本称为 sap_suse_cluster_connector，新版本称为 **sap-suse-cluster-connector**。

   <pre><code>sudo zypper info sap-suse-cluster-connector
   
    # Information for package sap-suse-cluster-connector:
    # ---------------------------------------------------
    # Repository     : SLE-12-SP3-SAP-Updates
    # Name           : sap-suse-cluster-connector
    # Version        : 3.1.0-8.1
    # Arch           : noarch
    # Vendor         : SUSE LLC &lt;https://www.suse.com/&gt;
    # Support Level  : Level 3
    # Installed Size : 45.6 KiB
    # Installed      : Yes
    # Status         : up-to-date
    # Source package : sap-suse-cluster-connector-3.1.0-8.1.src
    # Summary        : SUSE High Availability Setup for SAP Products
   </code></pre>

2. [A] 更新 SAP 资源代理  
   
   如本文中所述，使用新配置需要安装资源代理包的修补程序。 可以通过以下命令检查是否已安装了修补程序

   <pre><code>sudo grep 'parameter name="IS_ERS"' /usr/lib/ocf/resource.d/heartbeat/SAPInstance
   </code></pre>

   输出应类似于

   <pre><code>&lt;parameter name="IS_ERS" unique="0" required="0"&gt;
   </code></pre>

   如果 grep 命令未找到 IS_ERS 参数，则需要安装 [SUSE 下载页](https://download.suse.com/patch/finder/#bu=suse&familyId=&productId=&dateRange=&startDate=&endDate=&priority=&architecture=&keywords=resource-agents)上列出的修补程序

   <pre><code># example for patch for SLES 12 SP1
   sudo zypper in -t patch SUSE-SLE-HA-12-SP1-2017-885=1
   # example for patch for SLES 12 SP2
   sudo zypper in -t patch SUSE-SLE-HA-12-SP2-2017-886=1
   </code></pre>

3. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名

   <pre><code>sudo vi /etc/hosts
   </code></pre>

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名   

   <pre><code>
   # IP address of cluster node 1
   <b>10.1.1.18    anftstsapcl1</b>
   # IP address of cluster node 2
   <b>10.1.1.6     anftstsapcl2</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
   <b>10.1.1.20    anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP Netweaver ERS
   <b>10.1.1.21    anftstsapers</b>
   </code></pre>

## <a name="prepare-for-sap-netweaver-installation"></a>准备 SAP Netweaver 安装

1. [A] 创建共享目录

   <pre><code>sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/<b>QAS</b>/SYS
   sudo mkdir -p /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo mkdir -p /usr/sap/<b>QAS</b>/ERS<b>01</b>
   
   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/<b>QAS</b>/SYS
   sudo chattr +i /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>

2. **[A]** 配置 `autofs`

   <pre><code>
   sudo vi /etc/auto.master
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   如果使用 NFSv3，请使用以下内容创建文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=3,nobind,sync 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   如果使用 NFSv 4.1，请使用以下内容创建文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.4:/trans
   /usr/sap/<b>QAS</b>/SYS -nfsvers=4.1,nobind,sync,sec=sys 10.1.0.5:/usrsap<b>qas</b>sys
   </code></pre>
   
   > [!NOTE]
   > 装载卷时，请确保匹配 Azure NetApp 文件卷的 NFS 协议版本。 如果将 Azure NetApp 文件卷创建为 NFSv3 卷，请使用相应的 NFSv3 配置。 如果将 Azure NetApp 文件卷创建为 NFSv 4.1 卷，请按照说明禁用 ID 映射，并确保使用相应的 NFSv 4.1 配置。 在此示例中，Azure NetApp 文件卷创建为 NFSv3 卷。  
   
   重新启动 `autofs` 以装载新共享
    <pre><code>
      sudo systemctl enable autofs
      sudo service autofs restart
     </code></pre>

3. [A] 配置交换文件

   <pre><code>sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新启动代理以激活更改

   <pre><code>sudo service waagent restart
   </code></pre>

### <a name="installing-sap-netweaver-ascsers"></a>安装 SAP NetWeaver ASCS/ERS

1. **[1]** 为 ASCS 实例创建虚拟 IP 资源和运行状况探测

   > [!IMPORTANT]
   > 最新的测试，其中，netcat 停止响应由于积压工作（backlog）和仅处理一个连接的请求而导致的请求。 Netcat 资源停止侦听 Azure 负载均衡器请求，并且浮动 IP 变为不可用。  
   > 对于现有的 Pacemaker 群集，建议按照[Azure 负载平衡器检测强化](https://www.suse.com/support/kb/doc/?id=7024128)中的说明将 netcat 替换为 socat。 请注意，更改将需要短暂的停机时间。  

   <pre><code>sudo crm node standby <b>anftstsapcl2</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ASCS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>' directory='/usr/sap/<b>QAS</b>/ASCS<b>00</b>' fstype='nfs' options='sec=sys,vers=4.1' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ASCS IPaddr2 \
     params ip=<b>10.1.1.20</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ASCS anything \
     params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:620<b>00</b>,backlog=10,fork,reuseaddr /dev/null" \
     op monitor timeout=20s interval=10 depth=0
   
   sudo crm configure group g-<b>QAS</b>_ASCS fs_<b>QAS</b>_ASCS nc_<b>QAS</b>_ASCS vip_<b>QAS</b>_ASCS \
      meta resource-stickiness=3000
   </code></pre>

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   <pre><code>sudo crm_mon -r
   
   # Node anftstsapcl2: standby
   # <b>Online: [ anftstsapcl1 ]</b>
   # 
   # Full list of resources:
   #
   # Resource Group: g-QAS_ASCS
   #     fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #     nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #     vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   </code></pre>
  
2. [1] 安装 SAP NetWeaver ASCS  

   在第一个节点上使用映射到 ASCS 的负载均衡器前端配置的 IP 地址的虚拟主机名（例如， <b>anftstsapvh</b>、 <b>10.1.1.20</b>和用于探测负载平衡器的实例编号，如<b>00</b>），将 SAP NetWeaver ASCS 作为 root 用户安装在第一个节点上。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 你可以使用参数 SAPINST_USE_HOSTNAME 使用虚拟主机名安装 SAP。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   如果安装无法在/usr/sap/**QAS**/ASCS**00**中创建子文件夹，请尝试设置 ASCS**00**文件夹的所有者和组，然后重试。 

   <pre><code>
   chown <b>qas</b>adm /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ASCS<b>00</b>
   </code></pre>

3. **[1]** 为 ERS 实例创建虚拟 IP 资源和运行状况探测

   <pre><code>
   sudo crm node online <b>anftstsapcl2</b>
   sudo crm node standby <b>anftstsapcl1</b>
   # If using NFSv3
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' \
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   # If using NFSv4.1
   sudo crm configure primitive fs_<b>QAS</b>_ERS Filesystem device='<b>10.1.0.4</b>:/usrsap<b>qas</b>ers' directory='/usr/sap/<b>QAS</b>/ERS<b>01</b>' fstype='nfs' options='sec=sys,vers=4.1'\
     op start timeout=60s interval=0 \
     op stop timeout=60s interval=0 \
     op monitor interval=20s timeout=40s
   
   sudo crm configure primitive vip_<b>QAS</b>_ERS IPaddr2 \
     params ip=<b>10.1.1.21</b> cidr_netmask=<b>24</b> \
     op monitor interval=10 timeout=20
   
   sudo crm configure primitive nc_<b>QAS</b>_ERS anything \
    params binfile="/usr/bin/socat" cmdline_options="-U TCP-LISTEN:621<b>01</b>,backlog=10,fork,reuseaddr /dev/null" \
    op monitor timeout=20s interval=10 depth=0
   
   # WARNING: Resources nc_QAS_ASCS,nc_QAS_ERS violate uniqueness for parameter "binfile": "/usr/bin/socat"
   # Do you still want to commit (y/n)? y
   
   sudo crm configure group g-<b>QAS</b>_ERS fs_<b>QAS</b>_ERS nc_<b>QAS</b>_ERS vip_<b>QAS</b>_ERS
   </code></pre>

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   <pre><code>sudo crm_mon -r
   
   # Node <b>anftstsapcl1: standby</b>
   # <b>Online: [ anftstsapcl2 ]</b>
   # 
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS  (ocf::heartbeat:IPaddr2):     <b>Started anftstsapcl2</b>
   </code></pre>

4. [2] 安装 SAP Netweaver ERS

   在第二个节点上使用映射到 ERS 的负载均衡器前端配置的 IP 地址的虚拟主机名（例如， <b>anftstsapers</b>、 <b>10.1.1.21</b>和用于探测负载均衡器的实例号，例如<b>01</b>），以 root 身份安装 SAP NetWeaver ERS。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。 你可以使用参数 SAPINST_USE_HOSTNAME 使用虚拟主机名安装 SAP。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b> SAPINST_USE_HOSTNAME=<b>virtual_hostname</b>
   </code></pre>

   > [!NOTE]
   > 使用 SWPM SP 20 PL 05 或更高版本。 较低版本不会正确设置权限，安装将失败。

   如果安装无法在/usr/sap/**QAS**/ERS**01**中创建子文件夹，请尝试设置 ERS**01**文件夹的所有者和组，然后重试。

   <pre><code>
   chown qasadm /usr/sap/<b>QAS</b>/ERS<b>01</b>
   chgrp sapsys /usr/sap/<b>QAS</b>/ERS<b>01</b>
   </code></pre>


5. [1] 调整 ASCS/SCS 和 ERS 实例配置文件
 
   * ASCS/SCS 配置文件

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_<b>ASCS00</b>_<b>anftstsapvh</b>
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   </code></pre>

   * ERS 配置文件

   <pre><code>
   sudo vi /sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # Add the following lines
   service/halib = $(DIR_CT_RUN)/saphascriptco.so
   service/halib_cluster_connector = /usr/bin/sap_suse_cluster_connector
   
   # remove Autostart from ERS profile
   # Autostart = 1
   </code></pre>

6. [A] 配置 Keep Alive

   SAP NetWeaver 应用程序服务器和 ASCS/SCS 之间的通信是通过软件负载均衡器进行路由的。 负载均衡器在可配置的超时之后将断开非活动连接。 为了防止出现此情况，需要在 SAP NetWeaver ASCS/SCS 配置文件中设置一个参数，并更改 Linux 系统设置。 有关详细信息，[请参阅 SAP 说明 1410736][1410736] 。

   在上一步中已添加了 ASCS/SCS 配置文件参数 enque/encni/set_so_keepalive。

   <pre><code>
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   </code></pre>

7. [A] 在安装后配置 SAP 用户

   <pre><code>
   # Add sidadm to the haclient group
   sudo usermod -aG haclient <b>qas</b>adm
   </code></pre>

8. **[1]** 将 ASCS 和 ERS SAP 服务添加到 `sapservice` 文件

   将 ASCS 服务入口添加到第二个节点，并将 ERS 服务入口复制到第一个节点。

   <pre><code>
   cat /usr/sap/sapservices | grep ASCS<b>00</b> | sudo ssh <b>anftstsapcl2</b> "cat >>/usr/sap/sapservices"
   sudo ssh <b>anftstsapcl2</b> "cat /usr/sap/sapservices" | grep ERS<b>01</b> | sudo tee -a /usr/sap/sapservices
   </code></pre>

9. [1] 创建 SAP 群集资源

如果使用排入服务器1体系结构（ENSA1），请按如下所示定义资源：

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 failure-timeout=60 migration-threshold=1 priority=10
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true \
    meta priority=1000
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure location loc_sap_<b>QAS</b>_failover_to_ers rsc_sap_<b>QAS</b>_ASCS<b>00</b> rule 2000: runs_ers_<b>QAS</b> eq 1
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   SAP 在 SAP NW 7.52 中引入了对排队服务器2（包括复制）的支持。 从 ABAP 平台1809开始，默认情况下会安装排队服务器2。 有关排队服务器2支持，请参阅 SAP 说明[2630416](https://launchpad.support.sap.com/#/notes/2630416) 。
如果使用排队服务器2体系结构（[ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)），请按如下所示定义资源：

   <pre><code>sudo crm configure property maintenance-mode="true"
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ASCS<b>00</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ASCS<b>00</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ASCS<b>00</b>_<b>anftstsapvh</b>" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000
   
   sudo crm configure primitive rsc_sap_<b>QAS</b>_ERS<b>01</b> SAPInstance \
    operations \$id=rsc_sap_<b>QAS</b>_ERS<b>01</b>-operations \
    op monitor interval=11 timeout=60 on_fail=restart \
    params InstanceName=<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b> START_PROFILE="/sapmnt/<b>QAS</b>/profile/<b>QAS</b>_ERS<b>01</b>_<b>anftstsapers</b>" AUTOMATIC_RECOVER=false IS_ERS=true
   
   sudo crm configure modgroup g-<b>QAS</b>_ASCS add rsc_sap_<b>QAS</b>_ASCS<b>00</b>
   sudo crm configure modgroup g-<b>QAS</b>_ERS add rsc_sap_<b>QAS</b>_ERS<b>01</b>
   
   sudo crm configure colocation col_sap_<b>QAS</b>_no_both -5000: g-<b>QAS</b>_ERS g-<b>QAS</b>_ASCS
   sudo crm configure order ord_sap_<b>QAS</b>_first_start_ascs Optional: rsc_sap_<b>QAS</b>_ASCS<b>00</b>:start rsc_sap_<b>QAS</b>_ERS<b>01</b>:stop symmetrical=false
   
   sudo crm node online <b>anftstsapcl1</b>
   sudo crm configure property maintenance-mode="false"
   </code></pre>

   如果从较旧版本升级并切换到排队服务器2，请参阅 SAP 说明[2641019](https://launchpad.support.sap.com/#/notes/2641019)。 

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   <pre><code>sudo crm_mon -r
   # Full list of resources:
   #
   # stonith-sbd     (stonith:external/sbd): <b>Started anftstsapcl2</b>
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl1</b>
   #      nc_QAS_ASCS        (ocf::heartbeat:anything):      <b>Started anftstsapcl1</b>
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl1</b>
   #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl1</b>
   #  Resource Group: g-QAS_ERS
   #      fs_QAS_ERS (ocf::heartbeat:Filesystem):    <b>Started anftstsapcl2</b>
   #      nc_QAS_ERS (ocf::heartbeat:anything):      <b>Started anftstsapcl2</b>
   #      vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       <b>Started anftstsapcl2</b>
   #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   <b>Started anftstsapcl2</b>
   </code></pre>

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver 应用程序服务器准备 

某些数据库要求在应用程序服务器上执行数据库实例安装。 请准备好应用程序服务器虚拟机，使之能够在这些场合下使用。

以下步骤假定在与 ASCS/SCS 和 HANA 服务器不同的服务器上安装应用程序服务器。 否则，则无需进行以下某些步骤（如配置主机名解析）。

以下各项带有前缀 **[A]** -适用于 PAS 和 .aas， **[P]** -仅适用于 pas 或 **[S]** -仅适用于 .aas。


1. **[A]** 配置操作系统

   减小脏缓存的大小。 有关详细信息，请参阅 [RAM 较大的 SLES 11/12 服务器的写入性能低](https://www.suse.com/support/kb/doc/?id=7010287)。

   <pre><code>
   sudo vi /etc/sysctl.conf
   # Change/set the following settings
   vm.dirty_bytes = 629145600
   vm.dirty_background_bytes = 314572800
   </code></pre>

1. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名

   ```bash
   sudo vi /etc/hosts
   ```

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名

   <pre><code>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS/SCS
   <b>10.1.1.20 anftstsapvh</b>
   # IP address of the load balancer frontend configuration for SAP NetWeaver ERS
   <b>10.1.1.21 anftstsapers</b>
   # IP address of all application servers
   <b>10.1.1.15 anftstsapa01</b>
   <b>10.1.1.16 anftstsapa02</b>
   </code></pre>

1. **[A]** 创建 sapmnt 目录

   <pre><code>
   sudo mkdir -p /sapmnt/<b>QAS</b>
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/<b>QAS</b>
   sudo chattr +i /usr/sap/trans
   </code></pre>

1. **[P]** 创建 PAS 目录

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>02</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>02</b>
   </code></pre>

1. **[S]** 创建 .aas 目录

   <pre><code>
   sudo mkdir -p /usr/sap/<b>QAS</b>/D<b>03</b>
   sudo chattr +i /usr/sap/<b>QAS</b>/D<b>03</b>
   </code></pre>

1. **[P]** 在 PAS 上配置 `autofs`

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   如果使用 NFSv3，请使用以下内容创建一个新文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=3,nobind,sync <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   如果使用 NFSv 4.1，请使用以下内容创建一个新文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>02</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.5</b>:/usrsap<b>qas</b>pas
   </code></pre>

   重新启动 `autofs` 以装载新共享

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. **[P]** 在 .Aas 上配置 `autofs`

   <pre><code>sudo vi /etc/auto.master
   
   # Add the following line to the file, save and exit
   /- /etc/auto.direct
   </code></pre>

   如果使用 NFSv3，请使用以下内容创建一个新文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=3,nobind,sync <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   如果使用 NFSv 4.1，请使用以下内容创建一个新文件：

   <pre><code>
   sudo vi /etc/auto.direct
   # Add the following lines to the file, save and exit
   /sapmnt/<b>QAS</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/sapmnt<b>qas</b>
   /usr/sap/trans -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/trans
   /usr/sap/<b>QAS</b>/D<b>03</b> -nfsvers=4.1,nobind,sync,sec=sys <b>10.1.0.4</b>:/usrsap<b>qas</b>aas
   </code></pre>

   重新启动 `autofs` 以装载新共享

   <pre><code>
   sudo systemctl enable autofs
   sudo service autofs restart
   </code></pre>

1. [A] 配置交换文件

   <pre><code>
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=<b>y</b>
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=<b>2000</b>
   </code></pre>

   重新启动代理以激活更改

   <pre><code>sudo service waagent restart
   </code></pre>

## <a name="install-database"></a>安装数据库

在此示例中，SAP NetWeaver 安装在 SAP HANA 上。 可以使用每个受支持的数据库完成此安装。 有关如何在 Azure 中安装 SAP HANA 的详细信息，请参阅[Azure 虚拟机（vm）上 SAP HANA 的高可用性][sap-hana-ha]。 有关支持的数据库的列表，请参阅[SAP 说明 1928533][1928533]。

* 运行 SAP 数据库实例安装

   使用映射到数据库的负载均衡器前端配置的 IP 地址的虚拟主机名将 SAP NetWeaver 数据库实例作为根安装。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 应用程序服务器安装

请按照这些步骤安装 SAP 应用程序服务器。

1. **[A]** 准备应用程序服务器按照上述 " [SAP NetWeaver 应用程序服务器准备](high-availability-guide-suse-netapp-files.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)" 一章中的步骤来准备应用程序服务器。

2. **[A]** 安装 sap NetWeaver 应用程序服务器安装主要或其他 SAP NetWeaver 应用程序服务器。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   <pre><code>sudo &lt;swpm&gt;/sapinst SAPINST_REMOTE_ACCESS_USER=<b>sapadmin</b>
   </code></pre>

3. **[A]** 更新 SAP HANA 安全存储区

   更新 SAP HANA 安全存储以指向 SAP HANA 系统复制设置的虚拟名称。

   运行以下命令列出条目
   <pre><code>
   hdbuserstore List
   </code></pre>

   此命令应会列出如下所示的所有条目
   <pre><code>
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 10.1.1.5:<b>30313</b>
     USER: <b>SAPABAP1</b>
     DATABASE: <b>QAS</b>
   </code></pre>

   输出显示，默认条目的 IP 地址正在指向虚拟机而不是负载均衡器的 IP 地址。 需将此条目更改为指向负载均衡器的虚拟主机名。 请确保使用相同的端口（在上面的输出中为**30313** ）和数据库名称（在上面的输出中为**QAS** ）！

   <pre><code>
   su - <b>qas</b>adm
   hdbuserstore SET DEFAULT <b>qasdb:30313@QAS</b> <b>SAPABAP1</b> <b>&lt;password of ABAP schema&gt;</b>
   </code></pre>

## <a name="test-the-cluster-setup"></a>测试群集设

以下测试是[SUSE 的最佳实践指南][suse-ha-guide]中的测试用例的副本。 为方便起见，已复制于下方。 此外，请务必阅读最佳做法指南，并执行可能已经添加的所有其他测试。

1. Test HAGetFailoverConfig、HACheckConfig 和 HACheckFailoverConfig

   在当前运行 ASCS 实例的节点上运行以下命令作为 \<sapsid>adm。 如果命令失败，显示“失败: 内存不足”消息，则原因可能是主机名中存在短划线。 这是一个已知问题，将由 SUSE 在 sap-suse-cluster-connector 包中进行修复。

   <pre><code>
   anftstsapcl1:qasadm 52> sapcontrol -nr 00 -function HAGetFailoverConfig
   07.03.2019 20:08:59
   HAGetFailoverConfig
   OK
   HAActive: TRUE
   HAProductVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3
   HASAPInterfaceVersion: SUSE Linux Enterprise Server for SAP Applications 12 SP3 (sap_suse_cluster_connector 3.1.0)
   HADocumentation: https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
   HAActiveNode: anftstsapcl1
   HANodes: anftstsapcl1, anftstsapcl2

   anftstsapcl1:qasadm 54> sapcontrol -nr 00 -function HACheckConfig
   07.03.2019 23:28:29
   HACheckConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, Redundant ABAP instance configuration, 2 ABAP instances detected
   SUCCESS, SAP CONFIGURATION, Redundant Java instance configuration, 0 Java instances detected
   SUCCESS, SAP CONFIGURATION, Enqueue separation, All Enqueue server separated from application server
   SUCCESS, SAP CONFIGURATION, MessageServer separation, All MessageServer separated from application server
   SUCCESS, SAP CONFIGURATION, ABAP instances on multiple hosts, ABAP instances on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP SPOOL service configuration, 2 ABAP instances with SPOOL service detected
   SUCCESS, SAP STATE, Redundant ABAP SPOOL service state, 2 ABAP instances with active SPOOL service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP SPOOL service on multiple hosts, ABAP instances with active ABAP SPOOL service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP BATCH service configuration, 2 ABAP instances with BATCH service detected
   SUCCESS, SAP STATE, Redundant ABAP BATCH service state, 2 ABAP instances with active BATCH service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP BATCH service on multiple hosts, ABAP instances with active ABAP BATCH service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP DIALOG service configuration, 2 ABAP instances with DIALOG service detected
   SUCCESS, SAP STATE, Redundant ABAP DIALOG service state, 2 ABAP instances with active DIALOG service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP DIALOG service on multiple hosts, ABAP instances with active ABAP DIALOG service on multiple hosts detected
   SUCCESS, SAP CONFIGURATION, Redundant ABAP UPDATE service configuration, 2 ABAP instances with UPDATE service detected
   SUCCESS, SAP STATE, Redundant ABAP UPDATE service state, 2 ABAP instances with active UPDATE service detected
   SUCCESS, SAP STATE, ABAP instances with ABAP UPDATE service on multiple hosts, ABAP instances with active ABAP UPDATE service on multiple hosts detected
   SUCCESS, SAP STATE, SCS instance running, SCS instance status ok
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version (anftstsapvh_QAS_00), SAPInstance includes is-ers patch
   SUCCESS, SAP CONFIGURATION, Enqueue replication (anftstsapvh_QAS_00), Enqueue replication enabled
   SUCCESS, SAP STATE, Enqueue replication state (anftstsapvh_QAS_00), Enqueue replication active
   
   anftstsapcl1:qasadm 55> sapcontrol -nr 00 -function HACheckFailoverConfig
   07.03.2019 23:30:48
   HACheckFailoverConfig
   OK
   state, category, description, comment
   SUCCESS, SAP CONFIGURATION, SAPInstance RA sufficient version, SAPInstance includes is-ers patch
   </code></pre>

2. 手动迁移 ASCS 实例

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rscsap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Starting anftstsapcl1
   </code></pre>

   运行以下命令作为根，迁移 ASCS 实例。

   <pre><code>
   anftstsapcl1:~ # crm resource migrate rsc_sap_QAS_ASCS00 force
   INFO: Move constraint created for rsc_sap_QAS_ASCS00
   
   anftstsapcl1:~ # crm resource unmigrate rsc_sap_QAS_ASCS00
   INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

3. 测试 HAFailoverToNode

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   运行以下命令作为 \<sapsid>adm，迁移 ASCS 实例。

   <pre><code>
   anftstsapcl1:qasadm 53> sapcontrol -nr 00 -host anftstsapvh -user <b>qas</b>adm &lt;password&gt; -function HAFailoverToNode ""
   
   # run as root
   # Remove failed actions for the ERS that occurred as part of the migration
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   # Remove migration constraints
   anftstsapcl1:~ # crm resource clear rsc_sap_QAS_ASCS00
   #INFO: Removed migration constraints for rsc_sap_QAS_ASCS00
   </code></pre>

   测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

4. 模拟节点故障 

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   在其中运行 ASCS 实例的节点上运行以下命令作为根

   <pre><code>anftstsapcl2:~ # echo b > /proc/sysrq-trigger
   </code></pre>

   如果使用 SBD，则 Pacemaker 不应在已终止的节点上自动启动。 节点再次启动后的状态应类似如下所示。

   <pre><code>Online:
   Online: [ anftstsapcl1 ]
   OFFLINE: [ anftstsapcl2 ]

   Full list of resources:

    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1

   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=166, status=complete, exitreason='',
    last-rc-change='Fri Mar  8 18:26:10 2019', queued=0ms, exec=0ms
   </code></pre>

   运行以下命令在已终止的节点上启动 Pacemaker，并清理 SBD 消息和已失败的资源。

   <pre><code>
   # run as root
   # list the SBD device(s)
   anftstsapcl2:~ # cat /etc/sysconfig/sbd | grep SBD_DEVICE=
   # SBD_DEVICE="/dev/disk/by-id/scsi-36001405b730e31e7d5a4516a2a697dcf;/dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e;/dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a"

   anftstsapcl2:~ # sbd -d /dev/disk/by-id/scsi-36001405772fe8401e6240c985857e11 -d /dev/disk/by-id/scsi-36001405f69d7ed91ef54461a442c676e -d /dev/disk/by-id/scsi-360014058e5f335f2567488882f3a2c3a message anftstsapcl2 clear

   anftstsapcl2:~ # systemctl start pacemaker
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   测试之后的资源状态：

   <pre><code>
   Full list of resources:
   
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

5. 测试 ASCS 实例的手动重启

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   通过编辑事务 su01 中的用户等方式创建一个排队锁。 在运行 ASCS 实例的节点上，以 < sapsid 的方式运行以下命令\>adm。 这些命令将停止 ASCS 实例并重新启动该实例。 如果使用排队服务器1体系结构，则应在此测试中丢失排队锁。 如果使用排队服务器2体系结构，则将保留排队。 

   <pre><code>anftstsapcl2:qasadm 51> sapcontrol -nr 00 -function StopWait 600 2
   </code></pre>

   ASCS 实例在 Pacemaker 中现在应为禁用状态

   <pre><code>  rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Stopped (disabled)
   </code></pre>

   在同一节点上再次启动 ASCS 实例。

   <pre><code>anftstsapcl2:qasadm 52> sapcontrol -nr 00 -function StartWait 600 2
   </code></pre>

   如果使用排队服务器复制1体系结构，并且应重置后端，则应丢失 transaction su01 的排队锁。 测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

6. 终止消息服务器进程

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   运行以下命令作为根，确定消息服务器的进程并将其终止。

   <pre><code>anftstsapcl2:~ # pgrep ms.sapQAS | xargs kill -9
   </code></pre>

   如果仅终止消息服务器一次，则 `sapstart`会重新启动该服务器。 如果经常终止消息服务器，Pacemaker 会最终将 ASCS 实例移动到另一个节点。 运行以下命令作为根，清除测试后的 ASCS 和 ERS 实例的资源状态。

   <pre><code>
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl2:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

7. 终止排队服务器进程

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   </code></pre>

   在运行 ASCS 实例的节点上，运行以下命令作为根，以终止排队服务器。

   <pre><code>anftstsapcl1:~ # pgrep en.sapQAS | xargs kill -9
   </code></pre>

   ASCS 实例应会立即故障转移到另一个节点。 ASCS 实例启动后，ERS 实例也会进行故障转移。 运行以下命令作为根，清除测试后的 ASCS 和 ERS 实例的资源状态。

   <pre><code>
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ASCS00
   anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

8. 终止排队复制服务器进程

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   在运行 ERS 实例的节点上，运行以下命令作为根，以终止排队复制服务器。

   <pre><code>anftstsapcl1:~ # pgrep er.sapQAS | xargs kill -9
   </code></pre>

   如果只运行一次命令，`sapstart` 将重新启动此过程。 如果经常运行它，`sapstart` 将不会重新启动进程，并且资源将处于停止状态。 运行以下命令作为根，清除测试后的 ERS 实例的资源状态。

   <pre><code>anftstsapcl1:~ # crm resource cleanup rsc_sap_QAS_ERS01
   </code></pre>

   测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

9. 终止排队 sapstartsrv 进程

   开始测试之前的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

   在运行 ASCS 的节点上运行以下命令作为根。

   <pre><code>
   anftstsapcl2:~ # pgrep -fl ASCS00.*sapstartsrv
   #67625 sapstartsrv
   
   anftstsapcl2:~ # kill -9 67625
   </code></pre>

   Pacemaker 资源代理应会始终重启 sapstartsrv 进程。 测试之后的资源状态：

   <pre><code>
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:anything):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   stonith-sbd     (stonith:external/sbd): Started anftstsapcl1
    Resource Group: g-QAS_ERS
        fs_QAS_ERS (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ERS (ocf::heartbeat:anything):      Started anftstsapcl1
        vip_QAS_ERS        (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   </code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 了解如何建立高可用性并规划 SAP 灾难恢复 
* Azure 上的 HANA （大型实例），请参阅[azure 上的 SAP HANA （大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅[Azure 虚拟机（vm）上的 SAP HANA 的高可用性][sap-hana-ha]
