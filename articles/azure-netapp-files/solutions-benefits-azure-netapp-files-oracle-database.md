---
title: 将 Azure NetApp 文件与 Oracle 数据库结合使用的好处 |微软文档
description: 描述该技术，并提供 Oracle 直接 NFS （dNFS） 与传统 NFS 客户端之间的性能比较。 显示了将 dNFS 与 Azure NetApp 文件一起使用的优点。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772075"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>将 Azure NetApp 文件与 Oracle 数据库一起使用的好处

Oracle 直接 NFS （dNFS） 使得其性能高于操作系统自己的 NFS 驱动程序。 本文介绍了该技术，并提供了dNFS与传统NFS客户端（Kernel NFS）之间的性能比较。 它还显示了使用 Azure NetApp 文件的优势和易用性。  

## <a name="how-oracle-direct-nfs-works"></a>甲骨文直接 NFS 的工作原理

Oracle 直接 NFS （dNFS） 绕过操作系统的缓冲区缓存。 数据仅在用户空间中缓存一次，从而消除了内存副本的开销。  

传统的 NFS 客户端使用单个网络流，如下例所示： 

![使用单个网络流的传统 NFS 客户端](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

相比之下，Oracle dNFS 通过跨多个网络流的负载平衡网络流量来提高性能。 此功能使 Oracle 数据库能够动态建立大量 650 个不同的网络连接，如下例所示：  

![Oracle 直接 NFS 提高性能](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[直接 NFS 的 Oracle 常见问题解答](http://www.orafaq.com/wiki/Direct_NFS)显示，Oracle dNFS 是一个优化的 NFS 客户端。 它提供了对位于 NAS 存储设备上的 NFS 存储的快速和可扩展访问（可通过 TCP/IP 访问）。 dNFS 内置于数据库内核中，就像 ASM 一样，它主要用于 DAS 或 SAN 存储。 因此 *，准则是在实现 NAS 存储时使用 dNFS，并在实现 SAN 存储时使用 ASM。*

dNFS 是 Oracle 18c 中的默认选项，也是 RAC 的默认选项。

dNFS 从 Oracle 数据库 11g 开始可用。 下图将 dNFS 与本机 NFS 进行比较。 使用 dNFS 时，在 Azure 虚拟机上运行的 Oracle 数据库可以驱动比本机 NFS 客户端更多的 I/O。

![Oracle 和 Azure NetApp 文件 dNFS 与本机 NFS 的比较](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

您可以通过运行两个命令并重新启动数据库来启用或禁用 dNFS。

若要启用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

要禁用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp 文件与 Oracle 直接 NFS 结合使用

您可以使用 Azure NetApp 文件服务提高 Oracle dNFS 的性能。 该服务使您能够完全控制应用程序性能。 它可以满足极其苛刻的应用。 Oracle dNFS 与 Azure NetApp 文件的组合为您的工作负载提供了极大的优势。

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
- [Azure 上的 Oracle 应用程序和解决方案概述](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)