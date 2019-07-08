---
title: 适用于 Azure 的 NetApp 文件与 Red Hat Enterprise Linux 上的 SAP NetWeaver 的 azure 虚拟机高可用性 |Microsoft Docs
description: Azure 虚拟机在 Red Hat Enterprise Linux 上为 SAP NetWeaver 提供的高可用性
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: rdeltcheva
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/14/2019
ms.author: radeltch
ms.openlocfilehash: 8679cfe54c8fb2c88b312f67ea9b2d7115cc479e
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503573"
---
# <a name="azure-virtual-machines-high-availability-for-sap-netweaver-on-red-hat-enterprise-linux-with-azure-netapp-files-for-sap-applications"></a>使用 Azure NetApp 文件的 SAP 应用程序的 Red Hat Enterprise Linux 上的 SAP NetWeaver 的 azure 虚拟机高可用性

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

本文介绍如何部署虚拟机、 虚拟机配置、 安装群集框架和安装高度可用的 SAP NetWeaver 7.50 系统，使用[Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)。
在示例配置和安装命令等内容中，ASCS 实例编号为 00，ERS 实例编号为 01，主应用程序实例 (PAS) 是 02 和应用程序实例 (AAS) 是 03。 使用 SAP 系统 ID QAS。 

数据库层并不在本文中详细介绍。  

请先阅读以下 SAP 说明和文档：

* [Azure 的 NetApp 文件文档][anf-azure-doc] 
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
* [Azure 虚拟机规划和实施适用于 Linux 上的 SAP][planning-guide]
* [适用于 Linux 上的 SAP 的 azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP Netweaver in pacemaker cluster](https://access.redhat.com/articles/3150081)（Pacemaker 群集中的 SAP NetWeaver）
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Configuring ASCS/ERS for SAP Netweaver with standalone resources in RHEL 7.5](https://access.redhat.com/articles/3569681)（使用 RHEL 7.5 中的独立资源为 SAP NetWeaver 配置 ASCS/ERS）
  * [使用独立排队服务器 2 (ENSA2) 在 RHEL 上的 Pacemaker 中配置 SAP S/4HANA ASCS/ERS ](https://access.redhat.com/articles/3974941)
* Azure 特定的 RHEL 文档：
  * [Support Policies for RHEL High Availability Clusters - Microsoft Azure Virtual Machines as Cluster Members](https://access.redhat.com/articles/3131341)（RHEL 高可用性群集的支持策略 - Microsoft Azure 虚拟机作为群集成员）
  * [Installing and Configuring a Red Hat Enterprise Linux 7.4 (and later) High-Availability Cluster on Microsoft Azure](https://access.redhat.com/articles/3252491)（在 Microsoft Azure 上安装和配置 Red Hat Enterprise Linux 7.4 [及更高版本] 高可用性群集）
* [在 Microsoft Azure 中使用 Azure NetApp 文件 NetApp SAP 应用程序][anf-sap-applications-azure]

## <a name="overview"></a>概述

为 SAP Netweaver 中心服务的高 availability(HA) 需要共享的存储。
Red Hat Linux 上实现，到目前为止它已生成单独的高度可用的 GlusterFS 群集所必需。 

现在，可以通过来实现 SAP Netweaver HA 使用部署在 Azure 的 NetApp 文件上的共享的存储。 使用 Azure NetApp 文件的共享的存储不需要额外[GlusterFS 群集](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel-glusterfs)。 HA 的 SAP Netweaver 中央 services(ASCS/SCS) 仍需要使用 pacemaker。

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-rhel/high-availability-guide-rhel-anf.png)

SAP NetWeaver ASCS、SAP NetWeaver SCS、SAP NetWeaver ERS 和 SAP HANA 数据库使用虚拟主机名和虚拟 IP 地址。 在 Azure 上，需要负载均衡器才能使用虚拟 IP 地址。 以下列表显示 (A) SCS 和 ERS 的负载均衡器具有单独的前端 Ip 配置。

> [!IMPORTANT]
> 多 SID 群集的 SAP ASCS/ERS 为 Red Hat Linux Azure Vm 中的来宾操作系统原样**不支持**。 多 SID 群集描述多个具有一个 Pacemaker 群集中不同的 Sid 的 SAP ASCS/ERS 实例的安装。

### <a name="ascs"></a>(A)SCS

* 前端配置
  * IP 地址 192.168.14.9
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
  * IP 地址 192.168.14.10
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

SAP NetWeaver 要求对传输和配置文件目录使用共享存储。  在继续之前安装以进行 Azure NetApp 文件基础结构，自己应熟悉[Azure NetApp 文件文档][anf-azure-doc]。 检查是否到所选的 Azure 区域提供 Azure NetApp 文件。 以下链接将显示按 Azure 区域的 Azure NetApp 文件的可用性：[Azure 的 NetApp 文件按 Azure 区域的可用性][anf-avail-matrix]。

Azure 的 NetApp 文件是有几种[Azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)。 在部署之前 Azure NetApp 文件，请求到后面的 Azure NetApp 文件载入[注册，以便 Azure NetApp 文件说明][anf-register]。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

已部署的步骤假设[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 在同一 Azure 虚拟网络或对等互连的 Azure 虚拟网络中，必须部署 Azure NetApp 文件资源和 Vm，装载 Azure NetApp 文件资源的位置。  

1. 如果尚未完成的操作，请求[载入到 Azure 的 NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  
2. 按照所选 Azure 区域中创建的 NetApp 帐户[说明创建 NetApp 帐户](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)。  
3. 设置 Azure NetApp 文件容量池，遵循[说明如何设置 Azure NetApp 文件容量池](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)。  
本文中介绍的 SAP Netweaver 体系结构使用单个 Azure NetApp 文件容量池，高级 SKU。 我们建议在 Azure 上的 SAP Netweaver 应用程序工作负荷的 Azure NetApp 文件高级 SKU。  
4. 委托到 Azure 的 NetApp 文件的子网中所述[说明委托到 Azure NetApp 文件中的子网](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

5. 部署 Azure NetApp 文件卷，以下[如何为 Azure NetApp 文件创建一个卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)。 部署中指定的 Azure NetApp 文件的卷[子网](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)。 请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一 Azure 虚拟网络或对等互连的 Azure 虚拟网络。 在此示例中，我们使用两个 Azure NetApp 文件卷： sap<b>QAS</b>和 transSAP。 装载到相应的装入点的文件路径是否 /usrsap<b>qas</b>/sapmnt<b>QAS</b>，/usrsap<b>qas</b>/usrsap<b>QAS</b>sys，等等。  

   1. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/sapmnt<b>QAS</b>)
   2. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ascs)
   3. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>sys)
   4. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>ers)
   5. 卷 transSAP (nfs://192.168.24.4/transSAP)
   6. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>pa)
   7. 卷 sap<b>QAS</b> (nfs://192.168.24.5/usrsap<b>qas</b>/usrsap<b>QAS</b>aas)
  
在此示例中，我们使用 Azure 的所有 SAP Netweaver 文件系统的 NetApp 文件演示如何使用 Azure NetApp 文件。 此外可以作为部署不需要通过 NFS 装入的 SAP 文件系统[Azure 磁盘存储](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd)。 在此示例中<b>-e</b>必须是对 Azure NetApp 文件并<b>f g</b> (即 /usr/sap/<b>QAS</b>/D<b>02</b>，/usr/sap/<b>QAS</b>/D<b>03</b>) 可以部署为 Azure 磁盘存储。 

### <a name="important-considerations"></a>重要注意事项

在考虑 Azure NetApp 文件上的 SAP Netweaver SUSE 高可用性体系结构时，应注意以下重要注意事项：

- 最小容量池是 4 TiB。 容量池大小必须是 4 TiB 的倍数。
- 最小的卷是 100 GiB
- Azure 的 NetApp 文件和所有虚拟机，其中将装载 Azure NetApp 文件卷，必须在同一 Azure 虚拟网络中或在[虚拟网络对等互连](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)同一区域中。 现在支持通过 VNET 对等互连在同一区域中的 azure NetApp 文件访问。 尚不支持 azure 的 NetApp 访问通过全局对等互连。
- 所选虚拟网络必须具有一个子网，委派给 Azure NetApp 文件。
- Azure 的 NetApp 文件目前支持仅 NFSv3 
- Azure 的 NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)： 可以控制允许的客户端访问类型 （读取和写入，Read Only，等等）。 
- Azure 的 NetApp 文件功能尚未识别的区域。 当前 Azure NetApp 文件功能不被部署在 Azure 区域中的所有可用性区域中。 请注意在某些 Azure 区域中的潜在延迟影响。 

## <a name="setting-up-ascs"></a>设置 (A)SCS

在此示例中，部署资源的手动通过[Azure 门户](https://portal.azure.com/#home)。

### <a name="deploy-linux-manually-via-azure-portal"></a>通过 Azure 门户手动部署 Linux

首先需要创建 Azure NetApp 文件卷。 部署 Vm。 之后，创建一个负载均衡器并使用后端池中的虚拟机。

1. 创建负载均衡器（内部）  
   1. 创建前端 IP 地址
      1. ASCS 的 IP 地址 192.168.14.9
         1. 打开负载均衡器，选择前端 IP 池，并单击“添加”
         1. 输入新前端 IP 池的名称 (例如**前端。QAS。ASCS**)
         1. 设置为静态分配并输入 IP 地址 (例如**192.168.14.9**)
         1. 单击“确定”
      1. ASCS ERS 的 IP 地址 192.168.14.10
         * 重复上述步骤"a"来为 ERS 创建 IP 地址下的 (例如**192.168.14.10**和**前端。QAS。ERS**)
   1. 创建后端池
      1. 为 ASCS 创建后端池
         1. 打开负载均衡器，单击后端池，并单击“添加”
         1. 输入新后端池的名称 (例如**后端。QAS**)
         1. 单击“添加虚拟机”。
         1. 选择以前为 ASCS 创建可用性集 
         1. 选择 (A)SCS 群集的虚拟机
         1. 单击“确定”
   1. 创建运行状况探测
      1. ASCS 的端口 620**00**
         1. 打开负载均衡器，选择运行状况探测，并单击“添加”
         1. 输入新的运行状况探测的名称 (例如**运行状况。QAS。ASCS**)
         1. 选择 TCP 作为协议，选择端口 620**00**，将“间隔”保留为 5，将“不正常阈值”保留为 2
         1. 单击“确定”
      1. 端口 621**01** ASCS ERS 的
            * 重复上述步骤，在"c"，为 ERS 创建运行状况探测 (例如 621**01**和**运行状况。QAS。ERS**)
   1. 负载均衡规则
      1. ASCS 的 32**00** TCP
         1. 打开负载均衡器，选择负载均衡规则，然后单击添加
         1. 输入新负载均衡器规则的名称 (例如**lb。QAS。ASCS.3200**)
         1. 为 ASCS、 后端池和前面创建的运行状况探测选择前端 IP 地址 (例如**前端。QAS。ASCS**)
         1. 将协议保留为“TCP”，输入端口 **3200** 
         1. 将空闲超时增大到 30 分钟
         1. **确保启用浮动 IP**
         1. 单击“确定”
      1. ASCS 的其他端口
         * 重复上述步骤"d"下的，对于端口 36**00**、 39**00**、 81**00**，5**00**13、 5**00**14、 5**00**16 和 TCP ASCS 的
      1. ASCS ERS 的其他端口
         * 重复上述步骤"d"下的，对于端口 32**01**、 33**01**，5**01**13、 5**01**14、 5**01**16 和 TCPASCS ERS 的


> [!IMPORTANT]
> 不要启用 TCP 放置在 Azure 负载均衡器之后的 Azure Vm 上的时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数设置**net.ipv4.tcp_timestamps**到**0**。 有关详细信息，请参阅[负载均衡器运行状况探测](https://docs.microsoft.com/azure/load-balancer/load-balancer-custom-probe-overview)。

### <a name="create-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 (A)SCS 服务器的基本 Pacemaker。

### <a name="prepare-for-sap-netweaver-installation"></a>准备 SAP Netweaver 安装

以下各项带有前缀 [A] - 适用于所有节点、[1] - 仅适用于节点 1，或 [2] - 仅适用于节点 2    。

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

1. **[1]** Azure NetApp 文件卷中的创建 SAP 目录。  
   将临时 Azure NetApp 文件卷装载上的某个 Vm，并创建 SAP 目录 （文件路径）。  

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

1. **[A]** Create the shared directories

   ```
   sudo mkdir-p/sapmnt/QAS sudo mkdir-p /usr/sap/trans sudo mkdir-p /usr/sap/QAS/SYS sudo mkdir-p /usr/sap/QAS/ASCS00 sudo mkdir-p /usr/sap/QAS/ERS01
   
   sudo chattr + 我/sapmnt/QAS sudo chattr + 我 /usr/sap/trans sudo chattr + 我 /usr/sap/QAS/SYS sudo chattr + 我 /usr/sap/QAS/ASCS00 sudo chattr + 我 /usr/sap/QAS/ERS01
   ```

1. **[A]** Install NFS client and other requirements

   ```
   sudo yum -y install nfs-utils resource-agents resource-agents-sap
   ```

1. **[A]** Check version of resource-agents-sap

   Make sure that the version of the installed resource-agents-sap package is at least 3.9.5-124.el7
   ```
   sudo yum info resource-agents-sap
   
   # <a name="loaded-plugins-langpacks-product-id-search-disabled-repos"></a>加载插件： langpacks，产品 id，搜索-已禁用-存储库
   # <a name="repodata-is-over-2-weeks-old-install-yum-cron-or-run-yum-makecache-fast"></a>Repodata 是超过了 2 周。 安装 yum cron 吗？ 或运行： yum makecache 快速
   # <a name="installed-packages"></a>已安装的包
   # <a name="name---------resource-agents-sap"></a>Name        : resource-agents-sap
   # <a name="arch---------x8664"></a>体系结构： x86_64
   # <a name="version------395"></a>版本：3.9.5
   # <a name="release------124el7"></a>版本：124.el7
   # <a name="size---------100-k"></a>大小：100 k
   # <a name="repo---------installed"></a>存储库： 安装
   # <a name="from-repo----rhel-sap-for-rhel-7-server-rpms"></a>从存储库： rhel-sap-for-rhel-7-server-rpms
   # <a name="summary------sap-cluster-resource-agents-and-connector-script"></a>摘要：SAP 群集资源代理和连接器脚本
   # <a name="url----------httpsgithubcomclusterlabsresource-agents"></a>URL: https://github.com/ClusterLabs/resource-agents
   # <a name="license------gplv2"></a>许可证：GPLv2+
   # <a name="description--the-sap-resource-agents-and-connector-script-interface-with"></a>说明：SAP 资源代理和连接器脚本接口
   #          <a name="-pacemaker-to-allow-sap-instances-to-be-managed-in-a-cluster"></a>:若要允许管理群集中的 SAP 实例的 pacemaker
   #          <a name="-environment"></a>： 环境。
   ```


1. **[A]** Add mount entries

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>将以下行添加到 fstab、 保存并退出
    192.168.24.5:/sapQAS/sapmntQAS/sapmnt/QAS nfs rw，硬，rsize，则将 = 65536，wsize = 65536，vers = 3 192.168.24.5:/sapQAS/usrsapQASsys /usr/sap/QAS/SYS nfs rw，硬，rsize，则将 = 65536，wsize = 65536，vers = 3 192.168.24.4:/transSAP /usr/sap/trans nfs rw，硬，rsize，则将 =65536，wsize = 65536，vers = 3
   ```

   Mount the new shares

   ```
   sudo mount -a  
   ```

1. **[A]** Configure SWAP file

   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>ResourceDisk.EnableSwap 将属性设置为 y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>创建并在资源磁盘上使用交换文件。
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>设置与属性 ResourceDisk.SwapSizeMB 交换文件的大小
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>资源磁盘的可用空间的虚拟机大小而异。 请确保未设置为太大的值。 你可以检查与命令 swapon 的交换空间
   # <a name="size-of-the-swapfile"></a>交换文件的大小。
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo waagent 启服务
   ```

1. **[A]** RHEL configuration

   Configure RHEL as described in SAP Note [2002167]

### Installing SAP NetWeaver ASCS/ERS

1. **[1]** Create a virtual IP resource and health-probe for the ASCS instance

   ```
   sudo pc 节点备用 anftstsapcl2
   
   sudo pc 资源创建 fs_QAS_ASCS 文件系统 device='192.168.24.5:/sapQAS/usrsapQASascs' \
     directory='/usr/sap/QAS/ASCS00' fstype='nfs' \
     --group g-QAS_ASCS
   
   sudo pc 资源创建 vip_QAS_ASCS IPaddr2 \
     ip=192.168.14.9 cidr_netmask=24 \
     --group g-QAS_ASCS
   
   sudo pcs resource create nc_QAS_ASCS azure-lb port=62000 \
     --group g-QAS_ASCS
   ```

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl2-standby"></a>节点 anftstsapcl2： 备用
   # <a name="online--anftstsapcl1-"></a>联机: [anftstsapcl1]
   #
   # <a name="full-list-of-resources"></a>资源的完整列表：
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl1"></a>rsc_st_azure    (stonith:fence_azure_arm):    已启动的 anftstsapcl1
   #  <a name="resource-group-g-qasascs"></a>资源组： g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  已启动的 anftstsapcl1
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     已启动的 anftstsapcl1
   ```

1. **[1]** Install SAP NetWeaver ASCS  

   Install SAP NetWeaver ASCS as root on the first node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ASCS, for example <b>anftstsapvh</b>, <b>192.168.14.9</b> and the instance number that you used for the probe of the load balancer, for example <b>00</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>允许访问 SWPM。 此规则不是永久的。 如果重新启动计算机，您必须再次运行该命令。
   sudo firewall-cmd --zone=public  --add-port=4237/tcp
   
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ASCS**00**, try setting the owner and group of the ASCS**00** folder and retry.

   ```
   sudo chown qasadm /usr/sap/QAS/ASCS00 sudo chgrp sapsys /usr/sap/QAS/ASCS00
   ```

1. **[1]** Create a virtual IP resource and health-probe for the ERS instance

   ```
   sudo pc 节点 unstandby anftstsapcl2 sudo pc 节点备用 anftstsapcl1
   
   sudo pcs resource create fs_QAS_AERS Filesystem device='192.168.24.5:/sapQAS/usrsapQASers' \
     directory='/usr/sap/QAS/ERS01' fstype='nfs' \
    -g QAS_AERS 进行分组

   sudo pcs resource create vip_QAS_AERS IPaddr2 \
     ip=192.168.14.10 cidr_netmask=24 \
    -g QAS_AERS 进行分组
   
   sudo pcs resource create nc_QAS_AERS azure-lb port=62101 \
    -g QAS_AERS 进行分组
   ```
 
   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

   ```
   sudo pcs status
   
   # <a name="node-anftstsapcl1-standby"></a>节点 anftstsapcl1： 备用
   # <a name="online--anftstsapcl2-"></a>联机: [anftstsapcl2]
   #
   # <a name="full-list-of-resources"></a>资源的完整列表：
   #
   # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    已启动的 anftstsapcl2
   #  <a name="resource-group-g-qasascs"></a>资源组： g QAS_ASCS
   #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  已启动的 anftstsapcl2
   #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    启动 anftstsapcl2 <
   #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     已启动的 anftstsapcl2
   #  <a name="resource-group-g-qasaers"></a>资源组： g QAS_AERS
   #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):  已启动的 anftstsapcl2
   #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2
   #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):     已启动的 anftstsapcl2
   ```

1. **[2]** Install SAP NetWeaver ERS  

   Install SAP NetWeaver ERS as root on the second node using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the ERS, for example <b>anftstsapers</b>, <b>192.168.14.10</b> and the instance number that you used for the probe of the load balancer, for example <b>01</b>.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   # <a name="allow-access-to-swpm-this-rule-is-not-permanent-if-you-reboot-the-machine-you-have-to-run-the-command-again"></a>允许访问 SWPM。 此规则不是永久的。 如果重新启动计算机，您必须再次运行该命令。
   sudo firewall-cmd --zone=public  --add-port=4237/tcp

   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin SAPINST_USE_HOSTNAME=<virtual_hostname>
   ```

   If the installation fails to create a subfolder in /usr/sap/**QAS**/ERS**01**, try setting the owner and group of the ERS**01** folder and retry.

   ```
   sudo chown qaadm /usr/sap/QAS/ERS01 sudo chgrp sapsys /usr/sap/QAS/ERS01
   ```

1. **[1]** Adapt the ASCS/SCS and ERS instance profiles

   * ASCS/SCS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh
   
   # <a name="change-the-restart-command-to-a-start-command"></a>将重新启动命令更改为 start 命令
   #<a name="restartprogram01--local-en-pfpf"></a>Restart_Program_01 = 本地 $(_EN) $ （_pf）
   Start_Program_01 = 本地 $(_EN) $ （_pf）
   
   # <a name="add-the-keep-alive-parameter"></a>添加保持活动状态参数
   enque/encni/set_so_keepalive = true
   ```

   * ERS profile

   ```
   sudo vi /sapmnt/QAS/profile/QAS_ERS01_anftstsapers
   
   # <a name="change-the-restart-command-to-a-start-command"></a>将重新启动命令更改为 start 命令
   #<a name="restartprogram00--local-er-pfpfl-nrscsid"></a>Restart_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   Start_Program_00 = local $(_ER) pf=$(_PFL) NR=$(SCSID)
   
   # <a name="remove-autostart-from-ers-profile"></a>ERS 配置文件中删除自动启动
   # <a name="autostart--1"></a>自动启动 = 1
   ```


1. **[A]** Configure Keep Alive

   The communication between the SAP NetWeaver application server and the ASCS/SCS is routed through a software load balancer. The load balancer disconnects inactive connections after a configurable timeout. To prevent this, you need to set a parameter in the SAP NetWeaver ASCS/SCS profile and change the Linux system settings. Read [SAP Note 1410736][1410736] for more information.

   The ASCS/SCS profile parameter enque/encni/set_so_keepalive was already added in the last step.

   ```
   # <a name="change-the-linux-system-configuration"></a>更改 Linux 系统配置
   sudo sysctl net.ipv4.tcp_keepalive_time=120
   ```

1. **[A]** Update the /usr/sap/sapservices file

   To prevent the start of the instances by the sapinit startup script, all instances managed by Pacemaker must be commented out from /usr/sap/sapservices file. Do not comment out the SAP HANA instance if it will be used with HANA SR.

   ```
   sudo vi /usr/sap/sapservices
   
   # <a name="on-the-node-where-you-installed-the-ascs-comment-out-the-following-line"></a>在节点上安装 ASCS，注释掉以下行
   # <a name="ldlibrarypathusrsapqasascs00exeldlibrarypath-export-ldlibrarypath-usrsapqasascs00exesapstartsrv-pfusrsapqassysprofileqasascs00anftstsapvh--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ASCS00/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ASCS00/exe/sapstartsrv pf=/usr/sap/QAS/SYS/profile/QAS_ASCS00_anftstsapvh -D -u qasadm
   
   # <a name="on-the-node-where-you-installed-the-ers-comment-out-the-following-line"></a>上安装 ERS 的节点，注释掉以下行
   # <a name="ldlibrarypathusrsapqasers01exeldlibrarypath-export-ldlibrarypath-usrsapqasers01exesapstartsrv-pfusrsapqasers01profileqasers01anftstsapers--d--u-qasadm"></a>LD_LIBRARY_PATH=/usr/sap/QAS/ERS01/exe:$LD_LIBRARY_PATH; export LD_LIBRARY_PATH; /usr/sap/QAS/ERS01/exe/sapstartsrv pf=/usr/sap/QAS/ERS01/profile/QAS_ERS01_anftstsapers -D -u qasadm
   ```

1. **[1]** Create the SAP cluster resources

   If using enqueue server 1 architecture (ENSA1), define the resources as follows:

   ```
   sudo pcs property set maintenance-mode=true
   
    sudo pc 资源创建 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    meta resource-stickiness=5000 migration-threshold=1 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    -g QAS_AERS 进行分组
      
    sudo pc 约束共置添加 g QAS_AERS g QAS_ASCS-5000 sudo pc 约束位置 rsc_sap_QAS_ASCS00 规则分数 = 2000 runs_ers_QAS eq 1 sudo pc 约束顺序 g QAS_ASCS 然后 g QAS_AERS 类型 = 可选对称 = false
    
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   SAP introduced support for enqueue server 2, including replication, as of SAP NW 7.52. Starting with ABAP Platform 1809, enqueue server 2 is installed by default. See SAP note [2630416](https://launchpad.support.sap.com/#/notes/2630416) for enqueue server 2 support.
   If using enqueue server 2 architecture ([ENSA2](https://help.sap.com/viewer/cff8531bc1d9416d91bb6781e628d4e0/1709%20001/en-US/6d655c383abf4c129b0e5c8683e7ecd8.html)), install resource agent resource-agents-sap-4.1.1-12.el7.x86_64 or newer and define the resources as follows:

    ```
    sudo pcs property set maintenance-mode=true
    
    sudo pc 资源创建 rsc_sap_QAS_ASCS00 SAPInstance \
    InstanceName=QAS_ASCS00_anftstsapvh START_PROFILE="/sapmnt/QAS/profile/QAS_ASCS00_anftstsapvh" \
    AUTOMATIC_RECOVER = false \
    元资源粘性 = 5000 \
    --group g-QAS_ASCS
   
    sudo pcs resource create rsc_sap_QAS_ERS01 SAPInstance \
    InstanceName=QAS_ERS01_anftstsapers START_PROFILE="/sapmnt/QAS/profile/QAS_ERS01_anftstsapers" \
    AUTOMATIC_RECOVER = false IS_ERS = true \
    -g QAS_AERS 进行分组
      
    sudo pc 约束共置添加 g QAS_AERS 与 g QAS_ASCS-5000 sudo pc 约束顺序 g QAS_ASCS 然后 g QAS_AERS 类型 = 可选对称 = false
   
    sudo pcs node unstandby anftstsapcl1  sudo pcs property set maintenance-mode=false
    ```

   If you are upgrading from an older version and switching to enqueue server 2, see SAP note [2641322](https://launchpad.support.sap.com/#/notes/2641322). 

   Make sure that the cluster status is ok and that all resources are started. It is not important on which node the resources are running.

    ```
    sudo pcs status
    
    # <a name="online--anftstsapcl1-anftstsapcl2-"></a>联机: [anftstsapcl1 anftstsapcl2]
    #
    # <a name="full-list-of-resources"></a>资源的完整列表：
    #
    # <a name="rscstazure----stonithfenceazurearm------started-anftstsapcl2"></a>rsc_st_azure    (stonith:fence_azure_arm):    已启动的 anftstsapcl2
    #  <a name="resource-group-g-qasascs"></a>资源组： g QAS_ASCS
    #      <a name="fsqasascs--------ocfheartbeatfilesystem----started-anftstsapcl2"></a>fs_QAS_ASCS (ocf::heartbeat:Filesystem):  已启动的 anftstsapcl2
    #      <a name="ncqasascs--------ocfheartbeatazure-lb------started-anftstsapcl2"></a>nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2
    #      <a name="vipqasascs-------ocfheartbeatipaddr2-------started-anftstsapcl2"></a>vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     已启动的 anftstsapcl2
    #      <a name="rscsapqasascs00-ocfheartbeatsapinstance---started-anftstsapcl2"></a>rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
    #  <a name="resource-group-g-qasaers"></a>资源组： g QAS_AERS
    #      <a name="fsqasaers--------ocfheartbeatfilesystem----started-anftstsapcl1"></a>fs_QAS_AERS (ocf::heartbeat:Filesystem):  已启动的 anftstsapcl1
    #      <a name="ncqasaers--------ocfheartbeatazure-lb------started-anftstsapcl1"></a>nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1
    #      <a name="vipqasaers-------ocfheartbeatipaddr2-------started-anftstsapcl1"></a>vip_QAS_AERS (ocf::heartbeat:IPaddr2):     已启动的 anftstsapcl1
    #      <a name="rscsapqasers01--ocfheartbeatsapinstance---started-anftstsapcl1"></a>rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl1
   ```

1. **[A]** Add firewall rules for ASCS and ERS on both nodes
   Add the firewall rules for ASCS and ERS on both nodes.
   ```
   # <a name="probe-port-of-ascs"></a>ASCS 的探测端口
   sudo 防火墙-cmd-区域 = 公共--添加端口 = 62000/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 62000/tcp sudo 防火墙-cmd--区域 = 公共--添加端口 = 3200/tcp--永久 sudo 防火墙-cmd--区域 = 公共--添加端口 = 3200/tcp sudo防火墙 cmd--区域 = 公共--添加端口 = 3600/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 3600/tcp sudo 防火墙-cmd-区域 = 公共--添加端口 = 3900/tcp--永久 sudo 防火墙-cmd--区域 = 公共--添加端口 = 3900/tcp sudo防火墙 cmd--区域 = 公共--添加端口 = 8100/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 8100/tcp sudo 防火墙-cmd-区域 = 公共--添加端口 = 50013/tcp--永久 sudo 防火墙-cmd--区域 = 公共--添加端口 = 50013/tcp sudo防火墙 cmd--区域 = 公共--添加端口 = 50014/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 50014/tcp sudo 防火墙-cmd-区域 = 公共--添加端口 = 50016/tcp--永久 sudo 防火墙-cmd--区域 = 公共--添加端口 = 50016/tcp
   # <a name="probe-port-of-ers"></a>ERS 的探测端口
   sudo 防火墙-cmd-区域 = 公共--添加端口 = 62101/tcp--永久 sudo 防火墙-cmd--区域 = 公共--添加端口 = 62101/tcp sudo 防火墙-cmd--区域 = 公共--添加端口 = 3301/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 3301/tcp sudo防火墙 cmd--区域 = 公共--添加端口 = 50113/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 50113/tcp sudo 防火墙-cmd--区域 = 公共--添加端口 = 50114/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 50114/tcp sudo防火墙 cmd--区域 = 公共--添加端口 = 50116/tcp--永久 sudo 防火墙-cmd-区域 = 公共--添加端口 = 50116/tcp
   ```

## <a name="2d6008b0-685d-426c-b59e-6cd281fd45d7"></a>SAP NetWeaver application server preparation

   Some databases require that the database instance installation is executed on an application server. Prepare the application server virtual machines to be able to use them in these cases.  

   The steps bellow assume that you install the application server on a server different from the ASCS/SCS and HANA servers. Otherwise some of the steps below (like configuring host name resolution) are not needed.  

   The following items are prefixed with either **[A]** - applicable to both PAS and AAS, **[P]** - only applicable to PAS or **[S]** - only applicable to AAS.  

1. **[A]** Setup host name resolution
   You can either use a DNS server or modify the /etc/hosts on all nodes. This example shows how to use the /etc/hosts file.
   Replace the IP address and the hostname in the following commands:  

   ```
   sudo vi /etc/hosts
   ```

   Insert the following lines to /etc/hosts. Change the IP address and hostname to match your environment.

   ```
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs"></a>SAP NetWeaver ASCS 的负载均衡器前端配置的 IP 地址
   192.168.14.9 anftstsapvh
   # <a name="ip-address-of-the-load-balancer-frontend-configuration-for-sap-netweaver-ascs-ers"></a>SAP NetWeaver ASCS ERS 的负载均衡器前端配置的 IP 地址
   192.168.14.10 anftstsapers 192.168.14.7 anftstsapa01 192.168.14.8 anftstsapa02
   ```

1. **[A]** Create the sapmnt directory
   Create the sapmnt directory.
   ```
   sudo mkdir-p/sapmnt/QAS sudo mkdir-p /usr/sap/trans

   sudo chattr + 我/sapmnt/QAS sudo chattr + 我 /usr/sap/trans
   ```

1. **[A]** Install NFS client and other requirements  

   ```
   sudo yum -y install nfs-utils uuidd
   ```

1. **[A]** Add mount entries  

   ```
   sudo vi /etc/fstab
   
   # <a name="add-the-following-lines-to-fstab-save-and-exit"></a>将以下行添加到 fstab、 保存并退出
   192.168.24.5:/sapQAS/sapmntQAS /sapmnt/QAS nfs rw,hard,rsize=65536,wsize=65536,vers=3 192.168.24.4:/transSAP /usr/sap/trans nfs rw,hard,rsize=65536,wsize=65536,vers=3
   ```

   Mount the new shares

   ```
   sudo mount -a
   ```

1. **[P]** Create and mount the PAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D02 sudo chattr +i /usr/sap/QAS/D02
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>将以下行添加到 fstab
   92.168.24.5:/sapQAS/usrsapQASpas /usr/sap/QAS/D02 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>装载
   sudo mount -a
   ```

1. **[S]** Create and mount the AAS directory  

   ```
   sudo mkdir -p /usr/sap/QAS/D03 sudo chattr +i /usr/sap/QAS/D03
   
   sudo vi /etc/fstab
   # <a name="add-the-following-line-to-fstab"></a>将以下行添加到 fstab
   92.168.24.5:/sapQAS/usrsapQASaas /usr/sap/QAS/D03 nfs rw,hard,rsize=65536,wsize=65536,vers=3
   
   # <a name="mount"></a>装载
   sudo mount -a
   ```


1. **[A]** Configure SWAP file
 
   ```
   sudo vi /etc/waagent.conf
   
   # <a name="set-the-property-resourcediskenableswap-to-y"></a>ResourceDisk.EnableSwap 将属性设置为 y
   # <a name="create-and-use-swapfile-on-resource-disk"></a>创建并在资源磁盘上使用交换文件。
   ResourceDisk.EnableSwap=y
   
   # <a name="set-the-size-of-the-swap-file-with-property-resourcediskswapsizemb"></a>设置与属性 ResourceDisk.SwapSizeMB 交换文件的大小
   # <a name="the-free-space-of-resource-disk-varies-by-virtual-machine-size-make-sure-that-you-do-not-set-a-value-that-is-too-big-you-can-check-the-swap-space-with-command-swapon"></a>资源磁盘的可用空间的虚拟机大小而异。 请确保未设置为太大的值。 你可以检查与命令 swapon 的交换空间
   # <a name="size-of-the-swapfile"></a>交换文件的大小。
   ResourceDisk.SwapSizeMB=2000
   ```

   Restart the Agent to activate the change

   ```
   sudo waagent 启服务
   ```

## Install database

In this example, SAP NetWeaver is installed on SAP HANA. You can use every supported database for this installation. For more information on how to install SAP HANA in Azure, see [High availability of SAP HANA on Azure VMs on Red Hat Enterprise Linux][sap-hana-ha]. For a list of supported databases, see [SAP Note 1928533][1928533].

1. Run the SAP database instance installation

   Install the SAP NetWeaver database instance as root using a virtual hostname that maps to the IP address of the load balancer frontend configuration for the database.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

## SAP NetWeaver application server installation

Follow these steps to install an SAP application server.

1. Prepare application server

   Follow the steps in the chapter [SAP NetWeaver application server preparation](high-availability-guide-rhel.md#2d6008b0-685d-426c-b59e-6cd281fd45d7) above to prepare the application server.

1. Install SAP NetWeaver application server

   Install a primary or additional SAP NetWeaver applications server.

   You can use the sapinst parameter SAPINST_REMOTE_ACCESS_USER to allow a non-root user to connect to sapinst.

   ```
   sudo <swpm>/sapinst SAPINST_REMOTE_ACCESS_USER=sapadmin
   ```

1. Update SAP HANA secure store

   Update the SAP HANA secure store to point to the virtual name of the SAP HANA System Replication setup.

   Run the following command to list the entries as \<sapsid>adm

   ```
   hdbuserstore 列表
   ```

   This should list all entries and should look similar to
   ```
   数据文件： /home/qasadm/.hdb/anftstsapa01/SSFS_HDB。DAT 密钥文件： /home/qasadm/.hdb/anftstsapa01/SSFS_HDB。密钥
   
   密钥默认 ENV:192.168.14.4:30313 用户：SAPABAP1 数据库：QAS
   ```

   The output shows that the IP address of the default entry is pointing to the virtual machine and not to the load balancer's IP address. This entry needs to be changed to point to the virtual hostname of the load balancer. Make sure to use the same port (**30313** in the output above) and database name (**QAS** in the output above)!

   ```
   流式处理单位-qasadm hdbuserstore SET DEFAULT qasdb:30313@QAS SAPABAP1 <password of ABAP schema>
   ```

## Test the cluster setup

1. Manually migrate the ASCS instance

   Resource state before starting the test:

   ```
    rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

   Run the following commands as root to migrate the ASCS instance.

   ```
   [root@anftstsapcl1 ~]# pcs resource move rsc_sap_QAS_ASCS00
   
   [root@anftstsapcl1 ~]# pcs resource clear rsc_sap_QAS_ASCS00
   
   # <a name="remove-failed-actions-for-the-ers-that-occurred-as-part-of-the-migration"></a>删除迁移的过程中发生 ERS 的失败的操作
   [root@anftstsapcl1 ~] # pc 资源清理 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl2 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl1
   ```

1. Simulate node crash

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl2 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl1
   ```

   Run the following command as root on the node where the ASCS instance is running

   ```
   [root@anftstsapcl2 ~]# echo b > /proc/sysrq-trigger
   ```

   The status after the node is started again should look like this.

   ```
   联机: [anftstsapcl1 anftstsapcl2]
   
   资源的完整列表：
   
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   
   失败的操作：
   * 在 anftstsapcl1 rsc_sap_QAS_ERS01_monitor_11000 未在运行 (7): 调用 = 45，状态 = 完整，exitreason = '，
   ```

   Use the following command to clean the failed resources.

   ```
   [root@anftstsapcl1 ~] # pc 资源清理 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

1. Kill message server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```
   
   Run the following commands as root to identify the process of the message server and kill it.

   ```
   [root@anftstsapcl1 ~]# pgrep ms.sapQAS | xargs kill -9
   ```

   If you only kill the message server once, it will be restarted by `sapstart`. If you kill it often enough, Pacemaker will eventually move the ASCS instance to the other node. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl1 ~] # pc 资源清理 rsc_sap_QAS_ASCS00 [root@anftstsapcl1 ~] # pc 资源清理 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl2 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl1
   ```

1. Kill enqueue server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl2 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl1
   ```

   Run the following commands as root on the node where the ASCS instance is running to kill the enqueue server.

   ```
   [root@anftstsapcl2 ~]# pgrep en.sapQAS | xargs kill -9
   ```

   The ASCS instance should immediately fail over to the other node. The ERS instance should also fail over after the ASCS instance is started. Run the following commands as root to clean up the resource state of the ASCS and ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # pc 资源清理 rsc_sap_QAS_ASCS00 [root@anftstsapcl2 ~] # pc 资源清理 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

1. Kill enqueue replication server process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

   Run the following command as root on the node where the ERS instance is running to kill the enqueue replication server process.

   ```
   [root@anftstsapcl2 ~]# pgrep er.sapQAS | xargs kill -9
   ```

   If you only run the command once, `sapstart` will restart the process. If you run it often enough, `sapstart` will not restart the process and the resource will be in a stopped state. Run the following commands as root to clean up the resource state of the ERS instance after the test.

   ```
   [root@anftstsapcl2 ~] # pc 资源清理 rsc_sap_QAS_ERS01
   ```

   Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

1. Kill enqueue sapstartsrv process

   Resource state before starting the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

   Run the following commands as root on the node where the ASCS is running.

   ```
   [root@anftstsapcl1 ~]# pgrep -fl ASCS00.*sapstartsrv
   # <a name="59545-sapstartsrv"></a>59545 sapstartsrv
   
   [root@anftstsapcl1 ~]# kill -9 59545
   ```

   The sapstartsrv process should always be restarted by the Pacemaker resource agent as part of the monitoring. Resource state after the test:

   ```
   rsc_st_azure    (stonith:fence_azure_arm):    启动 anftstsapcl1 资源组： g QAS_ASCS fs_QAS_ASCS (ocf::heartbeat:Filesystem):  启动 anftstsapcl1 nc_QAS_ASCS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl1 vip_QAS_ASCS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl1 rsc_sap_QAS_ASCS00 (ocf::heartbeat:SAPInstance): 启动 anftstsapcl1 资源组： g QAS_AERS fs_QAS_AERS (ocf::heartbeat:Filesystem):  启动 anftstsapcl2 nc_QAS_AERS (ocf::heartbeat:azure-lb):    已启动的 anftstsapcl2 vip_QAS_AERS (ocf::heartbeat:IPaddr2):     启动 anftstsapcl2 rsc_sap_QAS_ERS01 (ocf::heartbeat:SAPInstance): 已启动的 anftstsapcl2
   ```

## Next steps

* [Azure Virtual Machines planning and implementation for SAP][planning-guide]
* [Azure Virtual Machines deployment for SAP][deployment-guide]
* [Azure Virtual Machines DBMS deployment for SAP][dbms-guide]
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure (large instances), see [SAP HANA (large instances) high availability and disaster recovery on Azure](hana-overview-high-availability-disaster-recovery.md).
* To learn how to establish high availability and plan for disaster recovery of SAP HANA on Azure VMs, see [High Availability of SAP HANA on Azure Virtual Machines (VMs)][sap-hana-ha]
