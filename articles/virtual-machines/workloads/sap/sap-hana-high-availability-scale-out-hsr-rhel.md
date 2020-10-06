---
title: 在 RHEL 上用 HSR 和 Pacemaker SAP HANA 扩展 |Microsoft Docs
description: 在 RHEL 上用 HSR 和 Pacemaker SAP HANA 扩展
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
ms.date: 10/02/2020
ms.author: radeltch
ms.openlocfilehash: edca4b44bd9e7aa9f100db3cea0bc69880a4c533
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2020
ms.locfileid: "91744737"
---
# <a name="high-availability-of-sap-hana-scale-out-system-on-red-hat-enterprise-linux"></a>Red Hat Enterprise Linux 上的 SAP HANA 扩展系统的高可用性 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
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
[1900823]:https://launchpad.support.sap.com/#/notes/1900823
[2292690]:https://launchpad.support.sap.com/#/notes/2292690
[2455582]:https://launchpad.support.sap.com/#/notes/2455582
[2593824]:https://launchpad.support.sap.com/#/notes/2593824
[2009879]:https://launchpad.support.sap.com/#/notes/2009879

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介绍如何使用 HANA 系统复制在扩展配置中部署高可用性 SAP HANA 系统 (HSR) 和 Azure 上的 Pacemaker Red Hat Enterprise Linux 虚拟机 (Vm) 。 所提供体系结构中的共享文件系统由 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md) 提供，并通过 NFS 进行装载。  

在示例配置和安装命令等中，HANA 实例为 **03** ，HANA 系统 ID 为 **HN1**。 这些示例基于 HANA 2.0 SP4 和 SAP 7.6 Red Hat Enterprise Linux。 

在开始之前，请参阅以下 SAP 说明和文章：

* SAP 说明 [1928533] 包括：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上的 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553]：列出 AZURE 中 sap 支持的 sap 软件部署的先决条件
* SAP 说明 [2002167] 为 Red Hat Enterprise Linux 建议使用 OS 设置
* SAP 说明 [2009879] 包含适用于 Red Hat Enterprise Linux 的 SAP HANA 准则
* SAP 说明 [2178632]：包含有关 Azure 中的 SAP 报告的所有监视指标的详细信息
* SAP 说明 [2191498]：包含 Azure 中的 Linux 所需的 SAP 主机代理版本
* SAP 说明 [2243692]：包含有关 Azure 中 Linux 上的 SAP 许可的信息
* SAP 说明 [1999351]：包含适用于 SAP 的 Azure 增强型监视扩展的其他疑难解答信息
* SAP 说明 [1900823]：包含有关 SAP HANA 存储要求的信息
* [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含适用于 Linux 的所有必需 SAP 说明
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SAP HANA 网络要求](https://www.sap.com/documents/2016/08/1cd2c2fb-807c-0010-82c7-eda71af511fa.html)
* 通用 RHEL 文档
  * [High Availability Add-On Overview](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_overview/index)（高可用性附加产品概述）
  * [High Availability Add-On Administration](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_administration/index)（高可用性附加产品管理）
  * [High Availability Add-On 参考](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/high_availability_add-on_reference/index)
  * [Red Hat Enterprise Linux 网络指南](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/networking_guide)
  * [如何实现在 NFS 共享上使用 HANA 文件系统配置 Pacemaker 群集中 SAP HANA 横向扩展系统复制](https://access.redhat.com/solutions/5423971)
* Azure 特定的 RHEL 文档：
  * [Install SAP HANA on Red Hat Enterprise Linux for Use in Microsoft Azure](https://access.redhat.com/public-cloud/microsoft-azure)（在 Red Hat Enterprise Linux 上安装要在 Microsoft Azure 中使用的 SAP HANA）
  * [用于 SAP HANA 横向扩展和系统复制的 Red Hat Enterprise Linux 解决方案](https://access.redhat.com/solutions/4386601)
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]
* [Azure NetApp 文件文档][anf-azure-doc] 


## <a name="overview"></a>概述

为 HANA 扩展安装实现 HANA 高可用性的一种方法是配置 HANA 系统复制，并通过 Pacemaker 群集保护解决方案，以允许自动故障转移。 当活动节点发生故障时，群集会将 HANA 资源故障转移到另一个站点。  
提供的配置显示每个站点上有三个 HANA 节点，以及用于防止裂脑方案的 "多数 maker" 节点。 可以改编说明，以将更多 Vm 作为 HANA DB 节点。  

`/hana/shared`所提供体系结构中的 HANA 共享文件系统由[Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md)提供。 它通过 NFSv 4.1 在同一 HANA 系统复制站点中的每个 HANA 节点上装入。 文件系统 `/hana/data` 和 `/hana/log` 是本地文件系统，不在 HANA DB 节点之间共享。 SAP HANA 将以非共享模式安装。 

> [!TIP]
> 有关推荐的 SAP HANA 存储配置，请参阅 [SAP HANA Azure vm 存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)。   

[![SAP HANA 通过 HSR 和 Pacemaker 群集进行扩展](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel.png)](./media/sap-hana-high-availability-rhel/sap-hana-high-availability-scale-out-hsr-rhel-detail.png#lightbox)

在前面的关系图中，在一个 Azure 虚拟网络中表示三个子网，遵循 SAP HANA 网络建议： 
* 对于客户端通信- `client` 10.23.0.0/24  
* 对于内部 HANA 节点间通信- `inter` 10.23.1.128/26  
* 对于 HANA 系统复制- `hsr` 10.23.1.192/26  

在 `/hana/data` `/hana/log` 本地磁盘上部署和时，无需部署单独的子网和单独的虚拟网卡以与存储通信。  

Azure NetApp 卷部署在一个单独的子网中，[委托给 Azure NetApp 文件] (https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet: `anf` 10.23.1.0/26。   

## <a name="set-up-the-infrastructure"></a>设置基础结构

在接下来的说明中，假设已创建资源组，其中包含三个 Azure 网络子网的 Azure 虚拟网络： `client` `inter` 和 `hsr` 。

### <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机
1. 部署 Azure Vm。  
对于本文档中介绍的配置，请部署七个虚拟机： 
   - 三个虚拟机充当 HANA 复制站点1的 HANA DB 节点： **hana-s1-db1**、 **hana-s1-db2** 和 **HANA-s1-db3**  
   - 三个虚拟机充当 HANA 复制站点2的 HANA DB 节点： **hana-s2-db1**、 **hana-s2-db2** 和 **hana-s2-db3**  
   - 用作 *多数 maker*的小型虚拟机： **hana-s-mm**

   作为 SAP DB HANA 节点部署的 Vm 应由 SAP for HANA 在 [SAP HANA 硬件目录](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中发布。 部署 HANA DB 节点时，请确保已选择 " [加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md) "。  
  
   对于 "多数制造商" 节点，你可以部署一个小型 VM，因为此 VM 不会运行任何 SAP HANA 资源。 在群集配置中使用多数 maker VM 来实现裂脑方案中的群集节点数。 在此示例中，大多数 maker VM 只需要子网中的一个虚拟网络接口 `client` 。        

   部署和的本地托管 `/hana/data` 磁盘 `/hana/log` 。 `/hana/data` `/hana/log` [SAP HANA Azure vm 存储配置](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations-storage)中介绍了和的最低推荐存储配置。

   为虚拟网络子网中的每个 VM 部署主要网络接口 `client` 。  
   通过 Azure 门户部署 VM 时，系统会自动生成网络接口名称。 为了简单起见，我们将引用自动生成的主要网络接口，这些接口连接到 `client` Azure 虚拟网络子网作为 **hana-s1-db1**、 **hana-s1**--客户端、hana-s1- **db3-客户端**，等等。  


   > [!IMPORTANT]
   > 请确保所选择的 OS 是通过 SAP 认证的，以便 SAP HANA 你使用的特定 VM 类型。 有关这些类型的 SAP HANA 认证的 VM 类型和操作系统版本的列表，请参阅 [SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 站点。 单击列出的 VM 类型的详细信息，获取该类型 SAP HANA 支持的 OS 版本的完整列表。  
  

2. 创建六个网络接口，一个用于每个 HANA DB 虚拟机，在 `inter` 此示例的虚拟网络子网 (中，hana **-s1-** s1-s1-s1- **s1**-db3，hana- **s2-db1**-，hana-s2-) ， **hana-** **s2-** **hana-s1-db3-inter**  

3. 创建六个网络接口，一个用于每个 HANA DB 虚拟机，在 `hsr` 此示例的虚拟网络子网 (中， **hana-s1-db1-hsr**， **hana-s1-hsr**， **hana-s1-db3-** hsr，hana- **s2**-db1) - **hsr，hana**-s2 **-hsr-** db3，  

4. 将新创建的虚拟网络接口附加到相应的虚拟机：  

    a. 在 [Azure 门户](https://portal.azure.com/#home)中，请参阅虚拟机。  

    b. 在左窗格中，选择 " **虚拟机**"。 按虚拟机名称筛选 (例如， **db1**) ，然后选择虚拟机。  

    c. 在 " **概述** " 窗格中，选择 " **停止** " 以解除分配虚拟机。  

    d. 选择 " **网络**"，然后连接网络接口。 在 " **附加网络接口** " 下拉列表中，选择已为 `inter` 和子网创建的网络接口 `hsr` 。  
    
    e. 选择“保存”。 
 
    f. 对于剩余的虚拟机，请重复步骤 b 到 e， (在我们的示例中，  **hana-s1-db2**、 **hana-s1-db3**、 **db1**、 **hana-s2-db2** 和 **hana-s2-db3**) 。
 
    g. 使虚拟机暂时处于停止状态。 接下来，我们将为所有新连接的网络接口启用 [加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md) 。  

5. `inter`通过执行以下步骤，为和子网的其他网络接口启用加速网络 `hsr` ：  

    a. 在[Azure 门户](https://portal.azure.com/#home)中打开[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。  

    b. 执行以下命令，为附加到 `inter` 和子网的其他网络接口启用加速网络 `hsr` 。  

    ```
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-inter --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-inter --accelerated-networking true
    
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s1-db3-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db1-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db2-hsr --accelerated-networking true
    az network nic update --id /subscriptions/your subscription/resourceGroups/your resource group/providers/Microsoft.Network/networkInterfaces/hana-s2-db3-hsr --accelerated-networking true
    ```

7. 启动 HANA DB 虚拟机

### <a name="deploy-azure-load-balancer"></a>部署 Azure 负载均衡器

1. 建议使用标准负载均衡器。 按照以下配置步骤部署标准负载均衡器：
   1. 首先创建前端 IP 池：

      1. 打开负载均衡器，选择**前端 IP 池**，然后选择“添加”。
      1. 输入新前端 IP 池的名称（例如 **hana-frontend**）。
      1. 将 " **分配** " 设置为 " **静态** " 并输入 IP 地址 (例如 **10.23.0.18**) 。
      1. 选择“确定”。
      1. 创建新前端 IP 池后，请记下池 IP 地址。

   1. 接下来，创建一个后端池，并将所有群集 Vm 添加到后端池：

      1. 打开负载均衡器，选择**后端池**，然后选择“添加”。
      1. 输入新后端池的名称（例如 **hana-backend**）。
      1. 选择“添加虚拟机”。
      1. 选择“虚拟机”。
      1. 选择 SAP HANA 群集的虚拟机以及其子网的 IP 地址 `client` 。
      1. 选择 **添加**。

   1. 接下来创建运行状况探测：

      1. 打开负载均衡器，选择**运行状况探测**，然后选择“添加”。
      1. 输入新运行状况探测的名称（例如 **hana-hp**）。
      1. 选择“TCP”作为协议，并选择端口 625**03**。 将“间隔”值保留设置为 5，将“不正常阈”值设置为 2。 
      1. 选择“确定”。

   1. 接下来，创建负载均衡规则：
   
      1. 打开负载均衡器，选择**负载均衡规则**，然后选择“添加”。
      1. 输入新负载均衡器规则的名称（例如 hana-lb）。
      1. 选择前面创建的前端 IP 地址、后端池和运行状况探测（例如 hana-frontend、hana-backend 和 hana-hp）。
      1. 选择“HA 端口”。
      1. 将“空闲超时”增大到 30 分钟。
      1. 确保**启用浮动 IP**。
      1. 选择“确定”。

   > [!Note]
   > 如果没有公共 IP 地址的 VM 被放在内部（无公共 IP 地址）标准 Azure 负载均衡器的后端池中，就不会有出站 Internet 连接，除非执行额外的配置来允许路由到公共终结点。 有关如何实现出站连接的详细信息，请参阅 [SAP 高可用性方案中使用 Azure 标准负载均衡器的虚拟机的公共终结点连接](./high-availability-guide-standard-load-balancer-outbound-connections.md)。  


   > [!IMPORTANT]
   > 请勿在放置于 Azure 负载均衡器之后的 Azure VM 上启用 TCP 时间戳。 启用 TCP 时间戳将导致运行状况探测失败。 将参数“net.ipv4.tcp_timestamps”设置为“0”。 有关详细信息，请参阅[负载均衡器运行状况探测](../../../load-balancer/load-balancer-custom-probe-overview.md)。
   > 另请参阅 SAP 说明 [2382421](https://launchpad.support.sap.com/#/notes/2382421)。  

### <a name="deploy-the-azure-netapp-files-infrastructure"></a>部署 Azure NetApp 文件基础结构 

为文件系统部署和卷 `/hana/shared` 。 `/hana/shared`对于每个 HANA 系统复制站点，都需要一个单独的卷。 有关详细信息，请参阅 [设置 Azure NetApp 文件基础结构](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-scale-out-standby-netapp-files-rhel#set-up-the-azure-netapp-files-infrastructure)。

在此示例中，使用了以下 Azure NetApp 文件卷： 

* volume **HN1**-s1 (nfs://10.23.1.7/**HN1**-s1) 
* volume **HN1**-) **HN1**-shared-s2 (nfs://10.23.1.7/

## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

下一节中的说明以下列缩写之一作为前缀：
* **[A]**：适用于所有节点
* **[AH]**：适用于所有 HANA DB 节点
* **[M]**：适用于多数 maker 节点
* **[AH1]**：适用于站点1上的所有 HANA DB 节点
* **[AH2]**：适用于站点2上的所有 HANA DB 节点
* **[1]**：仅适用于 HANA DB 节点1，站点1
* **[2]**：仅适用于 HANA DB 节点1、站点2


通过执行以下步骤来配置和准备操作系统：

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 在此示例中，添加了以下条目 `/etc/hosts` 。  

    ```
     # Client subnet
     10.23.0.11 hana-s1-db1
     10.23.0.12 hana-s1-db1
     10.23.0.13 hana-s1-db2
     10.23.0.14 hana-s2-db1
     10.23.0.15 hana-s2-db2
     10.23.0.16 hana-s2-db3
     10.23.0.17 hana-s-mm
     # Internode subnet
     10.23.1.138 hana-s1-db1-inter
     10.23.1.139 hana-s1-db2-inter
     10.23.1.140 hana-s1-db3-inter
     10.23.1.141 hana-s2-db1-inter
     10.23.1.142 hana-s2-db2-inter
     10.23.1.143 hana-s2-db3-inter
     # HSR subnet
     10.23.1.202 hana-s1-db1-hsr
     10.23.1.203 hana-s1-db2-hsr
     10.23.1.204 hana-s1-db3-hsr
     10.23.1.205 hana-s2-db1-hsr
     10.23.1.206 hana-s2-db2-hsr
     10.23.1.207 hana-s2-db3-hsr
    ```


2. **[A]** 安装 NFS 客户端包。  

    ```yum install nfs-utils ```


3. **[AH]** 适用于 HANA 配置的 Red Hat。  

    如和中所述配置 RHEL，请参阅 <https://access.redhat.com/solutions/2447641> 以下 SAP 说明：  
   - [2292690 - SAP HANA DB: Recommended OS settings for RHEL 7](https://launchpad.support.sap.com/#/notes/2292690)（2292690 - SAP HANA DB：RHEL 7 的建议 OS 设置）
   - [2777782-SAP HANA DB：适用于 RHEL 8 的建议 OS 设置](https://launchpad.support.sap.com/#/notes/2777782)
   - [2455582-Linux：运行用 GCC 1.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2455582)
   - [2593824-Linux：运行用 GCC 7. x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2593824) 
   - [2886607-Linux：运行用 GCC 6.x 编译的 SAP 应用程序](https://launchpad.support.sap.com/#/notes/2886607)

## <a name="prepare-the-file-systems"></a>准备文件系统
### <a name="mount-the-shared-file-systems"></a>装载共享文件系统

在此示例中，共享 HANA 文件系统部署在 Azure NetApp 文件上，并装载在 NFSv4 上。  

1. **[AH]** 为 HANA 数据库卷创建装入点。  

    ```
    mkdir -p /hana/shared
    ```

2. **[AH]** 验证 NFS 域设置。 确保将域配置为默认的 Azure NetApp 文件域，即， **`defaultv4iddomain.com`** 并将映射设置为 "无 **人**"。  
   仅当使用 Azure NetAppFiles NFSv 4.1 时，才需要执行此步骤。  

    > [!IMPORTANT]
    > 确保在 VM 上的 `/etc/idmapd.conf` 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置：“`defaultv4iddomain.com`”。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 `nobody`。  

    ```
    sudo cat /etc/idmapd.conf
    # Example
    [General]
    Domain = defaultv4iddomain.com
    [Mapping]
    Nobody-User = nobody
    Nobody-Group = nobody
    ```

3. **[AH]** 验证 `nfs4_disable_idmapping` 。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。  
   仅当使用 Azure NetAppFiles NFSv 4.1 时，才需要执行此步骤。  

    ```
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.9.0.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    ```

   有关如何更改参数的详细信息 `nfs4_disable_idmapping` ，请参阅 https://access.redhat.com/solutions/1749883 。

4. **[AH1]** 在 SITE1 HANA DB Vm 上装载共享的 Azure NetApp 文件卷。  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s1 /hana/shared
    ```

5. **[AH2]** 在 SITE2 HANA DB Vm 上装载共享的 Azure NetApp 文件卷。  

    ```
    sudo mount -o rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys 10.23.1.7:/HN1-shared-s2 /hana/shared
    ```


10. **[AH]** 验证是否在 `/hana/shared/` 具有 NFS 协议版本 **NFSv4**的所有 HANA DB vm 上装载了相应的文件系统。  

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

### <a name="prepare-the-data-and-log-local-file-systems"></a>准备数据并记录本地文件系统
在所提供的配置中，文件系统 `/hana/data` 和 `/hana/log` 部署在托管磁盘上，并在本地附加到每个 HANA DB VM。 需要执行这些步骤来创建每个 HANA DB 虚拟机上的本地数据和日志卷。 

设置带有  **逻辑卷管理器 (LVM) **的磁盘布局。 下面的示例假定每个 HANA 虚拟机均附加了三个数据磁盘，这些磁盘用于创建两个卷。

1. **[AH]** 列出所有可用磁盘：
    ```
    ls /dev/disk/azure/scsi1/lun*
    ```

   示例输出：

    ```
    /dev/disk/azure/scsi1/lun0  /dev/disk/azure/scsi1/lun1  /dev/disk/azure/scsi1/lun2 
    ```

2. **[AH]** 为要使用的所有磁盘创建物理卷：
    ```
    sudo pvcreate /dev/disk/azure/scsi1/lun0
    sudo pvcreate /dev/disk/azure/scsi1/lun1
    sudo pvcreate /dev/disk/azure/scsi1/lun2
    ```

3. **[AH]** 为数据文件创建卷组。 将一个卷组用于日志文件，将另一个卷组用于 SAP HANA 的共享目录：
    ```
    sudo vgcreate vg_hana_data_HN1 /dev/disk/azure/scsi1/lun0 /dev/disk/azure/scsi1/lun1
    sudo vgcreate vg_hana_log_HN1 /dev/disk/azure/scsi1/lun2
    ```

4. **[AH]** 创建逻辑卷。 
   线性卷是使用不带 `-i` 开关的 `lvcreate` 创建的。 我们建议你创建一个带区卷，以获得更好的 I/O 性能，并将带区大小与 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)中记录的值保持一致。 `-i` 参数应表示基础物理卷的数量，`-I` 参数则表示带区大小。 在本文档中，两个物理卷用于数据卷，因此 `-i` 开关参数设置为 **2**。 数据卷的条带大小为 **256 KiB**。 一个物理卷用于日志卷，因此，`-i` 或 `-I` 开关不会显式用于日志卷命令。  

   > [!IMPORTANT]
   > `-i`如果对每个数据或日志卷使用多个物理卷，请使用开关，并将其设置为基础物理卷的编号。 创建带区卷时，请使用 `-I` 开关来指定带区大小。  
   > 有关建议的存储配置，包括带区大小和磁盘数量，请参阅 [SAP HANA VM 存储配置](./hana-vm-operations-storage.md)。  

    ```
    sudo lvcreate -i 2 -I 256 -l 100%FREE -n hana_data vg_hana_data_HN1
    sudo lvcreate -l 100%FREE -n hana_log vg_hana_log_HN1
    sudo mkfs.xfs /dev/vg_hana_data_HN1/hana_data
    sudo mkfs.xfs /dev/vg_hana_log_HN1/hana_log
    ```

5. **[AH]** 创建装载目录并复制所有逻辑卷的 UUID：
    ```
    sudo mkdir -p /hana/data/HN1
    sudo mkdir -p /hana/log/HN1
    # Write down the ID of /dev/vg_hana_data_HN1/hana_data and /dev/vg_hana_log_HN1/hana_log
    sudo blkid
    ```

6. **[AH]**`fstab`为逻辑卷创建条目并装载：
    ```
    sudo vi /etc/fstab
    ```

   将以下行插入 `/etc/fstab` 文件：

    ```
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_data_HN1-hana_data /hana/data/HN1 xfs  defaults,nofail  0  2
    /dev/disk/by-uuid/UUID of /dev/mapper/vg_hana_log_HN1-hana_log /hana/log/HN1 xfs  defaults,nofail  0  2
    ```

   装载新卷：

    ```
    sudo mount -a
    ```

## <a name="installation"></a>安装  

在此示例中，使用 Azure Vm 上的 HSR 部署扩展配置中的 SAP HANA，我们使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>准备安装 HANA

1. **[AH]** 安装 HANA 之前，请设置 root 密码。 您可以在安装完成后禁用 root 密码。 Execute as `root` 命令 `passwd` 。  

2. **[1，2]** 更改权限 `/hana/shared` 
    ```
    chmod 775 /hana/shared
    ```

3. **[1]** 验证是否可以通过 SSH 登录到此站点 **hana-s1-db2** 和 **HANA-S1-Db3**中的 HANA DB vm，而不会提示输入密码。  
   如果不是这种情况，请 [使用基于密钥的身份验证](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)中所述的 exchange ssh 密钥。  
    ```
    ssh root@hana-s1-db2
    ssh root@hana-s1-db3
    ```

4. **[2]** 验证是否可以通过 SSH 登录到此站点 **hana-s2-db2** 和 **HANA-S2-Db3**中的 HANA DB vm，而不会提示输入密码。  
   如果不是这种情况，请 [使用基于密钥的身份验证](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/6/html/deployment_guide/s2-ssh-configuration-keypairs)中所述的 exchange ssh 密钥。  
    ```
    ssh root@hana-s2-db2
    ssh root@hana-s2-db3
    ```

5. **[AH]** 安装 HANA 2.0 SP4 所需的其他包。 有关详细信息，请参阅适用于 RHEL 7 的 SAP 说明 [2593824](https://launchpad.support.sap.com/#/notes/2593824) 。 

    ```
    # If using RHEL 7
    yum install libgcc_s1 libstdc++6 compat-sap-c++-7 libatomic1
    # If using RHEL 8
    yum install libatomic libtool-ltdl.x86_64
    ```


6. **[A]** 暂时禁用防火墙，使其不会干扰 HANA 安装。 在 HANA 安装完成后，可以重新启用它。 
    ```
    # Execute as root
    systemctl stop firewalld
    systemctl disable firewalld
    ```

### <a name="hana-installation-on-the-first-node-on-each-site"></a>每个站点上第一个节点上的 HANA 安装

1. **[1]** 按照 [SAP HANA 2.0 安装和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的说明安装 SAP HANA。 在接下来的说明中，将在站点1的第一个节点上显示 SAP HANA 安装。   

   a. **hdblcm** `root` 从 HANA 安装软件目录启动 hdblcm 程序。 使用 `internal_network` 参数并传递子网的地址空间，用于内部 HANA 节点间通信。  

    ```
    ./hdblcm --internal_network=10.23.1.128/26
    ```

   b. 在提示符下，输入以下值：

     * 对于 " **选择操作**"：输入 **1** (安装) 
     * 对于 **安装的其他组件**：输入 **2、3**
     * 对于安装路径：按 Enter (默认为/hana/shared) 
     * 对于 **本地主机名**：按 enter 接受默认值
     * 对于 "**是否要将主机添加到系统？**：输入**n** "
     * 对于 **SAP HANA 系统 ID**：输入 **HN1**
     * 对于 **实例号** [00]：输入 **03**
     * 对于 **本地主机辅助角色组** [默认值]：按 enter 接受默认值
     * 对于 " **选择系统使用情况/输入索引 [4]**"：为自定义) 输入 **4** 个 (
     * 对于 **数据卷的位置** [/hana/data/HN1]：按 enter 以接受默认值
     * 对于 **日志卷的位置** [/hana/log/HN1]：按 enter 以接受默认值
     * 是否 **限制最大内存分配？** [n]：输入 **n**
     * 对于 " **主机 hana-s1-db1** [hana-s1-db1]" 的证书主机名：按 enter 以接受默认值
     * 对于 **SAP 主机代理用户 (sapadm) 密码**：输入密码
     * 对于 " **确认 SAP 主机代理用户 (sapadm) 密码**：输入密码
     * 对于 **系统管理员 (hn1adm) 密码**：输入密码
     * 对于 **系统管理员主目录** [/usr/sap/HN1/home]：按 enter 以接受默认值
     * 对于 **系统管理员登录 Shell** [/bin/sh]：按 enter 接受默认值
     * 对于 **系统管理员用户 ID** [1001]：按 enter 以接受默认值
     * 对于 **用户组的输入 ID (sapsys) ** [79]：按 enter 接受默认值
     * 对于 **系统数据库用户 (系统) 密码**：输入系统的密码
     * 对于 " **确认系统数据库用户 (系统) 密码**：输入系统的密码
     * **计算机重启后是否重新启动系统？** [n]：输入 **n** 
     * 对于 " **是否要继续 (y/n) **：验证摘要，如果一切正常，请输入 **y**

2. **[2]** 重复前面的步骤，在站点2上的第一个节点上安装 SAP HANA。   

3. **[1，2]** 验证 global.ini  

   显示 global.ini，并确保 SAP HANA 内部节点间通信的配置已就位。 验证 **通信** 部分。 它应该具有子网的地址空间 `inter` ，并且 `listeninterface` 应设置为 `.internal` 。 验证 **internal_hostname_resolution** 部分。 它应该具有属于子网的 HANA 虚拟机的 IP 地址 `inter` 。  

   ```
     sudo cat /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
     # Example from SITE1 
     [communication]
     internal_network = 10.23.1.128/26
     listeninterface = .internal
     [internal_hostname_resolution]
     10.23.1.138 = hana-s1-db1
     10.23.1.139 = hana-s1-db2
     10.23.1.140 = hana-s1-db3
   ```

4. **[1，2]** `global.ini` 在非共享环境中准备安装，如 SAP 说明 [2080991](https://launchpad.support.sap.com/#/notes/0002080991)中所述。  

   ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    [persistence]
    basepath_shared = no
   ```

4. **[1，2]** 重新启动 SAP HANA 以激活更改。  

   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem
   ```

6. **[1，2]** 验证客户端接口是否将使用子网中的 IP 地址 `client` 进行通信。  

    ```
    # Execute as hn1adm
    /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "password" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result - example from SITE 2
    "hana-s2-db1","net_publicname","10.23.0.14"
   ```

   有关如何验证配置的信息，请参阅 SAP 说明 [2183363-配置 SAP HANA 内部网络](https://launchpad.support.sap.com/#/notes/2183363)。  

7. **[AH]** 更改数据和日志目录的权限，以避免 HANA 安装错误。  

   ```
    sudo chmod o+w -R /hana/data /hana/log
   ```

8. **[1]** 安装辅助 HANA 节点。 此步骤中的示例说明适用于站点1。  
   a. 启动常驻 **hdblcm** 程序作为 `root` 。    
    ```
     cd /hana/shared/HN1/hdblcm
     ./hdblcm 
    ```

   b. 在提示符下，输入以下值：

     * 对于 " **选择操作**"：输入 **2** (添加主机) 
     * 对于 " **输入逗号分隔的主机名" 以添加**： hana-s1-db2、hana-s1-db3
     * 对于 **安装的其他组件**：输入 **2、3**
     * 对于 " **输入根用户名" [root]**：按 enter 以接受默认值
     * 对于 **"主机 ' hana-s1-db2 ' [1]**： 1 (的" 选择角色 "，辅助角色) 
     * 对于 **"输入主机 ' hana-s1-db2 ' 的主机故障转移组 [默认值]"**：按 enter 以接受默认值
     * 对于 **"输入主机 ' hana-s1-db2 ' 的存储分区号 [<<assign automatically>>]**：按 enter 以接受默认值
     * 对于 **"输入主机 ' hana-s1-db2 ' 的辅助角色组 [默认值]"**：按 enter 以接受默认值
     * 对于 " **主机 ' hana-s1-db3 ' [1]**： 1 (，辅助角色) 
     * 对于 " **输入主机 ' hana-s1-db3 ' 的主机故障转移组 [默认值]**：按 enter 以接受默认值
     * 对于 **"输入主机 ' hana-s1-db3 ' 的存储分区号 [<<assign automatically>>]**：按 enter 以接受默认值
     * 对于 **"输入主机 ' hana-s1-db3 ' 的辅助角色组" [默认值]**：按 enter 以接受默认值
     * 对于 **系统管理员 (hn1adm) 密码**：输入密码
     * 对于 " **输入 SAP 主机代理用户 (sapadm) 密码**：输入密码
     * 对于 " **确认 SAP 主机代理用户 (sapadm) 密码**：输入密码
     * 对于 **主机 hana-s1-db2** [hana-s1-db2] 的证书主机名：按 enter 以接受默认值
     * 对于 " **主机 hana-s1-db3** [hana-s1-db3]" 的证书主机名：按 enter 以接受默认值
     * 对于 " **是否要继续 (y/n) **：验证摘要，如果一切正常，请输入 **y**

9. **[2]** 重复前面的步骤以在站点2上安装辅助 SAP HANA 节点。   

## <a name="configure-sap-hana-20-system-replication"></a>配置 SAP HANA 2.0 系统复制

1. **[1]** 在站点1上配置系统复制：

   以 **hn1**adm 的身份备份数据库：

    ```
    hdbsql -d SYSTEMDB -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupSYS')"
    hdbsql -d HN1 -u SYSTEM -p "passwd" -i 03 "BACKUP DATA USING FILE ('initialbackupHN1')"
    ```

   将系统 PKI 文件复制到辅助站点：

    ```
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/data/SSFS_HN1.DAT hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/data/
    scp /usr/sap/HN1/SYS/global/security/rsecssfs/key/SSFS_HN1.KEY  hana-s2-db1:/usr/sap/HN1/SYS/global/security/rsecssfs/key/
    ```

   创建主站点：

    ```
    hdbnsutil -sr_enable --name=HANA_S1
    ```

2. **[2]** 在站点2上配置系统复制：
    
   注册第二个站点以启动系统复制。 以 <hanasid\>adm 身份运行以下命令：

    ```
    sapcontrol -nr 03 -function StopWait 600 10
    hdbnsutil -sr_register --remoteHost=hana-s1-db1 --remoteInstance=03 --replicationMode=sync --name=HANA_S2
    sapcontrol -nr 03 -function StartSystem
    ```

3. **[1]** 检查复制状态

   检查复制状态并等待，直到所有数据库都保持同步。

    ```
    sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
    # | Database | Host          | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary | Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
    # |          |               |       |              |           |         |           | Host          | Port      | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
    # | -------- | ------------- | ----- | ------------ | --------- | ------- | --------- | ------------- | --------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
    # | HN1      | hana-s1-db3   | 30303 | indexserver  |         5 |       1 | HANA_S1   | hana-s2-db3   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | SYSTEMDB | hana-s1-db1   | 30301 | nameserver   |         1 |       1 | HANA_S1   | hana-s2-db1   |     30301 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30307 | xsengine     |         2 |       1 | HANA_S1   | hana-s2-db1   |     30307 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db1   | 30303 | indexserver  |         3 |       1 | HANA_S1   | hana-s2-db1   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    # | HN1      | hana-s1-db2   | 30303 | indexserver  |         4 |       1 | HANA_S1   | hana-s2-db2   |     30303 |         2 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
    #
    # status system replication site "2": ACTIVE
    # overall system replication status: ACTIVE
    #
    # Local System Replication State
    #   
    # mode: PRIMARY
    # site id: 1
    # site name: HANA_S1
    ```

4. **[1，2]** 更改 hana 配置，以便在 hana 系统复制虚拟网络接口时进行 hana 系统复制的通信。   
   - 在两个站点上停止 HANA
    ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StopSystem HDB
    ```

   - 编辑 global.ini 为 HANA 系统复制添加主机映射：使用子网中的 IP 地址 `hsr` 。  
    ```
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [system_replication_hostname_resolution]
    10.23.1.202 = hana-s1-db1
    10.23.1.203 = hana-s1-db2
    10.23.1.204 = hana-s1-db3
    10.23.1.205 = hana-s2-db1
    10.23.1.206 = hana-s2-db2
    10.23.1.207 = hana-s2-db3
    ```

   - 在两个站点上启动 HANA
   ```
    sudo -u hn1adm /usr/sap/hostctrl/exe/sapcontrol -nr 03 -function StartSystem HDB
   ```

   有关详细信息，请参阅 [系统复制的主机名解析](https://help.sap.com/viewer/eb3777d5495d46c5b2fa773206bbfb46/1.0.12/en-US/c0cba1cb2ba34ec89f45b48b2157ec7b.html)。  

5. **[AH]** 重新启用防火墙。  
   - 重新启用防火墙
       ```
       # Execute as root
       systemctl start firewalld
       systemctl enable firewalld
       ```

   - 打开所需的防火墙端口。 需要为 HANA 实例编号调整端口。  

       > [!IMPORTANT]
       > 创建防火墙规则以允许 HANA 节点之间的通信和客户端流量。 [所有 SAP 产品的 TCP/IP 端口](https://help.sap.com/viewer/ports)上均列出了所需端口。 以下命令只是一个示例。 在此方案中，使用了系统号03。

       ```
        # Execute as root
        sudo firewall-cmd --zone=public --add-port=30301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30301/tcp
        sudo firewall-cmd --zone=public --add-port=30303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30303/tcp
        sudo firewall-cmd --zone=public --add-port=30306/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30306/tcp
        sudo firewall-cmd --zone=public --add-port=30307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30307/tcp
        sudo firewall-cmd --zone=public --add-port=30313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30313/tcp
        sudo firewall-cmd --zone=public --add-port=30315/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30315/tcp
        sudo firewall-cmd --zone=public --add-port=30317/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30317/tcp
        sudo firewall-cmd --zone=public --add-port=30340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30340/tcp
        sudo firewall-cmd --zone=public --add-port=30341/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30341/tcp
        sudo firewall-cmd --zone=public --add-port=30342/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30342/tcp
        sudo firewall-cmd --zone=public --add-port=1128/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1128/tcp
        sudo firewall-cmd --zone=public --add-port=1129/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=1129/tcp
        sudo firewall-cmd --zone=public --add-port=40302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40302/tcp
        sudo firewall-cmd --zone=public --add-port=40301/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40301/tcp
        sudo firewall-cmd --zone=public --add-port=40307/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40307/tcp
        sudo firewall-cmd --zone=public --add-port=40303/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40303/tcp
        sudo firewall-cmd --zone=public --add-port=40340/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=40340/tcp
        sudo firewall-cmd --zone=public --add-port=50313/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50313/tcp
        sudo firewall-cmd --zone=public --add-port=50314/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=50314/tcp
        sudo firewall-cmd --zone=public --add-port=30310/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30310/tcp
        sudo firewall-cmd --zone=public --add-port=30302/tcp --permanent
        sudo firewall-cmd --zone=public --add-port=30302/tcp
       ```

## <a name="create-a-pacemaker-cluster"></a>创建 Pacemaker 群集

按照[在 Azure 中的 Red Hat Enterprise Linux 上设置 Pacemaker](high-availability-guide-rhel-pacemaker.md) 的步骤，创建适用于此 HANA 服务器的基本 Pacemaker。
包括所有虚拟机，包括群集中的多数制造商。  

> [!IMPORTANT]
> 不要设置 `quorum expected-votes` 为2，因为这不是一个双节点群集。  
> 请确保 `concurrent-fencing`  已启用群集属性，以便反序列化节点。   

## <a name="create-file-system-resources"></a>创建文件系统资源

1. **[1，2]** 停止两个复制站点上的 SAP HANA。 作为 <sid adm "执行 \> 。  

    ```
    sapcontrol -nr 03 -function StopSystem
    ```

2. **[AH]**`/hana/shared`在所有 HANA DB vm 上暂时装载了用于安装的非装载文件系统。 需要停止正在使用文件系统的任何进程和会话，然后才能将其卸载。 
 
    ```
    umount /hana/shared 
    ```

3. **[1]** 创建处于禁用状态的文件系统群集资源 `/hana/shared` 。 资源是使用选项创建的 `--disabled` ，因为你必须在启用装载之前定义位置约束。  

    ```
    # /hana/shared file system for site 1
    pcs resource create fs_hana_shared_s1 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1  directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # /hana/shared file system for site 2   
    pcs resource create fs_hana_shared_s2 --disabled ocf:heartbeat:Filesystem device=10.23.1.7:/HN1-shared-s1 directory=/hana/shared \
    fstype=nfs options='defaults,rw,hard,timeo=600,rsize=262144,wsize=262144,proto=tcp,intr,noatime,sec=sys,vers=4.1,lock,_netdev' op monitor interval=20s on-fail=fence timeout=40s OCF_CHECK_LEVEL=20 \
    op start interval=0 timeout=120 op stop interval=0 timeout=120

    # clone the /hana/shared file system resources for both site1 and site2
    pcs resource clone fs_hana_shared_s1 meta clone-node-max=1 interleave=true
    pcs resource clone fs_hana_shared_s2 meta clone-node-max=1 interleave=true
    ```
 
   `OCF_CHECK_LEVEL=20` 将属性添加到监视操作，以便监视器操作在文件系统上执行读/写测试。 如果没有此属性，则监视操作仅验证是否已装载文件系统。 这可能是一个问题，因为在连接丢失时，文件系统可能会保持装载，尽管无法访问。  

   `on-fail=fence` 还会将属性添加到监视操作。 使用此选项时，如果监视操作在节点上失败，则会立即隔离该节点。 如果没有此选项，则默认行为是停止依赖于失败资源的所有资源，然后重新启动失败的资源，然后启动依赖于失败资源的所有资源。 此行为不仅会在 SAPHana 资源依赖于失败的资源时花费很长时间，还可以完全失败。 如果持有 HANA 二进制文件的 NFS 共享不可访问，则 SAPHana 资源无法成功停止。  

4. **[1]** 配置并验证节点属性。 复制站点1上的所有 SAP HANA DB 节点均为分配的属性 `S1` ，并且复制站点2上的所有 SAP HANA db 节点均为分配的属性 `S2` 。  

    ```
    # HANA replication site 1
    pcs node attribute hana-s1-db1 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db2 NFS_SID_SITE=S1
    pcs node attribute hana-s1-db3 NFS_SID_SITE=S1
    # HANA replication site 2
    pcs node attribute hana-s2-db1 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db2 NFS_SID_SITE=S2
    pcs node attribute hana-s2-db3 NFS_SID_SITE=S2
    #To verify the attribute assignment to nodes execute
    pcs node attribute
    ```

5. **[1]** 配置约束，确定 NFS 文件系统的装载位置，并启用文件系统资源。  
    ```
    # Configure the constraints
    pcs constraint location fs_hana_shared_s1-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S1
    pcs constraint location fs_hana_shared_s2-clone rule resource-discovery=never score=-INFINITY NFS_SID_SITE ne S2
    # Enable the file system resources
    pcs resource enable fs_hana_shared_s1
    pcs resource enable fs_hana_shared_s2
    ```

   启用文件系统资源后，群集将装载 `/hana/shared` 文件系统。
 
6. **[AH]** 验证是否在 `/hana/shared` 两个站点上的所有 HANA DB vm 下装载了和卷。

    ```
    sudo nfsstat -m
    # Verify that flag vers is set to 4.1 
    # Example from SITE 1, hana-s1-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s1
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.11,local_lock=none,addr=10.23.1.7
    # Example from SITE 2, hana-s2-db1
    /hana/shared from 10.23.1.7:/HN1-shared-s2
     Flags: rw,noatime,vers=4.1,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.0.14,local_lock=none,addr=10.23.1.7
    ```

7. **[1]** 配置属性资源。 如果安装了的 NFS，则配置将属性设置为的约束 `true` `hana/shared` 。  

    ```
    # Configure the attribure resources
    pcs resource create hana_nfs_s1_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s1_active
    pcs resource create hana_nfs_s2_active ocf:pacemaker:attribute active_value=true inactive_value=false name=hana_nfs_s2_active
    # Clone the attribure resources
    pcs resource clone hana_nfs_s1_active meta clone-node-max=1 interleave=true
    pcs resource clone hana_nfs_s2_active meta clone-node-max=1 interleave=true
    # Configure the constraints, which will set the attribute values
    pcs constraint order fs_hana_shared_s1-clone then hana_nfs_s1_active-clone
    pcs constraint order fs_hana_shared_s2-clone then hana_nfs_s2_active-clone
    ```

   > [!TIP]
   > 如果你的配置包含其他文件系统（除了/之外） `hana/shared` 并装载了 NFS，则包括 `sequential=false` 选项，以便在文件系统之间没有排序依赖关系。 所有 NFS 装载的文件系统都必须在相应的属性资源之前开始，但是它们不需要以彼此相对的顺序启动。 有关详细信息，请参阅 [在 HANA 文件系统为 NFS 共享时如何实现在 pacemaker 群集中配置 SAP HANA 横向扩展 HSR](https://access.redhat.com/solutions/5423971)。  

8. **[1]** 将 pacemaker 置于维护模式，以便为创建 HANA 群集资源做准备。  
    ```
    pcs property set maintenance-mode=true
    ```

## <a name="create-sap-hana-cluster-resources"></a>创建 SAP HANA 群集资源

1. **[A]** 在所有群集节点上安装 HANA 横向扩展资源代理，包括多数制造商。    

    ```
    yum install -y resource-agents-sap-hana-scaleout 
    ```


   > [!NOTE]
   > 参阅 [RHEL HA 群集的支持策略-群集中 SAP HANA 的管理](https://access.redhat.com/articles/3397471) ，适用于操作系统版本的最低支持版本的包 `resource-agents-sap-hana-scaleout` 。  

2. **[1，2]** 安装 HANA "系统复制挂钩"。 需要在每个系统复制站点上的一个 HANA DB 节点上安装该挂钩。 SAP HANA 应仍为关闭状态。        

   1. 准备挂钩为 `root` 
    ```
     mkdir -p /hana/shared/myHooks
     cp /usr/share/SAPHanaSR-ScaleOut/SAPHanaSR.py /hana/shared/myHooks
     chown -R hn1adm:sapsys /hana/shared/myHooks
    ```

   2. 适应 `global.ini`
    ```
    # add to global.ini
    [ha_dr_provider_SAPHanaSR]
    provider = SAPHanaSR
    path = /hana/shared/myHooks
    execution_order = 1
    
    [trace]
    ha_dr_saphanasr = info
    ```

3. **[AH]** 群集需要群集节点上的 sudoers 配置才能 <sid \> adm。 在此示例中，通过创建新文件来实现。 执行命令 `root` 。    
    ``` 
    cat << EOF > /etc/sudoers.d/20-saphana
    # SAPHanaSR-ScaleOut needs for srHook
     Cmnd_Alias SOK = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SOK -t crm_config -s SAPHanaSR
     Cmnd_Alias SFAIL = /usr/sbin/crm_attribute -n hana_hn1_glob_srHook -v SFAIL -t crm_config -s SAPHanaSR
     hn1adm ALL=(ALL) NOPASSWD: SOK, SFAIL
     EOF
    ```

4. **[1，2]** 在两个复制站点上启动 SAP HANA。 作为 <sid adm "执行 \> 。  

    ```
    sapcontrol -nr 03 -function StartSystem 
    ```

5. **[1]** 验证是否安装了挂钩。 \>在活动 HANA 系统复制站点上以 <sid adm 的身份执行。   

    ```
    cdtrace
     awk '/ha_dr_SAPHanaSR.*crm_attribute/ \
     { printf "%s %s %s %s\n",$2,$3,$5,$16 }' nameserver_*

     # Example entries
     # 2020-07-21 22:04:32.364379 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:46.905661 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.092016 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:52.782774 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:04:53.117492 ha_dr_SAPHanaSR SFAIL
     # 2020-07-21 22:06:35.599324 ha_dr_SAPHanaSR SOK
    ```

6. **[1]** 创建 HANA 群集资源。 执行以下命令 `root` 。    
   1. 请确保群集已处于维护模式。  
    
   2. 接下来，创建 HANA 拓扑资源。  
      如果要生成 RHEL **7、windows** 群集，请使用以下命令：  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopologyScaleOut \
       SID=HN1 InstanceNumber=03 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

      如果 **生成 RHEL 8.x** 群集，请使用以下命令：  
      ```
      pcs resource create SAPHanaTopology_HN1_HDB03 SAPHanaTopology \
       SID=HN1 InstanceNumber=03 meta clone-node-max=1 interleave=true \
       op methods interval=0s timeout=5 \
       op start timeout=600 op stop timeout=300 op monitor interval=10 timeout=600

      pcs resource clone SAPHanaTopology_HN1_HDB03 meta clone-node-max=1 interleave=true
      ```

   3. 接下来，创建 HANA 实例资源。  
      > [!NOTE]
      > 本文包含对字词 *从属*的引用，这是 Microsoft 不再使用的术语。 从软件中删除该字词后，我们会将其从本文中删除。  
 
      如果要生成 RHEL **7、windows** 群集，请使用以下命令：    
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource master msl_SAPHana_HN1_HDB03 SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```

      如果 **生成 RHEL 8.x** 群集，请使用以下命令：  
      ```
      pcs resource create SAPHana_HN1_HDB03 SAPHanaController \
       SID=HN1 InstanceNumber=03 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=false \
       op demote interval=0s timeout=320 op methods interval=0s timeout=5 \
       op start interval=0 timeout=3600 op stop interval=0 timeout=3600 op promote interval=0 timeout=3600 \
       op monitor interval=60 role="Master" timeout=700 op monitor interval=61 role="Slave" timeout=700
     
      pcs resource promotable SAPHana_HN1_HDB03 \
       meta master-max="1" clone-node-max=1 interleave=true
      ```
      > [!IMPORTANT]
      > 建议最佳做法是，仅将 AUTOMATED_REGISTER 设置为 " **否**"，同时执行彻底的故障转移测试，以防止失败的主实例自动注册为辅助实例。 故障转移测试成功完成后，将 AUTOMATED_REGISTER 设置为 **"是"**，以便在接管系统复制后可以自动恢复。 

   4. 创建虚拟 IP 和关联的资源。  
      ```
      pcs resource create vip_HN1_03 ocf:heartbeat:IPaddr2 ip=10.23.0.18 op monitor interval="10s" timeout="20s"
      sudo pcs resource create nc_HN1_03 azure-lb port=62503
      sudo pcs resource group add g_ip_HN1_03 nc_HN1_03 vip_HN1_03
      ```

   5. 创建群集约束  
      如果要生成 RHEL **7、windows** 群集，请使用以下命令：  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then msl_SAPHana_HN1_HDB03

      pcs constraint colocation add g_ip_HN1_03 with master msl_SAPHana_HN1_HDB03 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```
 
      如果 **生成 RHEL 8.x** 群集，请使用以下命令：  
      ```
      #Start HANA topology, before the HANA instance
      pcs constraint order SAPHanaTopology_HN1_HDB03-clone then SAPHana_HN1_HDB03-clone

      pcs constraint colocation add g_ip_HN1_03 with master SAPHana_HN1_HDB03-clone 4000
      #HANA resources are only allowed to run on a node, if the node's NFS file systems are mounted. The constraint also avoids the majority maker node
      pcs constraint location SAPHanaTopology_HN1_HDB03-clone rule resource-discovery=never score=-INFINITY hana_nfs_s1_active ne true and hana_nfs_s2_active ne true
      ```

7. **[1]** 将群集置于维护模式。 请确保群集状态正常，并且所有资源都已启动。  
    ```
    sudo pcs property set maintenance-mode=false
    #If there are failed cluster resources, you may need to run the next command
    pcs resource cleanup
    ```
  
   > [!NOTE]
   > 上述配置中的超时只是示例，可能需要根据特定的 HANA 设置进行调整。 例如，如果启动 SAP HANA 数据库需要较长时间，则可能需要增加启动超时。
  
## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

1. 在开始测试前，请检查群集并 SAP HANA 系统复制状态。  

   a. 验证是否不存在失败的群集操作  
     ```
     #Verify that there are no failed cluster actions
     pcs status
     # Example
     #Stack: corosync
     #Current DC: hana-s-mm (version 1.1.19-8.el7_6.5-c3c624ea3d) - partition with quorum
     #Last updated: Thu Sep 24 06:00:20 2020
     #Last change: Thu Sep 24 05:59:17 2020 by root via crm_attribute on hana-s1-db1
     #
     #7 nodes configured
     #45 resources configured
     #
     #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #
     #Active resources:
     #
     #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
     #Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
     #    Started: [ hana--s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
     #Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
     #    Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
     #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
     #    Masters: [ hana-s1-db1 ]
     #    Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
     #Resource Group: g_ip_HN1_03
     #    nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
     #    vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
     ```

   b. 验证 SAP HANA 系统复制是否同步

      ```
      # Verify HANA HSR is in sync
      sudo su - hn1adm -c "python /usr/sap/HN1/HDB03/exe/python_support/systemReplicationStatus.py"
      #| Database | Host        | Port  | Service Name | Volume ID | Site ID | Site Name | Secondary     | Secondary| Secondary | Secondary | Secondary     | Replication | Replication | Replication    |
      #|          |             |       |              |           |         |           | Host          | Port     | Site ID   | Site Name | Active Status | Mode        | Status      | Status Details |
      #| -------- | ----------- | ----- | ------------ | --------- | ------- | --------- | ------------- | -------- | --------- | --------- | ------------- | ----------- | ----------- | -------------- |
      #| HN1      | hana-s1-db3 | 30303 | indexserver  |         5 |       2 | HANA_S1   | hana-s2-db3 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db2 | 30303 | indexserver  |         4 |       2 | HANA_S1   | hana-s2-db2 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |  
      #| SYSTEMDB | hana-s1-db1 | 30301 | nameserver   |         1 |       2 | HANA_S1   | hana-s2-db1 |     30301  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30307 | xsengine     |         2 |       2 | HANA_S1   | hana-s2-db1 |     30307  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |
      #| HN1      | hana-s1-db1 | 30303 | indexserver  |         3 |       2 | HANA_S1   | hana-s2-db1 |     30303  |         1 | HANA_S2   | YES           | SYNC        | ACTIVE      |                |

      #status system replication site "1": ACTIVE
      #overall system replication status: ACTIVE

      #Local System Replication State
      #~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

      #mode: PRIMARY
      #site id: 1
      #site name: HANA_S1
      ```

2. 验证故障情况下的群集配置，当节点无法访问 NFS 共享时 (`/hana/shared`) 。  

   SAP HANA 资源代理依赖于二进制文件，它们存储在 `/hana/shared` 中，以便在故障转移过程中执行操作。 文件系统 `/hana/shared` 通过 NFS 装载到所提供的配置中。 可以执行的测试是以 `/hana/shared` *只读*方式重新装载文件系统。 如果 `/hana/shared` 活动系统复制站点上的访问权限丢失，此方法会验证群集是否会进行故障转移。  

   **预期结果**：以只读方式重新装载时 `/hana/shared` ，对文件系统执行读/写操作的监视操作将会失败，因为它无法写入文件系统并将触发 HANA 资源故障转移。 *Read only* 当 HANA 节点失去对 NFS 共享的访问权限时，会出现相同的结果。  
     
   可以通过执行或来检查群集资源的状态 `crm_mon` `pcs status` 。 开始测试之前的资源状态：
      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured

      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      #
      #Active resources:

      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s1-db1 ]
      #     Slaves: [ hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s1-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s1-db1
      ```

   若要为 `/hana/shared` 某个主复制站点 vm 模拟失败，请执行以下命令：
      ```
      # Execute as root 
      mount -o ro /hana/shared
      # Or if the above command returns an error
      sudo mount -o ro 10.23.1.7/HN1-shared-s1 /hana/shared
      ```
  
   丢失对的访问权限的 HANA VM `/hana/shared` 应根据群集配置重新启动或停止。 群集资源将迁移到其他 HANA 系统复制站点。  
         
   如果在 VM 上未启动群集，则重新启动后，通过执行以下操作启动群集： 

      ```
      # Start the cluster 
      pcs cluster start
      ```
   
   群集启动时， `/hana/shared` 将自动装入文件系统。     
   如果将 AUTOMATED_REGISTER 设置为 "false"，则需要在辅助站点上配置 SAP HANA 系统复制。 在这种情况下，你可以执行这些命令以将 SAP HANA 重新配置为辅助副本。   

      ```
      # Execute on the secondary 
      su - hn1adm
      # Make sure HANA is not running on the secondary site. If it is started, stop HANA
      sapcontrol -nr 03 -function StopWait 600 10
      # Register the HANA secondary site
      hdbnsutil -sr_register --name=HANA_S1 --remoteHost=hana-s2-db1 --remoteInstance=03 --replicationMode=sync
      # Switch back to root and cleanup failed resources
      pcs resource cleanup SAPHana_HN1_HDB03
      ```

   测试后资源的状态： 

      ```
      # Output of crm_mon
      #7 nodes configured
      #45 resources configured
    
      #Online: [ hana-s-mm hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      
      #Active resources:
    
      #rsc_st_azure    (stonith:fence_azure_arm):      Started hana-s-mm
      # Clone Set: fs_hana_shared_s1-clone [fs_hana_shared_s1]
      #    Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: fs_hana_shared_s2-clone [fs_hana_shared_s2]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: hana_nfs_s1_active-clone [hana_nfs_s1_active]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 ]
      # Clone Set: hana_nfs_s2_active-clone [hana_nfs_s2_active]
      #     Started: [ hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Clone Set: SAPHanaTopology_HN1_HDB03-clone [SAPHanaTopology_HN1_HDB03]
      #     Started: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db1 hana-s2-db2 hana-s2-db3 ]
      # Master/Slave Set: msl_SAPHana_HN1_HDB03 [SAPHana_HN1_HDB03]
      #     Masters: [ hana-s2-db1 ]
      #     Slaves: [ hana-s1-db1 hana-s1-db2 hana-s1-db3 hana-s2-db2 hana-s2-db3 ]
      # Resource Group: g_ip_HN1_03
      #     nc_HN1_03  (ocf::heartbeat:azure-lb):      Started hana-s2-db1
      #     vip_HN1_03 (ocf::heartbeat:IPaddr2):       Started hana-s2-db1
      ```


建议通过同时执行在 [RHEL 上的 Azure vm SAP HANA 的 HA](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-high-availability-rhel#test-the-cluster-setup)中记录的测试，来全面测试 SAP HANA 群集配置。


## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅 [Azure 虚拟机 (vm) 中 SAP HANA 的高可用性 ][sap-hana-ha]。
