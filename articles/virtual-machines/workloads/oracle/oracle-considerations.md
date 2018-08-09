---
title: Microsoft Azure 上的 Oracle 解决方案 | Microsoft Docs
description: 了解 Microsoft Azure 上 Oracle 解决方案支持的配置和限制。
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 6435c866f6cdf5abea3862a718579f3a6e4d7378
ms.sourcegitcommit: eaad191ede3510f07505b11e2d1bbfbaa7585dbd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/03/2018
ms.locfileid: "39493058"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Microsoft Azure 上的 Oracle 解决方案及其部署
本文介绍在 Microsoft Azure 上成功部署各种 Oracle 解决方案所需的信息。 这些解决方案以 Oracle 在 Azure 市场中发布的虚拟机映像为基础。 若要获取当前可用映像的列表，请运行以下命令：
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
截至 2017-6-16，映像列表如下所示：
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

这些映像被视为“自带许可”，因此用户只需支付运行 VM 所产生的计算、存储和网络费用。  假定已获得使用 Oracle 软件的相关许可，且拥有 Oracle 最新支持协议。 Oracle 确保了从本地到 Azure 的许可移动性。 有关许可移动性的详细信息，请参阅已发布的 [Oracle 和 Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) 说明。 

用户还可选择将自定义映像作为解决方案的基础，自定义映像可以是用户在 Azure 中从头创建的映像，也可以是从本地环境上传的映像。

## <a name="support-for-jd-edwards"></a>对 JD Edwards 的支持
根据 Oracle 支持说明 [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)，JD Edwards EnterpriseOne 9.2 及以上版本受满足其特定 `Minimum Technical Requirements` (MTR) 的所有公有云产品/服务的支持。  创建的自定义映像需要符合 OS 和软件应用程序兼容性的 MTR 规范。 

## <a name="oracle-database-virtual-machine-images"></a>Oracle 数据库虚拟机映像
Oracle 支持在基于 Oracle Linux 的虚拟机映像上的 Azure 中运行 Oracle DB 12.1 标准版和企业版。  为使 Oracle DB 的生产工作负荷在 Azure 上表现最佳，请务必将 VM 映像调整到合适的大小并使用由高级存储支持的托管磁盘。 有关如何使用 Oracle 发布的 VM 映像在 Azure 中快速启动并运行 Oracle DB 的说明，[请查看 Oracle DB 快速入门演练](oracle-database-quick-create.md)。

### <a name="attached-disk-configuration-options"></a>附加磁盘配置选项

附加磁盘依赖于 Azure Blob 存储服务。 每个标准磁盘理论上每秒最多能够完成大约 500 个输入/输出操作 (IOPS)。 高级磁盘产品主要针对高性能数据库工作负荷，每个磁盘 IOPS 高达 5000。 尽管可使用单个磁盘（如果这可满足性能需求），但是如果使用多个附加磁盘，将数据库数据分散到这些磁盘上，然后使用 Oracle 自动存储管理 (ASM)，通常可以提高有效 IOPS 性能。 请参阅 [Oracle 自动存储概述](http://www.oracle.com/technetwork/database/index-100339.html)，了解更多 Oracle ASM 的具体信息。 有关如何在 Linux Azure VM 上安装和配置 Oracle ASM 的示例，可查看[安装和配置 Oracle 自动存储管理](configure-oracle-asm.md)教程。

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Oracle Real RAC 可用于减少本地多节点群集配置中单一节点的故障。 它依赖于网络多播和共享磁盘这两项本地技术，而这两项技术并非源自超大规模公有云环境。 如果数据库解决方案需要 Azure 中的 Oracle RAC，需要使用第三方软件来启用这些技术。  
  **Microsoft Azure 认证**产品称为 [FlashGrid Node for Oracle RAC](https://azuremarketplace.microsoft.com/marketplace/apps/flashgrid-inc.flashgrid-racnode?tab=Overview) 处于可以使用 Azure 市场发布：FlashGrid inc.有关此解决方案以及它如何在 Azure 中工作的详细信息，请参阅 [FlashGrid 解决方案页](https://www.flashgrid.io/oracle-rac-in-azure/)。

## <a name="high-availability-and-disaster-recovery-considerations"></a>高可用性和灾难恢复注意事项
在 Azure 中使用 Oracle 数据库时，用户负责实现高可用性和灾难恢复解决方案，避免出现任何停机。 

可以在 Azure 上使用[数据防护、活动数据防护](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html)或 [Oracle Golden Gate](http://www.oracle.com/technetwork/middleware/goldengate) 实现 Oracle Database Enterprise Edition（不依赖于 Oracle RAC）的高可用性和灾难恢复，其中两个数据库位于两个单独的虚拟机中。 这两个虚拟机应都位于相同的[虚拟网络](https://azure.microsoft.com/documentation/services/virtual-network/)，以确保它们能够通过专用持久性 IP 地址相互访问。  另外，建议将虚拟机放置在同一可用性集中，以便 Azure 将它们置于单独的容错域和升级域中。  如想拥有地理冗余，则可将这两个数据库复制到两个不同区域，并将两个实例与 VPN 网关连接起来。

“[在 Azure 上实现 Oracle DataGuard](configure-oracle-dataguard.md)”教程介绍了解基本安装过程，以便在 Azure 上试用。  

有了 Oracle 数据防护，可以通过一个虚拟机中的主数据库、另一个虚拟机中的辅助（备用）数据库和它们之间的单向复制设置实现高可用性。 结果是对数据库副本的读取访问权限。 使用 Oracle GoldenGate，可以配置两个数据库之间的双向复制。 若要了解如何使用这些工具为数据库设置高可用性解决方案，请参阅 Oracle 网站上的[活动数据防护](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html)和 [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) 文档。 如果需要对数据库副本的读写访问权限，可以使用 [Oracle 活动数据防护](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)。

“[在 Azure 上实现 Oracle GoldenGate](configure-oracle-golden-gate.md)”教程介绍了基本安装过程，以便在 Azure 上试用。

尽管 Azure 中构建了 HA 和 DR 解决方案，但最好确保准备一份备份策略来还原数据库。  [备份和恢复 Oracle 数据库](oracle-backup-recovery.md)教程将介绍建立一致备份的基本过程。

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server 虚拟机映像
* **群集仅在 Enterprise Edition 上受支持。** 仅在使用 WebLogic Server 的 Enterprise Edition 时许可使用 WebLogic 群集。 不要将群集用于 WebLogic Server 标准版。
* **不支持 UDP 多播。** Azure 支持 UDP 单播，但不支持多播和广播。 WebLogic Server 能够依赖于 Azure 的 UDP 单播功能。 为了获得依赖于 UDP 单播的最佳结果，建议将 WebLogic 群集大小保留静态，或者使群集中的托管服务器不超过 10 个。
* **WebLogic Server 预期 T3 访问（例如，使用企业 JavaBean 时）的公用端口和专用端口是相同的。** 请考虑多层方案，其中服务层 (EJB) 应用程序在 WebLogic Server 群集中运行，该群集包含两个或两个以上的 VM，采用名为 SLWLS 的 vNet。 客户端层位于同一 vNet 的其他子网中，运行尝试调用服务层中的 EJB 的简单 Java 程序。 由于有必要对服务层进行负载均衡，因此需要为 WebLogic Server 群集中的虚拟机创建公共负载均衡终结点。 如果指定的专用端口不同于公用端口（例如，7006:7008），则会发生下面这样的错误：

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   这是因为对于任何远程 T3 访问，WebLogic Server 都要求负载均衡器端口和 WebLogic 托管服务器端口是相同的。 在上面的示例中，客户端访问端口 7006（负载均衡器端口），托管服务器侦听 7008（专用端口）。 此限制仅适用于 T3 访问，而不适用于 HTTP。

   若要避免此问题，请使用下列解决方法之一：

  * 对于专用于 T3 访问的负载均衡终结点，可使用相同的专用和公用端口号。
  * 启动 WebLogic Server 时，请包括以下 JVM 参数：

         -Dweblogic.rjvm.enableprotocolswitch=true

如需相关信息，请参阅位于 <http://support.oracle.com> 的知识库文章 **860340.1**。

* **动态群集和负载均衡限制。** 假设要在 WebLogic Server 中使用动态群集并通过 Azure 中单个公共负载均衡终结点公开它。 只要对每个托管服务器使用固定的端口号（不是从范围中动态分配的），并且启动的托管服务器数不超过管理员正在跟踪的计算机数（即，每个虚拟机不能有一个以上托管服务器），就可以完成此操作。 如果配置导致启动的 WebLogic Server 数多于存在的虚拟机数（即，其中多个 WebLogic Server 实例共享同一个虚拟机），则其中多个 WebLogic Server 实例服务器不能绑定到给定端口号 - 该虚拟机上的其他 WebLogic Server 实例会失败。

   如果将管理服务器配置为自动向其托管服务器分配唯一端口号，则负载均衡将不可能，因为 Azure 不支持从单个公用端口映射到多个专用端口，而这是此配置所必需的。
* **虚拟机上的 Weblogic Server 的多个实例。** 如果虚拟机足够大，根据部署需求，可以考虑在同一个虚拟机上运行多个 WebLogic Server 实例的选项。 例如，在包含两个内核的中等大小虚拟机上，可以选择运行两个 WebLogic Server 实例。 但请注意，仍建议避免在体系结构中引入单点故障，如果只使用了一个运行多个 WebLogic Server 实例的虚拟机，就会出现这种情况。 至少使用两个虚拟机可能是更好的方法，其中每个虚拟机可以运行多个 WebLogic Server 实例。 其中每个 WebLogic Server 实例仍可以是同一个群集的一部分。 但是，请注意，目前不能使用 Azure 对同一个虚拟机中此类 WebLogic Server 部署公开的终结点进行负载均衡，因为 Azure 负载均衡器需要将负载均衡服务器分布到唯一的虚拟机中。

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK 虚拟机映像
* **JDK 6 和 7 最新更新。** 尽管建议使用最新公开支持的 Java 版本（当前为 Java 8），但 Azure 还提供 JDK 6 和 7 的映像。 这适用于尚未准备好升级到 JDK 8 的旧版应用程序。 虽然对旧版 JDK 映像的更新不再提供给公众，但是鉴于 Microsoft 与 Oracle 的合作关系，由 Azure 提供的 JDK 6 和 7 的映像将包含最近的非公开更新，该更新通常由 Oracle 只向一组 Oracle 支持的选定客户提供。 随着时间推移，将使用 JDK 6 和 7 的更新版本提供 JDK 映像的新版本。

   在此 JDK 6 和 7 的映像中提供的 JDK 以及从中派生的虚拟机和映像只能在 Azure 中使用。
* **64 位 JDK。** Azure 提供的 Oracle WebLogic Server 虚拟机映像和 Oracle JDK 虚拟机映像包含 64 位版本的 Windows Server 和 JDK。

## <a name="next-steps"></a>后续步骤
现在你已大致了解了 Microsoft Azure 上最新的 Oracle 解决方案。 下一步则是在 Azure 上部署你的首个 Oracle 数据库。
- 请先查看[在 Azure 上创建 Oracle 数据库](oracle-database-quick-create.md)教程。