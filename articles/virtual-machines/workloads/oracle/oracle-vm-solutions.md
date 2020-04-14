---
title: Azure 虚拟机上的 Oracle 解决方案 |微软文档
description: 了解 Microsoft Azure 上支持的 Oracle 虚拟机映像的配置和限制。
services: virtual-machines-linux
documentationcenter: ''
author: mimckitt
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: mimckitt
ms.custom: mimckitt
ms.openlocfilehash: 0a2374a4c3526b77a25f9fa8faa94c9cb0d4c4ea
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2020
ms.locfileid: "81263227"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 映像及其在 Microsoft Azure 上的部署

本文介绍有关基于 Oracle 在 Azure 应用商店中发布的虚拟机映像的 Oracle 解决方案的信息。 如果您对使用 Oracle 云基础架构的跨云应用程序解决方案感兴趣，请参阅[集成 Microsoft Azure 和 Oracle 云基础结构的 Oracle 应用程序解决方案](oracle-oci-overview.md)。

若要获取当前可用映像的列表，请运行以下命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

截至 2019 年 5 月，提供以下图像：

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

这些映像被视为“自带许可”，因此用户只需支付运行 VM 所产生的计算、存储和网络费用。  假定已获得使用 Oracle 软件的相关许可，且拥有 Oracle 最新支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 有关许可移动性的详细信息，请参阅已发布的 [Oracle 和 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 说明。 

用户还可选择将自定义映像作为解决方案的基础，自定义映像可以是用户在 Azure 中从头创建的映像，也可以是从本地环境上传的映像。

## <a name="oracle-database-vm-images"></a>Oracle 数据库 VM 映像
Oracle 支持在基于 Oracle Linux 的虚拟机映像上运行 Azure 中的 Oracle 数据库 12.1 以及更高版本的标准和企业版。  为了在 Azure 上为 Oracle 数据库的生产工作负载提供最佳性能，请确保正确调整 VM 映像的大小，并使用高级 SSD 或超 SSD 托管磁盘。 有关如何使用 Oracle 发布的 VM 映像在 Azure 中快速启动和运行 Oracle 数据库的说明，[请尝试 Oracle 数据库快速入门演练](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>附加磁盘配置选项

附加磁盘依赖于 Azure Blob 存储服务。 每个标准磁盘理论上每秒最多能够完成大约 500 个输入/输出操作 (IOPS)。 高级磁盘产品主要针对高性能数据库工作负荷，每个磁盘 IOPS 高达 5000。 如果满足性能需求，则可以使用单个磁盘。 但是，如果您使用多个附加磁盘，跨磁盘扩展数据库数据，然后使用 Oracle 自动存储管理 （ASM），则可以提高有效的 IOPS 性能。 请参阅 [Oracle 自动存储概述](https://www.oracle.com/technetwork/database/index-100339.html)，了解更多 Oracle ASM 的具体信息。 有关如何在 Linux Azure VM 上安装和配置 Oracle ASM 的示例，请参阅[安装和配置 Oracle 自动存储管理](configure-oracle-asm.md)教程。

### <a name="shared-storage-configuration-options"></a>共享存储配置选项

Azure NetApp 文件旨在满足在云中运行高性能工作负载（如数据库）的核心要求，并提供;
- Azure 本机共享 NFS 存储服务，用于通过 VM 本机 NFS 客户端或 Oracle dNFS 运行 Oracle 工作负载
- 可扩展的性能层，反映 IOPS 需求的实际范围
- 低延迟
- 高可用性、高耐用性和大规模可管理性，通常是关键任务企业工作负载（如 SAP 和 Oracle）要求的
- 快速高效的备份和恢复，实现最具侵略性的 RTO 和 RPO SLA

这些功能是可能的，因为 Azure NetApp 文件基于 NetApp® ONTAP®在 Azure 数据中心环境中运行的所有闪存系统 ， 作为 Azure 本机服务。 结果是理想的数据库存储技术，可以像其他 Azure 存储选项一样进行预配和使用。 有关如何部署和访问 Azure NetApp 文件 NFS 卷的详细信息，请参阅[Azure NetApp 文件文档](https://docs.microsoft.com/azure/azure-netapp-files/)。 有关[在 Azure NetApp 文件](https://www.netapp.com/us/media/tr-4780.pdf)上运行 Oracle 数据库的最佳做法建议，请参阅 Azure 部署最佳实践指南"使用 Azure NetApp 文件。


## <a name="licensing-oracle-database--software-on-azure"></a>许可 Oracle 数据库& Azure 上的软件
Microsoft Azure 是用于运行 Oracle 数据库的授权云环境。 在云中许可 Oracle 数据库时，Oracle 核心因子表不适用。 相反，当使用为企业版数据库启用的超线程技术的 VM 时，如果启用了超线程（如策略文档中所述），则将两个 vCPU 计算为相当于一个 Oracle 处理器许可证。 政策详情可[在此处](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)找到。
Oracle 数据库通常需要更高的内存和 IO。 因此，建议这些工作负载使用[内存优化 VM。](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) 为了进一步优化工作负载，建议对需要高内存、存储和 I/O 带宽但内核计数较高的 Oracle 数据库工作负载进行[受限核心 vCPU。](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu)

将 Oracle 软件和工作负载从本地迁移到 Microsoft Azure 时，Oracle 提供了许可证移动性，如[Azure 常见问题解答中的 Oracle](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)中所述


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle 实际应用程序群集 （Oracle RAC） 旨在缓解本地多节点群集配置中单个节点的故障。 它依赖于网络多播和共享磁盘这两项本地技术，而这两项技术并非源自超大规模公有云环境。 如果数据库解决方案在 Azure 中需要 Oracle RAC，则需要第三方软件来启用这些技术。 有关 Oracle RAC 的详细信息，请参阅[FlashGrid 天空群集页面](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项
在 Azure 中使用 Oracle 数据库时，您负责实现高可用性和灾难恢复解决方案，以避免任何停机。 

使用[数据卫士、活动数据卫士](https://www.oracle.com/database/technologies/high-availability/dataguard.html)或[Oracle GoldenGate，](https://www.oracle.com/technetwork/middleware/goldengate)在 Azure 上可实现 Oracle 数据库企业版的高可用性和灾难恢复（不依赖于 Oracle RAC），两个数据库位于两个单独的虚拟机上。 这两个虚拟机应都位于相同的[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)，以确保它们能够通过专用持久性 IP 地址相互访问。  另外，建议将虚拟机放置在同一可用性集中，以便 Azure 将它们置于单独的容错域和升级域中。 如果要具有异地冗余，请将两个数据库设置为在两个不同的区域之间复制，并将这两个实例与 VPN 网关连接。

本教程[在 Azure 上实现 Oracle 数据保护](configure-oracle-dataguard.md)，引导您完成 Azure 上的基本设置过程。  

有了 Oracle 数据防护，可以通过一个虚拟机中的主数据库、另一个虚拟机中的辅助（备用）数据库和它们之间的单向复制设置实现高可用性。 结果是对数据库副本的读取访问权限。 使用 Oracle GoldenGate，可以配置两个数据库之间的双向复制。 若要了解如何使用这些工具为数据库设置高可用性解决方案，请参阅 Oracle 网站上的[活动数据防护](https://www.oracle.com/database/technologies/high-availability/dataguard.html)和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文档。 如果需要对数据库副本的读写访问权限，可以使用 [Oracle 活动数据防护](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

在[Azure 上实现 Oracle 金门的](configure-oracle-golden-gate.md)教程将引导您完成 Azure 上的基本设置过程。

除了在 Azure 中构建 HA 和 DR 解决方案外，还应制定备份策略来还原数据库。 本教程["备份和恢复 Oracle 数据库"](oracle-backup-recovery.md)将引导您完成建立一致备份的基本过程。


## <a name="support-for-jd-edwards"></a>对 JD Edwards 的支持
根据 Oracle 支持说明[Doc ID 2178595.1，JD](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)Edwards EnterpriseOne 版本 9.2 及以上支持满足其`Minimum Technical Requirements`特定 （MTR）**的任何公共云产品**。  创建的自定义映像需要符合 OS 和软件应用程序兼容性的 MTR 规范。 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虚拟机映像

* **群集仅在 Enterprise Edition 上受支持。** 只有在使用 Oracle WebLogic 服务器的企业版时，您才有权使用 WebLogic 群集。 请勿将群集与 Oracle WebLogic 服务器标准版一起使用。
* **不支持 UDP 多播。** Azure 支持 UDP 单播，但不支持多播和广播。 Oracle WebLogic 服务器能够依赖于 Azure UDP 单播功能。 为了获得最佳结果，依靠 UDP 单播，我们建议 WebLogic 群集大小保持静态，或保留不超过 10 个托管服务器。
* **Oracle WebLogic 服务器希望 T3 访问的公共端口和专用端口相同（例如，在使用企业 JavaBeans 时）。** 请考虑一个多层方案，其中服务层 （EJB） 应用程序在 Oracle WebLogic Server 群集上运行，该群集由两个或多个 VM 组成，位于名为*SLWLS*的虚拟网络中。 客户端层位于同一虚拟网络中的不同子网中，运行一个简单的 Java 程序，试图在服务层中调用 EJB。 由于需要负载平衡服务层，因此需要为 Oracle WebLogic Server 群集中的虚拟机创建公共负载平衡终结点。 如果指定的专用端口不同于公用端口（例如，7006:7008），则会发生下面这样的错误：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   这是因为对于任何远程 T3 访问，Oracle WebLogic 服务器都希望负载均衡器端口和 WebLogic 托管服务器端口相同。 在上面的示例中，客户端访问端口 7006（负载均衡器端口），托管服务器侦听 7008（专用端口）。 此限制仅适用于 T3 访问，而不适用于 HTTP。

   若要避免此问题，请使用下列解决方法之一：

  * 对于专用于 T3 访问的负载均衡终结点，可使用相同的专用和公用端口号。
  * 启动 Oracle WebLogic 服务器时包括以下 JVM 参数：

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

如需相关信息，请参阅位于 <https://support.oracle.com> 的知识库文章 **860340.1**。

* **动态群集和负载均衡限制。** 假设您想在 Oracle WebLogic Server 中使用动态群集，并通过 Azure 中的单个公共负载平衡终结点公开它。 只要对每个托管服务器（不是动态分配从范围）使用固定端口号（而不是动态分配），并且启动的托管服务器不会超过管理员正在跟踪的计算机，就可以执行此操作。 也就是说，每个虚拟机不超过一台托管服务器）。 如果您的配置导致启动的 Oracle WebLogic 服务器多于虚拟机（即多个 Oracle WebLogic Server 实例共享同一虚拟机），则 Oracle WebLogic 服务器的这些实例中不可能绑定到给定端口号。 该虚拟机上的其他计算机失败。

   如果将管理服务器配置为自动向其托管服务器分配唯一端口号，则负载均衡将不可能，因为 Azure 不支持从单个公用端口映射到多个专用端口，而这是此配置所必需的。
* **虚拟机上的 Oracle WebLogic 服务器的多个实例。** 根据部署的要求，如果虚拟机足够大，则可以考虑在同一虚拟机上运行 Oracle WebLogic Server 的多个实例。 例如，在包含两个内核的中型虚拟机上，您可以选择运行 Oracle WebLogic Server 的两个实例。 但是，我们仍建议您避免在体系结构中引入单点故障，如果您只使用一台运行 Oracle WebLogic Server 多个实例的虚拟机，则就是这种情况。 使用至少两个虚拟机可能是一种更好的方法，然后每个虚拟机可以运行 Oracle WebLogic Server 的多个实例。 Oracle WebLogic 服务器的每个实例可能仍属于同一群集。 但是，当前无法使用 Azure 来负载平衡由同一虚拟机中的此类 Oracle WebLogic Server 部署公开的终结点，因为 Azure 负载均衡器要求负载平衡服务器在唯一虚拟机之间分配。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虚拟机映像
* **JDK 6 和 7 最新更新。** 尽管建议使用最新公开支持的 Java 版本（当前为 Java 8），但 Azure 还提供 JDK 6 和 7 的映像。 这适用于尚未准备好升级到 JDK 8 的旧版应用程序。 虽然对旧版 JDK 映像的更新不再提供给公众，但是鉴于 Microsoft 与 Oracle 的合作关系，由 Azure 提供的 JDK 6 和 7 的映像将包含最近的非公开更新，该更新通常由 Oracle 只向一组 Oracle 支持的选定客户提供。 随着时间推移，将使用 JDK 6 和 7 的更新版本提供 JDK 映像的新版本。

   JDK 6 和 7 映像中的 JDK 以及从这些映像派生的虚拟机和映像只能在 Azure 中使用。
* **64 位 JDK。** Azure 提供的 Oracle WebLogic Server 虚拟机映像和 Oracle JDK 虚拟机映像包含 64 位版本的 Windows Server 和 JDK。

## <a name="next-steps"></a>后续步骤
现在，您可以根据 Microsoft Azure 中的虚拟机映像概述当前的 Oracle 解决方案。 下一步是在 Azure 上部署第一个 Oracle 数据库。

> [!div class="nextstepaction"]
> [在 Azure 上创建 Oracle 数据库](oracle-database-quick-create.md)
