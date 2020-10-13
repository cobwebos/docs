---
title: 使用 Azure NetApp 文件与 Oracle Database 的好处 |Microsoft Docs
description: 介绍技术并在 Oracle 直接 NFS (dNFS) 和传统 NFS 客户端之间提供性能比较。 显示将 dNFS 与 Azure NetApp 文件一起使用的优点。
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 0f431015caf5bb3364fe5628a599f760c6318c47
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932492"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>将 Azure NetApp 文件与 Oracle Database 配合使用的好处

Oracle 直接 NFS (dNFS 的) 使性能比操作系统自己的 NFS 驱动程序的性能更高。 本文介绍了该技术，并提供了 dNFS 和传统 NFS 客户端 (内核 NFS) 之间的性能比较。 它还展示了在 Azure NetApp 文件中使用 dNFS 的优点和便利性。  

## <a name="how-oracle-direct-nfs-works"></a>Oracle 直接 NFS 的工作方式

以下摘要说明了 Oracle 直接 NFS 的工作方式：

* Oracle 直接 NFS 会绕过操作系统缓冲区缓存。 数据在用户空间仅缓存一次，从而消除了内存副本的开销。  

* 传统 NFS 客户端使用单个网络流，如下所示：    

    ![使用单个网络流的传统 NFS 客户端](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Oracle 直接 NFS 通过跨多个网络流的负载平衡网络流量进一步提高了性能。 如下所示，650已通过 Oracle Database 动态建立了不同的网络连接：  

    ![Oracle 直接 NFS 提高性能](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[直接 NFS 的 ORACLE 常见问题解答](http://www.orafaq.com/wiki/Direct_NFS)显示 oracle dNFS 是一个优化的 NFS 客户端。 它提供对位于 NAS 存储设备上的 NFS 存储的快速、可缩放访问， (通过 TCP/IP) 访问。 dNFS 内置于数据库内核中，就像 ASM 一样，后者主要用于 DAS 或 SAN 存储。 因此， *指导原则是在实现 NAS 存储时使用 dNFS，并在实现 SAN 存储时使用 ASM。*

dNFS 是 Oracle 18c 中的默认选项。

dNFS 从 Oracle Database 11g 开始可用。 下图将 dNFS 与本机 NFS 进行比较。 当你使用 dNFS 时，运行在 Azure 虚拟机上的 Oracle 数据库的驱动时间比本机 NFS 客户端多。

![Oracle 和 Azure NetApp 文件与本机 NFS 的 dNFS 比较](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

可以通过运行两个命令并重新启动数据库来启用或禁用 dNFS。

若要启用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

若要禁用：  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>与 Oracle 直接 NFS 结合的 Azure NetApp 文件

可以通过 Azure NetApp 文件服务提高 Oracle dNFS 的性能。 此服务提供对应用程序性能的总体控制。 它可以满足极其苛刻的应用程序。 Oracle dNFS 与 Azure NetApp 文件的组合为工作负荷提供了极大的优势。

## <a name="next-steps"></a>后续步骤

- [使用 Azure NetApp 文件的解决方案体系结构](azure-netapp-files-solution-architectures.md)
- [Azure 上的 Oracle 应用程序和解决方案概述](../virtual-machines/workloads/oracle/oracle-overview.md)