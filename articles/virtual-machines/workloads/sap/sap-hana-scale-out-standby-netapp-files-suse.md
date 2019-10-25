---
title: 在 Azure Vm 上用备用节点 SAP HANA 扩展 SUSE Linux Enterprise Server 上的 Azure NetApp 文件 |Microsoft Docs
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
ms.date: 04/30/2019
ms.author: radeltch
ms.openlocfilehash: 11b0746c3e9e137775b2466af776b4cd9ba1e5df
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791667"
---
# <a name="sap-hana-scale-out-with-standby-node-on-azure-vms-with-azure-netapp-files-on-suse-linux-enterprise-server"></a>在 Azure Vm 上用备用节点 SAP HANA 扩展 SUSE Linux Enterprise Server 上的 Azure NetApp 文件 

[dbms-guide]:dbms-guide.md
[deployment-guide]:deployment-guide.md
[planning-guide]:planning-guide.md

[anf-azure-doc]:https://docs.microsoft.com/azure/azure-netapp-files/
[anf-avail-matrix]:https://azure.microsoft.com/global-infrastructure/services/?products=netapp&regions=all 
[anf-register]:https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register
[anf-sap-applications-azure]:https://www.netapp.com/us/media/tr-4746.pdf

[2205917]: https://launchpad.support.sap.com/#/notes/2205917
[1944799]: https://launchpad.support.sap.com/#/notes/1944799
[1928533]: https://launchpad.support.sap.com/#/notes/1928533
[2015553]: https://launchpad.support.sap.com/#/notes/2015553
[2178632]: https://launchpad.support.sap.com/#/notes/2178632
[2191498]: https://launchpad.support.sap.com/#/notes/2191498
[2243692]: https://launchpad.support.sap.com/#/notes/2243692
[1984787]: https://launchpad.support.sap.com/#/notes/1984787
[1999351]: https://launchpad.support.sap.com/#/notes/1999351
[1410736]:https://launchpad.support.sap.com/#/notes/1410736
[1900823]: https://launchpad.support.sap.com/#/notes/1900823

[sap-swcenter]:https://support.sap.com/en/my-support/software-downloads.html

[suse-ha-guide]:https://www.suse.com/products/sles-for-sap/resource-library/sap-best-practices/
[suse-drbd-guide]:https://www.suse.com/documentation/sle-ha-12/singlehtml/book_sleha_techguides/book_sleha_techguides.html
[suse-ha-12sp3-relnotes]:https://www.suse.com/releasenotes/x86_64/SLE-HA/12-SP3/

[sap-hana-ha]:sap-hana-high-availability.md
[nfs-ha]:high-availability-guide-suse-nfs.md


本文介绍如何使用[azure](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)虚拟机上的备用扩展配置在 azure 虚拟机上部署高可用性 HANA 系统，并在 azure 虚拟机上使用共享存储卷。  
在示例配置和安装命令等中，HANA 实例为**03** ，HANA 系统 ID 为**HN1**。 这些示例基于 HANA 2.0 SP4 和 SAP 12 SP4 SUSE Linux Enterprise Server。 

请先阅读以下 SAP 说明和文档：

* [Azure NetApp 文件文档][anf-azure-doc] 
* SAP 说明 [1928533]，其中包含：  
  * SAP 软件部署支持的 Azure VM 大小的列表
  * Azure VM 大小的重要容量信息
  * 支持的 SAP 软件、操作系统 (OS) 和数据库组合
  * Microsoft Azure 上 Windows 和 Linux 所需的 SAP 内核版本
* SAP 说明 [2015553] 列出了在 Azure 中 SAP 支持的 SAP 软件部署的先决条件。
* SAP 说明 [2205917] 包含适用于 SUSE Linux Enterprise Server for SAP Applications 的推荐 OS 设置
* SAP 说明[1944799]提供 Sap 应用程序的 sap 准则 SUSE Linux Enterprise Server
* SAP 说明 [2178632] 包含为 Azure 中的 SAP 报告的所有监控指标的详细信息。
* SAP 说明 [2191498] 包含 Azure 中的 Linux 所需的 SAP 主机代理版本。
* SAP 说明 [2243692] 包含 Azure 中的 Linux 上的 SAP 许可的相关信息。
* SAP 说明 [1984787] 包含有关 SUSE Linux Enterprise Server 12 的一般信息。
* SAP 说明 [1999351] 包含适用于 SAP 的 Azure 增强型监视扩展的其他故障排除信息。
* SAP 说明[1900823]包含有关 SAP HANA 存储要求的信息
* [SAP Community WIKI](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) 包含适用于 Linux 的所有必需 SAP 说明。
* [适用于 Linux 上的 SAP 的 Azure 虚拟机规划和实施][planning-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机部署][deployment-guide]
* [适用于 Linux 上的 SAP 的 Azure 虚拟机 DBMS 部署][dbms-guide]
* [SUSE SAP HA 最佳实践指南][suse-ha-guide]本指南包含在本地设置 Netweaver HA 和 SAP HANA 系统复制所需的所有信息。 请使用上述指南作为常规基准。 它们提供更多详细信息。
* [SUSE 高可用性扩展 12 SP3 发行说明][suse-ha-12sp3-relnotes]
* [使用 Azure NetApp 文件 Microsoft Azure 上的 NetApp SAP 应用程序][anf-sap-applications-azure]
* [在具有 NFS 的 NetApp 系统上 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)。 配置指南包含有关如何使用 Azure NetApp 文件提供的 NFS 设置 SAP HANA 的信息。


## <a name="overview"></a>概述

要实现 HANA 高可用性，其中一种方法是主机自动故障转移。 若要配置主机自动故障转移，请将一个或多个虚拟机添加到 HANA 系统，并将其配置为备用节点。 当活动节点发生故障时，备用节点会自动接管。 使用 Azure 虚拟机提供的配置， [Azure NetApp 文件上的 NFS](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-introduction/)即可实现此目的。  

备用节点需要有权访问所有数据库卷。 HANA 卷必须装载为 NFSv4 卷。 NFSv4 协议中经过改进的基于文件租约的锁定机制用于 `I/O` 防护。 

> [!IMPORTANT]
> 必须将 HANA 数据和日志卷部署为 NFSv 4.1 卷，并使用 NFSv 4.1 协议进行装载，才能获得支持的配置。 NFSv3 不支持带有备用节点的 HANA 主机自动故障转移配置。

![SAP NetWeaver 高可用性概述](./media/high-availability-guide-suse-anf/sap-hana-scale-out-standby-netapp-files-suse.png)

按照 SAP HANA 的网络建议，在一个 Azure 虚拟网络中创建了三个子网：用于与存储系统进行通信、用于内部 HANA 节点间通信以及用于客户端通信。 Azure NetApp 卷位于单独的子网中，[委托给 Azure Netapp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)。  

在此示例配置中，子网为：  

  - `storage` 10.23.2.0/24  
  - `hana` 10.23.3.0/24  
  - `client` 10.23.0.0/24  
  - `anf` 10.23.1.0/26  

## <a name="setting-up-the-azure-netapp-files-infrastructure"></a>设置 Azure NetApp 文件基础结构 

在继续安装 Azure NetApp 文件基础结构之前，请先熟悉[Azure Netapp 文件文档][anf-azure-doc]。 Azure NetApp 文件在多个[azure 区域](https://azure.microsoft.com/global-infrastructure/services/?products=netapp)中提供。 检查所选的 Azure 区域是否提供 Azure NetApp 文件。  

以下链接显示了 azure 区域的 azure NetApp 文件的可用性： azure [netapp 的 Azure Netapp 文件可用性][anf-avail-matrix]。  
在部署 Azure NetApp 文件之前，请在[注册 azure][anf-register]netapp 文件说明后，请求加入 Azure netapp 文件。 

### <a name="deploy-azure-netapp-files-resources"></a>部署 Azure NetApp 文件资源  

以下步骤假定你已部署[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)。 Azure NetApp 文件资源和将在其中装入 Azure NetApp 文件资源的 Vm 必须部署在同一 Azure 虚拟网络或对等互连 Azure 虚拟网络中。  

1. 如果尚未执行此操作，请请求[加入 Azure NetApp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-register)。  

2. 按照[创建 Netapp 帐户的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-netapp-account)，在所选的 Azure 区域中创建 netapp 帐户。  

3. 按照[如何设置 Azure Netapp 文件容量池的说明](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-set-up-capacity-pool)设置 Azure netapp 文件容量池。  
本文中所述的 HANA 体系结构使用单个 Azure NetApp 文件容量池，即超高服务级别。 对于 Azure 上的 HANA 工作负荷，建议 Azure NetApp 文件超或高级[服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)。  

4. 按照[说明将子网委托给 Azure Netapp 文件](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-delegate-subnet)中所述，将子网委托给 azure netapp 文件。  

5. 按照[说明为 Azure Netapp 文件创建卷](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-create-volumes)，部署 Azure netapp 文件卷。  部署卷时，请确保选择 " **nfsv 4.1**版本"。 当前访问 NFSv 4.1 需要额外的允许列表。 在指定的 Azure NetApp 文件[子网](https://docs.microsoft.com/rest/api/virtualnetwork/subnets)中部署卷。 请记住，Azure NetApp 文件资源和 Azure Vm 必须位于同一个 Azure 虚拟网络中，或者位于对等互连 Azure 虚拟网络中。 例如<b>HN1</b>-Mnt00001、 <b>HN1</b>和 mnt00001 等。是卷名称，nfs://10.23.1.5/HN1-data-mnt00001，nfs://10.23.1.4/<b>HN1</b><b>-mnt00001</b>等，是 Azure NetApp 文件卷的文件路径。  

   1. volume <b>HN1</b>-mnt00001 （nfs://10.23.1.5/<b>HN1</b>-mnt00001）
   2. volume <b>HN1</b>-mnt00002 （nfs://10.23.1.6/<b>HN1</b>-mnt00002）
   3. volume <b>HN1</b>-mnt00001 （nfs://10.23.1.4/<b>HN1</b>-mnt00001）
   4. volume <b>HN1</b>-mnt00002 （nfs://10.23.1.6/<b>HN1</b>-mnt00002）
   5. volume <b>HN1</b>（nfs://10.23.1.4/<b>HN1</b>-shared）
   
   在此示例中，我们为每个 HANA 数据和日志卷使用了不同的 Azure NetApp 文件。 对于较小或非生产系统上的更高成本优化配置，可以将所有数据装载和所有日志装载到单个卷上。  

### <a name="important-considerations"></a>重要注意事项

考虑在 SUSE 高可用性体系结构上用于 SAP Netweaver 的 Azure NetApp 文件时，请注意以下重要事项：

- 最小容量池为 4 TiB。  
- 最小卷大小为 100 GiB
- Azure NetApp 文件以及将在其中装入 Azure NetApp 文件的所有虚拟机都必须位于同一个 Azure 虚拟网络中，或位于同一区域中的[对等互连虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)。  
- 所选虚拟网络必须具有子网，并委托给 Azure NetApp 文件。
- Azure NetApp 卷的吞吐量是卷配额和服务级别的功能，如[Azure NetApp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 调整 HANA Azure NetApp 卷的大小时，请确保生成的吞吐量符合 HANA 系统要求。  
- Azure NetApp 文件提供[导出策略](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-configure-export-policy)：可控制允许的客户端、访问类型（读取 & 写入、只读等）。 
- Azure NetApp 文件功能尚不能识别区域。 目前，azure NetApp 文件功能未部署在 Azure 区域中的所有可用性区域中。 请注意某些 Azure 区域中潜在的延迟影响。  
- 将部署的虚拟机接近于 Azure NetApp 存储非常重要，以实现低延迟。 对于 SAP HANA 工作负荷低延迟是至关重要的。 与你的 Microsoft 代表合作，以确保虚拟机和 Azure NetApp 文件卷在接近邻近性时进行部署。  
- 用于虚拟机上的<b>sid</b>Adm 和组 `sapsys` Id 的用户 id 必须与 Azure NetApp 文件中的配置相匹配。 

> [!IMPORTANT]
> 对于 SAP HANA 工作负荷低延迟是至关重要的。 与你的 Microsoft 代表合作，以确保虚拟机和 Azure NetApp 文件卷在接近邻近性时进行部署。  

> [!IMPORTANT]
> 如果<b>sid</b>Adm 的用户 id 与虚拟机和 Azure NetApp 配置之间 `sapsys` 的组 id 不匹配，则在虚拟机上装载的 Azure netapp 卷上的文件的权限将显示为 `nobody`。 [在将新系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)加入 Azure NetApp 文件时，请确保为<b>Sid</b>ADM 和组 `sapsys` ID 指定正确的用户 id。

### <a name="sizing-for-hana-database-on-azure-netapp-files"></a>在 Azure NetApp 文件上调整 HANA 数据库的大小

Azure NetApp 卷的吞吐量是卷大小和服务级别的一项功能，如[Azure Netapp 文件的服务级别](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)中所述。 

在 Azure 中设计 SAP 基础结构时，应注意 SAP 的一些最低存储要求，这些要求转换为最小吞吐量特征：

- 启用读/写，/hana/log 为 250 MB/秒，大小为 1 MB i/o  
- 对于/hana/data，为 16 MB 和 64 MB i/o 大小启用至少 400 MB/秒的读取活动  
- 对于具有 16 MB 和 64 MB i/o 大小的/hana/data，至少 250 MB/秒的写入活动  

每 1 TiB 的卷配额的[Azure NetApp 文件吞吐量限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-service-levels)如下：
- 高级存储层-64 MiB/秒  
- 超存储层-128 MiB/秒  

为了满足数据和日志的 SAP 最小吞吐量要求，并根据 `/hana/shared` 的准则，建议的大小如下所示：

| 数据量(Volume) | 大小<br /> 高级存储层 | 大小<br /> 超存储层 |
| --- | --- | --- |
| /hana/log | 4 TiB | 2 TiB |
| /hana/data | 6.3 TiB | 3.2 TiB |
| /hana/shared | 每4个辅助角色节点最大（512 GB，1xRAM） | 每4个辅助角色节点最大（512 GB，1xRAM） |

本文中介绍的布局的 SAP HANA 配置使用的是 Azure NetApp 文件 Ultra 存储层，如下所示：

| 数据量(Volume) | 大小<br /> 超存储层 |
| --- | --- |
| /hana/log/mnt00001 | 2 TiB |
| /hana/log/mnt00002 | 2 TiB |
| /hana/data/mnt00001 | 3.2 TiB |
| /hana/data/mnt00002 | 3.2 TiB |
| /hana/shared | 2 TiB |

> [!NOTE]
> 此处所述的 Azure NetApp 文件大小建议旨在满足 SAP 向其基础结构提供商提供的最低要求。 在实际的客户部署和工作负载情况下，这可能不够。 使用这些建议作为起点并根据具体工作负载的要求进行调整。  

> [!TIP]
> 你可以动态地重新调整 Azure NetApp 文件卷的大小，而无需 `unmount` 卷、停止虚拟机或停止 SAP HANA。 这样就可以灵活地满足应用程序预期和无法预料的吞吐量需求。

## <a name="deploy-linux-virtual-machines-via-azure-portal"></a>通过 Azure 门户部署 Linux 虚拟机

首先，需要创建 Azure NetApp 文件卷。 在[Azure 虚拟网络](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)中创建[azure 虚拟网络子网](https://docs.microsoft.com/azure/virtual-network/virtual-network-manage-subnet)。 部署虚拟机。 创建其他网络接口，并将网络接口附加到相应的 Vm。 每个虚拟机都有三个网络接口，分别对应于三个 Azure 虚拟网络子网（`storage`、`hana` 和 `client`）。  请参阅[如何在 Azure 中创建具有多个网络接口卡的 Linux 虚拟机](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics)。  

> [!IMPORTANT]
> 对于 SAP HANA 工作负荷低延迟是至关重要的。 与你的 Microsoft 代表合作，确保虚拟机和 Azure NetApp 文件卷在接近的情况下进行了部署，以实现低延迟。 使用 SAP HANA Azure NetApp 文件时，[在使用新的 SAP HANA 系统](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxjSlHBUxkJBjmARn57skvdUQlJaV0ZBOE1PUkhOVk40WjZZQVJXRzI2RC4u)时提交所需的信息。  
> 
接下来的步骤假定已创建资源组、Azure 虚拟网络和三个 Azure 虚拟网络子网： `storage`、`hana` 和 `client`。  部署虚拟机时，请选择存储子网，以便存储网络接口是虚拟机上的主接口。 如果无法做到这一点，则必须配置到通过存储子网网关的 Azure NetApp 委托子网的显式路由。 

> [!IMPORTANT]
> 请确保你选择的 OS 已经过 SAP 针对你使用的特定 VM 类型上的 SAP HANA 进行的认证。 可以在[经 SAP HANA 认证的 IaaS 平台](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)中查找获得 SAP HANA 认证的 VM 类型和这些 VM 类型的 OS 版本列表。 请确保单击进入列出的 VM 类型的详细信息，以获取特定 VM 类型的 OS 版本支持的 SAP HANA 完整列表。  

1. 为 SAP HANA 创建可用性集。 请确保设置最大更新域。  

2. 创建三个虚拟机（**hanadb1**、 **hanadb2**、 **hanadb3**）  
   - 在 SAP HANA 支持的 Azure 库中使用使用 SLES4SAP 映像。 在此示例中，我们使用了使用 SLES4SAP 12 SP4 映像。  
   - 选择先前为 SAP HANA 创建的可用性集。  
   - 选择存储 Azure 虚拟网络子网。 选择 "[加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)"。  
部署虚拟机时，系统会自动生成网络接口名称。 我们将引用连接到存储 Azure 虚拟网络子网的网络接口，如**hanadb1**、 **hanadb2**和**hanadb3**。 
3. 为 `hana` 虚拟网络子网创建三个网络接口-每个虚拟机一个。 在此示例中： **hanadb1**、 **hanadb2**和**hanadb3**。  
4. 为**客户端**虚拟网络子网创建三个网络接口-每个虚拟机一个。 在此示例中： **hanadb1**、 **hanadb2**和**hanadb3**。  
5. 将新创建的虚拟网络接口附加到相应的虚拟机  

    1. 导航到[Azure 门户](https://portal.azure.com/#home)中的虚拟机。  
    2. 选择左侧导航窗格中的 "虚拟机"。 按虚拟机名称（例如**hanadb1**）进行筛选。 单击虚拟机。  
    3. 在 "概述" 中，选择 "停止" 以解除分配虚拟机。  
    4. 选择 "网络"，附加网络接口。 从 "附加网络接口" 下的下拉列表中选择已为 **`hana`** 和**客户**端子网创建的网络接口。  保存。 
    5. 对剩余的虚拟机重复上述步骤。 在我们的示例中为- **hanadb2**和**hanadb3**。
    6. 使虚拟机暂时处于停止状态。 接下来，我们将为所有新连接的网络接口启用[加速网络](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)。  

6. 为 **`hana`** 和 **`client`** 子网的其他网络接口启用加速网络。  

    1. 在[Azure 门户](https://portal.azure.com/#home)中打开[cloud shell](https://azure.microsoft.com/features/cloud-shell/)  
    2. 执行以下命令，为附加到 **`hana`** 和 **`client`** 子网的其他网络接口启用加速网络。  

    <pre><code>
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-hana</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-hana</b> --accelerated-networking true
    
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb1-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb2-client</b> --accelerated-networking true
    az network nic update --id /subscriptions/<b>your subscription</b>/resourceGroups/<b>your resource group</b>/providers/Microsoft.Network/networkInterfaces/<b>hanadb3-client</b> --accelerated-networking true
    </code></pre>
7. 启动虚拟机  

    1. 选择左侧导航窗格中的 "虚拟机"。 按虚拟机名称（例如**hanadb1**）进行筛选。 单击虚拟机。  
    2. 在 "概述" 中，选择 "启动"。  

## <a name="operating-system-configuration-and-preparation"></a>操作系统配置和准备

以下各项带有前缀 **[A]** -适用于所有节点， **[1]** -仅适用于节点1， **[2]** -仅适用于节点2或 **[3]** -仅适用于节点3。

1. **[A]** 维护虚拟机上的主机文件。 包括所有子网的条目。 在此示例中，已将以下条目添加到 `/etc/hosts`。  
    <pre><code>
    # Storage
    10.23.2.4   hanadb1
    10.23.2.5   hanadb2
    10.23.2.6   hanadb3
    # Client
    10.23.0.5   hanadb1-client
    10.23.0.6   hanadb2-client
    10.23.0.7   hanadb3-client
    # Hana
    10.23.3.4   hanadb1-hana
    10.23.3.5   hanadb2-hana
    10.23.3.6   hanadb3-hana
    </code></pre>

2. **[A]** 更改 DHCP 和云配置设置，以避免意外的主机名更改。  
    <pre><code>
    vi /etc/sysconfig/network/dhcp
    #Change the following DHCP setting to "no"
    DHCLIENT_SET_HOSTNAME="no"
    vi /etc/sysconfig/network/ifcfg-eth0
    # Edit ifcfg-eth0 
    #Change CLOUD_NETCONFIG_MANAGE='yes' to "no"
    CLOUD_NETCONFIG_MANAGE='no'
    </code></pre>

3. **[A]** 准备操作系统，以便在具有 Nfs 的 netapp 系统上运行 SAP HANA，如[使用 NFS 的 netapp AFF 系统配置指南 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)中所述。 创建 NetApp 配置设置的配置文件： `/etc/sysctl.d/netapp-hana.conf`。  

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

4. **[A]** 使用 Microsoft for Azure 配置设置创建配置文件： `/etc/sysctl.d/ms-az.conf`。  

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

4. **[A]** 按照[使用 NFS 配置指南的 NetApp AFF 系统上的 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)中的建议调整 sunrpc 设置。  
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

2. **[1]** 为**HN1**上的 `/usr/sap` 创建节点特定的目录。  

    <pre><code>
    # Create a temporary directory to mount  <b>HN1</b>-shared
    mkdir /mnt/tmp
    mount <b>10.23.1.4</b>:/<b>HN1</b>-shared /mnt/tmp
    cd /mnt/tmp
    mkdir shared usr-sap-<b>hanadb1</b> usr-sap-<b>hanadb2</b> usr-sap-<b>hanadb3</b>
    # unmount /hana/shared
    cd
    umount /mnt/tmp
    </code></pre>

3. **[A]** 验证 NFS 域设置。 请确保将域配置为 " **`localdomain`** "，并将 "映射" 设置为 "无**人**"。  
    <pre><code>
    sudo cat  /etc/idmapd.conf
    # Example
    [General]
    Verbosity = 0
    Pipefs-Directory = /var/lib/nfs/rpc_pipefs
    Domain = <b>localdomain</b>
    [Mapping]
    Nobody-User = <b>nobody</b>
    Nobody-Group = <b>nobody</b>
    </code></pre>

4. **[A]** 禁用 NFSv4 ID 映射。 执行装载命令以创建目录结构，其中 `nfs4_disable_idmapping` 所在的位置。  由于已为内核/驱动程序保留访问权限，你将无法在/sys/modules 下手动创建目录。  

    <pre><code>
    
    mkdir /mnt/tmp
    mount 10.23.1.4:/HN1-shared /mnt/tmp
    umount  /mnt/tmp
    # Disable NFSv4 idmapping. 
    echo "N" > /sys/module/nfs/parameters/nfs4_disable_idmapping
    </code></pre>

5. **[A]** 手动创建 SAP HANA 组和用户。 组 sapsys 和用户**hn1**Adm 的 id 必须设置为在载入过程中提供的相同 id。 此示例中的 Id 设置为**1001**。 否则，将无法访问卷。  组 sapsys 和用户帐户**hn1**adm 和 Sapadm 的 id 在所有虚拟机上必须相同。  

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

7. **[1]** 在**hanadb1**上装入节点特定的卷。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb1</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

8. **[2]** 在**hanadb2**上装入节点特定的卷。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb2</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

9. **[3]** 在**hanadb3**上装载特定于节点的卷。  
    <pre><code>
    sudo vi /etc/fstab
    # Add the following entries
    10.23.1.4:/<b>HN1</b>-shared/usr-sap-<b>hanadb3</b> /usr/sap/<b>HN1</b>  nfs   rw,vers=4,minorversion=1,hard,timeo=600,rsize=262144,wsize=262144,intr,noatime,lock,_netdev,sec=sys  0  0
    # Mount the volume
    sudo mount -a 
    </code></pre>

10. **[A]** 验证是否已使用 NFS 协议版本**NFSV4**装载所有 HANA 卷。  
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

在此示例中，使用 Azure 的备用节点在扩展配置中部署 SAP HANA，我们使用 HANA 2.0 SP4。  

### <a name="prepare-for-hana-installation"></a>准备安装 HANA

1. **[A]** 在安装 HANA 之前设置根密码（可以在安装完成后禁用 root 密码成功）。 作为 `root` 命令 `passwd` 执行。  

2. **[1]** 验证是否可以通过 ssh 连接到**hanadb2**和**hanadb3**，而不会提示输入密码。  
    <pre><code>
    ssh root@<b>hanadb2</b>
    ssh root@<b>hanadb3</b>
    </code></pre>

3. **[A]** 安装 HANA 2.0 SP4 所需的其他包。 有关详细信息，请参阅 sap 说明[2593824](https://launchpad.support.sap.com/#/notes/2593824) 。 
    <pre><code>
    sudo zypper install libgcc_s1 libstdc++6 libatomic1 
    </code></pre>

4. **[2，3]** 将 SAP HANA `data` 和 `log` 目录的所有权更改为**hn1**adm。   
    <pre><code>
    # Execute as root
    sudo chown hn1adm:sapsys /hana/data/<b>HN1</b>
    sudo chown hn1adm:sapsys /hana/log/<b>HN1</b>
    </code></pre>

### <a name="hana-installation"></a>HANA 安装

1. **[1]** 按照[SAP HANA 2.0 安装和更新指南](https://help.sap.com/viewer/2c1988d620e04368aa4103bf26f17727/2.0.04/en-US/7eb0167eb35e4e2885415205b8383584.html)中的说明安装 SAP HANA。 在此示例中，我们将安装 master、一个辅助角色和一个备用节点 SAP HANA 扩展。  
   从 HANA 安装软件目录启动**hdblcm**程序。 使用 `internal_network` 参数并传递子网的地址空间，用于内部 HANA 节点间通信。  

    <pre><code>./hdblcm --internal_network=10.23.3.0/24
    </code></pre>

   在提示符下输入以下值。

     * 选择操作：输入**1** （用于安装）
     * 选择要安装的其他组件： Enter **2、3**
     * 输入安装路径：按 Enter （默认为/hana/shared）
     * 输入本地主机名：按 Enter 接受默认值
     * 是否要将主机添加到系统？ 输入**y**
     * 输入要添加的逗号分隔的主机名： **hanadb2、hanadb3**
     * 输入根用户名 [root]：按 Enter 接受默认值
     * 输入根用户密码：输入根的密码
     * 选择主机 hanadb2 的角色：输入**1** （适用于辅助角色）
     * 输入主机 hanadb2 的主机故障转移组 [默认值]：按 Enter 接受默认值
     * 输入主机 hanadb2 的存储分区号 [<<assign automatically>>]：按 Enter 接受默认值
     * 为 host hanadb2 输入辅助角色组 [默认值]：按 Enter 接受默认值
     * 选择主机 hanadb3 的角色：输入**2** （用于备用）
     * 输入主机 hanadb3 的主机故障转移组 [默认值]：按 Enter 接受默认值
     * 为 host hanadb3 输入辅助角色组 [默认值]：按 Enter 接受默认值
     * 输入 SAP HANA 系统 ID：输入**HN1**
     * 输入实例编号 [00]：输入**03**
     * 输入本地主机辅助角色组 [默认值]：按 Enter 接受默认值
     * 选择系统使用情况/输入索引 [4]：输入**4** （对于自定义）
     * 输入数据卷的位置 [/hana/data/HN1]：按 Enter 接受默认值
     * 输入日志卷的位置 [/hana/log/HN1]：按 Enter 接受默认值
     * 是否限制最大内存分配? [n]：输入**n**
     * 输入主机 hanadb1 [hanadb1] 的证书主机名：按 Enter 接受默认值
     * 输入主机 hanadb2 [hanadb2] 的证书主机名：按 Enter 接受默认值
     * 输入主机 hanadb3 [hanadb3] 的证书主机名：按 Enter 接受默认值
     * 输入系统管理员（hn1adm）密码：输入密码
     * 输入系统数据库用户（系统）密码：输入系统的密码
     * 确认系统数据库用户（系统）密码：输入系统的密码
     * 重新引导计算机后是否重新启动系统? [n]：输入**n** 
     * 是否要继续（y/n）：验证摘要和所有内容是否正常输入**y**


2. **[1]** 验证 global.asa  

   显示 global .ini，并确保内部 SAP HANA 节点间通信的配置已就位。 验证部分**通信**。 它应该具有 **`hana`** 子网的地址空间，并且 `listeninterface` 应设置为 "`.internal`"。 验证 " **internal_hostname_resolution**" 部分。 它应该具有属于 **`hana`** 子网的 HANA 虚拟机的 IP 地址。  

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

3. **[1]** 添加主机映射，以确保客户端 IP 地址用于客户端通信。 添加 `public_host_resolution` 部分，并从客户端子网添加相应的 IP 地址。  

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

5. **[1]** 验证客户端接口是否将使用**客户**端子网中的 IP 地址进行通信。  
   <pre><code>
    sudo -u hn1adm /usr/sap/HN1/HDB03/exe/hdbsql -u SYSTEM -p "<b>password</b>" -i 03 -d SYSTEMDB 'select * from SYS.M_HOST_INFORMATION'|grep net_publicname
    # Expected result
    "<b>hanadb3</b>","net_publicname","<b>10.23.0.7</b>"
    "<b>hanadb2</b>","net_publicname","<b>10.23.0.6</b>"
    "<b>hanadb1</b>","net_publicname","<b>10.23.0.5</b>"
   </code></pre>

   有关如何验证配置的详细信息，请参阅 sap 说明[2183363-配置 SAP HANA 内部网络](https://launchpad.support.sap.com/#/notes/2183363)。  

6. 若要优化基础 Azure NetApp 文件存储的 SAP HANA，请设置以下 SAP HANA 参数：

   - `max_parallel_io_requests` **128**
   - `async_read_submit`
   - `async_write_submit_active`
   - **全部**`async_write_submit_blocks`

   有关详细信息，请参阅[NETAPP AFF Systems WITH NFS 配置指南中的 SAP HANA](https://www.netapp.com/us/media/tr-4435.pdf)。 

   从 SAP HANA 2.0 系统开始，你可以在 `global.ini` 中设置参数。 请参阅 SAP 说明[1999930](https://launchpad.support.sap.com/#/notes/1999930)。  
   对于 SAP HANA 1.0 系统（高达 SPS12 的版本），可以在安装过程中设置这些参数，如 SAP 说明[2267798](https://launchpad.support.sap.com/#/notes/2267798)中所述。  

7. Azure NetApp 文件使用的存储的文件大小限制为 16 TB。 当达到 16 TB 的文件大小限制时，SAP HANA 不会隐式识别存储限制，并且不会自动创建新的数据文件。 随着 SAP HANA 尝试将文件增长到 16 TB 以上，这将导致错误并最终导致服务器崩溃。 

   > [!IMPORTANT]
   > 若要防止 SAP HANA 尝试增长超过存储 sybsystem 的[16TB 限制](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-resource-limits)的数据文件，请在 `global.ini` 中设置以下参数。  
   > -  datavolume_striping = true
   > - datavolume_striping_size_gb = 15000 有关详细信息，请参阅 SAP 说明[2400005](https://launchpad.support.sap.com/#/notes/2400005)。
   > 请注意 SAP 说明[2631285](https://launchpad.support.sap.com/#/notes/2631285)。 

## <a name="test-sap-hana-failover"></a>测试 SAP HANA 故障转移 

1. 模拟 SAP HANA 辅助节点上的节点崩溃  

   在模拟节点崩溃之前，运行以下命令作为**hn1**adm 来捕获环境状态。  

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

   在辅助角色节点上以 root 身份运行以下命令，在本例中为**hanadb2** ，以模拟节点崩溃。  
   <pre><code>
    echo b > /proc/sysrq-trigger
   </code></pre>

   监视系统故障转移完成。 故障转移完成后，请捕获状态，其外观应如下所示。  
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
   > 若要避免 SAP HANA 故障转移的延迟，当节点遇到内核死机时，请在**所有**HANA 虚拟机上将 `kernel.panic` 设置为20秒。 此配置在 `/etc/sysctl` 中完成。 重新启动虚拟机以激活更改。 如果未执行此更改，则在节点出现内核死机时，故障转移可能需要10分钟或更长时间。  

2. 终止名称服务器  
   在测试之前，运行以下命令作为**hn1**adm 来检查环境状态：  

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

   在 active master 节点上以**hn1**adm （在本例中为**hanadb1**）上运行以下命令。  

   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   备用节点**hanadb3**将作为主节点接管。 故障转移测试完成后的资源状态：  

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

   再次开始**hanadb1**上的 HANA 实例，即在已终止名称服务器的同一虚拟机上。 **Hanadb1**节点将重新加入环境，并保留其备用角色。  
   <pre><code>
    hn1adm@hanadb1:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   在**hanadb1**上开始 SAP HANA 后，预期的状态如下：  
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

   现在，再次终止当前活动主节点上的名称服务器，即在 hanadb3 上。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB kill
   </code></pre>

   Node **hanadb1**将恢复主节点的角色。 故障转移测试完成后的状态如下所示：
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

   开始 SAP HANA **hanadb3** -它可用作备用节点。  
   <pre><code>
    hn1adm@hanadb3:/usr/sap/HN1/HDB03> HDB start
   </code></pre>

   **Hanadb3**上的 SAP HANA 开始后的状态。  
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
* 了解如何建立高可用性并规划 SAP 灾难恢复 
* Azure 上的 HANA （大型实例），请参阅[azure 上的 SAP HANA （大型实例）的高可用性和灾难恢复](hana-overview-high-availability-disaster-recovery.md)。
* 若要了解如何建立高可用性并规划 Azure Vm 上 SAP HANA 的灾难恢复，请参阅[Azure 虚拟机（vm）上的 SAP HANA 的高可用性][sap-hana-ha]
