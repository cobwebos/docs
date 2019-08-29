---
title: Azure 虚拟机上的 azure 虚拟机高可用性 Azure NetApp 文件 Red Hat Enterprise Linux 上的 SAP NetWeaver |Microsoft Docs
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
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: d3fbd38484696f0b133e7494fed11a22dc038148
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101104"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>适用于 sap NetWeaver 的 azure 虚拟机高可用性, 适用于 SAP 应用程序的 Azure NetApp 文件 Red Hat Enterprise Linux

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

本文介绍如何使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)部署虚拟机、配置虚拟机、安装群集框架, 以及安装高可用性 SAP NetWeaver 7.50 系统。
在示例配置和安装命令等内容中，ASCS 实例为 number 00, ERS 实例为数字 01, 主应用程序实例 (PAS) 为 02, 应用程序实例 (.AAS) 为03。 使用 SAP 系统 ID QAS。 

本文不详细介绍数据库层。  

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533]，其中包含：
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本

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
  * [在 RHEL 上的 Pacemaker 中, 通过独立的排队服务器 2 (ENSA2) 配置 SAP S/4HANA ASCS/ERS](https://access.redhat.com/articles/3974941)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
* [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

SAP Netweaver central services 的高可用性 (HA) 需要共享存储。
若要在 Red Hat Linux 上实现此目的, 需要构建单独的高可用性 GlusterFS 群集。 

现在, 可以使用共享存储实现 SAP Netweaver HA, 并将其部署在 Azure NetApp 文件中。 使用 Azure NetApp 文件作为共享存储, 无需额外的[GlusterFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)。 SAP Netweaver central services (ASCS/SCS) 的 HA 仍需要 Pacemaker。

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP HANA 数据库使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示了负载均衡器的配置, 其中包含 (A) SCS 和 ERS 的单独前端 Ip。

> [!IMPORTANT]
> **不支持**将适用于 Red Hat LINUX 的 SAP ASCS/ERS 的多 SID 群集作为 Azure vm 中的来宾操作系统。 多 SID 群集介绍了如何在一个 Pacemaker 群集中安装具有不同 Sid 的多个 SAP ASCS/ERS 实例。

### <a name="ascs"></a>(A)SCS

* 前端配置
  * IP 地址192.168.14。9
* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口
* 探测端口
  * 端口 620&lt;nr&gt;
* 负载均衡规则
  * 32&lt;nr&gt; TCP
  * 36&lt;nr&gt; TCP
  * 39&lt;nr&gt; TCP
  * 81&lt;nr&gt; TCP
  * 5&lt;nr&gt;13 TCP
  * 5&lt;nr&gt;14 TCP
  * 5&lt;nr&gt;16 TCP

### <a name="ers"></a>ERS

* 前端配置
  * IP 地址192.168.14.10
* 后端配置
  * 连接到所有虚拟机（这些虚拟机应为 (A)SCS/ERS 群集的一部分）的主网络接口
* 探测端口
  * 端口 621&lt;nr&gt;
* 负载均衡规则
  * 32&lt;nr&gt; TCP
  * 33&lt;nr&gt; TCP
  * 5&lt;nr&gt;13 TCP
  * 5&lt;nr&gt;14 TCP
  * 5&lt;nr&gt;16 TCP

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

SAP NetWeaver 要求对传输和配置文件目录使用共享存储。  在继续安装 Azure NetApp 文件基础结构之前, 请先熟悉[Azure Netapp 文件文档][anf-azure-doc]。 检查所选的 Azure 区域是否提供 Azure NetApp 文件。 以下链接显示 Azure 的 azure NetApp 文件可用性:Azure [NetApp 文件按 Azure 区域可用性][anf-avail-matrix]。

Azure NetApp 文件在多个[azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 在部署 Azure NetApp 文件之前, 请在[注册 azure][anf-register]netapp 文件说明后, 请求加入 Azure netapp 文件。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

这些步骤假定你已部署[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 文件资源和将在其中装入 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。  

1. 如果尚未执行此操作, 请请求[加入 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  
2. 按照[创建 Netapp 帐户的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account), 在所选的 Azure 区域中创建 netapp 帐户。  
3. 按照[如何设置 Azure Netapp 文件容量池的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)设置 Azure netapp 文件容量池。  
本文中介绍的 SAP Netweaver 体系结构使用了单个 Azure NetApp 文件容量池、高级 SKU。 对于 Azure 上的 SAP Netweaver 应用程序工作负载, 我们建议 Azure NetApp 文件高级 SKU。  
4. 按照[说明将子网委托给 Azure Netapp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中所述, 将子网委托给 azure netapp 文件。  

5. 按照[说明为 Azure Netapp 文件创建卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes), 部署 Azure netapp 文件卷。 在指定的 Azure NetApp 文件[子网](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 请记住, Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中, 或者位于对等互连 Azure 虚拟网络中。 在此示例中, 我们使用两个 Azure NetApp 文件卷: sap<b>QAS</b>和 transSAP。 装载到相应装入点的文件路径为/usrsap<b>qas</b>/sapmnt<b>qas</b>、/usrsap<b>qas</b>/usrsap<b>qas</b>sys 等。  

   1. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/sapmnt<b>QAS</b>)
   2. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ascs)
   3. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>sys)
   4. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>ers)
   5. volume transSAP (nfs://192.168.24.4/transSAP)
   6. volume sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>pas)
   7. 批量 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>QAS</b>/usrsap<b>QAS</b>.aas)
  
在此示例中, 我们使用了适用于所有 SAP Netweaver 文件系统的 Azure NetApp 文件来演示如何使用 Azure NetApp 文件。 不需要通过 NFS 装载的 SAP 文件系统也可以部署为[Azure 磁盘存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。 在此示例中, <b>-e</b>必须位于 Azure NetApp 文件上, 并且<b>f-g</b> (即/usr/sap/<b>QAS</b>/d<b>02</b>,/Usr/sap/<b>QAS</b>/d<b>03</b>) 可以部署为 azure 磁盘存储。 

### <a name="important-considerations"></a>重要注意事项

考虑在 SUSE 高可用性体系结构上用于 SAP Netweaver 的 Azure NetApp 文件时, 请注意以下重要事项:

- 最小容量池为 4 TiB。 容量池大小必须为 4 TiB 的倍数。
- 最小卷为 100 GiB
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机都必须位于同一个 Azure 虚拟网络中, 或位于同一区域中的[对等互连虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。 现在支持通过同一区域中的 VNET 对等互连进行 Azure NetApp 文件访问。 目前尚不支持通过全局对等互连进行 Azure NetApp 访问。
- 所选虚拟网络必须具有子网, 并委托给 Azure NetApp 文件。
- Azure NetApp 文件目前仅支持 NFSv3 
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy): 可控制允许的客户端、访问类型 (读取 & 写入、只读等)。 
- Azure NetApp 文件功能尚不能识别区域。 目前, azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域中潜在的延迟影响。 

## <a name="setting-up-ascs"></a>设置 (A)SCS

在此示例中, 资源是通过[Azure 门户](https://portal.azure.com/#home)手动部署的。

### <a name="deploy-linux-manually-via-azure-portal"></a>通过 Azure 门户手动部署 Linux

首先, 需要创建 Azure NetApp 文件卷。 部署 Vm。 之后，创建一个负载均衡器并使用后端池中的虚拟机。

1. 创建负载均衡器（内部）  
   1. 创建前端 IP 地址
      1. ASCS 的 IP 地址192.168.14。9
         1. 打开负载均衡器，选择前端 IP 池，并单击“添加”
         1. 输入新前端 IP 池的名称 (例如 "前端" **。QAS.ASCS**)
         1. 将 "分配" 设置为 "静态" 并输入 IP 地址 (例如**192.168.14.9**)
         1. 单击“确定”
      1. ASCS ERS 的 IP 地址192.168.14.10
         * 重复上面的 "a" 中的步骤, 创建 ERS 的 IP 地址 (例如**192.168.14.10**和**前端。QAS.ERS**)
   1. 创建后端池
      1. 为 ASCS 创建后端池
         1. 打开负载均衡器，单击后端池，并单击“添加”
         1. 输入新后端池的名称 (例如**后端。QAS**)
         1. 单击“添加虚拟机”。
         1. 选择之前为 ASCS 创建的可用性集 
         1. 选择 (A)SCS 群集的虚拟机
         1. 单击“确定”
   1. 创建运行状况探测
      1. ASCS 的端口 620**00**
         1. 打开负载均衡器，选择运行状况探测，并单击“添加”
         1. 输入新运行状况探测的名称 (例如**运行状况)。QAS.ASCS**)
         1. 选择 TCP 作为协议，选择端口 620**00**，将“间隔”保留为 5，将“不正常阈值”保留为 2
         1. 单击“确定”
      1. ASCS ERS 的端口 621**01**
            * 重复上述 "c" 下的步骤, 为 ERS 创建运行状况探测 (例如 621**01**和**health。QAS.ERS**)
   1. 负载均衡规则
      1. ASCS 的 32**00** TCP
         1. 打开负载均衡器, 选择 "负载均衡规则", 然后单击 "添加"
         1. 输入新负载均衡器规则的名称 (例如**lb。QAS.ASCS**)
         1. 选择之前创建的 ASCS、后端池和运行状况探测的前端 IP 地址 (例如 "**前端"。QAS.ASCS**)
         1. 将协议保留为“TCP”，输入端口 **3200**
         1. 将空闲超时增大到 30 分钟
         1. **确保启用浮动 IP**
         1. 单击“确定”
      1. ASCS 的其他端口
         * 对于端口 36**00**、39**00**、81**00**、5**00**13、5**00**14、5**00**16 和 TCP ASCS
      1. ASCS ERS 的其他端口
         * 对于端口 32**01**、33**01**、5**01**13、5**01**14、5**01**16 和 TCP (适用于 ASCS ERS), 请重复上述步骤。


> [!IMPORTANT]
> 不要在 azure 负载均衡器后面的 Azure Vm 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数**net.tcp _timestamps**设置为**0**。 有关详细信息, 请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 (A)SCS 服务器的基本 Pacemaker。

### <a name="prepare-for-sap-netweaver-installation"></a>准备 SAP Netweaver 安装

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2。

1. [A] 设置主机名称解析

   可以使用 DNS 服务器，或修改所有节点上的 /etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名

    ```
    sudo vi /etc/hosts
    ```

   将以下行插入 /etc/hosts。 根据环境更改 IP 地址和主机名

    ```
    # IP address of cluster node 1
    192.168.14.5    anftstsapcl1
    # IP address of cluster node 2
    192.168.14.6     anftstsapcl2
    # IP address of the load balancer frontend configuration for SAP Netweaver ASCS
    192.168.14.9    anftstsapvh
    # IP address of the load balancer frontend configuration for SAP Netweaver ERS
    192.168.14.10    anftstsapers
    ```

1. **[1]** 在 Azure NetApp 文件卷中创建 SAP 目录。  
   将 Azure NetApp 文件卷暂时装载到其中一个 Vm 上, 并创建 SAP 目录 (文件路径)。  

    ```
     #mount temporarily the volume
     sudo mkdir -p /saptmp
     sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp 192.168.24.5:/sapQAS /saptmp
     # create the SAP directories
     sudo cd /saptmp
     sudo mkdir -p sapmntQAS
     sudo mkdir -p usrsapQASascs
     sudo mkdir -p usrsapQASers
     sudo mkdir -p usrsapQASsys
     sudo mkdir -p usrsapQASpas
     sudo mkdir -p usrsapQASaas
     # unmount the volume and delete the temporary directory
     sudo cd ..
     sudo umount /saptmp
     sudo rmdir /saptmp
    ``` 

1. [A] 创建共享目录

   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans
   sudo mkdir -p /usr/sap/QAS/SYS
   sudo mkdir -p /usr/sap/QAS/ASCS00
   sudo mkdir -p /usr/sap/QAS/ERS01
   
   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   sudo chattr +i /usr/sap/QAS/SYS
   sudo chattr +i /usr/sap/QAS/ASCS00
   sudo chattr +i /usr/sap/QAS/ERS01
   ```

1. **[A]** 安装 NFS 客户端和其他要求

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** 检查 resource-agents-sap 的版本

   请确保已安装的 resource-agents-sap 包的版本至少为 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # Loaded plugins: langpacks, product-id, search-disabled-repos
   # Repodata is over 2 weeks old. Install yum-cron? Or run: yum makecache fast
   # Installed Packages
   # Name        : resource-agents-sap
   # Arch        : x86_64
   # Version     : 3.9.5
   # Release     : 124.el7
   # Size        : 100 k
   # Repo        : installed
   # From repo   : rhel-sap-for-rhel-7-server-rpms
   # Summary     : SAP cluster resource agents and connector script
   # URL         : https://github.com/ClusterLabs/resource-agents
   # License     : GPLv2+
   # Description : The SAP resource agents and connector script interface with
   #          : Pacemaker to allow SAP instances to be managed in a cluster
   #          : environment.
   ```


1. **[A]** 添加装载条目

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
    192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw,hard,rsize=65536,wsize=65536,vers=3
    192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   装载新共享

   ```
   sudo mount -a  
   ```

1. [A] 配置交换文件

   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   重新启动代理以激活更改

   ```
   sudo service waagent restart
   ```

1. **[A]** RHEL 配置

   按照 SAP 说明 [2002167] 中的说明配置 RHEL

### <a name="installing-sap-netweaver-ascsers"></a>安装 SAP NetWeaver ASCS/ERS

1. **[1]** 为 ASCS 实例创建虚拟 IP 资源和运行状况探测

   ```
   sudo pcs node standby anftstsapcl2
   
   sudo pcs resource create fs_QAS_ASCS Filesystem device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pcs resource create vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   ```
   sudo pcs status
   
   # Node anftstsapcl2: standby
   # Online: [ anftstsapcl1 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
   ```

1. [1] 安装 SAP NetWeaver ASCS  

   在第一个节点上使用映射到 ASCS 的负载均衡器前端配置的 IP 地址的虚拟主机名 (例如<b>anftstsapvh</b>、 <b>192.168.14.9</b>和用于的实例编号) 在第一个节点上安装 SAP NetWeaver ASCS探测负载均衡器, 例如<b>00</b>。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安装无法在/usr/sap/**QAS**/ASCS**00**中创建子文件夹, 请尝试设置 ASCS**00**文件夹的所有者和组, 然后重试。

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00
   sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** 为 ERS 实例创建虚拟 IP 资源和运行状况探测

   ```
   sudo pcs node unstandby anftstsapcl2
   sudo pcs node standby anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    --group g-QAS_AERS

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    --group g-QAS_AERS
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    --group g-QAS_AERS
   ```
 
   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

   ```
   sudo pcs status
   
   # Node anftstsapcl1: standby
   # Online: [ anftstsapcl2 ]
   #
   # Full list of resources:
   #
   # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
   #  Resource Group: g-QAS_ASCS
   #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2<
   #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   #  Resource Group: g-QAS_AERS
   #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
   #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
   #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
   ```

1. [2] 安装 SAP Netweaver ERS  

   在第二个节点上使用映射到 ERS 的负载均衡器前端配置的 IP 地址的虚拟主机名 (例如<b>anftstsapers</b>、 <b>192.168.14.10</b>和你用于探测负载均衡器, 例如<b>01</b>。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   ```
   # Allow access to SWPM. This rule is not permanent. If you reboot the machine, you have to run the command again.
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   如果安装无法在/usr/sap/**QAS**/ERS**01**中创建子文件夹, 请尝试设置 ERS**01**文件夹的所有者和组, 然后重试。

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01
   sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. [1] 调整 ASCS/SCS 和 ERS 实例配置文件

   * ASCS/SCS 配置文件

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # Change the restart command to a start command
   #Restart_Program_01 = local $(_EN) pf=$(_PF)
   Start_Program_01 = local $(_EN) pf=$(_PF)
   
   # Add the keep alive parameter
   enque/encni/set_so_keepalive = true
   ```

   * ERS 配置文件

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # Change the restart command to a start command
   #Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # remove Autostart from ERS profile
   # Autostart = 1
   ```


1. [A] 配置 Keep Alive

   SAP NetWeaver 应用程序服务器和 ASCS/SCS 之间的通信是通过软件负载均衡器进行路由的。 负载均衡器在可配置的超时之后将断开非活动连接。 若要防止出现这种情况, 需要在 SAP NetWeaver ASCS/SCS 配置文件中设置参数并更改 Linux 系统设置。 有关详细信息,[请参阅 SAP 说明 1410736][1410736] 。

   在上一步中已添加了 ASCS/SCS 配置文件参数 enque/encni/set_so_keepalive。

   ```
   # Change the Linux system configuration
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** 更新 /usr/sap/sapservices 文件

   要防止 sapinit 启动脚本启动实例，必须从 /usr/sap/sapservices 文件中注释掉由 Pacemaker 管理的所有实例。 如果要将 SAP HANA 实例与 HANA SR 配合使用，请不要将其注释掉。

   ```
   sudo vi /usr/sap/sapservices
   
   # On the node where you installed the ASCS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # On the node where you installed the ERS, comment out the following line
   # LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. [1] 创建 SAP 群集资源

   如果使用排入服务器1体系结构 (ENSA1), 请按如下所示定义资源:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint location rsc_sap_QAS_ASCS00 rule score=2000 runs_ers_QAS eq 1
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
    
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   SAP 在 SAP NW 7.52 中引入了对排队服务器 2 (包括复制) 的支持。 从 ABAP 平台1809开始, 默认情况下会安装排队服务器2。 有关排队服务器2支持, 请参阅 SAP 说明[2630416](https://launchpad.support.sap.com/#/notes/2630416) 。
   如果使用的是排队服务器2体系结构 ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), 请安装 resource agent resource-agents-sap-4.1.1 或更高版本, 并按如下所示定义资源:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pcs resource create rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER=false \
    meta resource-stickiness=5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER=false IS_ERS=true \
    --group g-QAS_AERS
      
    sudo pcs constraint colocation add g-QAS_AERS with g-QAS_ASCS -5000
    sudo pcs constraint order g-QAS_ASCS then g-QAS_AERS kind=Optional symmetrical=false
   
    sudo pcs node unstandby anftstsapcl1
    sudo pcs property set maintenance-mode=false
    ```

   如果从较旧版本升级并切换到排队服务器 2, 请参阅 SAP 说明[2641322](https://launchpad.support.sap.com/#/notes/2641322)。 

   请确保群集状态正常，并且所有资源都已启动。 资源在哪个节点上运行并不重要。

    ```
    sudo pcs status
    
    # Online: [ anftstsapcl1 anftstsapcl2 ]
    #
    # Full list of resources:
    #
    # rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl2
    #  Resource Group: g-QAS_ASCS
    #      fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
    #      nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
    #      vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
    #      rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    #  Resource Group: g-QAS_AERS
    #      fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
    #      nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
    #      vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
    #      rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. **[A]** 在两个节点上添加 ASCS 和 ERS 的防火墙规则, 在两个节点上添加 ASCS 和 ERS 的防火墙规则。
   ```
   # Probe Port of ASCS
   sudo firewall-cmd --zone=public --add-port=62000/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62000/tcp
   sudo firewall-cmd --zone=public --add-port=3200/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3200/tcp
   sudo firewall-cmd --zone=public --add-port=3600/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3600/tcp
   sudo firewall-cmd --zone=public --add-port=3900/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3900/tcp
   sudo firewall-cmd --zone=public --add-port=8100/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=8100/tcp
   sudo firewall-cmd --zone=public --add-port=50013/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50013/tcp
   sudo firewall-cmd --zone=public --add-port=50014/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50014/tcp
   sudo firewall-cmd --zone=public --add-port=50016/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50016/tcp
   # Probe Port of ERS
   sudo firewall-cmd --zone=public --add-port=62101/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=62101/tcp
   sudo firewall-cmd --zone=public --add-port=3301/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=3301/tcp
   sudo firewall-cmd --zone=public --add-port=50113/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50113/tcp
   sudo firewall-cmd --zone=public --add-port=50114/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50114/tcp
   sudo firewall-cmd --zone=public --add-port=50116/tcp --permanent
   sudo firewall-cmd --zone=public --add-port=50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver 应用程序服务器准备

   某些数据库要求在应用程序服务器上执行数据库实例安装。 请准备好应用程序服务器虚拟机，使之能够在这些场合下使用。  

   以下步骤假定在与 ASCS/SCS 和 HANA 服务器不同的服务器上安装应用程序服务器。 否则，则无需进行以下某些步骤（如配置主机名解析）。  

   以下各项带有前缀 **[A]** -适用于 PAS 和 .aas, **[P]** -仅适用于 pas 或 **[S]** -仅适用于 .aas。  

1. **[A]** 安装主机名称解析你可以使用 DNS 服务器, 或修改所有节点上的/etc/hosts。 此示例演示如何使用 /etc/hosts 文件。
   请替换以下命令中的 IP 地址和主机名：  

   ```
   sudo vi /etc/hosts
   ```

   将以下行插入 /etc/hosts。 更改 IP 地址和主机名以与环境匹配。

   ```
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS
   192.168.14.9 anftstsapvh
   # IP address of the load balancer frontend configuration for SAP NetWeaver ASCS ERS
   192.168.14.10 anftstsapers
   192.168.14.7 anftstsapa01
   192.168.14.8 anftstsapa02
   ```

1. **[A]** 创建 Sapmnt 目录创建 sapmnt 目录。
   ```
   sudo mkdir -p /sapmnt/QAS
   sudo mkdir -p /usr/sap/trans

   sudo chattr +i /sapmnt/QAS
   sudo chattr +i /usr/sap/trans
   ```

1. **[A]** 安装 NFS 客户端和其他要求  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** 添加装载条目  

   ```
   sudo vi /etc/fstab
   
   # Add the following lines to fstab, save and exit
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3
   192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   装载新共享

   ```
   sudo mount -a
   ```

1. **[P]** 创建并装入 PAS 目录  

   ```
   sudo mkdir -p /usr/sap/QAS/D02
   sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```

1. **[S]** 创建和装载 .aas 目录  

   ```
   sudo mkdir -p /usr/sap/QAS/D03
   sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # Add the following line to fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # Mount
   sudo mount -a
   ```


1. [A] 配置交换文件
 
   ```
   sudo vi /etc/waagent.conf
   
   # Set the property ResourceDisk.EnableSwap to y
   # Create and use swapfile on resource disk.
   ResourceDisk.EnableSwap=y
   
   # Set the size of the SWAP file with property ResourceDisk.SwapSizeMB
   # The free space of resource disk varies by virtual machine size. Make sure that you do not set a value that is too big. You can check the SWAP space with command swapon
   # Size of the swapfile.
   ResourceDisk.SwapSizeMB=2000
   ```

   重新启动代理以激活更改

   ```
   sudo service waagent restart
   ```

## <a name="install-database"></a>安装数据库

在此示例中，SAP NetWeaver 安装在 SAP HANA 上。 可以使用每个受支持的数据库完成此安装。 有关如何在 Azure 中安装 SAP HANA 的详细信息, 请参阅. For a list of supported databases, see [SAP Note 1928533][1928533] [Red Hat Enterprise Linux 上的 Azure vm SAP HANA 的高可用性][sap-hana-ha]。

1. 运行 SAP 数据库实例安装

   使用映射到数据库的负载均衡器前端配置的 IP 地址的虚拟主机名将 SAP NetWeaver 数据库实例作为根安装。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## <a name="sap-netweaver-application-server-installation"></a>SAP NetWeaver 应用程序服务器安装

请按照这些步骤安装 SAP 应用程序服务器。

1. 准备应用程序服务器

   遵循前面 [SAP NetWeaver 应用程序服务器准备](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7)一章中的步骤准备应用程序服务器。

1. 安装 SAP NetWeaver 应用程序服务器

   安装主服务器或其他的 SAP NetWeaver 应用程序服务器。

   可以使用 sapinst 参数 SAPINST_REMOTE_ACCESS_USER 允许非根用户连接到 sapinst。

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. 更新 SAP HANA 安全存储

   更新 SAP HANA 安全存储以指向 SAP HANA 系统复制设置的虚拟名称。

   运行以下命令将条目列为 \<sapsid>adm

   ```
   hdbuserstore List
   ```

   此命令应会列出如下所示的所有条目
   ```
   DATA FILE       : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.DAT
   KEY FILE        : /home/qasadm/.hdb/anftstsapa01/SSFS_HDB.KEY
   
   KEY DEFAULT
     ENV : 192.168.14.4:30313
     USER: SAPABAP1
     DATABASE: QAS
   ```

   输出显示，默认条目的 IP 地址正在指向虚拟机而不是负载均衡器的 IP 地址。 需将此条目更改为指向负载均衡器的虚拟主机名。 请确保使用相同的端口 (在上面的输出中为**30313** ) 和数据库名称 (在上面的输出中为**QAS** )!

   ```
   su - qasadm
   hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## <a name="test-the-cluster-setup"></a>测试群集设

1. 手动迁移 ASCS 实例

   开始测试之前的资源状态：

   ```
    rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   运行以下命令作为根，迁移 ASCS 实例。

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # Remove failed actions for the ERS that occurred as part of the migration
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 模拟节点故障

   开始测试之前的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   在其中运行 ASCS 实例的节点上运行以下命令作为根

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   节点再次启动后的状态应类似如下所示。

   ```
   Online: [ anftstsapcl1 anftstsapcl2 ]
   
   Full list of resources:
   
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   
   Failed Actions:
   * rsc_sap_QAS_ERS01_monitor_11000 on anftstsapcl1 'not running' (7): call=45, status=complete, exitreason='',
   ```

   使用以下命令清除失败的资源。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 终止消息服务器进程

   开始测试之前的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```
   
   运行以下命令作为根，确定消息服务器的进程并将其终止。

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   如果仅终止消息服务器一次, 则将由`sapstart`重新启动。 如果经常终止消息服务器，Pacemaker 会最终将 ASCS 实例移动到另一个节点。 运行以下命令作为根，清除测试后的 ASCS 和 ERS 实例的资源状态。

   ```
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl1 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

1. 终止排队服务器进程

   开始测试之前的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
   ```

   在运行 ASCS 实例的节点上，运行以下命令作为根，以终止排队服务器。

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   ASCS 实例应会立即故障转移到另一个节点。 ASCS 实例启动后，ERS 实例也会进行故障转移。 运行以下命令作为根，清除测试后的 ASCS 和 ERS 实例的资源状态。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ASCS00
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 终止排队复制服务器进程

   开始测试之前的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   在运行 ERS 实例的节点上，运行以下命令作为根，以终止排队复制服务器。

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   如果只运行一次命令, `sapstart`将重新启动该过程。 如果经常运行, `sapstart`则不会重新启动进程, 资源将处于停止状态。 运行以下命令作为根，清除测试后的 ERS 实例的资源状态。

   ```
   [root@anftstsapcl2 ~]# pcs resource cleanup rsc_sap_QAS_ERS01
   ```

   测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

1. 终止排队 sapstartsrv 进程

   开始测试之前的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

   在运行 ASCS 的节点上运行以下命令作为根。

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # 59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   作为监视的一部分，应始终由 Pacemaker 资源代理重新启动 sapstartsrv 进程。 测试之后的资源状态：

   ```
   rsc_st_azure    (stonith:fence_azure_arm):      Started anftstsapcl1
    Resource Group: g-QAS_ASCS
        fs_QAS_ASCS        (ocf::heartbeat:Filesystem):    Started anftstsapcl1
        nc_QAS_ASCS        (ocf::heartbeat:azure-lb):      Started anftstsapcl1
        vip_QAS_ASCS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl1
        rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance):   Started anftstsapcl1
    Resource Group: g-QAS_AERS
        fs_QAS_AERS        (ocf::heartbeat:Filesystem):    Started anftstsapcl2
        nc_QAS_AERS        (ocf::heartbeat:azure-lb):      Started anftstsapcl2
        vip_QAS_AERS       (ocf::heartbeat:IPaddr2):       Started anftstsapcl2
        rsc_sap_QAS_ERS01  (ocf::heartbeat:SAPInstance):   Started anftstsapcl2
   ```

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性以及针对 Azure 上的 SAP HANA（大型实例）规划灾难恢复，请参阅 [Azure 上的 SAP HANA（大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复, 请参阅[Azure 虚拟机 (vm) 上的 SAP HANA 的高可用性][sap-hana-ha]
