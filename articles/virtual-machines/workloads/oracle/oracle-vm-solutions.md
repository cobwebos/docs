---
title: Azure 虚拟机上的 oracle 解决方案 |Microsoft Docs
description: 了解有关支持的配置和限制的 Microsoft Azure 上的 Oracle 虚拟机映像。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 9dd7f7d07b34ed3c1076b46c0bf5185d6c8cd31a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "67074229"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM 映像和 Microsoft Azure 上部署

本文介绍如何基于发布的 Azure Marketplace 中的 Oracle 虚拟机映像的 Oracle 解决方案有关的信息。 如果想在 Oracle 云基础结构的跨云应用程序解决方案，请参阅[Oracle 应用程序解决方案将 Microsoft Azure 和 Oracle 云基础结构集成](oracle-oci-overview.md)。

若要获取当前可用映像的列表，请运行以下命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

截至 2019 年 5 是可用的以下映像：

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

## <a name="support-for-jd-edwards"></a>对 JD Edwards 的支持
根据 Oracle 支持说明[Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)，JD Edwards EnterpriseOne 版本 9.2 和更高版本上支持**任何公共云产品/服务**满足其特定`Minimum Technical Requirements`(MTR)。  创建的自定义映像需要符合 OS 和软件应用程序兼容性的 MTR 规范。 

## <a name="oracle-database-vm-images"></a>Oracle 数据库 VM 映像
Oracle 支持在基于 Oracle Linux 的虚拟机映像上的 Azure 中运行 Oracle DB 12.1 标准版和企业版。  为使 Oracle DB 的生产工作负荷在 Azure 上表现最佳，请务必将 VM 映像调整到合适的大小并使用由高级存储支持的托管磁盘。 有关如何使用 Oracle 发布的 VM 映像在 Azure 中快速启动并运行 Oracle DB 的说明，[请查看 Oracle DB 快速入门演练](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>附加磁盘配置选项

附加磁盘依赖于 Azure Blob 存储服务。 每个标准磁盘理论上每秒最多能够完成大约 500 个输入/输出操作 (IOPS)。 高级磁盘产品主要针对高性能数据库工作负荷，每个磁盘 IOPS 高达 5000。 如果满足性能需求，可以使用单个磁盘。 但是，如果使用多个附加的磁盘、 数据库数据分散到它们，然后使用 Oracle 自动存储管理 (ASM) 可以提高有效 IOPS 性能。 请参阅 [Oracle 自动存储概述](https://www.oracle.com/technetwork/database/index-100339.html)，了解更多 Oracle ASM 的具体信息。 有关如何安装和配置 Linux Azure VM 上的 Oracle ASM 的示例，请参阅[安装和配置 Oracle 自动存储管理](configure-oracle-asm.md)教程。

### <a name="shared-storage-configuration-options"></a>共享的存储的配置选项

Azure 的 NetApp 文件旨在满足在云中运行如数据库的高性能工作负荷的核心要求，并提供;
- Azure 本机共享 NFS 存储服务用于运行 Oracle 工作负荷是通过本机 NFS 客户端 VM 或 Oracle dNFS
- 反映真实世界范围内的 IOPS 需求的可缩放的性能层
- 低延迟
- 高可用性、 高持续性和可管理性在规模较大，通常所要求的任务关键企业工作负荷 （如 SAP 和 Oracle）
- 快速、 高效地备份和恢复，以实现最高的 RTO 和 RPO SLA

因为 Azure NetApp 文件基于 Azure 数据中心环境-作为本机 Azure 服务中运行的 NetApp ONTAP® 所有闪存系统，可使用这些功能。 结果是一种理想的数据库存储技术，可以预配和使用就像其他 Azure 存储选项一样。 请参阅[Azure NetApp 文件文档](https://docs.microsoft.com/azure/azure-netapp-files/)有关如何部署和访问 Azure NetApp 文件 NFS 卷上的详细信息。 请参阅[Azure 部署最佳实践指南使用 Azure NetApp 文件上的 Oracle](https://www.netapp.com/us/media/tr-4780.pdf)有关操作 Azure NetApp 文件上的 Oracle 数据库的最佳做法建议。


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real RAC 可用于减少本地多节点群集配置中单一节点的故障。 它依赖于网络多播和共享磁盘这两项本地技术，而这两项技术并非源自超大规模公有云环境。 如果数据库解决方案需要在 Azure 中的 Oracle RAC，需要第三方软件来启用这些技术。 有关 Oracle RAC 的详细信息，请参阅[FlashGrid SkyCluster 页](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项
当在 Azure 中使用 Oracle 数据库，你负责实现高可用性和灾难恢复解决方案，以避免任何停机。 

高可用性和灾难恢复 Oracle Database Enterprise edition （而不依赖于 Oracle RAC） 可以实现 Azure 上使用[数据防护、 活动数据防护](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)，或[Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)，其中两个单独的虚拟机上的两个数据库。 这两个虚拟机应都位于相同的[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)，以确保它们能够通过专用持久性 IP 地址相互访问。  另外，建议将虚拟机放置在同一可用性集中，以便 Azure 将它们置于单独的容错域和升级域中。 如果希望具有异地冗余，请设置两个不同区域之间复制并将两个实例连接到 VPN 网关的两个数据库。

本教程[在 Azure 上实现 Oracle Data Guard](configure-oracle-dataguard.md)将指导你完成基本安装过程在 Azure 上。  

有了 Oracle 数据防护，可以通过一个虚拟机中的主数据库、另一个虚拟机中的辅助（备用）数据库和它们之间的单向复制设置实现高可用性。 结果是对数据库副本的读取访问权限。 使用 Oracle GoldenGate，可以配置两个数据库之间的双向复制。 若要了解如何使用这些工具为数据库设置高可用性解决方案，请参阅 Oracle 网站上的[活动数据防护](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html)和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文档。 如果需要对数据库副本的读写访问权限，可以使用 [Oracle 活动数据防护](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

本教程[在 Azure 上实现 Oracle GoldenGate](configure-oracle-golden-gate.md)将指导你完成基本安装过程在 Azure 上。

除了有在 Azure 中构建了 HA 和 DR 解决方案，应备份策略来将数据库还原。 本教程[备份和恢复 Oracle 数据库](oracle-backup-recovery.md)将指导你完成建立一致备份的基本过程。

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虚拟机映像

* **群集仅在 Enterprise Edition 上受支持。** 仅在使用 WebLogic Server 的 Enterprise Edition 时许可使用 WebLogic 群集。 不要将群集用于 WebLogic Server 标准版。
* **不支持 UDP 多播。** Azure 支持 UDP 单播，但不支持多播和广播。 WebLogic Server 能够依赖于 Azure 的 UDP 单播功能。 为依赖于 UDP 单播的最佳结果，我们建议将 WebLogic 群集大小保留静态，或保留与不超过 10 个托管服务器。
* **WebLogic Server 预期 T3 访问（例如，使用企业 JavaBean 时）的公用端口和专用端口是相同的。** 请考虑多层方案，其中服务层 (EJB) 应用程序运行在 WebLogic Server 群集包含两个或多个 Vm，在名为的虚拟网络中的*SLWLS*。 客户端层位于不同的子网在同一虚拟网络中运行尝试调用服务层中的 EJB 的简单 Java 程序。 有必要进行负载平衡服务层，因为公共负载均衡终结点需要为 WebLogic Server 群集中的虚拟机创建。 如果指定的专用端口不同于公用端口（例如，7006:7008），则会发生下面这样的错误：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   这是因为对于任何远程 T3 访问，WebLogic Server 都要求负载均衡器端口和 WebLogic 托管服务器端口是相同的。 在上面的示例中，客户端访问端口 7006（负载均衡器端口），托管服务器侦听 7008（专用端口）。 此限制仅适用于 T3 访问，而不适用于 HTTP。

   若要避免此问题，请使用下列解决方法之一：

  * 对于专用于 T3 访问的负载均衡终结点，可使用相同的专用和公用端口号。
  * 启动 WebLogic Server 时，请包括以下 JVM 参数：

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

如需相关信息，请参阅位于 <https://support.oracle.com> 的知识库文章 **860340.1**。

* **动态群集和负载均衡限制。** 假设要在 WebLogic Server 中使用动态群集并通过 Azure 中单个公共负载均衡终结点公开它。 这可以，只要对每个 （不能动态分配范围中） 的托管服务器使用固定的端口号并不会启动更多的托管的服务器不是有管理员正在跟踪的计算机。 也就是说，没有每个虚拟机的多个托管的服务器）。 如果配置导致更多多于存在的虚拟机正在启动的 WebLogic 服务器 （也就是说，多个 WebLogic Server 实例共享同一个虚拟机），则不能为多个 WebLogic 服务器的这些实例中的若要将绑定到给定的端口号。 虚拟机上的其他故障。

   如果将管理服务器配置为自动向其托管服务器分配唯一端口号，则负载均衡将不可能，因为 Azure 不支持从单个公用端口映射到多个专用端口，而这是此配置所必需的。
* **多个 WebLogic Server 实例的虚拟机上。** 根据你的部署要求，可以考虑多个 WebLogic Server 实例运行在同一个虚拟机，如果虚拟机足够大。 例如，在包含两个内核的中等大小虚拟机上，可以选择运行两个 WebLogic Server 实例。 但是，我们仍建议您避免在体系结构，它会出现此情况，如果你使用正在运行多个 WebLogic Server 实例的只是一个虚拟机中引入单点故障。 使用至少两个虚拟机可更好的方法，并且每个虚拟机可以运行多个 WebLogic Server 实例。 每个 WebLogic Server 实例可能仍处于同一个群集的一部分。 但是，目前不能使用 Azure 负载均衡终结点中同一个虚拟机，此类 WebLogic Server 部署公开的因为 Azure 负载均衡器需要将负载均衡服务器分布在唯一虚拟机。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虚拟机映像
* **JDK 6 和 7 最新更新。** 尽管建议使用最新公开支持的 Java 版本（当前为 Java 8），但 Azure 还提供 JDK 6 和 7 的映像。 这适用于尚未准备好升级到 JDK 8 的旧版应用程序。 虽然对旧版 JDK 映像的更新不再提供给公众，但是鉴于 Microsoft 与 Oracle 的合作关系，由 Azure 提供的 JDK 6 和 7 的映像将包含最近的非公开更新，该更新通常由 Oracle 只向一组 Oracle 支持的选定客户提供。 随着时间推移，将使用 JDK 6 和 7 的更新版本提供 JDK 映像的新版本。

   仅可以在 Azure 中使用的 JDK 6 和 7 的映像和虚拟机和映像以及从中派生在提供的 JDK。
* **64 位 JDK。** Azure 提供的 Oracle WebLogic Server 虚拟机映像和 Oracle JDK 虚拟机映像包含 64 位版本的 Windows Server 和 JDK。

## <a name="next-steps"></a>后续步骤
现在已概要当前基于 Microsoft Azure 中虚拟机映像的 Oracle 解决方案。 下一步是部署在 Azure 上的将首个 Oracle 数据库。

> [!div class="nextstepaction"]
> [在 Azure 上创建 Oracle 数据库](oracle-database-quick-create.md)
