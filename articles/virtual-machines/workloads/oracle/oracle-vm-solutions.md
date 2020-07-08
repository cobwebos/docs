---
title: Azure 虚拟机上的 Oracle 解决方案 | Microsoft Docs
description: 了解 Microsoft Azure 上 Oracle 虚拟机映像支持的配置和限制。
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/12/2020
ms.author: borisb
ms.openlocfilehash: b9f4fdb0cc750fdee1fe34694656f5252e16ba5e
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2020
ms.locfileid: "85552095"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure 上的 Oracle VM 映像及其部署

本文介绍了以 Oracle 在 Azure 市场中发布的虚拟机映像为基础的 Oracle 解决方案的相关信息。 如果对包含 Oracle 云基础结构的跨云应用程序解决方案感兴趣，请参阅[集成 Microsoft Azure 和 Oracle 云基础结构的 Oracle 应用程序解决方案](oracle-oci-overview.md)。

若要获取当前可用映像的列表，请运行以下命令：

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

截至2020年6月，提供以下映像：

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
oracle-database-19-3    Oracle       oracle-db-19300         Oracle:oracle-database-19-3:oracle-db-19300:19.3.0           19.3.0
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.00                             6.10.00
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.0                                6.8.0
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190529                         6.8.20190529
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.0                                6.9.0
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190529                         6.9.20190529
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.0                                7.3.0
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190529                         7.3.20190529
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.1                                7.4.1
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190529                         7.4.20190529
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.1                                7.5.1
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.2                                7.5.2
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190529                         7.5.20190529
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.2                                7.6.2
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.3                                7.6.3
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.4                                7.6.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.1                                 7.7.1
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.2                                 7.7.2
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.3                                 7.7.3
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.4                                 7.7.4
Oracle-Linux            Oracle       77                      Oracle:Oracle-Linux:77:7.7.5                                 7.7.5
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.01                             7.7.01
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.02                             7.7.02
Oracle-Linux            Oracle       77-ci                   Oracle:Oracle-Linux:77-ci:7.7.03                             7.7.03
Oracle-Linux            Oracle       78                      Oracle:Oracle-Linux:78:7.8.01                                7.8.01
Oracle-Linux            Oracle       8                       Oracle:Oracle-Linux:8:8.0.2                                  8.0.2
Oracle-Linux            Oracle       8-ci                    Oracle:Oracle-Linux:8-ci:8.0.11                              8.0.11
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.0                                 8.1.0
Oracle-Linux            Oracle       81                      Oracle:Oracle-Linux:81:8.1.2                                 8.1.2
Oracle-Linux            Oracle       81-ci                   Oracle:Oracle-Linux:81-ci:8.1.0                              8.1.0
Oracle-Linux            Oracle       81-gen2                 Oracle:Oracle-Linux:81-gen2:8.1.11                           8.1.11
Oracle-Linux            Oracle       ol77-ci-gen2            Oracle:Oracle-Linux:ol77-ci-gen2:7.7.1                       7.7.1
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.01                         7.7.01
Oracle-Linux            Oracle       ol77-gen2               Oracle:Oracle-Linux:ol77-gen2:7.7.02                         7.7.02
Oracle-Linux            Oracle       ol78-gen2               Oracle:Oracle-Linux:ol78-gen2:7.8.11                         7.8.11
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
weblogic-122130-jdk8u3  Oracle       owls-122130-8u131-ol73  Oracle:weblogic-122130-jdk8u131-ol73:owls-122130-8u131-ol7   1.1.6
weblogic-122130-jdk8u4  Oracle       owls-122130-8u131-ol74  Oracle:weblogic-122130-jdk8u131-ol74:owls-122130-8u131-ol7   1.1.1
weblogic-122140-jdk8u6  Oracle       owls-122140-8u251-ol76  Oracle:weblogic-122140-jdk8u251-ol76:owls-122140-8u251-ol7   1.1.1
weblogic-141100-jdk116  Oracle       owls-141100-11_07-ol76  Oracle:weblogic-141100-jdk11_07-ol76:owls-141100-11_07-ol7   1.1.1
weblogic-141100-jdk8u6  Oracle       owls-141100-8u251-ol76  Oracle:weblogic-141100-jdk8u251-ol76:owls-141100-8u251-ol7   1.1.1
```

这些映像被视为“自带许可”，因此用户只需支付运行 VM 所产生的计算、存储和网络费用。  假定已获得使用 Oracle 软件的相关许可，且拥有 Oracle 最新支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 有关许可移动性的详细信息，请参阅已发布的 [Oracle 和 Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 说明。

用户还可选择将自定义映像作为解决方案的基础，自定义映像可以是用户在 Azure 中从头创建的映像，也可以是从本地环境上传的映像。

## <a name="oracle-database-vm-images"></a>Oracle 数据库 VM 映像

Oracle 支持在基于 Oracle Linux 的虚拟机映像上的 Azure 中运行 Oracle Database 12.1 以及更高版本的标准版和企业版。  为使 Oracle Database 的生产工作负荷在 Azure 上表现最佳，请务必将 VM 映像调整到合适的大小并使用高级 SSD 或超级 SSD 托管磁盘。 有关如何使用 Oracle 发布的 VM 映像在 Azure 中快速启动并运行 Oracle Database 的说明，[请查看 Oracle Database 快速入门演练](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>附加磁盘配置选项

附加磁盘依赖于 Azure Blob 存储服务。 每个标准磁盘理论上每秒最多能够完成大约 500 个输入/输出操作 (IOPS)。 高级磁盘产品主要针对高性能数据库工作负荷，每个磁盘 IOPS 高达 5000。 如果满足性能需求，则可以使用单个磁盘。 然而，如果改用多个附加磁盘，将数据库数据分散到这些磁盘上，然后使用 Oracle 自动存储管理 (ASM)，则可以提高有效的 IOPS 性能。 请参阅 [Oracle 自动存储概述](https://www.oracle.com/technetwork/database/index-100339.html)，了解更多 Oracle ASM 的具体信息。 有关如何在 Linux Azure VM 上安装和配置 Oracle ASM 的示例，请参阅[安装和配置 Oracle 自动存储管理](configure-oracle-asm.md)教程。

### <a name="shared-storage-configuration-options"></a>共享存储配置选项

Azure NetApp 文件旨在满足在云中运行高性能工作负荷（如数据库）的核心要求，并提供；

- Azure 本机共享 NFS 存储服务，用于通过 VM 本机 NFS 客户端或 Oracle dNFS 运行 Oracle 工作负荷
- 可缩放的性能层，反映了 IOPS 需求的实际范围
- 低延迟
- 关键任务企业工作负荷（如 SAP 和 Oracle）通常所需的大规模高可用性、高持续性和可管理性
- 快速高效的备份和恢复，实现最严格的 RTO 和 RPO SLA

这些功能可用是因为 Azure NetApp 文件基于作为 Azure 本机服务在 Azure 数据中心环境中运行的 NetApp® ONTAP® 全闪存系统。 其结果是一个理想的数据库存储技术，可以像其他 Azure 存储选项一样进行预配和使用。 有关如何部署和访问 Azure NetApp 文件 NFS 卷的详细信息，请参阅 [Azure NetApp 文件文档](https://docs.microsoft.com/azure/azure-netapp-files/)。 有关在 Azure NetApp 文件上对 Oracle 数据库进行操作的最佳做法建议，请参阅[使用 Azure NetApp 文件的 Azure 上的 Oracle 部署最佳做法指南](https://www.netapp.com/us/media/tr-4780.pdf)。

## <a name="licensing-oracle-database--software-on-azure"></a>在 Azure 上授权 Oracle Database 与软件

Microsoft Azure 是一种用于运行 Oracle Database 的已授权云环境。 在云中授权 Oracle 数据库时，Oracle 内核系数表不适用。 相反，当使用为 Enterprise Edition 数据库启用的超线程技术的 VM 时，如果启用了超线程（如策略文档中所述），则应将两个 vCPU 视为等效于一个 Oracle 处理器许可证。 [此处](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)提供了策略详细信息。
Oracle 数据库通常需要更高端存储器和 IO。 出于此原因，建议对这些工作负荷使用[内存优化 VM](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory)。 若要进一步优化工作负荷，建议将[受约束的核心 vCPU](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) 用于需要高端存储器、存储和 I/O 带宽，但不需要高核心计数的 Oracle Database 工作负荷。

将 Oracle 软件和工作负荷从本地迁移到 Microsoft Azure 时，Oracle 提供了许可移动性，如 [Azure 上的 Oracle 常见问题解答](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html)中所述

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)

Oracle Real 应用程序群集 (Oracle RAC) 用于减少本地多节点群集配置中单一节点的故障。 它依赖于网络多播和共享磁盘这两项本地技术，而这两项技术并非源自超大规模公有云环境。 如果数据库解决方案需要 Azure 中的 Oracle RAC，需要使用第三方软件来启用这些技术。 有关 Oracle RAC 的详细信息，请参阅 [FlashGrid SkyCluster 页](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项

在 Azure 中使用 Oracle 数据库时，用户负责实现高可用性和灾难恢复解决方案，避免出现任何停机。

可以在 Azure 上使用[数据防护、活动数据防护](https://www.oracle.com/database/technologies/high-availability/dataguard.html)或 [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate) 实现 Oracle Database Enterprise Edition 的高可用性和灾难恢复（不依赖于 Oracle RAC），其中两个数据库位于两个单独的虚拟机中。 这两个虚拟机应都位于相同的[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)，以确保它们能够通过专用持久性 IP 地址相互访问。  另外，建议将虚拟机放置在同一可用性集中，以便 Azure 将它们置于单独的容错域和升级域中。 如想拥有地理冗余，请将这两个数据库设置为在两个不同区域之间复制，并将两个实例与 VPN 网关连接起来。

[在 Azure 上实现 Oracle 数据防护](configure-oracle-dataguard.md)教程介绍了 Azure 上的基本安装过程。  

有了 Oracle 数据防护，可以通过一个虚拟机中的主数据库、另一个虚拟机中的辅助（备用）数据库和它们之间的单向复制设置实现高可用性。 结果是对数据库副本的读取访问权限。 使用 Oracle GoldenGate，可以配置两个数据库之间的双向复制。 若要了解如何使用这些工具为数据库设置高可用性解决方案，请参阅 Oracle 网站上的[活动数据防护](https://www.oracle.com/database/technologies/high-availability/dataguard.html)和 [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文档。 如果需要对数据库副本的读写访问权限，可以使用 [Oracle 活动数据防护](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

[在 Azure 上实现 Oracle GoldenGate](configure-oracle-golden-gate.md)教程介绍了 Azure 上的基本安装过程。

除了在 Azure 中构建 HA 和 DR 解决方案外，还应准备一份备份策略来还原数据库。 [备份和恢复 Oracle Database](oracle-backup-recovery.md)教程介绍了建立一致备份的基本过程。

## <a name="support-for-jd-edwards"></a>对 JD Edwards 的支持

根据 Oracle 支持说明 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)，JD Edwards EnterpriseOne 9.2 及以上版本受满足其特定 `Minimum Technical Requirements` (MTR) 的所有公有云产品/服务的支持。  创建的自定义映像需要符合 OS 和软件应用程序兼容性的 MTR 规范。

## <a name="oracle-weblogic-server-virtual-machine-offers"></a>Oracle WebLogic Server 虚拟机套餐

Oracle 和 Microsoft 正在协作，以 Azure 应用程序套餐的集合形式将 WebLogic Server 引入 Azure 市场。  [Oracle WebLogic Server Azure 应用程序](oracle-weblogic.md)一文中介绍了这些套餐。

### <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虚拟机映像

- **群集仅在 Enterprise Edition 上受支持。** 仅在使用 Oracle WebLogic Server 的 Enterprise Edition 时许可使用 WebLogic 群集。 不要将群集用于 Oracle WebLogic Server Standard Edition。
- **不支持 UDP 多播。** Azure 支持 UDP 单播，但不支持多播和广播。 Oracle WebLogic Server 能够依赖于 Azure 的 UDP 单播功能。 为了获得依赖于 UDP 单播的最佳结果，建议将 WebLogic 群集大小保留静态，或者使托管服务器不超过 10 个。
- **Oracle WebLogic Server 预期 T3 访问（例如，使用企业 JavaBean 时）的公用端口和专用端口是相同的。** 请考虑多层方案，其中服务层 (EJB) 应用程序在 Oracle WebLogic Server 群集中运行，该群集包含两个或两个以上的 VM，采用名为 SLWLS 的虚拟网络。 客户端层位于同一虚拟网络的其他子网中，运行尝试调用服务层中的 EJB 的简单 Java 程序。 由于有必要对服务层进行负载均衡，因此需要为 Oracle WebLogic Server 群集中的虚拟机创建公共负载均衡终结点。 如果指定的专用端口不同于公用端口（例如，7006:7008），则会发生下面这样的错误：

```bash
   [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

   Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]
```

   这是因为对于任何远程 T3 访问，Oracle WebLogic Server 都要求负载均衡器端口和 WebLogic 托管服务器端口是相同的。 在上面的示例中，客户端访问端口 7006（负载均衡器端口），托管服务器侦听 7008（专用端口）。 此限制仅适用于 T3 访问，而不适用于 HTTP。

   若要避免此问题，请使用下列解决方法之一：

- 对于专用于 T3 访问的负载均衡终结点，可使用相同的专用和公用端口号。
- 启动 Oracle WebLogic Server 时，请包括以下 JVM 参数：

```bash
   -Dweblogic.rjvm.enableprotocolswitch=true
```

如需相关信息，请参阅位于 <https://support.oracle.com> 的知识库文章 **860340.1**。

- **动态群集和负载均衡限制。** 假设要在 Oracle WebLogic Server 中使用动态群集并通过 Azure 中单个公共负载均衡终结点公开它。 只要对每个托管服务器使用固定的端口号（不是从范围中动态分配的），并且启动的托管服务器数不超过管理员正在跟踪的计算机数，就可以完成此操作。 即，每个虚拟机不能有一个以上托管服务器）。 如果配置导致启动的 Oracle WebLogic Server 数多于存在的虚拟机数（即，其中多个 Oracle WebLogic Server 实例共享同一个虚拟机），则其中多个 Oracle WebLogic Server 实例不能绑定到给定端口号。 该虚拟机上的其他实例会失败。

   如果将管理服务器配置为自动向其托管服务器分配唯一端口号，则负载均衡将不可能，因为 Azure 不支持从单个公用端口映射到多个专用端口，而这是此配置所必需的。
- **虚拟机上 Oracle WebLogic Server 的多个实例。** 如果虚拟机足够大，根据部署需求，可以考虑在同一个虚拟机上运行多个 Oracle WebLogic Server 实例。 例如，在包含两个内核的中等大小虚拟机上，可以选择运行两个 Oracle WebLogic Server 实例。 但是，仍建议避免在体系结构中引入单一故障点，如果只使用了一个运行多个 Oracle WebLogic Server 实例的虚拟机，就会出现这种情况。 至少使用两个虚拟机可能是更好的方法，每个虚拟机可以运行多个 Oracle WebLogic Server 实例。 每个 Oracle WebLogic Server 实例仍可以是同一个群集的一部分。 但是，目前不能使用 Azure 对同一个虚拟机中此类 Oracle WebLogic Server 部署公开的终结点进行负载均衡，因为 Azure 负载均衡器需要将负载均衡服务器分布到唯一的虚拟机中。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虚拟机映像

- **JDK 6 和 7 最新更新。** 尽管建议使用最新公开支持的 Java 版本（当前为 Java 8），但 Azure 还提供 JDK 6 和 7 的映像。 这适用于尚未准备好升级到 JDK 8 的旧版应用程序。 虽然对旧版 JDK 映像的更新不再提供给公众，但是鉴于 Microsoft 与 Oracle 的合作关系，由 Azure 提供的 JDK 6 和 7 的映像将包含最近的非公开更新，该更新通常由 Oracle 只向一组 Oracle 支持的选定客户提供。 随着时间推移，将使用 JDK 6 和 7 的更新版本提供 JDK 映像的新版本。

   在 JDK 6 和 7 的映像中提供的 JDK 以及从中派生的虚拟机和映像只能在 Azure 中使用。
- **64 位 JDK。** Azure 提供的 Oracle WebLogic Server 虚拟机映像和 Oracle JDK 虚拟机映像包含 64 位版本的 Windows Server 和 JDK。

## <a name="next-steps"></a>后续步骤

现在你已大致了解了基于 Microsoft Azure 中虚拟机映像的最新 Oracle 解决方案。 下一步则是在 Azure 上部署你的首个 Oracle 数据库。

> [!div class="nextstepaction"]
> [在 Azure 上创建 Oracle 数据库](oracle-database-quick-create.md)
