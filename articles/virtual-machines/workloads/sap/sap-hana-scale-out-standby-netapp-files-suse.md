---
title: 在 SLES 上通过 Azure NetApp 文件 SAP HANA 扩展Microsoft Docs
description: 了解如何通过使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure Vm 上使用备用节点部署 SAP HANA 扩展系统。
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
ms.date: 04/24/2020
ms.author: radeltch
ms.openlocfilehash: 21d4af6985dbe246e60fe95f8f03de7f8aa0501b
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/25/2020
ms.locfileid: "91314056"
---
# <a name="deploy-a-sap-hana-scale-out-system-with-standby-node-on-azure-vms-by-using-azure-netapp-files-on-suse-linux-enterprise-server"></a>使用 SUSE Linux Enterprise Server 上的 Azure NetApp 文件在 Azure VM 上部署具有备用节点的 SAP HANA 横向扩展系统 

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

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介绍如何在 Azure 虚拟机上使用 azure 虚拟机上的备用扩展配置中的高可用性 SAP HANA 系统部署 (Vm) 通过使用 [Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-introduction.md) 来共享存储卷。  

在示例配置和安装命令等中，HANA 实例为 **03** ，HANA 系统 ID 为 **HN1**。 这些示例基于 HANA 2.0 SP4 和 SAP 12 SP4 SUSE Linux Enterprise Server。 

在开始之前，请参阅以下 SAP 说明和文章：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533] 包括：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上的 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553]：列出 AZURE 中 sap 支持的 sap 软件部署的先决条件
* SAP 说明 [2205917]：包含适用于 SAP 应用程序的 SUSE Linux Enterprise Server 的建议 OS 设置
* SAP 说明 [1944799]：包含适用于 Sap 应用程序的 sap 准则 SUSE Linux Enterprise Server
* SAP 说明 [2178632]：包含有关 Azure 中的 SAP 报告的所有监视指标的详细信息
* SAP 说明 [2191498]：包含 Azure 中的 Linux 所需的 SAP 主机代理版本
* SAP 说明 [2243692]：包含有关 Azure 中 Linux 上的 SAP 许可的信息
* SAP 说明 [1984787]：包含有关 SUSE Linux Enterprise Server 12 的一般信息
* SAP 说明 [1999351]：包含适用于 SAP 的 Azure 增强型监视扩展的其他疑难解答信息
* SAP 说明 [1900823]：包含有关 SAP HANA 存储要求的信息
* [SAP 社区 Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)：包含适用于 Linux 的所有必需 SAP 说明
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳实践指南][suse-ha-guide]：包含设置 NetWeaver 高可用性的所有必需信息，以及 SAP HANA 本地复制 (要用作一般基线;它们提供了更详细的信息) 
* [SUSE 高可用性扩展 12 SP3 发行说明][suse-ha-12sp3-relnotes]
* [使用 Azure NetApp 文件的 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]


## <a name="overview"></a>概述

实现 HANA 高可用性的一种方法是配置主机自动故障转移。 若要配置主机自动故障转移，请将一个或多个虚拟机添加到 HANA 系统，并将其配置为备用节点。 当活动节点发生故障时，备用节点会自动接管。 在 Azure 虚拟机的提供配置中，你可以通过 [在 Azure NetApp 文件上使用 NFS](../../../azure-netapp-files/azure-netapp-files-introduction.md)来实现自动故障转移。  

> [!NOTE]
> 备用节点需要访问所有数据库卷。 HANA 卷必须装载为 NFSv4 卷。 NFSv4 协议中经过改进的基于文件租约的锁定机制用于进行 `I/O` 防护。 

> [!IMPORTANT]
> 若要生成受支持的配置，必须将 HANA 数据和日志卷部署为 NFSv 4.1 卷，并使用 NFSv 4.1 协议进行装载。 NFSv3 不支持带有备用节点的 HANA 主机自动故障转移配置。

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

在前面的关系图中，SAP HANA 网络建议后，三个子网会在一个 Azure 虚拟网络中表示： 
* 有关客户端通信
* 用于与存储系统进行通信
* 对于内部 HANA 节点间通信

Azure NetApp 卷位于单独的子网中， [委托给 Azure Netapp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)。  

在此示例配置中，子网为：  

  - `client` 10.23.0.0/24  
  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `anf` 10.23.1.0/26  

## <a name="set-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

在继续安装 Azure NetApp 文件基础结构之前，请先熟悉 [Azure Netapp 文件文档][anf-azure-doc]。 

Azure NetApp 文件在多个 [azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 查看所选 Azure 区域是否提供 Azure NetApp 文件。  

有关 azure 区域的 Azure NetApp 文件可用性的信息，请参阅 azure [区域的 Azure Netapp 文件可用性][anf-avail-matrix]。  

部署 Azure NetApp 文件之前，请通过 [注册 Azure netapp 文件说明][anf-register]，请求加入 Azure netapp 文件。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

以下说明假定你已部署 [Azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)。 Azure NetApp 文件资源和将在其中装载 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。  

1. 如果尚未部署资源，请请求 [加入 Azure NetApp 文件](../../../azure-netapp-files/azure-netapp-files-register.md)。  

2. 按照 [创建 netapp 帐户](../../../azure-netapp-files/azure-netapp-files-create-netapp-account.md)中的说明，在所选的 Azure 区域中创建一个 netapp 帐户。  

3. 按照 [设置 Azure Netapp 文件容量池](../../../azure-netapp-files/azure-netapp-files-set-up-capacity-pool.md)中的说明设置 Azure netapp 文件容量池。  

   本文中提供的 HANA 体系结构在 *超服务* 级别使用单个 Azure NetApp 文件容量池。 对于 Azure 上的 HANA 工作负荷，建议使用 Azure NetApp 文件*Ultra*或*高级*[服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)。  

4. 按照将 [子网委托给 Azure Netapp 文件](../../../azure-netapp-files/azure-netapp-files-delegate-subnet.md)中的说明，将子网委托给 Azure netapp 文件。  

5. 按照 [为 Azure Netapp 文件创建 NFS 卷](../../../azure-netapp-files/azure-netapp-files-create-volumes.md)中的说明部署 Azure netapp 文件量。  

   部署卷时，请确保选择 **nfsv 4.1** 版本。 目前，需要将对 NFSv 4.1 的访问权限添加到允许列表中。 将卷部署在指定的 Azure NetApp 文件[子网](/rest/api/virtualnetwork/subnets)中。 将自动分配 Azure NetApp 卷的 IP 地址。 
   
   请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中，或者位于对等互连 Azure 虚拟网络中。 例如， **HN1**-Mnt00001、 **HN1**和 mnt00001 等，是卷名称和 nfs://10.23.1.5/**HN1**-data-mnt00001、nfs://10.23.1.4/**HN1**-mnt00001 等，是 Azure NetApp 文件卷的文件路径，等等。  

   * volume **HN1**-data-mnt00001 (nfs://10.23.1.5/**HN1**-mnt00001) 
   * volume **HN1**-data-mnt00002 (nfs://10.23.1.6/**HN1**-mnt00002) 
   * volume **HN1**-mnt00001 (nfs://10.23.1.4/**HN1**-mnt00001) 
   * volume **HN1**-mnt00002 (nfs://10.23.1.6/**HN1**-mnt00002) 
   * volume **HN1**-shared (nfs://10.23.1.4/**HN1**-shared) 
   
   在此示例中，我们为每个 HANA 数据和日志卷使用了单独的 Azure NetApp 文件量。 对于较小或非生产系统的更具成本优化的配置，可以将所有数据装载和所有日志装载到单个卷上。  

### <a name="important-considerations"></a>重要注意事项

在 SUSE 高可用性体系结构上创建适用于 SAP NetWeaver 的 Azure NetApp 文件时，请注意以下重要事项：

- 最小容量池为 4 tib (TiB) 。  
- 最小卷大小为 100 gb (GiB) 。
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机必须位于同一区域中的同一 Azure 虚拟网络或 [对等互连虚拟网络](../../../virtual-network/virtual-network-peering-overview.md) 中。  
- 所选虚拟网络必须具有委托给 Azure NetApp 文件的子网。
- Azure NetApp 文件量的吞吐量是卷配额和服务级别的功能，如 [Azure NetApp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 当调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量符合 HANA 系统要求。  
- 使用 Azure NetApp 文件 [导出策略](../../../azure-netapp-files/azure-netapp-files-configure-export-policy.md)，可以控制允许的客户端、访问类型 (读写、只读等) 。 
- Azure NetApp 文件功能尚未识别区域。 目前，该功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域的潜在延迟影响。  
-  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 与 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在邻近的地方部署。  

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>调整 Azure NetApp 文件上的 HANA 数据库的大小

Azure NetApp 文件量的吞吐量是卷大小和服务级别的一项功能，如 [Azure Netapp 文件的服务级别](../../../azure-netapp-files/azure-netapp-files-service-levels.md)中所述。 

在 Azure 中设计 SAP 基础结构时，请注意 SAP 所需的一些最低存储要求，这些要求转换为最小吞吐量特征：

- 启用读写的/hana/log 为 250 mb/秒 (MB/s) ，大小为 1 MB。  
- 为/hana/data 提供至少 400 MB/s 的读取活动，以提供 16 MB 和 64-MB i/o 大小。  
- 为具有 16 MB 和 64-MB i/o 大小的/hana/data 启用至少 250 MB/s 的写入活动。 

每 1 TiB 的卷配额的 [Azure NetApp 文件吞吐量限制](../../../azure-netapp-files/azure-netapp-files-service-levels.md)为：
- 高级存储层-64 MiB/秒  
- 超存储层-128 MiB/秒  

为了满足数据和日志的 SAP 最小吞吐量要求以及/hana/shared 的准则，建议的大小为：

| Volume | 大小<br>高级存储层 | 大小<br>超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- | --- |
| /hana/log/ | 4 TiB | 2 TiB | v4.1 |
| /hana/data | 6.3 TiB | 3.2 TiB | v4.1 |
| /hana/shared | 最大 (512 GB，1xRAM) 每4个工作节点 | 最大 (512 GB，1xRAM) 每4个工作节点 | v3 或 v4.1 |

本文使用 Azure NetApp 文件 Ultra 存储层提供的布局的 SAP HANA 配置如下：

| Volume | 大小<br>超级存储层 | 支持的 NFS 协议 |
| --- | --- | --- |
| /hana/log/mnt00001 | 2 TiB | v4.1 |
| /hana/log/mnt00002 | 2 TiB | v4.1 |
| /hana/data/mnt00001 | 3.2 TiB | v4.1 |
| /hana/data/mnt00002 | 3.2 TiB | v4.1 |
| /hana/shared | 2 TiB | v3 或 v4.1 |

> [!NOTE]
> 此处所述的 Azure NetApp 文件大小调整建议旨在满足 SAP 为其基础结构提供商建议的最低要求。 在实际的客户部署和工作负载情况下，这些大小可能不够用。 请将这些建议作为起点，并根据具体工作负载的要求进行调整。  

> [!TIP]
> 你可以动态调整 Azure NetApp 文件卷的大小，而无需 *卸载* 卷、停止虚拟机或停止 SAP HANA。 这种方法可以灵活地满足应用程序的预期和无法预料的吞吐量需求。

## <a name="deploy-linux-virtual-machines-via-the-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机

首先，需要创建 Azure NetApp 文件卷。 然后执行以下步骤：
1. 在[azure 虚拟网络](../../../virtual-network/virtual-networks-overview.md)中创建[azure 虚拟网络子网](../../../virtual-network/virtual-network-manage-subnet.md)。 
1. 部署 VM。 
1. 创建其他网络接口，并将网络接口附加到相应的 Vm。  

   每个虚拟机都有三个网络接口，分别对应于三个 Azure 虚拟网络子网 (`client` `storage` 和 `hana`) 。 

   有关详细信息，请参阅 [在 Azure 中创建具有多个网络接口卡的 Linux 虚拟机](../../linux/multiple-nics.md)。  

> [!IMPORTANT]
> 对于 SAP HANA 工作负载，低延迟至关重要。 若要实现低延迟，请与 Microsoft 代表合作，以确保虚拟机和 Azure NetApp 文件卷在接近附近部署。 当你使用 SAP HANA Azure NetApp 文件 [加入新 SAP HANA 系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u) 时，请提交所需的信息。 
 
以下说明假定你已创建资源组、Azure 虚拟网络和三个 Azure 虚拟网络子网： `client` 、 `storage` 和 `hana` 。 部署 Vm 时，请选择 "客户端子网"，使客户端网络接口成为 Vm 上的主接口。 还需要通过存储子网网关配置到 Azure NetApp 文件委托子网的显式路由。 

> [!IMPORTANT]
> 请确保所选择的 OS 是通过 SAP 认证的，以便 SAP HANA 你使用的特定 VM 类型。 有关这些类型的 SAP HANA 认证的 VM 类型和操作系统版本的列表，请参阅 [SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) 站点。 单击列出的 VM 类型的详细信息，获取该类型 SAP HANA 支持的 OS 版本的完整列表。  

1. 为 SAP HANA 创建可用性集。 请确保设置最大更新域。  

2. 执行以下步骤，创建三个虚拟机 (**hanadb1**， **hanadb2**， **hanadb3**) ：  

   a. 在 SAP HANA 支持的 Azure 库中使用使用 SLES4SAP 映像。 在此示例中，我们使用了使用 SLES4SAP 12 SP4 映像。  

   b. 选择之前为 SAP HANA 创建的可用性集。  

   c. 选择客户端 Azure 虚拟网络子网。 选择 " [加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md)"。  

   部署虚拟机时，系统会自动生成网络接口名称。 在这些说明中，为简单起见，我们将引用附加到客户端 Azure 虚拟网络子网的自动生成的网络接口，如 **hanadb1**、 **hanadb2**和 **hanadb3**。 

3. 为虚拟网络子网创建三个网络接口，每个虚拟机对应一个虚拟 `storage` 网络子网 (在此示例中为 **hanadb1**、 **hanadb2**和 **hanadb3 存储**) 。  

4. 为虚拟网络子网创建三个网络接口，每个虚拟机对应于 `hana`  虚拟网络子网 (在此示例中为 **hanadb1**、 **hanadb2**和) **hanadb3** 。  

5. 执行以下步骤，将新创建的虚拟网络接口附加到相应的虚拟机：  

    a. 在 [Azure 门户](https://portal.azure.com/#home)中，请参阅虚拟机。  

    b. 在左窗格中，选择 " **虚拟机**"。 按虚拟机名称筛选 (例如 **hanadb1**) ，然后选择虚拟机。  

    c. 在 " **概述** " 窗格中，选择 " **停止** " 以解除分配虚拟机。  

    d. 选择 " **网络**"，然后连接网络接口。 在 " **附加网络接口** " 下拉列表中，选择已为 `storage` 和子网创建的网络接口 `hana` 。  
    
    e. 选择“保存”。 
 
    f. 对于剩余的虚拟机，重复步骤 b 到 e， (在我们的示例中，  **hanadb2** 和 **hanadb3**) 。
 
    g. 使虚拟机暂时处于停止状态。 接下来，我们将为所有新连接的网络接口启用 [加速网络](../../../virtual-network/create-vm-accelerated-networking-cli.md) 。  

6. `storage`通过执行以下步骤，为和子网的其他网络接口启用加速网络 `hana` ：  

    a. 在[Azure 门户](https://portal.azure.com/#home)中打开[Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) 。  

    b. 执行以下命令，为附加到 `storage` 和子网的其他网络接口启用加速网络 `hana` 。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-storage</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-storage</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true

    </code></pre>

7. 执行以下步骤，启动虚拟机：  

    a. 在左窗格中，选择 " **虚拟机**"。 按虚拟机名称筛选 (例如 **hanadb1**) ，然后选择它。  

    b. 在 " **概述** " 窗格中，选择 " **启动**"。  

## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

下一节中的说明以下列其中一项为前缀：
* **[A]**：适用于所有节点
* **[1]**：仅适用于节点1
* **[2]**：仅适用于节点2
* **[3]**：仅适用于节点3

通过执行以下步骤来配置和准备操作系统：

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 在此示例中，添加了以下条目 `/etc/hosts` 。  

    <pre><code>
    # Storage
    10.23.2.4   hanadb1-storage
    10.23.2.5   hanadb2-storage
    10.23.2.6   hanadb3-storage
    # Client
    10.23.0.5   hanadb1
    10.23.0.6   hanadb2
    10.23.0.7   hanadb3
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** 更改用于存储的网络接口的 DHCP 和云配置设置，以避免意外的主机名更改。  

    以下说明假定存储网络接口是 `eth1` 。 

    <pre><code>
    vi /etc/sysconfig/network/dhcp
    # Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-<b>eth1</b>
    # Edit ifcfg-eth1 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

2. **[A]** 添加网络路由，以便与 Azure NetApp 文件的通信通过存储网络接口进行。  

    以下说明假定存储网络接口是 `eth1` 。  

    <pre><code>
    vi /etc/sysconfig/network/ifroute-<b>eth1</b>
    # Add the following routes 
    # RouterIPforStorageNetwork - - -
    # ANFNetwork/cidr RouterIPforStorageNetwork - -
    <b>10.23.2.1</b> - - -
    <b>10.23.1.0/26</b> <b>10.23.2.1</b> - -
    </code></pre>

    重新启动 VM 以激活更改。  

3. **[A]** 如 [使用 Azure netapp 文件 Microsoft Azure 上的 Netapp SAP 应用程序][anf-sap-applications-azure]中所述，准备好使用 NFS 在 netapp 系统上运行 SAP HANA 的操作系统。 为 NetApp 配置设置创建配置文件 */etc/sysctl.d/netapp-hana.conf* 。  

    <pre><code>
    vi /etc/sysctl.d/netapp-hana.conf
    # Add the following entries in the configuration file
    net.core.rmem_max = 16777216
    net.core.wmem_max = 16777216
    net.core.rmem_default = 16777216
    net.core.wmem_default = 16777216
    net.core.optmem_max = 16777216
    net.ipv4.tcp_rmem = 65536 16777216 16777216
    net.ipv4.tcp_wmem = 65536 16777216 16777216
    net.core.netdev_max_backlog = 300000
    net.ipv4.tcp_slow_start_after_idle=0
    net.ipv4.tcp_no_metrics_save = 1
    net.ipv4.tcp_moderate_rcvbuf = 1
    net.ipv4.tcp_window_scaling = 1
    net.ipv4.tcp_timestamps = 1
    net.ipv4.tcp_sack = 1
    </code></pre>

4. **[A]** 使用 Microsoft for Azure 配置设置创建配置文件 */etc/sysctl.d/ms-az.conf* 。  

    <pre><code>
    vi /etc/sysctl.d/ms-az.conf
    # Add the following entries in the configuration file
    ipv6.conf.all.disable_ipv6 = 1
    net.ipv4.tcp_max_syn_backlog = 16348
    net.ipv4.ip_local_port_range = 40000 65300
    net.ipv4.conf.all.rp_filter = 0
    sunrpc.tcp_slot_table_entries = 128
    vm.swappiness=10
    </code></pre>

4. **[A]** 按照 [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]中的建议调整 sunrpc 设置。  

    <pre><code>
    vi /etc/modprobe.d/sunrpc.conf
    # Insert the following line
    options sunrpc tcp_max_slot_table_entries=128
    </code></pre>

## <a name="mount-the-azure-netapp-files-volumes"></a>装载 Azure NetApp 文件卷

1. **[A]** 创建 HANA 数据库卷的装入点。  

    <pre><code>
    mkdir -p /hana/data/<b>HN1</b>/mnt00001
    mkdir -p /hana/data/<b>HN1</b>/mnt00002
    mkdir -p /hana/log/<b>HN1</b>/mnt00001
    mkdir -p /hana/log/<b>HN1</b>/mnt00002
    mkdir -p /hana/shared
    mkdir -p /usr/sap/<b>HN1</b>
    </code></pre>

2. **[1]** 为 **HN1**上的/usr/sap 创建节点特定的目录。  

    <pre><code>
    # Create a temporary directory to mount <b>HN1</b>-shared
    mkdir /mnt/tmp
    # if using NFSv3 for this volume, mount with the following command
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    # if using NFSv4.1 for this volume, mount with the following command
    mount -t nfs -o sec=sys,vers=4.1 <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 验证 NFS 域设置。 请确保域配置为默认的 Azure NetApp 文件域（即“`defaultv4iddomain.com`”），并且映射设置为“无人”。  

    > [!IMPORTANT]
    > 确保在 VM 上的 `/etc/idmapd.conf` 中设置 NFS 域，以匹配 Azure NetApp 文件上的默认域配置：“`defaultv4iddomain.com`”。 如果 NFS 客户端（即 VM）上的域配置和 NFS 服务器（即 Azure NetApp 配置）之间不匹配，则 VM 上已装载的 Azure NetApp 卷上文件的权限将显示为 `nobody`。  

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

4. **[A]** 验证 `nfs4_disable_idmapping`。 它应设置为“Y”。若要创建 `nfs4_disable_idmapping` 所在的目录结构，请执行 mount 命令。 无法在 /sys/modules 下手动创建目录，因为访问权限是为内核/驱动程序保留的。  

    <pre><code>
    # Check nfs4_disable_idmapping 
    cat /sys/module/nfs/parameters/nfs4_disable_idmapping
    # If you need to set nfs4_disable_idmapping to Y
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    echo "Y" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    # Make the configuration permanent
    echo "options nfs nfs4_disable_idmapping=Y" >> /etc/modprobe.d/nfs.conf
    </code></pre>

5. **[A]** 手动创建 SAP HANA 组和用户。 组 sapsys 和用户 **hn1**Adm 的 id 必须设置为在载入过程中提供的相同 id。  (在此示例中，Id 设置为 **1001**) 。如果 id 设置不正确，则将无法访问卷。 所有虚拟机上的组 sapsys 和用户帐户 **hn1**adm 和 Sapadm 的 id 必须相同。  

    <pre><code>
    # Create user group 
    sudo groupadd -g 1001 sapsys
    # Create  users 
    sudo useradd <b>hn1</b>adm -u 1001 -g 1001 -d /usr/sap/<b>HN1</b>/home -c "SAP HANA Database System" -s /bin/sh
    sudo useradd sapadm -u 1002 -g 1001 -d /home/sapadm -c "SAP Local Administrator" -s /bin/sh
    # Set the password  for both user ids
    sudo passwd hn1adm
    sudo passwd sapadm
    </code></pre>

6. **[A]** 装载共享的 Azure NetApp 文件卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.5:/<b>HN1</b>-data-mnt00001 /hana/data/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-data-mnt00002 /hana/data/<b>HN1</b>/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-log-mnt00001 /hana/log/<b>HN1</b>/mnt00001  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.6:/<b>HN1</b>-log-mnt00002 /hana/log/HN1/mnt00002  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    10.23.1.4:/<b>HN1</b>-shared/shared /hana/shared  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount all volumes
    sudo mount -a 
    </code></pre>

7. **[1]** 在 **hanadb1**上装入节点特定的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 在 **hanadb2**上装入节点特定的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 在 **hanadb3**上装载特定于节点的卷。  

    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 验证是否已使用 NFS 协议版本 **NFSV4**装载所有 HANA 卷。  

    <pre><code>
    sudo nfsstat -m
    # Verify that flag vers is set to <b>4.1</b> 
    # Example from <b>hanadb1</b>
    /hana/data/<b>HN1</b>/mnt00001 from 10.23.1.5:/<b>HN1</b>-data-mnt00001
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.5
    /hana/log/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-log-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/data/<b>HN1</b>/mnt00002 from 10.23.1.6:/<b>HN1</b>-data-mnt00002
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.6
    /hana/log/<b>HN1</b>/mnt00001 from 10.23.1.4:/<b>HN1</b>-log-mnt00001
    Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /usr/sap/<b>HN1</b> from 10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b>
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    /hana/shared from 10.23.1.4:/<b>HN1</b>-shared/shared
     Flags: rw,noatime,vers=<b>4.1</b>,rsize=262144,wsize=262144,namlen=255,hard,proto=tcp,timeo=600,retrans=2,sec=sys,clientaddr=10.23.2.4,local_lock=none,addr=10.23.1.4
    </code></pre>

## <a name="installation"></a>安装  

在此示例中，使用 Azure 的备用节点在扩展配置中部署 SAP HANA，我们使用了 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>准备安装 HANA

1. **[A]** 安装 HANA 之前，请设置 root 密码。 您可以在安装完成后禁用 root 密码。 Execute as `root` 命令 `passwd` 。  

2. **[1]** 验证是否可以通过 SSH 登录到 **hanadb2** 和 **hanadb3**，而不会提示输入密码。  

    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安装 HANA 2.0 SP4 所需的其他包。 有关详细信息，请参阅 SAP 说明 [2593824](https://launchpad.support.sap.com/#/notes/2593824)。 

    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2]，[3]** 将 SAP HANA 和目录的所有权更改 `data` `log` 为 **hn1**adm。   

    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 安装

1. **[1]** 按照 [SAP HANA 2.0 安装和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的说明安装 SAP HANA。 在此示例中，我们将安装 master、一个辅助角色和一个备用节点 SAP HANA 扩展。  

   a. 从 HANA 安装软件目录启动 **hdblcm** 程序。 使用 `internal_network` 参数并传递子网的地址空间，用于内部 HANA 节点间通信。  

    <pre><code>
    ./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   b. 在提示符下，输入以下值：

     * 对于 " **选择操作**"：输入 **1** (安装) 
     * 对于 **安装的其他组件**：输入 **2、3**
     * 对于安装路径：按 Enter (默认为/hana/shared) 
     * 对于 **本地主机名**：按 enter 接受默认值
     * 在 "**是否要将主机添加到系统？**：输入**y** "
     * **要添加逗号分隔的主机名**：输入**hanadb2、hanadb3**
     * 对于 **Root 用户名** [root]：按 enter 接受默认值
     * 对于 **Root 用户密码**：输入根用户的密码
     * 对于 "主机 hanadb2" 的角色：为辅助角色) 输入 **1**  个 (
     * 对于主机 hanadb2 的 **主机故障转移组** [默认值]：按 enter 接受默认值
     * 对于主机 hanadb2 的 **存储分区号** [<<assign automatically>>]：按 enter 接受默认值
     * 对于主机 hanadb2 的 **辅助角色组** [默认值]：按 enter 接受默认值
     * 对于 "主机 hanadb3 的 **Select 角色** "：输入 **2** (用于备用) 
     * 对于主机 hanadb3 的 **主机故障转移组** [默认值]：按 enter 接受默认值
     * 对于主机 hanadb3 的 **辅助角色组** [默认值]：按 enter 接受默认值
     * 对于 **SAP HANA 系统 ID**：输入 **HN1**
     * 对于 **实例号** [00]：输入 **03**
     * 对于 **本地主机辅助角色组** [默认值]：按 enter 接受默认值
     * 对于 " **选择系统使用情况/输入索引 [4]**"：为自定义) 输入 **4** 个 (
     * 对于 **数据卷的位置** [/hana/data/HN1]：按 enter 以接受默认值
     * 对于 **日志卷的位置** [/hana/log/HN1]：按 enter 以接受默认值
     * 是否 **限制最大内存分配？** [n]：输入 **n**
     * 对于 Host hanadb1 [hanadb1] 的 **证书主机名** ：按 enter 以接受默认值
     * 对于 Host hanadb2 [hanadb2] 的 **证书主机名** ：按 enter 以接受默认值
     * 对于 Host hanadb3 [hanadb3] 的 **证书主机名** ：按 enter 以接受默认值
     * 对于 **系统管理员 (hn1adm) 密码**：输入密码
     * 对于 **系统数据库用户 (系统) 密码**：输入系统的密码
     * 对于 " **确认系统数据库用户 (系统) 密码**：输入系统的密码
     * **计算机重启后是否重新启动系统？** [n]：输入 **n** 
     * 对于 " **是否要继续 (y/n) **：验证摘要，如果一切正常，请输入 **y**


2. **[1]** 验证 global.ini  

   显示 global.ini，并确保 SAP HANA 内部节点间通信的配置已就位。 验证 **通信** 部分。 它应该具有子网的地址空间 `hana` ，并且 `listeninterface` 应设置为 `.internal` 。 验证 **internal_hostname_resolution** 部分。 它应该具有属于子网的 HANA 虚拟机的 IP 地址 `hana` 。  

   <pre><code>
    sudo cat /usr/sap/<b>HN1</b>/SYS/global/hdb/custom/config/global.ini
    # Example 
    #global.ini last modified 2019-09-10 00:12:45.192808 by hdbnameserve
    [communication]
    internal_network = <b>10.23.3/24</b>
    listeninterface = .internal
    [internal_hostname_resolution]
    <b>10.23.3.4</b> = <b>hanadb1</b>
    <b>10.23.3.5</b> = <b>hanadb2</b>
    <b>10.23.3.6</b> = <b>hanadb3</b>
   </code></pre>

3. **[1]** 添加主机映射，以确保客户端 IP 地址用于客户端通信。 添加部分 `public_host_resolution` ，并从客户端子网添加相应的 IP 地址。  

   <pre><code>
    sudo vi /usr/sap/HN1/SYS/global/hdb/custom/config/global.ini
    #Add the section
    [public_hostname_resolution]
    map_<b>hanadb1</b> = <b>10.23.0.5</b>
    map_<b>hanadb2</b> = <b>10.23.0.6</b>
    map_<b>hanadb3</b> = <b>10.23.0.7</b>
   </code></pre>

4. **[1]** 重新启动 SAP HANA 以激活更改。  

   <pre><code>
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StopSystem HDB
    sudo -u <b>hn1</b>adm /usr/sap/hostctrl/exe/sapcontrol -nr <b>03</b> -function StartSystem HDB
   </code></pre>

5. **[1]** 验证客户端接口是否将使用子网中的 IP 地址 `client` 进行通信。  

   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   有关如何验证配置的信息，请参阅 SAP 说明 [2183363-配置 SAP HANA 内部网络](https://launchpad.support.sap.com/#/notes/2183363)。  

6. 若要优化基础 Azure NetApp 文件存储的 SAP HANA，请设置以下 SAP HANA 参数：

   - `max_parallel_io_requests`**128**
   - `async_read_submit`**开启**
   - `async_write_submit_active`**开启**
   - `async_write_submit_blocks`**全部**

   有关详细信息，请参阅 [使用 Azure NetApp 文件 Microsoft Azure 上的 NETAPP SAP 应用程序][anf-sap-applications-azure]。 

   从 SAP HANA 2.0 系统开始，你可以在中设置参数 `global.ini` 。 有关详细信息，请参阅 SAP 说明 [1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   
   对于 SAP HANA 1.0 systems SPS12 及更早版本，可以在安装过程中设置这些参数，如 SAP 说明 [2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp 文件使用的存储的文件大小限制为 16 tb (TB) 。 SAP HANA 不会隐式识别存储限制，在达到 16 TB 的文件大小限制时，它不会自动创建新的数据文件。 随着 SAP HANA 尝试将文件增长到超过 16 TB，此尝试将导致错误，并最终导致索引服务器崩溃。 

   > [!IMPORTANT]
   > 若要防止 SAP HANA 尝试增长超过 [16 TB](../../../azure-netapp-files/azure-netapp-files-resource-limits.md) 存储子系统限制的数据文件，请在中设置以下参数 `global.ini` 。  
   > - datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 有关详细信息，请参阅 SAP 说明 [2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 请注意 SAP 说明 [2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

1. 模拟 SAP HANA 辅助节点上的节点崩溃。 执行以下操作： 

   a. 在模拟节点崩溃之前，请运行以下命令作为 **hn1**adm 来捕获环境状态：  

   <pre><code>
    # Check the landscape status
    python /usr/sap/<b>HN1</b>/HDB<b>03</b>/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
   </code></pre>

   b. 若要模拟节点崩溃，请在辅助角色节点上以 root 身份运行以下命令，在本例中为 **hanadb2** ：  
   
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   c. 监视系统故障转移完成。 故障转移完成后，请捕获状态，如下所示：  

    <pre><code>
    # Check the instance status
    sapcontrol -nr <b>03</b>  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
    # Check the landscape status
    /usr/sap/HN1/HDB03/exe/python_support> python landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | no     | info   |          |        |         2 |         0 | default  | default  | master 2   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb3 | yes    | info   |          |        |         0 |         2 | default  | default  | master 3   | slave      | standby     | slave       | standby | worker  | default | default |
   </code></pre>

   > [!IMPORTANT]
   > 节点遇到内核死机时，通过 `kernel.panic` 在 *所有* HANA 虚拟机上设置为20秒来避免 SAP HANA 故障转移的延迟。 此配置在中完成 `/etc/sysctl` 。 重新启动虚拟机以激活更改。 如果未执行此更改，则当某个节点遇到内核死机时，故障转移可能需要10分钟或更长时间。  

2. 通过执行以下操作来终止名称服务器：

   a. 在测试之前，请通过运行以下命令作为 **hn1**adm 来检查环境的状态：  

   <pre><code>
    #Landscape status 
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
   </code></pre>

   b. 在活动主节点上作为 **hn1**adm 运行以下命令，在本例中为 **hanadb1** ：  

    <pre><code>
        hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
    </code></pre>
    
    备用节点 **hanadb3** 将作为主节点接管。 下面是故障转移测试完成后的资源状态：  

    <pre><code>
        # Check the instance status
        sapcontrol -nr 03 -function GetSystemInstanceList
        GetSystemInstanceList
        OK
        hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
        hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
        hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GRAY
        hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
        # Check the landscape status
        python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
        | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
        |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
        |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
        | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
        | hanadb1 | no     | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
        | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
        | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
    </code></pre>

   c. 重新启动 **hanadb1** (上的 HANA 实例，该实例在) 的名称服务器被终止的同一虚拟机上。 **Hanadb1**节点将重新加入环境，并保留其备用角色。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在 **hanadb1**上开始 SAP HANA 后，将需要以下状态：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03 -function GetSystemInstanceList
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GREEN
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | info   |          |        |         1 |         0 | default  | default  | master 1   | slave      | worker      | standby     | worker  | standby | default | -       |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | yes    | info   |          |        |         0 |         1 | default  | default  | master 3   | master     | standby     | master      | standby | worker  | default | default |
   </code></pre>

   d. 同样，请在当前活动的主节点上终止名称服务器， (即节点 **hanadb3**) 上。  
   
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1** 将恢复主节点的角色。 故障转移测试完成后，状态将如下所示：

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

   e. 启动 **hanadb3**上的 SAP HANA，它将准备好用作备用节点。  

   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在 **hanadb3**上开始 SAP HANA 后，状态如下所示：  

   <pre><code>
    # Check the instance status
    sapcontrol -nr 03  -function GetSystemInstanceList & python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    GetSystemInstanceList
    OK
    hostname, instanceNr, httpPort, httpsPort, startPriority, features, dispstatus
    hanadb1, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb2, 3, 50313, 50314, 0.3, HDB|HDB_WORKER, GREEN
    hanadb3, 3, 50313, 50314, 0.3, HDB|HDB_STANDBY, GRAY
    # Check the landscape status
    python /usr/sap/HN1/HDB03/exe/python_support/landscapeHostConfiguration.py
    | Host    | Host   | Host   | Failover | Remove | Storage   | Storage   | Failover | Failover | NameServer | NameServer | IndexServer | IndexServer | Host    | Host    | Worker  | Worker  |
    |         | Active | Status | Status   | Status | Config    | Actual    | Config   | Actual   | Config     | Actual     | Config      | Actual      | Config  | Actual  | Config  | Actual  |
    |         |        |        |          |        | Partition | Partition | Group    | Group    | Role       | Role       | Role        | Role        | Roles   | Roles   | Groups  | Groups  |
    | ------- | ------ | ------ | -------- | ------ | --------- | --------- | -------- | -------- | ---------- | ---------- | ----------- | ----------- | ------- | ------- | ------- | ------- |
    | hanadb1 | yes    | ok     |          |        |         1 |         1 | default  | default  | master 1   | master     | worker      | master      | worker  | worker  | default | default |
    | hanadb2 | yes    | ok     |          |        |         2 |         2 | default  | default  | master 2   | slave      | worker      | slave       | worker  | worker  | default | default |
    | hanadb3 | no     | ignore |          |        |         0 |         0 | default  | default  | master 3   | slave      | standby     | standby     | standby | standby | default | -       |
   </code></pre>

## <a name="next-steps"></a>后续步骤

* [适用于 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅 [Azure 虚拟机 (vm) 中 SAP HANA 的高可用性 ][sap-hana-ha]。
